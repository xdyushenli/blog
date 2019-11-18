---
title: react源码阅读日记（三）
date: 2019-11-07 17:42:12
tags: [ React ]
---
# 概述
本章主要介绍React Scheduler相关的内容。
在React 16版本中, React官方重写了React的核心代码, 将React的更新结构转变为以Fiber树为主。这样的好处在于通过Fiber树将更新任务划分为多个小块的节点更新, 允许进行优先级的调度或中断。当整个应用更新到某个节点时, 若优先级更高的任务出现, 则可以中断当前更新, 先执行优先级更高的任务, 执行完毕后再从上次更新的节点继续进行更新。
调度的目的在于使React执行更新的时候不会阻断浏览器进行动画渲染或用户输入响应等操作, 从而保证用户的良好交互体验。
发起更新请求后, 在调度器中会维护一个队列保存更新和更新对应的节点, 之后会根据更新任务是同步的还是异步的来判断进行更新。若是同步更新, 则立即执行。若是异步更新, 则将先执行浏览器优先级最高的更新, 等浏览器空闲时再执行React更新。在执行React更新的过程中, 根据调度器回传的deadline对象判断是否要把执行权交还给浏览器, 以保证用户交互的流畅。
# scheduleWork
书接上回。当调用ReactDOM.render、setState或forceUpdate后, 会产生一个更新请求, 也就是创建一个update对象。当这个update对象放入updateQueue之后, 之后的工作便由调度器接管了, 也就是调用了scheduleWork方法。
## scheduleWork流程
* 找到更新对应的FiberRoot节点（整个应用的顶点）。
* 如果符合条件, 重置stack
* 根据情况请求工作调度
## scheduleWork源码
```js
export const scheduleWork = scheduleUpdateOnFiber;
export function scheduleUpdateOnFiber(
  fiber: Fiber,
  expirationTime: ExpirationTime,
) {
  checkForNestedUpdates();
  warnAboutInvalidUpdatesOnClassComponentsInDEV(fiber);

  const root = markUpdateTimeFromFiberToRoot(fiber, expirationTime);
  if (root === null) {
    warnAboutUpdateOnUnmountedFiberInDEV(fiber);
    return;
  }

  root.pingTime = NoWork;

  checkForInterruption(fiber, expirationTime);
  recordScheduleUpdate();

  // TODO: computeExpirationForFiber also reads the priority. Pass the
  // priority as an argument to that function and this one.
  const priorityLevel = getCurrentPriorityLevel();

  if (expirationTime === Sync) {
    if (
      // Check if we're inside unbatchedUpdates
      (executionContext & LegacyUnbatchedContext) !== NoContext &&
      // Check if we're not already rendering
      (executionContext & (RenderContext | CommitContext)) === NoContext
    ) {
      // Register pending interactions on the root to avoid losing traced interaction data.
      schedulePendingInteractions(root, expirationTime);

      // This is a legacy edge case. The initial mount of a ReactDOM.render-ed
      // root inside of batchedUpdates should be synchronous, but layout updates
      // should be deferred until the end of the batch.
      let callback = renderRoot(root, Sync, true);
      while (callback !== null) {
        callback = callback(true);
      }
    } else {
      scheduleCallbackForRoot(root, ImmediatePriority, Sync);
      if (executionContext === NoContext) {
        // Flush the synchronous work now, wnless we're already working or inside
        // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
        // scheduleCallbackForFiber to preserve the ability to schedule a callback
        // without immediately flushing it. We only do this for user-initiated
        // updates, to preserve historical behavior of sync mode.
        flushSyncCallbackQueue();
      }
    }
  } else {
    scheduleCallbackForRoot(root, priorityLevel, expirationTime);
  }

  if (
    (executionContext & DiscreteEventContext) !== NoContext &&
    // Only updates at user-blocking priority or greater are considered
    // discrete, even inside a discrete event.
    (priorityLevel === UserBlockingPriority ||
      priorityLevel === ImmediatePriority)
  ) {
    // This is the result of a discrete event. Track the lowest priority
    // discrete update per root so we can flush them early, if needed.
    if (rootsWithPendingDiscreteUpdates === null) {
      rootsWithPendingDiscreteUpdates = new Map([[root, expirationTime]]);
    } else {
      const lastDiscreteTime = rootsWithPendingDiscreteUpdates.get(root);
      if (lastDiscreteTime === undefined || lastDiscreteTime > expirationTime) {
        rootsWithPendingDiscreteUpdates.set(root, expirationTime);
      }
    }
  }
}
```