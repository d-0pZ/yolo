# ---------- Build Stage ----------
FROM node:13.12.0-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --legacy-peer-deps && npm cache clean --force

COPY . .
RUN npm run build

# ---------- Production Stage ----------
FROM nginx:1.25.3-alpine

# Copy custom Nginx config for better performance
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy build output to Nginx html folder
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port used by Nginx
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
