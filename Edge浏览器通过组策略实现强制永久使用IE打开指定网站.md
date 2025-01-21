参考[https://bbs.pcbeta.com/viewthread-1990717-1-1.html](https://bbs.pcbeta.com/viewthread-1990717-1-1.html)实现

下载文件

解压后运行Install.cmd

[EdgeToIE.zip](https://github.com/user-attachments/files/18257308/EdgeToIE.zip)


直接运行版本

[阿里云盘](https://www.alipan.com/s/eNrDX4Ha1F3)

Install.cmd
```batch
@ECHO OFF
TITLE Edge浏览器强制永久使用IE打开指定网站
SETLOCAL EnableDelayedExpansion

PUSHD "%~dp0"
CD /d "%~dp0"

NET SESSION >nul 2>&1
IF !ERRORLEVEL! NEQ 0 (
	ECHO =======================================
	ECHO     警告：此操作需要管理员权限执行！
	ECHO =======================================
	CHOICE /M "是否继续执行操作？" /C YN /D N /T 30
	IF !ERRORLEVEL! EQU 2 (
		ECHO.
		ECHO 您按下了[N]或未响应，操作已取消，工具即将退出。
		EXIT /b 1
	)
	CLS
    Powershell -Command "Start-Process cmd -ArgumentList '/c %~s0 %*' -Verb runAs"
    POPD
    EXIT /b
)

IF "%~1"=="/s" GOTO START

ECHO ====================================
ECHO     警告：此操作将修改系统配置。
ECHO ====================================
ECHO.
ECHO 请确保您已理解这些风险，并获得授权继续操作。
ECHO 如果您不确定，请选择 [否] 以安全退出。
ECHO.

CHOICE /M "是否继续执行操作？" /C YN /D N /T 30
IF !ERRORLEVEL! EQU 2 (
    ECHO.
    ECHO 您按下了[N]或未响应，操作已取消，工具即将退出。
    EXIT /b 1
)
CLS

:START

IF EXIST C:\InternetExplorerSiteList.xml (
    ECHO 功能已启用！
	ECHO.
    GOTO END
)

tasklist | findstr msedge.exe >nul 2>nul
IF !ERRORLEVEL! EQU 0 (
    ECHO 请关闭Edge浏览器！
    ECHO.
	pause 
	GOTO START
)

<nul set /p=正在添加组策略模板...
XCOPY PolicyDefinitions C:\Windows\PolicyDefinitions /E /I /Y >nul
IF !ERRORLEVEL! NEQ 0 (
    ECHO 添加组策略模板失败！
    EXIT /b !ERRORLEVEL!
)
ECHO 完成！
ECHO.

<nul set /p=正在写入强制IE打开的网站列表...
COPY /y InternetExplorerSiteList.xml C:\InternetExplorerSiteList.xml >nul
IF !ERRORLEVEL! NEQ 0 (
    ECHO 写入强制IE打开的网站列表失败！
    EXIT /b !ERRORLEVEL!
)
ECHO 完成！
ECHO.

<nul set /p=正在修改注册表项以启用策略...
REG add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v InternetExplorerIntegrationLevel /t reg_dword /d 1 /f >nul
IF !ERRORLEVEL! NEQ 0 (
    ECHO 修改注册表项 InternetExplorerIntegrationLevel 失败！
    EXIT /b !ERRORLEVEL!
)

REG add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge" /v InternetExplorerIntegrationSiteList /t reg_sz /d "file:///C:/InternetExplorerSiteList.xml" /f >nul
IF !ERRORLEVEL! NEQ 0 (
    ECHO 修改注册表项 InternetExplorerIntegrationSiteList 失败！
    EXIT /b !ERRORLEVEL!
)
ECHO 完成！
ECHO.

<nul set /p=正在应用修改后的组策略...
GPUPDATE /force >nul
IF !ERRORLEVEL! NEQ 0 (
    ECHO 应用组策略修改失败！
    EXIT /b !ERRORLEVEL!
)
ECHO 完成！
ECHO.

:END
ECHO 如需修改网站列表，请编辑文件"C:\InternetExplorerSiteList.xml"的内容！
ECHO 编辑后需要完全重启Edge浏览器来应用修改！
ECHO.
CHOICE /c EQ /n /m "按 E 键打开编辑器，按 Q 键退出..."
IF !ERRORLEVEL! EQU 1 (
    START notepad C:\InternetExplorerSiteList.xml >nul
)

ENDLOCAL
EXIT
```
