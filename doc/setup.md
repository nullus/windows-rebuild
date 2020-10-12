# Setup instructions

## Broken license fix

Upgrading from Legacy BIOS/MBR to UEFI/GPT requires a conversion step to boot. A clean install borked the license. Microsoft support was as useful as a one legged man at an arse kicking party...

* Clean install on original hardware
* Swap MB/CPU
* Boot Windows 10 install USB, recovery, command prompt
* Run the following:
    
    mbr2gpt /validate /disk:0
    mbr2gpt /convert /disk:0

* Boot 🤞
* In Settings → Update & Security → Activation, run Troubleshoot
* Select option "I upgraded hardware"
* Choose old system and 

## Actual setup...

Ensure Windows Insider updates are configured

winget is included with preview of App Installer

Update WSL kernel:

    https://docs.microsoft.com/en-us/windows/wsl/wsl2-kernel#download-the-linux-kernel-update-package

Set default WSL version:
    
    wsl --set-default-version 2
Bypass script execution:
    
    Set-ExecutionPolicy RemoteSigned -Force
Use ssh-agent (start on boot):
    
    Set-Service ssh-agent -StartupType Automatic
Install Poetry:
    
    (Invoke-WebRequest -Uri https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py -UseBasicParsing).Content | python
