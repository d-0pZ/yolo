# ---------- Build Stage ----------
FROM node:13.12.0-alpine AS builder

# Build argument for environment
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

# Set working directory
WORKDIR /app

# Copy package files first for better Docker layer caching
COPY package*.json ./

# Install ALL dependencies (including devDependencies for build process)
RUN npm ci && npm cache clean --force

# Copy source code
COPY . .

# Build the React application for production
RUN npm run build

# ---------- Production Stage ----------
FROM nginx:1.25-alpine

# Build argument for environment (consistency across stages)
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy built React application from builder stage
COPY --from=builder /app/build /usr/share/nginx/html

# Create non-root user for security
RUN addgroup -g 1001 -S nginxuser && \
    adduser -S nginxuser -u 1001 -G nginxuser && \
    chown -R nginxuser:nginxuser /usr/share/nginx/html && \
    chown -R nginxuser:nginxuser /var/cache/nginx && \
    chown -R nginxuser:nginxuser /var/log/nginx && \
    chown -R nginxuser:nginxuser /etc/nginx/conf.d && \
    touch /var/run/nginx.pid && \
    chown -R nginxuser:nginxuser /var/run/nginx.pid

# Switch to non-root user
USER nginxuser

# Expose port 3000
EXPOSE 3000

# Health check for container monitoring
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:3000/ || exit 1

# Start nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
