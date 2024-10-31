# Caching

Caching is a technique for storing a subset of data temporarily to speed up data retrieval and reduce the workload on primary resources, like databases or APIs. Here’s a breakdown of some key points on caching:

1.  **Purpose**:
    
    -   **Performance Improvement**: Reduces latency by serving frequently accessed data faster.
    -   **Load Reduction**: Reduces the load on backend systems like databases or servers by avoiding repeated calculations or data fetches.
    -   **Cost Efficiency**: Saves resources and bandwidth, which can be crucial in distributed systems.
2.  **Types of Caches**:
    
    -   **Client-side Cache**: Often stored in the browser, like in localStorage or sessionStorage, for quick retrieval by client applications.
    -   **Server-side Cache**: Stored on the server, using services like Redis or Memcached, to serve data without re-running intensive queries.
    -   **CDN (Content Delivery Network)**: Caches static content, like images or stylesheets, closer to users geographically, improving load times for web applications.
3.  **Cache Expiration**:
    
    -   Caches can have **time-to-live (TTL)** settings that determine when they expire. This helps ensure data isn’t stale.
    -   Expiration strategies include:
        -   **Time-based expiration**: Data expires after a specific time.
        -   **Event-based expiration**: Caches are invalidated after specific events, like database updates.
4.  **Common Caching Algorithms**:
    
    -   **LRU (Least Recently Used)**: Removes the least recently accessed items first.
    -   **LFU (Least Frequently Used)**: Evicts the items accessed the fewest times.
    -   **FIFO (First In, First Out)**: Evicts the oldest item first.
5.  **Where It’s Used**:
    
    -   **Web and Mobile Apps**: To load frequently accessed resources quickly.
    -   **API Responses**: To reduce redundant data fetches from third-party services.
    -   **Database Query Results**: To improve database performance by storing frequently retrieved data temporarily.

Incorporating caching in your applications, especially as a fullstack developer, can make a noticeable difference in responsiveness and user experience, especially for apps with heavy data access needs.

Quick rundown of caching concepts to help with the learning goals:

1.  **What a Caching System Is**:
    
    -   A caching system is a method of storing data temporarily to speed up retrieval. It prevents the need to repeatedly fetch or compute the same data, which optimizes performance and reduces server load.
2.  **FIFO (First In, First Out)**:
    
    -   This cache strategy removes the oldest data first, meaning the first item added is the first one removed. Think of it like a queue—once the cache is full, new data kicks out the oldest item.
	#### Example
	```
	from collections import deque

	class FIFOCache:
	    def __init__(self, capacity):
	        self.cache = deque()  # Using deque to handle FIFO
	        self.data = {}
	        self.capacity = capacity

	    def put(self, key, value):
	        if key in self.data:
	            self.cache.remove(key)
	        elif len(self.cache) >= self.capacity:
	            oldest_key = self.cache.popleft()  # Remove the oldest item
	            del self.data[oldest_key]
	        self.cache.append(key)
	        self.data[key] = value

	    def get(self, key):
	        return self.data.get(key, -1)

	# Example usage
	cache = FIFOCache(2)
	cache.put("A", 1)
	cache.put("B", 2)
	cache.put("C", 3)  # "A" will be evicted
	print(cache.get("A"))  # Output: -1 (not found)
	print(cache.get("B"))  # Output: 2
	```

3.  **LIFO (Last In, First Out)**:
    
    -   Opposite of FIFO, in LIFO, the most recently added item is the first to be removed when the cache is full. It operates like a stack, where only the top item is removed first.
	#### Example
	```
	class LIFOCache:
	    def __init__(self, capacity):
	        self.cache = []
	        self.data = {}
	        self.capacity = capacity

	    def put(self, key, value):
	        if key in self.data:
	            self.cache.remove(key)
	        elif len(self.cache) >= self.capacity:
	            last_key = self.cache.pop()  # Remove the most recent item
	            del self.data[last_key]
	        self.cache.append(key)
	        self.data[key] = value

	    def get(self, key):
	        return self.data.get(key, -1)

	# Example usage
	cache = LIFOCache(2)
	cache.put("A", 1)
	cache.put("B", 2)
	cache.put("C", 3)  # "B" will be evicted
	print(cache.get("A"))  # Output: 1
	print(cache.get("B"))  # Output: -1

	```
4.  **LRU (Least Recently Used)**:
    
    -   LRU removes items that haven’t been accessed in a while, assuming that if data hasn’t been used recently, it’s less likely to be needed soon. This is useful when recently accessed data is expected to be reused.
	#### Example
	```
	from collections import OrderedDict

	class LRUCache:
	    def __init__(self, capacity):
	        self.cache = OrderedDict()  # OrderedDict maintains insertion order
	        self.capacity = capacity

	    def put(self, key, value):
	        if key in self.cache:
	            self.cache.move_to_end(key)  # Update recent access
	        self.cache[key] = value
	        if len(self.cache) > self.capacity:
	            self.cache.popitem(last=False)  # Remove the least recently used item

	    def get(self, key):
	        if key in self.cache:
	            self.cache.move_to_end(key)  # Update recent access
	            return self.cache[key]
	        return -1

	# Example usage
	cache = LRUCache(2)
	cache.put("A", 1)
	cache.put("B", 2)
	cache.get("A")    # Access "A"
	cache.put("C", 3)  # "B" will be evicted (least recently used)
	print(cache.get("B"))  # Output: -1
	print(cache.get("A"))  # Output: 1

	```
5.  **MRU (Most Recently Used)**:
    
    -   MRU evicts the most recently used items first. This approach is uncommon, as it’s typically used when older data is expected to be accessed more than newer data.
	#### Example
	```
	from collections import OrderedDict

	class MRUCache:
	    def __init__(self, capacity):
	        self.cache = OrderedDict()
	        self.capacity = capacity

	    def put(self, key, value):
	        if key in self.cache:
	            self.cache.move_to_end(key)
	        self.cache[key] = value
	        if len(self.cache) > self.capacity:
	            self.cache.popitem(last=True)  # Remove the most recently used item

	    def get(self, key):
	        if key in self.cache:
	            self.cache.move_to_end(key)
	            return self.cache[key]
	        return -1

	# Example usage
	cache = MRUCache(2)
	cache.put("A", 1)
	cache.put("B", 2)
	cache.get("A")    # Access "A"
	cache.put("C", 3)  # "A" will be evicted (most recently used)
	print(cache.get("A"))  # Output: -1
	print(cache.get("B"))  # Output: 2

	```
6.  **LFU (Least Frequently Used)**:
    
    -   LFU evicts items that have been accessed the least often. It’s ideal for scenarios where data frequently accessed is likely to be accessed again, optimizing for data that has proven usefulness.
	#### Example
	```
	from collections import defaultdict, OrderedDict

	class LFUCache:
	    def __init__(self, capacity):
	        self.capacity = capacity
	        self.cache = {}           # Stores key-value pairs
	        self.freq = defaultdict(OrderedDict)  # Maps frequency to items with that frequency
	        self.min_freq = 0

	    def _update_freq(self, key):
	        count = self.cache[key][1]
	        del self.freq[count][key]
	        if not self.freq[count]:
	            del self.freq[count]
	            if self.min_freq == count:
	                self.min_freq += 1
	        self.cache[key][1] += 1
	        self.freq[self.cache[key][1]][key] = None

	    def put(self, key, value):
	        if self.capacity == 0:
	            return
	        if key in self.cache:
	            self.cache[key][0] = value
	            self._update_freq(key)
	        else:
	            if len(self.cache) >= self.capacity:
	                evict_key, _ = self.freq[self.min_freq].popitem(last=False)
	                del self.cache[evict_key]
	            self.cache[key] = [value, 1]
	            self.freq[1][key] = None
	            self.min_freq = 1

	    def get(self, key):
	        if key not in self.cache:
	            return -1
	        self._update_freq(key)
	        return self.cache[key][0]

	# Example usage
	cache = LFUCache(2)
	cache.put("A", 1)
	cache.put("B", 2)
	cache.get("A")    # Increases frequency of "A"
	cache.put("C", 3)  # "B" will be evicted (least frequently used)
	print(cache.get("B"))  # Output: -1
	print(cache.get("A"))  # Output: 1

	```
7.  **Purpose of a Caching System**:
    
    -   Caching systems aim to improve access speed for frequently used data, reduce backend load, enhance the user experience, and save bandwidth and resources.
8.  **Limits of a Caching System**:
    
    -   **Storage capacity**: Caches have finite storage and may need a good replacement strategy (e.g., LRU, LFU).
    -   **Stale data**: Data in the cache may become outdated if not refreshed or invalidated.
    -   **Consistency issues**: If cache and main data sources are not synchronized, inconsistencies can occur.
    -   **Memory consumption**: Caches can consume significant memory, especially on systems with many cached items, requiring careful management to avoid overuse.