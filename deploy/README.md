# Deploying PRASUNET Powerpoint to powerpoint.prasunet.com

The web build of **PRASUNET Powerpoint** compiles Rust into WebAssembly and outputs a fully static web distribution inside `dist/web/`.

---

## 🚀 Option 1: Cloudflare Pages / Vercel / Netlify (Recommended - Fast & Free SSL)

Because the web application is built into static files (`index.html`, `.wasm`, `.js`), the easiest and fastest way to deploy to `powerpoint.prasunet.com` is with a static hosting provider:

### Cloudflare Pages
1. Push your repository to GitHub / GitLab.
2. Go to Cloudflare Dashboard → **Workers & Pages** → **Create application** → **Pages**.
3. Select your repository:
   - **Framework preset**: None
   - **Build command**: `cargo install trunk && cd apps/deckcraft-web && trunk build --release`
   - **Build output directory**: `dist/web`
4. Under **Custom domains**, add `powerpoint.prasunet.com`.
5. Cloudflare will automatically provision free SSL and serve via its global edge CDN.

### Vercel / Netlify
- Drag and drop the `dist/web` folder directly into Vercel or Netlify, or link the repo with output directory `dist/web`.
- In Domain Settings, assign `powerpoint.prasunet.com`.

---

## 🐳 Option 2: Docker / Docker Compose (On your VPS / Cloud Server)

A ready-to-use Dockerfile and Docker Compose configuration are provided in `deploy/`:

1. Run the container:
   ```bash
   cd deploy
   docker compose up -d --build
   ```
2. The app is served on port `8080`.
3. Point your reverse proxy (Nginx, Traefik, or Caddy) or Cloudflare DNS to your server IP.

---

## 🌐 Option 3: Nginx on Linux VPS (Ubuntu / Debian)

1. **Build the web bundle**:
   ```bash
   cd apps/deckcraft-web
   trunk build --release
   ```

2. **Copy the built assets to your web root**:
   ```bash
   sudo mkdir -p /var/www/powerpoint.prasunet.com
   sudo cp -r dist/web/* /var/www/powerpoint.prasunet.com/
   ```

3. **Configure Nginx**:
   Copy the provided Nginx configuration:
   ```bash
   sudo cp deploy/nginx.conf /etc/nginx/sites-available/powerpoint.prasunet.com
   sudo ln -s /etc/nginx/sites-available/powerpoint.prasunet.com /etc/nginx/sites-enabled/
   sudo nginx -t && sudo systemctl reload nginx
   ```

4. **Set up HTTPS / SSL with Let's Encrypt**:
   ```bash
   sudo certbot --nginx -d powerpoint.prasunet.com
   ```

---

## 🛠 Local Testing of the Web Build

To preview the web app locally before deploying:
```bash
cd apps/deckcraft-web
trunk serve
```
Then open `http://127.0.0.1:8768` in your browser.

