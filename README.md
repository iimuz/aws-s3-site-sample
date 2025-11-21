# AWS S3 Static Site Sample

A minimal sample project demonstrating how to deploy a static website to AWS using S3, CloudFront, and WAF with IP-based access control.

## Prerequisites

- Node.js: v20 or later
- AWS CLI: Configured with credentials
- AWS CDK: Installed globally (optional, can use npx)
- AWS Account: With appropriate permissions

## Getting Started

Install dependencies:

```bash
# Install all dependencies (both workspaces)
npm install

```

Configure iP restrictions.
Edit `infrastructure/lib/static-site-stack.ts` to set allowed IP addresses:

```sh
# ip v4 (最後に `/32` をつけて利用)
curl https://checkip.amazonaws.com
# ip v6 (最後に `/128` をつけて利用)
curl -6 https://api64.ipify.org
```

```typescript
const allowedIpAddresses: string[] = [
  "203.0.113.0/24", // Your office network
  "198.51.100.1/32", // Your home IP
];
```

Notes:

- Leave empty `[]` to allow all IPs (WAF still created but no restrictions)

Build frontend:

```bash
cd frontend
npm run build
```

This creates optimized static files in `frontend/dist/`.
Deploy infrastructure:

```bash
# Bootstrap CDK (first time only)
cd infrastructure
npx cdk bootstrap

# Deploy the stack
npm run deploy
```

Important:
The stack will be deployed to us-east-1 region (required for CloudFront + WAF).

Upload files to S3.
After deployment, CDK will output the bucket name. Upload your built files:

```bash
cd frontend
npm run deploy
```

Access your site.
The deployment outputs a CloudFront URL: `WebsiteURL = https://d111111abcdef8.cloudfront.net`
Open this URL in your browser.
If you configured IP restrictions, access will only work from allowed IPs.

## Cleanup

To avoid ongoing charges, delete the stack when done:

```bash
# Delete all files from S3 first
cd frontend
npm run destroy

# Destroy the stack
cd infrastructure
npm run destroy
```

Note: The stack is configured with `DESTROY` removal policy for sample purposes, so `cdk destroy` will delete the S3 bucket automatically.
