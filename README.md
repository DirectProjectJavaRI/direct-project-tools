# direct-project-tools

Command line tools for the Direct Project Java Reference Implementation: certificate
generation, DNS/LDAP certificate dumping, PKCS12/keystore management, policy building,
and configuration service administration. This is the `tools` subset of
[direct-project-stock](https://github.com/DirectProjectJavaRI/direct-project-stock),
split out as its own distributable, built against the released `org.nhind` component
libraries.

## Tools

| Tool | Description |
|------|-------------|
| `certGen` | Interactive generator for X.509 certificates (CA / trust anchor and end-entity) used for Direct addressing. |
| `dnsCertDumper` | Retrieves and dumps the X.509 certificates published in DNS (CERT records) for a Direct address or domain. |
| `ldapCertDumper` | Retrieves and dumps the X.509 certificates published in an LDAP directory for a Direct address or domain. |
| `createPKCS12` | Combines a certificate and its private key into a password-protected PKCS#12 (`.p12`) file. |
| `stripP12Pass` | Produces a copy of a PKCS#12 file with the passphrase removed. |
| `policyBuilder` | Interactive builder that compiles Direct trust policy from the simple-text policy lexicon. |
| `keyStoreMgr` | Manages secret keys held in a PKCS#11 token / HSM (used to protect keystore credentials). |
| `configManager` | Interactive Configuration Management Console for administering a Direct configuration service (domains, addresses, certificates, trust anchors, settings). |

## Building

```
mvn clean package
```

Produces `target/direct-project-tools-<version>.tar.gz`, containing a `tools/`
directory with each tool's `.sh`/`.bat` launcher and a `tools/lib/` directory with
all required jars.

## Unboxing

Requires a Java runtime on the `PATH` (`java -version`).

Extract the distribution archive:

```
tar xzf direct-project-tools-<version>.tar.gz
cd tools
```

This gives you the `.sh` and `.bat` launchers alongside a `lib/` directory holding
every jar the tools need. The `.sh` scripts are already marked executable.

## Running

Run each tool from inside the unpacked `tools/` directory so the launchers resolve
`./lib` correctly.

On macOS / Linux:

```
./certGen.sh
./dnsCertDumper.sh <args>
./configManager.sh <args>
```

On Windows:

```
certGen.bat
dnsCertDumper.bat <args>
configManager.bat <args>
```

`certGen` and `policyBuilder` are fully interactive and take no arguments. The
remaining tools accept command-line arguments that are passed straight through to
the underlying utility; run a tool with no arguments to see its usage.
