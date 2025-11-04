**Encryption** is the process of transforming plain-text data into cipher-text data, which can only be read by authorized parties. It is used to protect the confidentiality of data - both at-rest and in-transit.
```
plain ----encrypt---> cipher ----decrypt---> plain
```
There are 2 main kinds of encryption - **symmetric** (one key) and **asymmetric** (key-pair). Symmetric encryption uses the same key for encryption and decryption - fast and efficient, but less secure. If the key gets compromised, all data is at risk. Asymmetric encryption uses different keys for encryption and decryption - slow and computationally expensive, but more secure. Public keys are shared openly, and private keys are kept secret - as long as it is not compromised, the data is safe.
# GPG
^961aa3

**GPG** stands for **GNU Privacy Guard** - an asymmetric encryption method. Public key is used by sender to encrypt plain-text. Private key is used by recipient (you) to decrypt received cipher-text. Also used similarly for signature verification.
```bash
# Create a new GPG key
gpg --full-generate-key
# List GPG keys (public and private)
gpg --list-keys
gpg --list-secret-keys
# Export public key
gpg --armor --export KEYID_or_UID > public_key.asc
# Export private key
gpg --armor --export-secret-keys KEYID_or_UID > private_key.asc
# Edit existing GPG key
gpg --edit-key KEYID_or_UID
# Import GPG key (public or private) from a file
gpg --import key.asc
# Upload GPG key pair to Gdrive
```
`.asc` and `.gpg` contain the same crytographic data but in different formats. `.asc` (with `--armor`) is human-readable plain-text, and `.gpg` is machine-readable binary data. Although `.gpg` is faster to process than `.asc`, it is also less portable across systems.
# SSH
**SSH** stands for **Secure Shell** - a network protocol used for secure connection between two devices - remote login, remote command execution, remote file transfer, etc. A server is started on the remote device (typically port 22), and it presents its public key. The client (local host) verifies this using its private key, and then starts a secure connection (prevents man-in-the-middle attacks). After this, both sides switch to symmetric encryption using the session key for fast and efficient connection.
