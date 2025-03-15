# Build stage
FROM rust:1.74-slim-bullseye as builder

# Install build dependencies
RUN apt-get update && apt-get install -y \
    pkg-config \
    gcc \
    g++ \
    make \
    git \
    && rm -rf /var/cache/apt/*

WORKDIR /tmkms

# Copy source code
COPY . .

# Set RUSTFLAGS for x86_64 optimization
ENV RUSTFLAGS="-Ctarget-feature=+aes,+ssse3"

# Build TMKMS with softsign feature only
RUN cargo build --release --features=softsign

# Runtime stage
FROM debian:bullseye-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    ca-certificates \
    && rm -rf /var/cache/apt/*

# Create tmkms user
RUN useradd -m -s /bin/bash tmkms

# Copy the binary from builder
COPY --from=builder /tmkms/target/release/tmkms /usr/local/bin/
RUN chmod +x /usr/local/bin/tmkms

# Switch to tmkms user
USER tmkms
WORKDIR /home/tmkms/

# Default command (can be overridden)
ENTRYPOINT ["tmkms"]
CMD ["start"]
