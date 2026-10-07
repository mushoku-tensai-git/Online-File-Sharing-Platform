# DropVault — Sovereign End-to-End Encrypted File Sharing

> **Frontier privacy in your hands.** Client-side AES-256-GCM encrypted, zero-custody, single-file ephemeral file transfer platform styled with the Mistral AI editorial design system.

---

## Overview

**DropVault** is a standalone, client-side web application for secure, private file transfers. Files are sealed directly inside the browser memory before generating ephemeral retrieval links and transfer codes.

Built with a **zero-server custody** architecture, data is never transmitted to an untrusted central server in plaintext. All encryption, key derivation, integrity hashing, and decryption occur strictly on the user's client using the standardized W3C Web Crypto API.

<!-- STREAMING_CHUNK:Documenting core features and capabilities... -->

## Key Features

- **Client-Side AES-256-GCM Encryption**: Payloads are secured using 256-bit AES in Galois/Counter Mode with unique 96-bit initialization vectors (IV) and 128-bit salts.
- **PBKDF2 Key Derivation**: User passphrases derive cryptographically strong keys using PBKDF2 with 100,000 iterations of SHA-256.
- **Zero Server Custody**: All cryptographic operations occur in browser memory; no backend server retains unencrypted records.
- **Configurable Expiry Lifecycle**: Choose retention windows ranging from 10 minutes, 1 hour, 24 hours, 7 days, to permanent retention.
- **One-Time Self-Destruct ("Burn After Download")**: Automatically wipe and purge files from storage after the first successful download.
- **SHA-256 Tamper Verification**: Every payload produces a SHA-256 fingerprint displayed to both sender and receiver for bit-level integrity verification.
- **In-Browser Multi-Format Preview**: Inspect decrypted assets safely without external viewers:
  - **Images**: PNG, JPG, JPEG, GIF, WebP, SVG
  - **Video**: MP4, WebM, OGG
  - **Audio**: MP3, WAV, OGG, M4A with native player controls
  - **Documents & Code**: Markdown, Plain Text, JSON, JavaScript, HTML, CSS, Python, CSV
  - **PDFs**: Embedded in-browser sandbox rendering
- **Triple-Option Retrieval Routing**:
  1. Direct URL hash link (`#file=DV-XXXX`)
  2. 6-Character alphanumeric transfer code (`DV-XXXX`)
  3. Dynamic QR code generation for instant mobile phone transfer
- **Offline & Sandbox Resiliency**: Dual-tier storage leveraging IndexedDB (`DropVaultDB_Mistral`) with transparent fallbacks to an in-memory runtime store and `BroadcastChannel` cross-tab synchronization.
- **Editorial Mistral AI Aesthetic**: Designed per Mistral AI's brand manual with near-serif headline typography, warm cream surfaces, sober geometry, and the signature multi-stop sunset stripe gradient.

<!-- STREAMING_CHUNK:Detailing cryptographic pipeline and security model... -->

## Cryptographic Architecture & Flow

```
+-------------------------------------------------------------------+
|                        UPLOAD / SEAL FLOW                         |
+-------------------------------------------------------------------+
  [ File Input ] ───────────────> ArrayBuffer (In-Memory)
                                         │
               ┌─────────────────────────┴─────────────────────────┐
               ▼                                                   ▼
     [ SHA-256 Digest ]                                   [ Password Provided? ]
   (Integrity Fingerprint)                                         │
                                                    ┌──────────────┴──────────────┐
                                                    ▼ (Yes)                       ▼ (No)
                                           [ PBKDF2 Derivation ]            Raw ArrayBuffer
                                           • 100,000 Iterations                   │
                                           • 16-byte random salt                  │
                                                    │                             │
                                                    ▼                             │
                                           [ AES-256-GCM Cipher ]                 │
                                           • 12-byte random IV                    │
                                                    │                             │
                                                    ▼                             │
                                          Ciphertext ArrayBuffer                  │
                                                    │                             │
                                                    └──────────────┬──────────────┘
                                                                   ▼
                                                       [ Package Storage ]
                                                  • IndexedDB / Memory Vault
                                                  • 6-Char DV-Code Generated
                                                  • Ephemeral Hash Link Created

+-------------------------------------------------------------------+
|                       RECEIVE / DECRYPT FLOW                      |
+-------------------------------------------------------------------+
  [ Link / Code Lookup ] ───────> Fetch Package Record
                                         │
                              [ Password Challenge? ]
                                         │
                     ┌───────────────────┴───────────────────┐
                     ▼ (Yes)                                 ▼ (No)
             User Passphrase                         Pass directly to
                     │                               Object URL / Blob
                     ▼                                       │
           [ Derive Key + Salt ]                             │
                     ▼                                       │
           [ AES-GCM Decrypt ]                               │
                     │                                       │
                     └───────────────────┬───────────────────┘
                                         ▼
                             [ Decrypted Buffer Ready ]
                                         │
                       ┌─────────────────┴─────────────────┐
                       ▼                                   ▼
              [ In-Browser Preview ]              [ File Download ]
                                                           │
                                                [ Burn Check Active? ]
                                                           │
                                                           ▼
                                                Permanent Vault Purge
```

<!-- STREAMING_CHUNK:Outlining design system specifications and tokens... -->

## Mistral AI Design System Implementation

The application adheres strictly to the **Mistral AI Design Specification** (`design.md`):

| Token Category | Value / Spec | Role in DropVault |
|---|---|---|
| **Display Font** | `Newsreader` (Editorial serif) | Hero title, page headlines, card display headers |
| **Interface Font** | `Inter` (Geometric sans) | Form labels, descriptions, button text |
| **Monospace Font**| `JetBrains Mono` | SHA-256 fingerprints, transfer short codes |
| **Mistral Orange**| `#F54A00` (`primary`) | Action CTAs, primary interactive highlights |
| **Cream Surfaces**| `#F7F3E8`, `#FAF7F0` | Form cards, hero backdrop, dialog panels |
| **Beige Hairlines**| `#E2D9C5`, `#E7E3D8` | 1px border dividers and card boundaries |
| **Dark Canvas** | `#111215`, `#121316` | Promo bar, syntax code previews, checksum badges |
| **Sunset Stripe** | `#F54A00` → `#FA7A1E` → `#F99D26` → `#FFC83B` → `#F7F3E8` | Signature horizontal gradient band above footer |
| **Border Radius** | `8px` (`rounded-md`) / `12px` (`rounded-lg`) | Sober editorial geometry (no pill buttons) |

<!-- STREAMING_CHUNK:Providing quickstart and deployment instructions... -->

## Quick Start & Installation

Because DropVault is built as a **Single-File Web Application**, no build steps, Node.js installations, or bundlers are required.

### Method 1: Local Direct Launch
1. Clone or download `index.html` to your local system.
2. Double-click `index.html` to open it directly in any modern browser (Chrome, Firefox, Safari, Edge, Brave).

### Method 2: Local HTTP Server (Recommended)
Running through a lightweight local web server ensures optimal Web Crypto API and IndexedDB access:

```bash
# Using Python 3
python -m http.server 8080

# Using Node.js npx
npx serve .

# Using PHP
php -S localhost:8080
```
Then navigate to: `http://localhost:8080/index.html`

### Method 3: Static Web Hosting
DropVault can be hosted instantly without server-side configuration on:
- **GitHub Pages**
- **Cloudflare Pages**
- **Vercel / Netlify**
- **AWS S3 / CloudFront**

<!-- STREAMING_CHUNK:Summarizing browser compatibility and license notes... -->

## User Manual

### 1. Sending a File
1. Drag and drop any file into the sealing drop zone (or click **Browse Storage** / **Try Sample File**).
2. *(Optional)* Toggle **Protect with Secret Password** and enter an encryption passphrase.
3. Choose an **Expiration Lifecycle** (10m, 1h, 24h, 7d, or Permanent).
4. *(Optional)* Enable **Burn After Download** for single-use self-destruction.
5. Click **Create Secure Transfer**.
6. Share via the generated **Direct Retrieval URL**, 6-character **Transfer Code**, or by letting the recipient scan the **QR Code**.

### 2. Receiving a File
1. Open the shareable URL directly (which automatically populates `#file=DV-XXXX`), or navigate to the **Receive** tab and enter the 6-character code.
2. If encrypted, provide the required passphrase.
3. Click **Preview in Browser** to view content immediately, or click **Decrypt & Download** to save the verified payload directly to disk.

---

## Technical Specifications

- **File Format**: Single self-contained HTML file (`index.html`)
- **Styling**: Tailwind CSS CDN with custom Mistral color extensions
- **Icons**: Lucide Icons CDN (`lucide@latest`)
- **QR Engine**: QRCode.js (`1.0.0`)
- **Audio Feedback**: Procedural Web Audio API sound synthesizer (zero external audio files)
- **Security Protocols**: W3C Web Cryptography API (`SubtleCrypto`), SHA-256, PBKDF2, AES-GCM

---

## Security Notice

- **Passphrase Recovery**: Because DropVault uses zero-knowledge cryptography, passphrases are never stored or recoverable. If a passphrase is lost, the payload cannot be decrypted.
- **Context Security**: The Web Cryptography API requires a Secure Context (`HTTPS` or `localhost`). When hosting for production, ensure SSL/TLS is active.

---

## License

MIT License &copy; DropVault Project. Open-source sovereign software.
