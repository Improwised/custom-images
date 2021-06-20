ARG CT_VERSION
ARG CR_VERSION
ARG YQ_VERSIO
ARG GIT_CHGLOG_VERSION

FROM quay.io/helmpack/chart-testing:v${CT_VERSION} as chart-testing
FROM quay.io/helmpack/chart-releaser:v${CR_VERSION} as chart-releaser
FROM docker.io/mikefarah/yq:${YQ_VERSION} as yaml-yq
FROM quay.io/git-chglog/git-chglog:${GIT_CHGLOG_VERSION} as git-chglog

FROM alpine

ARG GHR_VERSION
ARG JQ_VERSION
ARG SEMVERTOOL_VERSION

ENV GHR_VERSION="v${GHR_VERSION}"
ENV SEMVERTOOL_VERSION="${SEMVERTOOL_VERSION}"
ENV JQ_VERSION="${JQ_VERSION}"

COPY --from=chart-testing "/usr/local/bin/ct" "/usr/local/tools/ct"
COPY --from=chart-testing "/usr/local/bin/helm" "/usr/local/tools/helm"
COPY --from=chart-testing "/usr/local/bin/kubectl" "/usr/local/tools/kubectl"
COPY --from=chart-releaser "/usr/local/bin/cr" "/usr/local/tools/cr"
COPY --from=yaml-yq "/usr/bin/yq" "/usr/local/tools/yq"
COPY --from=git-chglog "/usr/local/bin/git-chglog" "/usr/local/tools/git-chglog"

ENV PATH="/usr/local/tools/:${PATH}"

RUN \
    set -ex; \
    apk add --no-cache git bash; \
    mkdir -p /usr/local/tools/; \
    cd /tmp; \
    wget -nv -O ghr.tar.gz https://github.com/tcnksm/ghr/releases/download/${GHR_VERSION}/ghr_${GHR_VERSION}_linux_amd64.tar.gz; \
    wget -nv -O /usr/local/tools/semvertool https://raw.githubusercontent.com/fsaintjacques/semver-tool/${SEMVERTOOL_VERSION}/src/semver; \
    wget -nv -O /usr/local/tools/jq https://github.com/stedolan/jq/releases/download/jq-${JQ_VERSION}/jq-linux64; \
    tar xvzf ghr.tar.gz ghr_${GHR_VERSION}_linux_amd64/ghr && mv ghr_${GHR_VERSION}_linux_amd64/ghr /usr/local/tools/ghr; \
    chmod a+x /usr/local/tools/*; \
    rm -rf /tmp/*; \
    # export PATH=$PATH:/usr/local/tools/; \
    jq --version; \
    ghr --version; \
    ct version; \
    cr version; \
    git-chglog --version; \
    yq --version; \
    helm version; \
    kubectl version

ENTRYPOINT "/bin/bash"
