# ---------- Build Stage ----------
FROM node:18.20.4-alpine AS builder

WORKDIR /app

# Copy only package files first to leverage Docker layer cache
COPY package*.json ./

# Install only production dependencies
RUN npm ci --omit=dev && npm cache clean --force

# Copy application source code
COPY . .

# ---------- Runtime Stage ----------
FROM node:18.20.4-alpine

WORKDIR /app

# Create a non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Copy the built app and deps from the builder stage
COPY --from=builder --chown=nodejs:nodejs /app /app

USER nodejs

# Expose your app's port
EXPOSE 5000

# Health check to monitor container health
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:5000/health || exit 1

# Start your app
CMD ["npm", "start"]
