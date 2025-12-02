[**GitHub**](https://github.com/AGWA/git-crypt)
# Installation
**git-crypt** has an APT package in the Ubuntu universe repo.
This allows system-level installation using the typical command, or a user installation using [**this approach**](apt-local-install.md).
# Requirements
- Git repo
- [**GPG key**](encryption-key-pairs.md#^961aa3)
# Usage
## Initialization
- Run the following command inside the target git repo - `git-crypt init`.
- This will create the main symmetric AES key that will be used to encrypt and decrypt the data in the repo.
- This created key is actually stored as `.git/git-crypt/keys/default` file - which is the above AES key encrypted by the internal default policy of **git-crypt** (uses system entropy).
- The raw AES key is sufficient to unlock the repo using `git-crypt unlock <raw-aes-key>`. If this key is compromised, your data is not secure anymore.
> The generated AES key is never stored raw anywhere, but it can obtained using `git-crypt export-key <key-file>`.
- `git-crypt status` can be used to get the list of files in the repo that are encrypted.
## Encryption | Locking
- With an existing GPG key pair, run the following command - `git-crypt add-gpg-user <GPG-ID>`. This uses the **public half** of the key.
    - Here, `GPG-ID` can be any metadata of the GPG key that can uniquely identify it, i.e. email or fingerprint.
- This command uses the specified GPG key to encrypt AES key from above, and store it under `.git-crypt` folder at project root (which it creates if needed).
- **The encrypted AES key under `.git-crypt` is committed into the repo immediately.**
- Now, committing and pushing to remote will automatically encrypt the target contents prior.
    Check-out automatically decrypts the contents on devices with an added GPG key or the raw AES key.
- Run this command `git-crypt lock` to verify if the encryption has been setup correctly. Now, all target contents must be encrypted on local too.
### File List for Locking
- **git-crypt** expects a `.gitattributes` file at project root specifying which files and folders need to be encrypted and decrypted.
- Below format is expected in the attributes file for them to be encrypted or ignored
```git-attributes
# encryption
target_file filter=git-crypt diff=git-crypt
target_dir/** filter=git-crypt diff=git-crypt
# ignore
ignore_item !filter !diff
```
## Decryption | Unlocking
- After a fresh clone on a new device with supported GPG key, run `git-crypt unlock`.
- This will read all keys under `.git-crypt/keys` and calls `gpg --decrypt` on each one.
- If the new device does not have the private key corresponding to any of those keys, decryption fails for git-crypt.
    - So, any new collaborator must share the public half of their GPG key with the owner, who will add it to git-crypt as mentioned in above section.
- If decryption is successful, then git-crypt writes the final key into `.git/git-crypt/keys` folder (after creating it).
```
GPG-encrypted AES (.git-crypt) >> Raw AES (memory) >> Default-encrypted AES (.git/git-crypt)
```

> [!WARNING] Mismatched **git-crypt** versions
> When cloning the repository on a new device with a different **git-crypt** version, it is possible that `git-crypt unlock` gives this error even with GPG keys loaded -
> (this was observed between 0.7.0 and 0.8.0)
> ```
> git-crypt: This repository contains a malformed key file. It may be corrupted.
> ```
> When this happens, unlock the repository using direct decryption (below).
### Direct Decryption
Optionally, the repo can be decrypted directly using the raw AES key obtained from `git-crypt export-key`.
Run `git-crypt unlock <raw-aes>` to immediately decrypt the repo, without involving GPG at all. **But this method is much more prone to security breach**