# Network Protocols

## OSI Model

A conceptual framework used to understand and implement network communications. Each layer serves a specific purpose and communicates with layers directly above and below it.

Here are the OSI Model’s 7 layers, from Layer 1 (the lowest layer) to Layer 7 (the highest layer):

### 1. **Physical Layer**
   - **Function**: Handles the physical connection between devices, including the transmission and reception of raw binary data over a physical medium (e.g., cables, radio frequencies).
   - **Examples**: Ethernet cables, fiber optics, electrical signals, radio waves.

### 2. **Data Link Layer**
   - **Function**: Manages node-to-node data transfer and error detection/correction in the physical layer. It organizes data into frames for transmission.
   - **Examples**: Ethernet, Wi-Fi (IEEE 802.11), MAC addresses.

### 3. **Network Layer**
   - **Function**: Responsible for logical addressing, routing, and forwarding data to its destination across multiple networks. This layer determines the best path for data.
   - **Examples**: IP (Internet Protocol), ICMP (Internet Control Message Protocol), routers.

### 4. **Transport Layer**
   - **Function**: Provides end-to-end communication, reliability, and flow control. It ensures complete data transfer and may break data into segments for reliable delivery.
   - **Examples**: TCP (Transmission Control Protocol), UDP (User Datagram Protocol).

### 5. **Session Layer**
   - **Function**: Manages sessions (connections) between applications on different devices. It establishes, maintains, and terminates connections.
   - **Examples**: NetBIOS, PPTP (Point-to-Point Tunneling Protocol).

### 6. **Presentation Layer**
   - **Function**: Translates data between the application layer and the network, handling data encoding, encryption, and compression.
   - **Examples**: SSL/TLS (for encryption), JPEG, MPEG (for data format translation).

### 7. **Application Layer**
   - **Function**: Provides network services directly to user applications. It facilitates user interaction by providing application-specific functions like email, file transfer, and web browsing.
   - **Examples**: HTTP, FTP, SMTP, DNS, POP3.


## Application layer protocols:

Here's a brief explanation of common **Client-Server** and **Peer-to-Peer** protocols:

---

### **Client-Server Protocols**

1. **HTTP (Hypertext Transfer Protocol)**
   - **Purpose**: The foundation of data communication on the web, primarily used for transmitting hypermedia documents (like HTML).
   - **Function**: Operates over TCP (usually port 80 for HTTP and 443 for HTTPS) and allows clients (like browsers) to request resources from servers.
   - **Usage**: Web browsing, API requests, web applications.

2. **FTP (File Transfer Protocol)**
   - **Purpose**: Used to transfer files between a client and server on a network.
   - **Function**: Operates over TCP (ports 20 and 21), allowing users to upload and download files.
   - **Usage**: Website file management, large file transfers.
   - FTP (File Transfer Protocol) is not inherently secure.
    
    To make FTP more secure, there are encrypted versions:

    **FTPS (FTP Secure)**: Adds SSL/TLS encryption to standard FTP, similar to HTTPS, which encrypts both the login credentials and data during transmission.
    
    **SFTP (SSH File Transfer Protocol)**: A different protocol altogether, using SSH (Secure Shell) for secure file transfers with encryption, authentication, and data integrity checks.

3. **SMTP (Simple Mail Transfer Protocol)**
   - **Purpose**: Protocol for sending emails.
   - **Function**: Operates over TCP (port 25), enabling the sending and forwarding of emails between mail servers.
   - **Usage**: Email transmission from client to server, server-to-server.

4. **WebSockets**
   - **Purpose**: Enables full-duplex (two-way) communication between client and server in real-time.
   - **Function**: Operates over TCP (typically port 80 for ws and 443 for wss), allowing clients and servers to maintain an open connection.
   - **Usage**: Real-time applications like chat apps, online gaming, and live notifications.

5. **POP3 (Post Office Protocol v3)**
   - **Purpose**: Used for retrieving emails from a remote mail server.
   - **Function**: Operates over TCP (port 110), allowing a client to download emails from the server to local storage.
   - **Usage**: Email retrieval, especially for offline email access.

6. **IMAP (Internet Message Access Protocol)**

    - **Purpose**: Used for retrieving and managing emails stored on a remote mail server, allowing access from multiple devices.
    - **Function**: Operates over TCP (usually port 143, or 993 for secure connections) and keeps emails on the server, allowing real-time synchronization of actions (read, delete, organize) across devices.
    - **Usage**: Accessing emails from different devices (e.g., smartphone, computer) while keeping them stored on the server. Ideal for users needing multi-device access and synchronized mail management.

    **Comparison to POP3**: IMAP is preferred over POP3 for multi-device access and real-time sync, while POP3 is simpler but downloads emails locally, often deleting them from the server.
---

### **Peer-to-Peer Protocol**

1. **WebRTC (Web Real-Time Communication)**
   - **Purpose**: Enables direct, peer-to-peer communication between browsers for real-time audio, video, and data transfer.
   - **Function**: Uses multiple protocols, including ICE (Interactive Connectivity Establishment), STUN (Session Traversal Utilities for NAT), and DTLS (Datagram Transport Layer Security) for secure and low-latency communication.
   - **Usage**: Video calls, voice chats, file sharing between browsers without needing an intermediary server.


## Transport Layer Protocols: TCP/IP and UDP/IP

The **Transport Layer** of the OSI model manages end-to-end data transmission between devices, and the two primary protocols here are **TCP** (Transmission Control Protocol) and **UDP** (User Datagram Protocol). Both protocols operate over IP (Internet Protocol) but differ in how they handle data delivery, reliability, and connection management.

---

### **TCP/IP (Transmission Control Protocol over IP)**

- **Purpose**: Provides reliable, connection-oriented communication, ensuring that data packets arrive in sequence and without errors.
- **Packet Transmission**: TCP breaks data into packets and numbers them before sending. It verifies packet delivery and re-sends any lost packets.
- **Connection Management**: Establishes a connection through a "three-way handshake" (SYN, SYN-ACK, ACK) between the sender and receiver, maintaining the connection until all packets are confirmed.

    1. **SYN (Synchronize)**:
    - The client initiates a connection by sending a **SYN** packet to the server.
    - This packet includes an initial sequence number (ISN) that starts the connection and synchronizes the sequence of data packets.

    2. **SYN-ACK (Synchronize-Acknowledge)**:
    - The server receives the SYN packet, acknowledges it by sending back a **SYN-ACK** packet to the client.
    - The SYN-ACK packet includes the server’s own initial sequence number and an acknowledgment number, indicating it received the client’s SYN request.

    3. **ACK (Acknowledge)**:
    - The client receives the SYN-ACK and responds with an **ACK** packet, confirming it received the server’s SYN-ACK.
    - With this final acknowledgment, both the client and server are synchronized and the connection is established, allowing data transfer to begin.

### Summary
The SYN, SYN-ACK, ACK process ensures both the client and server are ready to communicate, providing a reliable connection setup before data transmission.
- **Key Features**:
  - **Reliability**: Retransmits lost packets, reorders out-of-sequence packets.
  - **Error Checking**: Checks each packet to ensure accurate delivery.
  - **Flow Control**: Adjusts data flow to prevent overwhelming the receiver.
- **Usage**: Ideal for applications needing reliable data transfer, like web browsing (HTTP/HTTPS), email (SMTP), and file transfers (FTP).

### **UDP/IP (User Datagram Protocol over IP)**

- **Purpose**: Provides fast, connectionless communication with no guarantee of packet delivery, order, or error correction.
- **Packet Transmission**: UDP sends packets called "datagrams" independently, without establishing a formal connection or checking for delivery.
- **Connection Management**: Connectionless—no handshake or formal connection setup, making it faster but less reliable.
- **Key Features**:
  - **Speed**: Low latency and minimal protocol overhead.
  - **No Retransmission**: No re-sending of lost packets; packets may arrive out of order or not at all.
- **Usage**: Suitable for applications where speed is prioritized over reliability, like live video/audio streaming, online gaming, and DNS queries.

---

### **Differences Between TCP and UDP**

| Feature          | **TCP/IP**                          | **UDP/IP**                          |
|------------------|-------------------------------------|-------------------------------------|
| **Connection**   | Connection-oriented (handshake)     | Connectionless                      |
| **Reliability**  | Reliable (guarantees delivery)      | Unreliable (no delivery guarantees) |
| **Error Checking** | Yes, with packet retransmission | No error checking/retransmission    |
| **Order**        | Maintains packet order             | No guarantee of order               |
| **Speed**        | Slower due to overhead             | Faster, minimal overhead            |
| **Usage**        | Web browsing, email, file transfer | Streaming, gaming, DNS, VoIP        |

In summary, **TCP** is preferred for reliable, ordered data transfer, while **UDP** is best for applications requiring low latency where occasional data loss is acceptable.