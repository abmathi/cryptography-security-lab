# AES-256 File Encryption and Validation with OpenSSL

## Executive Summary

This investigation demonstrates symmetric file encryption and decryption using OpenSSL and AES-256-CBC.

The exercise began with a guided encryption workflow, including secret generation, ciphertext inspection, decryption, and recovery verification.

An independent investigation extended the workflow to protect a simulated credentials file. The encryption process was improved using PBKDF2 with 100,000 iterations, followed by ciphertext inspection, an incorrect-key decryption test, and byte-for-byte recovery validation.

The investigation focused on understanding how encryption protects data confidentiality, how password-based key derivation affects an implementation, and why successful decryption should be verified rather than assumed.

## Investigation Scope

The work was conducted in an authorized Linux lab environment using OpenSSL and synthetic test data.

| Component | Implementation |
| --- | --- |
| Encryption algorithm | AES-256 |
| Cipher mode | CBC |
| Tool | OpenSSL |
| Guided exercise | Salted, password-based file encryption |
| Independent exercise | PBKDF2 with 100,000 iterations |
| Verification | SHA-256, `cmp`, `file`, `xxd`, `strings`, and `grep` |
| Test data | Sample plaintext and simulated credentials |

### Investigation Objectives

1. Encrypt and decrypt files using AES-256-CBC.
2. Inspect an encrypted artifact and identify its OpenSSL salted-file structure.
3. Verify that decryption reproduces the original file.
4. Identify and address the deprecated key-derivation warning encountered in the guided exercise.
5. Apply PBKDF2 to derive encryption material from separately stored passphrase material.
6. Test whether recognizable plaintext appears in the ciphertext.
7. Evaluate the limitations of encryption without authenticated integrity protection.

## Evidence Sources and Methodology

### Evidence Sources

The investigation used OpenSSL command output, encrypted-file inspection, cryptographic hashes, and file-comparison results.

Three screenshots preserve selected results from the guided and independent exercises.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Preparation | Create synthetic plaintext and generate secret material |
| Encryption | Encrypt the original file using AES-256-CBC |
| Inspection | Examine the encrypted file's format and visible content |
| Decryption | Recover the original file using the appropriate secret |
| Verification | Compare SHA-256 hashes and file contents |
| Improvement | Replace deprecated password-based key derivation with PBKDF2 |
| Negative testing | Attempt decryption with incorrect secret material |
| Assessment | Document confidentiality observations and security limitations |

### Evidence Standards

The analysis distinguishes successful encryption operations from broader security guarantees.

The presence of an OpenSSL salted-file header and apparently unreadable ciphertext does not independently prove that an encryption implementation is secure.

Matching SHA-256 hashes and a successful `cmp` comparison demonstrate that the recovered file matched the original during the test.

An unsuccessful incorrect-key decryption attempt does not establish authenticated encryption. AES-CBC alone does not detect all unauthorized ciphertext modifications.

## Investigation Organization

The report documents three findings:

1. AES-256-CBC Encryption and Ciphertext Inspection
2. Decryption and File Recovery Verification
3. Independent PBKDF2 Encryption and Validation

The final sections will summarize the security implications, evidence limitations, skills demonstrated, and supporting screenshots.

