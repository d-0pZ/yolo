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
FROM node:13.12.0-alpine

# Build argument for environment
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

# Set working directory
WORKDIR /app

# Install serve globally to serve the React build
RUN apk add --no-cache curl && \
    npm install -g serve@11.3.2

# Create non-root user for security
RUN addgroup -g 1001 -S reactuser && \
    adduser -S reactuser -u 1001 -G reactuser

# Copy built React application from builder stage
COPY --from=builder --chown=reactuser:reactuser /app/build /app/build

# Switch to non-root user
USER reactuser

# Expose port 3001
EXPOSE 3001

# Start React app using serve
CMD ["serve", "-s", "build", "-l", "3001"]
