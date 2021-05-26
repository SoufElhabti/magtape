# Change Log

## v2.1.0

### Overview

This is the intial public release of MagTape. Versions < 2.1.0 were internal only.

## v2.1.1

### Fixes

- Fixed a mistake with the URL used for installation in the Quickstart section of the Readme

## v2.1.2

This release contains several package updates geared towards fixing security related issues with [CVE-2017-18342](https://nvd.nist.gov/vuln/detail/CVE-2017-18342).

 The updated pyyaml package required updates to the Kubernetes Python client library, moving primary support to Kubernetes 1.15+. Backwards compatibility to Kubernetes 1.13 should exist, but isn't tested/gauranteed.

## v2.1.3

This release migrates to using the Gunicorn WSGI HTTP Server instead of the default Flask server. This change reduces average latency by about 75% in our normal benchmarking tests. This change also means the standar 3 replica deployment can handle almost 3 times the request rate as before.

## v2.1.4

This release adds the `approve` verb to the RBAC config to account for newer changes to the Kubernetes certificates/CSR API as noted [here](https://github.com/kubernetes/kubernetes/pull/86933). These changes were tested against K8s 1.14, 1.15, 1.16, 1.17, and 1.18.

## v2.1.5

This release adds new policies and enhances several CI workflow components.

### New Policies

- Singleton Pods (Check ID: MT1007)
- Host Port (Check ID: MT1008)
- emptyDir Volume (Check ID: MT1009)
- Host Path (Check ID: MT1010)
- Node Port Range (Check ID: MT2002)

### New CI Features

- Kubernetes Matrix for end-to-end testing. All commits/PR's are now tested against Kubernetes 1.16, 1.17, 1.18, and 1.19
- Rego linting and unit tests
- Code quality anallysis and static code scanning for Security/Best Practices

### Misc Enhancements

- Enhancements for Advanced install workflow with Kustomize

## v2.2.0

This release focuses on some security enhancements.

### Enhancements

- Add securityContext and non-root user for pod/containers (#47)
- Hardcode Gunicorn workers/threads to fix #48 (#49)
- Add HPA resource for horizontal scaling (#50)
- Add new framework for executing setup/teardown code between functional tests (#45)

### Misc Notes

- Changes OPA container listening port from `443` to `8443` since a non-root user can't bind to ports below 1000. The OPA container isn't exposed outside of localhost, so this shouldn't present any issues

## v2.3.0

This release has a breaking change, changes to RBAC, some new features, CI enhancements, changes to test mocking, and some updates to documentation.

### Breaking Changes

- the `MAGTAPE_SLACK_ANNOTATION` environment variable has been removed and is no longer used for enabling user-defined slack alerts.

**user-defined slack alerts**

For better security the user-defined Slack Incoming Webhook URL is now defined via creation of a `magtape-slack` secret that includes the `webhook-url` key and a value set to the Slack Incoming Webhook URL (typical base64 encoding applies).

The README has [an example](/README.md#User-defined-alert-target) of how you can create a properly formatted secret.

### Enhancements

- Enable shellcheck linting for bash (#57 authored by @ilrudie)
- Cleanup Rego testing/mocking (#60)
- Update docker/build-push-action to v2 (#62 authored by @ilrudie)
- Update functional testing documentation (#65 authored by @ilrudie)
- Enable server-side warnings on policy failures (#66)
- Bump cryptography Python package from 2.9.2 to 3.2 (#68 authored by dependabot)
- Add logic to handle in-cluster and out-of-cluster kubernetes client configs for API calls (#77)
- Add RBAC rules to read secrets for user defined Slack Incoming Webhook URL's (#77)
- Add logic to handle custom Slack Webhook even if Default is unset (#77)
- Bump the engineerd/setup-kind Action to v05.0 to support the [deprecations noted here](https://github.blog/changelog/2020-10-01-github-actions-deprecating-set-env-and-add-path-commands/) (#77)
- Change add-path commands in rego-checks CI jobs (#77)
- Add ci-bootstrap Make target to pin versions for specific utilities (ie. kubectl) for more consistent CI (#77)
- Enabled descriptive names for functional tests (#86)
- OPA version bumped to 0.25.2 (#88)

**server-side warnings on policy failures**

Server-side warnings were added in Kubernetes v1.19. This enhancement allows for messages to be surfaced to the end-users via kubectl and client-go. This gives MagTape yet another mechanism to display feedback on policy failures to the end-user. This change is transparent for Kubernetes releases prior to v1.19.

- [Kubernetes Blog](https://kubernetes.io/blog/2020/09/03/warnings/#admission-webhooks)
- [Kubernetes Enhancement Proposal](https://github.com/kubernetes/enhancements/tree/master/keps/sig-api-machinery/1693-warnings#server-side)
- [Changes to Admission Response Struct](https://github.com/kubernetes/kubernetes/blob/f7a13de36c4584464adc991c7a3d1f38f610232e/pkg/apis/admission/types.go#L141)

**Version 2 for docker/build-push-action**

Adopting version 2 of this action allows us to start consuming Docker `buildx`. This is transparent at the moment, but should allow us to more easily build images for e2e checks and relases across multiple architectures (amd64, ARM, ppc64le, etc.).

**RBAC rule changes**

Due to the change in how user-defined Slack Incoming Webhooks are applied, there's a need for the `magtape-sa` service account to read Secrets across all namespaces. This includes get, list, and watch actions.

## v2.3.1

This release includes a fix for `CVE-2020-36242` in the Python Cryptography package.

### Fixes

Bump [cryptography](https://github.com/pyca/cryptography) from 3.2 to 3.3.2 in /app/imageswap-init (Dependabot)

- [Release notes](https://github.com/pyca/cryptography/releases)
- [Changelog](https://github.com/pyca/cryptography/blob/master/CHANGELOG.rst)
- [Commits](https://github.com/pyca/cryptography/compare/3.2...3.3.2)

## v2.3.2

Bumps [jinja2](https://github.com/pallets/jinja) from 2.11.2 to 2.11.3. in /app/magtape (ref #99)

- PyPI: https://pypi.org/project/Jinja2/2.11.3/
- Changes: https://jinja.palletsprojects.com/en/2.11.x/changelog/#version-2-11-3

## v2.3.3

### Enhancements

- Add multi-arch image builds for release workflow, amd64, arm64, and ppc64le to start (#107 authored by @phenixblue)
- Fix typos in Makefile (#105 authored by @Freakin)
- Bump urllib3 to 1.26.4 (#101 authored by dependabot)

Bumps [urllib3](https://github.com/urllib3/urllib3) from 1.26.3 to 1.26.4 in /app/magtape-init

- [Release notes](https://github.com/urllib3/urllib3/releases)
- [Changelog](https://github.com/urllib3/urllib3/blob/main/CHANGES.rst)
- [Commits](https://github.com/urllib3/urllib3/commit/a8913042b676c510e94fc2b097f6b514ae11a537)