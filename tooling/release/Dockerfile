# syntax=docker/dockerfile:1
# Reproducible, offline-runtime Soroban artifact: amd64 + Rust 1.84.1 are
# fixed by an exact Docker Hub manifest-list digest (not a mutable tag).
FROM --platform=linux/amd64 rust:1.84-slim-bookworm@sha256:69fbd6ab81b514580bc14f35323fecb09feba9e74c5944ece9a70d9a2a369df0 AS builder
WORKDIR /workspace

RUN rustup target add wasm32v1-none

# Only copy the actual build inputs. Cargo.lock pins Rust dependencies;
# --locked fails rather than accepting an unreviewed dependency change.
COPY Cargo.toml Cargo.lock build.rs ./
COPY src/ ./src/

ENV CARGO_INCREMENTAL=0 \
    SOURCE_DATE_EPOCH=0 \
    TZ=UTC
RUN cargo build --locked --release --target wasm32v1-none

FROM scratch
COPY --from=builder /workspace/target/wasm32v1-none/release/task_manager_pro.wasm /task_manager_pro.wasm
