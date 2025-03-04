FROM docker.io/archlinux/archlinux:base-devel
#RUN apt update && \
#    apt install -y --no-install-recommends openssh-server git curl zip unzip \
#    && apt install -y --no-install-recommends clang cmake ninja-build pkg-config openjdk-17-jdk\
#    && apt clean -y
RUN printf "[archlinuxcn]\nServer = https://repo.archlinuxcn.org/\$arch" | tee -a /etc/pacman.conf \
    && pacman -Sy --noconfirm archlinux-keyring && pacman-key --init \
    && pacman -Syu --noconfirm archlinuxcn-keyring \
    && sed -i "s/#Color/Color/g; s/#ParallelDownloads/ParallelDownloads/g" /etc/pacman.conf \
    && pacman -S --noconfirm paru base-devel sudo openssh clang llvm cmake lld ninja zip unzip sdkmanager \
    && pacman -Scc --noconfirm \
    && git clone https://aur.archlinux.org/vfox-bin \
    && cd ./vfox-bin \
    && runuser -u nobody makepkg \
    && pacman -U --noconfirm *.pkg.tar.* \
    && cd .. && rm -rf ./vfox-bin

USER vscode
SHELL ["/bin/bash", "-c"]

ENV HOME=/home/vscode

# Install cmdline-tools and SDK
ENV ANDROID_HOME "$HOME/Android/sdk"
ENV ANDROID_SDK_ROOT $ANDROID_HOME
ENV PATH $ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH
RUN yes | sdkmanager --install "cmdline-tools;latest" "build-tools;34.0.0" "platforms;android-34" "platform-tools" \
    && echo 'eval "$(vfox activate bash)"' >> ~/.bashrc \
    && vfox add java flutter gradle\
    && vfox install java@21.0.6+7-zulu flutter@3.27.4 gradle@8.10.2 \
    && vfox use --global flutter@3.27.4 \
    && vfox use --global gradle@8.10.2 \
    && vfox use --global java@21.0.6+7-zulu

RUN flutter precache && yes | flutter doctor --android-licenses
