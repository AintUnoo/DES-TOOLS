# DES Cipher 🔐💻

DES Cipher is a from-scratch, browser-based implementation of the **Data Encryption Standard (DES)**, built for a Cryptography & Computer Network Security course. Every permutation table, S-box, and Feistel round is hand-coded in Python — no cryptographic libraries are used — and the entire cipher runs client-side in the browser via WebAssembly.

## 🎯 Key Features

- **Full DES Implementation**: Initial Permutation (IP), Final Permutation (FP), Expansion (E), Permutation (P), PC-1/PC-2 key permutations, and all 8 S-boxes coded from first principles.
- **16-Round Feistel Network**: Complete round-by-round encryption and decryption pipeline.
- **Encrypt & Decrypt**: Supports arbitrary-length UTF-8 text in both directions.
- **PKCS#5 Padding**: Correctly handles input of any length by padding to 8-byte blocks.
- **ECB Mode**: Multi-block support for full-length messages, not just single 64-bit blocks.
- **Key Management**: Generate a cryptographically random 64-bit key, copy it, or export it as a `.txt` file.
- **Step-by-Step Trace View**: Inspect the cipher's internal state at every stage — permutations, key schedule, individual Feistel rounds, and final output.
- **Input Validation**: Clear, descriptive error messages for invalid keys or malformed ciphertext.

## 🔒 Cipher Architecture

**1. Key Schedule**
- The 64-bit hex key is permuted via **PC-1**, discarding 8 parity bits to leave a 56-bit key.
- The result is split into two 28-bit halves (C and D).
- Across 16 rounds, both halves are left-rotated per a fixed shift schedule and recombined via **PC-2** to produce sixteen unique 48-bit subkeys.

**2. Block Processing**
- The 64-bit plaintext block passes through the **Initial Permutation (IP)**, splitting it into 32-bit L and R halves.
- The block is processed through **16 Feistel rounds**: each round expands R to 48 bits (E), XORs it with the round subkey, substitutes it through the 8 S-boxes, and permutes the result (P).
- After round 16, the halves are swapped and passed through the **Final Permutation (FP)** to produce the ciphertext.

**3. Decryption**
- Decryption reuses the identical encryption pipeline with the 16 subkeys applied in **reverse order** — a defining property of the Feistel structure.

**4. Multi-Block Handling**
- Plaintext is UTF-8 encoded and PKCS#5 padded to a multiple of 8 bytes, then split into 64-bit blocks and encrypted independently in **ECB mode**.

## 🧮 Algorithm Matrix

| Component | Purpose | Implementation Detail |
|---|---|---|
| Initial / Final Permutation | Bit diffusion at block boundaries | Fixed 64-bit lookup tables (IP, FP) |
| Expansion (E) | Widens R from 32 → 48 bits | Fixed expansion table per round |
| S-Boxes (×8) | Non-linear substitution (confusion) | 8 hardcoded 4×16 lookup tables per FIPS 46-3 |
| Permutation (P) | Bit diffusion after substitution | Fixed 32-bit permutation table |
| Key Schedule (PC-1 / PC-2) | Subkey derivation | 56-bit key split, rotated, and compressed per round |
| PKCS#5 Padding | Arbitrary-length input support | Byte-value padding to nearest 8-byte boundary |

## 🛠️ Tools & Technologies

- **Language**: Python (cipher logic), JavaScript (application logic)
- **Runtime**: Pyodide — executes Python natively in-browser via WebAssembly
- **Interface**: HTML / CSS
- **Cryptography Libraries**: None — every component implemented from scratch

## 🧪 Testing & Verification

- **Known-Answer Testing**: Verified encryption/decryption output against standard DES test vectors to confirm correctness of permutations, S-boxes, and the key schedule.
- **Round-Trip Testing**: Confirmed that encrypting then decrypting with the same key returns the original plaintext across varying message lengths.
- **Edge-Case Testing**: Validated behavior on empty input, non-hex characters, incorrect key length, and malformed/truncated ciphertext.
- **Padding Integrity**: Confirmed PKCS#5 padding is correctly applied and stripped, including detection of corrupted padding on decryption.

## 🚀 Usage

1. Open the application in a browser and wait for the Python runtime to initialize (a short one-time load).
2. Generate a random key, or enter your own 16-character hexadecimal key.
3. Select **Encrypt** or **Decrypt** mode.
4. Enter plaintext (or hex ciphertext) and click **Run DES**.
5. Copy or save the result, and optionally open **Show Steps** to inspect the full internal trace of the algorithm.

📦 Installation

Clone the repository: https://github.com/AintUnoo/DES-TOOLS.git

⚠️ Security Note

DES uses a 56-bit effective key length and is no longer considered cryptographically secure against modern brute-force attacks. This project is intended for educational purposes — to demonstrate the mechanics of a classical symmetric block cipher — and should not be used to protect real data.

📖 Reference

FIPS PUB 46-3, Data Encryption Standard (DES), National Institute of Standards and Technology.
