#
# ubuntu:VLTrader with license.properties file. See README.md for usage.
#
FROM ubuntu

# Install unzip, wget, & X11 libraries
RUN  sed 's/main$/main universe/' -i /etc/apt/sources.list \
  && apt-get update && apt-get install -y \
	unzip wget libxext-dev libxrender-dev libxtst-dev vim sudo \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /tmp/*

# Define environment variables for product, version, etc.
ARG CLEO_PRODUCT=VLTrader
ARG CLEO_INSTALL_FOLDER=/opt/versalex
ARG CLEO_PLATFORM=linux64-jre18
ARG CLEO_VERSION=5.3-SNAPSHOT
ARG CLEO_URL=http://10.10.1.57/nexus/service/local/artifact/maven/content?r=cleo&g=com.cleo.installers&a=$CLEO_PRODUCT&v=$CLEO_VERSION&e=bin&c=$CLEO_PLATFORM
ARG CLEO_CACHE=true

# Download & install the desired product artifact
RUN wget --progress=bar:force $CLEO_URL -O install.bin \
  && chmod +x install.bin \
  && ./install.bin \
	-i silent \
	-DUSER_INSTALL_DIR=$CLEO_INSTALL_FOLDER \
  && ln -s ${CLEO_INSTALL_FOLDER}/${CLEO_PRODUCT}c /opt/VersaLexc \
  && rm ./install.bin

# Generate a license using the given properties file
WORKDIR /tmp
COPY $CLEO_PRODUCT-license.properties license.properties
ARG LICENSE_URL=http://10.10.1.57/nexus/service/local/artifact/maven/redirect?r=cleo&g=com.cleo.util&a=LexiComLicenser_2&v=$CLEO_VERSION&e=jar
RUN wget --progress=bar:force $LICENSE_URL -O license.jar \
	&& if ! $CLEO_INSTALL_FOLDER/jre/bin/java -jar license.jar -f license.properties; \
	then \
		mv license_key.txt $CLEO_INSTALL_FOLDER \
		&& rm /tmp/license*; \
	fi

WORKDIR $CLEO_INSTALL_FOLDER
# Define the entry point
ENTRYPOINT ["/opt/VersaLexc"]