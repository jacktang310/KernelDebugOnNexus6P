# KernelDebugOnNexus6P

If have any problem, you can touch me at twitter @jacktang310 

Kernel debugging gives security researchers a tool to monitor and control a device under analysis. On desktop platforms such as Windows, macOS, and Linux, this is easy to perform. However, It is more difficult to do kernel debugging on the modern Google android device such as Nexus 6P , Pixel.    
This solution give a practical way to do kernel debugging with KGDB on Nexus 6P ( I think it works for Pixel , but I don't try )
usage:

1.	 Compile the kernel code with following configuration changing on the original configure setting:

    a.	CONFIG_KGDB=y
    
    CONFIG_KGDB_SERIAL_CONSOLE=y
    
    b.	CONFIG_MSM_WATCHDOG_V2 = n
    
    If this is enabled, the kernel will take the polling loop as a dead loop and restart the device. This must be disabled.
    
    c.	CONFIG_FORCE_PAGES=y
    
    If this is not enabled, the soft breakpoint isn’t set.
    
2.	Use the fastboot command to flash the kernel and Android image onto the target device.

3.	Start the Android system on the debugging machine. Run the following command to enable the adb network service: 
    
    adb tcpip 6666

4.	On the debugging machine, run the following commands:

    adb connect <device’s ip address>:6666
    
    adb shell
    
5.	Inside the adb shell , run the following commands:

    echo 0 > enable
    
    echo tty > f_acm/acm_transports
    
    echo acm > functions
    
    echo 1 > enable
    
    echo kgdb > f_acm/acm_transports
    
6.	On the debugging machine, run gdb. The Nexus 6P has an aarch64 kernel, so you need gdb for aarch64:

    sudo <path>/aarch64-linux-android-gdb <path>/vmlinux
    
    target remote /dev/ttyACM0
    
7.	Inside the adb shell, input following command:

    echo g > /proc/sysrq-trigger
    
    Note: The time interval between step 6’s last command and step 7 should not be too long. The shorter, the better.
    
8.	If successful, GDB will show the message of break into android kernel


