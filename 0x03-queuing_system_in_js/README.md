# Queuing System in JS
A **queuing system** in JavaScript is a data structure or logic used to manage tasks or requests in a sequential, first-in, first-out (FIFO) order. Queues are commonly used in scenarios like managing asynchronous operations, handling job processing, or implementing rate-limited APIs.

Here’s an overview of how to create a queuing system in JavaScript.



### **Components of a Queuing System**

1.  **Queue**: A data structure to hold the tasks.
2.  **Enqueue**: Add an item to the queue.
3.  **Dequeue**: Remove an item from the queue for processing.
4.  **Processor**: A function to handle the tasks one at a time, often asynchronously.



### **Creating a Basic Queue**

A basic queue can be implemented using an array. Here’s an example:
```
class Queue {
    constructor() {
        this.items = [];
    }

    // Add an item to the queue
    enqueue(item) {
        this.items.push(item);
    }

    // Remove an item from the queue
    dequeue() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items.shift();
    }

    // Check if the queue is empty
    isEmpty() {
        return this.items.length === 0;
    }

    // View the item at the front of the queue
    front() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items[0];
    }

    // Get the size of the queue
    size() {
        return this.items.length;
    }
}

// Example usage
const queue = new Queue();
queue.enqueue("Task 1");
queue.enqueue("Task 2");
console.log(queue.dequeue()); // "Task 1"
console.log(queue.front());   // "Task 2"
```
### **Asynchronous Queuing System**

For handling tasks asynchronously, you can extend the basic queue by adding a task processor.

**Example:**
```
class AsyncQueue {
    constructor() {
        this.queue = [];
        this.processing = false;
    }

    // Add a task to the queue
    enqueue(task) {
        this.queue.push(task);
        this.process();
    }

    // Process tasks in the queue
    async process() {
        if (this.processing || this.queue.length === 0) {
            return;
        }

        this.processing = true;

        const task = this.queue.shift();
        try {
            await task(); // Execute the task (assumes the task is a Promise-returning function)
        } catch (error) {
            console.error("Error processing task:", error);
        } finally {
            this.processing = false;
            this.process(); // Process the next task
        }
    }
}

// Example usage
const asyncQueue = new AsyncQueue();

asyncQueue.enqueue(async () => {
    console.log("Processing Task 1");
    await new Promise((resolve) => setTimeout(resolve, 1000)); // Simulate async work
    console.log("Task 1 completed");
});

asyncQueue.enqueue(async () => {
    console.log("Processing Task 2");
    await new Promise((resolve) => setTimeout(resolve, 500));
    console.log("Task 2 completed");
});
```
### **Adding Rate Limiting**

You can modify the queue processor to include a delay between task executions, useful for rate-limiting API requests.

**Example:**
```
class RateLimitedQueue {
    constructor(delay = 1000) {
        this.queue = [];
        this.delay = delay; // Delay between task executions in milliseconds
        this.processing = false;
    }

    enqueue(task) {
        this.queue.push(task);
        this.process();
    }

    async process() {
        if (this.processing || this.queue.length === 0) {
            return;
        }

        this.processing = true;

        while (this.queue.length > 0) {
            const task = this.queue.shift();
            try {
                await task();
            } catch (error) {
                console.error("Error processing task:", error);
            }
            await new Promise((resolve) => setTimeout(resolve, this.delay));
        }

        this.processing = false;
    }
}

// Example usage
const rateLimitedQueue = new RateLimitedQueue(2000); // 2-second delay

rateLimitedQueue.enqueue(async () => {
    console.log("Task 1 started");
    await new Promise((resolve) => setTimeout(resolve, 1000));
    console.log("Task 1 completed");
});

rateLimitedQueue.enqueue(async () => {
    console.log("Task 2 started");
    await new Promise((resolve) => setTimeout(resolve, 500));
    console.log("Task 2 completed");
});
```
### **Real-Life Use Cases**

1.  **Task Processing**:
    -   In worker threads or job queues (e.g., RabbitMQ, Kafka).
2.  **Rate Limiting**:
    -   Manage API calls to avoid exceeding rate limits.
3.  **Debouncing or Throttling**:
    -   Ensure operations (like keystroke handling) don’t overwhelm the system.
4.  **Event Management**:
    -   Queueing events for sequential processing.
5.  **File Processing**:
    -   Manage file uploads or downloads in a controlled manner.



### **Using Third-Party Libraries**

Instead of building a queue from scratch, you can use libraries like:

-   **Bull**: A robust queuing library for Node.js, integrated with Redis.
-   **Bee-Queue**: A lightweight alternative to Bull for Redis-based queues.
-   **p-queue**: A simple promise-based queue for managing async tasks.

**Example with Bull:**
```
npm install bull
```

```
const Queue = require('bull');

const jobQueue = new Queue('jobs');

jobQueue.process(async (job) => {
    console.log(`Processing job: ${job.id}`);
    await new Promise((resolve) => setTimeout(resolve, 1000)); // Simulated work
    console.log(`Job completed: ${job.id}`);
});

jobQueue.add({ name: 'Job 1' });
jobQueue.add({ name: 'Job 2' });
```
### **Conclusion**

A queuing system in JavaScript can range from a simple array-based implementation to a fully asynchronous rate-limited processor. For advanced use cases, you can leverage third-party libraries like Bull for production-grade systems. By designing and implementing queues thoughtfully, you can manage tasks efficiently and ensure smooth operation in your applications.

### -   How to run a Redis server on your machine
Running a Redis server on your machine involves installing Redis, starting the server, and optionally configuring it for specific use cases. Here's a step-by-step guide for running Redis on common operating systems.
### **1. Installing Redis**

#### **On Linux (Ubuntu/Debian):**

1.  **Update your package lists:**
    
	```
	sudo apt update
	```
2. Install Redis:
	```
	sudo apt install redis-server -y
	```
3. **Verify the installation**
	```
	redis-cli --version
	```
### **2. Running the Redis Server**

#### **Default Configuration:**

1.  **Start the Redis server:**
	```
	redis-server
	```
	This starts the server with default settings (port `6379`).
2. **Test the server:** Open a new terminal and run:
redis-cli ping
	```
	redis-cli ping
	```
	1If everything works, Redis will respond with `PONG`.
    

#### **Running Redis in the Background:**

1.  Modify the Redis configuration file (typically at `/etc/redis/redis.conf`

	```
	sudo nano /etc/redis/redis.conf
	```

	
2. Set `daemonize` to `yes`
	```
	daemonize yes
	```
3. Restart the Redis service
	```
	sudo service redis-server restart
	```
### **3. Managing the Redis Service**

#### **On Linux:**

-   **Start Redis**
	```
	sudo service redis-server start
	```
- Stop Redis
	```
	sudo service redis-server stop
	```
- Restart Redis
```
sudo service redis-server restart
```
- Check status
```
sudo service redis-server status
```

### **4. Configuring Redis**

Redis can be customized by editing its configuration file. The default configuration file is typically located at:

-   **Linux:** `/etc/redis/redis.conf`
-   **macOS:** `/usr/local/etc/redis.conf`
-   **Windows:** Configuration is embedded or provided as a `.conf` file.

**Common Configurations:**

-   Change the default port
```
port 6380
```
- Set a password for security
```
requirepass your_password
```
- Limit the maximum memory
```
maxmemory 256mb
maxmemory-policy allkeys-lru
```
Restart the Redis server after making changes for them to take effect.


### **5. Running Redis in Docker**

An alternative way to run Redis is via Docker, which avoids installing it natively on your machine.

1.  **Install Docker:** Follow the Docker installation guide.
    
2.  **Pull the Redis Docker image:**
```
docker pull redis
```
3. Run the Redis container
```
docker run --name redis-server -p 6379:6379 -d redis
```
4. Access Redis CLI
```
docker exec -it redis-server redis-cli
```
### **6. Verifying Redis is Running**

Use the Redis CLI to test the connection:

1.  Run:
```
redis-cli
```
2. Test commands
```
PING
SET key value
GET key
```
If Redis is running properly, you should see responses like `PONG`, and your key-value operations should work.
### **7. Monitoring Redis**

Redis provides built-in commands to monitor and debug:

-   **Monitor commands in real-time**`
```
redis-cli monitor
```
- View server information
```
redis-cli info
```
**Check connected clients:**

```
redis-cli client list
```
### **Conclusion**

Running a Redis server locally is straightforward, with options for native installation or containerized environments like Docker. With Redis properly configured and managed, it can serve as a powerful in-memory data store for caching, session management, job queues, and more.

##  How to run simple operations with the Redis client
