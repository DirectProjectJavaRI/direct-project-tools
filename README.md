# direct-project-tools

Command line tools for the Direct Project Java Reference Implementation: certificate generation, DNS/LDAP certificate dumping, PKCS12/keystore management, and policy building. This is the `tools` subset of [direct-project-stock](https://github.com/DirectProjectJavaRI/direct-project-stock), split out as its own distributable, built against the released `org.nhind` component libraries.

## Building

```
mvn clean package
```

Produces `target/direct-project-tools-<version>.tar.gz`, containing a `tools/` directory with each tool's `.sh`/`.bat` launcher and a `tools/lib/` directory with all required jars.

## Deploying

```
mvn clean deploy
```

Publishes to the same Maven Central deployment site used by the other `org.nhind` Direct Project Java RI modules (e.g. [agent](https://github.com/DirectProjectJavaRI/agent)).
