# Aegira Pro distribution

## Recommended repository model

Keep the Free source repository public if desired. Keep the Pro source repository private.

A copyright notice/proprietary license is useful, but it does not make publicly visible source secret. If the Pro source is pushed to a public GitHub repository, people can inspect and copy it even if the repository does not grant them a reuse license.

## Recommended customer distribution

Distribute Pro as a compiled release artifact rather than publishing the Pro source.

A normal installer can eventually be exposed at a stable URL such as:

`https://aegiralabs.io/install.sh`

A customer could then run:

`curl -fsSL https://aegiralabs.io/install.sh | sh`

The installer downloads the appropriate signed Aegira binary, installs it, and starts the service. The binary then requires a valid Pro entitlement.

Do not put private GitHub repository credentials, Composio project keys, billing secrets, or license-signing private keys in the installer script or binary.

## Better production protection

- Private Pro repository
- Signed release binaries
- Server-side license validation
- Short-lived/refreshable entitlement tokens
- License revocation support
- Checksums/signatures for downloaded binaries
- Rate limiting on license validation
- No payment credentials in Aegira
