FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

RUN apt update && \
    apt install -y --no-install-recommends openssh-server git curl zip unzip \
    && apt install -y --no-install-recommends clang cmake ninja-build pkg-config openjdk-17-jdk\
    && apt clean -y

USER vscode
SHELL ["/bin/bash", "-c", "-o", "pipefail"]

ENV HOME=/home/vscode

# Install cmdline-tools and SDK
ENV ANDROID_HOME "$HOME/Android/sdk"
ENV ANDROID_SDK_ROOT $ANDROID_HOME
ENV PATH $PATH:$ANDROID_HOME/cmdline-tools/latest:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools

ENV ANDROID_SDK_TOOLS_VERSION 11076708
ENV ANDROID_SDK_TOOLS_CHECKSUM 2d2d50857e4eb553af5a6dc3ad507a17adf43d115264b1afc116f95c92e5e258

WORKDIR ${ANDROID_HOME}
RUN curl -s https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_TOOLS_VERSION}_latest.zip > ./tools.zip \
    && echo "$ANDROID_SDK_TOOLS_CHECKSUM ./tools.zip" | sha256sum -c \
    && unzip -qq ./tools.zip -d $ANDROID_HOME \
    #&& mv $ANDROID_HOME/cmdline-tools/* $ANDROID_HOME/cmdline-tools/latest | mkdir $ANDROID_HOME/cmdline-tools/latest \
    && mv ${ANDROID_HOME}/cmdline-tool{s,} \
    && mkdir -p ${ANDROID_HOME}/cmdline-tools/latest \
    && mv ${ANDROID_HOME}/cmdline-tool/* ${ANDROID_HOME}/cmdline-tools/latest \
    && rm -rv ./tools.zip ${ANDROID_HOME}/cmdline-tool \
    && yes | sdkmanager --install "build-tools;34.0.0" "platforms;android-34" "platform-tools"

# Install Java and Gradle with SDKMan
RUN curl -s "https://get.sdkman.io" | bash \
    && source "$HOME/.sdkman/bin/sdkman-init.sh" \
    && sdk install gradle 7.5.1

ENV FLUTTER_VERSION="3.19.6"
ENV FLUTTER_HOME "$HOME/.flutter-sdk"
ENV PATH $PATH:$FLUTTER_HOME/bin

# Download and extract Flutter SDK
RUN mkdir $FLUTTER_HOME \
    && cd $FLUTTER_HOME \
    && curl --fail --remote-time --silent --location -O https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz \
    && tar xf flutter_linux_${FLUTTER_VERSION}-stable.tar.xz --strip-components=1 \
    && rm flutter_linux_${FLUTTER_VERSION}-stable.tar.xz

RUN flutter precache && yes | flutter doctor --android-licenses
