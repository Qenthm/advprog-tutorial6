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

### Next Steps

In the next commit, I'll implement:
1. Proper HTTP response generation
2. Handling different routes
3. Serving actual content back to the client