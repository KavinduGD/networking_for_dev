# UDP - User Datagram Protocol

![UDP Packet Structure](./images/udp.png)

## Address multiple applications on a single host using ports

### Simple protocol for sending and receiving datagrams

### 8 bytes header

### use-cases

- **DNS**
- **Video Streaming**
- **VPN**

### multiplexing and demultiplexing

<img src="./images/multiplexing and demultiplexing.png" width=600>

### Features

- **Connectionless → no handshake; just sends packets (like a postcard 📬).**
- **Unreliable → no guarantee of delivery or order.**
- **No retransmission → if a packet is lost, it’s gone.**
- **Faster than TCP because less overhead.**
