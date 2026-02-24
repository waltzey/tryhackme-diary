# TryHackMe - Cryptography Basics

**Room:** Cryptography Basics  
**Difficulty:** Easy  
**Focus:** Encryption fundamentals, symmetric/asymmetric cryptography, basic cryptographic mathematics

---

## Overview

Cryptography is the foundation of secure digital communication. This introductory room covers essential cryptographic concepts, encryption types, and basic mathematical operations used in modern cryptography. Understanding these fundamentals is crucial for cybersecurity professionals.

**Learning Objectives:**
- Understand key cryptography terminology
- Recognize the importance of cryptography in daily life
- Learn Caesar Cipher and historical ciphers
- Distinguish between symmetric and asymmetric encryption
- Apply basic cryptographic mathematics (XOR and modulo)

---

## Task 1: Introduction

### What is Cryptography?

Cryptography enables secure communication in the presence of adversaries. It ensures:
- **Confidentiality:** Only intended recipients can read messages
- **Integrity:** Messages cannot be altered undetected
- **Authenticity:** Verify identity of communicating parties

### Room Series

**Part 1:** Cryptography Basics (this room)  
**Part 2:** Public Key Cryptography Basics  
**Part 3:** Hashing Basics

**Prerequisites:** Basic Linux command line knowledge

---

## Task 2: Importance of Cryptography

### Core Security Goals

Cryptography protects three fundamental properties:

**1. Confidentiality**
- Ensures data remains private
- Prevents unauthorized access
- Encrypts sensitive information

**2. Integrity**
- Detects unauthorized modifications
- Verifies data hasn't been tampered with
- Uses hash functions for verification

**3. Authenticity**
- Confirms identity of parties
- Validates certificates
- Prevents impersonation attacks

### Real-World Applications

**Everyday Cryptography Usage:**

**Web Authentication:**
- Login credentials encrypted before transmission
- Prevents password theft via network sniffing
- HTTPS ensures secure web browsing

**SSH Connections:**
- Encrypted tunnel between client and server
- Protects terminal sessions from eavesdropping
- Secure remote administration

**Online Banking:**
- Browser verifies server certificates
- Confirms connection to legitimate bank
- Prevents man-in-the-middle attacks

**File Downloads:**
- Hash functions verify file integrity
- Confirms downloaded file matches original
- Detects corruption or tampering

### Compliance and Regulations

**Payment Card Industry Data Security Standard (PCI DSS):**

**Requirements:**
- Encrypt cardholder data at rest (stored)
- Encrypt cardholder data in motion (transmitted)
- Mandatory for all entities handling credit cards
- Ensures minimum security standards

**Key points:**
- Applies to credit card transactions
- Protects sensitive payment information
- Legal requirement for merchants
- Regular audits and compliance checks

**Healthcare Data Protection:**

Different countries have specific regulations:

**United States:**
- **HIPAA** (Health Insurance Portability and Accountability Act)
- **HITECH** (Health Information Technology for Economic and Clinical Health)

**European Union:**
- **GDPR** (General Data Protection Regulation)

**United Kingdom:**
- **DPA** (Data Protection Act)

**Common requirements:**
- Encrypt medical records
- Protect patient privacy
- Secure data transmission
- Control access to health information

### Hidden but Essential

Cryptography works behind the scenes:
- Users rarely interact directly with it
- Implemented automatically by applications
- Critical for digital trust
- Foundation of secure internet

**Question:** What is the standard required for handling credit card information?  
**Answer:** `PCI DSS`

---

## Task 3: Plaintext to Ciphertext

### Encryption Process Flow

```
Plaintext → [Encryption Function + Key] → Ciphertext
Ciphertext → [Decryption Function + Key] → Plaintext
```

### Key Terminology

**Plaintext:**
- Original, readable message or data
- Can be text, images, videos, or any data
- Input to encryption process
- Examples: "hello", credit card numbers, medical records

**Ciphertext:**
- Scrambled, unreadable encrypted data
- Output of encryption process
- Should reveal no information about original
- Only approximate size may be discernible

**Cipher:**
- Algorithm for encryption/decryption
- Mathematical method to transform data
- Usually developed by mathematicians
- Publicly known (security through obscurity is bad)

**Key:**
- String of bits used by cipher
- Secret value (except public keys)
- Must remain confidential
- Changes the cipher's output

**Encryption:**
- Process converting plaintext to ciphertext
- Uses cipher and key
- Cipher choice is public
- Key must remain secret

**Decryption:**
- Reverse process of encryption
- Converts ciphertext back to plaintext
- Requires correct key
- Without key, should be impossible

### Encryption Example

**Scenario:**
```
Input: Medical record (plaintext)
Key: Secret 256-bit value
Cipher: AES-256
Output: Encrypted file (ciphertext)
```

**Decryption:**
```
Input: Encrypted file (ciphertext)
Key: Same 256-bit value
Cipher: AES-256
Output: Original medical record (plaintext)
```

### Security Principles

**Kerckhoffs's Principle:**
- Cipher algorithm should be public
- Security relies on key secrecy
- "Security through obscurity" is weak
- Modern cryptography follows this principle

**Key Security:**
- Keys must be kept secret
- Compromise of key = compromise of data
- Key management is critical
- Use strong, random keys

**Question:** What do you call the encrypted plaintext?  
**Answer:** `ciphertext`

**Question:** What do you call the process that returns the plaintext?  
**Answer:** `decryption`

---

## Task 4: Historical Ciphers

### Caesar Cipher

**Origin:** First century BCE (Julius Caesar)  
**Method:** Shift each letter by fixed number

**Encryption Example:**

```
Plaintext: TRYHACKME
Key: 3 (shift right by 3)
Cipher: Caesar Cipher

T → W
R → U
Y → B
H → K
A → D
C → F
K → N
M → P
E → H

Ciphertext: WUBKDFNPH
```

**How it works:**
- Each letter shifts by key positions
- Wraps around after Z (Z+1 = A)
- Simple substitution cipher
- Preserves letter positions

**Decryption:**

```
Ciphertext: WUBKDFNPH
Key: 3 (shift left by 3)

W → T
U → R
B → Y
K → H
D → A
F → C
N → K
P → M
H → E

Plaintext: TRYHACKME
```

### Why Caesar Cipher is Insecure

**Limited keyspace:**
- Only 25 possible keys (shift 1-25)
- Shift by 26 returns original text
- Trivial to brute force
- Can try all keys in seconds

**Brute Force Attack:**
```
Ciphertext: WUBKDFNPH

Try key 1: VTAGZCMOG
Try key 2: USZYBDLNF
Try key 3: TRYHACKME ← Found it!
```

**Modern perspective:**
- Completely insecure by today's standards
- Educational value only
- Demonstrates basic encryption concepts
- Historical significance

### Other Historical Ciphers

**Vigenère Cipher (16th century):**
- Polyalphabetic substitution
- Uses keyword for encryption
- More secure than Caesar
- Still breakable with frequency analysis

**Enigma Machine (WWII):**
- Electromechanical rotor cipher
- Used by Nazi Germany
- Broken by Allied cryptanalysts
- Led to computer development

**One-Time Pad (Cold War):**
- Theoretically unbreakable
- Requires key as long as message
- Key used only once
- Impractical for most applications

### Interactive Learning

**Cryptii Website Tool:**
- Experiment with Caesar cipher
- Adjust rotation values
- See instant transformations
- Understand shift patterns

**Question:** Knowing that XRPCTCRGNEI was encrypted using Caesar Cipher, what is the original plaintext?  
**Answer:** `[Try all 25 possible shifts, or use frequency analysis to find common English words]`

**Solution approach:**
```bash
# Try each shift from 1-25
# Look for readable English text
# Common words: THE, AND, FOR, etc.
```

---

## Task 5: Types of Encryption

### Symmetric Encryption

**Definition:** Same key for encryption and decryption

**How it works:**
```
Encryption: Plaintext + Key → Ciphertext
Decryption: Ciphertext + Key → Plaintext
```

**Also known as:**
- Private key cryptography
- Secret key cryptography
- Shared key encryption

**Key challenge:**
- Must keep key secret
- Secure key distribution problem
- Need secure channel to share key
- Difficult with many recipients

**Real-world scenario:**

**Password-protected document:**
1. Create encrypted document
2. Email document to colleague ✓
3. Email password to colleague ✗ (Insecure!)
4. Must use different channel for password
5. Often requires in-person key exchange

**Why email password is insecure:**
- Anyone with mailbox access gets both
- No separation of encrypted data and key
- Defeats purpose of encryption

### Standard Symmetric Ciphers

**DES (Data Encryption Standard):**
- **Adopted:** 1977
- **Key size:** 56 bits
- **Status:** Broken in 1999 (< 24 hours)
- **Security:** Obsolete, do not use
- **Reason for failure:** Key too short for modern computing

**3DES (Triple DES):**
- **Method:** DES applied three times
- **Key size:** 168 bits (effective: 112 bits)
- **Status:** Deprecated in 2019
- **Use case:** Legacy systems only
- **Reason:** Ad-hoc solution when DES failed
- **Recommendation:** Replace with AES

**AES (Advanced Encryption Standard):**
- **Adopted:** 2001
- **Key sizes:** 128, 192, or 256 bits
- **Status:** Current standard
- **Security:** Considered secure
- **Usage:** Widely adopted globally
- **Performance:** Fast and efficient

**AES Key Sizes:**
```
AES-128: 128-bit key (good security)
AES-192: 192-bit key (higher security)
AES-256: 256-bit key (maximum security)
```

### Asymmetric Encryption

**Definition:** Different keys for encryption and decryption

**How it works:**
```
Encryption: Plaintext + Public Key → Ciphertext
Decryption: Ciphertext + Private Key → Plaintext
```

**Also known as:**
- Public key cryptography
- Public-private key encryption

**Key pair:**
- **Public key:** Shared with everyone (encrypt)
- **Private key:** Kept secret (decrypt)

**Advantages:**
- No secure channel needed for key distribution
- Public key can be freely shared
- Solves key distribution problem
- Enables digital signatures

### Standard Asymmetric Ciphers

**RSA (Rivest-Shamir-Adleman):**
- **Key sizes:** 2048, 3072, 4096 bits
- **Minimum recommended:** 2048 bits
- **Based on:** Prime factorization
- **Speed:** Slower than symmetric
- **Usage:** Widely adopted

**Diffie-Hellman:**
- **Key sizes:** 2048, 3072, 4096 bits
- **Minimum recommended:** 2048 bits
- **Purpose:** Key exchange
- **Based on:** Discrete logarithm problem

**ECC (Elliptic Curve Cryptography):**
- **Key sizes:** 256, 384, 521 bits
- **Efficiency:** Shorter keys, same security
- **Comparison:** ECC-256 ≈ RSA-3072
- **Advantages:** Faster, smaller keys
- **Growing adoption:** Modern systems

**Key Size Comparison:**

| Cipher | Key Size | Security Level |
|--------|----------|----------------|
| AES-128 | 128 bits | Symmetric standard |
| RSA | 2048 bits | Minimum recommended |
| RSA | 3072 bits | High security |
| ECC | 256 bits | Equivalent to RSA-3072 |

### Performance Considerations

**Symmetric encryption:**
- Fast encryption/decryption
- Efficient for large data
- Low computational overhead
- Suitable for bulk data

**Asymmetric encryption:**
- Slower than symmetric
- Higher computational cost
- Larger key sizes needed
- Used for key exchange, not bulk data

**Common hybrid approach:**
1. Use asymmetric to exchange symmetric key
2. Use symmetric key for bulk data encryption
3. Best of both worlds (security + speed)

### Mathematical Foundation

**Asymmetric encryption relies on:**
- Easy to compute in one direction
- Extremely difficult to reverse
- "Trapdoor" functions
- Computationally infeasible to break

**Examples:**
- **Prime factorization (RSA):** Easy to multiply primes, hard to factor product
- **Discrete logarithm (Diffie-Hellman):** Easy to exponentiate, hard to find exponent
- **Elliptic curves (ECC):** Easy to multiply points, hard to divide

### New Terms Summary

**Alice and Bob:**
- Fictional characters in crypto examples
- Represent communicating parties
- Industry standard naming convention

**Symmetric encryption:**
- Same key for encryption/decryption
- Key must remain secret
- Fast and efficient

**Asymmetric encryption:**
- Public key for encryption
- Private key for decryption
- Solves key distribution problem

**Question:** Should you trust DES? (Yea/Nay)  
**Answer:** `Nay`

**Question:** When was AES adopted as an encryption standard?  
**Answer:** `2001`

---

## Task 6: Basic Math

### XOR Operation

**XOR (Exclusive OR):**
- Binary logical operation
- Symbol: ⊕ or ^
- Fundamental in cryptography

**Truth Table:**

| A | B | A ⊕ B |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

**Rule:** Returns 1 if bits are different, 0 if same

**Example calculation:**
```
  1010
⊕ 1100
------
  0110

Bit by bit:
1 ⊕ 1 = 0
0 ⊕ 1 = 1
1 ⊕ 0 = 1
0 ⊕ 0 = 0
```

### XOR Properties

**Self-cancellation:**
```
A ⊕ A = 0
```
Any value XOR with itself equals zero

**Identity:**
```
A ⊕ 0 = A
```
Any value XOR with zero equals itself

**Commutative:**
```
A ⊕ B = B ⊕ A
```
Order doesn't matter

**Associative:**
```
(A ⊕ B) ⊕ C = A ⊕ (B ⊕ C)
```
Grouping doesn't matter

### XOR in Cryptography

**Simple symmetric encryption:**

**Encryption:**
```
P = Plaintext
K = Secret key
C = Ciphertext

C = P ⊕ K
```

**Decryption:**
```
P = C ⊕ K

Proof:
C ⊕ K = (P ⊕ K) ⊕ K
      = P ⊕ (K ⊕ K)    [associative]
      = P ⊕ 0          [self-cancellation]
      = P              [identity]
```

**Practical example:**
```
Plaintext:  01001000 01101001  ("Hi")
Key:        11001010 10101100
Ciphertext: 10000010 11000101

To decrypt:
Ciphertext: 10000010 11000101
Key:        11001010 10101100
Plaintext:  01001000 01101001  ("Hi")
```

**Limitations:**
- Key must be as long as plaintext
- Key must be truly random
- Key must never be reused
- Vulnerable if key is discovered

**Real-world usage:**
- Building block in complex ciphers
- Used in stream ciphers
- Part of block cipher operations
- Foundation for advanced encryption

### Modulo Operation

**Definition:** Remainder after division

**Notation:**
- `X % Y` or `X mod Y`
- Returns remainder when X divided by Y

**Examples:**

**Example 1:**
```
25 % 5 = 0
Calculation: 25 ÷ 5 = 5 remainder 0
Formula: 25 = 5 × 5 + 0
```

**Example 2:**
```
23 % 6 = 5
Calculation: 23 ÷ 6 = 3 remainder 5
Formula: 23 = 3 × 6 + 5
```

**Example 3:**
```
23 % 7 = 2
Calculation: 23 ÷ 7 = 3 remainder 2
Formula: 23 = 3 × 7 + 2
```

### Modulo Properties

**Range of results:**
```
For X % N:
Result is always: 0 to (N-1)

Examples:
X % 5 → results: 0, 1, 2, 3, 4
X % 10 → results: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
```

**Non-reversible:**
```
Given: X % 5 = 4
Possible X values: 4, 9, 14, 19, 24, 29...
Cannot determine exact X from result alone
```

**Always non-negative:**
```
Result of X % N is always ≥ 0
Result is always < N
```

### Modulo in Cryptography

**Uses in cryptography:**
- RSA encryption/decryption
- Diffie-Hellman key exchange
- Elliptic curve operations
- Hash functions
- Random number generation

**Why modulo is important:**
- Keeps numbers in fixed range
- Creates "clock arithmetic"
- Enables cyclic groups
- Foundation of many crypto algorithms

**Example in RSA:**
```
Encryption: C = (M^e) % N
Decryption: M = (C^d) % N

Where:
M = message
C = ciphertext
e = public exponent
d = private exponent
N = modulus
```

### Working with Large Numbers

**Tools for big integer math:**

**Python (built-in):**
```python
# Python handles arbitrary-precision integers
x = 118613842 % 9091
print(x)
```

**WolframAlpha (online):**
- Visit wolframalpha.com
- Enter: "118613842 mod 9091"
- Get instant result

**Calculator limitations:**
- Many calculators can't handle large numbers
- Use programming languages for crypto math
- Online tools available as backup

**Question:** What's 1001 ⊕ 1010?  
**Answer:** 
```
  1001
⊕ 1010
------
  0011  = 3 (decimal) or 0011 (binary)
```
**Answer:** `0011` or `3`

**Question:** What's 118613842 % 9091?  
**Answer:** `[Use Python: 118613842 % 9091, or WolframAlpha]`

**Question:** What's 60 % 12?  
**Answer:** 
```
60 ÷ 12 = 5 remainder 0
60 = 12 × 5 + 0
```
**Answer:** `0`

---

## Task 7: Summary

### Key Concepts Learned

**1. Cryptography Importance**
- Ensures confidentiality, integrity, authenticity
- Used daily (HTTPS, SSH, banking)
- Required by regulations (PCI DSS, HIPAA, GDPR)
- Foundation of digital trust

**2. Core Terminology**
- Plaintext → readable data
- Ciphertext → encrypted data
- Cipher → encryption algorithm
- Key → secret value
- Encryption/Decryption processes

**3. Historical Ciphers**
- Caesar Cipher (shift encryption)
- Only 25 possible keys
- Insecure by modern standards
- Educational value

**4. Encryption Types**

**Symmetric:**
- Same key for encrypt/decrypt
- Examples: DES (obsolete), 3DES (deprecated), AES (current)
- Fast but key distribution challenge

**Asymmetric:**
- Public/private key pairs
- Examples: RSA, Diffie-Hellman, ECC
- Slower but solves key distribution
- Smaller keys with ECC

**5. Cryptographic Math**

**XOR Operation:**
- Fundamental building block
- Properties: self-cancellation, identity, commutative, associative
- Used in simple encryption

**Modulo Operation:**
- Remainder after division
- Non-reversible
- Foundation of RSA, Diffie-Hellman
- Keeps values in range

### Complete Terminology Reference

| Term | Definition |
|------|------------|
| Plaintext | Original readable data |
| Ciphertext | Encrypted unreadable data |
| Cipher | Encryption algorithm |
| Key | Secret value for cipher |
| Encryption | Plaintext → Ciphertext |
| Decryption | Ciphertext → Plaintext |
| Symmetric | Same key for both |
| Asymmetric | Different keys (public/private) |
| XOR | Exclusive OR operation |
| Modulo | Remainder operation |

### Cipher Comparison

| Cipher | Type | Key Size | Status |
|--------|------|----------|--------|
| DES | Symmetric | 56-bit | Broken |
| 3DES | Symmetric | 168-bit | Deprecated |
| AES | Symmetric | 128/192/256-bit | Current standard |
| RSA | Asymmetric | 2048/3072/4096-bit | Widely used |
| ECC | Asymmetric | 256/384/521-bit | Growing adoption |

### Next Steps

**Continue learning:**
- **Next room:** Public Key Cryptography Basics
- Deep dive into RSA, Diffie-Hellman, ECC
- Digital signatures
- Certificate authorities
- Real-world implementations

**Future topics:**
- Hashing Basics
- Advanced cryptographic protocols
- SSL/TLS
- PGP/GPG
- Blockchain cryptography

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Secure Communication**
- Use asymmetric encryption for key exchange
- Use symmetric encryption for message content
- Combine strengths of both types

**Scenario 2: Password Storage**
- Never store plaintext passwords
- Use hashing (covered in future room)
- Add salt to prevent rainbow tables

**Scenario 3: File Encryption**
- Use AES for file encryption
- Generate strong random keys
- Secure key storage critical

### Best Practices

**Key Management:**
- Generate strong random keys
- Never reuse keys
- Secure key storage
- Regular key rotation

**Cipher Selection:**
- Use AES for symmetric encryption
- Use RSA-2048+ or ECC-256+ for asymmetric
- Avoid deprecated ciphers (DES, 3DES)
- Follow current standards

**Implementation:**
- Use established libraries
- Don't roll your own crypto
- Follow security guidelines
- Regular security audits

---
<img width="853" height="474" alt="image" src="https://github.com/user-attachments/assets/a9939903-450e-4f25-a01b-6f981ed0ee55" />

**Room Completed:** ✓  
**Date:** 02/24/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
