# Computer Networks - Transport Layer Notes

## Multiplexing and Demultiplexing

## Multiplexing and Demultiplexing

```
MULTIPLEXING:
┌─────────────┐    ┌─────────────┐
│   App 1     │    │             │
│  (Port 80)  │────┤             │
├─────────────┤    │  Transport  │    ┌─────────────┐
│   App 2     │    │    Layer    │────│   Network   │
│ (Port 443)  │────┤             │    │             │
├─────────────┤    │             │    └─────────────┘
│   App 3     │    │             │
│ (Port 21)   │────┤             │
└─────────────┘    └─────────────┘

DEMULTIPLEXING:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Network   │    │             │    │   App 1     │
│             │────┤  Transport  │────│  (Port 80)  │
└─────────────┘    │    Layer    │    ├─────────────┤
                   │             │────│   App 2     │
                   │             │    │ (Port 443)  │
                   │             │    ├─────────────┤
                   │             │────│   App 3     │
                   └─────────────┘    │ (Port 21)   │
                                      └─────────────┘
```

### Multiplexing

Transport layer combines all data of multiple applications to send over network.

- Our computer has many apps, all want to send data
- Multiplexing combines all data into one stream for sending
- Each app is given unique port number
- The TCP/UDP add headers to it
- Contains sending app port and receiving app port

### Demultiplexing

Separates the incoming data and deliver to its correct port

- Transport layer reads the port from header and then sends the data to its correct application

## Transport Layer Protocols

Transportation layer has 2 protocols:

- **TCP**
- **UDP**

## TCP (Transmission Control Protocol)

When we send data through transport layer, TCP adds header that provides set of information to receiver.

### 1. Byte Streaming and Segmentation

- In TCP, data is sent as a continuous stream of bytes, not as fixed messages (called **byte streaming**)
- Allows app to send any amount of data and TCP will ensure smooth delivery which is reliable
- Since data can be very large, TCP uses **segmentation** to break the data into smaller parts called segments
- These segments are sent over network and each segment has a sequence number to keep order

### 2. Connection Oriented

- TCP is called connection oriented as it creates connection before sending data
- TCP ensures connection is stable and ready before transfer starts

#### How Connection is Made (3-Way Handshake)

#### How Connection is Made (3-Way Handshake)

```
CLIENT                           SERVER
  │                                │
  │────────── SYN ─────────────────→│  Step 1: Client requests connection
  │                                │
  │←───────── SYN-ACK ─────────────│  Step 2: Server acknowledges & requests
  │                                │
  │────────── ACK ─────────────────→│  Step 3: Client acknowledges
  │                                │
  │        CONNECTION ESTABLISHED   │
  │                                │
  │←─────── DATA TRANSFER ────────→│
```

1. **SYN (Synchronize)** - Sender says "I want to make connection"
2. **SYN-ACK (Synchronize + Acknowledge)** - Receiver replies "OK, I agree, let's connect"
3. **ACK (Acknowledge)** - Sender replies "Let's start connection"

**Main advantage of connection oriented is Reliability**

### 3. Full Duplex

- **A ↔ B** Both can send data to each other at same time
- It is mode of communication in which data can be sent & received simultaneously in both directions
- Allows two-way communication at same time

### 4. Piggy Backing

- **Sender → Receiver** (needs to send ACK) → **(ACK + Data) = Piggybacking**
- Means sending ACK and data together in same packet
- Instead of sending a separate message "I got your data", the device waits & adds ACK to its outgoing data

### 5. Error Control

Provides error control mechanisms

### 6. Flow Control

Whenever we send data to receiver, we should know its capacity so that buffer is not overwhelmed

### 7. Congestion Control

Method to control too much data being sent to receiver which slows down system

## TCP Header

## TCP Header

```
TCP Header Structure (20-60 bytes):
┌─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│  Source Port    │ Destination Port│    Sequence Number (32 bits)     │
│    (16 bits)    │    (16 bits)    │                                   │
├─────────────────┴─────────────────┼───────────────────────────────────┤
│         Acknowledgment Number      │  Data │     Reserved    │ Flags   │
│              (32 bits)             │Offset │    (6 bits)     │(6 bits) │
├────────────────┬───────────────────┼───────┼─────────────────┼─────────┤
│  Window Size   │    Checksum       │       Urgent Pointer    │Options  │
│   (16 bits)    │   (16 bits)       │        (16 bits)        │& Padding│
└────────────────┴───────────────────┴─────────────────────────┴─────────┘
```

TCP Header is added to every segment sent over network - it carries important info.

**Range:** 20B - 60B

### Header Fields:

- **Source Port (16 Bits)** - Port number of sender

  - Helps to identify which app is sending data on sender computer

- **Destination Port (16 Bits)** - Port number of receiver

  - Helps to deliver the data to correct app on receiver PC

- **Sequence Number (32 Bits)** - Shows which byte of data is being sent

  - Used to arrange the data in correct order at receiver end
  - Data → big broken into smaller parts - this number tells order

- **Acknowledgment Number (32 Bits)** - Tells which bytes the receiver is expecting next

  - If ACK 101, it means receiver got up to 100 bytes & now expecting 101

- **Data Offset/Header Length** - Tells about where data starts after header

  - Ex: 5-HLEN 5×4 = 20 Byte long header then data

- **Reserved** - These bits are not used right now

  - Kept for future features in TCP
  - Set to 0 for now

- **FLAGS** - Tiny bits used to control the connection

  - **URG** - Urgent data is present
  - **ACK** - Acknowledgment field is valid
  - **PSH** - Push the data immediately
  - **RST** - Reset the connection
  - **SYN** - Used to start a connection
  - **FIN** - Used to end connection

- **Window Size (16 Bits)** - Tells the sender how much data the receiver can handle at the moment

  - Used for flow control
  - Helps in not overloading the receiver

- **Checksum (16 Bits)** - Used to check the error in header & data

  - If there is any corruption during transfer, data is rejected

- **Urgent Pointer (16 Bits)** - Used when URG flag is set

  - Shows how much data is urgent & must be given priority

- **Optional/Padding** - Optional part
  - Used for extra settings

## TCP Data Transfer

TCP is connection oriented, so before sending, connection is established between sender and receiver.

### Connection Establishment (3-Way Handshake)

Before data transfer:

1. Sender sends **SYN**
2. Receiver replies **SYN+ACK**
3. Sender replies with **ACK**

Now connection is established.

### Data Transfer

- TCP breaks data into smaller parts called segments
- Each segment has sequence number
- Receiver gets a segment, replies with ACK
- This ensures data is received correctly and in order

### Piggybacking

When both sender and receiver are sending data, they combine their data with ACK to save time.

### Pure ACK

If receiver has no data to send, it still sends only ACK to confirm received segment.

### Retransmission (Reliability)

If sender does not receive ACK on time, it assumes data is lost and resends it.

### Connection Termination

After sending data, sender and receiver close connection:

1. Sender sends **FIN**
2. Receiver replies with **ACK**
3. Receiver sends **FIN**
4. Sender replies with **ACK**

## UDP (User Datagram Protocol)

Connection-less protocol (not reliable)

- Does not check whether the receiver gets data or not
- Faster but not reliable than TCP

### Features:

1. **Connection-less** - No handshake before data transfer
2. **No guarantee of delivery**
3. **No sequencing** - Data may arrive in any order
4. **No acknowledgment** - Receiver does not send ACK
5. **Lightweight** - Very small header (8 bits)
6. **Used when speed is more important than reliability**

### UDP Segment Structure

### UDP Segment Structure

```
UDP Header Structure (8 bytes):
┌─────────────────┬─────────────────┐
│  Source Port    │ Destination Port│
│   (16 bits)     │   (16 bits)     │
├─────────────────┼─────────────────┤
│     Length      │    Checksum     │
│   (16 bits)     │   (16 bits)     │
└─────────────────┴─────────────────┘
│              DATA                 │
│            (Variable)             │
└───────────────────────────────────┘
```

- **Source Port** - Port number of sender app
- **Destination Port** - Port number of receiver app
- **Length (2 bytes)** - Total length = header + data
- **Checksum** - Used to detect error
- **Data** - Rest is data

### Checksum in UDP

UDP uses a checksum to check for errors during transmission (works same as TCP).

## Advantages of UDP over TCP

### A. Query Response Protocol

Request protocols like DNS use UDP instead of TCP because:

1. **Faster Response** - UDP is fast because it doesn't need to setup connection

   - Ex: In DNS we just send a small request like "what is IP address of google.com"

2. **Low Overhead** - UDP has small headers (8 Bytes)

   - TCP has bigger header (20 Bytes or more)
   - For small messages like DNS queries, UDP uses less memory & CPU

3. **No Need of Reliability** - DNS can resend the request if no response comes

   - We don't need extra reliability features of TCP

4. **One Question One Answer** - DNS usually single request & response

### B. Speed

Where we need speed, we use UDP because of less overhead

- Examples: Online games, Voice over IP

### C. Broadcasting/Multicasting

- After every 30 seconds, the node shares its routing table with every node
- In TCP: If A wants to send info, has to establish connection before transfer
- UDP is connection-less so it can send data to many users at once
- Faster and has less overhead than TCP
- No need for acknowledgment which saves time & network load

### D. Continuous Streaming

Examples: Skype, Zoom

- UDP: Fast, no delay, real-time, low overhead

## Difference between TCP & UDP

| Feature            | TCP                                   | UDP                      |
| ------------------ | ------------------------------------- | ------------------------ |
| Connection         | Connection Oriented                   | Connection Less          |
| Reliability        | Reliable                              | Less Reliable            |
| Ordering           | Ordering is followed                  | Ordering is not followed |
| Error Control      | Error control is mandatory (checksum) | Error control Optional   |
| Speed              | Slow transmission                     | Fast Transmission        |
| Overhead           | More overhead                         | Low overhead             |
| Flow Control       | Yes                                   | No Flow control          |
| Congestion Control | Yes                                   | No congestion Control    |
| Handshake          | 3-way handshake                       | None                     |
| Examples           | HTTP, FTP                             | DNS, DHCP                |

## Difference between Connection-less and Connection Oriented

| Feature                | Connection Oriented                                   | Connection-less                                  |
| ---------------------- | ----------------------------------------------------- | ------------------------------------------------ |
| Connection Requirement | Requires a connection before data transfer            | Does not require connection before data transfer |
| Reliability            | Guarantees reliable delivery of data                  | No guarantee of data, less reliable              |
| Ordering               | Order is followed                                     | Order is not followed                            |
| Flow Control           | Supports flow control to manage flow of data transfer | Does not support flow control                    |
| Error Handling         | Detects and corrects errors                           | Error detection is optional                      |
| Overhead               | More overhead due to connection management            | Less overhead since no connection is established |
| Resource Usage         | Consumes more resources for connection                | Consumes less resources                          |
| Example                | TCP                                                   | UDP                                              |
