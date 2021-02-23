# Disk Upgrade

## Transfer Windows install to a new disk using Windows Recovery Environment

Having recently purchased an NVMe to replace my SATA SSD, I wanted a method to transfer my Windows installation with minimal hassle. In the past I've used a bootable FreeBSD live DVD to clone partitions, but this was before I'd upgraded to a UEFI system, and I suspected there could be some issues with such a low level approach. Besides, I wanted to get more comfortable with Windows 10, and I suspected Windows Recovery Environment would provide sufficient tools to do the job.

Searching [DuckDuckGo](https://duckduckgo.com/about) for "migrating" or "cloning" a Windows 10 install produced numerous results for 3rd-party GUI utilities, but that's not what I was looking for. Eventually I found Microsoft documentation to [capture and apply an image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/capture-and-apply-windows-using-a-single-wim) which, though intended for hardware manufacturers, demonstrated all the tools required to perform the transfer.

