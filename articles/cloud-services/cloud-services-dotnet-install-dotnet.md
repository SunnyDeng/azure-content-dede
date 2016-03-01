<properties
   pageTitle="Installieren von .NET in einer Clouddienstrolle"
   description="Dieser Artikel beschreibt die manuelle Installation eines .NET Frameworks in Clouddienstweb- und Workerrollen."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="saurabh"/>

# Installieren von .NET in einer Clouddienstrolle 

Dieser Artikel beschreibt die Installation eines .NET Frameworks auf Clouddienst-Web- und Workerrollen. Über diese Schritte können Sie .NET 4.6.1 für die Azure-Gastbetriebssystemfamilie 4 installieren. Neueste Informationen zu Gast-BS-Versionen finden Sie unter [Azure Gast-BS-Versionen und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).

Die Installation von .NET auf ihren Web- und Workerrollen schließt die Erfassung des .NET Installationspakets als Teil Ihres Cloudprojekts und außerdem das Starten des Installationsprogramms als Teil der Startaufgaben der Rolle ein.

## Das Installationsprogramm für .NET zu Ihrem Projekt hinzufügen
- Laden Sie das Webinstallationsprogramm für das .NET Framework herunter, das Sie installieren möchten
	- [Webinstallationsprogramm für .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
- Für eine Webrolle
  1. Klicken Sie im **Projektmappen-Explorer** unter **Rollen** im Clouddienstprojekt mit der rechten Maustaste auf Ihre Rolle, und wählen Sie **Hinzufügen > Neuer Ordner** aus. Erstellen Sie einen Ordner mit dem Namen *bin*.
  2. Klicken Sie mit der rechten Maustaste auf den Ordner **bin**, und wählen Sie **Hinzufügen > Vorhandenes Element** aus. Wählen Sie das Installationsprogramm für .NET aus und fügen Sie es dem Ordner "Bin" hinzu.
- Für eine Workerrolle
  1. Klicken Sie mit der rechten Maustaste auf Ihre Rolle, und wählen Sie **Hinzufügen > Vorhandenes Element** aus. Wählen Sie das Installationsprogramm für .NET aus und fügen Sie zur Rolle hinzu. 

Dateien, die auf diese Weise dem Inhaltsordner "Rolle" hinzugefügt werden, werden automatisch dem Clouddienstpaket hinzugefügt und an einem einheitlichen Ort auf dem virtuellen Computer bereitgestellt. Wiederholen Sie diesen Vorgang für alle Web- und Workerrollen in Ihrem Clouddienst, sodass alle Rollen über eine Kopie des Installationsprogramms verfügen.

> [AZURE.NOTE] Installieren Sie .NET 4.6.1 für Ihre Clouddienstrolle, selbst wenn Ihre Anwendung .NET 4.6 verwendet. Das Azure-Gastbetriebssystem enthält die Updates [3098779](https://support.microsoft.com/kb/3098779) und [3097997](https://support.microsoft.com/kb/3097997). Die Installation von .NET 4.6 zusätzlich zu diesen Updates kann zu Problemen bei der Ausführung Ihrer .NET-Anwendungen führen, weshalb Sie direkt .NET 4.6.1 anstelle von .NET 4.6 installieren sollten. Weitere Informationen finden Sie unter [KB 3118750](https://support.microsoft.com/kb/3118750).

![Inhalte der Rolle mit Installer-Dateien][1]

## Definieren von Startaufgaben für Ihre Rollen
Mit Startaufgaben können Sie Vorgänge ausführen, bevor eine Rolle gestartet wird. Die Installation von .NET Framework als Teil der Startaufgabe stellt sicher, dass das Framework installiert ist, bevor Ihr Anwendungscode ausgeführt wird. Weitere Informationen zu Startaufgaben finden Sie unter: [Startaufgaben in Azure ausführen](cloud-services-startup-tasks.md).

1. Fügen Sie zur Datei *ServiceDefinition.csdef* unter den Knoten **Webrolle** oder **Workerrolle** für alle Rollen Folgendes hinzu:
	
	```xml
	<LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
	<Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
	```

	Die oben genannte Konfiguration führt den Konsolenbefehl *install.cmd* mit Administratorrechten aus, damit dieser das .NET Framework installieren kann. Über die Konfiguration wird auch ein LocalStorage mit dem Namen *NETFXInstall* erstellt. Das Startskript stellt das temporäre Verzeichnis so ein, dass diese lokale Speicherressource genutzt wird, damit das Installationsprogramm für .NET-Framework von dieser Ressource heruntergeladen und installiert wird. Es ist wichtig, die Größe dieser Ressource auf mindestens 1.024 MB einzustellen, damit das Framework ordnungsgemäß installiert werden kann. Weitere Informationen zu Startaufgaben finden Sie unter [Allgemeine Startaufgaben für Clouddienste](cloud-services-startup-tasks-common.md).

2. Erstellen Sie die Datei **install.cmd** und fügen Sie sie allen Rollen hinzu, indem Sie einen Rechtsklick auf die Rolle machen und **Hinzufügen > Vorhandenes Element...** auswählen. Nun sollten alle Rollen über die .NET Installerdateien sowie die Datei install.cmd verfügen.
	
	![Inhalte der Rolle mit allen Dateien][2]

	> [AZURE.NOTE] Verwenden Sie einen einfachen Texteditor wie Notepad, um diese Datei zu erstellen. Wenn Sie Visual Studio verwenden, um eine Textdatei erstellen und diese dann in ".cmd" umbenennen, enthält die Datei möglicherweise weiterhin eine UTF-8-Bytereihenfolge-Markierung und das Ausführen der ersten Zeile des Skripts führt zu einem Fehler. Verwenden Sie Visual Studio, um die Datei zu erstellen, fügen Sie eine REM (Anmerkung) zur ersten Zeile der Datei hinzu, damit diese bei der Ausführung ignoriert wird.

3. Fügen Sie das folgende Skript aus der **install.cmd**-Datei hinzu:

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP461"
	
	
	REM ***** Set script start timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set "log=install.cmd started %timestamp%."
	
	REM ***** Exit script if running in Emulator *****
	if %ComputeEmulatorRunning%=="true" goto exit
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set "netfxinstallfile=NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
	
	:NDP46
	set "netfxinstallfile=NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
	
	:NDP461
	set "netfxinstallfile=NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
	
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	echo %log% >> %startuptasklog%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto installed
	
	REM ***** Installing .NET *****
	echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
	if %ERRORLEVEL%== 0 goto installed
		echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%	
		if %ERRORLEVEL%== 3010 goto restart
		if %ERRORLEVEL%== 1641 goto restart
		echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
	
	:restart
	echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
	goto end
	
	:installed
	echo .NET (%netfx%) is installed >> %startuptasklog%
	
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	
	:exit
	EXIT /B 0
	```
		
	Das Installationsskript überprüft, ob die angegebene .NET-Framework-Version bereits auf dem Computer installiert ist, indem die Registrierungsdatenbank abgefragt wird. Wenn die .NET-Version nicht installiert ist, wird das .NET-Webinstallationsprogramm gestartet. Zur Unterstützung bei der Problembehandlung protokolliert das Skript alle Aktivitäten in einer Datei mit dem Namen *startuptasklog-(currentdatetime).txt*, die unter *InstallLogs* im lokalen Speicher abgelegt ist.

	> [AZURE.NOTE] Das Skript zeigt aus Gründen der Kontinuität immer noch die Installation von .NET 4.5.2 oder .NET 4.6. Es ist nicht erforderlich, .NET 4.5.2 manuell zu installieren, da es bereits für das Azure-Gastbetriebssystem verfügbar ist. Anstatt .NET 4.6 zu installieren, sollten Sie gemäß [KB 3118750](https://support.microsoft.com/kb/3118750) .NET 4.6.1 installieren.
      

## Konfigurieren der Diagnose zur Übertragung der Startaufgabenprotokolle in Blobspeicher 
Zur Vereinfachung der Problembehandlung können Sie die Azure-Diagnose so konfigurieren, dass alle Protokolldateien, die durch das Startskript oder Installationsprogramm für .NET erstellt wurden, in Blobspeicher übertragen werden. Bei diesem Ansatz können Sie die Protokolle anzeigen, indem Sie einfach die Protokolldateien aus dem Blobspeicher herunterladen, anstatt sich per Remotedesktop mit der Rolle anzumelden.

Öffnen Sie zur Konfiguration der Diagnose *diagnostics.wadcfgx* und fügen Sie Folgendes unter dem Knoten **Verzeichnisse** hinzu:

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Dadurch wird Azure-Diagnose zur Übertragung aller Dateien im Verzeichnis *log* unter der Ressource *NETFXInstall* in das Diagnose-Speicherkonto des Blobcontainers *netfx-install* konfiguriert.

## Ihren Dienst bereitstellen 
Wenn Sie Ihren Dienst bereitstellen, wird die Startaufgabe ausgeführt und das .NET Framework installiert, sofern es nicht bereits installiert ist. Ihre Rollen befinden sich im Status "Beschäftigt", während das Framework installiert wird und werden möglicherweise sogar neu gestartet, wenn die Framework-Installation dies erforderlich macht.

## Zusätzliche Ressourcen

- [Installation des .NET Frameworks][]
- [Gewusst wie: Feststellen, welche Versionen von .NET Framework installiert sind][]
- [Problembehandlung bei .NET Framework-Installationen][]

[Gewusst wie: Feststellen, welche Versionen von .NET Framework installiert sind]: https://msdn.microsoft.com/library/hh925568.aspx
[Installation des .NET Frameworks]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Problembehandlung bei .NET Framework-Installationen]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_0218_2016-->