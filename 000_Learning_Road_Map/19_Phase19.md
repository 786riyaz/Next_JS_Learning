# **Phase 19 – Deployment, Production Builds and CI/CD**



Advanced Level

Next.js 16.x

App Router + TypeScript



## **1. Learning objectives**

By the end of this phase, you will understand how to:

1. Prepare a Next.js application for production.
2. Create and test production builds.
3. Deploy applications to Vercel.
4. Deploy Next.js applications on a VPS using Node.js, PM2, and Nginx.
5. Configure production environment variables and secrets.
6. Understand standalone output and Docker deployment.
7. Automate builds and deployments using CI/CD.
8. Monitor, troubleshoot, and roll back production deployments.

## **2. Development vs. production environment**

A Next.js application generally runs in two primary modes.

| Feature               | Development          | Production               |
| --------------------- | -------------------- | ------------------------ |
| Command               | `next dev`           | `next start`             |
| Purpose               | Build and debug      | Serve real users         |
| Compilation           | On demand            | Before deployment        |
| Error messages        | Detailed             | More controlled          |
| Performance           | Development-focused  | Optimized                |
| Hot reload            | Yes                  | No                       |
| Source maps/debugging | Development-oriented | Production configuration |

### **Development mode**

When developing your application:

```
npm run dev
```

This starts the development server, usually at:

```
http://localhost:3000
```

You use it to:

- Write React components.
- Create pages and layouts.
- Test API endpoints.
- Debug errors.
- Use Fast Refresh.

### **Production mode**

A production application must first be built:

```
npm run build
```

Then start the production server:

```
npm run start
```

The production server serves the compiled application rather than running the development workflow.

**Important:** `next start` requires a successful production build first.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **3. Understanding Next.js production commands**

Next.js provides several CLI commands.

| Command      | Purpose                                                      |
| ------------ | ------------------------------------------------------------ |
| `next dev`   | Start development server                                     |
| `next build` | Compile and optimize the application                         |
| `next start` | Start production server                                      |
| `next info`  | Display system and Next.js diagnostic information            |
| `next lint`  | Not available as the old built-in lint command in Next.js 16 |

### **Recommended package.json scripts**

```
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint ."
  }
}
```

Next.js 16 uses Turbopack by default for development and production builds. You do not need to add `--turbo` or `--turbopack` to these commands. Also, run ESLint separately as part of your quality checks; do not assume `next build` performs linting.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **Test your production build locally**

Run:

```
npm run build
```

If the build succeeds:

```
npm run start
```

Open:

```
http://localhost:3000
```

This is an important step because an application working in development mode does not guarantee that its production build will work.

## **4. Understanding the&#x20;**`.next`**&#x20;directory**

When you execute:

```
npm run build
```

Next.js generates a `.next` directory.

Example:

```
my-next-app/
│
├── app/
├── components/
├── public/
├── node_modules/
│
├── .next/
│   ├── cache/
│   ├── server/
│   ├── static/
│   ├── build/
│   ├── BUILD_ID
│   └── ...
│
├── package.json
├── next.config.ts
└── tsconfig.json
```

### **What is&#x20;**`.next`**?**

`.next` contains generated build output and build-related data used by Next.js.

Some important directories:

| Directory/file     | Purpose                                            |
| ------------------ | -------------------------------------------------- |
| `.next/server`     | Server-side build output                           |
| `.next/static`     | Generated static assets                            |
| `.next/cache`      | Build and optimization cache                       |
| `.next/BUILD_ID`   | Build identifier                                   |
| `.next/standalone` | Minimal standalone deployment output, when enabled |

**Important points:**

- `.next` is generated automatically.
- You normally do not edit it manually.
- It is generally excluded from Git.
- You should rebuild after changing application source code.
- A normal Node.js deployment needs the appropriate build output and runtime dependencies.

Do not confuse `.next` with your source code. Your source code is in `app`, `src`, `components`, and other project directories.

## **5. Node.js version requirements**

Next.js 16 requires:

- Node.js 20.9.0 or later.
- TypeScript 5.1.0 or later, when using TypeScript.

Node.js 18 is no longer supported by Next.js 16.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



Check your installed version:

```
node -v
npm -v
```

You can define the expected Node.js version in `package.json`:

```
{
  "engines": {
    "node": ">=20.9.0"
  }
}
```

For a real deployment, choose a currently supported Node.js LTS release compatible with your hosting platform and dependencies.

You can also maintain an `.nvmrc` file:

```
22
```

This helps developers and deployment environments select a consistent Node.js version.

## **6. Environment variables in production**

In development, you might have:

```
# .env.local

MONGODB_URI=mongodb://localhost:27017/myapp
JWT_SECRET=my-development-secret
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

But production should not use your local database URL or development secrets.

For example:

```
MONGODB_URI=mongodb+srv://production-database-url
JWT_SECRET=strong-production-secret
NEXT_PUBLIC_APP_URL=https://example.com
```

### **Where should production variables be configured?**

It depends on your hosting environment.

| Hosting           | Where to configure                               |
| ----------------- | ------------------------------------------------ |
| Local development | `.env.local`                                     |
| Vercel            | Project Settings → Environment Variables         |
| VPS               | Server environment or protected environment file |
| Docker            | Runtime environment configuration or secrets     |
| CI/CD             | Secure repository or platform secrets            |

### **Important: Build-time vs runtime variables**

Some variables are needed during the build; others are read when the application runs.

For example:

```
const databaseUrl = process.env.MONGODB_URI;
```

This can be read by server-side code at runtime, provided the variable exists in the runtime environment.

However:

```
const appUrl = process.env.NEXT_PUBLIC_APP_URL;
```

Variables prefixed with `NEXT_PUBLIC_` are exposed to browser-side code and are generally inlined into the client bundle during the build.

That means changing a `NEXT_PUBLIC_` value after building does not automatically change the already-built client bundle.

**Security rule:** Never use `NEXT_PUBLIC_` for passwords, database credentials, private API keys, or session secrets.

# **7. Deploying Next.js to Vercel**

Recommended beginner deployment platform

[Dashboard redesign - Vercel](https://images.openai.com/static-rsc-4/-24q88srl2dqRRdzcLcZsbPDLVx2UCDP3qg1ga0zaeUrthlHA5YB9LeqheafSnMDkmG1PuHKVLpv98PDrj4oueCh0JDI7eWXzs-HAjKgsK3DmabG3uT4oWqgl9GSJtQBdGa_CakMSBL8DsHcQOxZfBavzbeWQ1CEdtiRSDzEnQY?purpose=inline)

[Migrate to Vercel from Netlify | Vercel Knowledge Base](https://images.openai.com/static-rsc-4/RFklhAvOpTUmDRDrFRjPgA2iiZZv_GNEXfrVbYZUxb8o5H5DBdMznmTQt9Vy36HeSO-X1PPCkzOlnZeqK_oLJrZt-bgPmsCkORUya-DV94Ik--We-K5tXtLIq7wgZReezDJ4Y1_IjVzfJQWpK08ntlJxZF22w-x8elVm0H_UY-U?purpose=inline)

[Using Environment Variables Set in Vercel with Next.js](https://images.openai.com/static-rsc-4/zI3XVzc0qbHhTqvDiUqqB_pYSYlkir05FRLirpMdM5tRfdtdFzR14UMN5-QClpe-50s_F_z9gHQ3YZhbdg8MZWJvy07UyDSn-0Mbc-_25TgtMfxIdQESsJjF4jQ6LxUNWXQ6iOvvIbpB_AJQcn7-RbwAqh8kZ092StfdSyS3rt8?purpose=inline)

[Code Reviewをツールの力で高速化する](https://images.openai.com/static-rsc-4/eKLD47eliT-v7gU5dj81121LoofxdyvkUKezsI8ivnFim6HoREKdhdi1wRxq7MYol3NdSuyRbyKKMb00WVU6exgvioYaJ60P15bo8NdB9MVumPlBi_OitahRPsTl709i7NMlLnWXeCGOaA-ri6feYhC-EXFYz6-It7r2pRGO-xw?purpose=inline)

[Vercel | Works With Supabase](https://images.openai.com/static-rsc-4/zA7ZQo1CtK0ikgtQnH5kxZmXUilrccjA48dr0K6fU-3iD8UTGla_yMRzDFANuYXebRzsGnvP62jsikqowoTt2yp5AB-G6V8I9jkFCqIMZICZkYsuL_MUGjhHQOE-62WKHV0a3q9_xrmw996lxBLWQB5RiDjXzlYIwck0KcfR7s0?purpose=inline)

[Vercelの環境変数、ビルドコマンド、Production Branchの設定方法 - サーバーワークスエンジニアブログ](https://images.openai.com/static-rsc-4/vbFJJeKzVhhL3aa2Zj18HP0Mc840T-FO3MCEKsa3l5ZfKlfPMCLCamkoDfTpNPHIVop0h_ajlwa9s9gIF2s5q1fpM8_V_hjfnoE1xoexuKQn-5xQLay2Qwv_w07E-TTE5SIyIlWEzQmwj-OYFQNwNuy1m8s2B-sCZG_LLjS9amQ?purpose=inline)

[Redeploy or promote CLI deployments from the dashboard - Vercel](https://images.openai.com/static-rsc-4/4LRutYttR0ybeMnukvtcCMs2pqO__eZRd1Cjp307ZQpYmvsanff5hIi52KERjtUuxnzZ40VGNFpyBUy2Jxwiz-NBF8cPfGJDjzlIEr2O-oLm7-PtXKL0SPSqXua8MVMH6WocuMUL0j0TdQLPJ7r8PY8IketrSZsNqK6SVS9lGI8?purpose=inline)

Illustrative Vercel dashboard views

Vercel is a hosting platform that provides an integrated deployment workflow for Next.js.

You can deploy through:

- GitHub integration.
- GitLab integration.
- Bitbucket integration.
- Vercel CLI.

The Git-based workflow is particularly useful because deployments can happen automatically when you push code.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://vercel.com\&sz=32)

Vercel+1



### **Step 1: Push your project to GitHub**

If your project is not already connected to Git:

```
git init
git add .
git commit -m "Initial commit"
```

Create a GitHub repository and connect it:

```
git remote add origin https://github.com/username/my-next-app.git

git branch -M main

git push -u origin main
```

### **Step 2: Import your repository into Vercel**

1. Open [Vercel](https://vercel.com/?utm_source=chatgpt.com).
2. Sign in.
3. Select **Add New → Project**.
4. Import your GitHub repository.
5. Select the Next.js project.
6. Verify the build settings.
7. Add production environment variables.
8. Click Deploy.

Vercel detects Next.js projects and normally configures the build settings automatically.

Typical build configuration:

| Setting             | Value                                     |
| ------------------- | ----------------------------------------- |
| Framework           | Next.js                                   |
| Build command       | `next build`                              |
| Install command     | `npm install` or detected package manager |
| Output directory    | Framework default                         |
| Development command | `next dev`                                |

Avoid overriding automatically detected settings unless your project requires it.

### **Step 3: Configure environment variables**

Navigate to:

```
Vercel Dashboard
    → Your Project
        → Settings
            → Environment Variables
```

Add variables such as:

```
MONGODB_URI=your-production-mongodb-uri
JWT_SECRET=your-production-secret
NEXT_PUBLIC_APP_URL=https://yourdomain.com
```

Choose the environment where each variable should apply.

Vercel supports three default environments:

| Environment | Purpose           |
| ----------- | ----------------- |
| Development | Local development |
| Preview     | Test deployments  |
| Production  | Live application  |

A change to environment variables applies to new deployments, not deployments that have already been built. Redeploy after changing a variable.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://vercel.com\&sz=32)

Vercel+2



### **Step 4: Understand automatic deployments**

Imagine your GitHub repository has two branches:

```
main
development
```

You can use them like this:

**development branch**

Preview deployment

Push changes → Vercel creates a preview URL → Test your changes.

**main branch**

Production deployment

Merge approved changes → Vercel creates a production deployment.

This workflow helps you test changes before exposing them to real users.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://vercel.com\&sz=32)

Vercel+1



### **Step 5: Configure a custom domain**

Suppose you own:

```
example.com
```

You want your Next.js application to run at:

```
https://example.com
```

In Vercel:

1. Open your project.
2. Go to Settings → Domains.
3. Add your domain.
4. Configure the DNS records requested by Vercel.
5. Wait for DNS verification and SSL configuration.

Do not guess DNS record values. Use the exact records displayed by your domain and hosting provider.

### **Step 6: Verify the deployment**

After deployment, check:

- Home page loads.
- Nested routes work.
- Dynamic routes work.
- API endpoints work.
- Login and logout work.
- Database connection works.
- Images load.
- Environment variables are correct.
- Browser console has no unexpected errors.
- Server logs have no critical errors.

## **8. Deploying Next.js on a VPS**

Important for self-hosting

A VPS (Virtual Private Server) gives you control over the server environment. You can install Node.js, configure Nginx, run your Next.js application, connect a database, and manage deployments yourself.

Examples of VPS providers include:

- Hostinger.
- DigitalOcean.
- AWS EC2.
- Hetzner.

### **8.1 Understand the VPS architecture**

A typical production architecture looks like this:

**User's browser**

[https://example.com](https://example.com?utm_source=chatgpt.com)

**DNS + HTTPS**

Domain resolves to your VPS IP address

**Nginx – Reverse Proxy**

Receives public HTTP/HTTPS traffic

**Next.js Node.js server**

localhost:3000 – application rendering and APIs

**Database / External services**

MongoDB, MySQL, APIs, file storage

### **8.2 Understand the role of each component**

| Component | Responsibility                     |
| --------- | ---------------------------------- |
| Domain    | Human-readable website address     |
| DNS       | Resolves domain to server IP       |
| Nginx     | Reverse proxy and web server       |
| Node.js   | Runs the Next.js application       |
| PM2       | Keeps the Node.js process running  |
| Database  | Stores application data            |
| SSL/TLS   | Encrypts communication using HTTPS |

### **8.3 Prepare your VPS**

Connect to your VPS using SSH:

```
ssh username@YOUR_SERVER_IP
```

Check Node.js and npm:

```
node -v
npm -v
```

Install a compatible Node.js version if required.

Then install Nginx on an Ubuntu server:

```
sudo apt update
sudo apt install nginx
```

Check its status:

```
sudo systemctl status nginx
```

### **8.4 Upload or clone your Next.js project**

A common approach is to clone your Git repository:

```
git clone https://github.com/username/my-next-app.git

cd my-next-app
```

Install dependencies:

```
npm ci
```

**What is&#x20;**`npm ci`**?**

`npm ci` installs dependencies using the exact versions recorded in `package-lock.json`.

Difference:

| `npm install`                   | `npm ci`                        |
| ------------------------------- | ------------------------------- |
| Can update the lockfile         | Does not update the lockfile    |
| Useful during development       | Useful in automated deployments |
| Can install/update dependencies | Requires a compatible lockfile  |

Use the package manager and lockfile appropriate to your project. For an npm project with a committed `package-lock.json`, `npm ci` is a good production deployment choice.

### **8.5 Configure production environment variables**

You need to provide production values to the server.

For example, create a protected environment file outside your publicly served application directory:

```
sudo nano /etc/my-next-app.env
```

Example contents:

```
NODE_ENV=production
PORT=3000

MONGODB_URI=your-production-database-url
JWT_SECRET=your-strong-production-secret
NEXT_PUBLIC_APP_URL=https://example.com
```

Restrict file access:

```
sudo chmod 600 /etc/my-next-app.env
```

Do not commit this file to Git or place it inside a publicly served directory. The example below uses PM2 to load it.

### **8.6 Build your Next.js application**

Inside your project directory:

```
npm run build
```

If the build succeeds, you can run:

```
npm run start
```

The application normally listens on port 3000.

You can test it from the VPS:

```
curl http://localhost:3000
```

However, running `npm run start` directly in an SSH terminal is not a good long-term process-management strategy. If the terminal or process stops, your application may stop too.

That is where PM2 comes in.

## **9. Process management with PM2**

[PM2 - Home - Advanced Node.js Process Manager & Monitoring](https://images.openai.com/static-rsc-4/sIioqB3w9CO9Ce-F8fwsE2F9TaIHghgBculIN-RXxwhd8o_z4IuQcpLPAC3XbFTvqbBB5M_1vlyJg8spzXIItQ_EANIgHF8q8CBV_8ctCK4PppTBpw9Reek2X5a0poElRAHtcEw8kS0qafbJCwKumRkbx5vj8njFHGVeq8psGJw?purpose=inline)

[pm2.io](https://pm2.io/?utm_source=chatgpt.com)

PM2 is a process manager that helps keep Node.js applications running.

It provides:

- Process monitoring.
- Automatic restart after crashes.
- Application logs.
- Startup on server reboot.
- Multiple application process management.

### **9.1 Install PM2**

```
sudo npm install -g pm2
```

### **9.2 Start the Next.js application**

```
pm2 start npm --name "my-next-app" -- start
```

PM2 starts the command:

```
npm start
```

with the process name:

```
my-next-app
```

### **9.3 Useful PM2 commands**

| Command                   | Purpose                       |
| ------------------------- | ----------------------------- |
| `pm2 list`                | List running applications     |
| `pm2 status`              | Show process status           |
| `pm2 logs`                | View application logs         |
| `pm2 logs my-next-app`    | View logs for one application |
| `pm2 restart my-next-app` | Restart application           |
| `pm2 stop my-next-app`    | Stop application              |
| `pm2 delete my-next-app`  | Remove process from PM2       |
| `pm2 monit`               | Monitor resource usage        |

### **9.4 Load environment variables with PM2**

You can create an ecosystem configuration file in your project:

```
// ecosystem.config.cjs

module.exports = {
  apps: [
    {
      name: "my-next-app",
      script: "npm",
      args: "start",
      cwd: "/var/www/my-next-app",
      env: {
        NODE_ENV: "production",
        PORT: 3000
      },
      env_file: "/etc/my-next-app.env"
    }
  ]
};
```

Start it:

```
pm2 start ecosystem.config.cjs
```

PM2 supports environment configuration in its ecosystem files. Protect the external environment file and verify your installed PM2 version supports the configuration you use.

### **9.5 Enable startup after a VPS reboot**

Run:

```
pm2 startup
```

PM2 will display a command. Copy and execute the command it provides.

Then save the process list:

```
pm2 save
```

Now PM2 can restore the saved processes when the server reboots, provided the generated startup service is correctly configured.

## **10. Nginx as a reverse proxy**

This is one of the most important concepts in VPS deployment.

Suppose your Next.js application is running at:

```
http://localhost:3000
```

But you want visitors to access:

```
https://example.com
```

You do not normally expose port 3000 directly to the public internet.

Instead, Nginx receives the public request and forwards it to the Next.js server.

### **10.1 What is a reverse proxy?**

A reverse proxy is a server that receives requests from clients and forwards them to an internal application server.

For example:

```
Browser
   |
   | https://example.com
   |
   v
Nginx :443
   |
   | http://127.0.0.1:3000
   |
   v
Next.js
```

### **10.2 Create an Nginx configuration**

On Ubuntu, create a configuration file:

```
sudo nano /etc/nginx/sites-available/my-next-app
```

Add:

```
server {
    listen 80;
    server_name example.com www.example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For
            $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### **10.3 Enable the configuration**

```
sudo ln -s \
/etc/nginx/sites-available/my-next-app \
/etc/nginx/sites-enabled/
```

Test the configuration:

```
sudo nginx -t
```

If the configuration is valid:

```
sudo systemctl reload nginx
```

Now Nginx forwards requests to your Next.js application.

### **10.4 Configure HTTPS**

You should use HTTPS in production.

A common option on Ubuntu is Certbot with Let's Encrypt:

```
sudo apt install certbot python3-certbot-nginx
```

Request a certificate:

```
sudo certbot --nginx -d example.com -d www.example.com
```

Certbot can configure Nginx to use the certificate and enable HTTPS redirection.

Follow the instructions shown by Certbot and verify certificate renewal.

**Security note:** Keep the Next.js application port private. Allow public inbound traffic to the required web ports, usually 80 and 443, and restrict SSH access appropriately.

## **11. Standalone output in Next.js**

Next.js provides an option to generate a minimal deployment package.

This is useful when deploying to:

- VPS.
- Docker containers.
- Self-hosted infrastructure.
- Other Node.js hosting environments.

### **11.1 Configure standalone output**

Open:

```
next.config.ts
```

Add:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "standalone",
};

export default nextConfig;
```

### **11.2 Build your application**

```
npm run build
```

Next.js generates:

```
.next/
│
├── server/
├── static/
├── cache/
│
└── standalone/
    ├── node_modules/
    ├── server.js
    ├── package.json
    └── ...
```

The standalone folder contains a minimal server and the traced runtime files needed by the application.

### **11.3 Copy static assets**

The standalone output does not automatically include the `public` and `.next/static` directories.

Copy them:

```
cp -r public .next/standalone/
```

```
cp -r .next/static .next/standalone/.next/
```

If your project does not have a `public` directory, skip that copy command.

### **11.4 Start the standalone server**

```
cd .next/standalone
```

```
PORT=3000 HOSTNAME=127.0.0.1 node server.js
```

The application will start using the standalone server.

For a PM2 deployment, you can run the standalone `server.js` directly rather than starting `npm`.

For example:

```
pm2 start .next/standalone/server.js \
  --name my-next-app
```

Set the required runtime environment variables in your process manager configuration.

The standalone output behavior and asset-copy requirements are documented in the Next.js output configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **12. Docker deployment**

[Docker Images Explained: Creation, Containers, Advantages & Disadvantages, with Real-Time Use Cases](https://images.openai.com/static-rsc-4/dxdSSHoNC-E2I_NKYCbc3-cu2lquNY4hnVgi84C8u3cfUds70TdbvgmEV5xoJi32fa3UYTZn3XjQi1Ec2pIN-4-fKt2SC5zM_Y9t2VGHSF_ky9N94SSCiXvkNws1mafAOwz-HxpwOkebqGzmxpkqk_datvqvqrGVfH0ohYqdH2U?purpose=inline)

[clouddevopshub.com](https://www.clouddevopshub.com/blog/ultimate-guide-to-docker-images?utm_source=chatgpt.com)

Docker packages an application and its runtime dependencies into an image. That image can be used to create containers on different machines.

### **12.1 Why use Docker?**

Without Docker, deployment environments can differ:

```
Developer machine:
Node.js 22
npm 10
Linux/Windows differences

Production server:
Node.js 20
npm 9
Different system libraries
```

These differences can cause deployment problems.

Docker helps make the runtime environment more consistent.

### **12.2 Configure standalone output**

In `next.config.ts`:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "standalone",
};

export default nextConfig;
```

### **12.3 Create a Dockerfile**

Create a file named:

```
Dockerfile
```

Example multi-stage Dockerfile:

```
FROM node:22-alpine AS base

# Install dependencies
FROM base AS deps

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm ci

# Build the application
FROM base AS builder

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

RUN npm run build

# Production runtime
FROM base AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV PORT=3000
ENV HOSTNAME=0.0.0.0

COPY --from=builder /app/public ./public

COPY --from=builder /app/.next/standalone ./

COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000

CMD ["node", "server.js"]
```

This example assumes that your project has a `public` directory. If it does not, remove the `COPY` instruction for `public`.

### **12.4 Create a&#x20;**`.dockerignore`

```
node_modules
.next
.git
.env*
npm-debug.log
```

This prevents unnecessary local files and environment files from entering the Docker build context.

### **12.5 Build the Docker image**

```
docker build -t my-next-app .
```

### **12.6 Run the container**

```
docker run -d \
  --name my-next-app \
  -p 3000:3000 \
  -e MONGODB_URI="your-database-url" \
  -e JWT_SECRET="your-secret" \
  my-next-app
```

In real deployments, avoid putting secrets directly in shell history or committing them into Docker files. Supply secrets through your deployment platform's protected runtime configuration or secret manager.

### **12.7 Useful Docker commands**

| Command                      | Purpose                 |
| ---------------------------- | ----------------------- |
| `docker images`              | List images             |
| `docker ps`                  | List running containers |
| `docker ps -a`               | List all containers     |
| `docker logs my-next-app`    | View container logs     |
| `docker stop my-next-app`    | Stop container          |
| `docker start my-next-app`   | Start container         |
| `docker restart my-next-app` | Restart container       |
| `docker rm my-next-app`      | Remove container        |

**Important:** A Docker image is not the same thing as a container.

- Image = packaged application template.
- Container = running instance created from an image.

## **13. CI/CD – Continuous Integration and Continuous Deployment**

This is an essential topic for professional development.

### **13.1 What is CI?**

CI stands for **Continuous Integration**.

It means automatically checking code changes whenever developers push code or create pull requests.

Typical CI tasks:

- Install dependencies.
- Run ESLint.
- Run TypeScript checks.
- Run unit tests.
- Create a production build.

### **13.2 What is CD?**

CD commonly refers to **Continuous Delivery** or **Continuous Deployment**.

- Continuous Delivery: changes are automatically prepared for release, but production release may require approval.
- Continuous Deployment: successful changes are automatically released to production.

### **13.3 Understand the CI/CD workflow**

**Developer writes code**

**Push to GitHub**

**CI pipeline starts**

Install → Lint → Type-check → Test → Build

**Checks pass**

**Deployment**

Preview or production environment

### **13.4 GitHub Actions**

GitHub Actions allows you to automate workflows directly from your GitHub repository.

Create this file:

```
.github/
└── workflows/
    └── ci.yml
```

Example workflow:

```
name: Next.js CI

on:
  push:
    branches: [main, development]

  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Run TypeScript checks
        run: npx tsc --noEmit

      - name: Build application
        run: npm run build
```

This workflow:

1. Checks out your repository.
2. Installs the selected Node.js version.
3. Installs dependencies.
4. Runs ESLint.
5. Checks TypeScript.
6. Creates a production build.

If any required step fails, the workflow reports failure.

**Note:** If your application requires environment variables during its build, configure safe test values or appropriate CI secrets. Never put production credentials directly in the YAML file.

### **13.5 Automating deployment**

There are two common approaches:

**Approach A – Vercel Git integration**

```
GitHub push
    ↓
Vercel build
    ↓
Deployment
    ↓
Preview or production
```

This is the simplest approach for most Vercel-hosted Next.js projects.

**Approach B – VPS deployment pipeline**

```
GitHub push
    ↓
GitHub Actions
    ↓
Tests and build checks
    ↓
SSH into VPS
    ↓
Update application
    ↓
Restart PM2
```

A VPS deployment pipeline needs secure SSH credentials, a deployment user with limited permissions, and a reliable restart/rollback strategy. Do not run arbitrary untrusted pull-request code with production server credentials.

## **14. Static export vs Node.js hosting**

Not every Next.js application needs a running Node.js server.

Next.js supports different deployment models.

| Deployment model                | Best suited for                             |
| ------------------------------- | ------------------------------------------- |
| Managed platform such as Vercel | Most Next.js applications                   |
| Node.js server                  | Full Next.js server features                |
| Docker                          | Container-based infrastructure              |
| Static export                   | Websites that can be served as static files |

### **14.1 Static export**

Static export generates HTML, CSS, JavaScript, and other static assets that can be hosted on a static web server.

Configure:

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "export",
};

export default nextConfig;
```

Build:

```
npm run build
```

Next.js generates an `out` directory.

```
my-next-app/
│
├── app/
├── public/
├── out/
│   ├── index.html
│   ├── about.html
│   └── ...
└── next.config.ts
```

You can host the exported files on a static hosting service or web server.

### **14.2 Static export limitations**

Static export does not provide the full Next.js server runtime.

For example, features that require a Next.js server are not available in a static-only deployment, including:

- Server Actions.
- Dynamic server-side rendering.
- Runtime Route Handlers.
- Server-dependent authentication flows.
- The default server-side image optimization service.

Some dynamic routes can still be exported if their pages can be generated at build time, for example using `generateStaticParams()`.

**Decision rule:** If your application needs server-side authentication, database access, runtime APIs, or dynamic server rendering, choose a Node.js-compatible deployment rather than static export.

## **15. Database considerations during deployment**

A Next.js application may work perfectly on your laptop but fail after deployment because the database environment is different.

Consider this example:

```
const uri = process.env.MONGODB_URI;
```

Locally, it might point to:

```
mongodb://localhost:27017/myapp
```

But your production VPS or Vercel deployment cannot automatically access your laptop's local MongoDB.

### **MongoDB production considerations**

If you use MongoDB Atlas:

1. Configure a production database.
2. Set the correct connection string in the hosting environment.
3. Configure network access appropriately.
4. Use a database user with only the permissions required.
5. Use TLS and protect credentials.
6. Reuse database connections appropriately instead of opening a new connection for every request.

### **MySQL production considerations**

If your MySQL database is on a VPS:

- Make sure the database service is running.
- Ensure the application can connect to the correct host and port.
- Configure firewall rules.
- Use a dedicated database user.
- Do not expose MySQL publicly without a specific security requirement.
- Apply schema migrations before releasing code that depends on them.

**Important:** A deployed application and its database are separate services. Deploying the Next.js frontend does not automatically deploy or migrate your database.

## **16. Production logs, monitoring and rollback**

Deploying an application is not the end of the process. You must also verify that it continues to work correctly.

### **16.1 Application logs**

Logs help you understand what is happening inside your application.

For example:

```
export async function GET() {
  try {
    const users = await getUsers();

    return Response.json(users);
  } catch (error) {
    console.error("Failed to fetch users:", error);

    return Response.json(
      { message: "Internal server error" },
      { status: 500 }
    );
  }
}
```

Do not log passwords, session tokens, private keys, or complete sensitive database records.

### **16.2 Where to check logs**

| Environment            | Where to check                              |
| ---------------------- | ------------------------------------------- |
| Local production build | Terminal                                    |
| Vercel                 | Project → Deployments → Function/Build logs |
| VPS with PM2           | `pm2 logs`                                  |
| Docker                 | `docker logs container-name`                |
| Nginx                  | `/var/log/nginx/`                           |

Useful VPS commands:

```
pm2 logs my-next-app
```

```
sudo tail -f /var/log/nginx/error.log
```

### **16.3 Monitoring**

Production monitoring helps you detect:

- Application crashes.
- Slow response times.
- High CPU and memory usage.
- Database connection failures.
- HTTP 500 errors.
- Unavailable services.
- Core Web Vitals regressions.

Common monitoring tools include:

- Vercel Observability.
- Sentry.
- Prometheus.
- Grafana.

### **16.4 Rollback**

Suppose you deploy version 2 of your application and it introduces a serious bug.

You may need to restore version 1.

A rollback means returning production traffic to a previously working deployment or release.

On Vercel, you can manage deployments and use rollback-related deployment workflows from the dashboard.

On a VPS, a safer approach is to keep previous release versions available:

```
/var/www/my-next-app/
│
├── releases/
│   ├── release-001/
│   ├── release-002/
│   └── release-003/
│
└── current -> releases/release-003/
```

The `current` symbolic link points to the active release.

If release 003 fails, you can switch it back to release 002 and restart the application.

**Production principle:** Never assume that a successful deployment means the application is healthy. Verify it after deployment.

## **17. Common deployment errors**

These are problems you should learn to diagnose.

| Error                              | Possible cause                                                      | Solution                                                |
| ---------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------- |
| Build fails                        | TypeScript or compilation error                                     | Inspect build output and fix errors                     |
| Missing environment variable       | Variable not configured on host                                     | Configure it in the correct environment                 |
| Database connection refused        | Wrong URI, firewall or unavailable database                         | Verify connectivity and credentials                     |
| Node version error                 | Unsupported Node.js version                                         | Use a compatible Node.js version                        |
| Port already in use                | Another process occupies the port                                   | Check the running process and change the port if needed |
| 502 Bad Gateway                    | Nginx cannot reach the application                                  | Check PM2 status and upstream port                      |
| 404 for static assets              | Incorrect asset path or missing copied assets                       | Inspect build output and deployment files               |
| Images fail                        | Incorrect `remotePatterns` configuration or inaccessible image host | Verify image host and Next.js image configuration       |
| Works locally but fails on Linux   | Case-sensitive file paths                                           | Match import casing exactly                             |
| Application stops after SSH closes | Started directly in the terminal                                    | Use PM2 or a service manager                            |
| Build runs out of memory           | Insufficient server memory                                          | Optimize build or use a suitable build environment      |
| API returns 500                    | Runtime exception or configuration issue                            | Inspect application and database logs                   |

### **Example: 502 Bad Gateway**

Suppose you visit:

```
https://example.com
```

But Nginx shows:

```
502 Bad Gateway
```

Possible architecture:

```
Browser
   |
   v
Nginx
   |
   v
localhost:3000
   |
   X
Next.js is not running
```

Troubleshooting:

**Step 1 – Check PM2**

```
pm2 list
```

**Step 2 – Check application logs**

```
pm2 logs my-next-app
```

**Step 3 – Check port 3000**

```
curl http://localhost:3000
```

**Step 4 – Check Nginx**

```
sudo nginx -t
```

**Step 5 – Restart if the cause is fixed**

```
pm2 restart my-next-app
```

Do not blindly restart services without inspecting the logs. Find the underlying cause first.

## **18. Production deployment checklist**

Before releasing a Next.js application, use this checklist.

**Deployment readiness**

### **0/15**

Production build completes successfully

ESLint checks pass

TypeScript checks pass

All required environment variables are configured

No secrets are committed to Git

Production database connection works

Authentication and authorization are verified

Dynamic routes and API endpoints work

Images, fonts and static assets load

Custom domain and HTTPS work

Application logs show no critical errors

Error and 404 pages are configured

Performance and accessibility have been checked

Database backups and recovery strategy are available

Rollback procedure is understood

Reset checklist

## **19. Real-world deployment example**

Suppose you build an application with:

- Next.js 16.
- TypeScript.
- MongoDB Atlas.
- Authentication.
- Server Actions.
- API Route Handlers.
- Cloudinary for images.

You want to deploy it to production.

A suitable architecture could be:

## **Production architecture**

**GitHub**

Source code and CI workflow

**Vercel**

Next.js hosting and deployment

**MongoDB Atlas**

Application data

**Cloudinary**

Image storage and delivery

GitHub triggers deployment. Vercel runs the Next.js application, which accesses MongoDB and Cloudinary using server-side credentials.

A typical release process:

1. Develop a feature locally.
2. Test it with `npm run dev`.
3. Run `npm run lint`.
4. Run `npx tsc --noEmit`.
5. Test the production build using `npm run build`.
6. Push the code to a development branch.
7. Review the preview deployment.
8. Merge approved changes into `main`.
9. Verify the production deployment.
10. Monitor logs, database connectivity, and performance.

This workflow is applicable to many production Next.js applications.

## **20. Interview questions and answers**

### **Basic questions**

**Q1. What is the difference between&#x20;**`next dev`**&#x20;and&#x20;**`next start`**?**

`next dev` starts the development server with development features. `next start` runs the application in production mode and requires a prior production build.

**Q2. What is the purpose of&#x20;**`next build`**?**

It compiles and optimizes the application for production and generates the required build output.

**Q3. What is the&#x20;**`.next`**&#x20;directory?**

It is the generated build directory containing compiled application output, static assets, server files, and build-related data.

**Q4. What is Vercel?**

Vercel is a cloud platform that provides hosting and deployment workflows for Next.js and other web applications.

**Q5. What is a production build?**

A production build is the compiled and optimized version of an application intended to serve real users.

### **Intermediate questions**

**Q6. What is the difference between&#x20;**`npm install`**&#x20;and&#x20;**`npm ci`**?**

`npm install` installs dependencies and can update the lockfile. `npm ci` installs from the lockfile without modifying it and is commonly used in CI environments.

**Q7. What is PM2?**

PM2 is a Node.js process manager used to keep applications running, restart them after crashes, manage logs, and configure startup after a server reboot.

**Q8. What is Nginx?**

Nginx is a web server that can also work as a reverse proxy, forwarding incoming requests to application servers such as Next.js.

**Q9. What is standalone output?**

Standalone output is a minimal deployment package generated by Next.js that includes the required traced server files and a minimal server entry point.

**Q10. What is the difference between Preview and Production deployments on Vercel?**

Preview deployments are intended for testing changes before release. Production deployments serve the live application through the production domain.

### **Advanced questions**

**Q11. What is CI/CD?**

CI/CD is a set of automated development and release practices. CI checks and validates changes; CD automates preparing or releasing those changes.

**Q12. Why might an application work locally but fail after deployment?**

Possible causes include missing environment variables, incompatible Node.js versions, case-sensitive imports, unavailable databases, build-time errors, and incorrect server configuration.

**Q13. Why should we use a reverse proxy?**

A reverse proxy handles public requests and forwards them to internal application servers. It can provide TLS termination, routing, and a central entry point for multiple applications.

**Q14. What is the difference between a Docker image and a Docker container?**

An image is a packaged template containing the application and its dependencies. A container is a running instance of that image.

**Q15. Why is static export not suitable for every Next.js application?**

Static export does not include the full Next.js server runtime. Applications requiring runtime Server Actions, server-side rendering, or runtime Route Handlers need a server-capable deployment.

**Q16. How would you deploy a Next.js application on an Ubuntu VPS?**

A typical approach is to install a compatible Node.js version, clone the project, install dependencies, configure environment variables, build the app, run it with PM2, and configure Nginx as a reverse proxy with HTTPS.

**Q17. How would you troubleshoot a 502 Bad Gateway error?**

Check whether the application process is running, inspect its logs, verify the upstream port, test the local application endpoint, and validate the Nginx configuration.

**Q18. What happens if you change an environment variable in Vercel?**

The changed value applies to new deployments. An existing deployment does not automatically receive the updated value, so a redeployment is needed.

## **21. Practical assignments**

Complete these exercises to gain hands-on deployment experience.

### **Assignment 1 – Production build**

- Configure `package.json` scripts.
- Run `npm run build`.
- Fix all build errors.
- Run `npm run start`.
- Test your application locally in production mode.

### **Assignment 2 – Vercel deployment**

- Push a Next.js project to GitHub.
- Import it into Vercel.
- Configure environment variables.
- Deploy the application.
- Test the preview deployment.
- Merge changes into the production branch.
- Configure a custom domain.

### **Assignment 3 – VPS deployment**

- Connect to an Ubuntu VPS using SSH.
- Install Node.js and Nginx.
- Clone your Next.js repository.
- Install dependencies using `npm ci`.
- Configure production environment variables.
- Build the application.
- Start the application using PM2.
- Configure Nginx reverse proxy.
- Configure HTTPS using Certbot.
- Verify the application after restarting the VPS.

### **Assignment 4 – Docker**

- Configure `output: "standalone"`.
- Create a production Dockerfile.
- Create `.dockerignore`.
- Build a Docker image.
- Run a container.
- Pass environment variables securely.
- Inspect container logs.

### **Assignment 5 – CI/CD**

- Create a GitHub Actions workflow.
- Automate dependency installation.
- Run ESLint.
- Run TypeScript checks.
- Run the production build.
- Verify that a failed check prevents the workflow from succeeding.

## **22. Quick revision notes**

| Concept               | Remember                                             |
| --------------------- | ---------------------------------------------------- |
| `next dev`            | Development server                                   |
| `next build`          | Production compilation                               |
| `next start`          | Production server                                    |
| `.next`               | Generated build directory                            |
| `npm ci`              | Lockfile-based dependency installation               |
| Vercel                | Managed Next.js deployment platform                  |
| Preview deployment    | Test changes before production                       |
| Production deployment | Live application                                     |
| VPS                   | Virtual private server                               |
| PM2                   | Node.js process manager                              |
| Nginx                 | Web server and reverse proxy                         |
| Standalone output     | Minimal Next.js deployment package                   |
| Docker image          | Packaged application template                        |
| Docker container      | Running image instance                               |
| CI                    | Automated code validation                            |
| CD                    | Automated delivery or deployment                     |
| Static export         | Static files without the full Next.js server runtime |
| Rollback              | Restore a previously working release                 |

### **Most important commands to remember**

```
# Development
npm run dev

# Install dependencies
npm ci

# Production build
npm run build

# Production server
npm run start

# PM2
pm2 start npm --name my-app -- start
pm2 list
pm2 logs
pm2 restart my-app

# Nginx
sudo nginx -t
sudo systemctl reload nginx

# Docker
docker build -t my-next-app .
docker run -p 3000:3000 my-next-app
```

## **23. Phase 19 completion checklist**

You should now be able to explain:

- Development vs production mode.
- `next dev`, `next build`, and `next start`.
- The `.next` build directory.
- Production environment variables.
- Vercel Git-based deployments.
- Preview and production environments.
- VPS deployment architecture.
- PM2 process management.
- Nginx reverse proxy configuration.
- Standalone output.
- Docker deployment.
- GitHub Actions and CI/CD.
- Static export limitations.
- Production monitoring and rollback.
- Common deployment errors and troubleshooting.

## **24. What's next?**

Next phase

# **Phase 20 – Testing Next.js Applications**

Learn how to test your Next.js applications professionally.

Topics will include:

1. Software testing fundamentals.
2. Unit testing with Vitest.
3. Component testing with React Testing Library.
4. Integration testing.
5. API and Route Handler testing.
6. Server Actions testing.
7. End-to-end testing with Playwright.
8. Mocking APIs and databases.
9. Test coverage and CI integration.
10. Real-world testing strategies.

When you're ready, say **OK, Phase 20**.