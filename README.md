# docker images collection

- [![.github/workflows/cicd.yml](https://github.com/corpusops/docker-misc/workflows/.github/workflows/cicd.yml/badge.svg?branch=main)](https://github.com/corpusops/docker-hugo/actions?query=workflow%3A.github%2Fworkflows%2Fcicd.yml+branch%3Amain)
- Some images have their integrations done on [docker-images](https://github.com/corpusops/docker-images)

## Use rsyslog image
- See [./rsyslog](./rsyslog).
- tag is ``corpusops/rsyslog``
    - OS based variants:
        - ``corpusops/rsyslog:debian``
        - ``corpusops/rsyslog:alpine``
        - ``corpusops/rsyslog:ubuntu``
- this image exposes a syslog daemon on port ``10514``
- It will split with its default config every log inside ``/var/log/docker/<prog_name>.log``
- logs also go to stdout
- env vars:
    - ``LOGROTATE_SIZE=5M``: size to trigger a logrotate from.
    - ``LOGROTATE_DAYS=30``: number of days to keep logs for.
    - ``RSYSLOG_SPLITTED_CONFIGS=1``:
        - if ``1``: logs are splitted under ``/var/log/docker/<prog_name>.log``
        - else things go inside like usually in ``/var/log``

One current pattern is to redirect docker logs through a local syslog this way:

```yaml
x-vars:
  base: &base
    restart: always
    # this will configure the baremetal host to reroute logs
    # through localhost:1514 which forwards to ``log`` container itself
    logging:
      driver: "syslog"
      options:
        syslog-address: "tcp://localhost:1514"
        tag: "someservice"
services:
  log:
    <<: [ *base ]
    image: corpuspops/rsyslog
    ports: ["127.0.0.1:1514:10514"]
    # ensure no syslog log loop
    logging: {driver: "json-file", options: {max-size: "10M", max-file: "50"}}
  someservice:
    <<: [ *base ]
    image: some/service
```
