# Web Server in Rust

## Commit 1 Reflection Notes

In this commit, I implemented a basic HTTP request handler that listens on port 7878 and prints out incoming HTTP requests. This is the first step towards building a functional web server.

### What I Observed

When I ran the server with `cargo run` and accessed `127.0.0.1:7878` in my browser, the server received and printed out the HTTP request headers. Each request contained:

1. The request line: `GET / HTTP/1.1` - This indicates a GET request to the root path using HTTP version 1.1
2. Various HTTP headers including:
   - Host information
   - User-Agent details (Firefox browser on macOS)
   - Accept headers for content negotiation
   - Language preferences
   - Encoding capabilities
   - Connection information
   - Cookie data
   - Security-related fetch metadata

### Technical Insights

The server implementation:
1. Creates a TCP listener bound to `127.0.0.1:7878`
2. Accepts incoming connections in a loop
3. For each connection, reads the HTTP request line-by-line until an empty line (which marks the end of headers)
4. Collects these lines into a vector and prints them out

Currently, the server receives the requests correctly but doesn't send any response back to the client. This explains why the browser continues to make repeated requests - it's waiting for a response that never comes.

## Commit 2 Reflection Notes

In this commit, I upgraded the server to return actual HTML content to the browser, creating a complete request-response cycle.

![Commit 2 screen capture](/assets/images/commit2.png)

### Improvements Made

1. **HTML Response Generation**: The server now constructs a proper HTTP response with:
   - A status line `HTTP/1.1 200 OK`
   - Content-Length header with the appropriate length
   - The HTML content read from a local file (hello.html)

2. **Complete HTTP Flow**: Instead of just receiving and printing requests, the server now:
   - Reads the incoming HTTP request
   - Processes it (currently in a basic way)
   - Generates an appropriate response
   - Sends the response back to the client

### Technical Details

The key addition is the response formatting and sending:
```rust
let status_line = "HTTP/1.1 200 OK";
let contents = fs::read_to_string("hello.html").unwrap();
let length = contents.len();

let response = format!(
    "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
);

stream.write_all(response.as_bytes()).unwrap();
```

This code:
1. Creates the HTTP status line
2. Reads the HTML content from a file
3. Calculates the content length
4. Formats these components into a valid HTTP response with proper headers
5. Writes the response back to the TCP stream

### Observations

When accessing the server now, the browser successfully receives and renders the HTML content. This is a significant improvement from the previous version where the browser would just wait indefinitely for a response.

## Commit 3 Reflection Notes

In this commit, I enhanced the server to validate requests and provide different responses based on the requested path.

![Commit 3 screen capture](/assets/images/commit3.png)

### Improvements Made

1. **Request Validation**: The server now checks if the request is for the root path (`/`):
   - If the request is `GET / HTTP/1.1`, it returns the main HTML page
   - For any other request, it returns a 404 error page

2. **Error Handling**: Added proper error responses with:
   - HTTP status code 404 for "not found" resources
   - Custom error page (404.html) with a user-friendly message

3. **Code Refactoring**: Reduced code duplication by:
   - Extracting common logic for both success and error cases
   - Using a tuple to determine the status line and filename based on the request
   - Following the DRY (Don't Repeat Yourself) principle

### Technical Details

The key improvement is the conditional response generation:
```rust
let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
    ("HTTP/1.1 200 OK", "hello.html")
} else {
    ("HTTP/1.1 404 NOT FOUND", "404.html")
};

let contents = fs::read_to_string(filename).unwrap();
let length = contents.len();

let response =
    format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

stream.write_all(response.as_bytes()).unwrap();
```

This code:
1. Determines the appropriate status line and filename based on the request
2. Uses a more concise pattern with destructuring to assign values
3. Removes duplicate code by handling file reading and response generation once

### Observations

When accessing the server now:
- Navigating to `127.0.0.1:7878` shows the main page (hello.html)
- Accessing any other path like `127.0.0.1:7878/something` shows the 404 error page
- The browser correctly displays different content based on the request path

This implementation follows proper HTTP protocol by returning appropriate status codes and content based on the requested resources.