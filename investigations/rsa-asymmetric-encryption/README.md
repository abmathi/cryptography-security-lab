# RSA Asymmetric Encryption and Hybrid File Protection

## Executive Summary

This investigation demonstrates asymmetric encryption using RSA and OpenSSL, followed by an independent experiment exploring the limitations of direct RSA encryption and the use of hybrid cryptography.

The guided exercise involved generating a 2048-bit RSA key pair, encrypting a small plaintext file with the public key, and recovering it with the corresponding private key.

The independent investigation extended the exercise by attempting to encrypt a 1 KiB file directly with RSA. OpenSSL rejected the operation with a `data too large for key size` error.

To address this limitation, I implemented a hybrid encryption workflow using AES-256-CBC to encrypt the larger file and RSA to protect the smaller AES passphrase material. The encrypted passphrase was subsequently recovered using the RSA private key, allowing the original file to be decrypted.

Successful recovery was validated through matching SHA-256 hashes and a direct file comparison.

The investigation demonstrates how asymmetric and symmetric encryption complement each other in practical data-protection workflows.

## Investigation Scope

The investigation was conducted in an authorized Linux lab environment using OpenSSL and synthetic test data.

| Component | Implementation |
| --- | --- |
| Asymmetric algorithm | RSA |
| RSA key length | 2048 bits |
| Symmetric algorithm | AES-256-CBC |
| Key derivation | PBKDF2 with 100,000 iterations |
| Cryptographic utility | OpenSSL |
| Guided exercise | RSA key generation and small-file encryption/decryption |
| Independent exercise | RSA size-limit testing and hybrid encryption |
| Verification | SHA-256 and `cmp` |
| Test data | Small plaintext file and 1 KiB random file |

### Investigation Objectives

1. Generate an RSA private key and derive the corresponding public key.
2. Encrypt a small plaintext file using the RSA public key.
3. Recover the original file using the RSA private key.
4. Verify successful file recovery.
5. Investigate RSA's direct-encryption message-size limitation.
6. Implement a hybrid RSA/AES encryption workflow.
7. Validate recovery of the AES passphrase and encrypted file.
8. Evaluate the security limitations of the demonstrated implementation.

## Evidence Sources and Methodology

### Evidence Sources

The investigation used OpenSSL command output, generated cryptographic artifacts, file-size observations, error messages, SHA-256 hashes, and direct file-comparison results.

Three screenshots document the guided RSA operations and independent hybrid-encryption experiment.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Key generation | Generate RSA private and public keys |
| RSA encryption | Encrypt a small file using the public key |
| RSA decryption | Recover plaintext using the private key |
| Validation | Compare original and recovered file contents |
| Size-limit testing | Attempt direct RSA encryption of a 1 KiB file |
| Hybrid encryption | Encrypt bulk data with AES and protect passphrase material with RSA |
| Hybrid recovery | Recover the AES passphrase, decrypt the file, and verify the result |
| Assessment | Evaluate cryptographic design considerations and limitations |

### Evidence Standards

The analysis distinguishes successful cryptographic operations from broader security guarantees.

A successful RSA encryption operation does not establish that RSA is appropriate for arbitrary-size data.

The 2048-bit RSA key produces a 256-byte RSA ciphertext block, but padding reduces the maximum plaintext size accepted by a single encryption operation.

The independent hybrid workflow demonstrated recovery of the original test data. It was not designed or assessed as a production-ready secure file format.

AES-CBC does not inherently authenticate ciphertext, and matching hashes calculated after recovery do not replace authenticated encryption.

The investigation also distinguishes encryption of a passphrase from direct encryption of a raw AES key.

## Investigation Organization

The report documents three findings:

1. RSA Key Generation and Public-Key Encryption
2. RSA Private-Key Decryption and Recovery Verification
3. Independent RSA Size-Limit Testing and Hybrid Encryption

The final sections consolidate security implications, evidence limitations, skills demonstrated, and supporting screenshots.