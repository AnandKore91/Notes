# Grand Central Dispatch (GCD)
    Grand Central Dispatch (GCD) is a low-level API for managing concurrent operations. It can help you improve your app’s responsiveness by deferring computationally expensive tasks to the background. It’s an easier concurrency model to work with than locks and threads.

## Concurrency
- The operating system scheduler manages the threads independently of each other. Each thread can execute concurrently, but it’s up to the system to decide if this happens, when this happens, and how it happens.

- Single-core devices achieve concurrency through a method called time-slicing. They run one thread, perform a context switch, then run another thread.

- Multi-core devices, on the other hand, execute multiple threads at the same time via parallelism.

- GCD is built on top of threads. Under the hood it manages a shared thread pool.

- With GCD you add blocks of code or work items to dispatch queues and GCD decides which thread to execute them on.

- Note that GCD decides how much parallelism it requires based on the system and available system resources. It’s important to note that parallelism requires concurrency, but concurrency does not guarantee parallelism.

- Basically, concurrency is about structure while parallelism is about execution.

## Queues
- GCD operates on dispatch queues through a class aptly named DispatchQueue. You submit units of work to this queue and GCD will execute them in a FIFO order (First In, First Out), guaranteeing that the first task submitted is the first one started.

- Dispatch queues are thread-safe which means that you can access them from multiple threads simultaneously.

- Queues can be either serial or concurrent.

**Serial queues**
    Serial queues guarantee that only one task runs at any given time. GCD controls the execution timing. You won’t know the amount of time between one task ending and the next one beginning.

**Concurrent queues**
    Concurrent queues allow multiple tasks to run at the same time. The queue guarantees tasks start in the order you add them. Tasks can finish in any order and you have no knowledge of the time it will take for the next task to start, nor the number of tasks that are running at any given time.

## GCD provides three main types of queues:
1. **Main queue**:
    Runs on the main thread and is a serial queue.

2. **Global queues**:
    Concurrent queues that are shared by the whole system. There are four such queues with different priorities : high, default, low, and background. The background priority queue has the lowest priority and is throttled in any I/O activity to minimize negative system impact.

3. **Custom queues**:
    Queues that you create which can be serial or concurrent. Requests in these queues actually end up in one of the global queues.

## Synchronous vs. Asynchronous:
**Synchronous**:
    A synchronous function returns control to the caller after the task completes. You can schedule a unit of work synchronously by calling DispatchQueue.sync(execute:)

**Asynchronous**:
    An asynchronous function returns immediately, ordering the task to start but not waiting for it to complete. Thus, an asynchronous function does not block the current thread of execution from proceeding on to the next function. You can schedule a unit of work asynchronously by calling DispatchQueue.async(execute:).

## Closures:
    Closures are self-contained, callable blocks of code you can store and pass around.

    Each task you submit to a DispatchQueue is a *DispatchWorkItem*. You can configure the behavior of a *DispatchWorkItem* such as its QoS class or whether to spawn a new detached thread.

## Sample:
```
DispatchQueue.global(qos: .userInitiated).async { [weak self] in
  guard let self = self else {
    return
  }
  let overlayImage = self.faceOverlayImageFrom(self.image)
  DispatchQueue.main.async { [weak self] in
    self?.fadeInNewImage(overlayImage)
  }
}
```
Here’s what the code’s doing step by step:

    1. You move the work to a background global queue and run the work in the closure asynchronously. This lets viewDidLoad() finish earlier on the main thread and makes the loading feel more snappy. Meanwhile, the face detection processing is started and will finish at some later time.

    2. At this point, the face detection processing is complete and you’ve generated a new image. Since you want to use this new image to update your UIImageView, you add a new closure to the main queue. Remember – anything that modifies the UI must run on the main thread!

    3. Finally, you update the UI with fadeInNewImage(_:)_ which performs a fade-in transition of the new googly eyes image.

## Quick guide of how and when to use the various queues with async:
    1. **Main Queue**: This is a common choice to update the UI after completing work in a task on a concurrent queue. To do this, you code one closure inside another. Targeting the main queue and calling async guarantees that this new task will execute sometime after the current method finishes.

    2. **Global Queue**: This is a common choice to perform non-UI work in the background.

    3. **Custom Serial Queue**: A good choice when you want to perform background work serially and track it. This eliminates resource contention and race conditions since you know only one task at a time is executing. Note that if you need the data from a method, you must declare another closure to retrieve it or consider using sync.

## Delaying Task Execution:
```
// 1
let delayInSeconds = 2.0

// 2
DispatchQueue.main.asyncAfter(deadline: .now() + delayInSeconds) { [weak self] in
  guard let self = self else {
    return
  }

  if PhotoManager.shared.photos.count > 0 {
    self.navigationItem.prompt = nil
  } else {
    self.navigationItem.prompt = "Add photos with faces to modify them!"
  }

  // 3
  self.navigationController?.viewIfLoaded?.setNeedsLayout()
}
```

Here’s what’s going on above:
    1. You specify the amount of time to delay.
    2. You then wait for the specified time then asynchronously run the block which updates the photos count and updates the prompt.
    3. Force the navigation bar to lay out after setting the prompt to make sure it looks kosher.

## Why not use Timer?
    You could consider using it if you have repeated tasks which are easier to schedule with Timer. Here are two reasons to stick with dispatch queue’s asyncAfter().

    One is readability. To use Timer you have to define a method, then create the timer with a selector or invocation to the defined method. With DispatchQueue and asyncAfter(), you simply add a closure.

    Timer is scheduled on run loops so you would also have to make sure you scheduled it on the correct run loop (and in some cases for the correct run loop modes). In this regard, working with dispatch queues is easier.
