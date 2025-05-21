# Advanced Programming Tutorial 10

## Broadcast

### Original code, and how it run

**Server and Client**

![Image](https://github.com/user-attachments/assets/0a5480e7-2c4f-4737-ad22-f56b6126e931)

![Image](https://github.com/user-attachments/assets/8514088b-c9fd-4ed7-956d-d5d13ac5f2f7)

![Image](https://github.com/user-attachments/assets/8c0ddb06-26f8-4c12-905c-d0f9d0b26be8)

To run the project, I first started the server by executing cargo run --bin server, which listens on 127.0.0.1:2000. Then, I opened three separate terminals to run three clients using cargo run --bin client. Each client receives a welcome message on connection and can start sending text input from the terminal. When a message is sent from one client, the server prints the sender’s address and the message, and then broadcasts it to all connected clients, including the original sender. As shown in the screenshots, typing a message such as “Hello” in one client causes all clients to receive From server: Hello.

This behavior is achieved using Tokio's broadcast channel, which allows the server to rebroadcast any incoming message to all clients. The tokio::select! macro is used in both server and client to concurrently handle sending and receiving messages. Overall, this experiment shows how Rust’s async features and channel-based communication can be used to build a concurrent WebSocket-based chat system.

### Modifying Port

#### Server port 2000 while Client port 8080 (Mismatch)

**Server port 2000**
![Image](https://github.com/user-attachments/assets/2ce84b3e-4f49-417f-9c4a-2eb0b5790d55)

**Client port 8080**
![Image](https://github.com/user-attachments/assets/a5ccacfd-a222-4afb-9672-6c6bb6f50c4d)

In this test, I intentionally introduced a mismatch between the server and client ports to observe the behavior of the WebSocket connection. The server was still running on port 2000, while the client had already been configured to connect to port 8080. As a result, when the client attempted to connect, it failed because no WebSocket server was listening on port 8080. This triggered a ConnectionRefused error on the client side, clearly indicating that although the application itself compiled and ran, the connection could not be established. This highlights the importance of ensuring that both client and server agree on the same port when setting up WebSocket communication. Without matching ports, the two sides cannot establish a proper connection regardless of whether the rest of the logic is correct.

#### Server port 8080 while Client port 8080 (Match)

**Server port 8080**
![Image](https://github.com/user-attachments/assets/80c02c29-e573-4311-8082-91092ce05408)

**Client port 8080**
![Image](https://github.com/user-attachments/assets/5427f82e-13c7-4d58-8dcb-bc2434ba9e51)

In this scenario, both the server and the client were configured to use the same WebSocket port: 8080. As shown in the screenshots, the server successfully started listening on port 8080, and the client was able to establish a connection immediately. Upon connection, the client received the expected welcome message from the server. This successful interaction confirms that WebSocket communication works correctly only when both sides—the client and the server—are aligned on the same host and port. Unlike the previous test with mismatched ports (where the client attempted to connect to port 8080 while the server was still on 2000), having matching ports ensures the TCP handshake can occur, allowing the WebSocket protocol to negotiate and establish a two-way communication channel.

### Small changes, add IP and Port

**Server**

![Image](https://github.com/user-attachments/assets/cfe44e28-8535-46d4-9323-a193b9351858)

**Client**

![Image](https://github.com/user-attachments/assets/a58bafdc-833d-4b27-807a-61aad4b5f268)

![Image](https://github.com/user-attachments/assets/b204d300-8ac3-48da-aa08-09e6b7b893a4)

In this stage of the experiment, a personalization was added on the client side by modifying the display format to show "From Faizi's computer server: ...". This change was made in client.rs, where incoming messages are printed. At the same time, in server.rs, the line bcast_tx.send(format!("{addr} : {text}"))?; was added. This ensures that each message includes the sender’s IP address and port before being broadcast. Together, these changes allow clients to see clearly who sent each message, with both a human-readable prefix and technical connection details for identification.