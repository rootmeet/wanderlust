# -------------------- Build Stage --------------------------------
FROM node:21 as frontend-builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

# -------------------- Final Stage --------------------------------
FROM node:21-alpine

WORKDIR /app

# Copy stage 1 compress
COPY --from=frontend-builder /app .

COPY .env.sample .env.local

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host"]
