# UERANSIM v3.3.0 (aligungr/UERANSIM, pinned tag v3.3.0)
# NOTE: official repo ships NO Dockerfile (verified on v3.3.0 tag AND master, 2026-08).
#       Build with context = repo ROOT, -f pointing into docker/:
#         docker build -t ueransim:v3.3.0 -f <repo>/docker/Dockerfile <repo>
# gNB run: --network host (N2 SCTP / N3 GTP-U)
# UE  run: --device /dev/net/tun --cap-add NET_ADMIN
FROM ubuntu:22.04 AS builder

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    ca-certificates cmake make g++ pkg-config \
    libsctp-dev libyaml-cpp-dev libgtest-dev libssl-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /ueransim
COPY . .

WORKDIR /ueransim/build
RUN cmake .. && make -j"$(nproc)"

FROM ubuntu:22.04

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    libsctp1 libyaml-cpp0.7 iproute2 iptables kmod \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /ueransim/build/nr-gnb /usr/local/bin/nr-gnb
COPY --from=builder /ueransim/build/nr-ue  /usr/local/bin/nr-ue
COPY --from=builder /ueransim/build/nr-cli /usr/local/bin/nr-cli

WORKDIR /ueransim
CMD ["bash"]
