# How to build a yocto image
1. Create work dir
    ```
    $ mkdir imx-yocto-bsp
    $ cd imx-yocto-bsp
    ```

2. Obtain source code

    IMX Original
    ```
    $ repo init --depth=1 -u https://github.com/nxp-imx/imx-manifest -b imx-linux-kirkstone -m imx-5.15.52-2.1.0.xml
    $ repo sync -c -j `nproc --all`
    ```

    KBT Cutsom
    ```
    $ repo init --depth=1 -u http://172.29.128.8/gitlab/emb-sys-group/gcu3/imx-manifest.git -b imx-linux-kirkstone -m imx-5.15.52-2.1.0_kbt.xml
    $ repo sync -c -j `nproc --all`
    ```
    `--depth=1`: Shallow clone
    `-c`: Fetch only current branch
    `-j`: Thread num

    <br>When the entire log is needed
    ```
    $ cd <some-git-repository>
    $ git fetch <remote name> <branch name> --unshallow
    ```

3. Setup environment

    First time
    ```
    $ DISTRO=fsl-imx-xwayland MACHINE=imx8qxpc0mek source imx-setup-release.sh -b build
    ```
    *EULA acceptance required

    <br>After the second time
    ```
    $ source setup-environment build
    ```

    The Yocto Project now allows to reuse Shared State from its autobuilder. If the network connection between our server and your machine is faster than you would build recipes, you can try to speed up your builds by using such Share State and Hash Equivalence by setting:
    ```
    BB_SIGNATURE_HANDLER = "OEEquivHash"
    BB_HASHSERVE = "auto"
    BB_HASHSERVE_UPSTREAM = "hashserv.yocto.io:8687"
    SSTATE_MIRRORS ?= "file://.* https://sstate.yoctoproject.org/all/PATH;downloadfilename=PATH"
    ```


4. Build
    ```
    $ bitbake <image name>
    ```

    i.MX Yocto project images
    | Image name | Target | Provided by layer |
    | ---------- | ------ | ----------------- |
    | core-image-minimal | A small image that only allows a device to boot. | poky |
    | core-image-base | A console-only image that fully supports the target device hardware. | poky |
    | core-image-sato | An image with Sato, a mobile environment and visual style for mobile devices. The image supports a Sato theme and uses Pimlico applications. It contains a terminal, an editor and a file manager. | poky |
    | imx-image-core | An i.MX image with i.MX test applications to be used for Wayland backends. This image is used by our daily core testing. | meta-imx/meta-sdk |
    | fsl-image-machine-test | An Fsl Community i.MX core image with console environment - no GUI interface. | meta-freescale-distro |
    | imx-image-multimedia | Builds an i.MX image with a GUI without any Qt content. | meta-imx/meta-sdk |
    | imx-image-full | Builds an opensource Qt 6 image with Machine Learning features. These images are only supported for i.MX SoC with hardware graphics. They are not supported on the i.MX 6UltraLite, i.MX 6UltraLiteLite, i. MX 6SLL, i.MX 7Dual, i.MX 8MNanoLite, or i.MX 8DXL | meta-imx/meta-sdk |
    | imx-image-full-dev | gcc and ssh connection available | meta-imx/meta-sdk |
