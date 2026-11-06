# Multi-stage Dockerfile for PRASUNET Powerpoint
# 1. Build WebAssembly assets
FROM rust:1.85-slim AS builder

WORKDIR /app

# Install build dependencies, trunk, and wasm target
RUN apt-get update && apt-get install -y --no-install-recommends \
    pkg-config \
    libssl-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*

RUN rustup target add wasm32-unknown-unknown
RUN cargo install --locked trunk

# Copy workspace source files
COPY . .

# Build web release into dist/web
WORKDIR /app/apps/deckcraft-web
RUN trunk build --release

# 2. Production web server
FROM nginx:alpine

# Copy custom Nginx configuration
COPY deploy/nginx.conf /etc/nginx/conf.d/default.conf

# Copy compiled web bundle
COPY --from=builder /app/dist/web /var/www/powerpoint.prasunet.com

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

