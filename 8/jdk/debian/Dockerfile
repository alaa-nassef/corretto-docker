FROM debian:buster-slim

# x86_64 args
ARG deb_x64=java-1.8.0-amazon-corretto-jdk_8.265.01-1_amd64.deb
ARG path_x64=https://corretto.aws/downloads/resources/8.265.01.1
ARG key_x64=6DC3636DAE534049C8B94623A122542AB04F24E3

# aarch64 args
ARG deb_aarch64=java-1.8.0-amazon-corretto-devel-1.8.0_265.b01-1.x86_64.rpm
ARG path_aarch64=https://corretto.aws/downloads/resources/8.265.01.1
ARG key_aarch64=6DC3636DAE534049C8B94623A122542AB04F24E3

# In addition to installing the DEB, we also install
# fontconfig. The folks who manage the docker hub's
# official image library have found that font management
# is a common usecase, and painpoint, and have
# recommended that Java images include font support.
#
# See:
#  https://github.com/docker-library/official-images/blob/master/test/tests/java-uimanager-font/container.java

RUN set -eux; \
	dpkgArch="$(dpkg --print-architecture)"; \
	case "$dpkgArch" in \
			amd64) deb=$deb_x64; path=$path_x64; key=$key_x64 ;; \
			arm64) deb=$deb_aarch64; path=$path_aarch64; key=$key_aarch64 ;; \
			*) echo >&2 "Unsupported architecture $dpkgArch."; exit 1 ;; \
	esac; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends \
# utilities for keeping Debian and OpenJDK CA certificates in sync
		ca-certificates fontconfig java-common p11-kit \
	; \
	rm -rf /var/lib/apt/lists/*; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		curl \
		dirmngr \
		gnupg; \
	rm -rf /var/lib/apt/lists/*; \
	curl -O $path/$deb; \
	export GNUPGHOME="$(mktemp -d)"; \
  gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys $key; \
	gpg --armor --export $key > corretto.asc; \
	apt-key add corretto.asc; \
	if [ ! -d /usr/share/man/man1 ]; then \
		mkdir -p /usr/share/man/man1; \
	fi; \
	dpkg --install $deb; \
	rm -rf $GNUPGHOME corretto.asc $deb; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false curl dirmngr gnupg

ENV LANG C.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto
