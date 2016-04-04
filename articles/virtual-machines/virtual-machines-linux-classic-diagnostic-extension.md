
<properties
		pageTitle="Überwachen eines Linux-VM mit einer VM-Erweiterung | Microsoft Azure"
		description="Hier erfahren Sie, wie Sie die Linux-Diagnoseerweiterung zur Überwachung der Leistungs- und Diagnosedaten einer Linux-VM in Azure verwenden."
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Verwenden der Linux-Diagnoseerweiterung zur Überwachung der Leistungs- und Diagnosedaten eines Linux-VM

## Einführung

Die Linux-Diagnoseerweiterung unterstützt Benutzer mit den folgenden Funktionen bei der Überwachung von Linux-VMs unter Microsoft Azure:

- Sammeln und Hochladen von Systemleistungs-, Diagnose- und Systemprotokolldaten von virtuellen Linux-Computern in die Speichertabelle eines Benutzers
- Anpassen der zu sammelnden und hochzuladenden Datenmetriken durch den Benutzer
- Hochladen angegebener Protokolldateien in eine festgelegte Speichertabelle durch den Benutzer

In Version 2.0 werden folgende Daten verarbeitet:

- Alle Rsyslog-Protokolle von Linux, einschließlich System-, Sicherheits- und Anwendungsprotokolle.
- Alle in [diesem Dokument](https://scx.codeplex.com/wikipage?title=xplatproviders") angegebenen Systemdaten.
- Benutzerdefinierte Protokolldateien.

Beachten Sie, dass diese Erweiterung sowohl mit dem klassischen als auch dem Ressourcen-Manager-Bereitstellungsmodell funktioniert.


## So aktivieren Sie die Erweiterung
Die Erweiterung kann über das [Azure-Portal](https://ms.portal.azure.com/#), die Azure PowerShell oder per Azure-Befehlszeilenschnittstellen-Skript aktiviert werden.

Um die System- und Leistungsdaten direkt im Azure-Portal anzuzeigen und zu konfigurieren, führen Sie diese [Schritte](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ „URL zum Windows-Blog“/) aus.


Dieser Artikel befasst sich mit dem Aktivieren und Konfigurieren der Erweiterung per Azure-Befehlszeilenschnittstelle. Dadurch können Sie die Daten direkt in der Speichertabelle anzeigen und lesen.


## Voraussetzungen
- Microsoft Azure Linux Agent in Version 2.0.6 oder höher. Beachten Sie, dass die meisten Images des Linux-Katalogs für virtuelle Azure-Computer die Version 2.0.6 oder höher besitzen. Durch Ausführen von **WAAgent -version** können Sie ermitteln, welche Version auf dem virtuellen Computer installiert ist. Wenn auf dem virtuellen Computer eine ältere Version als 2.0.6 ausgeführt wird, können Sie sie unter Verwendung dieser [Anweisungen](https://github.com/Azure/WALinuxAgent "Anweisungen") aktualisieren.
- [Azure-Befehlszeilenschnittstelle](./xplat-cli-install.md). Folgen Sie [dieser Anleitung](./xplat-cli-install.md), um die Azure-Befehlszeilenschnittstelle auf Ihrem Computer einzurichten. Sobald die Azure-Befehlszeilenschnittstelle installiert ist, können Sie über Ihre Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) mithilfe des Befehls **azure** auf die Befehle der Azure-Befehlszeilenschnittstelle zugreifen. Mit **azure vm extension set --help** erhalten Sie beispielsweise ausführliche Syntaxinformationen. Mit **azure login** können Sie sich bei Azure anmelden, und **azure vm list** führt alle Ihre virtuellen Computer in Azure auf.
- Ein Speicherkonto zum Speichern der Daten. Sie benötigen einen zuvor erstellten Speicherkontonamen und den Zugriffsschlüssel zum Hochladen von Daten an Ihren Speicher.


## Verwenden des Azure-CLI-Befehls zum Aktivieren der Linux-Diagnoseerweiterung

###  Szenario 1: Aktivieren der Erweiterung mit dem Standard-Dataset
Ab Version 2.0 werden standardmäßig folgende Daten gesammelt:

- Alle Rsyslog-Informationen (einschließlich System-, Sicherheits- und Anwendungsprotokolle).  
- Ein Kernsatz von Basissystemdaten, der vollständige Satz von Daten ist in diesem [Dokument](https://scx.codeplex.com/wikipage?title=xplatproviders) beschrieben. Wenn Sie zusätzliche Daten aktivieren möchten, fahren Sie mit den Schritten in Szenario 2 und 3 fort.

Schritt 1: Erstellen Sie eine Datei namens „PrivateConf.json“ mit folgendem Inhalt:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

Schritt 2. Führen Sie **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** aus.


###   Szenario 2: Anpassen der Leistungsüberwachungsmetrik  
Dieser Abschnitt beschreibt, wie Sie die Tabelle mit den Leistungs- und Diagnosedaten anpassen.

Schritt 1: Erstellen Sie eine Datei namens „PrivateConfig.json“ mit dem Inhalt aus dem nächsten Beispiel. Geben Sie die Daten an, die Sie sammeln möchten.

Alle unterstützten Anbieter und Variablen finden Sie in diesem [Dokument](https://scx.codeplex.com/wikipage?title=xplatproviders). Sie können mehrere Abfragen in mehreren Tabellen speichern, indem Sie weitere Abfragen an das Skript anhängen.

Rsyslog-Daten werden standardmäßig gesammelt.

	{
     	"storageAccountName":"storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


Schritt 2. Führen Sie **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** aus.


###   Szenario 3: Hochladen eigener Protokolldateien
In diesem Abschnitt erfahren Sie, wie Sie bestimmte Protokolldateien sammeln und an Ihr Speicherkonto hochladen. Zum Speichern des Protokolls müssen Sie den Pfad zur Protokolldatei und den Namen der Tabelle angeben. Sie können mehrere Protokolldateien nutzen, indem Sie mehrere Datei-/Tabelleneinträge an das Skript anfügen.

Schritt 1: Erstellen Sie eine Datei namens „PrivateConf.json“ mit folgendem Inhalt:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


Schritt 2. Führen Sie **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** aus.


###   Szenario 4: Deaktivieren der Linux-Überwachungserweiterung
Schritt 1: Erstellen Sie eine Datei namens „PrivateConf.json“ mit folgendem Inhalt:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account",
     	“perfCfg”:[],
     	“enableSyslog”:”False”
	}


Schritt 2. Führen Sie **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** aus.


## Überprüfen der Daten
Die Leistungs- und Diagnosedaten werden in einer Azure Storage-Tabelle gespeichert. In [diesem Artikel](storage-ruby-how-to-use-table-storage.md) erfahren Sie, wie Sie mithilfe von Azure-CLI-Skripts auf die Daten in der Speichertabelle zugreifen.

Darüber hinaus können Sie folgende Tools mit grafischer Benutzeroberfläche für den Datenzugriff verwenden:

1.	Verwenden Sie den Server-Explorer von Visual Studio. Navigieren Sie zum Speicherkonto. Nach etwa fünf Minuten werden die vier Standardtabellen angezeigt: „LinuxCpu“, „LinuxDisk“, „LinuxMemory“ und „Linuxsyslog“. Doppelklicken Sie auf den Tabellennamen, um die Daten anzuzeigen.
2.	Verwenden Sie den [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/ "Azure-Speicher-Explorer") für den Datenzugriff.

![image](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

Wenn Sie in Szenario 2 und 3 „fileCfg“ oder „perfCfg“ aktiviert haben, können Sie mithilfe der weiter oben genannten Tools auch andere Daten anzeigen.



## Bekannte Probleme
- In Version 2.0 kann auf die Rsyslog-Informationen und die benutzerdefinierte Protokolldatei nur über Skripts zugegriffen werden.

<!---HONumber=AcomDC_0323_2016-->