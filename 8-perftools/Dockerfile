FROM rhel7:7.6-122
LABEL MAINTAINER Red Hat, Inc.

ENV SUMMARY="Red Hat Developer Toolset 8 Performance Tools container image" \
    DESCRIPTION="Performance tools: systemtap, valgrind, dyninst, elfutils, oprofile ltrace dwz make memstomp strace"

LABEL com.redhat.component="devtoolset-8-perftools-container" \
      name="rhscl/devtoolset-8-perftools-rhel7" \
      version="8" \
      summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="Developer Toolset 8 Performance Tools" \
      io.k8s.min-memory="2Gi" \
      maintainer="Frank Eigler <fche@redhat.com>"

# To use subscription inside container yum command has to be run first (before yum-config-manager)
# https://access.redhat.com/solutions/1443553
RUN yum repolist > /dev/null && \
    yum-config-manager --enable rhel-server-rhscl-7-rpms && \
    yum-config-manager --enable rhel-7-server-optional-rpms && \
    yum-config-manager --disable epel >/dev/null || : && \
    INSTALL_PKGS="devtoolset-8-systemtap devtoolset-8-valgrind devtoolset-8-elfutils devtoolset-8-elfutils-devel devtoolset-8-oprofile devtoolset-8-oprofile-jit devtoolset-8-oprofile-devel devtoolset-8-ltrace devtoolset-8-dwz devtoolset-8-make devtoolset-8-memstomp devtoolset-8-strace" && \
    [ $(uname -m) = "x86_64" ] && INSTALL_PKGS="$INSTALL_PKGS devtoolset-8-dyninst devtoolset-8-dyninst-devel" || : && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

# Copy extra files to the image.
COPY ./root/ /

ENV HOME=/root \
    PATH=/root/bin:/opt/rh/devtoolset-8/root/usr/bin/:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN mkdir -p ${HOME} && \
    chmod u+x /usr/bin/usage

USER 0

WORKDIR ${HOME}

# Enable the SCL for all bash scripts.
ENV BASH_ENV=/root/etc/scl_enable \
    ENV=/root/etc/scl_enable \
    PROMPT_COMMAND=". /root/etc/scl_enable"

# Set the default CMD to print the usage of the perftools image
ENTRYPOINT ["container-entrypoint"]
CMD ["usage"]
