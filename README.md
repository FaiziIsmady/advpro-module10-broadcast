# Advanced Programming Tutorial 10

## Broadcast

### Original code, and how it run

**Server and Client**

![Image](https://github.com/user-attachments/assets/0a5480e7-2c4f-4737-ad22-f56b6126e931)

![Image](https://github.com/user-attachments/assets/8514088b-c9fd-4ed7-956d-d5d13ac5f2f7)

![Image](https://github.com/user-attachments/assets/8c0ddb06-26f8-4c12-905c-d0f9d0b26be8)

To run the project, I first started the server by executing cargo run --bin server, which listens on 127.0.0.1:2000. Then, I opened three separate terminals to run three clients using cargo run --bin client. Each client receives a welcome message on connection and can start sending text input from the terminal. When a message is sent from one client, the server prints the sender’s address and the message, and then broadcasts it to all connected clients, including the original sender. As shown in the screenshots, typing a message such as “Hello” in one client causes all clients to receive From server: Hello.

This behavior is achieved using Tokio's broadcast channel, which allows the server to rebroadcast any incoming message to all clients. The tokio::select! macro is used in both server and client to concurrently handle sending and receiving messages. Overall, this experiment shows how Rust’s async features and channel-based communication can be used to build a concurrent WebSocket-based chat system.
