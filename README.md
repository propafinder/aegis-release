# AEGIS Release Assets

This repository hosts public AEGIS-NDR release assets.

It is an asset-delivery repository only. Source code, architecture docs, issues, and development history live in the main project repository:

[github.com/propafinder/aegis](https://github.com/propafinder/aegis)

## Channels

| Channel | URL | Use |
|---|---|---|
| Stable latest | [`/releases/latest`](https://github.com/propafinder/aegis-release/releases/latest) | Linux server/client and macOS client install helpers |
| Versioned tags | [`/releases`](https://github.com/propafinder/aegis-release/releases) | Reproducible installs and audit |
| Android preview | Android pre-release tags | Manual VpnService testing only; not production |

Do not treat Android preview APKs as stable desktop/server release parity.

## Stable Linux Server Install

Run on the relay server:

```bash
curl -fsSL https://github.com/propafinder/aegis-release/releases/latest/download/install-server.sh \
  | sudo bash -s -- \
      --relay-address <SERVER_IP>:8443 \
      --server-name <SERVER_IP>
```

Verify:

```bash
systemctl is-active aegis-server
/usr/local/bin/aegis-server --version
```

## Issue a Client Bundle

Run on the server after installation:

```bash
sudo -u aegis /usr/local/bin/aegis-server \
  --config /etc/aegis/server/aegis-server.toml \
  bundle-token issue \
  --bundle-path /etc/aegis/server/client-bundle.tar.gz \
  --public-host <SERVER_IP> \
  --public-port 8443
```

Bundle URLs contain one-time secrets. Do not publish them.

## Stable Linux/macOS Client Install

Run on the client:

```bash
curl -fsSL https://github.com/propafinder/aegis-release/releases/latest/download/bootstrap-install.sh \
  | bash -s -- client --bundle-url '<BUNDLE_URL>'
```

macOS foreground mode:

```bash
curl -fsSL https://github.com/propafinder/aegis-release/releases/latest/download/bootstrap-install.sh \
  | bash -s -- client --bundle-url '<BUNDLE_URL>' --service-mode foreground
```

macOS LaunchDaemon mode:

```bash
curl -fsSL https://github.com/propafinder/aegis-release/releases/latest/download/bootstrap-install.sh \
  -o /tmp/aegis-bootstrap-install.sh

sudo bash /tmp/aegis-bootstrap-install.sh client \
  --bundle-url '<BUNDLE_URL>' \
  --service-mode launchdaemon \
  --service-user aegis \
  --user-home /Users/aegis
```

## Verify Client Egress

```bash
curl -4 --socks5-hostname 127.0.0.1:1080 https://ifconfig.me
```

Expected result: the relay/server public IP or the documented provider egress IP for the proof being run.

## Android Preview APK

Android assets are preview builds for field testing. They are not Play Store builds, not production releases, and not proof of broad Android readiness.

Download from the Android pre-release tag:

[android-v0.1.2-poc-preview-20260527](https://github.com/propafinder/aegis-release/releases/tag/android-v0.1.2-poc-preview-20260527)

Example:

```bash
curl -L -o aegis-android.apk \
  https://github.com/propafinder/aegis-release/releases/download/android-v0.1.2-poc-preview-20260527/aegis-android-0.1.2-poc-preview.apk

shasum -a 256 aegis-android.apk
adb install -r aegis-android.apk
```

Expected SHA-256 for that APK:

```text
de69014bf7fa8674581a003bf5d3bfbd9c8a5adac8181b508f5b11ad19721410
```

Android preview scope:

- VpnService-based app testing.
- Existing-bundle enrollment.
- VPS bootstrap flow testing.
- Per-app UI and relay path validation.

Android preview non-goals:

- No production release claim.
- No Play Store claim.
- No broad OEM/device matrix claim.
- No guarantee that every Android app/protocol is captured.

## Common Release Assets

| Asset | Purpose |
|---|---|
| `install-server.sh` | Stable server installer |
| `bootstrap-install.sh` | Stable client installer |
| `release.manifest.json` | Release metadata |
| `*.sha256` | Checksums |
| `*.deb`, `*.rpm`, archives | Packaged binaries when published for a tag |
| `*.apk` | Android preview APK when published on Android pre-release tags |

## Validate Latest Assets

```bash
curl -fsSIL https://github.com/propafinder/aegis-release/releases/latest/download/install-server.sh
curl -fsSIL https://github.com/propafinder/aegis-release/releases/latest/download/bootstrap-install.sh
curl -fsSL https://github.com/propafinder/aegis-release/releases/latest/download/release.manifest.json | jq .
```

## Status Boundaries

Stable release assets prove only the release channel they are published for. They do not by themselves prove:

- Cloudflare MASQUE/UDP through normal proxying.
- `legacy_tls` as HTTP-like camouflage.
- macOS NetworkExtension readiness.
- Android production readiness.
- Broad Windows DNS/UDP/IPv6 capture.
- Universal censorship bypass.

For evidence-backed project status, use the main repository:

[github.com/propafinder/aegis](https://github.com/propafinder/aegis)
