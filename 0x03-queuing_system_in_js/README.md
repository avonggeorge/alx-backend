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

### **1. Accessing the Redis Client**

To interact with the Redis server:

1.  Open a terminal.
2.  Run the following command
```
redis-cli
```
3.  You’ll see a prompt like
```
127.0.0.1:6379>
```
This indicates that you’re connected to the Redis server running on `127.0.0.1` (localhost) on port `6379`.

### **2. Basic Commands**

#### **Set and Get Keys**

-   **Set a key-value pair:**
```
SET mykey "Hello, Redis!"
```
- Retrieve the value of a key
	```
	GET mykey
	```
	Output
	```
	"Hello, Redis!"
	```
#### **Delete a Key**

-   **Delete a key:**
	```
	DEL mykey
	```
- Check if a key exists:
	```
	EXISTS mykey
	```
	Output
	```
	(integer) 0  # Key does not exist
	```
#### **Set a Key with Expiry**

-   **Set a key with a time-to-live (TTL)**
	```
	SETEX mykey 10 "Temporary Value"
	```
	This key will expire in 10 seconds
- Check the TTL of a key
	```
	TTL mykey
   ```
### **3. Working with Data Structures**

#### **Strings**

-   **Increment a numeric value**
	```
	INCR counter
	```
-  Append to a string
	```
	APPEND mykey " Welcome!"
   ```
   Retrieve the updated string
   ```
   GET mykey
   ```
   #### **Lists**

-   **Add items to a list (left push)**
	```
	LPUSH mylist "Item1"
	LPUSH mylist "Item2"
	```
-	**Retrieve all items in a list**
	```
	LRANGE mylist 0 -1
	```
- **Remove and retrieve the last item:**

	```
	RPOP mylist
	```
	#### **Hashes**

-   **Set a field in a hash**
	```
	HSET user:1 name "George" age 25
	```
- **Retrieve a specific field:**
	```
	HGET user:1 name
	```
- **Retrieve all fields and values in a hash**
	```
	HGETALL user:1
	```
**Sets**
Add items to a set
```
SADD myset "A" "B" "C"
```
- Retrieve all items in a set 
	```
	SMEMBERS myset
	```
- Check membership
	```
	SISMEMBER myset "A"
	```
#### **Sorted Sets**

-   **Add items with scores**
	```
	ZADD scores 100 "Alice" 200 "Bob"
	```
- Retrieve items by score 
	```
	ZRANGE scores 0 -1 WITHSCORES
	```

### **4. Managing Keys**

#### **View All Keys**

-   **List all keys:**
	```
	KEYS *
	```
	Use pattern to match keys
	```
	KEYS user:*
	```
#### **Expire Keys**

-   **Set an expiration time:**
    ```
    EXPIRE mykey 30
	```
	This sets the key to expire in 30 seconds .
#### **Rename a Key**

-   **Rename a key**
	```
	RENAME oldkey newkey
	```
### **5. Transactions**

-   **Start a transaction**
	```
	MULTI
	```
- **Queue commands**
	```
	SET key1 "value1"
	SET key2 "value2"
	```
- **Execute the transaction:**
	```
	EXEC
	```
	To discard queued commands
	```
	DISCARD
	```
### **6. Monitoring and Debugging**

#### **Monitor Commands in Real-Time**

-   **Monitor all operations on the Redis server:**
	```
	MONITOR
	```
#### **View Memory Usage**

-   **Check memory usage for a key**
	```
	MEMORY USAGE mykey
	```
#### **Server Information**

-   **View server stats**
	```
	INFO
	```
### **Example Session**

```
127.0.0.1:6379> SET mykey "Hello"
OK
127.0.0.1:6379> GET mykey
"Hello"
127.0.0.1:6379> LPUSH mylist "A"
(integer) 1
127.0.0.1:6379> LPUSH mylist "B"
(integer) 2
127.0.0.1:6379> LRANGE mylist 0 -1
1) "B"
2) "A"
127.0.0.1:6379> DEL mykey
(integer) 1
127.0.0.1:6379> EXISTS mykey
(integer) 0
```
### **Conclusion**

The Redis client (`redis-cli`) is a powerful tool for performing basic to advanced operations with Redis. Its simple syntax allows quick interaction with keys, data structures, and the Redis server



## -   How to use a Redis client with Node JS for basic operations
Using a Redis client with Node.js enables you to interact with a Redis server programmatically. Here's how you can perform basic operations using the popular `redis` Node.js package.



### **1. Install the Redis Package**

First, install the `redis` package using npm

```
npm install redis
```
### **2. Set Up the Redis Client**

Create a basic Node.js script, and import the `redis` package:
```
const redis = require('redis');

// Create a Redis client
const client = redis.createClient();

// Handle connection events
client.on('connect', () => {
    console.log('Connected to Redis!');
});

client.on('error', (err) => {
    console.error(`Error: ${err}`);
});

// Connect the client to the Redis server
client.connect();
```
### **3. Perform Basic Operations**

#### **Set and Get a Key**
```
async function setAndGetKey() {
    // Set a key-value pair
    await client.set('mykey', 'Hello, Redis!');
    console.log('Key set successfully');

    // Retrieve the value of a key
    const value = await client.get('mykey');
    console.log(`Value: ${value}`);
}

setAndGetKey();
```
**Delete a Key**
```
async function deleteKey() {
    // Delete a key
    await client.del('mykey');
    console.log('Key deleted');
}

deleteKey();
```
**Set a Key with Expiry**
```
async function setKeyWithExpiry() {
    // Set a key with an expiry of 10 seconds
    await client.setEx('tempkey', 10, 'Temporary Value');
    console.log('Key with expiry set');

    // Check TTL (time-to-live) for the key
    const ttl = await client.ttl('tempkey');
    console.log(`Time-to-live: ${ttl} seconds`);
}

setKeyWithExpiry();
```
### **4. Working with Data Structures**

#### **Lists**
```
async function listOperations() {
    // Push values to a list
    await client.lPush('mylist', 'Item1');
    await client.lPush('mylist', 'Item2');
    console.log('Items added to list');

    // Retrieve list elements
    const list = await client.lRange('mylist', 0, -1);
    console.log('List items:', list);

    // Pop an element from the list
    const popped = await client.rPop('mylist');
    console.log('Popped item:', popped);
}

listOperations();
```
**Hashes**
```
async function hashOperations() {
    // Set fields in a hash
    await client.hSet('user:1', 'name', 'George', 'age', '25');
    console.log('Hash set successfully');

    // Retrieve a specific field
    const name = await client.hGet('user:1', 'name');
    console.log('Name:', name);

    // Retrieve all fields and values
    const user = await client.hGetAll('user:1');
    console.log('User:', user);
}

hashOperations();
```
**Sets**
```
async function setOperations() {
    // Add elements to a set
    await client.sAdd('myset', 'A', 'B', 'C');
    console.log('Set created');

    // Retrieve all elements of a set
    const members = await client.sMembers('myset');
    console.log('Set members:', members);

    // Check if an element exists in the set
    const isMember = await client.sIsMember('myset', 'A');
    console.log('Is A in the set?', isMember);
}

setOperations();
```
**Sorted Sets**
```
async function sortedSetOperations() {
    // Add elements to a sorted set with scores
    await client.zAdd('scores', [
        { score: 100, value: 'Alice' },
        { score: 200, value: 'Bob' },
    ]);
    console.log('Sorted set created');

    // Retrieve elements by score
    const sortedSet = await client.zRange('scores', 0, -1, { withScores: true });
    console.log('Sorted set:', sortedSet);
}

sortedSetOperations();
```
### **5. Using Transactions**

You can queue multiple commands using the `multi` method and execute them atomically:

```
async function transactionExample() {
    // Begin a transaction
    const pipeline = client.multi();

    // Queue commands
    pipeline.set('key1', 'value1');
    pipeline.set('key2', 'value2');

    // Execute the transaction
    const results = await pipeline.exec();
    console.log('Transaction results:', results);
}

transactionExample();
```
### **6. Disconnect the Client**

Once you're done interacting with Redis, close the connection
```
client.quit();
```
### **Full Example**

Here's a complete example that demonstrates the above operations:
```
const redis = require('redis');

(async () => {
    const client = redis.createClient();

    client.on('connect', () => console.log('Connected to Redis'));
    client.on('error', (err) => console.error(`Redis error: ${err}`));

    await client.connect();

    // Set and get a key
    await client.set('mykey', 'Hello, Redis!');
    console.log('Set key:', await client.get('mykey'));

    // Work with a list
    await client.lPush('mylist', 'Item1', 'Item2');
    console.log('List:', await client.lRange('mylist', 0, -1));

    // Close the connection
    await client.quit();
})();
```

### **Conclusion**

Using Redis with Node.js is straightforward and powerful, allowing you to perform a wide range of operations such as managing strings, lists, hashes, sets, and sorted sets. With its async API, the Redis client integrates seamlessly with modern JavaScript workflows.

## -   How to store hash values in Redis
Storing hash values in Redis is straightforward and useful when you want to store structured data, such as user profiles or configuration settings. Redis hashes are ideal for representing objects where you can access individual fields without retrieving the entire object.
### **1. Storing Hash Values**

Redis provides the `HSET` command to store field-value pairs in a hash. Each hash is identified by a key, and you can add multiple fields to the hash.

#### Example:
```
const redis = require('redis');

(async () => {
    const client = redis.createClient();

    client.on('connect', () => console.log('Connected to Redis'));
    client.on('error', (err) => console.error(`Redis error: ${err}`));

    await client.connect();

    // Add fields to a hash
    await client.hSet('user:1000', {
        name: 'George',
        age: '30',
        profession: 'Software Engineer',
    });
    console.log('Hash created successfully');

    // Close the connection
    await client.quit();
})();
```
### **2. Retrieving Hash Values**

To retrieve values from a hash, you can use the following commands:

#### **Retrieve a Specific Field:**
```
const name = await client.hGet('user:1000', 'name');
console.log(`Name: ${name}`);
```
**Retrieve All Fields and Values:**
```
const user = await client.hGetAll('user:1000');
console.log('User:', user);
```
### **3. Modifying Hash Fields**

You can update specific fields of a hash without affecting other fields using `HSET`:
```
await client.hSet('user:1000', 'age', '31');
console.log('Age updated');
```
### **4. Checking for Field Existence**

You can check if a specific field exists in a hash using `HEXISTS`:
```
const exists = await client.hExists('user:1000', 'profession');
console.log(`Profession exists? ${exists}`);
```
### **5. Deleting Fields from a Hash**

To remove specific fields from a hash, use the `HDEL` command:
```
await client.hDel('user:1000', 'profession');
console.log('Field deleted');
```
### **6. Incrementing Numeric Fields**

You can increment numeric fields in a hash using `HINCRBY`:
```
await client.hIncrBy('user:1000', 'age', 1);
console.log('Age incremented');
```
### **7. Retrieve All Field Names or Values**

To get only the field names or values:

-   **Field Names:**
	```
	const fields = await client.hKeys('user:1000');
	console.log('Fields:', fields);
	```
- **Field Values:**
	```
	const values = await client.hVals('user:1000');
	console.log('Values:', values);
	```
	### **8. Full Example**

Here’s a complete example that includes all the operations:
```
const redis = require('redis');

(async () => {
    const client = redis.createClient();

    client.on('connect', () => console.log('Connected to Redis'));
    client.on('error', (err) => console.error(`Redis error: ${err}`));

    await client.connect();

    // Store hash values
    await client.hSet('user:1000', {
        name: 'George',
        age: '30',
        profession: 'Software Engineer',
    });
    console.log('Hash created');

    // Retrieve all fields and values
    const user = await client.hGetAll('user:1000');
    console.log('User:', user);

    // Update a field
    await client.hSet('user:1000', 'age', '31');
    console.log('Age updated');

    // Increment age
    await client.hIncrBy('user:1000', 'age', 1);
    console.log('Age incremented');

    // Check field existence
    const exists = await client.hExists('user:1000', 'profession');
    console.log(`Profession exists? ${exists}`);

    // Get all field names
    const fields = await client.hKeys('user:1000');
    console.log('Fields:', fields);

    // Delete a field
    await client.hDel('user:1000', 'profession');
    console.log('Profession field deleted');

    // Close the connection
    await client.quit();
})();
```

### **Use Cases**

-   **User Profiles:** Store user details like name, age, email, etc.
-   **Configuration Settings:** Store application settings or preferences.
-   **Caching Database Results:** Cache frequently queried data to reduce database calls.

Redis hashes are efficient for small collections of key-value pairs, and they allow fine-grained access to individual fields.

## -   How to deal with async operations with Redis
Dealing with asynchronous operations in Redis using Node.js is essential to ensure proper handling of Redis responses and avoid issues like unhandled promises. Here's a guide on handling async operations effectively with Redis:
### **1. Using the Async API in the Redis Client**

The modern `redis` Node.js package provides an async/await API, which simplifies working with Redis asynchronously.

#### Example:
```
const redis = require('redis');

(async () => {
    const client = redis.createClient();

    client.on('connect', () => console.log('Connected to Redis'));
    client.on('error', (err) => console.error(`Redis error: ${err}`));

    await client.connect();

    try {
        // Set a key asynchronously
        await client.set('key1', 'value1');
        console.log('Key set');

        // Get the key asynchronously
        const value = await client.get('key1');
        console.log('Value retrieved:', value);
    } catch (error) {
        console.error('Error during Redis operations:', error);
    } finally {
        // Close the connection
        await client.quit();
    }
})();
```
### 2. Handling Promises with `.then()` and `.catch()`

If you prefer using promises directly without `async/await`, you can use `.then()` and `.catch()` for error handling.

#### Example:
```
const redis = require('redis');

const client = redis.createClient();

client.on('connect', () => console.log('Connected to Redis'));
client.on('error', (err) => console.error(`Redis error: ${err}`));

client.connect()
    .then(() => client.set('key2', 'value2'))
    .then(() => client.get('key2'))
    .then((value) => console.log('Value retrieved:', value))
    .catch((err) => console.error('Redis error:', err))
    .finally(() => client.quit());
```
### **3. Using Callbacks (Legacy Approach)**

While the `redis` package now supports async/await natively, the legacy callback approach is still valid, though less modern and harder to read.

#### Example:
```
const redis = require('redis');
const client = redis.createClient();

client.on('connect', () => console.log('Connected to Redis'));
client.on('error', (err) => console.error(`Redis error: ${err}`));

client.set('key3', 'value3', (err, reply) => {
    if (err) {
        console.error('Error setting key:', err);
    } else {
        console.log('Key set:', reply);

        client.get('key3', (err, value) => {
            if (err) {
                console.error('Error getting key:', err);
            } else {
                console.log('Value retrieved:', value);
            }
            client.quit();
        });
    }
});
```
### **4. Handling Multiple Async Operations**

To handle multiple Redis operations, you can use `Promise.all()` or chains of `async/await` calls.

#### Example: Using `Promise.all()`
```
(async () => {
    const client = redis.createClient();

    await client.connect();

    try {
        await Promise.all([
            client.set('keyA', 'valueA'),
            client.set('keyB', 'valueB'),
            client.set('keyC', 'valueC'),
        ]);

        const values = await Promise.all([
            client.get('keyA'),
            client.get('keyB'),
            client.get('keyC'),
        ]);

        console.log('Retrieved values:', values);
    } catch (err) {
        console.error('Error during operations:', err);
    } finally {
        await client.quit();
    }
})();
```
### **5. Using Transactions for Atomicity**

Redis supports transactions, which are useful when you need to execute multiple commands atomically. In Node.js, you can use `multi` to queue commands and execute them with `exec`.

#### Example:
```
(async () => {
    const client = redis.createClient();

    await client.connect();

    try {
        const pipeline = client.multi();

        pipeline.set('keyX', 'valueX');
        pipeline.set('keyY', 'valueY');

        const results = await pipeline.exec();
        console.log('Transaction results:', results);
    } catch (err) {
        console.error('Error during transaction:', err);
    } finally {
        await client.quit();
    }
})();
```
### **6. Error Handling for Async Operations**

Always wrap Redis operations in try-catch blocks to handle unexpected errors gracefully.

#### Example:
```
(async () => {
    const client = redis.createClient();

    await client.connect();

    try {
        await client.set('keyZ', 'valueZ');
        const value = await client.get('keyZ');
        console.log('Retrieved value:', value);
    } catch (err) {
        console.error('Error during Redis operation:', err);
    } finally {
        await client.quit();
    }
})();
```
### **7. Handling Long-Running Async Operations**

When dealing with long-running Redis commands, consider setting timeouts to avoid indefinite hangs.

#### Example:
```
(async () => {
    const client = redis.createClient();

    await client.connect();

    try {
        const promise = client.get('nonexistentKey');
        const value = await Promise.race([
            promise,
            new Promise((_, reject) => setTimeout(() => reject(new Error('Timeout')), 5000)), // 5-second timeout
        ]);

        console.log('Value:', value);
    } catch (err) {
        console.error('Error or timeout:', err);
    } finally {
        await client.quit();
    }
})();
```
### **8. Debugging Async Issues**

-   **Enable Debug Logs:** Use the `DEBUG` environment variable when running your Node.js app to see Redis logs.
	 ```
	 DEBUG=redis:* node app.js
	```
-   **Inspect Promises:** Use tools like `console.log` or `async_hooks` to track unresolved promises.



### **Summary**

To effectively deal with async operations in Redis with Node.js:

-   Use the `redis` package's native `async/await` API for clarity.
-   Handle errors with try-catch or `.catch()` for promises.
-   Use `Promise.all()` for parallel operations or `multi` for transactions.
-   Implement timeouts for long-running commands.
-   Always close the Redis connection (`quit()` or `disconnect()`) when done.

## -   How to use Kue as a queue system
Kue is a Node.js library for creating and managing a priority job queue system backed by Redis. It allows you to process jobs asynchronously, making it ideal for tasks like sending emails, processing videos, or performing background calculations.

Here’s a detailed guide on how to use **Kue** as a queue system:


### **1. Install Kue**

You need Node.js and Redis installed on your system.

#### Install the Kue package:
```
npm install kue
```
### **2. Set Up a Basic Queue**

Start by requiring the Kue library and creating a queue:

#### Example:
```
const kue = require('kue');
const queue = kue.createQueue();
```
### **3. Creating Jobs**

You can define jobs with specific types, data, and priorities.

#### Example: Creating a Job
```
const emailJob = queue.create('email', {
    title: 'Welcome Email',
    to: 'user@example.com',
    template: 'welcome-email',
}).save((err) => {
    if (!err) console.log(`Job created with ID: ${emailJob.id}`);
});
```
-    **`email`**: The type of the job.
-   **Data**: You can pass any JSON object as job data.
-   **`save()`**: Saves the job to the queue.

### **4. Processing Jobs**

Kue lets you define job processors for handling jobs of a specific type.

#### Example: Processing a Job
```
queue.process('email', (job, done) => {
    sendEmail(job.data, done);
});

// Simulated email sending function
function sendEmail(data, done) {
    console.log(`Sending email to ${data.to} with template ${data.template}`);
    // Simulate success
    setTimeout(done, 2000); // Mark job as done after 2 seconds
}
```
-   **`queue.process()`**: Defines how jobs of a particular type should be processed.
-   **`done()`**: A callback to indicate the job is completed. Pass an error to indicate failure.



### **5. Setting Job Priorities and Delays**

You can prioritize jobs (`low`, `normal`, `high`, `critical`) or delay them.

#### Example: Priority and Delayed Jobs
```
queue.create('email', {
    title: 'Password Reset',
    to: 'user@example.com',
    template: 'password-reset',
})
    .priority('high') // Set priority
    .delay(5000)      // Delay job by 5 seconds
    .save();
```
### **6. Monitoring Job Progress**

You can update the progress of a job during processing and track it.

#### Example: Progress Updates
```
queue.process('video', (job, done) => {
    let framesProcessed = 0;

    function processFrame() {
        framesProcessed++;
        job.progress(framesProcessed, 100); // Update progress
        if (framesProcessed === 100) done(); // Complete the job
        else setTimeout(processFrame, 50); // Simulate frame processing
    }

    processFrame();
});
```
### **7. Handling Events**

Kue emits events for different job states (e.g., `enqueue`, `start`, `complete`, `failed`).

#### Example: Listening to Events
```
queue.on('job enqueue', (id, type) => {
    console.log(`Job ${id} of type ${type} has been added to the queue`);
});

queue.on('job complete', (id, result) => {
    console.log(`Job ${id} completed with result: ${result}`);
});
```
### **8. Viewing the Kue Dashboard**

Kue provides a web-based dashboard to monitor jobs.

#### Install `kue-ui`:
```
npm install kue-ui
```
**Set up the dashboard:**
```
const express = require('express');
const kueUi = require('kue-ui');
const app = express();

kueUi.setup({
    apiURL: '/api', // API endpoint
    baseURL: '/kue', // UI base URL
    updateInterval: 5000, // Update interval (ms)
});

app.use('/api', kue.app);
app.use('/kue', kueUi.app);
app.listen(3000, () => console.log('Dashboard running on http://localhost:3000/kue'));
```
Navigate to `http://localhost:3000/kue` to view the job queue.


### **9. Managing Failed Jobs**

You can retry failed jobs or clean them up.

#### Example: Retry Failed Jobs
```
queue.on('job failed', (id, error) => {
    console.log(`Job ${id} failed with error: ${error}`);
    kue.Job.get(id, (err, job) => {
        if (!err) job.inactive(); // Retry the job
    });
});
```
### **10. Cleaning Up Jobs**

Kue allows cleaning up completed or failed jobs to prevent memory issues.

#### Example: Cleanup Jobs
```
queue.clean(5000, 'completed'); // Remove completed jobs older than 5000ms
queue.clean(5000, 'failed');    // Remove failed jobs older than 5000ms
```
### **11. Handling Graceful Shutdown**

Ensure the queue is shut down cleanly when the process exits.

#### Example:
```
process.on('SIGTERM', () => {
    queue.shutdown(5000, (err) => {
        console.log('Kue shutdown: ', err || 'success');
        process.exit(0);
    });
});
```
### **Example Full Workflow**

Here’s how everything ties together:

#### Producer:
```
const kue = require('kue');
const queue = kue.createQueue();

queue.create('email', {
    title: 'Welcome Email',
    to: 'user@example.com',
    template: 'welcome-email',
})
    .priority('high')
    .save();
```
Consumer:
```
const kue = require('kue');
const queue = kue.createQueue();

queue.create('email', {
    title: 'Welcome Email',
    to: 'user@example.com',
    template: 'welcome-email',
})
    .priority('high')
    .save();
```
### **Key Features of Kue**

-   **Scalable**: Can handle thousands of jobs.
-   **Priority Management**: Prioritize jobs based on importance.
-   **Job States**: Manage active, completed, delayed, and failed jobs.
-   **Web Interface**: Monitor jobs visually.
-   **Event-Driven**: React to job events for better control.

### **Alternatives**

While Kue is powerful, newer libraries like **Bull** and **Bee-Queue** are also worth exploring, as they may offer better performance and more features.

## -   How to build a basic Express app interacting with a Redis server

Building a basic Express app that interacts with a Redis server involves setting up an Express server and using the `redis` Node.js client to perform basic operations. Here’s a step-by-step guide:

### **1. Prerequisites**

Ensure you have the following installed:

-   **Node.js**: For running the Express app.
-   **Redis**: Installed and running on your machine or a remote server.



### **2. Install Required Packages**

Run the following command to install Express and the Redis client:

```
npm install express redis
```
### **3. Initialize the Project**

Set up a basic Node.js project if you haven’t already:
```
npm init -y
```
### **4. Create the Express App**

Create a file named `app.js` (or any name of your choice) and include the following code:

#### **Basic Express and Redis Setup**
```
const express = require('express');
const redis = require('redis');

// Initialize Express
const app = express();
app.use(express.json()); // Middleware to parse JSON

// Connect to Redis server
const redisClient = redis.createClient({
    host: '127.0.0.1',
    port: 6379,
});

// Handle Redis connection errors
redisClient.on('error', (err) => {
    console.error('Redis error:', err);
});

// Confirm Redis connection
redisClient.on('connect', () => {
    console.log('Connected to Redis server');
});
```

### **5. Define Routes for Redis Operations**

#### **Store a Key-Value Pair**
```
app.post('/store', (req, res) => {
    const { key, value } = req.body;

    if (!key || !value) {
        return res.status(400).send({ error: 'Key and value are required' });
    }

    redisClient.set(key, value, (err, reply) => {
        if (err) {
            return res.status(500).send({ error: 'Failed to store data' });
        }
        res.send({ message: `Stored key-value pair: ${key} -> ${value}` });
    });
});
```
Retrieve a Value by Key
```
app.get('/retrieve/:key', (req, res) => {
    const { key } = req.params;

    redisClient.get(key, (err, value) => {
        if (err) {
            return res.status(500).send({ error: 'Failed to retrieve data' });
        }

        if (value === null) {
            return res.status(404).send({ error: 'Key not found' });
        }

        res.send({ key, value });
    });
});
```
Delete a Key
```
app.delete('/delete/:key', (req, res) => {
    const { key } = req.params;

    redisClient.del(key, (err, reply) => {
        if (err) {
            return res.status(500).send({ error: 'Failed to delete key' });
        }

        if (reply === 0) {
            return res.status(404).send({ error: 'Key not found' });
        }

        res.send({ message: `Deleted key: ${key}` });
    });
});
```
Increment a Counter
```
app.post('/increment/:key', (req, res) => {
    const { key } = req.params;

    redisClient.incr(key, (err, newValue) => {
        if (err) {
            return res.status(500).send({ error: 'Failed to increment counter' });
        }
        res.send({ message: `Incremented ${key}`, newValue });
    });
});
```
### **6. Start the Express Server**

Add the following code to start your Express server:
```
const PORT = 3000;

app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```
### **7. Example Test Requests**

Use a tool like Postman, curl, or your browser to test the endpoints:

#### **Store Data** (POST)

URL: `http://localhost:3000/store` Body (JSON):
```
{
    "key": "name",
    "value": "George"
}
```
#### **Retrieve Data** (GET)

URL: `http://localhost:3000/retrieve/name`

#### **Delete Data** (DELETE)

URL: `http://localhost:3000/delete/name`

#### **Increment Counter** (POST)

URL: `http://localhost:3000/increment/counter`



### **8. Complete Code**

Here’s the full example in one block:
```
const express = require('express');
const redis = require('redis');

const app = express();
app.use(express.json());

const redisClient = redis.createClient({
    host: '127.0.0.1',
    port: 6379,
});

redisClient.on('error', (err) => console.error('Redis error:', err));
redisClient.on('connect', () => console.log('Connected to Redis server'));

app.post('/store', (req, res) => {
    const { key, value } = req.body;

    if (!key || !value) {
        return res.status(400).send({ error: 'Key and value are required' });
    }

    redisClient.set(key, value, (err) => {
        if (err) return res.status(500).send({ error: 'Failed to store data' });
        res.send({ message: `Stored key-value pair: ${key} -> ${value}` });
    });
});

app.get('/retrieve/:key', (req, res) => {
    const { key } = req.params;

    redisClient.get(key, (err, value) => {
        if (err) return res.status(500).send({ error: 'Failed to retrieve data' });
        if (value === null) return res.status(404).send({ error: 'Key not found' });
        res.send({ key, value });
    });
});

app.delete('/delete/:key', (req, res) => {
    const { key } = req.params;

    redisClient.del(key, (err, reply) => {
        if (err) return res.status(500).send({ error: 'Failed to delete key' });
        if (reply === 0) return res.status(404).send({ error: 'Key not found' });
        res.send({ message: `Deleted key: ${key}` });
    });
});

app.post('/increment/:key', (req, res) => {
    const { key } = req.params;

    redisClient.incr(key, (err, newValue) => {
        if (err) return res.status(500).send({ error: 'Failed to increment counter' });
        res.send({ message: `Incremented ${key}`, newValue });
    });
});

const PORT = 3000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
```
### **Key Notes**

-   **Redis Client**: Ensure Redis is running locally or provide the correct host and port for a remote server.
-   **Error Handling**: Always handle Redis errors to ensure stability.
-   **Scaling**: Use connection pooling or clustering for large-scale applications.

This basic Express app demonstrates how to interact with Redis for common operations like storing, retrieving, deleting, and updating data. You can extend this to build more complex functionalities like caching or job queues.
## -   How to the build a basic Express app interacting with a Redis server and queue

To build a basic Express app that interacts with a Redis server and uses a queue, we can leverage a library like **Kue** or **Bull** for queuing. Here’s a guide using **Bull**, which is popular for its simplicity and reliability.



### **1. Prerequisites**

Ensure you have:

-   **Node.js** and **npm/yarn** installed.
-   **Redis** running locally or accessible remotely.



### **2. Install Required Packages**

Run the following commands to install the necessary dependencies:
```
npm install express redis bull
```
### **3. Initialize the Project**

If not already done, initialize your Node.js project:
```
npm init -y
```
### **4. Create the Express App**

Create a file named `app.js`.



#### **Setup Basic Express App and Redis Client**

```
const express = require('express');
const redis = require('redis');
const { Queue } = require('bull');

const app = express();
app.use(express.json());

// Redis client for basic operations
const redisClient = redis.createClient({
    host: '127.0.0.1',
    port: 6379,
});

// Bull queue setup
const jobQueue = new Queue('jobs', {
    redis: {
        host: '127.0.0.1',
        port: 6379,
    },
});

// Redis connection event listeners
redisClient.on('error', (err) => console.error('Redis error:', err));
redisClient.on('connect', () => console.log('Connected to Redis server'));
```
### **5. Define Routes for Queue Operations**

#### **Add a Job to the Queue**

When a job is added to the queue, we pass some data (e.g., a task to process later).
```
app.post('/add-job', async (req, res) => {
    const { task, priority } = req.body;

    if (!task) {
        return res.status(400).send({ error: 'Task is required' });
    }

    try {
        const job = await jobQueue.add(
            { task },
            { priority: priority || 1 } // Default priority is 1
        );
        res.status(201).send({ message: 'Job added to queue', jobId: job.id });
    } catch (err) {
        res.status(500).send({ error: 'Failed to add job to queue' });
    }
});
```
#### **Process Jobs in the Queue**

This worker listens for jobs in the queue and processes them asynchronously.
```
jobQueue.process(async (job) => {
    console.log(`Processing job ID: ${job.id} with data:`, job.data);
    // Simulate job processing
    await new Promise((resolve) => setTimeout(resolve, 2000));
    console.log(`Job ID: ${job.id} completed`);
});
```
#### **Get Job Status**

Check the status of a specific job by its ID.
```
app.get('/job-status/:id', async (req, res) => {
    const { id } = req.params;

    try {
        const job = await jobQueue.getJob(id);

        if (!job) {
            return res.status(404).send({ error: 'Job not found' });
        }

        const state = await job.getState();
        res.send({
            jobId: id,
            state,
            data: job.data,
        });
    } catch (err) {
        res.status(500).send({ error: 'Failed to get job status' });
    }
});
```
#### **Clear Completed Jobs**

Remove all completed jobs from the queue.
```
app.delete('/clear-completed', async (req, res) => {
    try {
        await jobQueue.clean(1000, 'completed'); // Clean jobs completed > 1 sec ago
        res.send({ message: 'Completed jobs cleared' });
    } catch (err) {
        res.status(500).send({ error: 'Failed to clear completed jobs' });
    }
});
```
### **6. Start the Server**

Add the following code to start the server:
```
const PORT = 3000;

app.listen(PORT, () => {
    console.log(`Server running at http://localhost:${PORT}`);
});
```
### **7. Full Code Example**

Here’s the full application:
```
const express = require('express');
const redis = require('redis');
const { Queue } = require('bull');

const app = express();
app.use(express.json());

const redisClient = redis.createClient({
    host: '127.0.0.1',
    port: 6379,
});

const jobQueue = new Queue('jobs', {
    redis: {
        host: '127.0.0.1',
        port: 6379,
    },
});

redisClient.on('error', (err) => console.error('Redis error:', err));
redisClient.on('connect', () => console.log('Connected to Redis server'));

app.post('/add-job', async (req, res) => {
    const { task, priority } = req.body;

    if (!task) {
        return res.status(400).send({ error: 'Task is required' });
    }

    try {
        const job = await jobQueue.add(
            { task },
            { priority: priority || 1 }
        );
        res.status(201).send({ message: 'Job added to queue', jobId: job.id });
    } catch (err) {
        res.status(500).send({ error: 'Failed to add job to queue' });
    }
});

jobQueue.process(async (job) => {
    console.log(`Processing job ID: ${job.id} with data:`, job.data);
    await new Promise((resolve) => setTimeout(resolve, 2000));
    console.log(`Job ID: ${job.id} completed`);
});

app.get('/job-status/:id', async (req, res) => {
    const { id } = req.params;

    try {
        const job = await jobQueue.getJob(id);

        if (!job) {
            return res.status(404).send({ error: 'Job not found' });
        }

        const state = await job.getState();
        res.send({
            jobId: id,
            state,
            data: job.data,
        });
    } catch (err) {
        res.status(500).send({ error: 'Failed to get job status' });
    }
});

app.delete('/clear-completed', async (req, res) => {
    try {
        await jobQueue.clean(1000, 'completed');
        res.send({ message: 'Completed jobs cleared' });
    } catch (err) {
        res.status(500).send({ error: 'Failed to clear completed jobs' });
    }
});

const PORT = 3000;

app.listen(PORT, () => {
    console.log(`Server running at http://localhost:${PORT}`);
});
```
### **8. Example Usage**

Use tools like **Postman**, **curl**, or a browser to test:

#### **Add a Job**

**POST**: `http://localhost:3000/add-job`  
Body:
```
{
    "task": "Send email to user",
    "priority": 2
}
```
#### **Get Job Status**

**GET**: `http://localhost:3000/job-status/:id`  
Replace `:id` with the job ID returned in the previous step.

#### **Clear Completed Jobs**

**DELETE**: `http://localhost:3000/clear-completed`



### **Key Concepts**

1.  **Queue Management**:
    
    -   Jobs are added asynchronously.
    -   They are processed one at a time or concurrently, depending on your configuration.
2.  **Redis for Queues**:
    
    -   Redis serves as a fast in-memory store for the queue's data.
3.  **Monitoring and Scaling**:
    
    -   Use tools like **Bull Dashboard** for monitoring queues.
    -   Configure concurrency and retry logic for production systems.

This basic app demonstrates how to build an Express app with Redis and Bull for queuing tasks. It’s scalable and serves as a foundation for job processing workflows.