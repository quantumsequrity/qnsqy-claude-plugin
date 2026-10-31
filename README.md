# QNSQY for Claude

QNSQY is a post-quantum cryptography (PQC) toolkit from Quantum Sequrity. This
plugin lets Claude Code use the QNSQY program installed on your own computer.
With it, Claude can:

*   find quantum-vulnerable cryptography and write a CBOM (cryptography bill of
    materials);
*   encrypt files for a recipient's public key with ML-KEM;
*   export your public key;
*   check ML-DSA signatures, QNSQY release signatures, hashes and encrypted
    files.

The plugin's tools have no field for a password, passphrase, private key, API
key or payment token, refuse any call that adds one, and never return one.

## Requirements

*   **Linux** (x86_64 or ARM64) with QNSQY's current release installed from our
    signed packages at https://quantumsequrity.com/download, so that `qnsqy` is
    on your `PATH`. The plugin starts whatever `qnsqy` your `PATH` finds first,
    so install QNSQY only from there.
    Windows and macOS are not supported by the plugin yet.
*   **Claude Code** on the same computer. In claude.ai chat the plugin has no
    QNSQY tools, because chat cannot start programs on your computer. Cowork has
    not been tested.
*   **A QNSQY account.** Sign in with the QNSQY app, or with `qnsqy login` in
    your own terminal outside Claude.

## Install

In your shell:

```
claude plugin marketplace add quantumsequrity/qnsqy-claude-plugin
claude plugin install qnsqy@quantumsequrity
```

Then start a new Claude Code session, or run `/reload-plugins`.

Using Codex? Install our Codex plugin instead:
https://github.com/quantumsequrity/qnsqy-codex

If you added QNSQY to Claude Code yourself before, remove that entry with
`claude mcp remove <its name>`, so that Claude sees only this plugin's tools.
It is an MCP server that runs `qnsqy --mcp`, and `claude mcp list` shows its
name.

## What the plugin runs, sends and fetches

*   **Runs**: Claude Code starts your installed QNSQY as a local tool server
    with `qnsqy --mcp-profile=strict`. Each tool call runs the matching QNSQY
    command on your computer, inside QNSQY's sandbox.
*   **Tools**: Claude sees 13 tools (10 on the Free plan):
    *   scan;
    *   encrypt and batch encrypt;
    *   export a public key;
    *   verify, batch verify and verify a release;
    *   hash, check a hash, and check an encrypted file;
    *   status, usage and version.

    Batch encrypt, batch verify and key export need the Pro plan or above.
*   **Sends**: the plugin itself sends nothing. QNSQY talks to our billing
    server, `billing.quantumsequrity.com`, to check your plan and record usage,
    as section 2 of our [privacy policy](https://quantumsequrity.com/privacy)
    describes:
    *   **At sign-in and at each check-in**: a device ID derived on your
        device, your computer's name and the operating system type.
    *   **Before a metered operation**: the operation type, the data size, the
        program version and the chosen algorithms.
    *   **After a metered operation**: whether it succeeded, the bytes processed
        and how long it took.
    *   **For every operation**: a usage receipt with the operation type, data
        size, time, result, credits used and a hash of the file's path. The path
        itself is not sent, but a path that is easy to guess can be checked
        against its hash.
    *   **Audit forwarding**: if you have set up audit log forwarding in
        QNSQY's settings, audit events also go to the syslog server you
        configured.

    QNSQY never sends your files, their contents, your passwords or your keys.
*   **Environment**: QNSQY's commands receive only these QNSQY variables:
    *   `QNSQY_API_KEY`, `QNSQY_CONFIG_DIR`, `QNSQY_LOG_LEVEL`,
        `QNSQY_REQUIRE_INTEGRITY` and `QNSQY_LICENSE_DATA`, if you have set
        them;
    *   `QNSQY_MACHINE_KEY`, which QNSQY sets itself for its own commands.

    In this plugin they never receive a payment token or remote-storage keys.
*   **Claude sees** what you ask and what the tools return: file names, paths
    and results. All of this is part of your conversation with Claude, which is
    why the tools never take secrets.
*   **Fetches**: nothing. The plugin downloads no code.

## Passwords, passphrases and keys

*   **No secret fields.** The tools have no field for a password, passphrase,
    private key, API key or payment token. QNSQY refuses any call that adds one
    before doing anything.
*   **Encryption.** Encryption in the plugin is to a public key file.
*   **Steps that need a password.** To decrypt, sign, create keys or sign in,
    use the QNSQY app (from your desktop menu, or type `! qnsqy --gui` in
    Claude Code), or run `qnsqy` in your own terminal. QNSQY asks for the
    password in its own window or prompt. Create your account at
    https://quantumsequrity.com or in the app, and manage API keys in your
    dashboard.

## Limits

*   **Tool calls can be steered.** Text that Claude reads, for example inside a
    scanned file or a web page, can try to steer it. The strict tools refuse
    unlisted fields, including every secret field, and cannot overwrite or
    delete files. A valid call is still
    possible, such as encrypting a file to the wrong public key. Check each
    QNSQY call before you approve it. Whether Claude Code asks you first depends
    on your permission settings.
*   **Claude's own shell is outside the plugin.** Claude Code can also run
    `qnsqy` in its shell. A password on such a command line, for example one
    piped into `--password-stdin`, reaches the chat. Never approve a `qnsqy`
    command that carries a password.
*   **Typed secrets.** If you type a password into the chat itself, it is part of
    the conversation, so don't.

## Example prompts

1.  "Scan ~/projects/billing for quantum-vulnerable cryptography and write a
    CycloneDX CBOM to cbom.json."
2.  "Encrypt report.pdf for the public key in alice-enc.pub."
3.  "Export my public key named work to work.pub so that I can send it to a
    colleague."
4.  "Check the signature on contract.qs with bob-sign.pub, then hash
    invoice.pdf with BLAKE3."
5.  "Which QNSQY plan am I on, and how much have I used?"

## Troubleshooting

*   **No QNSQY tools appear, or the server failed to start.** One of these is
    the cause:
    *   QNSQY is not installed;
    *   `qnsqy` is not on your `PATH`;
    *   your QNSQY is older than the current release. Older releases do not know
        the plugin's option, so they stop.

    Install the current release from https://quantumsequrity.com/download, then
    run `/reload-plugins`. On Windows and macOS the plugin is not available yet.
*   **"Not logged in"** or **"Login required"**: sign in with the QNSQY app,
    or with `qnsqy login` in your own terminal. Scanning needs a signed-in
    account, on every plan.
*   **A plan limit message**: some algorithms, and the batch and key-export
    tools, need the Pro or Business plan (https://quantumsequrity.com/pricing).
*   **"Not accepted in the strict profile"**: the plugin never accepts passwords
    or keys. Finish that step in the QNSQY app or in your own terminal.
*   **The QNSQY app does not open on a desktop that has only X11**: the app needs
    Wayland. It opens on X11 only with `--allow-x11`, which we do not
    recommend, because X11 lets other programs read keystrokes. Use the matching
    `qnsqy` command in your own terminal instead, for example `qnsqy decrypt`.

## Security and support

*   **Certifications**: QNSQY implements the FIPS 203 (ML-KEM) and FIPS 204
    (ML-DSA) algorithms. It is not FIPS 140-validated, SOC 2 certified, FedRAMP
    authorized or HIPAA-audited.
*   **Support**: support@quantumsequrity.com
*   **Security reports**: security@quantumsequrity.com
    (https://quantumsequrity.com/security)
*   **Privacy policy**: https://quantumsequrity.com/privacy
*   **Terms**: https://quantumsequrity.com/terms

## Licence

See [LICENSE](LICENSE).
