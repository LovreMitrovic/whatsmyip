# whatsmyip

A small Bash utility for quickly displaying your private or public IP address, with an optional QR code so you can scan it from your phone — handy for testing local dev servers on mobile.

## Features

- Show your **private** (LAN) IP address
- Show your **public** (WAN) IP address
- Render a scannable **QR code** in the terminal
- Optionally **append a port** to the URL (e.g. `http://192.168.1.42:3000`)
- Validates the IP before generating a QR code

## Requirements

- `bash`
- [`qrencode`](https://fukuchi.org/works/qrencode/) — for QR code output
- `curl` — for fetching the public IP
- `iproute2` (`ip` command) — for resolving the private IP on Linux (macOS uses the built-in `route` / `ipconfig`)

Install dependencies on Debian/Ubuntu:

```bash
sudo apt install qrencode curl iproute2
```

On macOS (with Homebrew):

```bash
brew install qrencode
```

## Installation

Save the script as `whatsmyip`, make it executable, and place it in your `PATH`:

```bash
sudo mv whatsmyip /usr/local/bin/whatsmyip
sudo chmod +x /usr/local/bin/whatsmyip
```

You should now be able to run `whatsmyip` from anywhere.

## Usage

```
whatsmyip private [--no-qrcode] [--append-port PORT]
whatsmyip public  [--no-qrcode] [--append-port PORT]
whatsmyip --help
```

### Commands

| Command   | Description                |
|-----------|----------------------------|
| `private` | Show your private (LAN) IP |
| `public`  | Show your public (WAN) IP  |

### Options

| Option                | Description                              |
|-----------------------|------------------------------------------|
| `--no-qrcode`         | Disable QR code output                   |
| `--append-port PORT`  | Append a port to the URL (e.g. `3000`)   |
| `--help`, `-h`        | Show usage information                   |

## Examples

Show your private IP with a QR code:

```bash
whatsmyip private
```

Show your private IP with a QR code pointing to a dev server on port 3000:

```bash
whatsmyip private --append-port 3000
```

Show your public IP without a QR code:

```bash
whatsmyip public --no-qrcode
```

## How it works

- **Private IP** is the source IP your machine would use to reach the internet. On Linux it's resolved via `ip -4 route get 1.1.1.1`; on macOS, `route -n get 1.1.1.1` finds the outbound interface and `ipconfig getifaddr` reads its address.
- **Public IP** is fetched from `ifconfig.me`, falling back to `ipinfo.io/ip` if the first request fails or times out (1 second).
- The QR code encodes a `http://<ip>[:port]` URL, suitable for scanning with a phone to open the address in a browser.

## Notes

- If the public IP lookup fails on both endpoints, the script prints `unknown` and skips the QR code.
- The IP validation step ensures `qrencode` is only called with a well-formed IPv4 address.

## License

MIT
