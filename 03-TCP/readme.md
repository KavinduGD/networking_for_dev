# TCP

## TCP and Certification

1. Client send SYN to server
2. Server responds with SYN-ACK (ACK is for SYN send by client)
3. Client responds with ACK (ACK is for SYN-ACK send by server)

After that Certification shared between client and server

1. Client send Hello message to server ( It contains the supported cipher suites, TLS version)
2. Server responds with Hello message ( It contains the selected cipher suite, TLS version)
3. Server sends Certificate message ( It contains the server's certificate)
4. Client verifies the server's certificate ( It checks if the certificate is valid and trusted)
5. Client create a session key and encrypt it with the server's public key ( It uses the server's public key to encrypt the session key)
6. Client sends the encrypted session key to server
7. Server decrypts the session key with its private key ( It uses its private key to decrypt the session key)
8. Both client and server use the session key to encrypt and decrypt the data they exchange

🛑 Why we don't use asymmetric encryption for all communication?

- Because asymmetric encryption is computationally expensive and slower than symmetric encryption. It is more efficient to use asymmetric encryption for the initial key exchange and then switch to symmetric encryption for the actual data transfer.

<img src="./images/tls_and certificate.png" width="900px">

## TCP vs UDP

### TCP (Transmission Control Protocol)

- Connection-oriented protocol
- Reliable data transfer ( guarantees that data is delivered in order and without errors)
- Uses flow control and congestion control mechanisms
- Suitable for applications that require reliable communication (e.g., web browsing, email, file transfer)
- Slower than UDP due to the overhead of establishing a connection and ensuring reliability

### UDP (User Datagram Protocol)

- Connectionless protocol
- Unreliable data transfer ( does not guarantee that data is delivered in order or without errors)
- No flow control or congestion control mechanisms
- Suitable for applications that require fast communication and can tolerate some data loss (e.g., video streaming, online gaming, VoIP)
- Faster than TCP due to the lack of overhead for establishing a connection and ensuring reliability

