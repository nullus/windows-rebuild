# Windows 10 rebuild

## Specification

### Goal

Working Windows 10 using canonical process, and procedures to create an
environment suitable for work. No weird cusomisations! Just out-of-the-box
Windows 10

### Required software

* Jetbrains IDEs
  * IntelliJ
  * PyCharm
* Ubuntu userspace
* Git
* Brave (+Firefox?)
* Unity
* Steam

### Process

1. Create USB image:
    
    sudo dd if=dist/Win10_2004_English_x64.iso of=/dev/disk4 bs=262144


### Resources

* Windows 10:
  * [Download](https://www.microsoft.com/en-au/software-download/windows10ISO)
* New package manager (preview):
  * [Download](https://github.com/microsoft/winget-cli/releases)
  * [Documentation](https://docs.microsoft.com/en-us/windows/package-manager/)
  * [Guide](https://www.howtogeek.com/674470/how-to-use-windows-10s-package-manager-winget/)
* New terminal
  * [Guide](https://www.howtogeek.com/673729/heres-why-the-new-windows-10-terminal-is-amazing/)
