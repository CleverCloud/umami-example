# Umami Analytics on Clever Cloud

[![Clever Cloud - PaaS](https://img.shields.io/badge/Clever%20Cloud-PaaS-orange)](https://clever-cloud.com)

This example shows how to deploy [Umami](https://umami.is/), an open-source, privacy-focused web analytics platform, on Clever Cloud from source using a Node.js runtime and a PostgreSQL add-on.

## About the Application

Umami is a simple, fast, privacy-focused alternative to Google Analytics. It uses:

- **Next.js** for the web application
- **Prisma** as the ORM with automatic migrations
- **PostgreSQL** as the database

### Features

- Privacy-focused analytics (no cookies, GDPR compliant)
- Real-time dashboard
- Visitor insights (pages, referrers, browsers, OS, devices, countries)
- Custom events tracking
- Multiple website support
- Lightweight tracking script (~2KB)

## Technology Stack

- [Node.js 18.18+](https://nodejs.org/) - JavaScript runtime
- [PostgreSQL 12.14+](https://www.postgresql.org/) - Database
- [pnpm](https://pnpm.io/) - Package manager

## Prerequisites

- A [Clever Cloud](https://clever-cloud.com) account
- [Clever Tools CLI](https://github.com/CleverCloud/clever-tools) installed
- [Git](https://git-scm.com/)

## Deploying on Clever Cloud

You have two options to deploy your application on Clever Cloud: using the Web Console or using the Clever Tools CLI.

### Option 1: Deploy using the Web Console

#### 1. Create an account on Clever Cloud

If you don't already have an account, go to the [Clever Cloud console](https://console.clever-cloud.com/) and follow the registration instructions.

#### 2. Create a PostgreSQL add-on

1. Log in to the [Clever Cloud console](https://console.clever-cloud.com/)
2. Click on "Create" and select "An add-on"
3. Choose "PostgreSQL"
4. Select a plan that fits your needs (Dev plan works for testing)
5. Name your add-on (e.g., `umami-db`)

#### 3. Set up your application on Clever Cloud

1. Click on "Create" and select "An application"
2. Select "Create from a GitHub repository" and use `https://github.com/umami-software/umami.git`
   - Alternatively, fork the repository first, then select your fork
3. Choose "Node.js" as the runtime environment
4. Configure your application settings (name, region, instance size)
5. Link the PostgreSQL add-on created in the previous step

#### 4. Configure environment variables

In your application's dashboard, go to "Environment variables" and set:

```
DATABASE_URL=$POSTGRESQL_ADDON_URI
APP_SECRET=<a-random-secret-string>
PORT=8080
NODE_ENV=production
CC_NODE_DEV_DEPENDENCIES=install
CC_POST_BUILD_HOOK=pnpm build
```

> **Note**: `$POSTGRESQL_ADDON_URI` is automatically set when you link the PostgreSQL add-on. You can reference it directly.

#### 5. Configure the build instance

In your application's dashboard, go to "Scalability" and set the build instance size to **M** (Next.js builds need more memory).

#### 6. Deploy your application

Deploy the application from the console or using Git:

```bash
# Add Clever Cloud as a remote repository
git remote add clever git+ssh://git@push-par-clevercloud-customers.services.clever-cloud.com/app_<your-app-id>.git

# Push your code to deploy
git push clever master
```

### Option 2: Deploy using Clever Tools CLI

#### 1. Install Clever Tools

Install the Clever Tools CLI following the [official documentation](https://www.clever-cloud.com/doc/clever-tools/getting_started/):

```bash
# Using npm
npm install -g clever-tools

# Or using Homebrew (macOS)
brew install clever-tools
```

#### 2. Log in to your Clever Cloud account

```bash
clever login
```

#### 3. Clone Umami

```bash
git clone https://github.com/umami-software/umami.git .
```

#### 4. Create the application and database

```bash
# Create a Node.js application
clever create --type node umami

# Create a PostgreSQL add-on and link it
clever addon create postgresql-addon umami-db --link umami

# Set required environment variables
clever env set DATABASE_URL "\$POSTGRESQL_ADDON_URI"
clever env set APP_SECRET $(openssl rand -hex 32)
clever env set PORT 8080
clever env set NODE_ENV "production"
clever env set CC_NODE_DEV_DEPENDENCIES "install"
clever env set CC_POST_BUILD_HOOK "pnpm build"

# Use a M instance for building (Next.js build needs more memory)
clever scale --build-flavor M
```

#### 5. Deploy your application

```bash
clever deploy
```

#### 6. Open your application in a browser

Once deployed, access your Umami instance:

```bash
clever open
```

Log in with the default credentials:

- **Username**: `admin`
- **Password**: `umami`

> **Important**: Change the default password immediately after your first login.

## Environment Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `DATABASE_URL` | Yes | - | PostgreSQL connection string |
| `APP_SECRET` | Yes | - | Unique random string for the installation |
| `PORT` | Yes | `3000` | Server port (must be `8080` on Clever Cloud) |
| `DISABLE_LOGIN` | No | `0` | Disable the login page |
| `DISABLE_TELEMETRY` | No | `0` | Opt out of Umami telemetry |
| `PRIVATE_MODE` | No | `0` | Disable all external network calls |
| `CLIENT_IP_HEADER` | No | - | HTTP header for real client IP (e.g. `X-Forwarded-For`) |
| `TRACKER_SCRIPT_NAME` | No | `script.js` | Custom name for the tracking script |
| `COLLECT_API_ENDPOINT` | No | `/api/send` | Custom collection endpoint |
| `BASE_PATH` | No | `/` | Subdirectory path (build-time only) |

For the full list, see the [Umami environment variables documentation](https://umami.is/docs/environment-variables).

## Monitoring Your Application

Once deployed, you can monitor your application through:

- **Web Console**: The Clever Cloud console provides logs, metrics, and other tools to help you manage your application.
- **CLI**: Use `clever logs` to view application logs and `clever status` to check the status of your application.

## Additional Resources

- [Umami Documentation](https://umami.is/docs)
- [Umami GitHub Repository](https://github.com/umami-software/umami)
- [Clever Cloud Node.js Documentation](https://www.clever-cloud.com/developers/doc/applications/nodejs/)
- [Clever Cloud PostgreSQL Documentation](https://www.clever-cloud.com/developers/doc/addons/postgresql/)
- [Clever Cloud Documentation](https://www.clever-cloud.com/doc/)
