# rep-web-project/frontend/Dockerfile
# Multi-stage build for a lean production image

# Stage 1: Build the Next.js application
FROM node:20-alpine AS builder
WORKDIR /app
# Install dependencies
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile

# Copy source code and build
COPY . .
# คำสั่ง build จะสร้าง folder .next/standalone ตามที่เราตั้งค่า output: 'standalone' ใน next.config.js
RUN npx next build

# Stage 2: Minimal runtime environment (Production)
FROM node:20-alpine AS runner
WORKDIR /app

# ตั้งค่า user เพื่อความปลอดภัย
RUN addgroup -g 1001 nodejs
RUN adduser -u 1001 nextjs -G nodejs
USER nextjs

# Copy files needed for running
COPY --from=builder --chown=nextjs:nodejs /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/package.json ./package.json

# Copy the standalone server and static assets
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# Start the application
EXPOSE 3000
ENV PORT 3000
CMD ["node", "server.js"]