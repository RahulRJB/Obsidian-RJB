
# SSH


DATE:  08-06-24


Tags: [[Notes/Linux|Linux]]

# References:



# Content:


- stands for Secure Shell. 
- Network protocol that allows secure communication between two computers over an internet connection. Unlike regular communication, which can be intercepted, SSH encrypts everything being transferred, making it much harder for hackers to steal information or tamper with data.

- Simplified breakdown of how SSH works:
	1. **Client Initiates:** The process starts on your local machine (client) where you initiate an SSH connection to a remote server. You'll need an SSH client program, which is readily available for most operating systems.
	2. **Connection and Authentication:** The client program establishes a connection with the server on a specific port (usually port 22). Then, it verifies your identity through an authentication process. This may involve using a **password** or a more secure method like **public key cryptography**.
	3. **Secure Tunnel:** Once authenticated, SSH creates a secure tunnel between the two machines. This tunnel encrypts all data flowing back and forth, ensuring only the authorized user can access the information.
	4. **Remote Access:** With the secure tunnel established, you can now send commands to the remote server as if you were sitting right in front of it. You can manage files, run programs, and configure settings on the server through the SSH client interface.


- **SSH Keys:** Offer an alternative to password-based authentication for SSH connections. Its a more secure and convenient way to log in to remote servers. Here's how SSH keys work:

- **The Key Pair:**
	- **Public Key:** This key is freely distributable. It's like a public lock that anyone can use to send a message. You can place it on the server you want to connect to.
	- **Private Key:** This key is **secret** and should be kept secure. It's like a private key that only you have and can unlock messages sent with the public key. You keep this on your local machine.

- **The Authentication Process:**
	1. **Initial Connection:** When you try to connect to the server using SSH with keys, the server sends a challenge message encrypted with your public key (which is stored on the server).
	2. **Decrypting the Challenge:** Your local SSH client uses your private key (which you keep secret) to decrypt the challenge message. This proves you have the corresponding private key to the public key stored on the server.
	3. **Sending Signed Response:** The SSH client then encrypts the decrypted challenge message with some additional data and sends it back to the server.
	4. **Verifying the Response:** The server uses the public key on its side to decrypt the response you sent. If the decryption matches the original challenge and additional data, it verifies your identity and grants access.





