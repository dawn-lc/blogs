```batch
@ECHO off
TITLE Starting script...
WHERE /q PowerShell 
IF %ERRORLEVEL% NEQ 0 ECHO PowerShell is not installed. && PAUSE > NUL && EXIT
PowerShell -NoProfile -ExecutionPolicy Bypass -Command "if ($PSVersionTable.PSVersion.Major -lt 3) {throw 'Requires PowerShell 3.0 or higher.'}; $content = Get-Content -Path '%~f0' -Raw -Encoding UTF8; $mainIndex = $content.LastIndexOf('#POWERSHELL#'); if ($mainIndex -lt 0) {throw 'PowerShell script not found.'}; $code = $content.Substring($mainIndex + '#POWERSHELL#'.Length); $script = [ScriptBlock]::Create($code); & $script @args -- %*"
EXIT
#POWERSHELL#
Write-Host "你好，世界"
Write-Host $pwd
Write-Host "收到参数数量：$($args.Count)"
for ($i = 0; $i -lt $args.Count; $i++) {
    Write-Host "参数 ${i}: $($args[$i])"
}
Write-Host "按任意键继续..."
[void][System.Console]::ReadKey($true)
Exit
```
使用方法：

在`#POWERSHELL#`后编写PowerShell脚本。
将编写好的脚本另保存为cmd后缀名。
ps脚本即可直接运行。
