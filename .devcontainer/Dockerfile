FROM docker.io/archlinux/archlinux:base-devel

RUN printf "[archlinuxcn]\nServer = https://repo.archlinuxcn.org/\$arch" | tee -a /etc/pacman.conf \
    && pacman -Sy --noconfirm archlinux-keyring && pacman-key --init \
    && pacman -Syu --noconfirm archlinuxcn-keyring \
    && sed -i "s/#Color/Color/g; s/#ParallelDownloads/ParallelDownloads/g" /etc/pacman.conf \
    && pacman -S --noconfirm paru base-devel openssh clang llvm cmake lld ninja zip unzip \
    && mkdir aur \
    && chown nobody:nobody aur \
    && cd aur \
    && runuser -u nobody -- git clone https://aur.archlinux.org/fvm-bin \
    && cd ./fvm-bin \
    && runuser -u nobody makepkg \
    && pacman -U --noconfirm *.pkg.tar.* \
    && cd .. \
    && runuser -u nobody -- git clone https://aur.archlinux.org/fvm-flutter-proxy \
    && cd ./fvm-flutter-proxy \
    && runuser -u nobody makepkg \
    && pacman -U --noconfirm *.pkg.tar.* \
    && cd ../.. && rm -rf ./aur \
    && pacman -Scc --noconfirm \
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
    && sudo pacman -Rns --noconfirm sdkmanager && sudo pacman -Scc --noconfirm \
    && curl -s "https://get.sdkman.io" | bash \
    && source "$HOME/.sdkman/bin/sdkman-init.sh" \
    && sdk install gradle 8.10.2 \
    && sdk install java 21.0.6-tem \
    && fvm install 3.27.4 \
    && flutter precache && yes | flutter doctor --android-licenses
