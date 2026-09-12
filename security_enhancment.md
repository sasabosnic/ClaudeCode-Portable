Software Requirements Specification (SRS): Portable Claude Code with Hardware-Backed YubiKey Authentication
1. Introduction & Overview
 * Project Objective: Implement a fully portable, secure instance of Claude Code running from removable storage (USB drive) with cryptographic authentication bound to a physical YubiKey hardware token.
 * Core Security Goal: Prevent plaintext credential storage on disk and enforce physical user interaction (PIN and touch) for every session authentication lifecycle.
2. Functional Requirements
 * FR-1: Portable GnuPG Environment
   * The USB drive must contain self-contained or portable GnuPG binaries (gpg, gpg-agent, scdaemon) to eliminate host-machine software dependencies.
   * The GPG home directory (GNUPGHOME) must be isolated strictly within the portable directory structure (e.g., ./.gnupg).
 * FR-2: Hardware-Backed Credential Storage
   * Anthropic API keys must be symmetrically encrypted using the user's YubiKey OpenPGP application (api_key.gpg) and stored locally on the portable drive.
   * Decryption must require the YubiKey's hardware PIN and a physical touch gesture.
 * FR-3: Native API Key Helper Integration
   * The portable Claude Code configuration (.claude/settings.json) must utilize the apiKeyHelper directive pointing to a local execution script (yubikey-helper.sh).
   * The helper script must automatically invoke the portable GPG runtime, trigger the hardware YubiKey prompt, and output the decrypted API key dynamically to standard output upon successful authentication.
 * FR-4: Environmental Isolation & Memory Management
   * Decrypted API keys must reside exclusively in transient memory during session execution and be scrubbed upon termination.
   * Environment configuration scripts must override system-wide paths to ensure zero footprint is left on the host machine.
3. Non-Functional & Security Requirements
 * NFR-1: Integrity Verification
   * The portable container must include a startup pre-check script that validates cryptographic checksums (SHA-256) of binaries and scripts prior to execution.
 * NFR-2: Host Compatibility
   * The toolchain must interface seamlessly with native host smartcard daemons (pcscd on Linux/macOS, SCardSvr on Windows) without requiring administrative driver installations.
 * NFR-3: Supply-Chain & Network Hardening
   * All wrapper dependencies and helper scripts must have locked versions, and network communications must be restricted exclusively to verified Anthropic API endpoints.
