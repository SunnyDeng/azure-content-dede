<properties
	pageTitle="PHP-Web- und -Workerrollen | Microsoft Azure"
	description="Ein Leitfaden zum Erstellen von PHP-Web- und -Workerrollen in einem Azure-Clouddienst und zum Konfigurieren der PHP-Laufzeit."
	services=""
	documentationCenter="php"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="robmcm"/>

#Erstellen von PHP-Web- und Workerrollen

## Übersicht

In diesem Leitfaden erfahren Sie, wie Sie PHP-Web- oder Workerrollen in einer Windows-Entwicklungsumgebung erstellen, eine bestimmte Version von PHP aus den "integrierten" verfügbaren Versionen auswählen, die PHP-Konfiguration ändern, Erweiterungen aktivieren und diese schließlich in Azure bereitstellen. Es wird zudem beschrieben, wie Sie eine Web- oder Workerrolle für die Nutzung einer PHP-Laufzeit konfigurieren (mit benutzerdefinierter Konfiguration und Erweiterungen), die Sie bereitstellen.

## Was sind PHP-Web- und Workerrollen?

Azure bietet drei Computemodelle für das Ausführen von Anwendungen: Azure App Service, Azure Virtual Machines und Azure Cloud Services. Alle drei Modell unterstützen PHP. Cloud Services enthält Web- und Workerrollen und bietet *Platform as a Service (PaaS)*. In einem Clouddienst stellt eine Webrolle einen dedizierten Internet Information Services (IIS)-Webserver zum Hosten von Front-End-Webanwendungen bereit. Eine Workerrolle kann asynchrone, langfristige oder fortwährende Aufgaben ausführen, die von einer Benutzerinteraktion oder -eingabe unabhängig sind.

Weitere Informationen zu diesen Optionen finden Sie unter [Computehostingoptionen in Azure](./cloud-services/fundamentals-application-models.md).

## Herunterladen des Azure SDK für PHP

Das [Azure SDK für PHP] besteht aus verschiedenen Komponenten. In diesem Artikel werden zwei Komponenten verwendet: Azure PowerShell und die Azure-Emulatoren. Diese beiden Komponenten können über den Microsoft-Webplattform-Installer installiert werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

## Erstellen eines Cloud-Dienst-Projekts

Der erste Schritt bei der Erstellung einer PHP-Web- oder -Workerrolle ist die Erstellung eines Azure-Dienstprojekts. Ein Azure-Dienstprojekt dient als logischer Container für Web- und Workerrollen und enthält das [Azure-Dienstdefinitionsschema (.csdef-Datei)] und das [Azure-Dienstkonfigurationsschema (.cscfg-Datei)] des Projekts.

Führen Sie Azure PowerShell als Administrator sowie folgenden Befehl aus, um ein neues Azure-Dienstprojekt zu erstellen:

	PS C:\>New-AzureServiceProject myProject

Durch diesen Befehl wird ein neues Verzeichnis (`myProject`) erstellt, dem Sie Web- und Workerrollen hinzufügen können.

## Hinzufügen von PHP-Web- und Workerrollen

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um eine PHP-Webrolle zu einem Projekt hinzuzufügen:

	PS C:\myProject> Add-AzurePHPWebRole roleName

Für eine Workerrolle verwenden Sie diesen Befehl:

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] Der Parameter `roleName` ist optional. Wenn er nicht angegeben wird, wird automatisch ein Name generiert. Die erste erstellte Webrolle heißt `WebRole1`, die zweite `WebRole2` usw. Die erste erstellte Workerrolle heißt `WorkerRole1`, die zweite `WorkerRole2` usw.

## Angeben der integrierten PHP-Version

Wenn Sie eine PHP-Web- oder Workerrolle zu einem Projekt hinzugefügt haben, ändern sich die Konfigurationsdateien des Projekts, sodass PHP bei der Bereitstellung auf jeder Web- oder Workerinstanz der Anwendung installiert wird. Führen Sie folgenden Befehl aus, um die PHP-Version anzuzeigen, die standardmäßig installiert wird:

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Die Ausgabe dieses Befehls wird ähnlich wie die unten angezeigte Ausgabe aussehen. In diesem Beispiel ist das Kennzeichen `IsDefault` für PHP 5.3.17 auf `true` festgelegt, das heißt, dass dies die installierte PHP-Standardversion ist.

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Sie können die PHP-Laufzeitversion auf alle der abgehörten PHP-Versionen festlegen. Um zum Beispiel die PHP-Version (für eine Rolle namens `roleName`) auf 5.4.0 festzulegen, verwenden Sie folgenden Befehl:

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Die verfügbaren PHP-Versionen können sich in Zukunft ändern.

## Anpassen der integrierten PHP-Laufzeit

Sie haben vollständige Kontrolle über die Konfiguration der PHP-Laufzeit, die mit den oben beschrieben Schritten installiert wird, einschließlich der Änderung der `php.ini`-Einstellungen und Aktivierung der Erweiterungen.

Führen Sie folgende Schritte aus, um die integrierte PHP-Laufzeit anzupassen:

1. Fügen Sie dem Verzeichnis `bin` einen neuen Ordner namens `php` der Webrolle hinzu. Für eine Workerrolle fügen Sie diesen zum Stammverzeichnis der Rolle hinzu.
2. Erstellen Sie im Ordner `php` einen weiteren Ordner namens `ext`. Legen Sie alle `.dll`-Erweiterungsdateien (z. B. `php_mongo.dll`), die Sie aktivieren möchten, in diesem Ordner ab.
3. Fügen Sie dem Ordner `php` die Datei `php.ini` hinzu. Aktivieren Sie alle benutzerdefinierten Erweiterungen, und legen Sie alle PHP-Direktiven in dieser Datei fest. Wenn Sie zum Beispiel `display_errors` und die Erweiterung `php_mongo.dll` aktivieren möchten, sieht der Inhalt der Datei `php.ini` folgendermaßen aus:

		display_errors=On
		extension=php_mongo.dll

> [AZURE.NOTE] Alle Einstellungen, die Sie nicht explizit in der bereitgestellten Datei `php.ini` festlegen, werden automatisch auf die Standardwerte festgelegt. Sie können jedoch auch eine komplette `php.ini`-Datei hinzufügen.

## Verwenden Ihrer eigenen PHP-Laufzeit
In einigen Fällen möchten Sie evtl. eine eigene PHP-Laufzeit angeben, anstatt eine integrierte PHP-Laufzeit auszuwählen und diese wie oben beschrieben zu konfigurieren. Sie können beispielsweise dieselbe PHP-Laufzeit in einer Web- oder Workerrolle verwenden, die Sie auch in Ihrer Entwicklungsumgebung verwenden. So lässt sich leichter sicherstellen, dass die Anwendung ihr Verhalten in der Produktionsumgebung nicht ändert.

### Konfigurieren einer Webrolle für die Verwendung Ihrer eigenen PHP-Laufzeit

Führen Sie die folgenden Schritte aus, um eine Webrolle für die Verwendung einer von Ihnen angegebenen PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Webrolle hinzu, wie weiter oben in diesem Thema beschrieben.
2. Erstellen Sie einen Ordner `php` im Ordner `bin`, der sich im Stammverzeichnis der Webrolle befindet, und fügen Sie dann die PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner usw.) dem Ordner `php` hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Laufzeit [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Webrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dazu den [sqlncli.msi x64-Installer] dem Ordner `bin` im Stammverzeichnis der Webrolle hinzu. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definieren Sie eine Startaufgabe, die [Internet Information Services (IIS)][iis.net] für die Verwendung Ihrer PHP-Laufzeit konfiguriert, um Anforderungen für `.php`-Seiten zu behandeln. Öffnen Sie dazu die Datei `setup_web.cmd` (in der Datei `bin` des Stammverzeichnisses Ihrer Webrolle) in einem Texteditor, und ersetzen Sie den Inhalt durch folgendes Skript:

		@ECHO ON
		cd "%~dp0"

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
		SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Fügen Sie die Anwendungsdateien zum Stammverzeichnis Ihrer Webrolle hinzu. Dies wird das Stammverzeichnis des Webservers sein.

6. Veröffentlichen Sie die Anwendung entsprechend der Beschreibung im Abschnitt [Veröffentlichen der Anwendung](#how-to-publish-your-application) weiter unten.

> [AZURE.NOTE] Das Skript `download.ps1` (im Ordner `bin` des Stammverzeichnisses der Webrolle) kann gelöscht werden, nachdem Sie die oben beschriebenen Schritte für die Verwendung Ihrer eigenen PHP-Laufzeit ausgeführt haben.

### Konfigurieren einer Workerrolle für die Verwendung Ihrer eigenen PHP-Laufzeit

Führen Sie die folgenden Schritte aus, um eine Workerrolle für die Verwendung einer von Ihnen angegebenen PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Workerrolle hinzu, wie weiter oben in diesem Thema beschrieben.
2. Erstellen Sie einen Ordner namens `php` im Stammverzeichnis der Workerrolle, und fügen Sie dann Ihre PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner usw.) dem Ordner `php` hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Laufzeit [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Workerrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dazu den [sqlncli.msi x64-Installer] dem Stammverzeichnis der Workerrolle hinzu. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definieren Sie eine Startaufgabe, die die ausführbare Datei `php.exe` bei der Rollenbereitstellung zur PATH-Umgebungsvariable der Workerrolle hinzufügt. Öffnen Sie dazu die Datei `setup_worker.cmd` (im Stammverzeichnis der Workerrolle) in einem Texteditor, und ersetzen Sie den Inhalt durch folgendes Skript:

		@echo on

		cd "%~dp0"

		echo Granting permissions for Network Service to the web root directory...
		icacls ..\ /grant "Network Service":(OI)(CI)W
		if %ERRORLEVEL% neq 0 goto error
		echo OK

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		setx Path "%PATH%;%~dp0php" /M

		if %ERRORLEVEL% neq 0 goto error

		echo SUCCESS
		exit /b 0

		:error

		echo FAILED
		exit /b -1

5. Fügen Sie die Anwendungsdateien zum Stammverzeichnis Ihrer Workerrolle hinzu.

6. Veröffentlichen Sie die Anwendung entsprechend der Beschreibung im Abschnitt [Veröffentlichen der Anwendung](#how-to-publish-your-application) weiter unten.

## Ausführen der Anwendung in den Server- und Speicheremulatoren

Die Azure-Emulatoren bieten eine lokale Umgebung, in der Sie Ihre Azure-Anwendung testen können, bevor Sie sie in der Cloud bereitstellen. Es gibt einige Unterschiede zwischen den Emulatoren und der Azure-Umgebung. Zum besseren Verständnis finden Sie weitere Informationen unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](./storage/storage-use-emulator.md).

PHP muss lokal installiert sein, um den Serveremulator nutzen zu können. Der Serveremulator verwendet Ihre lokale PHP-Installation, um die Anwendung auszuführen.

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um das Projekt in den Emulatoren auszuführen:

	PS C:\MyProject> Start-AzureEmulator

Eine Ausgabe ähnlich der folgenden wird angezeigt.

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Wenn Sie einen Webbrowser öffnen und zur lokalen Adresse in der Ausgabe (`http://127.0.0.1:81` in der Beispielausgabe oben) navigieren, können Sie im Emulator sehen, wie die Anwendung ausgeführt wird.

Führen Sie folgenden Befehl aus, um die Emulatoren zu stoppen:

	PS C:\MyProject> Stop-AzureEmulator

## Veröffentlichen der Anwendung

Um die Anwendung zu veröffentlichen, müssen Sie zunächst die Veröffentlichungseinstellungen mithilfe des Cmdlets [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) importieren. Anschließend können Sie die Anwendung mithilfe des Cmdlets [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) veröffentlichen. Informationen zum Anmelden finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

## Nächste Schritte

Weitere Informationen finden Sie im [PHP Developer Center](/develop/php/).

[Azure SDK für PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[Azure-Dienstdefinitionsschema (.csdef-Datei)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[Azure-Dienstkonfigurationsschema (.cscfg-Datei)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64-Installer]: http://go.microsoft.com/fwlink/?LinkID=239648

<!---HONumber=AcomDC_0323_2016-->