# ---------- Build Stage ----------
FROM node:13.12.0-alpine AS builder    

# Set working directory
WORKDIR /app

# Copy dependency files
COPY package*.json ./  
# Install clean dependencies
RUN npm ci --legacy-peer-deps && npm cache clean --force

# Copy all source files
COPY . .
RUN npm run build

# ---------- Production Stage ----------
FROM nginx:1.25.3-alpine

# Copy custom Nginx config for routing SPA requests
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy built frontend app to Nginx public directory
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port used by Nginx
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
