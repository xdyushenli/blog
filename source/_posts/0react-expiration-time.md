---
title: React中的expirationTime
date: 2019-11-07 17:22:42
tags: [React]
---
# (未完成)expirationTime的计算方法
在之前的代码中, 多次涉及到了expirationTime。这是用于判断更新任务优先级的重要标志, 在本节中, 将讨论expirationTime的计算方法。
expirationTime用于标记异步任务的过期时间。在更新时, 异步任务时可以被打断的, 但为了防止异步任务一直被打断而无法执行下去, 为该任务设置了过期时间。若当前时间超过了异步任务的过期时间, 那么该任务的优先级便会提高, 从而尽快将该任务执行完毕。
在updateContainer中, 通过调用expirationTime来计算了过期时间。
```js
// updateContainer
const currentTime = requestCurrentTime();
const expirationTime = computeExpirationForFiber(
  currentTime,
  current,
  suspenseConfig,
);
```
## computeExpirationForFiber
```js
export function requestCurrentTime() {
  if ((executionContext & (RenderContext | CommitContext)) !== NoContext) {
    // We're inside React, so it's fine to read the actual time.
    return msToExpirationTime(now());
  }
  // We're not inside React, so we may be in the middle of a browser event.
  if (currentEventTime !== NoWork) {
    // Use the same start time for all updates until we enter React again.
    return currentEventTime;
  }
  // This is the first update since React yielded. Compute a new start time.
  currentEventTime = msToExpirationTime(now());
  return currentEventTime;
}

export function computeExpirationForFiber(
  currentTime: ExpirationTime,
  fiber: Fiber,
  suspenseConfig: null | SuspenseConfig,
): ExpirationTime {
  const mode = fiber.mode;
  if ((mode & BatchedMode) === NoMode) {
    return Sync;
  }

  const priorityLevel = getCurrentPriorityLevel();
  if ((mode & ConcurrentMode) === NoMode) {
    return priorityLevel === ImmediatePriority ? Sync : Batched;
  }

  if ((executionContext & RenderContext) !== NoContext) {
    // Use whatever time we're already rendering
    return renderExpirationTime;
  }

  let expirationTime;
  if (suspenseConfig !== null) {
    // Compute an expiration time based on the Suspense timeout.
    expirationTime = computeSuspenseExpiration(
      currentTime,
      suspenseConfig.timeoutMs | 0 || LOW_PRIORITY_EXPIRATION,
    );
  } else {
    // Compute an expiration time based on the Scheduler priority.
    switch (priorityLevel) {
      case ImmediatePriority:
        expirationTime = Sync;
        break;
      case UserBlockingPriority:
        // TODO: Rename this to computeUserBlockingExpiration
        expirationTime = computeInteractiveExpiration(currentTime);
        break;
      case NormalPriority:
      case LowPriority: // TODO: Handle LowPriority
        // TODO: Rename this to... something better.
        expirationTime = computeAsyncExpiration(currentTime);
        break;
      case IdlePriority:
        expirationTime = Never;
        break;
      default:
        invariant(false, 'Expected a valid priority level');
    }
  }

  // If we're in the middle of rendering a tree, do not update at the same
  // expiration time that is already rendering.
  // TODO: We shouldn't have to do this if the update is on a different root.
  // Refactor computeExpirationForFiber + scheduleUpdate so we have access to
  // the root when we check for this condition.
  if (workInProgressRoot !== null && expirationTime === renderExpirationTime) {
    // This is a trick to move this update into a separate batch
    expirationTime -= 1;
  }

  return expirationTime;
}
```
在计算expirationTime中, 可以注意到expirationTime总是按照某个固定值增加的, 只有度过一段时间后, 计算出的expirationTime才会不同。这么做的好处在于若两次调用setState的时间间隔很短, 计算出的expirationTime也是相同的, 这样保证了其优先级也是相同的, 会进行合并更新。否则即使setState调用时间间隔很短, 也会进行多次更新, 性能会受很大影响。

# 不同的expirationTime
### 种类
* Sync模式: 优先级最高, 任务创建后需要立即更新到DOM中
* 异步模式: 需要进行调度才会执行, 可能会被中断
* 指定context

不同Fiber.mode的Fiber对象创建的更新, 其需要用到的expirationTime也不同。