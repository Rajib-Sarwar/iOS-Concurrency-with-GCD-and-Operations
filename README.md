# Concurrency with GCD and Priority Queues

### Summary of Grand Central Dispatch Queues

Grand Central Dispatch (GCD) manages threads, providing global and private queues for task execution. 

#### **Global Queues**
- **Main Queue**: Serial, used for UI updates on the main thread.
```swift
DispatchQueue.main
```

- **Other Global Queues**: Concurrent, support multiple threads with varying priorities (Quality of Service, QoS):
  - **User Interactive**: For immediate tasks like touch gestures.
  - **User Initiated**: Tasks initiated by the user, e.g., opening a file.
  - **Utility**: Long-running tasks like networking or data processing.
  - **Background**: Non-critical tasks like prefetching, synchronizing with remote server, backups or maintenance.
  - Default QoS is used if none is specified.

```swift
DispatchQueue.global(qos: .userInteractive)
DispatchQueue.global(qos: .userInitiated)
DispatchQueue.global(qos: .utility)
DispatchQueue.global(qos: .background)
DispatchQueue.global() // assigned .default qos
DispatchQueue.global(qos: .unspecified)
```

#### **Private Queues**
- Created with a label and optional QoS or attributes.
- **Serial by Default**: Ensure thread safety for shared resources.
- **Concurrent Attribute**: Allows multiple threads for debugging or implementing barriers.
```swift
// Creating a private serial queue (default behavior)
let serialQueue = DispatchQueue(label: "com.example.serialQueue")

// Creating a private concurrent queue
let concurrentQueue = DispatchQueue(label: "com.example.concurrentQueue", qos: .utility, attributes: .concurrent)

```

#### **Task Dispatch**
- **Asynchronous (async)**: The current queue continues without waiting.
- **Synchronous (sync)**: The current queue waits for the task to complete.
  - Avoid using `sync` on the main queue as it causes UI freezes.

#### **Concurrency Concepts**
- **Serial Queues**: Run one task at a time, ensuring thread safety.
- **Concurrent Queues**: Run multiple tasks simultaneously by creating threads.
- **Synchronous vs. Asynchronous**: Determines whether the source queue waits for task completion.
- **Serial vs. Concurrent**: Determines the number of threads in the destination queue.

#### **Common Use Cases**
- Dispatch non-UI tasks asynchronously to concurrent queues.
- Dispatch UI tasks back to the main queue asynchronously.
- Use `sync` on serial queues for critical, short tasks like reading shared resources.

#### **QoS Adjustments**
- The system promotes QoS if a higher-priority task is added to a queue, solving priority inversion.

#### **Best Practices**
- Use serial queues for thread-safe writes.
- Use concurrent queues for tasks that allow multiple reads.
- Dispatch long-running tasks off the main queue with `async`.

The next part explores **dispatch barriers** for handling simultaneous read and write operations.
