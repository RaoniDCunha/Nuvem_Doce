
# Stage 1: Install dependencies
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN 
  if [ -f yarn.lock ]; then yarn install --frozen-lockfile; 
  elif [ -f pnpm-lock.yaml ]; then pnpm install --frozen-lockfile; 
  else npm install --frozen-lockfile; 
  fi

# Stage 2: Build the application
FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Stage 3: Run the application
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV production
# If you're not using a custom server, only copy the .next directory,
# otherwise, copy your custom server files.
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

EXPOSE 3000
CMD ["npm", "start"]
