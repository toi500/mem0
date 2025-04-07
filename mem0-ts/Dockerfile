# ---- Builder Stage ----
FROM node:20-alpine AS builder

# Install pnpm
RUN npm install -g pnpm

WORKDIR /usr/src/app

# Copy package manifests from the mem0-ts directory relative to the build context
COPY mem0-ts/package.json mem0-ts/pnpm-lock.yaml ./

# Install all dependencies (including devDependencies needed for build)
RUN pnpm install --frozen-lockfile

# Copy the rest of the source code from the mem0-ts directory relative to the build context
COPY mem0-ts/. .

# Run the build script defined in package.json
RUN pnpm run build

# Prune devDependencies for the final stage
RUN pnpm prune --prod


# ---- Final Stage ----
FROM node:20-alpine

# Install pnpm (needed if the CMD uses pnpm)
RUN npm install -g pnpm

WORKDIR /usr/src/app

# Copy only necessary files from the builder stage
COPY --from=builder /usr/src/app/package.json /usr/src/app/pnpm-lock.yaml ./
COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /usr/src/app/dist ./dist
# Copy src if needed by the start script (e.g., ts-node examples)
# Note: The path inside the builder container is still /usr/src/app/src
COPY --from=builder /usr/src/app/src ./src

# Expose a port if your application listens on one (adjust if necessary)
# EXPOSE 3000

# Define the command to run.
# This uses the "example" script from package.json. Adjust if needed.
CMD ["pnpm", "run", "example"]
