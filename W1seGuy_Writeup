# TryHackMe — W1seGuy | CTF Writeup

**Category:** Cryptography  
**Platform:** TryHackMe  
**Author:** Landon Abro

---

## Overview

W1seGuy is a cryptography challenge centered on a custom XOR encryption scheme. The server XOR-encrypts a flag with a randomly generated 5-character key, hex-encodes the result, and sends it to you, then asks you to supply the encryption key to receive a second flag. The vulnerability is a **known-plaintext attack**: since all TryHackMe flags follow the format `THM{...}`, we already know part of the plaintext, which lets us directly recover the key.

---

## Recon: Reading the Source Code

The challenge provides the server-side Python source. The key lines:

res \= ''.join(random.choices(string.ascii\_letters \+ string.digits, k=5))

key \= str(res)

The key is **5 alphanumeric characters**, randomly generated per session.

for i in range(0, len(flag)):

    xored \+= chr(ord(flag\[i\]) ^ ord(key\[i % len(key)\]))

hex\_encoded \= xored.encode().hex()

Each byte of the flag is XOR'd with the corresponding byte of the key (cycling with `i % len(key)`), then the result is hex-encoded and sent to the client.

**The weakness:** XOR is symmetric — `ciphertext XOR key = plaintext` and `ciphertext XOR plaintext = key`. Since we know the flag starts with `THM{` and ends with `}`, we know 5 bytes of plaintext upfront — exactly the length of the key.

---

## Exploitation

### Step 1 — Connect to the server

ncat 10.66.168.22 1337

The server responds with the hex-encoded ciphertext:

This XOR encoded text has flag 1: 117a021c217453230925004a3b262531062c0c32045c3d5430297e360f043746365724374a00152c

What is the encryption key?

### Step 2 — Known-plaintext attack in CyberChef

Since the flag format is `THM{...}` (5 known characters including the closing `}`), I used CyberChef with the following recipe:

1. **From Hex** — decode the hex string to raw bytes  
2. **XOR** — key: `THM{}`, scheme: Standard

XOR-ing the ciphertext against the known plaintext prefix `THM{}` causes the first 5 output characters to be the key itself (since `ciphertext XOR plaintext = key`). The output began with `E20gQ` — the 5-character encryption key.

### Step 3 — Decrypt flag 1

Updated the CyberChef XOR key to `E20gQ` and re-baked:

THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}

**Flag 1 recovered.**

### Step 4 — Submit the key to get flag 2

Sent the key `E20gQ` back to the server:

Congrats\! That is the correct key\! Here is flag 2: THM{BrUt3\_ForC1nG\_XOR\_cAn\_B3\_FuN\_n0?}

**Flag 2 recovered.**

---

## Key Takeaways

- **XOR encryption is only as strong as its key secrecy.** When any part of the plaintext is predictable (like a known flag format), an attacker can recover the key directly — no brute force needed.  
- **Short, reused keys are dangerous.** A 5-character key cycling over a long plaintext leaks key bytes at every position the plaintext is known.  
- **CyberChef** is a powerful analyst tool for rapid cryptographic operations without writing custom code.  
- The real-world analog is attacking protocols with predictable headers — the same known-plaintext principle has broken historical ciphers and weak modern implementations alike.

---

*Tools used: ncat, CyberChef*  
