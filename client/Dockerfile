# --- Stage 1: Build the React static assets ---
FROM node:24.18.0 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# --- Stage 2: Serve with Nginx ---
FROM nginx:alpine
# Copy built React files into Nginx public folder
COPY --from=builder /app/dist /usr/share/nginx/html

# COPY YOUR CUSTOM NGINX CONFIG HERE:
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]