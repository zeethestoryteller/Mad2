# Designing API


**Remote Procedure Call (RPC)** is a foundational client-server communication concept used for several decades.

* **Definition:** RPC allows a client program (e.g., Client A) to connect to a remote server (e.g., Server B) and execute a function or procedure located on that server.
* **Functionality:** It handles how the client connects to the server, passes arguments/parameters into the function, and receives the resulting response back from the server.
* **Evolution into Web APIs:** Modern web APIs are an evolution of RPC. Instead of traditional custom networking protocols, web APIs typically leverage HTTP as a simple, text-based communication layer to invoke these remote functions, pass parameters, and receive responses.
