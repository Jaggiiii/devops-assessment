# ---------- Stage 1: Build ----------
FROM node:22 AS builder

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy source and build
COPY . .
RUN npm run build


# ---------- Stage 2: Run ----------
FROM nginx:alpine

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy build output
COPY --from=builder /app/dist /usr/share/nginx/html

# 🔑 Copy nginx reverse proxy config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Fix permissions for non-root nginx
RUN mkdir -p /run/nginx \
    && chown -R appuser:appgroup \
        /usr/share/nginx/html \
        /etc/nginx \
        /var/cache/nginx \
        /run \
        /run/nginx

# Switch to non-root user
USER appuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
