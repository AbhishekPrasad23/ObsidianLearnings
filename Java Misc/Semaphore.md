
In Java, a `Semaphore` is a synchronization primitive that controls access to a shared resource by multiple threads. It maintains a set of permits, where each `acquire()` call blocks if necessary until a permit is available, and each `release()` call adds a permit, potentially releasing a blocking acquirer.

### Key Methods in `Semaphore`
1. **`Semaphore(int permits)`**: Constructs a `Semaphore` with the given number of permits.
2. **`Semaphore(int permits, boolean fair)`**: Constructs a `Semaphore` with the given number of permits and a fairness setting. If `fair` is `true`, threads acquire permits in FIFO order.
3. **`acquire()`**: Acquires a permit, blocking until one is available.
4. **`acquire(int permits)`**: Acquires the specified number of permits, blocking until all are available.
5. **`release()`**: Releases a permit, increasing the number of available permits by one.
6. **`release(int permits)`**: Releases the specified number of permits.
7. **`tryAcquire()`**: Attempts to acquire a permit without blocking. Returns `true` if successful, `false` otherwise.
8. **`tryAcquire(int permits)`**: Attempts to acquire the specified number of permits without blocking.
9. **`tryAcquire(long timeout, TimeUnit unit)`**: Attempts to acquire a permit within the given timeout.
10. **`availablePermits()`**: Returns the number of available permits.

### Example Usage
Here’s an example of how to use a `Semaphore` to control access to a shared resource:

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    private static final int PERMITS = 3; // Number of permits
    private static final Semaphore semaphore = new Semaphore(PERMITS, true); // Fair semaphore

    public static void main(String[] args) {
        // Create and start 10 threads
        for (int i = 0; i < 10; i++) {
            Thread thread = new Thread(new Worker(i));
            thread.start();
        }
    }

    static class Worker implements Runnable {
        private final int id;

        public Worker(int id) {
            this.id = id;
        }

        @Override
        public void run() {
            try {
                System.out.println("Worker " + id + " is trying to acquire a permit.");
                semaphore.acquire(); // Acquire a permit
                System.out.println("Worker " + id + " has acquired a permit.");

                // Simulate work
                Thread.sleep(2000);

                System.out.println("Worker " + id + " is releasing the permit.");
                semaphore.release(); // Release the permit
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### Explanation
1. The `Semaphore` is initialized with 3 permits, meaning up to 3 threads can access the shared resource simultaneously.
2. Each `Worker` thread tries to acquire a permit using `semaphore.acquire()`. If no permits are available, the thread blocks until one is released.
3. After acquiring a permit, the thread simulates work by sleeping for 2 seconds.
4. Once the work is done, the thread releases the permit using `semaphore.release()`, allowing other threads to acquire it.

### Output Example
```
Worker 0 is trying to acquire a permit.
Worker 0 has acquired a permit.
Worker 1 is trying to acquire a permit.
Worker 1 has acquired a permit.
Worker 2 is trying to acquire a permit.
Worker 2 has acquired a permit.
Worker 3 is trying to acquire a permit.
Worker 4 is trying to acquire a permit.
Worker 5 is trying to acquire a permit.
Worker 0 is releasing the permit.
Worker 3 has acquired a permit.
Worker 1 is releasing the permit.
Worker 4 has acquired a permit.
Worker 2 is releasing the permit.
Worker 5 has acquired a permit.
...
```

### Key Points
- Semaphores are useful for limiting the number of threads accessing a resource.
- They can be used to implement resource pools, throttling, or mutual exclusion.
- The fairness parameter ensures that threads acquire permits in the order they requested them, avoiding thread starvation.

Let me know if you need further clarification!