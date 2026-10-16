# Envoy Gateway & Gateway API Studio (tp-envoy-gateway)

[![SLSA Level 3](https://img.shields.io/badge/SLSA-Level%203-success?style=flat-square&logo=sigstore)](https://slsa.dev)
[![DevSecOps 9+](https://img.shields.io/badge/DevSecOps-9%2B%20Pillars-blue?style=flat-square&logo=kubernetes)](https://talaripradeep.info)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)

> **Production Repository:** [`Pradeeptalari14/tp-envoy-gateway`](https://github.com/Pradeeptalari14/tp-envoy-gateway)  
> **Interactive Studio Workspace:** [https://talaripradeep.info/tools/envoy-gateway/](https://talaripradeep.info/tools/envoy-gateway/)  
> **Visual Flow Canvas:** [https://talaripradeep.info/studios/flow/?studio=envoy-gateway](https://talaripradeep.info/studios/flow/?studio=envoy-gateway)  
> **Architecture Center:** [https://talaripradeep.info/architecture/](https://talaripradeep.info/architecture/)

---

## 🏛️ Overview
Kubernetes Gateway API controller powered by Envoy Proxy. Declarative HTTPRoute, canary traffic splitting, and OIDC security policies.

This repository is built following the **9+ DevSecOps / SRE Governance Principles**:
1. **Cosign SLSA L3**: Keyless container and manifest attestation recorded to Rekor.
2. **Semgrep AST Guardrails**: Pre-commit AST rules preventing taint paths and plaintext credentials.
3. **CycloneDX SBOM**: Syft-generated Software Bill of Materials with Grype zero High/Critical CVE gating.
4. **Kyverno Admission Policies**: Enforcing CIS Kubernetes hardening, non-root users, and read-only filesystems.
5. **eBPF In-Kernel Runtime Observability**: Tetragon tracing and live syscall profiling.
6. **GitOps Zero-Drift Synchronization**: ArgoCD/Flux continuous declarative reconciliation.
7. **MELT Golden Signals**: OpenTelemetry traces, Prometheus metrics, and structured logging.
8. **Automated SRE Remediation**: Self-healing circuits and SLO compliance.
9. **Zero-Trust Microsegmentation**: Strict SPIFFE/mTLS and encrypted storage volumes.

---

## 📄 Primary Configuration: `gateway.yaml`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: eg
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
---
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: eg-gateway
  namespace: envoy-gateway-system
spec:
  gatewayClassName: eg
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      tls:
        mode: Terminate
        certificateRefs:
          - kind: Secret
            name: tls-wildcard-cert
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: production-canary-route
  namespace: default
spec:
  parentRefs:
    - name: eg-gateway
      namespace: envoy-gateway-system
  rules:
    - backendRefs:
        - name: payments-v1
          port: 8080
          weight: 90
        - name: payments-v2
          port: 8080
          weight: 10

```

---

## 🚀 Quickstart & Verification

```bash
# 1. Clone repository
git clone https://github.com/Pradeeptalari14/tp-envoy-gateway.git
cd tp-envoy-gateway

# 2. Run verification drill
bash scripts/validate.sh
```

---

## 📜 License
MIT License. Copyright (c) 2026 Talari Pradeep. All rights reserved.
