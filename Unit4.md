# UNIT 4 - Connection Oriented Data Transfer

## Principle behind Connection Oriented Data Transfer

Connection oriented means that the connection will get established before sending of data. This is done to ensure reliability, in order, error free communication between two devices.

This is achieved by:

### Step 1: Connection Establishment

Before data transfer begins, connection must be established between sender and receiver.

- Done by process called **handshaking**
- In TCP, 3-way handshake is done:
  1. **SYN**
  2. **SYN+ACK**
  3. **ACK**
- These steps ensure processes are ready for communication

### Step 2: Reliable Data Transfer

One of the main goals of connection oriented is to ensure data is delivered accurately and completely.

- Data is broken into small segments
- Each segment is numbered as sequence number and receives ACK after receiving it
- If a packet is lost or not acknowledged within certain time, it is retransmitted
- This guarantees that receiver gets data without loss

### Step 3: Flow Control

Prevents the sender from sending too much data at once.

- The receiver may have limited buffer space or slower processing speed
- TCP uses sliding window protocol where receiver tells the sender how much data is to be sent
- Prevents buffer overflow

### Step 4: Error Control

Ensures data is error free.

- Uses checksum to detect errors
- Retransmits corrupted or lost data

### Step 5: Ordered Delivery

- Packets are delivered in correct order
- Sequence number helps in reordering if packets are out of order

### Step 6: Connection Termination

- Connection is properly closed after data transfer
- Ensures all data has been sent correctly before closing

## Principles of Connection Oriented Communication

```
┌─────────────────────────┐
│ Connection Establishment│
├─────────────────────────┤
│ Reliable Data Transfer  │
├─────────────────────────┤
│ Flow Control            │
├─────────────────────────┤
│ Error Control           │
├─────────────────────────┤
│ Ordered Delivery        │
├─────────────────────────┤
│ Connection Termination  │
└─────────────────────────┘
```

## Flow Control

It is a speed matching mechanism where the receiver should inform the sender at what speed the packets are being received by receiver. Later it is responsible for sender to send data at that speed.

- Flow control coordinates the amount of data that can be sent before receiving acknowledgment
- It is a set of procedures that tells how data is being sent and how much data it can transmit before it waits for ACK from receiver
- Receiver has limited speed at which it can process incoming data and limited amount of memory in which it can store incoming data
- Receiver must inform the sender before limits are reached and request sender to send fewer frames or stop temporarily

### Protocols Involved

**Noiseless Channels:**

- Stop and Wait (simplest)

**Noisy Channels:**

- Stop and Wait ARQ
- Go Back N ARQ
- Selective Repeat ARQ

## Stop and Wait Protocol

Stop and Wait protocol is data link protocol for transmission of data over noiseless channels. It provides unidirectional data transmission with flow control facilities but not error control. After sending one frame, the sender waits for an acknowledgment before transmitting the next frame.

### Primitives

**Sender Rules:**

1. Send one packet at a time
2. Send the next packet after receiving ACK for the previous

**Receiver Rules:**

1. Receive and consume data packet
2. After consuming, ACK needs to be sent

### Working Diagram

```
SENDER                           RECEIVER
  │                                │
  │──────── Frame 1 ──────────────→│
  │                                │ Process Frame 1
  │←──────── ACK 1 ────────────────│
  │                                │
  │──────── Frame 2 ──────────────→│
  │                                │ Process Frame 2
  │←──────── ACK 2 ────────────────│
  │                                │
  │──────── Frame 3 ──────────────→│
```

### Problems

1. **Due to lost data:**

   - Sender waits for ACK for an infinite amount of time
   - Receiver waits for data for infinite time

2. **Due to lost ACK:**

   - Sender waits for infinite amount of time for ACK

3. **Due to delayed ACK/Data:**
   - After timeout on sender side, a delayed ACK might be wrongly considered as ACK of some other data packet

## Stop and Wait ARQ (Automatic Repeat Request) Protocol

After sending one frame, the sender waits for an acknowledgment before transmitting the next frame. If the acknowledgment does not arrive after a certain period of time, the sender times out and retransmits the original frame.

**Stop and Wait ARQ = Stop and Wait + Timeout Timer + Sequence Number**

### Scenarios Handled

```
SCENARIO 1: Normal Operation
SENDER                           RECEIVER
  │──────── Frame 0 ──────────────→│
  │←──────── ACK 0 ────────────────│
  │──────── Frame 1 ──────────────→│
  │←──────── ACK 1 ────────────────│

SCENARIO 2: Lost Frame
SENDER                           RECEIVER
  │──────── Frame 0 ──────────X    │ (Frame Lost)
  │        (Timeout)               │
  │──────── Frame 0 ──────────────→│ (Retransmit)
  │←──────── ACK 0 ────────────────│

SCENARIO 3: Lost ACK
SENDER                           RECEIVER
  │──────── Frame 0 ──────────────→│
  │      X←──────── ACK 0 ─────────│ (ACK Lost)
  │        (Timeout)               │
  │──────── Frame 0 ──────────────→│ (Retransmit)
  │←──────── ACK 0 ────────────────│
```

### Drawbacks

- One frame at a time
- Poor utilization of bandwidth
- Poor performance

## Sliding Window Protocol

- Send multiple frames at a time
- Number of frames to be sent is based on window size
- Each frame has a sequence number

### Working Diagram

```
SENDER WINDOW (Size = 4)
┌───┬───┬───┬───┬───┬───┬───┬───┐
│ 0 │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │
└───┴───┴───┴───┴───┴───┴───┴───┘
  ↑               ↑
  │               │
  Window Start    Window End

Can send frames 0, 1, 2, 3 simultaneously
```

## Go Back N ARQ

**N** = Sender window size

- Uses the concept of protocol pipelining (sender can send multiple frames before receiving acknowledgment for the first frame)
- Finite number of frames and the frames are numbered in sequential manner
- Number of frames that can be sent depends on window size
- If acknowledgment is not received within a time period, then all frames in current window are retransmitted

### Window Size Calculation

- Window size = 4 → 2² (power of 2 defines how many bits)
- Then sequence numbers will be: 0, 1, 2, 3 = 00, 01, 10, 11

### Working Diagram

```
SENDER                           RECEIVER
  │──────── Frame 0 ──────────────→│
  │──────── Frame 1 ──────────────→│
  │──────── Frame 2 ──────────X    │ (Frame 2 Lost)
  │──────── Frame 3 ──────────────→│
  │                                │
  │←──────── ACK 0 ────────────────│
  │←──────── ACK 1 ────────────────│
  │      (Timeout for Frame 2)     │
  │                                │
  │──────── Frame 2 ──────────────→│ (Retransmit from Frame 2)
  │──────── Frame 3 ──────────────→│ (Retransmit Frame 3)
  │──────── Frame 4 ──────────────→│ (Continue...)
```

## Selective Repeat ARQ

In selective repeat, only the erroneous or lost frames are retransmitted while correct frames are received and buffered. The receiver, while keeping track of sequence numbers, buffers the frames in memory and sends NACK for only frames which are missing or damaged.

### Working Diagram

```
SENDER                           RECEIVER
  │──────── Frame 0 ──────────────→│ ✓ (Buffered)
  │──────── Frame 1 ──────────────→│ ✓ (Buffered)
  │──────── Frame 2 ──────────X    │ ✗ (Lost)
  │──────── Frame 3 ──────────────→│ ✓ (Buffered)
  │──────── Frame 4 ──────────────→│ ✓ (Buffered)
  │                                │
  │←──────── NACK 2 ───────────────│ (Request Frame 2 only)
  │                                │
  │──────── Frame 2 ──────────────→│ ✓ (Delivered in order)
  │←──────── ACK 0-4 ──────────────│ (All frames delivered)
```

## Sequence Number / Acknowledgment Number

In Computer Networks, especially in transport layer TCP, data is sent between sender and receiver in small chunks called segments. To make sure data transmission is done in correct manner, TCP uses two important concepts:

### Sequence Number

A sequence number is a number assigned to each byte of data sent by the sender. It helps the receiver to know the order of received data and detect any missing or duplicate data.

**Purpose:**

- To keep track of order of data
- To arrange out of order segments
- To detect lost or duplicate data segments

**Working:**

- Suppose sender wants to send a large message (like a video file)
- TCP breaks it into smaller segments
- Each segment is given a sequence number based on byte number
- Like the first segment might have a sequence number like 101, meaning it starts at byte 101

### Acknowledgment Number

An ACK is a number sent by the receiver to the sender to confirm that it has received data.

**Purpose:**

- To tell the sender "I received data up to this sequence number"
- Helps in error and flow control

### Example

```
SENDER                           RECEIVER
  │                                │
  │── Seq=100, Data(50 bytes) ────→│
  │                                │ Received bytes 100-149
  │←────── ACK=150 ────────────────│ "Send next starting from 150"
  │                                │
  │── Seq=150, Data(30 bytes) ────→│
  │                                │ Received bytes 150-179
  │←────── ACK=180 ────────────────│ "Send next starting from 180"
```

### Importance

Both Sequence and Acknowledgment numbers are important for:

- **Reliable delivery**
- **Ordered delivery**
- **Error detection**
- **Flow control**
- **Congestion control**
