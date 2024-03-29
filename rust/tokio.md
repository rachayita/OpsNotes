# async fn

- return value of async fn is an anonymous type that implements Future trait
- async fn must be executed by a runtime
- runtime contains asynchronous task scheduler, evented i/o, timers, etc
- runtime doesnot automatically start, so the main fn need to start it
- #[tokio::main] macro transforms async fn main() into a synchronous fn main() 
  - that initializes a runtime instance and execute the async main fn

  # tasks
- tasks are very lightweight
  - they require only a single allocation and 64 bytes of memory
  - applications should feel free to spawn thousands, if not millions of tasks
- at spawning a task on runtime, its type's lifetime must be `'static`
  - spawned task must not contain any references to data owned outside the task
