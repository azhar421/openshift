# Use Node 18 as the base image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json first for caching
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the full source code
COPY . .

# Fix: Set legacy OpenSSL provider for Webpack compatibility
ENV NODE_OPTIONS=--openssl-legacy-provider

# Build the application
RUN npm run build

# Install serve globally to serve the production build
RUN yarn global add serve

# Expose the default port
EXPOSE 3000

# Command to serve the build
CMD ["serve", "-s", "build", "-l", "3000"]
