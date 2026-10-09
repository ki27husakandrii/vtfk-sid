# --- Етап 1: Статична збірка musl із кешуванням залежностей ---
FROM rust:1.79-alpine AS build
WORKDIR /app
RUN apk add --no-cache musl-dev

# Кешування: шари залежностей ідуть до реального коду
COPY ../sid/Cargo.toml ../sid/Cargo.lock* ./
RUN mkdir src && echo "fn main() {}" > src/main.rs \
    && cargo build --release --target x86_64-unknown-linux-musl \
    && rm -rf src

# Реальний код додається останнім
COPY ../sid/src ./src
RUN touch src/main.rs && cargo build --release --target x86_64-unknown-linux-musl

# --- Етап 2: Рівень 3 (абсолютно чистий scratch) ---
FROM scratch
COPY --from=build /app/target/x86_64-unknown-linux-musl/release/sid /sid
ENTRYPOINT ["/sid"]
