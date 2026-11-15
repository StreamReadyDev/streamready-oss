# Security Policy

StreamReady runs as Administrator and changes system-wide Windows settings, so security issues here matter more than in most hobby projects. If you've found one, please report it privately rather than opening a public issue.

## Reporting a vulnerability

Use GitHub's private vulnerability reporting:

1. Go to the **Security** tab of this repository.
2. Click **Report a vulnerability**.
3. Describe the issue, how to reproduce it, and its potential impact.

This opens a private conversation with the maintainer: the issue won't be public until a fix is ready (if ever needed).

If GitHub's private reporting isn't available for some reason, open a regular issue asking the maintainer to contact you, without any details of the vulnerability itself.

## What counts as a security issue here

- A tweak that changes a setting other than what it claims to, or that an attacker could exploit (e.g. a registry write with an injectable path/value).
- A download that isn't properly verified before being executed.
- Anything that could let StreamReady be used to persist malicious code (e.g. a scheduled task mechanism that could be hijacked).
- A file or scheduled task that runs with administrator rights from a place an ordinary program can change (StreamReady keeps these in `C:\ProgramData\StreamReady-OSS`, which only Administrators and SYSTEM can change).
- Text read from the registry or from a device or folder name that ends up executed as part of an undo script.
- Credential handling issues (e.g. the Autologin feature's password storage).

## Release hashes and verifying a download

- Every release publishes the SHA-256 hash of the release zip and of `src/StreamReady.ps1` in `SHA256SUMS.txt` (attached to the release) and at the bottom of the release notes. How to check them is in the README (section about SmartScreen and antivirus warnings).
- If a hash from an official release page does not match the file you downloaded from the same page, treat it as a possible tampering problem and report it privately (see above). Do not run the file.
- What a hash proves: the file is the one that was published. It does not prove who published it. If the release page itself were compromised, the file and its hash could both be replaced. For a stronger check, compare the script hash with a second source: the hash of `src/StreamReady.ps1` from a fresh clone of the tagged release (`Get-FileHash src\StreamReady.ps1 -Algorithm SHA256`) must equal the script hash in `SHA256SUMS.txt`.
- The release is unsigned (no code signing certificate yet), so Windows SmartScreen or an antivirus may warn about it. That is a known limit, not a vulnerability. Please do report a clear false positive from a named antivirus product as an ordinary issue.
- Tools the app downloads are checked too: the USB polling tool is pinned to one exact file and SHA-256 hash, and Nvidia Profile Inspector relies on its digital signature (see `docs/TWEAKS.txt`, section about download verification).

## What's already known and intentional

- StreamReady requires Administrator: this is required for the registry/service/feature changes it makes, and is explained to the user before the UAC prompt.
- Several tweaks are genuinely high-risk by design (adding Defender exclusions for game folders, disabling VBS/Hyper-V, disabling CPU security mitigations). These are documented in `docs/TWEAKS.txt`, unticked by default, and require the user to explicitly opt in.
- The OpenSSH Server toggle opens port 22 for remote login: it shows a confirmation dialog explaining this before enabling, and has a matching disable button.
- Some undo steps restore Windows defaults rather than your exact earlier setting, some items need your own steps to be put back (see "What Undo covers and what it cannot" in the README), and a few paths are not yet tested end to end on a clean machine. These are listed under "Known limits" in the README and are not treated as vulnerabilities unless they can be abused by another program or person.
- A downloaded tool with a valid digital signature is accepted without checking who the publisher is, and Nvidia Profile Inspector is fetched from its latest release rather than a pinned file. Both are described in `docs/TWEAKS.txt`.
