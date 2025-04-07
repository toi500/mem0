# ---- Builder Stage ----
FROM node:20-alpine AS builder

# Install pnpm
RUN npm install -g pnpm

WORKDIR /usr/src/app

# Copy package manifests
COPY package.json pnpm-lock.yaml ./

# Install all dependencies (including devDependencies needed for build)
RUN pnpm install --frozen-lockfile

# Copy the rest of the source code
COPY . .

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
COPY --from=builder /usr/src/app/src ./src # Copy src if needed by the start script (e.g., ts-node examples)

# Expose a port if your application listens on one (adjust if necessary)
# EXPOSE 3000

# Define the command to run.
# This uses the "start" script from package.json which runs an example.
# Adjust this CMD if you need to run a different script or application entry point.
# If this library isn't meant to run as a standalone service, you might remove the CMD
# or change it to something like CMD ["node"] for an interactive prompt.
CMD ["pnpm", "run", "example"]
