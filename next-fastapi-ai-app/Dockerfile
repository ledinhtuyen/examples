# Stage 1: Build Next.js application
FROM node:20-slim AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production environment
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/*

# Create and activate virtual environment
RUN python -m venv .venv
ENV PATH="/app/.venv/bin:$PATH"
ENV VIRTUAL_ENV="/app/.venv"

# Copy built Next.js files and other necessary files
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
COPY requirements.txt ./
COPY api ./api
COPY start.sh ./
COPY .env.local ./
RUN chmod +x start.sh

# Install production dependencies
RUN npm install --only=production --omit=dev
RUN pip install --no-cache-dir -r requirements.txt

# Set production environment
ENV NODE_ENV=production
ENV PORT=3000

EXPOSE 3000
EXPOSE 8000

# Start both Next.js and FastAPI
CMD ["./start.sh"]
