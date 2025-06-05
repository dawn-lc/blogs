```batch
@ECHO off
SETLOCAL EnableDelayedExpansion
TITLE Initializing Script...
<NUL SET /p="Checking system requirements ... "
WHERE /q PowerShell 
IF !ERRORLEVEL! NEQ 0 (ECHO PowerShell is not installed. & PAUSE & EXIT)
PowerShell -Command "if ($PSVersionTable.PSVersion.Major -lt 3) { exit 1 }"
IF !ERRORLEVEL! NEQ 0 (ECHO Requires PowerShell 3.0 or later. & PAUSE & EXIT)
PowerShell -Command "$content = Get-Content -Path '%~f0' -Raw -Encoding UTF8; if ($content.LastIndexOf('#POWERSHELL#') -le 0) { exit 1 }"
IF !ERRORLEVEL! NEQ 0 (ECHO Embedded script section not found. & PAUSE & EXIT)
ECHO OK
FOR /f %%a IN ('PowerShell -Command "[guid]::NewGuid().ToString('n')"') do set "GUID=%%a"
SET "TempScriptPath=!TEMP!\tmp_!GUID!.ps1"
PowerShell -Command "$content = Get-Content -Path '%~f0' -Raw -Encoding UTF8; Set-Content -Encoding UTF8 -Path '!TempScriptPath!' -Value $content.Substring($content.LastIndexOf('#POWERSHELL#') + '#POWERSHELL#'.Length);"
PowerShell -NoProfile -ExecutionPolicy Bypass -File "!TempScriptPath!" %*
DEL /f /q "!TempScriptPath!"
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
