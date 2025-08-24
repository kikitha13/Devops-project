# Stage 1: Build the Angular app
FROM node:18 AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build --prod

# Stage 2: Serve app with Nginx
FROM nginx:alpine

# Copy the built Angular app from the previous stage
COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start Nginx server

CMD ["nginx", "-g", "daemon off;"]