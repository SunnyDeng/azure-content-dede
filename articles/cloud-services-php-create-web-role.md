<properties urlDisplayName="Create Web and Worker Roles" pageTitle="Erstellen von Web- und Workerrollen" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="PHP" title="How to create PHP web and worker roles" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/28/2014" ms.author="tomfitz" />

#Erstellen von PHP-Web- und Workerrollen

In diesem Leitfaden erfahren Sie, wie Sie PHP-Web- oder Workerrollen in einer Windows-Entwicklungsumgebung erstellen, eine bestimmte Version von PHP der "integrierten" verfügbaren Versionen auswählen, die PHP-Konfiguration ändern, Erweiterungen aktivieren und diese schließlich zu Azure bereitstellen. Es wird zudem beschrieben, wie Sie eine Web- oder Workerrolle für die Nutzung einer PHP-Laufzeit konfigurieren (mit benutzerdefinierter Konfiguration und Erweiterungen), die Sie bereitstellen.

##<a name="TableOfContents"></a>Inhaltsverzeichnis
* [Was sind PHP-Web- und Workerrollen?](#WhatIs)
* [Herunterladen des Azure SDK für PHP](#DownloadSdk)
* [Gewusst wie: Erstellen eines Cloud-Dienst-Projekts](#CreateProject)
* [Gewusst wie: Hinzufügen von PHP-Web- und Workerrollen](#AddRole)
* [Gewusst wie: Angeben der integrierten PHP-Version](#SpecifyPHPVersion)
* [Gewusst wie: Anpassen der integrierten PHP-Laufzeit](#CustomizePHP)
* [Gewusst wie: Verwenden Ihrer eigenen PHP-Laufzeit](#OwnPHP)
* [Gewusst wie: Ausführen der Anwendung in den Server- und Speicheremulatoren](#Emulators)
* [Gewusst wie: Veröffentlichen der Anwendung](#Publish) 

##<a name="WhatIs"></a>Was sind PHP-Web- und Workerrollen?
Azure bietet drei Modelle für die Ausführung von Anwendungen: [Azure-Websites][execution model-web sites], [Azure Virtual Machines][execution model-vms] und [Azure Cloud Services][execution model-cloud services]. Alle drei Modelle unterstützen PHP. Cloud Services, welche Web- und Workerrollen enthalten, bieten *Platform as a Service (PaaS)*. In einem Cloud-Dienst bietet eine Webrolle einen speziellen IIS-Webserver (Internet Information Services), um Front-End-Webanwendungen zu hosten, während eine Workerrolle asynchrone, langfristige oder fortwährende Aufgaben ausführen kann, die unabhängig von einer Benutzerinteraktion oder -eingabe sind.

Weitere Informationen finden Sie unter [Was ist ein Cloud-Dienst?].

##<a name="DownloadSdk"></a>Herunterladen des Azure SDK für PHP

Das [Azure SDK für PHP] besteht aus verschiedenen Komponenten. In diesem Artikel werden zwei Komponenten verwendet: Azure PowerShell und Azure-Emulatoren. Diese beiden Komponenten können über den Microsoft-Webplattform-Installer installiert werden: [Azure PowerShell und Azure-Emulatoren installieren][install ps and emulators].

##<a name="CreateProject"></a>Gewusst wie: Erstellen eines Cloud-Dienst-Projekts

Der erste Schritt bei der Erstellung einer PHP-Web- oder Workerrolle ist die Erstellung eines Azure-Dienstprojekts. Ein Azure-Dienstprojekt dient als logischer Container für Web- und Workerrollen und enthält die [Dienstdefinitionsdateien (.csdef)] und [Dienstkonfigurationsdateien (.cscfg)] des Projekts. 

Führen Sie folgenden Befehl aus, um ein neues Azure-Dienstprojekt zu erstellen:

	PS C:\>New-AzureServiceProject myProject

Durch diesen Befehl wird ein neues Verzeichnis ("myProject") erstellt, zu dem Sie Web- und Workerrollen hinzufügen können.

##<a name="AddRole"></a>Gewusst wie: Hinzufügen von PHP-Web- und Workerrollen

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um eine PHP-Webrolle zu einem Projekt hinzuzufügen:

	PS C:\myProject> Add-AzurePHPWebRole roleName

Für eine Workerrolle verwenden Sie diesen Befehl:

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Der <code>roleName</code> -Parameter ist optional. Wenn er nicht angegeben wird, wird automatisch ein Name generiert. Die erste erstellte Webrolle ist <code>WebRole1</code>, die zweite <code>WebRole2</code>und so weiter. Die erste erstellte Workerrolle ist <code>WorkerRole1</code>, die zweite <code>WorkerRole2</code>und so weiter.</p> 
</div>

##<a name="SpecifyPHPVersion"></a>Gewusst wie: Angeben der integrierten PHP-Version

Wenn Sie eine PHP-Web- oder Workerrolle zu einem Projekt hinzugefügt haben, ändern sich die Konfigurationsdateien des Projekts, sodass PHP bei der Bereitstellung auf jeder Web- oder Workerinstanz der Anwendung installiert wird. Führen Sie folgenden Befehl aus, um die PHP-Version anzuzeigen, die standardmäßig installiert wird:

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Die Ausgabe dieses Befehls wird ähnlich wie die unten angezeigte Ausgabe aussehen. In diesem Beispiel ist das IsDefault-Kennzeichen auf "true" für PHP 5.3.17 festgelegt, das heißt, dass dies die installierte Standard-PHP-Version sein wird. 

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Sie können die PHP-Laufzeitversion auf alle der abgehörten PHP-Versionen festlegen. Um zum Beispiel die PHP-Version (für eine Rolle namens "roleName") auf 5.4.0 festzulegen, verwenden Sie folgenden Befehl:

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>In Zukunft werden weitere PHP-Versionen verfügbar sein, und die verfügbaren Versionen können sich ändern.</p> 
</div>

##<a name="CustomizePHP"></a>Gewusst wie: Anpassen der integrierten PHP-Laufzeit

Sie haben die vollständige Kontrolle über die Konfiguration der PHP-Laufzeit, die installiert ist, wenn Sie die Schritte oben befolgen, einschließlich der Änderung der php.ini-Einstellungen und Aktivierung der Erweiterungen.

Führen Sie folgende Schritte aus, um die integrierte PHP-Laufzeit anzupassen:

1. Fügen Sie einen neuen Ordner namens "php" zum Verzeichnis "bin" der Webrolle hinzu. Für eine Workerrolle fügen Sie diesen zum Stammverzeichnis der Rolle hinzu.
2. Erstellen Sie im Ordner "php" einen anderen Ordner namens "ext". Legen Sie alle .dll-Erweiterungsdateien (z. B. php_mongo.dll), die Sie aktivieren möchten, in diesen Ordner.
3. Fügen Sie eine php.ini-Datei zum Ordner "php" hinzu. Aktivieren Sie alle benutzerdefinierten Erweiterungen, und legen Sie alle PHP-Direktiven in dieser Datei fest. Wenn Sie zum Beispiel "display_errors" und die Erweiterung "php_mongo.dll" aktivieren möchten, wäre der Inhalt der Datei "php.ini" folgendermaßen:

		display_errors=On
		extension=php_mongo.dll

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Alle Einstellungen, die Sie nicht explizit in der von Ihnen bereitgestellten Datei <code>php.ini</code> vornehmen, werden automatisch auf die Standardwerte gesetzt. Sie können jedoch auch eine komplette Datei <code>php.ini</code> hinzufügen. </p> 
</div>

##<a name="OwnPHP"></a>Gewusst wie: Verwenden Ihrer eigenen PHP-Laufzeit
In einigen Fällen möchten Sie evtl. eine eigene PHP-Laufzeit angeben, anstatt eine integrierte PHP-Laufzeit auszuwählen und diese wie oben beschrieben zu konfigurieren. Sie können zum Beispiel dieselbe PHP-Laufzeit in einer Web- oder Workerrolle verwenden, die Sie auch in Ihrer Entwicklungsumgebung verwenden, wodurch Sie leichter sicherstellen können, dass die Anwendung nicht ihr Verhalten in der Produktionsumgebung ändert.

<h3><a name="OwnPHPWebRole"></a>Konfigurieren einer Webrolle für die Verwendung Ihrer eigenen PHP-Laufzeit</h3>

Folgen Sie den Schritten unten, um eine Webrolle für die Verwendung einer von Ihnen bereitgestellten PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Webrolle hinzu, wie unter [Gewusst wie: Erstellen eines Cloud-Dienst-Projekts](#CreateProject) und [Gewusst wie: Hinzufügen von PHP-Web- und Workerrollen](#AddRole) oben beschrieben.
2. Erstellen Sie einen Ordner "php" im Ordner "bin", der sich im Stammverzeichnis der Webrolle befindet, und fügen Sie die PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner, etc.) zum Ordner "php" hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Laufzeit [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Webrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dazu das sqlncli.msi-Installationsprogramm zum Ordner "bin" im Stammverzeichnis der Webrolle hinzu. Sie können das Installationsprogramm hier herunterladen: [sqlncli.msi x64-Installationsprogramm]. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Im nächsten Schritt wird eine Startaufgabe definiert, die [Internetinformationsdienste (IIS)][iis.net] für die Verwendung Ihrer PHP-Laufzeit konfiguriert, um Anforderungen für PHP-Seiten zu behandeln. Öffnen Sie dazu die Datei "setup_web.cmd" (im Ordner "bin" des Stammverzeichnisses Ihrer Webrolle) in einem Texteditor, und ersetzen Sie die Inhalte mit folgendem Skript:

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

6. Veröffentlichen Sie Ihre Anwendung wie im Abschnitt [Gewusst wie: Veröffentlichen der Anwendung](#Publish) unten beschrieben.

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Das <code>download.ps1</code> -Skript (im Ordner <code>bin</code> des Stammverzeichnisses der Webrolle) kann gelöscht werden, wenn Sie die hier beschriebenen Schritte für die Verwendung Ihrer eigenen PHP-Laufzeit befolgt haben.</p> 
</div>

<h3><a name="OwnPHPWorkerRole"></a>Konfigurieren einer Workerrolle für die Verwendung Ihrer eigenen PHP-Laufzeit</h3>

Folgen Sie den Schritten unten, um eine Workerrolle für die Verwendung einer von Ihnen bereitgestellten PHP-Laufzeit zu konfigurieren.

1. Erstellen Sie ein Azure-Dienstprojekt, und fügen Sie eine PHP-Workerrolle hinzu, wie unter [Gewusst wie: Erstellen eines Cloud-Dienst-Projekts](#CreateProject) und [Gewusst wie: Hinzufügen von PHP-Web- und Workerrollen](#AddRole) oben beschrieben.
2. Erstellen Sie einen Ordner "php" im Stammverzeichnis der Workerrolle, und fügen Sie die PHP-Laufzeit (alle Binärdateien, Konfigurationsdateien, Unterordner, etc.) zum Ordner "php" hinzu.
3. (OPTIONAL) Wenn Ihre PHP-Laufzeit [Microsoft-Treiber für PHP für SQL Server][sqlsrv drivers] verwendet, müssen Sie die Workerrolle bei der Bereitstellung für die Installation von [SQL Server Native Client 2012][sql native client] konfigurieren. Fügen Sie dafür das sqlncli.msi-Installationsprogramm zum Stammverzeichnis der Workerrolle hinzu. Sie können das Installationsprogramm hier herunterladen: [sqlncli.msi x64-Installationsprogramm]. Das im nächsten Schritt beschriebene Startskript wird das Installationsprogramm ausführen, wenn die Rolle bereitgestellt wird. Wenn Ihre PHP-Laufzeit nicht Microsoft-Treiber für PHP für SQL Server verwendet, können Sie folgende Zeile aus dem im nächsten Schritt gezeigten Skript entfernen:

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Als nächsten Schritt definieren Sie eine Startaufgabe, welche die ausführbare Datei "php.exe" bei der Bereitstellung zur PATH-Umgebungsvariablen der Workerrolle hinzufügt. Öffnen Sie dazu die Datei "setup_worker.cmd" (im Stammverzeichnis der Workerrolle) in einem Texteditor, und ersetzen Sie die Inhalte mit folgendem Skript:

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

6. Veröffentlichen Sie Ihre Anwendung wie im Abschnitt [Gewusst wie: Veröffentlichen der Anwendung](#Publish) unten beschrieben.

##<a name="Emulators"></a>Gewusst wie: Ausführen der Anwendung in den Server- und Speicheremulatoren

Die Server- und Speicheremulatoren bieten eine lokale Umgebung, in der Sie Ihre Azure-Anwendung testen können, bevor Sie diese in der Cloud bereitstellen. Es gibt einige Unterschiede zwischen den Emulatoren und der Azure-Umgebung. Weitere Informationen zum besseren Verständnis finden Sie unter [Unterschiede zwischen dem Serveremulator und Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg432960.aspx) und [Unterschiede zwischen dem Speicheremulator und den Azure-Speicherdiensten](http://msdn.microsoft.com/de-de/library/windowsazure/gg433135.aspx).

Sie müssen PHP lokal installiert haben, um den Serveremulator zu nutzen. Der Serveremulator verwendet Ihre lokale PHP-Installation, um die Anwendung auszuführen.

Führen Sie folgenden Befehl im Stammverzeichnis des Projekts aus, um das Projekt in den Emulatoren auszuführen:

	PS C:\MyProject> Start-AzureEmulator

Die Ausgabe wird ähnlich wie diese aussehen:

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Sie können sehen, dass die Anwendung im Emulator ausgeführt wird, indem Sie einen Webbrowser öffnen und zur lokalen Adresse navigieren, die in der Ausgabe angezeigt wird (`http://127.0.0.1:81` in der Beispielausgabe oben).

Führen Sie folgenden Befehl aus, um die Emulatoren zu stoppen:

	PS C:\MyProject> Stop-AzureEmulator

##<a name="Publish"></a>Gewusst wie: Veröffentlichen der Anwendung

Um die Anwendung zu veröffentlichen, müssen Sie zuerst die Veröffentlichungseinstellungen mit dem **Import-PublishSettingsFile**-Cmdlet importieren. Anschließend können Sie die Anwendung mit dem **Publish-AzureServiceProject**-Cmdlet veröffentlichen. Details zur Verwendung dieser Cmdlets finden Sie unter [Gewusst wie: Veröffentlichungseinstellungen importieren] und [Gewusst wie: Bereitstellen eines Cloud-Diensts zu Azure].

[execution model-web sites]: /de-de/develop/net/fundamentals/compute/#WebSites
[execution model-vms]: /de-de/develop/net/fundamentals/compute/#VMachine
[execution model-cloud services]: /de-de/develop/net/fundamentals/compute/#CloudServices
[Azure SDK für PHP]: /de-de/develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
[Was ist ein Cloud-Dienst?]: /de-de/manage/services/cloud-services/what-is-a-cloud-service/
[Dienstdefinitionsdateien (.csdef)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758711.aspx
[Dienstkonfigurationsdateien (.cscfg)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[SQL Native Client]: http://msdn.microsoft.com/de-de/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64-Installationsprogramm]: http://go.microsoft.com/fwlink/?LinkID=239648
[Gewusst wie: Veröffentlichungseinstellungen importieren]: /de-de/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
[Gewusst wie: Bereitstellen eines Cloud-Diensts zu Azure]: /de-de/develop/php/how-to-guides/powershell-cmdlets/#Deploy

<!--HONumber=35.1-->
