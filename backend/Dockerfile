# ---------- Build Stage ----------
FROM node:13.12.0-alpine AS builder

# Build argument for environment
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

WORKDIR /app

# Copy only package files first to leverage Docker layer cache
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production && npm cache clean --force

# Copy application source code
COPY . .

# ---------- Runtime Stage ----------
FROM node:13.12.0-alpine

# Set environment from build arg
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

WORKDIR /app

# Install curl for health checks
RUN apk add --no-cache curl

# Create a non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Copy the built app and deps from the builder stage
COPY --from=builder --chown=nodejs:nodejs /app /app

USER nodejs

# Expose your app's port
EXPOSE 5000

# Start your app
CMD ["npm", "start"]
