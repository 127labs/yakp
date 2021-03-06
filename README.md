## drone-k8s-helm

[![Build Status](https://drone.127labs.com/api/badges/127labs/drone-k8s-helm/status.svg)](https://drone.127labs.com/127labs/drone-k8s-helm)

This is a [drone](https://github.com/drone/drone) plugin to deploy with kubernetes helm.

## Usage

```yaml
pipeline:
  ship:
    image: 127labs/drone-k8s-helm
    chart: stable/dokuwiki
    master: https://127.0.0.1:8001
    token: super-long-token
    release: wiki
    skip_tls: true
    clean_before_release: true
    values:
      dokuwikiEmail: $${DOKUWIKI_EMAIL}
      dokuwikiPassword: $${DOKUWIKI_PASSWORD}
    secrets: [dokuwiki_email, dokuwiki_password]
```

Which is equivalent to:

```shell
docker run \
  -e HELM_CHART="stable/dokuwiki" \
  -e HELM_MASTER="https://127.0.0.1:8001" \
  -e HELM_TOKEN="super-long-token" \
  -e HELM_RELEASE=wiki \
  -e HELM_SKIP_TLS=true \
  -e HELM_CLEAN_BEFORE_RELEASE=true \
  -e HELM_VALUES='{"dokuwikiEmail":"${DOKUWIKI_EMAIL}","dokuwikiPassword":"${DOKUWIKI_PASSWORD}"}' \
  -e DOKUWIKI_EMAIL=imran@127labs.com \
  -e DOKUWIKI_PASSWORD=password \
  127labs/drone-k8s-helm
```

Most of the time it's always better to remove all secrets from the .drone.yml manifest.

Here's an example of a production .drone.yml manifest that uses the mounted secrets `plugin_master` and `plugin_token` in place of the config of `master` and `token`

```yaml
pipeline:
  staging:
    image: 127labs/drone-k8s-helm
    chart: stable/dokuwiki
    release: wiki-staging
    skip_tls: true
    clean_before_release: true
    values:
      dokuwikiEmail: $${DOKUWIKI_EMAIL_STAGING}
      dokuwikiPassword: $${DOKUWIKI_PASSWORD_STAGING}
    secrets:
      - dokuwiki_email_staging
      - dokuwiki_password_staging
      - helm_master
      - helm_token
  production:
    image: 127labs/drone-k8s-helm
    chart: stable/dokuwiki
    release: wiki
    skip_tls: true
    clean_before_release: true
    values:
      dokuwikiEmail: $${DOKUWIKI_EMAIL}
      dokuwikiPassword: $${DOKUWIKI_PASSWORD}
    secrets:
      - dokuwiki_email
      - dokuwiki_password
      - helm_master
      - helm_token
```
