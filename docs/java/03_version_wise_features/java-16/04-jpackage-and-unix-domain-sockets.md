---
title: jpackage and Unix-Domain Socket Channels
parent: Java-16
nav_order: 4
---

# `jpackage` and Unix-Domain Socket Channels

## `jpackage`

Java 16 standardized `jpackage`, which produces native application packages such as MSI, PKG, DEB, and RPM. It is most relevant to desktop/server distribution and internal runtime packaging, not ordinary container-image builds.

```bash
jpackage --type deb --name payments-worker \
  --input build/libs --main-jar payments-worker.jar \
  --main-class com.example.Worker
```

### Production guidance

- Build packages in CI for the target OS and architecture; packages are not universally portable.
- Keep runtime images, JDK patches, signing, service-user setup, and rollback plans explicit.
- For Kubernetes-style deployment, compare the operational cost with a minimal container/JRE image before adopting `jpackage`.
- Test install, upgrade, uninstall, log locations, configuration paths, and service startup behavior.

## Unix-domain socket channels

Java 16 adds `AF_UNIX` support to NIO `SocketChannel` and `ServerSocketChannel`. Unix-domain sockets provide local inter-process communication addressed by filesystem paths rather than IP address and port.

```
var address = UnixDomainSocketAddress.of("/var/run/payments.sock");
try (ServerSocketChannel server = ServerSocketChannel.open(StandardProtocolFamily.UNIX)) {
    server.bind(address);
    try (SocketChannel client = server.accept()) {
        // read/write with the normal channel APIs
    }
}
```

## Why use a Unix-domain socket

- The peer must be on the same host.
- Filesystem permissions provide an additional operating-system access-control boundary.
- Local IPC can avoid loopback TCP overhead.
- NIO channel/selector semantics remain familiar.

## Edge cases and negative cases

- Do not cast every `SocketAddress` to `InetSocketAddress`; Unix-domain addresses use `UnixDomainSocketAddress`.
- Socket-path length and platform support vary; test the deployment OS.
- Remove stale socket files safely during controlled startup/shutdown.
- Shared-volume/container permissions determine whether another process can connect.
- There is no remote access: use TCP or another transport for cross-host communication.

## Daily backend example

Use a Unix-domain socket for a local sidecar, proxy, or agent only when both processes are intentionally co-located:

```
var address = UnixDomainSocketAddress.of("/var/run/metrics-agent.sock");
try (SocketChannel channel = SocketChannel.open(StandardProtocolFamily.UNIX)) {
    channel.connect(address);
    channel.write(StandardCharsets.UTF_8.encode("flush\n"));
}
```

Enforce filesystem ownership and mode bits; do not treat local IPC as automatically authenticated.

