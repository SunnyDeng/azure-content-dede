<properties
   pageTitle="Azure Backup – Offlinesicherung oder anfängliches Seeding mithilfe des Azure Import/Export-Diensts"
   description="Erfahren Sie, wie Sie mit Azure Backup mithilfe des Azure Import/Export-Diensts Daten aus dem Netzwerk senden können. Dieser Artikel beschreibt das Offlineseeding der ersten Sicherungsdaten über den Azure Import/Export-Dienst."
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

# Workflow zur Offlinesicherung in Azure Backup

Azure Backup ist umfassend in den Azure Import/Export-Dienst integriert. Dadurch können Sie die Daten der ersten Sicherung schnell übertragen. Wenn Sie bei der ersten Sicherung mehrere TB an Daten über ein Netzwerk mit hoher Latenz und niedriger Bandbreite übertragen müssen, können Sie die erste Sicherungskopie mithilfe des Azure Import/Export-Diensts auf einer oder mehreren Festplatten an ein Azure-Datencenter liefern. Dieser Artikel bietet eine Übersicht über die für diesen Workflow erforderlichen Schritte.

## Übersicht

Mit Azure Backup und Azure Import/Export können die Daten einfach und unkompliziert offline über Laufwerke in Azure hochgeladen werden. Statt die erste vollständige Kopie über das Netzwerk zu übertragen, werden die Sicherungsdaten in einen *Stagingspeicherort* geschrieben. Beim Stagingspeicherort kann es sich um einen direkt angeschlossenen Speicher oder eine Netzwerkfreigabe handeln. Nach Abschluss der ersten Kopie mit dem *Azure Import/Export-Tool* werden diese Daten auf ein SATA-Laufwerk geschrieben, das schließlich an das Azure-Datencenter gesendet wird. Abhängig von der Größe der ersten Sicherung sind möglicherweise mehrere SATA-Laufwerke zum Abschließen dieses Vorgangs erforderlich. Das Azure Import/Export-Tool berücksichtigt solche Szenarien. Nachdem die Sicherungen auf Datenträger geschrieben wurden, können sie zum nächsten Datencenterstandort gesendet und in Azure hochgeladen werden. Azure Backup kopiert dann die Sicherungsdaten in den Sicherungstresor, und die inkrementellen Sicherungen werden geplant.

## Voraussetzungen

1. Machen Sie sich unbedingt mit dem Azure Import/Export-Workflow vertraut, der [hier](../storage-import-export-service.md) aufgeführt ist.

2. Stellen Sie vor dem Initiieren des Workflows sicher, dass ein Azure-Sicherungstresor erstellt wurde, dass Tresoranmeldedaten heruntergeladen wurden, dass der Azure Backup-Agent entweder auf dem Windows Server/Windows-Client oder dem SCDPM-Server (System Center Data Protection Manager) installiert wurde und dass der Computer beim Azure Backup-Tresor registriert ist.

3. Laden Sie [hier](https://manage.windowsazure.com/publishsettings) die Azure-Einstellungen zum Veröffentlichen von Dateien auf den Computer herunter, von dem die Daten gesichert werden sollen.

4. Bereiten Sie einen *Stagingspeicherort* vor. Dabei kann es sich um eine Netzwerkfreigabe oder ein zusätzliches Laufwerk auf dem Computer handeln. Stellen Sie sicher, dass der Stagingspeicherort genügend Speicherplatz für Ihre erste Kopie bietet. Wenn Sie beispielsweise versuchen, einen 500-GB-Dateiserver zu sichern, sorgen Sie dafür, dass der Stagingbereich mindestens 500 GB umfasst (auch wenn eine geringere Menge in Anspruch genommen wird). Der Stagingbereich ist ein Übergangsspeicher, der während dieses Workflows temporär verwendet wird.

5. Externer SATA-Laufwerkswriter und ein externes 3,5-Zoll-SATA-Laufwerk. Der Import-/Exportdienst unterstützt nur SATA II/III-Laufwerke im 3,5-Zoll-Format. Festplatten größer als 4 TB werden nicht unterstützt. Sie können SATA II/III-Laufwerke über einen SATA II/III-USB-Adapter extern an die meisten Computer anschließen. Schlagen Sie in der Dokumentation zu Azure Import/Export die aktuellen Laufwerke nach, die vom Dienst unterstützt werden.

5. Aktivieren Sie BitLocker auf dem Computer, an den der SATA-Laufwerkswriter angeschlossen ist.

6. Laden Sie das Azure Import/Export-Tool von [hier](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) auf den Computer herunter, mit dem der SATA-Laufwerkswriter verbunden ist.


## Workflow
Die in diesem Abschnitt bereitgestellten Informationen dienen zum Abschließen des Workflows zur **Offlinesicherung**, damit Ihre Daten an ein Azure-Datencenter versandt und in den Azure-Speicher hochgeladen werden können. Wenn Sie Fragen zum Importdienst oder zu einem anderen Aspekt des Prozesses haben, finden Sie weitere Informationen in der [oben](../storage-import-export-service.md) referenzierten Übersicht über den Importdienst.

### Initiieren der Offlinesicherung

1. Bei der zeitlichen Planung einer Sicherung wird Ihnen der folgender Bildschirm angezeigt (in Windows Server, Windows-Client oder SCDPM). <br/> ![ImportScreen][1]

  Der entsprechende SCDPM-Bildschirm sieht wie folgt aus. <br/> ![DPM-Importbildschirm][8]

Hinweis:

+ **Stagingspeicherort** – Bezieht sich auf den temporären Speicherort, in den die erste Sicherungskopie geschrieben wird. Hierbei kann es sich um eine Netzwerkfreigabe oder den lokalen Computer handeln.

+ **Name des Azure-Importauftrags** – Beim Abschließen dieses Workflows müssen Sie im Azure-Portal einen *Importauftrag* erstellen. (Dies wird im weiteren Verlauf des Dokuments behandelt.) Stellen Sie eine Eingabe bereit, die Sie später ebenfalls im Azure-Portal verwenden möchten.

+ **Azure-Veröffentlichungseinstellungen** – Dies ist eine XML-Datei, die Informationen zu Ihrem Abonnementprofil enthält. Außerdem enthält sie sichere Anmeldeinformationen, die Ihrem Abonnement zugeordnet sind. Die Datei kann [hier](https://manage.windowsazure.com/publishsettings) heruntergeladen werden. Geben Sie den lokalen Pfad zu der Datei mit den Veröffentlichungseinstellungen an.

+ **Azure-Abonnement-ID** – Geben Sie die Azure-Abonnement-ID an, mit der Sie den Azure-Importauftrag initiieren möchten. Wenn Sie mehrere Azure-Abonnements besitzen, verwenden Sie die ID, die dem Importauftrag zugeordnet ist.

+ **Azure-Speicherkonto** – Geben Sie den Namen des Azure-Speicherkontos ein, dem dieser Importauftrag zugeordnet werden soll.

+ **Azure-Speichercontainer** – Geben Sie den Namen des Zielspeicherblobs ein, in das die Daten dieses Auftrags importiert werden.


Schließen Sie den Workflow ab, und wählen Sie in der Azure Backup-MMC **Jetzt sichern** aus, um die Kopie der Offlinesicherung zu initiieren. Die erste Sicherung wird bei diesem Schritt in den Stagingbereich geschrieben.<br/>

  ![Jetzt sichern][2]

Der entsprechende Workflow in SCDPM wird aktiviert, indem Sie auf die **Schutzgruppe** klicken und die Option **Wiederherstellungspunkt erstellen** wählen. Anschließend wird die Option **Onlineschutz** gewählt.<br/> ![DPM – Jetzt sichern][9]

Sobald der Vorgang abgeschlossen ist, wird im Stagingspeicherort eine *.AIBBlob*- und eine *.BaseBlob*-Datei erstellt. <br/> ![Ausgabe][3]

### Vorbereiten der SATA-Festplatte

1. Laden Sie das [Microsoft Azure Import/Export-Tool](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) auf den *Kopiercomputer* herunter. Stellen Sie sicher, dass der Bereitstellungsspeicherort (im vorherigen Schritt) von dem Computer aus zugänglich ist, auf dem der nächste Satz von Befehlen ausgeführt werden soll. Falls erforderlich, kann der Kopiercomputer mit dem Quellcomputer identisch sein.

2. Entpacken Sie die Datei *WAImportExport.zip*. Führen Sie das *WAImportExport*-Tool aus. Es formatiert das SATA-Laufwerk, schreibt die Sicherungsdaten auf das SATA-Laufwerk und verschlüsselt sie. Bevor Sie den folgenden Befehl ausführen, stellen Sie sicher, dass BitLocker auf dem Computer aktiviert ist. <br/>

*.\WAImportExport.exe PrepImport /j:<*Journaldatei*>.jrn /id: <*Sitzungs-ID*> /sk:<*Speicherkontoschlüssel*> /BlobType:**PageBlob** /t:<*Ziellaufwerkbuchstabe*> /format /encrypt /srcdir:<*Stagingspeicherort*> /dstdir: <*VirtuellesVerzeichnisZielblob*>/*


| Parameter | Beschreibung|
|-------------|-------------|
| /j:<*Journaldatei*>| Der Pfad zur Journaldatei. Jedes Laufwerk muss über genau eine Journaldatei verfügen. Beachten Sie, dass die Journaldatei sich nicht auf dem Ziellaufwerk befinden darf. Die Dateierweiterung der Journaldatei lautet ".jrn" und wird bei der Ausführung dieses Befehls erstellt.|
|/id:<*SitzungsID*> | Die Sitzungs-ID identifiziert eine *Kopiersitzung*. Sie wird verwendet, um eine exakte Wiederherstellung einer unterbrochenen Kopiersitzung sicherzustellen. Die in einer Kopiersitzung kopierten Dateien werden in einem Verzeichnis gespeichert, das nach der Sitzungs-ID auf dem Ziellaufwerk benannt wird.|
| /sk:<*Speicherkontoschlüssel*> | Der Kontoschlüssel für das Speicherkonto, in das die Daten importiert werden. |
| /BlobType | Geben Sie **PageBlob** an. Dieser Workflow wird nur erfolgreich ausgeführt, wenn die Option "PageBlob" angegeben ist. Dies ist nicht die Standardoption und sollte in diesem Befehl angegeben werden. |
|/t:<*Ziellaufwerkbuchstabe*> | Der Laufwerkbuchstabe der Zielfestplatte für die aktuelle Kopiersitzung ohne nachgestellten Doppelpunkt.|
|/format | Geben Sie diesen Parameter an, wenn das Laufwerk formatiert werden muss. Andernfalls können Sie ihn auslassen. Bevor das Tool das Laufwerk formatiert, werden Sie von der Konsole aus zur Bestätigung aufgefordert. Um die Bestätigung zu unterdrücken, geben Sie den /silentmode-Parameter an.|
|/encrypt | Dieser Parameter wird angegeben, wenn das Laufwerk noch nicht mit BitLocker verschlüsselt wurde und vom Tool verschlüsselt werden muss. Wenn das Laufwerk bereits mit BitLocker verschlüsselt wurde, lassen Sie diesen Parameter aus, und geben Sie den /bk-Parameter an, in dem Sie den vorhandenen BitLocker-Schlüssel bereitstellen. Wenn Sie den /format-Parameter angeben, müssen Sie auch den /encrypt-Parameter festlegen. |
|/srcdir:<*Quellverzeichnis*> | Das Quellverzeichnis, das Dateien enthält, die auf das Ziellaufwerk kopiert werden. Der Verzeichnispfad muss ein absoluter Pfad sein (kein relativer Pfad).|
|/dstdir:<*VirtuellesVerzeichnisZielblob*> | Der Pfad zum virtuellen Zielverzeichnis in Ihrem Microsoft Azure-Speicherkonto. Achten Sie darauf, gültige Containernamen zu verwenden, wenn Sie virtuelle Zielverzeichnisse oder Blobs angeben. Containernamen müssen kleingeschrieben werden.|

  >[AZURE.NOTE]Eine Journaldatei wird im Ordner "WAImportExport" erstellt. Darin werden die gesamten Informationen des Workflows erfasst. Sie benötigen diese Datei beim Erstellen eines Importauftrags im Azure-Portal.

  ![PowerShell-Ausgabe][4]

### Erstellen eines Importauftrags im Azure-Portal
1. Navigieren Sie im [Verwaltungsportal](https://manage.windowsazure.com/) zu Ihrem Speicherkonto, und klicken Sie auf **Import/Export** und dann im Aufgabenbereich auf **Importauftrag erstellen**. <br/> ![Portal][5]

2. Geben Sie im ersten Schritt des Assistenten an, dass Sie Ihr Laufwerk vorbereitet haben und dass Sie die Laufwerkprotokolldatei zur Hand haben. Geben Sie im zweiten Schritt des Assistenten die Kontaktinformationen des Ansprechpartners für diesen Importauftrag an.

3. Laden Sie im dritten Schritt die Laufwerkjournaldateien hoch, die Sie im vorherigen Abschnitt erhalten haben.

4. Geben Sie im vierten Schritt einen beschreibenden Namen für den Importauftrag ein. Beachten Sie, dass der eingegebene Name nur Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten darf, mit einem Buchstaben beginnen muss und keine Leerzeichen enthalten darf. Mithilfe des ausgewählten Namens können Sie Ihre Aufträge während und nach der Bearbeitung nachverfolgen.

5. Wählen Sie anschließend Ihr Rechenzentrum aus der Liste aus. Unter "Datencenterregion" werden das Datencenter und die Adresse angegeben, an die Sie Ihr Paket schicken müssen. <br/> ![DC][6]

6. Wählen Sie in Schritt 5 Ihren Rücktransporteur aus und geben Sie Ihr Kundenkonto beim Transportunternehmen an. Microsoft verwendet diese Kontodaten, um Ihre Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken.

7. Versenden Sie den Datenträger, und geben Sie die Nachverfolgungsnummer ein, um den Status der Lieferung zu verfolgen. Sobald der Datenträger im Datencenter eintrifft, wird er in das Speicherkonto kopiert, und der Status wird aktualisiert. <br/>

![Status "Abgeschlossen"][7]

### Abschließen des Workflows
Sobald die ersten Sicherungsdaten im Speicherkonto verfügbar sind, kopiert der Azure Backup-Agent den Inhalt der Daten von diesem Konto aus in das mehrinstanzenfähige Sicherungsspeicherkonto. Zum nächsten geplanten Sicherungszeitpunkt führt der Azure Backup-Agent die inkrementelle Sicherung über die erste Sicherungskopie durch.

## Nächste Schritte
+ Wenn Sie Fragen zum Azure Import/Export-Workflow haben, finden Sie weitere Informationen in diesem [Artikel](../storage-import-export-service.md).

+ Bei Fragen zum Workflow finden Sie weitere Informationen im Abschnitt zur Offlinesicherung in den [häufig gestellten Fragen](backup-azure-backup-faq.md) zu Azure Backup.

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
 

<!---HONumber=62-->