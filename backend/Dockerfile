# Stage 1
FROM node:21 AS backend-builder

# Setup working dir
WORKDIR /app

# Copy code
COPY . .

# Run dependencies
RUN npm i

FROM node:21-alpine

# Setup working dir
WORKDIR /app

# Copy stage 1 compress
COPY --from=backend-builder /app .

# Copy envi variables
COPY .env.sample .env

# expose port
EXPOSE 5000

# run application
CMD ["npm", "start"]
