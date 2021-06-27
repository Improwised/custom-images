ARG CR_VERSION
ARG YQ_VERSION=4.9.5
ARG GIT_CHGLOG_VERSION=0.14.1

FROM quay.io/helmpack/chart-releaser:${CR_VERSION} as chart-releaser
FROM docker.io/mikefarah/yq:${YQ_VERSION} as yaml-yq
FROM quay.io/git-chglog/git-chglog:${GIT_CHGLOG_VERSION} as git-chglog

FROM quay.io/helmpack/chart-testing as tools

ARG GHR_VERSION
ARG JQ_VERSION
ARG SEMVERTOOL_VERSION

ENV GHR_VERSION="${GHR_VERSION}"
ENV SEMVERTOOL_VERSION="${SEMVERTOOL_VERSION}"
ENV JQ_VERSION="${JQ_VERSION}"

COPY --from=chart-releaser "/usr/local/bin/cr" "/usr/local/tools/cr"
COPY --from=yaml-yq "/usr/bin/yq" "/usr/local/tools/yq"
COPY --from=git-chglog "/usr/local/bin/git-chglog" "/usr/local/tools/git-chglog"

ENV PATH="/usr/local/tools/:${PATH}"

RUN \
    set -ex; \
    apk add --no-cache bash; \
    mkdir -p /usr/local/tools/; \
    cd /tmp; \
    wget -nv -O ghr.tar.gz https://github.com/tcnksm/ghr/releases/download/${GHR_VERSION}/ghr_${GHR_VERSION}_linux_amd64.tar.gz; \
    wget -nv -O /usr/local/tools/semvertool https://raw.githubusercontent.com/fsaintjacques/semver-tool/${SEMVERTOOL_VERSION}/src/semver; \
    wget -nv -O /usr/local/tools/jq https://github.com/stedolan/jq/releases/download/${JQ_VERSION}/jq-linux64; \
    tar xvzf ghr.tar.gz ghr_${GHR_VERSION}_linux_amd64/ghr && mv ghr_${GHR_VERSION}_linux_amd64/ghr /usr/local/tools/ghr; \
    chmod a+x /usr/local/tools/*; \
    rm -rf /tmp/*; \
    ls /usr/local/tools; \
    yamale -h; \
    yamllint -v; \
    git --version; \
    jq --version; \
    ghr --version; \
    ct version; \
    cr version; \
    git-chglog --version; \
    semvertool --version; \
    yq --version; \
    helm version; \
    kubectl version --client

FROM quay.io/helmpack/chart-testing
RUN apk add --no-cache bash
COPY --from=tools "/usr/local/tools/*" "/usr/local/bin/"
