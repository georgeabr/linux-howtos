### Solutions for UEFI booting
If you have deleted and recreated the UEFI partition ([see link](https://superuser.com/questions/1230741/how-to-resize-the-efi-system-partition#1289122)), your linux distros will not appear in the boot menu.  
This is because the id of the UEFI partition has changed.  
You need to delete and recreate the boot entries.  
Let's update the entry `0009 Linux Boot Manager`.  
We will do a listing with `efibootmgr`:
```bash
$ efibootmgr

BootCurrent: 0002
Timeout: 0 seconds
BootOrder: 0006,0002,0000
Boot0000* Windows Boot Manager  HD(1,GPT,e9bca0e2-c3c9-4cee-bfd1-c7832702e092,0x800,0x32000)/\EFI\Microsoft\Boot\bootmgfw.efi57494e444f5753000100000088000000780000004200430044004f0042004a004500430054003d007b00390064006500610038003600320063002d0035006300640064002d0034006500370030002d0061006300630031002d006600330032006200330034003400640034003700390035007d00000000000100000010000000040000007fff0400
Boot0001* Fedora        HD(1,GPT,e9bca0e2-c3c9-4cee-bfd1-c7832702e092,0x800,0x32000)/\EFI\fedora\shimx64.efi
Boot0002* Arch  HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Arch\grubx64.efi
Boot0004* UEFI: KIOXIA-EXCERIA PLUS G3 SSD, Partition 1 HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Microsoft\Boot\bootmgfw.efi0000424f
Boot0006* Fedora        HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\fedora\shimx64.efi
Boot0007* UEFI: KingstonDTR30G2 PMAP, Partition 2       PciRoot(0x0)/Pci(0x14,0x0)/USB(1,0)/HD(2,GPT,bdb458bb-9979-5536-0c3f-6cd9b3c94985,0x3a8ffd8,0x10000)0000424f
Boot0009* Linux Boot Manager    HD(1,GPT,f8064846-b26a-4046-ac2c-458f2e136a61,0x800,0x154000)/\EFI\systemd\systemd-bootx64.efi
```
We will delete the entry id `0009`:
```bash
$ sudo efibootmgr -b 0009 -B
BootCurrent: 0002
Timeout: 0 seconds
BootOrder: 0006,0002,0000
Boot0000* Windows Boot Manager  HD(1,GPT,e9bca0e2-c3c9-4cee-bfd1-c7832702e092,0x800,0x32000)/\EFI\Microsoft\Boot\bootmgfw.efi57494e444f5753000100000088000000780000004200430044004f0042004a004500430054003d007b00390064006500610038003600320063002d0035006300640064002d0034006500370030002d0061006300630031002d006600330032006200330034003400640034003700390035007d00000000000100000010000000040000007fff0400
Boot0002* Arch  HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Arch\grubx64.efi
Boot0004* UEFI: KIOXIA-EXCERIA PLUS G3 SSD, Partition 1 HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Microsoft\Boot\bootmgfw.efi0000424f
Boot0006* Fedora        HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\fedora\shimx64.efi
Boot0007* UEFI: KingstonDTR30G2 PMAP, Partition 2       PciRoot(0x0)/Pci(0x14,0x0)/USB(1,0)/HD(2,GPT,bdb458bb-9979-5536-0c3f-6cd9b3c94985,0x3a8ffd8,0x10000)0000424f

```
We will add it again:
```bash
$ sudo efibootmgr -c -d /dev/nvme0n1 -p 1 -l /EFI/systemd/systemd-bootx64.efi -L "Linux Boot Manager"
BootCurrent: 0002
Timeout: 0 seconds
BootOrder: 0001,0006,0002,0000
Boot0000* Windows Boot Manager  HD(1,GPT,e9bca0e2-c3c9-4cee-bfd1-c7832702e092,0x800,0x32000)/\EFI\Microsoft\Boot\bootmgfw.efi57494e444f5753000100000088000000780000004200430044004f0042004a004500430054003d007b00390064006500610038003600320063002d0035006300640064002d0034006500370030002d0061006300630031002d006600330032006200330034003400640034003700390035007d00000000000100000010000000040000007fff0400
Boot0002* Arch  HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Arch\grubx64.efi
Boot0004* UEFI: KIOXIA-EXCERIA PLUS G3 SSD, Partition 1 HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\Microsoft\Boot\bootmgfw.efi0000424f
Boot0006* Fedora        HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\fedora\shimx64.efi
Boot0007* UEFI: KingstonDTR30G2 PMAP, Partition 2       PciRoot(0x0)/Pci(0x14,0x0)/USB(1,0)/HD(2,GPT,bdb458bb-9979-5536-0c3f-6cd9b3c94985,0x3a8ffd8,0x10000)0000424f
Boot0001* Linux Boot Manager    HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\systemd\systemd-bootx64.efi
```
To compare, the id of the partition has changed:
```
Boot0009* Linux Boot Manager    HD(1,GPT,f8064846-b26a-4046-ac2c-458f2e136a61,0x800,0x154000)/\EFI\systemd\systemd-bootx64.efi
Boot0001* Linux Boot Manager    HD(1,GPT,9c4a52a5-6e00-4b89-934c-3196b4703b22,0x800,0x64800)/\EFI\systemd\systemd-bootx64.efi

```
