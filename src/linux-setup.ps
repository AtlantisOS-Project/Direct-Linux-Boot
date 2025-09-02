<#
.SYNOPSIS
    Create a minimal partition for linux (5GB), copy Ubuntu-ISO + Kernel + GRUB and create UEFI entry
#>

param(
    [string]$Disk = "0",
    [int]$SizeMB = 5120
)

Write-Host "Ubuntu Boot-Setup" -ForegroundColor Cyan

# ask user for iso
$isoPath = Read-Host "Enter the path to the Ubuntu ISO (e.g. C:\Users\path\to\Downloads\ubuntu.iso)"

if (-Not (Test-Path $isoPath)) {
    Write-Error "ISO nicht gefunden: $isoPath"
    exit 1
}

# create partition)

Write-Host ">>> Creating partition..." -ForegroundColor Yellow
$diskpartScript = @"
select disk $Disk
create partition primary size=$SizeMB
format fs=fat32 quick label=LINUXBOOT
assign letter=L
exit
"@

$scriptFile = "$env:TEMP\create_linux_partition.txt"
$diskpartScript | Out-File -Encoding ASCII $scriptFile
diskpart /s $scriptFile | Out-Null
Remove-Item $scriptFile

$bootDrive = "L:"

# mount the iso
Write-Host ">>> Mounting ISO..." -ForegroundColor Yellow
$iso = Mount-DiskImage -ImagePath $isoPath -PassThru
$isoDriveLetter = ($iso | Get-Volume).DriveLetter + ":"

if (-Not (Test-Path "$isoDriveLetter\casper\vmlinuz")) {
    Write-Error "Konnte Kernel nicht im ISO finden: $isoDriveLetter\casper\vmlinuz"
    Dismount-DiskImage -ImagePath $isoPath
    exit 1
}

Write-Host "ISO eingebunden als $isoDriveLetter"

# create filesystem structure
New-Item -ItemType Directory -Force -Path "$bootDrive\EFI\Boot" | Out-Null
New-Item -ItemType Directory -Force -Path "$bootDrive\boot\grub" | Out-Null
New-Item -ItemType Directory -Force -Path "$bootDrive\iso" | Out-Null

# copy kernel and initramfs
Copy-Item "$isoDriveLetter\casper\vmlinuz" "$bootDrive\boot\vmlinuz"
Copy-Item "$isoDriveLetter\casper\initrd" "$bootDrive\boot\initrd"

# copy efi files
if (Test-Path "$isoDriveLetter\EFI\BOOT\grubx64.efi") {
    Copy-Item "$isoDriveLetter\EFI\BOOT\grubx64.efi" "$bootDrive\EFI\Boot\bootx64.efi"
}
elseif (Test-Path "$isoDriveLetter\EFI\BOOT\BOOTx64.EFI") {
    # Fallback, manche ISOs haben nur BOOTX64.EFI → umbenennen
    Copy-Item "$isoDriveLetter\EFI\BOOT\BOOTx64.EFI" "$bootDrive\EFI\Boot\bootx64.efi"
}
else {
    Write-Warning "grubx64.efi nicht im ISO gefunden! Bitte manuell hinzufügen."
}

# copy iso to the partition
Copy-Item "$isoPath" "$bootDrive\iso\ubuntu.iso"

# create a new grub.cfg
$grubCfg = @"
set timeout=5
set default=0

menuentry "Ubuntu Live ISO (toram)" {
    set isofile="/iso/ubuntu.iso"
    loopback loop $isofile
    linux /boot/vmlinuz boot=casper iso-scan/filename=$isofile toram quiet splash ---
    initrd /boot/initrd
}
"@

$grubCfg | Out-File -Encoding ASCII "$bootDrive\boot\grub\grub.cfg"

# unmount the iso
Write-Host ">>> Unmounting ISO..." -ForegroundColor Yellow
Dismount-DiskImage -ImagePath $isoPath

# set a new boot entry
Write-Host ">>> Set boot entry..." -ForegroundColor Yellow
$bcdOut = bcdedit /create /d "Ubuntu ISO Boot" /application bootsector
$guid = ($bcdOut | Select-String "{.*}").Matches.Value

bcdedit /set $guid device partition=$bootDrive
bcdedit /set $guid path \EFI\Boot\bootx64.efi
bcdedit /displayorder $guid /addfirst
bcdedit /default $guid
bcdedit /timeout 5

Write-Host "=== Ready! === Now you can reboot to the ISO." -ForegroundColor Green
