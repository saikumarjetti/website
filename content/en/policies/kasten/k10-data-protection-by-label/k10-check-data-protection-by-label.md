---
title: "Check Data Protection By Label"
category: Kasten K10 by Veeam
version: 1.6.2
subject: Deployment, StatefulSet
policyType: "validate"
description: >
    Check the 'dataprotection' label that production Deployments and StatefulSet have a named K10 Policy. Use in combination with 'generate' ClusterPolicy to 'generate' a specific K10 Policy by name.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//kasten/k10-data-protection-by-label/k10-check-data-protection-by-label.yaml" target="-blank">/kasten/k10-data-protection-by-label/k10-check-data-protection-by-label.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: k10-data-protection-by-label
  annotations:
    policies.kyverno.io/title: Check Data Protection By Label
    policies.kyverno.io/category: Kasten K10 by Veeam
    kyverno.io/kyverno-version: 1.6.2
    policies.kyverno.io/minversion: 1.6.2
    kyverno.io/kubernetes-version: "1.21-1.22"
    policies.kyverno.io/subject: Deployment, StatefulSet
    policies.kyverno.io/description: >-
      Check the 'dataprotection' label that production Deployments and StatefulSet have a named K10 Policy.
      Use in combination with 'generate' ClusterPolicy to 'generate' a specific K10 Policy by name.
spec:
  validationFailureAction: audit
  rules:
  - name: k10-data-protection-by-label
    match:
      any: 
      - resources:
          kinds:
          - Deployment
          - StatefulSet
          selector:
            matchLabels:
              purpose: production
    validate:
      message: "Deployments and StatefulSets that specify 'dataprotection' label must have a valid k10-?* name (use labels: dataprotection: k10-<policyname>)"
      pattern:
        metadata:
          labels:
            dataprotection: "k10-*"

```
