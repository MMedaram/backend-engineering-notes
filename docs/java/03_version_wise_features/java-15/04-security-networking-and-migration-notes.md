---
title: Security, Networking, and Migration Notes
parent: Java-15
nav_order: 4
---

# Java 15 Security, Networking, and Migration Notes

## Overview

Java 15 added EdDSA support, reimplemented the legacy DatagramSocket API, removed Nashorn and Solaris/SPARC ports, and deprecated RMI Activation for removal. These are primarily upgrade and compatibility topics for backend teams.

## EdDSA

Java 15 adds the Edwards-Curve Digital Signature Algorithm through the standard JCA/JCE APIs. Ed25519 and Ed448 provide modern digital-signature options with strong security properties and compact keys/signatures.

```
KeyPairGenerator generator = KeyPairGenerator.getInstance("Ed25519");
KeyPair keyPair = generator.generateKeyPair();

Signature signer = Signature.getInstance("Ed25519");
signer.initSign(keyPair.getPrivate());
signer.update(payload);
byte[] signature = signer.sign();
```

### Production guidance

- Choose algorithms from a documented security policy; do not change signing algorithms ad hoc.
- Store private keys in a KMS, HSM, or protected secret-management system—not configuration files.
- Include algorithm and key identifiers in signed-message protocols to support rotation.
- Test interoperability with clients, identity providers, and certificates before rollout.
- Signature verification confirms integrity/authenticity; it does not provide encryption or authorization.

## Legacy DatagramSocket API reimplementation

Java 15 reimplemented the legacy `java.net.DatagramSocket` and `MulticastSocket` implementation. Source compatibility was intended to remain high, but network-heavy systems should test behavior, timeouts, multicast, socket options, and platform-specific deployment assumptions after a JDK upgrade.

### Backend relevance

Most HTTP services do not use UDP directly. Relevant cases include service discovery, telemetry agents, DNS-like protocols, multicast, game/IoT gateways, and infrastructure integrations. Prefer integration tests on the operating systems and container/network policies used in production.

## Nashorn removal

Nashorn and the `jjs` tool were removed in Java 15. Applications that execute JavaScript through `javax.script`, depend on `jdk.nashorn.*`, or use `jjs` in automation require a migration plan.

### Upgrade checklist

1. Search source, tests, build scripts, and runtime plugins for `Nashorn`, `ScriptEngine`, `jjs`, and `jdk.nashorn`.
2. Classify the use: build-time script, configuration expression, plugin engine, or application feature.
3. Replace it with a maintained external engine only after evaluating sandboxing, performance, licensing, and operational support.
4. Do not execute untrusted scripts in the application JVM without strong isolation.

## Solaris and SPARC ports removed

Java 15 removed the Solaris and SPARC ports. Teams deploying to these platforms need a vendor-supported alternative JDK/platform strategy. This is a build and infrastructure compatibility concern rather than a Java source-code change.

## RMI Activation deprecated for removal

RMI Activation was deprecated for removal. Existing systems should plan to migrate away from activation-daemon-based lifecycle management.

For new backend services, prefer explicit process orchestration, container platforms, health checks, service discovery, and modern RPC/messaging designs rather than RMI Activation.

## Daily migration sample

```java
// Before: indirect script-engine dependency that may have relied on Nashorn.
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");

// Migration decision: remove the scripting requirement, or isolate a supported
// external engine behind a narrowly defined adapter with explicit sandboxing.
```

The correct replacement is business-specific. Avoid a mechanical engine swap without a security review.

## Common mistakes

1. Treating EdDSA as a drop-in replacement without protocol/interoperability tests.
2. Logging private keys, signed payloads, or sensitive signature failures.
3. Assuming DatagramSocket reimplementation needs no regression testing.
4. Replacing Nashorn with an unmaintained or unsandboxed script engine.
5. Ignoring Solaris/SPARC removal until a deployment image fails to build.
6. Treating RMI Activation deprecation as a reason to retain obsolete lifecycle architecture.

