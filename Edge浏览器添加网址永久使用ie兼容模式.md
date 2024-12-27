参考https://bbs.pcbeta.com/viewthread-1990717-1-1.html实现

下载文件
解压后运行Install.cmd

[EdgeToIE.zip](https://github.com/user-attachments/files/18257016/EdgeToIE.zip)

Install.cmd
```batch
@ECHO OFF
SETLOCAL EnableDelayedExpansion
TITLE 强制网站使用InternetExplorer打开

PUSHD %~dp0 & CD /d "%~dp0"
%1 %2
mshta vbscript:createobject("shell.application").shellexecute("%~s0","goto :runas","","runas",1)(window.close)&goto :eof
:runas

if exist C:\InternetExplorerSiteList.xml (
    echo 功能已启用
    GOTO END
)
echo 文件不存在，继续执行其他操作...

<nul set /p=正在添加组策略模板...
XCOPY PolicyDefinitions C:\Windows\PolicyDefinitions /E /I /Y >nul
ECHO 完成

<nul set /p=正在写入强制IE打开的网站列表...
COPY /y InternetExplorerSiteList.xml C:\InternetExplorerSiteList.xml >nul
ECHO 完成

<nul set /p=正在修改注册表项以启用策略...
REG add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v InternetExplorerIntegrationLevel /t reg_dword /d 1 /f >nul
REG add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v InternetExplorerIntegrationSiteList /t reg_sz /d "file:///C:/InternetExplorerSiteList.xml" /f >nul
ECHO 完成

<nul set /p=正在应用所有修改...
GPUPDATE /force >nul
ECHO 完成

:END
ECHO 如需修改强制使用IE打开的网站列表，请编辑C:\InternetExplorerSiteList.xml的内容！

choice /c YN /n /m "按 Y 键打开编辑器，按 N 键退出..."
if errorlevel 2 (
     pause >nul
) else (
    start notepad C:\InternetExplorerSiteList.xml >nul
)
```
