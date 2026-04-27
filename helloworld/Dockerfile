# Stage 1: runtime image（精简）
FROM debian:bookworm-slim

# 运行时需要的最小包（仅 CA 证书）
RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl jq xz-utils \
    && apt-get remove --purge --auto-remove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ARG NAIVE_VERSION
# 下载编译好的文件
RUN set -eux; \
    case "$TARGETARCH" in \
      amd64) ARCH="x64" ;; \
      arm64) ARCH="arm64" ;; \
      *) echo "Unsupported arch: $TARGETARCH" && exit 1 ;; \
    esac; \
    URL=$(curl -s "https://api.github.com/repos/klzgrad/naiveproxy/releases/tags/${NAIVE_VERSION}" \
      | jq -r ".assets[] | select(.name | test(\"^naiveproxy-.*-linux-${ARCH}\\\\..*\")) | .browser_download_url") ; \
    curl -L -o naiveproxy.tar.xz "$URL"; \
    tar -xvf naiveproxy.tar.xz; \
    rm -f naiveproxy.tar.xz; \
    mv naiveproxy-*-linux-${ARCH} /app

RUN chmod +x /app/naive

# ENTRYPOINT 指向可执行，CMD 提供默认参数（可被追加参数覆盖）
ENTRYPOINT ["/app/naive"]
CMD ["-c", "/app/config.json"]

# 备忘：
# build之后，获取tag:
# curl -s https://api.github.com/repos/klzgrad/naiveproxy/releases/latest | jq -r '.tag_name'
