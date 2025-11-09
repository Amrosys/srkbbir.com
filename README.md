# سرك ببير - Sirkbbir

**[Read in Arabic (اقرأ بالعربية)](README.ar.md)**

# Sirkbbir - Share Secrets Securely

![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jhaals/yopass?sort=semver)

Sirkbbir is an Arabic-first secret sharing platform based on Yopass, designed for sharing secrets in a quick and secure manner.
The sole purpose of Sirkbbir is to minimize passwords floating around in ticket management systems, Slack messages, and emails. Messages are encrypted/decrypted locally in the browser and sent to Sirkbbir without the decryption key, which is only visible once during encryption. Sirkbbir then returns a one-time URL with a specified expiry date.

**Available at [sirkbbir.com](https://sirkbbir.com)**

- End-to-End encryption using [OpenPGP](https://openpgpjs.org/)
- Secrets can only be viewed once
- No accounts or user management required
- Secrets self destruct after X hours
- Custom password option
- Limited file upload functionality
- **Full Arabic language support with RTL interface**

## Features

- **Arabic-First Interface**: Complete Arabic translation with right-to-left (RTL) support
- **End-to-End Encryption**: Encryption and decryption happen locally in your browser
- **Self-Destruction**: Messages have a fixed lifetime and are deleted automatically
- **One-time Downloads**: Encrypted messages can only be downloaded once
- **Simple Sharing**: Generate unique one-click links for encrypted files or messages
- **No Accounts**: Quick and easy sharing without registration
- **Open Source**: Built on open source software for full transparency

## Installation / Configuration

### Docker Compose

Use the Docker Compose file in `deploy/docker-compose/` directory to set up a Sirkbbir instance.

```console
cd deploy/docker-compose/insecure
docker-compose up -d
```

### Docker

Without TLS encryption (needs a reverse proxy for transport encryption):

```console
docker run --name memcached_sirkbbir -d memcached
docker run -p 127.0.0.1:80:1337 --link memcached_sirkbbir:memcached -d jhaals/yopass --memcached=memcached:11211
```

Then point your reverse proxy that handles TLS connections to `127.0.0.1:80`.

## Technology Stack

- **Frontend**: React with TypeScript, Vite, TailwindCSS
- **Backend**: Go
- **Database**: Redis or Memcached
- **Encryption**: OpenPGP.js

## Development

```bash
# Install dependencies
cd website
yarn install

# Run development server
yarn dev

# Build for production
yarn build
```

## Server Configuration

Here are the server configuration options.

Command line flags:

```console
$ yopass-server -h
      --address string             listen address (default 0.0.0.0)
      --database string            database backend ('memcached' or 'redis') (default "memcached")
      --max-length int             max length of encrypted secret (default 10000)
      --memcached string           Memcached address (default "localhost:11211")
      --metrics-port int           metrics server listen port (default -1)
      --port int                   listen port (default 1337)
      --redis string               Redis URL (default "redis://localhost:6379/0")
      --tls-cert string            path to TLS certificate
      --tls-key string             path to TLS key
      --cors-allow-origin          Access-Control-Allow-Origin CORS setting (default *)
      --force-onetime-secrets      reject non onetime secrets from being created
      --disable-upload             disable the /file upload endpoints
```

Encrypted secrets can be stored either in Memcached or Redis by changing the `--database` flag.

## License

This project is based on [Yopass](https://github.com/jhaals/yopass) and maintains the same open source license.

## Credits

Based on the excellent [Yopass](https://github.com/jhaals/yopass) project by Johan Haals and contributors.

Modified and localized for Arabic-speaking users as Sirkbbir.
