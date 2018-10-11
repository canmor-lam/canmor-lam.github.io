---
layout: post
title:  Deploy Microchip Development Environments on VPS
categories: tools
tags: [xc32, microchip]
---

Linux 32bit is required, Ubuntu i386 distribution is recommended.

0. Install 32bit runtime if you are running a 64bit OS.

   ```shell
   sudo dpkg --add-architecture i386
   sudo apt-get update
   sudo apt-get install libc6:i386 libx11-6:i386 libxext6:i386 libstdc++6:i386 libexpat1:i386
   ```

2. Download files

    ```shell
    wget http://ww1.microchip.com/downloads/en/DeviceDoc/xc32-v1.43-full-install-linux-installer.run http://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v4.01-linux-installer.tar
    ```
    or 
    ```
    http://www.microchip.com/mplabx-ide-linux-installer
    ```

3. Install MPLAB X

    Extract installer:

    ```shell
    tar xf MPLABX-v4.01-linux-installer.tar
    ```

    You will get a shell script file, excute it:

    ```shell
    sudo ./MPLABX-v4.01-linux-installer.sh
    ```

    Just follow the prompt to answer the requests to get installation done.

4. Install xc32 compiler

    Give the installer excutive permission and run it:

    ```shell
    chmod +x xc32-v1.43-full-install-linux-installer.run
    sudo ./xc32-v1.43-full-install-linux-installer.run
    ```

    Just follow the prompt to answer the requests to get installation done. Installer will ask you to choose a license, 'Workstation' is recommended (you can get a license for trial later).

4. Activate xc32

    Get license from [here](http://www.microchip.com/xcdemo/GetDemoLicense.aspx), with your *host ID* which is showed to you during previous xc32 installation. Download a license script from web site, copy to your server and execute:

    ```shell
    chmod +x 42010a8c0002-xc32-demo.sh
    ./42010a8c0002-xc32-demo.sh
    ```

    >  **Tips**
    >
    > You should replace *42010a8c0002* with your host ID

6. Install java runtime (JRE, required by IDE)

    Almost done, since MPLAB X is based on NetBeans, which is required a Java runtime to run, and MPLAB X does not ship with a Java runtime, we need to get one.

    ```shell
    sudo apt-get install java
    ```

7. Install harmony if necessary

    ```shell
    wget http://ww1.microchip.com/downloads/en/DeviceDoc/harmony_v2_01b_linux_installer.run
    chmod +x harmony_v2_01b_linux_installer.run
    sudo ./harmony_v2_01b_linux_installer.run
    ```
