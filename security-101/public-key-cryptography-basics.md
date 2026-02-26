# TryHackMe - Public Key Cryptography Basics

**Room:** Public Key Cryptography Basics  
**Difficulty:** Easy  
**Focus:** Asymmetric encryption, RSA, Diffie-Hellman, SSH, TLS certificates, PGP/GPG

---

## Overview

Public key cryptography (asymmetric encryption) is fundamental to modern secure communications. This room explores how asymmetric cryptography enables authentication, integrity verification, and secure key exchange across insecure networks.

**Learning Objectives:**
- Understand asymmetric encryption principles
- Learn RSA mathematics and implementation
- Master Diffie-Hellman key exchange
- Use SSH key authentication
- Understand digital signatures and certificates
- Apply PGP/GPG for encrypted communications

---

## Task 1: Introduction

### Security Requirements in Digital Communication

**Four pillars of secure communication:**

**1. Authentication**
- Verify you're communicating with the correct party
- Prevent impersonation attacks
- Confirm identity before trusting

**2. Authenticity**
- Verify information comes from claimed source
- Detect forged messages
- Trust the sender's identity

**3. Integrity**
- Ensure data hasn't been altered in transit
- Detect tampering attempts
- Guarantee message completeness

**4. Confidentiality**
- Prevent unauthorized eavesdropping
- Keep communications private
- Encrypt sensitive data

### Real-World Analogy

**In-person meeting:**
- **Authentication:** See and hear the person
- **Authenticity:** Know words come from them
- **Integrity:** No one can change their words
- **Confidentiality:** Private conversation away from others

**Digital communication challenges:**
- Cannot see who's on the other end
- Messages travel through multiple networks
- Anyone could intercept or modify data
- Need cryptographic solutions

### Role of Public Key Cryptography

**Symmetric encryption (private key):**
- Primarily protects confidentiality
- Same key for encryption/decryption
- Key distribution problem

**Asymmetric encryption (public key):**
- Enables authentication
- Provides authenticity verification
- Ensures integrity
- Solves key distribution

---

## Task 2: Common Use of Asymmetric Encryption

### Key Exchange Problem

**Challenge:** How to agree on a symmetric key without transmitting it where eavesdroppers can see?

**Solution:** Use asymmetric encryption for key exchange

### The Lock and Box Analogy

**Scenario:** Send secret instructions to friend

**Step 1:** Ask friend for a lock (only they have the key)

**Step 2:** Put instructions in indestructible box

**Step 3:** Lock box with friend's lock

**Step 4:** Send locked box (anyone can see it, but can't open it)

**Step 5:** Friend unlocks with their private key

**Step 6:** Use secret code (symmetric key) for future communication

**Mapping to cryptography:**
- Secret code = Symmetric encryption key
- Lock = Server's public key
- Key = Server's private key
- Box = Encrypted message

### Real-World Implementation

**Hybrid approach (best of both worlds):**

1. **Use asymmetric encryption once:**
   - Exchange symmetric encryption key
   - Authenticate parties involved
   - Slower but secure

2. **Use symmetric encryption for data:**
   - Encrypt actual communications
   - Fast and efficient
   - Uses agreed-upon key

**Additional requirements:**
- Digital signatures verify identity
- Certificates prove authenticity
- Prevents man-in-the-middle attacks

**Question:** In the analogy presented, what real object is analogous to the public key?  
**Answer:** `lock`

---

## Task 3: RSA

### RSA Overview

**Named after:** Rivest, Shamir, Adleman  
**Type:** Public-key encryption algorithm  
**Purpose:** Secure data transmission over insecure channels

### Mathematical Foundation

**Based on:** Difficulty of factoring large numbers

**Easy direction:**
```
Multiply two primes: 113 × 127 = 14,351
```

**Hard direction:**
```
Factor 14,351 = ? × ?
(Much more difficult!)
```

**Real-world example:**
```
Prime 1: 982,451,653,031
Prime 2: 169,743,212,279
Product: 166,764,499,494,295,486,767,649

Multiplying: Easy (even by hand)
Factoring the product: Extremely difficult
```

**Actual RSA keys:**
- Prime numbers: ~300 digits each
- Product: ~600 digits
- Computers can't factor in reasonable time

### RSA Process

**Key generation (Bob's perspective):**

1. Choose two large primes: p and q
2. Calculate n = p × q
3. Calculate ϕ(n) = n - p - q + 1
4. Choose e (public exponent)
5. Calculate d (private exponent)
6. **Public key:** (n, e)
7. **Private key:** (n, d)

**Encryption (Alice sends to Bob):**
```
Ciphertext = (Plaintext^e) mod n
```

**Decryption (Bob receives):**
```
Plaintext = (Ciphertext^d) mod n
```

### Numerical Example

**Bob's key generation:**
```
p = 157
q = 199
n = p × q = 31,243

ϕ(n) = n - p - q + 1 = 30,888

e = 163 (public exponent)
d = 379 (private exponent)
where e × d mod ϕ(n) = 1

Public key: (31243, 163)
Private key: (31243, 379)
```

**Alice encrypts x = 13:**
```
y = x^e mod n
y = 13^163 mod 31243
y = 16,341
```

**Bob decrypts y = 16,341:**
```
x = y^d mod n
x = 16341^379 mod 31243
x = 13 ✓
```

### RSA Variables

**Standard notation:**
- **p, q:** Large prime numbers
- **n:** Product of p and q (modulus)
- **e:** Public exponent
- **d:** Private exponent
- **m:** Message (plaintext)
- **c:** Ciphertext
- **Public key:** (n, e)
- **Private key:** (n, d)

### RSA in CTFs

**Common challenges:**
- Given some variables, find others
- Break weak RSA implementations
- Factor small n values
- Find d from e and ϕ(n)

**Useful tools:**
- **RsaCtfTool:** Automated RSA attack tool
- **rsatool:** Key generation and manipulation
- **FactorDB:** Online integer factorization

**Recommended practice:**
- Breaking RSA room on TryHackMe
- CryptoHack RSA challenges

**Question:** Knowing that p = 4391 and q = 6659. What is n?  
**Answer:** `29,244,469` (4391 × 6659)

**Question:** Knowing that p = 4391 and q = 6659. What is ϕ(n)?  
**Answer:** `29,230,420` (n - p - q + 1 = 29244469 - 4391 - 6659 + 1)

---

## Task 4: Diffie-Hellman Key Exchange

### The Key Distribution Problem

**Scenario:** Need to establish shared secret without pre-existing secure channel

**Challenge:** Can't send symmetric key over insecure network

**Solution:** Diffie-Hellman Key Exchange

### How It Works (Abstract)

**Setup:**
- Alice and Bob each have secrets (A and B)
- They have public common material (C)

**Assumptions:**
1. Combined secrets can't be separated
2. Order of combination doesn't matter

**Process:**
1. Alice creates AC, Bob creates BC
2. They exchange AC and BC publicly
3. Alice combines BC with A → ABC
4. Bob combines AC with B → ABC
5. Both have identical shared secret: ABC

### Diffie-Hellman Step-by-Step

**Step 1: Agree on public parameters**
```
p = large prime number
g = generator (0 < g < p)

Example: p = 29, g = 3
(Real-world: much larger numbers)
```

**Step 2: Generate private keys**
```
Alice chooses: a = 13 (private)
Bob chooses: b = 15 (private)

Keep these secret!
```

**Step 3: Calculate public keys**
```
Alice calculates: A = g^a mod p = 3^13 mod 29 = 19
Bob calculates: B = g^b mod p = 3^15 mod 29 = 26
```

**Step 4: Exchange public keys**
```
Alice sends A = 19 to Bob
Bob sends B = 26 to Alice

Can be sent over insecure channel!
```

**Step 5: Calculate shared secret**
```
Alice calculates: s = B^a mod p = 26^13 mod 29 = 10
Bob calculates: s = A^b mod p = 19^15 mod 29 = 10

Both get same result: 10
This is the shared secret key!
```

### Why It's Secure

**Eavesdropper sees:**
- p = 29
- g = 3
- A = 19
- B = 26

**Eavesdropper needs to find:**
- a or b (private keys)
- This is the **discrete logarithm problem**
- Computationally infeasible with large numbers

### Diffie-Hellman + RSA

**Common combination:**
- **Diffie-Hellman:** Key agreement
- **RSA:** Digital signatures, authentication

**Why both?**
- DH alone vulnerable to man-in-the-middle
- RSA signatures prove identity
- Combined: secure and authenticated

**Question:** Consider p = 29, g = 5, a = 12. What is A?  
**Answer:** `5^12 mod 29 = 10`

**Question:** Consider p = 29, g = 5, b = 17. What is B?  
**Answer:** `5^17 mod 29 = 20`

**Question:** Knowing that p = 29, a = 12, and B = 20, what is the key?  
**Answer:** `20^12 mod 29 = 10`

**Question:** Knowing that p = 29, b = 17, and A = 10, what is the key?  
**Answer:** `10^17 mod 29 = 10`

---

## Task 5: SSH

### SSH Authentication Overview

**Two-step authentication:**
1. **Server authentication:** Client verifies server identity
2. **Client authentication:** Server verifies client identity

### Authenticating the Server

**First connection:**
```
ssh 10.10.244.173

The authenticity of host '10.10.244.173' can't be established.
ED25519 key fingerprint is SHA256:lLzhZc7YzRBDchm02qTX0qsLqeeiTCJg5ipOT0E/YM8.
Are you sure you want to continue connecting (yes/no)?
```

**What's happening:**
- Server sends public key fingerprint
- Client checks if it recognizes this key
- User must verify server identity
- Prevents man-in-the-middle attacks

**After confirmation:**
- SSH client records public key
- Future connections silent
- Warning if key changes

### Authenticating the Client

**Password authentication:**
- Simple but less secure
- Vulnerable to brute force
- Not recommended for production

**Key-based authentication (recommended):**
- Uses public/private key pairs
- More secure than passwords
- Default on many Linux distributions

### SSH Key Generation

**Generate key pair:**
```bash
ssh-keygen -t ed25519
```

**Supported algorithms:**
- **DSA:** Digital Signature Algorithm
- **ECDSA:** Elliptic Curve DSA
- **ECDSA-SK:** ECDSA with Security Key
- **Ed25519:** EdDSA with Curve25519 (recommended)
- **Ed25519-SK:** Ed25519 with Security Key
- **RSA:** Traditional RSA keys

**Generation example:**
```bash
ssh-keygen -t ed25519

Generating public/private ed25519 key pair.
Enter file: /home/user/.ssh/id_ed25519
Enter passphrase: [optional]

Your public key: /home/user/.ssh/id_ed25519.pub
Your private key: /home/user/.ssh/id_ed25519
```

### SSH Key Files

**Public key (id_ed25519.pub):**
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINqNMqNhpXZGt6T8Q8bOplyTeldfWq3T3RyNJTmTMJq9 user@host
```
- Share this with servers
- Add to `~/.ssh/authorized_keys` on remote host

**Private key (id_ed25519):**
```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMw...
-----END OPENSSH PRIVATE KEY-----
```
- **NEVER share this!**
- Keep on your local machine only
- Protect with passphrase

### SSH Private Key Security

**Critical security rules:**

1. **Never share private keys**
   - Treat like passwords
   - Anyone with your key can access your servers

2. **Use passphrases**
   - Encrypt private key
   - Passphrase never transmitted
   - Only decrypts key locally

3. **Set correct permissions**
   ```bash
   chmod 600 ~/.ssh/id_ed25519
   ```
   - Only owner can read/write
   - SSH ignores improperly secured keys

4. **Generate keys locally**
   ```bash
   ssh-copy-id user@host
   ```
   - Create on your machine
   - Copy public key to server
   - Private key never leaves your system

### Using SSH Keys

**Connect with specific key:**
```bash
ssh -i ~/.ssh/id_ed25519 user@host
```

**Server-side authorized keys:**
```
~/.ssh/authorized_keys
```
- Contains public keys of allowed users
- One key per line
- Controls who can log in

### SSH Keys in Security Testing

**CTF/Pentesting uses:**
- "Upgrade" reverse shells to stable SSH
- Leave backdoor access
- Better than unstable shells
- Tab completion and Ctrl+C work

**Note:** www-data user typically can't SSH, but regular users and root can

**Question:** Check the SSH Private Key in ~/Public-Crypto-Basics/Task-5. What algorithm does the key use?  
**Answer:** `[View the key file - look for BEGIN line indicating RSA, ED25519, etc.]`

---

## Task 6: Digital Signatures and Certificates

### What is a Digital Signature?

**Purpose:**
- Verify authenticity of digital documents
- Confirm integrity (not modified)
- Prove who created/signed the document

**How it works:**
1. Sign document with private key
2. Others verify with your public key
3. Only you have private key → proves you signed it

**Legal status:** In many countries, digital signatures = physical signatures

### Simple Digital Signature Process

**Signing (Bob):**
1. Create hash of document
2. Encrypt hash with private key
3. Send: original document + encrypted hash

**Verifying (Alice):**
1. Receive document + encrypted hash
2. Decrypt hash with Bob's public key
3. Calculate hash of received document
4. Compare: if hashes match → authentic and unmodified

**Important distinction:**
- **Electronic signature:** Pasted image (not secure)
- **Digital signature:** Cryptographic proof (secure)

### TLS Certificates

**Problem:** How does browser know server is legitimate?

**Solution:** TLS certificates

**Certificate chain of trust:**
```
Root CA (trusted by OS/browser)
    ↓
Intermediate CA
    ↓
Organization Certificate
    ↓
Server Certificate (tryhackme.com)
```

**How it works:**
1. Browser trusts root CAs (pre-installed)
2. Root CA signs intermediate CA
3. Intermediate CA signs organization
4. Organization signs server certificate
5. Browser verifies entire chain

### Certificate Authorities

**Trusted CAs:**
- Pre-installed in OS/browsers
- Examples: Let's Encrypt, DigiCert, GlobalSign
- List for Firefox: [Mozilla CA list]
- List for Chrome: [Google CA list]

**Getting a certificate:**

**Commercial CAs:**
- Purchase annual certificate
- Validate domain ownership
- Pay fees

**Let's Encrypt (FREE):**
- Free automated certificates
- 90-day validity (auto-renewal)
- Recommended for all websites
- Easy to set up

### HTTPS Implementation

**Requirements:**
1. Own a domain
2. Get TLS certificate
3. Configure web server
4. Enable HTTPS

**Benefits:**
- Encrypted communication
- User trust (lock icon)
- SEO advantages
- Required for modern web

**Question:** What does a remote web server use to prove itself to the client?  
**Answer:** `certificate`

**Question:** What would you use to get a free TLS certificate for your website?  
**Answer:** `Let's Encrypt`

---

## Task 7: PGP and GPG

### What is PGP/GPG?

**PGP (Pretty Good Privacy):**
- Software for encryption and signing
- Proprietary (originally)

**GPG (GNU Privacy Guard):**
- Open-source implementation of OpenPGP
- Free alternative to PGP
- Widely used on Linux

**Common uses:**
- Encrypt email messages
- Sign emails for authenticity
- Encrypt files
- Verify software downloads

### Generating GPG Keys

**Command:**
```bash
gpg --full-gen-key
```

**Configuration options:**

**Key type:**
```
(1) RSA and RSA
(2) DSA and Elgamal
(9) ECC (sign and encrypt) - default
```

**Elliptic curve:**
```
(1) Curve 25519 - default
(4) NIST P-384
(6) Brainpool P-256
```

**Expiration:**
```
0 = never expires
<n> = expires in n days
<n>w = weeks
<n>m = months
<n>y = years
```

**User information:**
```
Real name: strategos
Email: strategos@tryhackme.thm
Comment: Work email key
```

**Result:**
```
pub   ed25519 2024-08-29
      AB7E6AA87B6A8E0D159CA7FFE5E63DBD5F83D5ED
uid   Strategos <strategos@tryhackme.thm>
sub   cv25519 2024-08-29 [E]
```

### Using GPG Keys

**Import key from backup:**
```bash
gpg --import backup.key
```

**List keys:**
```bash
gpg --list-keys
```

**Encrypt message:**
```bash
gpg --encrypt --recipient strategos@tryhackme.thm message.txt
```

**Decrypt message:**
```bash
gpg --decrypt confidential_message.gpg
```

**Sign file:**
```bash
gpg --sign document.pdf
```

**Verify signature:**
```bash
gpg --verify document.pdf.sig
```

### GPG Key Security

**Passphrase protection:**
- Private keys can be passphrase-protected
- Similar to SSH key passphrases
- Adds extra security layer

**Key cracking (CTFs):**
- Use John the Ripper
- Tool: gpg2john
- Converts GPG key to John format
- Attempts to crack passphrase

**Key backup:**
- Critical: backup private keys
- Store in secure location
- Need for new computers
- Can't decrypt old messages without key

### GPG in Email

**Encryption workflow:**

**Sending (Alice):**
1. Write email
2. Encrypt with recipient's public key
3. Send encrypted message

**Receiving (Bob):**
1. Receive encrypted email
2. Decrypt with private key
3. Read plaintext message

**Signing workflow:**

**Sending (Alice):**
1. Write email
2. Sign with private key
3. Send message + signature

**Receiving (Bob):**
1. Receive email + signature
2. Verify with Alice's public key
3. Confirm authenticity

**Question:** Use GPG to decrypt the message in ~/Public-Crypto-Basics/Task-7. What secret word does the message hold?  
**Answer:** `[Run: gpg --decrypt ~/Public-Crypto-Basics/Task-7/message.gpg]`

---

## Task 8: Conclusion

### Key Concepts Summary

**Cryptography vs Cryptanalysis:**
- **Cryptography:** Science of securing communication
- **Cryptanalysis:** Study of breaking cryptographic systems

**Attack types:**
- **Brute-force:** Try every possible key
- **Dictionary:** Try common passwords/words

### Topics Covered

**1. Asymmetric Cryptography Basics**
- Public/private key pairs
- Solves key distribution problem
- Slower than symmetric encryption

**2. RSA**
- Based on prime factorization
- Variables: p, q, n, e, d, m, c
- Used for encryption and digital signatures

**3. Diffie-Hellman**
- Key exchange protocol
- Based on discrete logarithm problem
- Enables secure key agreement

**4. SSH**
- Server and client authentication
- Key-based login (more secure)
- Algorithms: RSA, Ed25519, ECDSA

**5. Digital Signatures & Certificates**
- Prove authenticity and integrity
- Certificate chains of trust
- Let's Encrypt for free TLS

**6. PGP/GPG**
- Email encryption
- File encryption and signing
- Open-source implementation

### Next Steps

**Continue learning:**
- **Next room:** Hashing Basics
- Hash functions and applications
- Password hashing
- Integrity verification

**Further topics:**
- Advanced cryptographic protocols
- Blockchain cryptography
- Quantum cryptography
- Cryptographic vulnerabilities

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Secure Web Server**
```bash
# Get Let's Encrypt certificate
certbot certonly --standalone -d example.com

# Configure web server with TLS
# Enable HTTPS on port 443
```

**Scenario 2: SSH Key Authentication**
```bash
# Generate key pair
ssh-keygen -t ed25519

# Copy to server
ssh-copy-id user@server

# Connect
ssh user@server
```

**Scenario 3: Encrypted Email**
```bash
# Generate GPG key
gpg --full-gen-key

# Export public key
gpg --export --armor email@example.com > public.key

# Share public key
# Receive encrypted emails
```

### Security Best Practices

**Key management:**
- Backup private keys securely
- Use strong passphrases
- Rotate keys periodically
- Never share private keys

**Algorithm selection:**
- RSA: 2048-bit minimum (4096 recommended)
- ECC: 256-bit (equivalent to RSA-3072)
- Ed25519: Recommended for SSH

**Certificate validation:**
- Verify certificate chains
- Check expiration dates
- Use certificate pinning (advanced)

---

**Room Completed:** ✓  
**Date:** 02/20/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
