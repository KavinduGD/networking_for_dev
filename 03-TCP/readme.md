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
