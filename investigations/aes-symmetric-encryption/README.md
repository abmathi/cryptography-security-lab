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

## Investigation Findings

### Finding 1 — AES-256-CBC Encryption and Ciphertext Inspection

#### Investigation Context

The first exercise examined file encryption using OpenSSL and AES-256 in Cipher Block Chaining (CBC) mode.

The objective was to encrypt a plaintext file, inspect the resulting encrypted artifact, and determine what information could be observed without decrypting it.

#### Encryption Procedure

I encrypted a sample text file using OpenSSL:

```bash
openssl enc -aes-256-cbc -salt \
  -in test_file.txt \
  -out encrypted_file.bin \
  -pass file:aes_key.bin
```

The command used AES-256-CBC, enabled salting, and read password material from `aes_key.bin`.

The `-pass file:` option supplies passphrase material for OpenSSL's password-based key derivation. It does not directly load a raw AES-256 encryption key.

#### Encrypted File Inspection

Following encryption, I inspected the original and encrypted files.

| Field | Observed Result |
| --- | --- |
| Original file | `test_file.txt` |
| Original file size | 46 bytes |
| Encrypted file | `encrypted_file.bin` |
| Encrypted file size | 64 bytes |
| File identification | `openssl enc'd data with salted password` |
| Hex inspection command | `xxd encrypted_file.bin` |
| Header | `Salted__` |
| Header bytes | `53616c7465645f5f` |

![AES encrypted file inspection](evidence/01-aes-encrypted-file-inspection.png)

*Figure 1 — OpenSSL AES-256-CBC encryption of a sample file, showing the deprecated key-derivation warning, file-size comparison, salted file identification, and `Salted__` header in the hexadecimal output.*

The encrypted file began with OpenSSL's `Salted__` marker, followed by salt bytes and ciphertext.

The encrypted artifact was larger than the original because the OpenSSL salted format includes an 8-byte marker, an 8-byte salt, and block-aligned encrypted data.

The hexadecimal inspection showed that the output was not stored as ordinary readable plaintext.

However, a salted header and unreadable bytes alone do not prove that the encryption configuration is secure.

#### Key-Derivation Warning

During encryption, OpenSSL displayed the following warning:

```text
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
```

The command used OpenSSL's legacy password-based derivation behavior rather than explicitly enabling PBKDF2.

This warning identified an improvement opportunity addressed in the independent investigation.

#### Security Relevance

Symmetric encryption helps protect stored information against unauthorized disclosure when suitable algorithms, key derivation, and secret-management practices are used.

File inspection can help identify whether an artifact was processed by an encryption utility and what format was produced.

Nevertheless, confirming an encrypted file format does not independently establish confidentiality against every threat or provide ciphertext integrity protection.

#### Analyst Assessment

**Assessment: AES-256-CBC encryption successfully produced an OpenSSL salted ciphertext artifact.**

The original 46-byte plaintext file was converted into a 64-byte encrypted artifact containing the OpenSSL salted-file header.

The encryption command also generated a deprecated key-derivation warning, motivating the subsequent PBKDF2 implementation.

### Finding 2 — Decryption and File Recovery Verification

#### Investigation Context

The second exercise focused on recovering the original plaintext from an AES-256-CBC encrypted file.

The objective was to confirm that the correct passphrase material could successfully decrypt the ciphertext and to verify that the recovered file matched the original.

#### Decryption Procedure

I decrypted the previously generated ciphertext using OpenSSL:

```bash
openssl enc -d -aes-256-cbc \
  -in encrypted_file.bin \
  -out decrypted_file.txt \
  -pass file:aes_key.bin
```

The command used the same AES cipher mode and passphrase source as the encryption operation.

OpenSSL displayed the same deprecated key-derivation warning observed during encryption, confirming that the guided workflow continued to use the legacy password-based derivation method.

#### Plaintext Recovery

After decryption, I inspected the output file:

```bash
cat decrypted_file.txt
```

The recovered contents were:

```text
This is a sample file for AES encryption lab.
```

The successful recovery demonstrated that the ciphertext could be decrypted using the appropriate passphrase material.

#### SHA-256 Verification

To verify the recovered file, I calculated SHA-256 hashes for the original and decrypted files:

```bash
sha256sum test_file.txt decrypted_file.txt
```

Both files produced the same SHA-256 digest:

```text
8acbe28ee8979cd7ff0b1d68ddcbed4a290031f93f1a6f29336409434071b454
```

| File | Verification Result |
| --- | --- |
| `test_file.txt` | Original reference file |
| `decrypted_file.txt` | Matching SHA-256 digest |
| Comparison | No content differences detected by hash comparison |

![AES decryption and SHA-256 file verification](evidence/02-decryption-integrity-verification.png)

*Figure 2 — OpenSSL AES-256-CBC decryption showing successful plaintext recovery and identical SHA-256 hashes for `test_file.txt` and `decrypted_file.txt`. The deprecated key-derivation warning remained present.*

Matching cryptographic hashes provide strong evidence that the recovered file was identical to the original during this test.

This verification was performed after decryption and should not be confused with authenticated encryption or automatic ciphertext-integrity protection.

#### Security Relevance

File encryption must be paired with reliable recovery procedures to ensure that protected information remains usable by authorized users.

Verifying decrypted output helps identify incorrect secret material, corrupted data, and unexpected changes during the encryption/decryption workflow.

However, AES-CBC encryption does not independently provide authenticated integrity protection. The successful file comparison demonstrates recovery in this controlled exercise, not resistance to malicious ciphertext modification.

#### Analyst Assessment

**Assessment: Original plaintext successfully recovered and verified through matching SHA-256 hashes.**

The decryption operation produced the expected plaintext, and SHA-256 comparison confirmed that the recovered file matched the original.

The repeated key-derivation warning reinforced the need to improve the encryption workflow using PBKDF2 in the independent investigation.