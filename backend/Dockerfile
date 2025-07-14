# ---------- Build Stage ----------
# Use Alpine for a small footprint
FROM node:13.12.0-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

COPY . .

# ---------- Runtime Stage ----------
# Lightweight production environment(node-alpine)
FROM node:13.12.0-alpine

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && adduser -S nodeuser -u 1001

WORKDIR /app

# Copy only production dependencies and source code from builder stage
COPY --from=builder --chown=nodeuser:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodeuser:nodejs /app/package*.json ./
COPY --from=builder --chown=nodeuser:nodejs /app/server.js ./
COPY --from=builder --chown=nodeuser:nodejs /app/models ./models/
COPY --from=builder --chown=nodeuser:nodejs /app/routes ./routes/
COPY --from=builder --chown=nodeuser:nodejs /app/upload.js ./

# Create persistent uploads directory with proper permissions
RUN mkdir -p uploads && chown nodeuser:nodejs uploads

# Run app as non-root user
USER nodeuser

# Expose backend port
EXPOSE 5000

# Start the backend server
CMD ["npm", "start"]
