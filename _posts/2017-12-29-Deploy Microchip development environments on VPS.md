---
layout: post
title:  Deploy Microchip Development Environments on VPS
date:   2017-06-29 03:18:51
categories: 
---

Linux 32bit is required, Ubuntu i386 distribution is recommended.

1. Install desktop

    ```bash
    # apt-get update
    # apt-get install ubuntu-desktop -y
    ```

2. Download files

    ```shell
    $ wget http://ww1.microchip.com/downloads/en/DeviceDoc/xc32-v1.43-full-install-linux-installer.run http://ww1.microchip.com/downloads/en/DeviceDoc/MPLABX-v4.01-linux-installer.tar
    ```
    or 
    ```
    http://www.microchip.com/mplabx-ide-linux-installer
    ```

3. Install MPLAB X

    Extract installer:

    ```shell
    $ tar xf MPLABX-v4.01-linux-installer.tar
    ```

    You will get a shell script file, excute it:

    ```shell
    # ./MPLABX-v4.01-linux-installer.sh
    ```

    Just follow the prompt to answer the requests to get installation done.

4. Install xc32 compiler

    Give the installer excutive permission and run it:

    ```shell
    $ chmod +x xc32-v1.43-full-install-linux-installer.run
    # ./xc32-v1.43-full-install-linux-installer.run
    ```

    Just follow the prompt to answer the requests to get installation done.

5. Activate xc32

    Get license from [here](http://www.microchip.com/xcdemo/GetDemoLicense.aspx).

6. Install java runtime (JRE, required by IDE)

    Almost done, since MPLAB X is based on NetBeans, which is required a Java runtime to run, and MPLAB X does not ship with a Java runtime, we need to get one.

    ```shell
    # apt-get install java
    ```

7. Install harmony if necessary

    ```shell
    $ wget http://ww1.microchip.com/downloads/en/DeviceDoc/harmony_v2_01b_linux_installer.run
    $ chmod +x harmony_v2_01b_linux_installer.run
    # ./harmony_v2_01b_linux_installer.run
    ```
