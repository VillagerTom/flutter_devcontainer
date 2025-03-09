FROM docker.io/archlinux/archlinux:base-devel

RUN printf "[archlinuxcn]\nServer = https://repo.archlinuxcn.org/\$arch" | tee -a /etc/pacman.conf \
    && pacman -Sy --noconfirm archlinux-keyring && pacman-key --init \
    && pacman -Syu --noconfirm archlinuxcn-keyring \
    && sed -i "s/#Color/Color/g; s/#ParallelDownloads/ParallelDownloads/g" /etc/pacman.conf \
    && pacman -S --noconfirm paru base-devel openssh clang llvm cmake lld ninja zip unzip \
    && pacman -Scc --noconfirm \
    && mkdir aur \
    && chown nobody:nobody aur \
    && cd aur \
    && runuser -u nobody -- git clone https://aur.archlinux.org/vfox-bin \
    && cd ./vfox-bin \
    && runuser -u nobody makepkg \
    && pacman -U --noconfirm *.pkg.tar.* \
    && cd ../.. && rm -rf ./aur \
    && useradd -m -G wheel vscode \
    && echo "vscode ALL=(ALL:ALL) NOPASSWD: ALL" | tee -a /etc/sudoers

USER vscode
SHELL ["/bin/bash", "-c"]
ENV HOME=/home/vscode
ENV ANDROID_HOME "$HOME/Android/sdk"
ENV ANDROID_SDK_ROOT $ANDROID_HOME
ENV PATH $ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH
RUN sudo pacman -S --noconfirm sdkmanager \
    && mkdir -p $ANDROID_HOME \
    && yes | sdkmanager --install "cmdline-tools;latest" "build-tools;35.0.1" "platforms;android-35" "platform-tools" \
    && sudo pacman -Rns --noconfirm sdkmanager && sudo pacman -Scc --noconfirm\
    && echo 'eval "$(vfox activate bash)"' >> ~/.bashrc \
    && vfox add java flutter gradle\
    && vfox install java@21.0.6+7-zulu flutter@3.27.4 gradle@8.10.2 \
    && vfox use --global flutter@3.27.4 \
    && vfox use --global gradle@8.10.2 \
    && vfox use --global java@21.0.6+7-zulu

RUN flutter precache && yes | flutter doctor --android-licenses
