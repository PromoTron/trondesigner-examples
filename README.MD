# TronDesigner - Editor - Integration Examples

This repository contains integration examples for TronDesigner Editor.

## Requirements
1. Active TronDesigner license
2. Node.js 20+ and npm
3. Docker (optional, for containerized run)

## Required Environment Variables
The app requires these variables at startup:

- `TD_URL`
- `TD_URL_INTEGRATION`
- `TD_API_KEY`

Example values:

### Development
```env
TD_URL=https://app-dev.trondesigner.com
TD_URL_INTEGRATION=https://integration-dev.trondesigner.com
TD_API_KEY=<YOUR_DEV_API_KEY>
```

### Production
```env
TD_URL=https://app.trondesigner.com
TD_URL_INTEGRATION=https://integration.trondesigner.com
TD_API_KEY=<YOUR_PROD_API_KEY>
```

## Run Locally
```bash
cd src
npm ci
npm run run
```

Default app URL: `http://localhost:3000`

## Docker
Build image:
```bash
cd src
docker build -t trondesigner-examples .
```

Run container with env file:
```bash
docker run --rm -p 3000:3000 --env-file .env trondesigner-examples
```

Or run with explicit variables:
```bash
docker run --rm -p 3000:3000 \
  -e TD_URL=https://app-dev.trondesigner.com \
  -e TD_URL_INTEGRATION=https://integration-dev.trondesigner.com \
  -e TD_API_KEY=<YOUR_API_KEY> \
  trondesigner-examples
```
