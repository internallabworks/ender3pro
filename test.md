

```powershell
$VMLocation = "E:\vms"
$VMISO = "C:\BuildDeploy\Boot\LiteTouchMedia.iso"
$VMNetwork = "Bridged"
 
# Create K002
$VMName = "K002"
$VMMemory = 4096MB
$VMDiskSize = 60GB
New-VM -Name $VMName -BootDevice CD -MemoryStartupBytes $VMMemory -SwitchName $VMNetwork -Path $VMLocation -NoVHD -Verbose
New-VHD -Path "$VMLocation\$VMName\Virtual Hard Disks\$VMName-Disk1.vhdx" -SizeBytes $VMDiskSize -Verbose
Add-VMHardDiskDrive -VMName $VMName -Path "$VMLocation\$VMName\Virtual Hard Disks\$VMName-Disk1.vhdx" -Verbose
Set-VMDvdDrive -VMName $VMName -Path $VMISO -Verbose
Set-VMMemory -VMName $VMName -DynamicMemoryEnabled $true -MinimumBytes 1024MB -MaximumBytes 4096MB -StartupBytes 2048MB  -Buffer 20
Set-VMNetworkAdapter -StaticMacAddress "00:15:5D:01:FB:33" -VMName $VMName
Start-VM -VMName $VMName
 
$VM = Get-VM -Name $VMName
while ($VM.State -ne "off")
{ 
    write-host "The VM is still running"
    sleep 30 
}
# Delete the VM
Remove-VM -Name $VMName -Force
Remove-Item -Recurse -Force C:\vms\$VMName
Copy-Item -Path C:\BuildDeploy\Captures\w10x64enteng.wim -Destination '\\wds01\CustomImages$\Captures' -Force
Move-Item -Path C:\BuildDeploy\Captures\w7x64enteng.wim -Destination 'C:\BuildDeploy\Operating Systems\Win7entrefeng270211\Win7entrefeng.wim' -Force
```

