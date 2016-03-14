<properties
	pageTitle="Problembehandlung bei Azure-Diagnose"
	description="Behandeln von Problemen bei der Verwendung von Azure-Diagnose in Azure Cloud Services, Virtual Machines und"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Problembehandlung mit Azure-Diagnose
Informationen zur Problembehandlung mit Azure-Diagnose. Weitere Informationen zu Azure-Diagnose finden Sie unter [Überblick über Azure-Diagnose](azure-diagnostics.md#cloud-services).

## Die Azure-Diagnose wird nicht gestartet.
Diagnose besteht aus zwei Komponenten: einem Gast-Agent-Plug-in und Monitoring Agent.

In einer Clouddienstrolle befinden sich die Protokolldateien für das Gast-Agent-Plug-In in folgender Datei:

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

Auf einem virtuellen Azure-Computer befinden sich die Protokolldateien für das Gast-Agent-Plug-In in folgender Datei:

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

Die folgenden Fehlercodes werden von dem Plug-In zurückgegeben:

Exitcode|Beschreibung
---|---
0|Erfolgreich.
-1|Allgemeiner Fehler.
-2|Die RCF-Datei konnte nicht geladen werden.<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.
-3|Die Diagnosekonfigurationsdatei kann nicht geladen werden.<p><p>Lösung: Der Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. Gelöst werden kann der Fehler durch Bereitstellung einer Konfigurationsdatei, die dem Schema entspricht.
-4|Es wird bereits eine andere Instanz des Überwachungs-Agents der Diagnose unter Verwendung des lokalen Ressourcenverzeichnisses ausgeführt.<p><p>Lösung: Geben Sie für **LocalResourceDirectory** einen anderen Wert an.
-6|Das Startprogramm für das Gast-Agent-Plug-In hat versucht, die Diagnose mit einer ungültigen Befehlszeile zu starten.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.
-10|Das Diagnose-Plug-In wurde mit einem Ausnahmefehler beendet.
-11|Der Gast-Agent konnte den für den Start und die Überwachung des Überwachungs-Agents zuständigen Prozess nicht erstellen.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.<p>
-101|Ungültige Argumente beim Aufrufen des Diagnose-Plug-Ins.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.
-102|Der Plug-In-Prozess kann sich nicht selbst initialisieren.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.
-103|Der Plug-In-Prozess kann sich nicht selbst initialisieren. Insbesondere kann das Protokollierungsobjekt nicht erstellt werden.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.
-104|Die vom Gast-Agent bereitgestellte RCF-Datei konnte nicht geladen werden.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.
-105|Das Diagnose-Plug-In kann die Diagnosekonfigurationsdatei nicht öffnen.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.
-106|Die Diagnosekonfigurationsdatei kann nicht gelesen werden.<p><p>Lösung: Der Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. Gelöst werden kann der Fehler daher durch Bereitstellung einer Konfigurationsdatei, die dem Schema entspricht. Die XML-Datei, die an die Diagnose-Erweiterung übergeben wird, finden Sie im Ordner *%SystemDrive%\\WindowsAzure\\Config* auf dem virtuellen Computer. Öffnen Sie die betreffende XML-Datei, und suchen Sie nach **Microsoft.Azure.Diagnostics** und dann nach dem Feld **xmlCfg**. Die Daten sind base64-codiert und müssen daher zunächst [decodiert](http://www.bing.com/search?q=base64+decoder) werden, um die von der Diagnose geladenen XML-Daten anzuzeigen.<p>
-107|Das an den Überwachungs-Agent übergebene Ressourcenverzeichnis ist ungültig.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn der Überwachungs-Agent auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p>
-108 |Die Diagnosekonfigurationsdatei kann nicht in die Konfigurationsdatei des Überwachungs-Agents konvertiert werden.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In manuell mit einer ungültigen Konfigurationsdatei aufgerufen wird.
-110|Allgemeiner Fehler in der Diagnosekonfiguration.<p><p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In manuell mit einer ungültigen Konfigurationsdatei aufgerufen wird.
-111|Der Überwachungs-Agent kann nicht gestartet werden.<p><p>Lösung: Überprüfen Sie, ob genügend Systemressourcen verfügbar sind.
-112|Allgemeiner Fehler


## Diagnosedaten werden nicht in Azure Storage protokolliert
Azure-Diagnose speichert alle Daten in Azure Storage.

Wenn Ereignisdaten fehlen, liegt das in den meisten Fällen an falsch definierten Speicherkontoinformationen.

Lösung: Konfigurieren Sie die Diagnosekonfigurationsdatei, und installieren Sie Diagnose erneut. Wenn das Problem weiterhin auftritt, nachdem Sie die Diagnoseerweiterung neu installiert haben, müssen Sie möglicherweise das Debuggen fortsetzen und den Überwachungs-Agent auf Fehler überprüfen. Bevor Ereignisdaten in Ihr Speicherkonto hochgeladen werden, werden sie im LocalResourceDirectory gespeichert.

Für die Clouddienstrolle lautet das LocalResourceDirectory wie folgt:

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Für virtuelle Computer lautet das LocalResourceDirectory wie folgt:

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Wenn der LocalResourceDirectory-Ordner keine Dateien enthält, kann der Überwachungs-Agent nicht gestartet werden. Der Grund ist normalerweise eine ungültige Konfigurationsdatei, ein Ereignis, für das die Datei „CommandExecution.log“ einen entsprechenden Eintrag enthalten sollte.

Wenn der Überwachungs-Agent erfolgreich Ereignisdaten erfasst, werden für jedes in der Konfigurationsdatei definierte Ereignis TSF-Dateien angezeigt. Der Überwachungs-Agent protokolliert alle Fehler in der Datei „MaEventTable.tsf“. Um den Inhalt dieser Datei anzuzeigen, können Sie die TSF-Datei mithilfe der Anwendung table2csv in eine Datei mit durch Kommas getrennten Werten (CSV) konvertieren:

Bei einer Clouddienstrolle:

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* entspricht bei einer Clouddienstrolle in der Regel dem Laufwerk D:

Auf einem virtuellen Computer:

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Mit den oben aufgeführten Befehlen wird die Protokolldatei *maeventtable.csv* generiert, die Sie öffnen und auf Fehlermeldungen prüfen können.


## Tabellen mit Diagnosedaten nicht gefunden
Die Tabellen im Azure-Speicher, die Azure-Diagnosedaten enthalten, werden anhand des folgenden Codes benannt:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Beispiel:

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

Damit werden vier Tabellen generiert:

Ereignis|Tabellenname
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-->