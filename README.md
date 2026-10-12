# Cryptography Security Lab

Hands-on cryptography investigations demonstrating symmetric encryption, asymmetric encryption, password-based key derivation, hybrid file protection, and recovery verification using OpenSSL.

This repository contains two investigations conducted in an authorized Linux lab environment using synthetic test data. Each includes documented commands, technical findings, screenshots, security implications, and evidence limitations.

## Investigations

### 1. AES — Symmetric File Encryption

**Focus:** AES-256-CBC encryption, PBKDF2 key derivation, and file recovery verification.

The investigation began with a guided OpenSSL encryption workflow and identified a deprecated password-based key derivation configuration.

An independent exercise improved the implementation using PBKDF2 with 100,000 iterations, tested incorrect-passphrase behavior, inspected ciphertext, and confirmed successful file recovery.

**Key findings:**
- AES-256-CBC generated an OpenSSL salted ciphertext artifact.
- Decryption recovered the original plaintext, verified using SHA-256.
- PBKDF2 addressed the legacy key-derivation warning.
- Incorrect-passphrase decryption failed with `bad decrypt`.
- SHA-256 and `cmp` confirmed byte-for-byte recovery.

**Security consideration:** AES-CBC provides confidentiality but does not inherently authenticate ciphertext or prevent undetected modifications.

**Evidence:** 3 findings, 3 screenshots.

[View AES Investigation](investigations/aes-symmetric-encryption/README.md)

### 2. RSA — Asymmetric and Hybrid Encryption

**Focus:** RSA key operations, public/private key encryption, message-size limitations, and hybrid RSA/AES file protection.

The investigation demonstrated RSA encryption and decryption using a 2048-bit key pair.

An independent experiment revealed that a 1 KiB file exceeded the direct RSA encryption limit. A hybrid workflow subsequently encrypted the larger file using AES-256-CBC and protected its passphrase material using RSA.

**Key findings:**
- RSA public-key encryption converted a 40-byte plaintext file into a 256-byte ciphertext artifact.
- RSA private-key decryption recovered the original data.
- Direct RSA encryption of a 1 KiB file failed with `data too large for key size`.
- AES-256-CBC with PBKDF2 encrypted the larger file.
- RSA protected and recovered the AES passphrase material.
- SHA-256 and `cmp` verified successful recovery.

**Security consideration:** The demonstrated hybrid implementation was educational rather than production-ready. New implementations should use appropriate RSA padding, authenticated encryption, and secure key management.

**Evidence:** 3 findings, 3 screenshots.

[View RSA Investigation](investigations/rsa-asymmetric-encryption/README.md)

## Tools and Technologies

| Tool / Technology | Application |
| --- | --- |
| OpenSSL | File encryption, decryption, RSA key operations, and PBKDF2 |
| AES-256-CBC | Symmetric file encryption |
| RSA-2048 | Public-key encryption and private-key recovery |
| PBKDF2 | Password-based key derivation |
| SHA-256 | File recovery verification |
| `cmp` | Byte-for-byte file comparison |
| `xxd` | Hexadecimal ciphertext inspection |
| `file`, `strings`, `grep` | Encrypted artifact identification and inspection |
| Linux | Controlled execution environment |

## Investigation Methodology

Both investigations followed an evidence-driven workflow:

1. **Establish scope:** Define authorized test data and cryptographic objectives.
2. **Perform operations:** Generate key material and execute encryption/decryption commands.
3. **Inspect artifacts:** Examine ciphertext structure, output sizes, and relevant tool messages.
4. **Validate recovery:** Compare hashes and file contents against the originals.
5. **Test limitations:** Investigate incorrect-passphrase behavior and RSA message-size constraints.
6. **Assess security:** Distinguish successful encryption from confidentiality, authenticity, and key-management guarantees.
7. **Document evidence:** Preserve findings, commands, screenshots, and technical limitations.

## Repository Structure

```text
cryptography-security-lab/
├── README.md
└── investigations/
    ├── aes-symmetric-encryption/
    │   ├── README.md
    │   └── evidence/
    │       └── 3 screenshots
    └── rsa-asymmetric-encryption/
        ├── README.md
        └── evidence/
            └── 3 screenshots
```

## Scope and Limitations

All cryptographic operations were conducted in an authorized lab environment using synthetic test data.

The AES investigation used CBC mode, which does not provide authenticated ciphertext integrity. The RSA investigation used PKCS#1 v1.5 encryption padding rather than RSA-OAEP.

Neither investigation implemented enterprise key management, hardware security modules, or a production-ready encrypted-file format.

Successful recovery verification demonstrates correctness for the tested artifacts, not comprehensive cryptographic security.

## Skills Demonstrated

- Symmetric and asymmetric cryptographic operations.
- OpenSSL command-line encryption and decryption.
- Password-based key derivation with PBKDF2.
- RSA public/private key generation and usage.
- Ciphertext inspection and format identification.
- Hybrid RSA/AES file-protection workflows.
- Cryptographic error interpretation and troubleshooting.
- SHA-256 and byte-for-byte recovery verification.
- Security analysis of padding, integrity protection, and key management.
- Evidence-based technical documentation.

## Portfolio

These investigations complement my broader cybersecurity portfolio, including network security, security operations, digital forensics, cloud security, and identity and access management.

**GitHub:** [abmathi](https://github.com/abmathi)

**Portfolio:** [Austin's Angle](https://austinsangle.com)