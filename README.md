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
