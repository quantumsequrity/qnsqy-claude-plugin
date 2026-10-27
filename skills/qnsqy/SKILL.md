---
name: qnsqy
description: Use when the user asks to use QNSQY or a .qs file, or asks Claude to scan their files for quantum-vulnerable cryptography or a CBOM, encrypt files to a public key, export a public key, check signatures, hashes or encrypted files, or decrypt, sign, create keys or sign in with QNSQY on their own computer.
---

# QNSQY

QNSQY is a post-quantum cryptography toolkit from Quantum Sequrity. It
implements the FIPS 203 (ML-KEM) and FIPS 204 (ML-DSA) algorithms, but it is
not FIPS 140-validated, SOC 2 certified, FedRAMP authorized or HIPAA-audited,
so never say or suggest that it is.

## Where the tools work

The QNSQY tools come from the QNSQY program on the user's own computer.

*   **Where they work today**: Claude Code on a Linux computer that has
    QNSQY's current release installed.
*   **Where they do not work**: claude.ai chat, because chat cannot start
    programs on the user's computer. They do not work on Windows or macOS yet.
    In those places, tell the user so, and point them to
    https://quantumsequrity.com/download.

## Rules

1.  **Use only this plugin's QNSQY tools.** Their names start with
    `mcp__plugin_qnsqy_qnsqy__`. Never call a QNSQY tool from another server
    that takes a password, passphrase or payment token.
2.  **Never ask for, accept or pass on a secret.** A secret is a password,
    passphrase, private key, API key or payment token. Never ask for one,
    accept one, repeat one or pass one on, whether in a tool call, a command or
    a message. If the user offers one:
    *   do not use it;
    *   ask them to keep it out of the chat;
    *   give the matching hand-off from rule 4.
3.  **Never put a file name inside a command.** You may name a file in a
    sentence, but never inside a command.
4.  **Hand-offs.** Use these fixed words:
    *   **Decrypt**: "Open the QNSQY app from your desktop menu, or type
        `! qnsqy --gui` here, and choose the file in the app, or run
        `qnsqy decrypt` in your own terminal outside Claude. QNSQY asks for the
        password in its own window or prompt."
    *   **Sign**: "Sign it in the QNSQY app, or run `qnsqy sign` in your own
        terminal outside Claude."
    *   **Create a key pair**: "Create it in the QNSQY app, or run
        `qnsqy keygen-enc` (for encryption) or `qnsqy keygen-sign` (for
        signing) in your own terminal outside Claude."
    *   **Sign in**: "Sign in with the QNSQY app, or run `qnsqy login` in your
        own terminal outside Claude."
    *   **Create an account or an API key**: "Create the account at
        https://quantumsequrity.com or in the QNSQY app, and manage API keys in
        your dashboard at https://quantumsequrity.com/dashboard."
    *   **Anything else that needs a password**: "This needs a password, so
        use the QNSQY app or your own terminal for it." This covers password
        encryption, the vault, rekey, deniable containers, time-lock, stego,
        polyglot files, migrate, threshold decryption, and ABE authority and
        user keys.

## Which tool for which task

The tools you see depend on the user's plan and on their QNSQY build, so rely
on the tool list that you actually have.

*   **Find quantum-vulnerable cryptography or make a CBOM**: the scan tool. Use
    `format: cyclonedx` for a CycloneDX 1.6 CBOM. It does not overwrite an
    existing report file.
*   **Encrypt for someone**: use the encrypt tool for one file and the batch
    encrypt tool for several files (Pro and above). Set `recipient` to the
    recipient's public key file.
*   **Share my public key**: the key export tool (Pro and above). It writes the
    public key to a new file.
*   **Check what I received**:
    *   **A signed `.qs` file and the sender's public key**: verify.
    *   **Several signed files**: batch verify (Pro and above).
    *   **A QNSQY release file and its `.sig` file**: verify release, which
        checks against the release key built into QNSQY.
    *   **Hashes**: hash and hash verify.
    *   **An encrypted `.qs` file, checked without a password**: verify
        integrity.
*   **Check my setup**:
    *   **Account and plan**: status.
    *   **Credits**: usage.
    *   **The program**: version.

## Troubleshooting

*   **No QNSQY tools appear.** One of these is the cause:
    *   QNSQY is not installed, or it is not on `PATH`;
    *   QNSQY is older than the current release, which stops on the plugin's
        option;
    *   the computer runs Windows or macOS, which the plugin does not support
        yet.

    On Linux, the user installs the current release from
    https://quantumsequrity.com/download and then runs `/reload-plugins`.
*   **"Not logged in"** or **"Login required"**: the user signs in with the
    QNSQY app, or with `qnsqy login` in their own terminal. Scanning needs a
    signed-in account on every plan.
*   **A plan limit message**: some algorithms and tools need Pro or Business.
*   **"Not accepted in the strict profile"** or **"Required in the strict
    profile"**: fix the call. Never add a password; give a hand-off instead.
*   **The QNSQY app does not open on a desktop that has only X11**: the app
    needs Wayland. It opens on X11 only with `--allow-x11`, and QNSQY does not
    recommend that, because X11 lets other programs read keystrokes. Suggest the
    matching `qnsqy` command in the user's own terminal instead.
