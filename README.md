# Module 6 - Concurrency
**Andhika Nayaka Arya Wibowo (2306174135)**

## Commit 1: Reflection Notes

To process incoming HTTP requests from a browser, I utilized `BufReader` to wrap the `TcpStream`, enabling efficient line-by-line reading via `.lines()`. Each line corresponds to a specific part of the HTTP request, such as the request method, path, and headers.

- The `.map(|line| line.unwrap())` function was applied to transform `Result<String>` into `String`, simplifying data handling.
- The `.take_while(|line| !line.is_empty())` method ensured reading stopped at the first empty line, marking the separation between headers and the request body.
- By gathering the lines into a `Vec<String>`, I was able to observe how a browser structures and sends requests. This provided deeper insight into HTTP communication at a lower level.  

## Commit 2: Reflection Notes

![Commit 2 screen capture](/assets/images/commit2.png)

In this milestone, I learned how to serve an actual HTML file from a Rust web server. By modifying the `handle_connection` method, I was able to read the contents of `hello.html` and send it as part of the HTTP response. The `fs::read_to_string()` function made it easy to load the entire file into memory, allowing the server to return a proper web page instead of just plain text.

A key takeaway from this update is the importance of the `Content-Length` header in HTTP responses. Without it, some browsers might not render the page correctly. Additionally, I gained a better understanding of HTTP response structure, which consists of:

1. A **status line** (e.g., `HTTP/1.1 200 OK`)
2. **Response headers**, such as `Content-Length`
3. A **blank line** to separate headers from the body
4. The **actual HTML content**

By ensuring the response follows this format, the browser can correctly interpret and render the webpage.  

## Commit 3: Reflection Notes

![Commit 3 screen capture](/assets/images/commit3.png)

In this milestone, I implemented conditional response logic in the `handle_connection` method to serve different pages based on the requested path. The key idea was to inspect the request line from the browser and return an appropriate response:

- If the request is for `/` (`GET / HTTP/1.1`), the server responds with a `200 OK` status and serves `hello.html`.
- If the request is for any other path (e.g., `/bad`), the server returns a `404 NOT FOUND` status and serves a custom `404.html` file.

This simple request validation ensures that only valid URLs return content, mimicking real-world web server behavior.

## Commit 4: Reflection Notes

To simulate a performance bottleneck, I introduced a `/sleep` route in the web server. When accessed, this route causes the server to **pause for 10 seconds** using `thread::sleep()` before responding. This experiment highlights a critical limitation of single-threaded servers: **a slow request can block all other incoming requests**.

### Observations

- Accessing `/sleep` in one browser tab **froze the entire server**, preventing any other requests from being processed until the delay was over.
- Even when trying to access `/` in a separate tab, the request was blocked until `/sleep` finished executing.

### Key Takeaways

- **Single-threaded servers lack scalability**—a single slow request can impact all users.
- **Concurrency is crucial** in web server design to maintain responsiveness under load.
- **Multithreading or asynchronous processing** is necessary in real-world applications to ensure smooth handling of multiple requests.

This experiment deepened my understanding of web server performance and the role of concurrency in handling simultaneous connections efficiently.  

## Commit 5: Reflection Notes

In this milestone, I improved the server’s performance by implementing a **ThreadPool**, allowing it to handle multiple incoming connections concurrently. Instead of creating a new thread for every request, I initialized a fixed number of reusable worker threads using `ThreadPool::new(4)`. This approach significantly improves efficiency and scalability.

### How the ThreadPool Works

- A **fixed number of worker threads** (four in this case) are created at startup and remain active.
- Instead of spawning new threads for each request, tasks are **queued** and assigned to available worker threads.
- The `execute` method sends a closure (task) to the **message passing channel (mpsc)**, where it is received and executed by an idle worker thread.

### Key Takeaways

- **Avoiding excessive thread creation**: Instead of creating and destroying threads dynamically, which is costly, I reuse a fixed pool of threads.
- **Efficient concurrency**: The pool allows handling multiple requests in parallel, improving responsiveness under load.
- **Using `usize` for thread count**: This ensures only valid non-negative values are used, preventing invalid configurations.
- **Defining closures with `FnOnce()`**: Since each task is only executed once per connection, using `FnOnce()` ensures correct behavior without unnecessary overhead.

By implementing a **multithreaded server**, I have taken a crucial step toward building a more scalable and performant web server. 

## Commit 5: Reflection Notes

In this milestone, I improved the server’s performance by implementing a **ThreadPool**, allowing it to handle multiple incoming connections concurrently. Instead of creating a new thread for every request, I initialized a fixed number of reusable worker threads using `ThreadPool::new(4)`. This approach significantly improves efficiency and scalability.

### How the ThreadPool Works

- A **fixed number of worker threads** (four in this case) are created at startup and remain active.
- Instead of spawning new threads for each request, tasks are **queued** and assigned to available worker threads.
- The `execute` method sends a closure (task) to the **message passing channel (mpsc)**, where it is received and executed by an idle worker thread.

### Key Takeaways

- **Avoiding excessive thread creation**: Instead of creating and destroying threads dynamically—which is costly—I reuse a fixed pool of threads.
- **Efficient concurrency**: The pool allows handling multiple requests in parallel, improving responsiveness under load.
- **Using `usize` for thread count**: This ensures only valid non-negative values are used, preventing invalid configurations.
- **Defining closures with `FnOnce()`**: Since each task is executed only once per connection, using `FnOnce()` ensures correct behavior without unnecessary overhead.

By implementing a **multithreaded server**, I have taken a crucial step toward building a more scalable and performant web server.