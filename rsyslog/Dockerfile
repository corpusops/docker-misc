ARG BASE_IMAGE=corpusops/alpine-bare:latest
FROM $BASE_IMAGE as runner
ARG DEV_DEPENDENCIES_PATTERN='^#\s*dev dependencies'
ADD /rsyslog/ /install
RUN \
  set -e \
  && cd install \
  && sed -i -re "s/(python-?)[0-9]\.[0-9]+/\1$PY_VER/g" pkgs.txt \
  && cops_pkgmgr_install.sh $(grep -E -v '^#' pkgs.txt) \
  && rm -rf /var/cache/apk/* /var/lib/apt/lists/*
USER root
ENV SUPERVISORD_CONFIGS="cron rsyslog"\
    RSYSLOG_SPLITTED_CONFIGS="1"
ENTRYPOINT ["/bin/supervisord.sh"]
