---
title: "Verify SLSA Provenance (Keyless)"
category: Sample
version: 1.7.0
subject: Pod
policyType: "verifyImages"
description: >
    Provenance is used to identify how an artifact was produced and from where it originated. SLSA provenance is an industry-standard method of representing that provenance. This policy verifies that an image has SLSA provenance and was signed by the expected subject and issuer when produced through GitHub Actions. It requires configuration based upon your own values.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/verify_image_slsa.yaml" target="-blank">/other/verify_image_slsa.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-slsa-provenance-keyless
  annotations:
    policies.kyverno.io/title: Verify SLSA Provenance (Keyless)
    policies.kyverno.io/category: Sample
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.7.0
    kyverno.io/kyverno-version: 1.7.2
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >-
      Provenance is used to identify how an artifact was produced
      and from where it originated. SLSA provenance is an industry-standard
      method of representing that provenance. This policy verifies that an
      image has SLSA provenance and was signed by the expected subject and issuer
      when produced through GitHub Actions. It requires configuration based upon
      your own values.
spec:
  validationFailureAction: enforce
  webhookTimeoutSeconds: 30
  rules:
    - name: check-slsa-keyless
      match:
        any:
        - resources:
            kinds:
              - Pod
      verifyImages:
      - imageReferences:
        - "myreg.org/path/repo:*"
        attestors:
        - entries:
          - keyless:
            # In the case of GitHub Actions, the subject will be set to your Actions workflow responsible
            # for kicking off the process. Note that this will not be set to the external action
            # responsible for the provenance generation.
              subject: "https://github.com/myname/myrepo/.github/workflows/my-workflow.yaml@refs/heads/mybranch"
              issuer: "https://token.actions.githubusercontent.com"
        attestations:
        - predicateType: https://slsa.dev/provenance/v0.2
          conditions:
          - all:
            - key: "{{ invocation.configSource.uri }}"
              operator: Equals
              value: "git+https://github.com/myname/myrepo@refs/heads/mybranch"
            - key: "{{ invocation.configSource.entryPoint }}"
              operator: Equals
              value: ".github/workflows/my-workflow.yaml"
            - key: "{{ regex_match('^https://github.com/slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@refs/tags/v[0-9].[0-9].[0-9]$','{{ builder.id}}') }}"
              operator: Equals
              value: true

```
