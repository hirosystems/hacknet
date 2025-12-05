FROM node:20-bookworm AS builder

# This will be overridden by the value from docker-compose
# Supports: branch name, tag, or commit SHA
# Examples:
#   STACKS_API_BRANCH=develop           (branch)
#   STACKS_API_BRANCH=v8.13.4           (tag)
#   STACKS_API_BRANCH=abc123def456      (commit)
ARG STACKS_API_BRANCH=master

RUN echo "Building Stacks API from: $STACKS_API_BRANCH"

# Clone efficiently: shallow for branches/tags, targeted fetch for commits
# Also fetch tags (required for npm run generate:git-info)
RUN git init /code/stacks-api && \
    cd /code/stacks-api && \
    git remote add origin https://github.com/hirosystems/stacks-blockchain-api.git && \
    git fetch --depth=1 origin $STACKS_API_BRANCH && \
    git checkout FETCH_HEAD && \
    git fetch --tags --depth=1

WORKDIR /code/stacks-api

# Install dependencies and build
RUN npm ci && npm run build

# Prune dev dependencies for smaller final image
RUN npm prune --production

FROM node:20-bookworm-slim

WORKDIR /app

# Copy the entire built application
COPY --from=builder /code/stacks-api ./

# Default API ports
EXPOSE 3700 3999

STOPSIGNAL SIGTERM

CMD ["node", "./lib/index.js"]

