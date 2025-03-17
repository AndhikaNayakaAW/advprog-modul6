# Module 6 - Concurrency
**Andhika Nayaka Arya Wibowo (2306174135)**

## Commit 1: Reflection Notes

To process incoming HTTP requests from a browser, I utilized `BufReader` to wrap the `TcpStream`, enabling efficient line-by-line reading via `.lines()`. Each line corresponds to a specific part of the HTTP request, such as the request method, path, and headers.

- The `.map(|line| line.unwrap())` function was applied to transform `Result<String>` into `String`, simplifying data handling.
- The `.take_while(|line| !line.is_empty())` method ensured reading stopped at the first empty line, marking the separation between headers and the request body.
- By gathering the lines into a `Vec<String>`, I was able to observe how a browser structures and sends requests. This provided deeper insight into HTTP communication at a lower level.  
