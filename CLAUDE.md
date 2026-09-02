# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```
mvn clean package    # build target/direct-project-tools-<version>.tar.gz
mvn clean deploy      # publish to Maven Central (org.nhind release site)
```

There is no test suite, no lint step, and no application code to run here — see below.

## What this repository is

This is a **packaging-only Maven project** (`<packaging>pom</packaging>`). It contains **no Java source**. Every tool's actual implementation lives in the upstream released `org.nhind` component libraries:

| Tool launcher | Main class | Comes from |
|---|---|---|
| `certGen` | `org.nhindirect.stagent.cert.tools.certgen.DirectProjectCertGenerator` | `agent` |
| `dnsCertDumper` | `org.nhindirect.stagent.cert.tools.DNSCertDumper` | `agent` |
| `ldapCertDumper` | `org.nhindirect.stagent.cert.tools.LDAPCertDumper` | `agent` |
| `createPKCS12` | `org.nhindirect.stagent.cert.tools.CreatePKCS12` | `agent` |
| `stripP12Pass` | `org.nhindirect.stagent.cert.tools.StripP12Passphrase` | `agent` |
| `policyBuilder` | `org.nhindirect.policy.tools.policybuild.SimpleTextPolicyBuilder` | `direct-policy` |
| `keyStoreMgr` | `org.nhindirect.common.crypto.tools.PKCS11SecretKeyManager` | `direct-common` |
| `configManager` | (Spring Boot fat jar, `java -jar`) | `config-manager` |

To change tool behavior you must change the upstream library and release a new version, then bump the coordinate here.

## Build architecture

`mvn package` runs three plugins in the `package` phase:

1. **maven-dependency-plugin / `copy-tool-dependencies`** (`copy-dependencies` goal) — copies the runtime dependency trees of `agent`, `direct-policy`, and `direct-common` into `target/tools/lib/`. It carries a large `<excludeGroupIds>` list: `direct-common` pulls a Spring WebFlux/Netty/Feign stack for its config-service REST client that none of these CLI tools use, so those trees are pruned to keep `tools/lib` lean. **If a tool ever needs a runtime dependency whose groupId is on that exclude list, it will silently be missing from the distribution** — the list must be kept in sync with what the tools actually load.
2. **maven-dependency-plugin / `copy-config-manager`** (`copy` goal) — fetches the single `org.nhind:config-manager` jar by exact coordinates and renames it to `config-manager.jar`. It is handled separately, not as a `<dependency>`, because it is a self-contained executable Spring Boot fat jar launched with `java -jar` (which ignores `-classpath`); adding it as a normal dependency would resolve its entire transitive compile tree into the reactor and splat redundant/conflicting loose jars into `tools/lib`. The launcher hard-codes the `config-manager.jar` filename.
3. **maven-assembly-plugin** with `src/descriptors/distribution.xml` — bundles `target/tools/` (the libs) plus each `src/tools/*.sh` / `*.bat` launcher into the final `tar.gz`.

Two runtime models in the shipped distribution: the classpath tools run `java -classpath .:./lib/*  <MainClass>`; `configManager` runs `java -jar ./lib/config-manager.jar`.

## Adding or changing a tool

1. Add `src/tools/<name>.sh` and `src/tools/<name>.bat` (mirror an existing pair).
2. Add both files to the `<files>` list in `src/descriptors/distribution.xml` (the `.sh` entry needs `<fileMode>777</fileMode>`).
3. If it introduces a new upstream library, add a `<dependency>` in `pom.xml` and verify no needed runtime groupId is caught by the `excludeGroupIds` list.

## Versioning

The `9.0.0` component versions and the project `<version>` are maintained by hand in `pom.xml` (including the `copy-config-manager` `<artifactItem>` version). The parent is `spring-boot-dependencies` for dependency and plugin version management.
