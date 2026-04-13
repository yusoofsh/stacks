# Stage 1: Build Caddy with modules
FROM caddy:builder AS builder

# Make Go downloads more reliable
ENV GOPROXY=https://proxy.golang.org,direct \
    GOSUMDB=sum.golang.org \
    CGO_ENABLED=0

# Use BuildKit cache for Go modules + build cache
RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    xcaddy build \
        --with github.com/mholt/caddy-l4 \
        --with github.com/caddy-dns/cloudflare \
        --with github.com/WeidiDeng/caddy-cloudflare-ip

# Stage 2: Final minimal image
FROM caddy:latest

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
