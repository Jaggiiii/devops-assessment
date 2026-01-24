# ---------- Stage 1: Build ----------
FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build


# ---------- Stage 2: Run ----------
FROM nginx:alpine

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy build output
COPY --from=builder /app/dist /usr/share/nginx/html

# Fix permissions properly for nginx runtime
RUN mkdir -p /run/nginx \
    && chown -R appuser:appgroup \
        /usr/share/nginx/html \
        /var/cache/nginx \
        /etc/nginx \
        /run \
        /run/nginx

# Switch to non-root user
USER appuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
