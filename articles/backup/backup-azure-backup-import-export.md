<properties
	pageTitle="Azure Backup - Offlinesicherung oder anfängliche Seeding mithilfe von Azure-Import/Export-Dienst"
	description="Erfahren Sie, wie Azure-Sicherung zum Senden von Daten aus dem Netzwerk mithilfe von Azure-Import/Export-Dienst ermöglicht. Dieser Artikel beschreibt die offline seeding der anfänglichen backup-Daten über den Azure Import-/Export-Dienst"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/07/2015"
	ms.author="prvijay"/>

# Offline-Sicherung-Workflow in Azure-Sicherung

Azure-Sicherung ist tief in der Azure-Import/Export-Dienst integriert Dadurch können Sie die Anfangsdaten für die Sicherungen schnell übertragen. Wenn Sie TB anfänglichen backup-Daten, die über eine hohe Latenz & Netzwerk mit niedriger Bandbreite übertragen werden muss verfügen, können den Azure-Import/Export-Dienst Sie um die erste Sicherungskopie auf eine oder mehrere Festplatten an ein Azure-Rechenzentrum zu liefern. Dieser Artikel bietet einen Überblick über die erforderlichen Schritte für diesen Workflow abzuschließen.

## Übersicht

Mit der Azure-Sicherung und Azure-Import/Export ist es einfacher und unkomplizierter zum Hochladen von Daten in Azure offline über Laufwerke. Zu übertragen die erste vollständige Kopie über das Netzwerk, bezieht sich die Sicherungsdaten auf einem *stagingspeicherort*. Verzeichnis "staging" kann einen direkt angeschlossenen Speicher oder eine Netzwerkfreigabe sein könnte. Nach Abschluss die erste Kopie mit der *Azure-Import/Export-Tool*, diese Daten bezieht sich auf ein SATA-Laufwerk, das schließlich an die Azure-Rechenzentrum geliefert wird. Abhängig von der Größe der ersten Sicherung eine oder mehrere SATA-Laufwerke müssen zum Abschließen dieses Vorgangs. Die Azure-Import/Export-Tool-Konten für diese Szenarien. Nachdem die Sicherungen auf den Datenträger geschrieben werden, können sie zum nächsten Standort Rechenzentrums zum Hochladen in Azure geliefert werden. Azure-Sicherung kopiert dann die Sicherungsdaten in den sicherungstresor und inkrementelle Sicherungen geplant sind.

## Voraussetzungen

1. Es ist wichtig, sich mit der Azure-Import-Export-Workflow vertraut machen, die angezeigt wird, [hier](../storage-import-export-service.md).

2. Vor dem Initiieren des Workflows, stellen Sie sicher, dass ein Azure-sicherungstresor erstellt wurde, tresoranmeldedaten heruntergeladen wurden, Azure-Sicherungs-Agent auf dem Server für die Windows Server-Windows-Clients oder System Center Data Protection Manager (SCDPM) installiert wurde und der Computer mit dem Azure Backup-Tresor registriert ist.

3. Herunterladen die Azure-Datei Veröffentlichungseinstellungen aus [hier](https://manage.windowsazure.com/publishsettings) auf dem Computer, der die Daten gesichert werden sollen.

4. Vorbereiten einer *stagingspeicherort* eine Netzwerkfreigabe oder ein zusätzliches Laufwerk auf dem Computer sein könnte. Sicherstellen Sie, dass das Bereitstellungsverzeichnis genügend Speicherplatz für Ihre ursprüngliche Kopie zu halten. Für z. B. Wenn Sie versuchen, einen 500-GB-Dateiserver zu sichern, sicherzustellen, dass der Stagingbereich mindestens 500 GB (obwohl es sich bei eine geringeren Menge verwendet wird). Staging-Bereich ist 'vorübergehender Speicher' und vorübergehend während dieser Workflow verwendet wird.

5. Externe SATA-Laufwerk Writer und einem externen 3,5-Zoll-SATA-Laufwerk. Der Import-/Exportdienst unterstützt nur SATA II/III-Laufwerke im 3,5-Zoll-Format. Festplatten größer als 4 TB werden nicht unterstützt. Sie können SATA II/III-Laufwerke über einen SATA II/III-USB-Adapter extern an die meisten Computer anschließen. Dokumentation der Azure-Import/Export des aktuellen Satzes von Laufwerken, die vom Dienst unterstützt werden.

5. Aktivieren Sie BitLocker auf dem Computer mit dem Writer die SATA-Laufwerk verbunden ist.

6. Laden Sie das Azure-Import/Export-Tool von [hier](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) an den Computer mit der Writer die SATA-Laufwerk verbunden ist.


## Workflow
In diesem Abschnitt bereitgestellte Informationen ist zum Abschließen der **Offline Backup** Workflow, damit Ihre Daten an ein Azure-Rechenzentrum übermittelt und den Azure-Speicher hochgeladen werden. Wenn Sie Fragen zu den Import-Dienst oder einen Teil des Prozesses haben, finden Sie in der Import-Dienst verwiesen [über](../storage-import-export-service.md).

### Initiieren von Offline-Datensicherungen

1. Als Teil eine Sicherung zu planen wird den folgenden Bildschirm (in Windows Server, Windows-Client oder SCDPM) auftreten. <br/> ![ImportScreen][1]

  Im Bildschirm coresponding SCDPM sieht wie folgt aus. <br/> ![Importieren des DPM-Bildschirm][8]

Hinweis:

+ **Stagingspeicherort** -bezieht sich dies auf der temporäre Speicherort, zu dem die erste Sicherungskopie geschrieben wird. Dies könnte auf eine Netzwerkfreigabe oder auf dem lokalen Computer sein.

+ **Name des Importauftrags Azure** – als Teil dieser Workflow abschließen, müssen Sie erstellen ein *Importauftrag* im Azure-Portal (im späteren Teil des Dokuments behandelt). Geben Sie eine Eingabe, die Sie später in der Azure-Portal ebenfalls verwenden möchten.

+ **Azure-Veröffentlichungseinstellungen** -Dies ist eine XML-Datei enthält Informationen zu Ihrem Abonnementprofil. Außerdem enthält Sie sichere Anmeldeinformationen, die Ihrem Abonnement zugeordnet sind. Die Datei kann heruntergeladen werden [hier](https://manage.windowsazure.com/publishsettings). Geben Sie den lokalen Pfad zu der Datei mit den Veröffentlichungseinstellungen.

+ **Azure-Abonnement-ID** -Geben Sie die Azure-Abonnement-Id, die den Azure-Importauftrag initiieren möchten. Wenn Sie mehrere Azure-Abonnements verfügen, verwenden Sie die ID des Importauftrags zugeordnet.

+ **Azure Storage-Konto** -Geben Sie den Namen des Azure Storage-Kontos, das diesen Importauftrag zugeordnet werden soll.

+ **Azure-Speichercontainer** -Geben Sie den Namen des Ziel-Blob-Speicher, in dem dieser Auftrag Daten importiert werden.


Der Workflow abgeschlossen, und wählen Sie **Jetzt sichern** in der Mmc Azure-Sicherung der offline-Sicherungskopien zu initiieren. Die erste Sicherung wird in den Staging-Bereich als Teil dieses Schritts geschrieben.<br/>

  ![Jetzt sichern][2]

Der entsprechende Workflow in SCDPM wird aktiviert, indem Sie auf die **Schutzgruppe** und wählen Sie die **Wiederherstellungspunkt erstellen** Option. Anschließend werden die Auswahl der **Onlineschutz** Option.<br/> ![Jetzt die DPM-Sicherung][9]

Sobald der Vorgang abgeschlossen ist, ein *. AIBBlob* und *. BaseBlob* Datei wird im Verzeichnis "staging" erstellt. <br/> ![Ausgabe][3]

### SATA-Festplatte vorbereiten

1. Herunterladen der [Microsoft Azure-Import/Export-Tool](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) an die *Kopierer*. Sicherstellen Sie, dass das Bereitstellungsverzeichnis (im vorherigen Schritt) zugegriffen werden kann, auf dem Computer den nächsten Satz von Befehlen ausführen möchten. Falls erforderlich, kann dieses Gerät Quellcomputer identisch sein.

2. Entzippen Sie die *WAImportExport.zip* Datei. Führen Sie die *WAImportExport* Tools, die das SATA-Laufwerk zu formatieren, die Sicherungsdaten auf das SATA-Laufwerk schreiben und verschlüsseln. Stellen Sie sicher, dass BitLocker auf dem Computer aktiviert ist, bevor Sie den folgenden Befehl ausführen. <br/>

*.\WAImportExport.exe PrepImport /j: < * JournalFile*> .jrn/ID: < * SessionId * > /sk: < * StorageAccountKey * > /BlobType: ** PageBlob ** / t: < * TargetDriveLetter * >/formatieren / verschlüsseln /srcdir: < * staging Speicherort * > /dstdir: < * DestinationBlobVirtualDirectory * > / *


| Parameter | Beschreibung|
|-------------|-------------|
| /j: < * JournalFile * >| Der Pfad zur Journaldatei. Jedes Laufwerk muss genau eine Journaldatei verfügen. Beachten Sie, dass die Journaldatei nicht auf dem Ziellaufwerk befinden muss. Die Dateierweiterung für die Erfassung ist .jrn und wird als Teil der Ausführung dieses Befehls erstellt.|
|/ ID: < * SessionId * > | Die Sitzungs-ID identifiziert einen *Sitzung kopieren*. Es wird verwendet, um eine exakte Wiederherstellung einer unterbrochenen Kopiersitzung sicherzustellen. Dateien, die in einer Kopiersitzung kopiert werden, werden in ein Verzeichnis auf dem Ziellaufwerk die Sitzungs-ID gespeichert.|
| /SK: < * StorageAccountKey * > | Der Kontoschlüssel für das Speicherkonto, in dem die Daten importiert werden. |
| / BlobType | Geben Sie **PageBlob**, diesen Workflow wird nur erfolgreich, wenn die PageBlob-Option angegeben ist. Dies ist die Standardoption und sollte in diesem Befehl angegeben werden. |
|/ t: < * TargetDriveLetter * > | Der Laufwerkbuchstabe der Zielfestplatte für die aktuelle Kopiersitzung ohne nachgestellten Doppelpunkt.|
|/ Format | Geben Sie diesen Parameter, wenn das Laufwerk formatiert werden muss; Andernfalls lassen Sie sie. Bevor das Tool das Laufwerk formatiert wird, wird eine Bestätigung von der Konsole aufgefordert. Um die Bestätigung zu unterdrücken, geben Sie den/silentmode-Parameter.|
|/ Verschlüsseln | Dieser Parameter angegeben, wenn das Laufwerk noch nicht mit BitLocker verschlüsselt und vom Tool verschlüsselt werden muss. Wenn das Laufwerk bereits mit BitLocker verschlüsselt wurde, lassen Sie diesen Parameter, und geben Sie den /bk Parameter, der den vorhandenen BitLocker-Schlüssel. Wenn Sie den Format-Parameter angeben, müssen Sie auch angeben der / Parameter zu verschlüsseln. |
|/srcdir: < * Quellverzeichnis * > | Das Quellverzeichnis, das Dateien enthält, die auf das Ziellaufwerk kopiert werden. Der Verzeichnispfad muss ein absoluter Pfad (keinen relativen Pfad).|
|/dstdir: < * DestinationBlobVirtualDirectory * > | Der Pfad zum virtuellen Zielverzeichnis in Ihrem Microsoft Azure Storage-Konto. Achten Sie darauf, dass Sie gültige Containernamen zu verwenden, wenn Sie virtuelle Zielverzeichnisse oder Blobs angeben. Bedenken Sie, dass Containernamen kleingeschrieben werden müssen.|

  >[AZURE.NOTE]Eine Journaldatei wird im Ordner "WAImportExport" erstellt, die die gesamte Informationen des Workflows aufzeichnet. Sie benötigen diese Datei beim Erstellen eines Importauftrags im Azure-Portal.

  ![PowerShell-Ausgabe][4]

### Importauftrag in Azure-Portal erstellen
1. Navigieren Sie zu Ihrem Speicherkonto in der [Verwaltungsportal](https://manage.windowsazure.com/), und klicken Sie auf **Import/Export-** und klicken Sie dann auf **Importauftrag erstellen** im Aufgabenbereich. <br/> ![Portal][5]

2. Geben Sie im ersten Schritt des Assistenten an, dass Sie Ihr Laufwerk vorbereitet haben und dass Sie die Laufwerkprotokolldatei zur Hand haben. Geben Sie in Schritt2 des Assistenten Kontaktinformationen des Ansprechpartners für diesen Importauftrag.

3. Hochladen Sie in Schritt 3 Protokolldateien, die Sie während der im vorherigen Abschnitt zu erhalten.

4. Geben Sie im vierten Schritt einen beschreibenden Namen für den Importauftrag ein. Beachten Sie, dass der eingegebene Name nur Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten darf, mit einem Buchstaben beginnen muss und keine Leerzeichen enthalten darf. Mithilfe des ausgewählten Namens können Sie Ihre Aufträge während und nach der Bearbeitung nachverfolgen.

5. Wählen Sie anschließend Ihr Rechenzentrum aus der Liste aus. Die Datacenter-Region wird angegeben, das Rechenzentrum und die Adresse, zu dem Sie Ihr Paket schicken müssen. <br/> ![DC][6]

6. Wählen Sie in Schritt 5 Ihren Rücktransporteur aus und geben Sie Ihr Kundenkonto beim Transportunternehmen an. Microsoft verwendet diese Kontodaten, um Ihre Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken.

7. Liefern Sie den Datenträger aus, und geben Sie die Tracking-Nummer, um den Status der Lieferung verfolgen. Sobald der Datenträger im Rechenzentrum eintrifft, mit dem Speicherkonto kopiert werden und der Status wird aktualisiert. <br/>

![Status abgeschlossen][7]

### Der Workflow abgeschlossen
Nach die anfänglichen backup-Daten im Speicherkonto verfügbar ist, kopiert der Azure-Sicherungs-Agent den Inhalt der Daten von diesem Konto aus dem Sicherungsspeicher Multi-tenanted-Konto an. In den nächsten geplanten Download backup führt der Azure-Sicherungs-Agent die inkrementelle Sicherung über die erste Sicherungskopie.

## Nächste Schritte
+ Fragen auf den Azure-Import/Export-Workflow, finden Sie in diesen [Artikel](../storage-import-export-service.md).

+ Lesen Sie den Abschnitt der Offline-Sicherung der Azure-Sicherung [häufig gestellte Fragen zu](backup-azure-backup-faq.md) Fragen zum Workflow

<!--Image references-->
[1]: ./media/backup-azure-backup-import-export/importscreen.png
[2]: ./media/backup-azure-backup-import-export/backupnow.png
[3]: ./media/backup-azure-backup-import-export/opbackupnow.png
[4]: ./media/backup-azure-backup-import-export/psoutput.png
[5]: ./media/backup-azure-backup-import-export/azureportal.png
[6]: ./media/backup-azure-backup-import-export/dc.png
[7]: ./media/backup-azure-backup-import-export/complete.png
[8]: ./media/backup-azure-backup-import-export/dpmoffline.png
[9]: ./media/backup-azure-backup-import-export/dpmbackupnow.png

<!---HONumber=GIT-SubDir--> 