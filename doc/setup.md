# Setup instructions

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
