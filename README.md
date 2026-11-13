FOR FLASHING OPENWRT AND REVERTING TO STOCK!

DO NOT FLASH ON V2 SINCE IT DOES NOT HAVE CFE!!!!
Download the firmware file from here.
Rename it to "ArcherC2300v1_tp_recovery"
If your PC has file format or whatever it is called turned off, do not add.bin as the bootloader will not accept it.
If it is on and you see the file format, make sure .bin is there at the end. Mine has it turned on and flashing is successful.
Next, I recommend you to not make it your main router after this and replace it with a better router maybe.
Now download a free software like Tftpd64 and make the file directory to whatever folder you kept it in.
Next, set your PC's Static IP to 192.168.0.66 (For windows, press WIN+R at the same time or right click on the Windows Start Icon and press Run. Then type ncpa.cpl and once you
are in the config menu, Right click on Ethernet, right click on TCP/IPv4 Properties and select properties, then choose set your own IP or whateveer it is called, 
Set the IP to 192.168.0.66, and Subnet Mask will be 255.255.255.0 no default gateway is meeded.)
Turn off the router.
Next, while it is off, press and hold the Reset pin button and turn on the router, until you see the WPS light on at the top.
Tftpd64 will start the transfer. Once it is done, you are successfully in OpenWRT!
Next to enter OpenWRT, type 192.168.1.1
Log into OpenWRT with root, then password root.
Now you have wireless freedom and garbage range!
To reveert back to stock, use the mtd functiom in the SSH terminal.
To log in, type ssh root@192.168.1.1
It will ask you for a fingerprint thingy or whatever.
Type yes.
Then type the password "root"
Type this command: 
cat /proc/mtd
You will see a firmware directory.
Then exit the SSH Shell.
Once you are out, open the folder of your Archer firmware file. I naame it ArcherC2300v1_tp_recovery
Type or paste this command:
$bytes = [System.IO.File]::ReadAllBytes(".\Your_Firmware_Name")
$text = [System.Text.Encoding]::ASCII.GetString($bytes)
$offset = $text.IndexOf("fwup-ptn")
"Offset in decimal: $offset | Hex: 0x{0:X}" -f $offset
Replace Your Firmware Name with whatever the file name is.
Note: On most TP-Link Broadcom/SafeLoader images, this prints 131584 (0x20200) or 524800 (0x80200).
If it returns -1, use 0x20200 (131584) as the standard TP-Link bootloader skip length.
After it is done, then type or paste this command:
$inFile  = ".\Your_Firmware_File"
$outFile = ".\ArcherC2300_tp_recovery"
$skip    = 0x20200  # Replace with the exact $offset found in Step 1

$bytes = [System.IO.File]::ReadAllBytes($inFile)
$stripped = $bytes[$skip..($bytes.Length - 1)]
[System.IO.File]::WriteAllBytes($outFile, $stripped)
Note again: Replace "Your_Firmware_File" with the name.
After that, type this command:
Get-Item $inFile, $outFile | Select-Object Name, Length
[System.Text.Encoding]::ASCII.GetString([System.IO.File]::ReadAllBytes($outFile)[0..31])
ArcherC2300v1_tp_recovery.bin will be smaller than the original file by $skip bytes (e.g., 131,584 bytes smaller),
and the first 32 characters will show fwup-ptn (or your kernel header) instead of U-Boot/CFE strings.
After that, download a free software called WinSCP.
Then make whatever a session just type the Host, 192.168.1.1, then in username type root and password root.
Then drag and drop your stripped firmware file into the tmp folder.
Then after that, paste this command:
mtd -r write /tmp/ArcherC2300v1_tp_recovery.bin firmware
Note: If it does not reboot, type "reboot"
And wallah! You are back into Stock firmware.

@RouteryGuy - Firmware Compiler and README Writer.







