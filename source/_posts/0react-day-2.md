---
title: react源码阅读日记（二）
date: 2019-10-11 19:11:43
tags: [ react ]
---
# 本章简介
本章主要介绍React创建更新的过程, 但不涉及执行更新。
在React中, 有三种创建更新的方式: 
1. ReactDOM.render || hydrate
2. setState
3. forceUpdate
咱们一个一个来看。

# ReactDOM.render
## 创建更新的步骤
1. 创建ReactSyncRoot, 该对象是一个包含整个React应用的顶点对象。
2. 创建FiberRoot和RootFiber。
3. 创建更新请求, 接下来进入调度阶段, 由调度器（reconciler）进行接管。调度器负责任务调度以及调和与平台无关的节点。
## ReactDOM.render源码详解
ReactDOM.render方法会直接调用并返回legacyRenderSubtreeIntoContainer方法。
legacyRenderSubtreeIntoContainer方法首先会进行一次判断, 判断当前传入的container是否已存在_reactRootContainer属性, 该属性是一个ReactSyncRoot对象, 包含了从container的所有子节点的信息。该方法的最终返回值是null或一个FiberRoot对象。
```js
function legacyRenderSubtreeIntoContainer(
  parentComponent: ?React$Component<any, any>,
  children: ReactNodeList,
  container: DOMContainer,
  forceHydrate: boolean,
  callback: ?Function,
) {
  let root: _ReactSyncRoot = (container._reactRootContainer: any);
  let fiberRoot;
  if (!root) {
    // 调用legacyCreateRootFromDOMContainer方法创建一ReactSyncRoot对象, 并将其挂载在container._reactRootContainer上
    fiberRoot = root._internalRoot;
    // 判断callback是否为函数, 并做简单的封装
    // 获取ReactSyncRoot对象的_internalRoot属性
    // 调用unbatchedUpdates方法, 将updateContainer方法传入作为参数
  } else {
    fiberRoot = root._internalRoot;
    // 获取ReactSyncRoot对象的_internalRoot属性
    // 判断callback是否为函数, 并做简单的封装
    // 调用updateContainer方法
  }
  return getPublicRootInstance(fiberRoot);
}
```
### 什么是ReactSyncRoot对象? 
ReactSyncRoot对象是一个包含整个React应用的顶点对象。
值得注意的是, 16.9.0版本的react源码中同时保留了ReactRoot对象和ReactSyncRoot对象。两个对象并没有太多不同的地方, 唯一的区别在于创建ReactSyncRoot对象时多传入了一个参数。在该版本的react中, 使用ReactDOM.render方法时, 最终创建的是ReactSyncRoot对象。由于二者基本等价, 因此在下面的文章中只介绍ReactSyncRoot对象。
ReactSyncRoot对象只有一个名为`_internalRoot`的属性, 在这个属性上挂载着调用`createContainer`方法的返回值, 该返回值是一个FiberRoot对象。
### unbatchedUpdates & updateContainer
unbatchedUpdates方法会直接调用传入的函数, 也就是updateContainer方法。因此, 在这个流程中二者是基本等价的。
```js
// flow: type OpaqueRoot = FiberRoot
export function updateContainer(
  element: ReactNodeList,
  container: OpaqueRoot,
  parentComponent: ?React$Component<any, any>,
  callback: ?Function,
): ExpirationTime {
    const current = container.current;
    const currentTime = requestCurrentTime();
    const suspenseConfig = requestCurrentSuspenseConfig();
    // 计算过期时间
    const expirationTime = computeExpirationForFiber(
        currentTime,
        current,
        suspenseConfig,
    );
    return updateContainerAtExpirationTime(
        element,
        container,
        parentComponent,
        expirationTime,
        suspenseConfig,
        callback,
    );
}

export function updateContainerAtExpirationTime(
  element: ReactNodeList,
  container: OpaqueRoot,
  parentComponent: ?React$Component<any, any>,
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
  callback: ?Function,
) {
    const current = container.current;
    // 一般context都是null
    const context = getContextForSubtree(parentComponent);
    if (container.context === null) {
        container.context = context;
    } else {
        container.pendingContext = context;
    }

    return scheduleRootUpdate(
        current,
        element,
        expirationTime,
        suspenseConfig,
        callback,
    );
}

function scheduleRootUpdate(
  current: Fiber,
  element: ReactNodeList,
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
  callback: ?Function,
) {
    // 创建代表更新请求的对象
    const update = createUpdate(expirationTime, suspenseConfig);
    update.payload = {element};

    // 将更新请求放入更新队列中
    enqueueUpdate(current, update);

    // 通知React开始进行任务调度, 执行更新
    scheduleWork(current, expirationTime);

    return expirationTime;
}
```
到这里, ReactDOM.render函数的基本流程就理清了, 但还有一个问题没有解决, 那就是什么是FiberRoot对象? 这个问题在下一节中进行回答。

# FiberRoot
## FiberRoot对象是什么? 
FiberRoot对象是整个应用的起点, 包含了应用挂载的目标节点, 并记录应用更新过程的各种信息。
## createContainer源码详解
从上节中我们得知, FiberRoot对象是由createContainer方法创建并返回的。在这一节中, 将深入探讨其源码。
```js
export function createContainer(
  containerInfo: Container,
  tag: RootTag,
  hydrate: boolean,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
): OpaqueRoot {
  return createFiberRoot(containerInfo, tag, hydrate, hydrationCallbacks);
}

export function createFiberRoot(
  containerInfo: any,
  tag: RootTag,
  hydrate: boolean,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
): FiberRoot {
  const root: FiberRoot = (new FiberRootNode(containerInfo, tag, hydrate): any);
  if (enableSuspenseCallback) {
    root.hydrationCallbacks = hydrationCallbacks;
  }

  // 返回值为一个FiberNode对象, 对应当前应用所在的节点
  // 每一个React Element都有一个FiberNode对象, 且FiberNode对象具有和React Element相似的树结构
  const uninitializedFiber = createHostRootFiber(tag);
  // 该属性是整个FiberNode树的顶点   
  root.current = uninitializedFiber;
  uninitializedFiber.stateNode = root;

  return root;
}
```
可以看到, 最终的FiberRoot对象是由createFiberRoot创建的。FiberRoot对象具体的数据结构可参考以下[链接](https://react.jokcy.me/book/api/react-structure.html)
## FiberRoot的数据结构
### FiberRoot的类型定义
```js
type BaseFiberRootProperties = {|
  // The type of root (legacy, batched, concurrent, etc.)
  tag: RootTag,

  // Any additional information from the host associated with this root.
  containerInfo: any,
  // Used only by persistent updates.
  pendingChildren: any,
  // The currently active root fiber. This is the mutable root of the tree.
  current: Fiber,

  pingCache:
    | WeakMap<Thenable, Set<ExpirationTime>>
    | Map<Thenable, Set<ExpirationTime>>
    | null,

  finishedExpirationTime: ExpirationTime,
  // A finished work-in-progress HostRoot that's ready to be committed.
  finishedWork: Fiber | null,
  // Timeout handle returned by setTimeout. Used to cancel a pending timeout, if
  // it's superseded by a new one.
  timeoutHandle: TimeoutHandle | NoTimeout,
  // Top context object, used by renderSubtreeIntoContainer
  context: Object | null,
  pendingContext: Object | null,
  // Determines if we should attempt to hydrate on the initial mount
  +hydrate: boolean,
  // List of top-level batches. This list indicates whether a commit should be deferred. Also contains completion callbacks.
  firstBatch: Batch | null,
  // Node returned by Scheduler.scheduleCallback
  callbackNode: *,
  // Expiration of the callback associated with this root
  callbackExpirationTime: ExpirationTime,
  // The earliest pending expiration time that exists in the tree
  firstPendingTime: ExpirationTime,
  // The latest pending expiration time that exists in the tree
  lastPendingTime: ExpirationTime,
  // The time at which a suspended component pinged the root to render again
  pingTime: ExpirationTime,
|};

// The following attributes are only used by interaction tracing builds.
// They enable interactions to be associated with their async work,
// And expose interaction metadata to the React DevTools Profiler plugin.
// Note that these attributes are only defined when the enableSchedulerTracing flag is enabled.
type ProfilingOnlyFiberRootProperties = {|
  interactionThreadID: number,
  memoizedInteractions: Set<Interaction>,
  pendingInteractionMap: PendingInteractionMap,
|};

// The follow fields are only used by enableSuspenseCallback for hydration.
type SuspenseCallbackOnlyFiberRootProperties = {|
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
|};

// Exported FiberRoot type includes all properties,
// To avoid requiring potentially error-prone :any casts throughout the project.
// Profiling properties are only safe to access in profiling builds (when enableSchedulerTracing is true).
// The types are defined separately within this file to ensure they stay in sync.
// (We don't have to use an inline :any cast when enableSchedulerTracing is disabled.)
export type FiberRoot = {
  ...BaseFiberRootProperties,
  ...ProfilingOnlyFiberRootProperties,
  ...SuspenseCallbackOnlyFiberRootProperties,
};
```

### FiberRoot的构造函数
```js
function FiberRootNode(containerInfo, tag, hydrate) {
  // 用于标记当前节点的类型
  this.tag = tag;
  // root节点对应的Fiber对象
  this.current = null;
  // root节点, render函数传入的第二个参数
  this.containerInfo = containerInfo;
  // 只有持久更新时会用到, 一般用于服务器端更新, 在react-dom中不会被用到
  this.pendingChildren = null;
  this.pingCache = null;
  this.finishedExpirationTime = NoWork;
  // 用于保存在一次更新过程中更新结果的Fiber对象
  this.finishedWork = null;
  // 用于实现Suspense组件的延时渲染功能
  this.timeoutHandle = noTimeout;
  // 只有主动调用renderSubtreeIntoContainer时才会用到
  this.context = null;
  this.pendingContext = null;
  // 布尔值, 表示是否要对原有节点进行复用
  this.hydrate = hydrate;
  this.firstBatch = null;
  this.callbackNode = null;

  // 这四个属性用于标记当前更新任务的优先级
  this.callbackExpirationTime = NoWork;
  this.firstPendingTime = NoWork;
  this.lastPendingTime = NoWork;
  this.pingTime = NoWork;

  // 用于进行追踪查看的属性
  if (enableSchedulerTracing) {
    this.interactionThreadID = unstable_getThreadID();
    this.memoizedInteractions = new Set();
    this.pendingInteractionMap = new Map();
  }
  // 若当前组件使用了Suspense功能, 该属性用于挂载Suspense组件的回调函数
  if (enableSuspenseCallback) {
    this.hydrationCallbacks = null;
  }
}
```

# Fiber
## Fiber是什么? 
每一个React Element都对应一个Fiber对象。在Fiber对象中记录了节点的各种状态, 如state、props等。Fiber对象也有和React Element类似的树形结构, 通过Fiber对象可以串联起整个应用。
## Fiber的数据结构
### Fiber的类型定义
```js
export type Fiber = {|
  // These first fields are conceptually members of an Instance. This used to
  // be split into a separate type and intersected with the other Fiber fields,
  // but until Flow fixes its intersection bugs, we've merged them into a
  // single type.

  // An Instance is shared between all versions of a component. We can easily
  // break this out into a separate object to avoid copying so much to the
  // alternate versions of the tree. We put this on a single object for now to
  // minimize the number of objects created during the initial render.

  // Tag identifying the type of fiber.
  tag: WorkTag,

  // Unique identifier of this child.
  key: null | string,

  // The value of element.type which is used to preserve the identity during
  // reconciliation of this child.
  elementType: any,

  // The resolved function/class/ associated with this fiber.
  type: any,

  // The local state associated with this fiber.
  stateNode: any,

  // Conceptual aliases
  // parent : Instance -> return The parent happens to be the same as the
  // return fiber since we've merged the fiber and instance.

  // Remaining fields belong to Fiber

  // The Fiber to return to after finishing processing this one.
  // This is effectively the parent, but there can be multiple parents (two)
  // so this is only the parent of the thing we're currently processing.
  // It is conceptually the same as the return address of a stack frame.
  return: Fiber | null,

  // Singly Linked List Tree Structure.
  child: Fiber | null,
  sibling: Fiber | null,
  index: number,

  // The ref last used to attach this node.
  // I'll avoid adding an owner field for prod and model that as functions.
  ref: null | (((handle: mixed) => void) & {_stringRef: ?string}) | RefObject,

  // Input is the data coming into process this fiber. Arguments. Props.
  pendingProps: any, // This type will be more specific once we overload the tag.
  memoizedProps: any, // The props used to create the output.

  // A queue of state updates and callbacks.
  updateQueue: UpdateQueue<any> | null,

  // The state used to create the output
  memoizedState: any,

  // Dependencies (contexts, events) for this fiber, if it has any
  dependencies: Dependencies | null,

  // Bitfield that describes properties about the fiber and its subtree. E.g.
  // the ConcurrentMode flag indicates whether the subtree should be async-by-
  // default. When a fiber is created, it inherits the mode of its
  // parent. Additional flags can be set at creation time, but after that the
  // value should remain unchanged throughout the fiber's lifetime, particularly
  // before its child fibers are created.
  mode: TypeOfMode,

  // Effect
  effectTag: SideEffectTag,

  // Singly linked list fast path to the next fiber with side-effects.
  nextEffect: Fiber | null,

  // The first and last fiber with side-effect within this subtree. This allows
  // us to reuse a slice of the linked list when we reuse the work done within
  // this fiber.
  firstEffect: Fiber | null,
  lastEffect: Fiber | null,

  // Represents a time in the future by which this work should be completed.
  // Does not include work found in its subtree.
  expirationTime: ExpirationTime,

  // This is used to quickly determine if a subtree has no pending changes.
  childExpirationTime: ExpirationTime,

  // This is a pooled version of a Fiber. Every fiber that gets updated will
  // eventually have a pair. There are cases when we can clean up pairs to save
  // memory if we need to.
  alternate: Fiber | null,

  // Time spent rendering this Fiber and its descendants for the current update.
  // This tells us how well the tree makes use of sCU for memoization.
  // It is reset to 0 each time we render and only updated when we don't bailout.
  // This field is only set when the enableProfilerTimer flag is enabled.
  actualDuration?: number,

  // If the Fiber is currently active in the "render" phase,
  // This marks the time at which the work began.
  // This field is only set when the enableProfilerTimer flag is enabled.
  actualStartTime?: number,

  // Duration of the most recent render time for this Fiber.
  // This value is not updated when we bailout for memoization purposes.
  // This field is only set when the enableProfilerTimer flag is enabled.
  selfBaseDuration?: number,

  // Sum of base times for all descendants of this Fiber.
  // This value bubbles up during the "complete" phase.
  // This field is only set when the enableProfilerTimer flag is enabled.
  treeBaseDuration?: number,

  // Conceptual aliases
  // workInProgress : Fiber ->  alternate The alternate used for reuse happens
  // to be the same as work in progress.
  // __DEV__ only
  _debugID?: number,
  _debugSource?: Source | null,
  _debugOwner?: Fiber | null,
  _debugIsCurrentlyTiming?: boolean,
  _debugNeedsRemount?: boolean,

  // Used to verify that the order of hooks does not change between renders.
  _debugHookTypes?: Array<HookType> | null,
|};
```

### Fiber的构造函数
```js
function FiberNode(
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
) {
  // Instance
  // 标记不同的组件类型
  this.tag = tag;
  // ReactElement.key
  this.key = key;
  // ReactElement.type, 即createElement的第一个参数
  this.elementType = null;
  // 异步组件resolve之后返回的组件类型, 一般是function或者class
  this.type = null;
  // 对应当前节点的FiberRoot实例
  this.stateNode = null;

  // Fiber
  // 这四个属性用于串联整个Fiber树结构
  // 当前Fiber对象在树结构中的父节点
  this.return = null;
  // 当前Fiber对象在树结构中的第一个子节点
  this.child = null;
  // 当前Fiber对象在树结构中的下一个兄弟节点
  this.sibling = null;
  this.index = 0;

  // ReactElement.ref
  this.ref = null;

  // 更新后产生的新的props对象
  this.pendingProps = pendingProps;
  // 上次更新结束后产生的props对象
  this.memoizedProps = null;
  // 用于存放由于当前组件发起更新产生的更新队列
  this.updateQueue = null;
  // 上次更新完成的state对象 
  this.memoizedState = null;
  // 存放当前Fiber依赖的context列表 
  this.dependencies = null;

  // 该节点所采用的的渲染模式, 默认继承父Fiber对象, 创建后不应再修改
  this.mode = mode;

  // Effects
  // Effects用于标记组件要执行的更新以及生命周期
  this.effectTag = NoEffect;
  this.nextEffect = null;

  this.firstEffect = null;
  this.lastEffect = null;

  // 当前节点产生的更新请求的过期时间
  this.expirationTime = NoWork;
  // 子节点产生的更新请求的过期时间
  this.childExpirationTime = NoWork;

  // 用于保存当前Fiber对象上次更新前的副本, 便于复用
  // workInProcess是用于记录需要更新的Fiber节点的树形结构, 其中每个节点都与Fiber节点一一对应, 该属性可认为是连接workInProcess和Fiber对象的通道
  // currentFiber <==> workInProcess
  // 所有的更新都先在workInProcess中进行, 完成后在改变对应的Fiber节点
  this.alternate = null;

  // 用于记录渲染过程的时间信息
  if (enableProfilerTimer) {
    // Note: The following is done to avoid a v8 performance cliff.
    //
    // Initializing the fields below to smis and later updating them with
    // double values will cause Fibers to end up having separate shapes.
    // This behavior/bug has something to do with Object.preventExtension().
    // Fortunately this only impacts DEV builds.
    // Unfortunately it makes React unusably slow for some applications.
    // To work around this, initialize the fields below with doubles.
    //
    // Learn more about this here:
    // https://github.com/facebook/react/issues/14365
    // https://bugs.chromium.org/p/v8/issues/detail?id=8538
    this.actualDuration = Number.NaN;
    this.actualStartTime = Number.NaN;
    this.selfBaseDuration = Number.NaN;
    this.treeBaseDuration = Number.NaN;

    // It's okay to replace the initial doubles with smis after initialization.
    // This won't trigger the performance cliff mentioned above,
    // and it simplifies other profiler code (including DevTools).
    this.actualDuration = 0;
    this.actualStartTime = -1;
    this.selfBaseDuration = 0;
    this.treeBaseDuration = 0;
  }

  // This is normally DEV-only except www when it adds listeners.
  if (enableUserTimingAPI) {
    this._debugID = debugCounter++;
    this._debugIsCurrentlyTiming = false;
  }

  if (__DEV__) {
    this._debugSource = null;
    this._debugOwner = null;
    this._debugNeedsRemount = false;
    this._debugHookTypes = null;
    if (!hasBadMapPolyfill && typeof Object.preventExtensions === 'function') {
      Object.preventExtensions(this);
    }
  }
}
```

# update & updateQueue
书接上回, 再来回顾一下上次深入ReactDOM.render调用栈的结果, 在最后调用了`scheduleRootUpdate`方法, 在这个方法中创建了更新请求并执行了更新任务。
```js
function scheduleRootUpdate(
  current: Fiber,
  element: ReactNodeList,
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
  callback: ?Function,
) {
    // 创建代表更新请求的对象
    const update = createUpdate(expirationTime, suspenseConfig);
    update.payload = {element};

    // 将更新请求放入更新队列中
    enqueueUpdate(current, update);

    // 通知React开始进行任务调度, 执行更新
    scheduleWork(current, expirationTime);

    return expirationTime;
}
```
在本节中, 将深入`scheduleRootUpdate`方法, 介绍update对象的数据结构以及update对象的创建过程。
## update
### update对象是什么?
update对象是用于记录更新所需要的信息的组件, 存放于Fiber.updateQueue中。在scheduleRootUpdate函数中, 是使用createUpdate来创建update对象的。在创建了update对象后, 将其放入Fiber对象的updateQueue属性中。
### update对象的定义 & createUpdate
```js
export type Update<State> = {
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,

  tag: 0 | 1 | 2 | 3,
  payload: any,
  callback: (() => mixed) | null,

  next: Update<State> | null,
  nextEffect: Update<State> | null,

  //DEV only
  priority?: ReactPriorityLevel,
};

export function createUpdate(
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
): Update<*> {
  let update: Update<*> = {
    // 更新请求的过期时间
    expirationTime,
    suspenseConfig,

    // 分别对应四种情况, 分别代表需要进行的操作
    // 更新state
    // const UpdateState = 0;
    // 替换state
    // const ReplaceState = 1;
    // 强制更新state
    // const ForceUpdate = 2;
    // 实现Error Boundary功能
    // const CaptureUpdate = 3;
    tag: UpdateState,
    // 需要进行更新的内容, 如setState的第一个参数
    payload: null,
    // setState 或 render对应的回调函数
    callback: null,
    // 下一个update对象
    next: null,
    // 下一个sideEffect
    nextEffect: null,
  };

  return update;
}
```

## updateQueue
### updateQueue是什么?
updateQueue是用于存放update对象的单向链表结构, 挂载在发起更新的Fiber对象中。
### updateQueue对象的定义
```js
export type UpdateQueue<State> = {
  // 上次渲染完成后的当前节点的state对象
  baseState: State,

  // update对象单向链表的头部
  firstUpdate: Update<State> | null,
  // update对象单向链表的尾部
  lastUpdate: Update<State> | null,

  // 第一个由于错误捕获产生的update
  firstCapturedUpdate: Update<State> | null,
  // 最后一个由于错误捕获产生的update
  lastCapturedUpdate: Update<State> | null,

  // 第一个sideEffect
  firstEffect: Update<State> | null,
  // 最后一个sideEffect
  lastEffect: Update<State> | null,

  // 第一个由于错误捕获产生的sideEffect
  firstCapturedEffect: Update<State> | null,
  // 最后一个由于错误捕获产生的sideEffect
  lastCapturedEffect: Update<State> | null,
};
```
### enqueueUpdate及其用到的函数
在enqueueUpdate中, 将最新产生的update对象放入了Fiber.updateQueue的尾部, 同时保证Fiber.alternate.updateQueue与Fiber.updateQueue的一致性。
```js
export function createUpdateQueue<State>(baseState: State): UpdateQueue<State> {
  const queue: UpdateQueue<State> = {
    baseState,
    firstUpdate: null,
    lastUpdate: null,
    firstCapturedUpdate: null,
    lastCapturedUpdate: null,
    firstEffect: null,
    lastEffect: null,
    firstCapturedEffect: null,
    lastCapturedEffect: null,
  };
  return queue;
}

function cloneUpdateQueue<State>(
  currentQueue: UpdateQueue<State>,
): UpdateQueue<State> {
  const queue: UpdateQueue<State> = {
    baseState: currentQueue.baseState,
    firstUpdate: currentQueue.firstUpdate,
    lastUpdate: currentQueue.lastUpdate,

    // keep these effects.
    firstCapturedUpdate: null,
    lastCapturedUpdate: null,

    firstEffect: null,
    lastEffect: null,

    firstCapturedEffect: null,
    lastCapturedEffect: null,
  };
  return queue;
}

export function createUpdate(
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
): Update<*> {
  let update: Update<*> = {
    expirationTime,
    suspenseConfig,

    tag: UpdateState,
    payload: null,
    callback: null,

    next: null,
    nextEffect: null,
  };

  return update;
}

function appendUpdateToQueue<State>(
  queue: UpdateQueue<State>,
  update: Update<State>,
) {
  // Append the update to the end of the list.
  if (queue.lastUpdate === null) {
    // Queue is empty
    queue.firstUpdate = queue.lastUpdate = update;
  } else {
    queue.lastUpdate.next = update;
    queue.lastUpdate = update;
  }
}

export function enqueueUpdate<State>(fiber: Fiber, update: Update<State>) {
  const alternate = fiber.alternate;
  let queue1;
  let queue2;
  
  // 当前节点初次产生更新
  if (alternate === null) {
    queue1 = fiber.updateQueue;
    queue2 = null;
    // 创建updateQueue, 并将其挂载在Fiber对象上
    if (queue1 === null) {
      queue1 = fiber.updateQueue = createUpdateQueue(fiber.memoizedState);
    }
  } else {
    // There are two owners.
    queue1 = fiber.updateQueue;
    queue2 = alternate.updateQueue;
    if (queue1 === null) {
      if (queue2 === null) {
        // Neither fiber has an update queue. Create new ones.
        queue1 = fiber.updateQueue = createUpdateQueue(fiber.memoizedState);
        queue2 = alternate.updateQueue = createUpdateQueue(
          alternate.memoizedState,
        );
      } else {
        // Only one fiber has an update queue. Clone to create a new one.
        queue1 = fiber.updateQueue = cloneUpdateQueue(queue2);
      }
    } else {
      if (queue2 === null) {
        // Only one fiber has an update queue. Clone to create a new one.
        queue2 = alternate.updateQueue = cloneUpdateQueue(queue1);
      } else {
        // Both owners have an update queue.
      }
    }
  }
  if (queue2 === null || queue1 === queue2) {
    // There's only a single queue.
    appendUpdateToQueue(queue1, update);
  } else {
    // There are two queues. We need to append the update to both queues,
    // while accounting for the persistent structure of the list — we don't
    // want the same update to be added multiple times.
    if (queue1.lastUpdate === null || queue2.lastUpdate === null) {
      // One of the queues is not empty. We must add the update to both queues.
      appendUpdateToQueue(queue1, update);
      appendUpdateToQueue(queue2, update);
    } else {
      // Both queues are non-empty. The last update is the same in both lists,
      // because of structural sharing. So, only append to one of the lists.
      appendUpdateToQueue(queue1, update);
      // But we still need to update the `lastUpdate` pointer of queue2.
      queue2.lastUpdate = update;
    }
  }
}
```
# expirationTime
在之前的代码中, 多次涉及到了expirationTime。这是用于判断更新任务优先级的重要标志。
由于篇幅过长, 详情见[React中的expirationTime](https://xdyushenli.github.io/2019/11/07/react-expiration-time/)。

# setState & forceUpdate
## 核心
* 都是给节点的Fiber创建更新。
  * 对于ReactDOM.render而言, 节点是整个应用。
  * 对于setState和forceUpdate而言, 节点是当前的Class Component的实例。
* 更新类型不同, 二者生成的update对象上的tag属性不同。
## 说明
setState和forceUpdate的源代码和updateContainer基本一致, 所以不做赘述。
