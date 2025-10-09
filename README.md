# Multithreaded HTTP Server

This project delivers a minimal yet efficient HTTP/1.1 server written entirely in Python without any external dependencies. It’s capable of delivering static web content—like HTML pages, text documents, and images—from a styled resources/ folder, while also accepting JSON file uploads. A customizable pool of worker threads enables the server to handle several client requests at the same time. Additionally, it incorporates essential security mechanisms such as directory traversal safeguards, validation of host headers, and strict checks on content types.

---

## Build and Run Instructions

### Requirements

* Python 3.7+
* No external libraries are needed, as the project relies solely on Python’s standard library.

### Steps to Run

1. Clone or download the repository.
2. Ensure the `resources` directory is present in the project root. This directory contains themed resources (HTML pages, text documents, and iPhone 17-themed images such as `iphone17backview.png`, `iphone17features.png`, etc.).
3. Run the server:

   ```bash
   python server.py 
   # Defaults to host: 127.0.0.1, port: 8080, max_threads: 10
   ```

   or

   ```bash
   python server.py --host 127.0.0.1 --port 8080 --max_threads 10 
   # Specify the host, port, and max_threads
   ```
4. Open a browser and visit:

   ```
   http://127.0.0.1:8080
   ```

---

## Binary Transfer Implementation

* Files inside the `resources` directory are read in **binary mode (`rb`)** to ensure proper delivery of both text and media content.  
* `.html` files are served with the header `Content-Type: text/html; charset=utf-8` and sent directly to the client.  
* `.txt` files and supported image formats (`.png`, `.jpg`, `.jpeg`) are transmitted as raw binary data with the appropriate headers.

  * `Content-Type: application/octet-stream`
  * `Content-Disposition: attachment; filename="<filename>"`
* This ensures the client receives files exactly as stored, preserving integrity (no unintended encoding issues).

---

## Thread Pool Architecture

* The server uses a **ThreadPoolExecutor** for concurrency.
* When a client connects:

  1. If an idle worker thread is available, the incoming request is assigned to it instantly.  
  2. If all threads are currently occupied, the connection is placed in a queue until a thread becomes available.  
* Each worker thread is responsible for:
  
  * Parsing incoming HTTP requests (`GET` / `POST`)
  * Serving requested files or handling file uploads
  * Logging all server activity with timestamps and thread identifiers

* This architecture prevents resource exhaustion and ensures fairness between connections.

---

## Security Measures Implemented

1. **Path Traversal Protection**: Requests are sanitized with `safe_path()` to guarantee files are served exclusively from the `resources` directory.  
2. **MIME Enforcement**: Only the following file types are supported:  
   * `.html`, `.txt`, `.png`, `.jpg`, `.jpeg`  
   * Any other file type returns `415 Unsupported Media Type`.  
3. **Host Header Validation**: Confirms that requests match the configured host and port, mitigating Host Header attacks.  
4. **Restricted Uploads**: Only JSON (`Content-Type: application/json`) is accepted via POST; uploaded files are stored in `resources/uploads`.  
5. **Connection Control**: Enforces a maximum of 100 requests per connection and a keep-alive timeout of 30 seconds to prevent idle, long-lived connections.


---

## Limitations

* **Binary transfer buffering**: Files are loaded entirely into memory before being sent, which may lead to high memory usage for large files.  
* **Limited MIME support**: Only a restricted set of file types is currently handled.  
* **Single-directory limitation**: All content must be located within the `resources` directory; serving files from multiple directories is not supported.

---

This server offers a **compact, iPhone 17-themed demo** showcasing HTTP handling, file serving, multithreaded concurrency, and fundamental security features.

