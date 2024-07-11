FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

RUN apt update && \
    apt install -y --no-install-recommends openssh-client openssh-server git curl \
    && apt install -y --no-install-recommends clang cmake ninja-build pkg-config sdkmanager \
    && apt clean -y

USER vscode

ENV HOME=/home/vscode
ENV ANDROID_HOME "/opt/android-sdk-linux"
ENV ANDROID_SDK_ROOT $ANDROID_HOME
ENV PATH $PATH:$ANDROID_HOME/cmdline-tools:$ANDROID_HOME/cmdline-tools/bin:$ANDROID_HOME/platform-tools

# Install Java and Gradle with SDKMan
RUN curl -s "https://get.sdkman.io" | bash \
    && source "$HOME/.sdkman/bin/sdkman-init.sh" \
    && sdk install java 17.0.11-ms \
    && sdk install gradle 7.5.1

# Install cmdline-tools and SDK
RUN yes | sdkmanager --install "build-tools;34.0.0" "platforms;android-34" "platform-tools" "cmdline-tools;latest"

ENV FLUTTER_VERSION="3.19.5"
ENV FLUTTER_HOME "$HOME/.flutter-sdk"
ENV PATH $PATH:$FLUTTER_HOME/bin

# Download and extract Flutter SDK
RUN mkdir $FLUTTER_HOME \
    && cd $FLUTTER_HOME \
    && curl --fail --remote-time --silent --location -O https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz \
    && tar xf flutter_linux_${FLUTTER_VERSION}-stable.tar.xz --strip-components=1 \
    && rm flutter_linux_${FLUTTER_VERSION}-stable.tar.xz

RUN flutter precache
