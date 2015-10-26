<properties
	pageTitle="Wiederherstellen von Daten von einem anderen DPM-Server im Sicherungstresor | Microsoft Azure"
	description="Wiederherstellen von Daten, die Sie in einem Azure-Sicherungstresor auf einem beliebigen bei diesem Tresor registrierten DPM-Server gesichert haben."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/14/2015" ms.author="giridham"; "jimpark"/>

# Wiederherstellen von Daten von einem anderen DPM-Server im Sicherungstresor
Sie können nun Daten wiederherstellen, die Sie in einem Azure-Sicherungstresor auf einem beliebigen bei diesem Tresor registrierten DPM-Server gesichert haben. Das entsprechende Verfahren ist vollständig in die DPM-Verwaltungskonsole integriert und ähnelt dem anderer Wiederherstellungsworkflows.

Zum Wiederherstellen von Daten von einem anderen DPM-Server im Sicherungstresor benötigen Sie [System Center Data Protection Manager UR7](https://support.microsoft.com/de-DE/kb/3065246) und den [aktuellen Azure Backup-Agent](http://aka.ms/azurebackup_agent).

## Wiederherstellen von Daten von einem anderen DPM-Server
So stellen Sie Daten von einem anderen DPM-Server wieder her

1. Klicken Sie auf der Registerkarte **Wiederherstellung** der DPM-Verwaltungskonsole auf **Externen DPM hinzufügen** (links oben auf dem Bildschirm).

    ![Externen DPM hinzufügen](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. Geben Sie die **Tresoranmeldedaten** für den DPM-Server an, dessen Daten wiederhergestellt werden. Wählen Sie den **DPM-Server** aus der Liste der beim Sicherungstresor registrierten DPM-Server aus, und geben Sie die **Verschlüsselungspassphrase** für den DPM-Server an, dessen Daten wiederhergestellt werden.

    ![Anmeldeinformationen des externen DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE]Sie können nur Daten zwischen DPM-Servern wiederherstellen, die bei demselben Sicherungstresor registriert sind.

    Sobald der externe DPM-Server erfolgreich hinzugefügt wurde, können Sie die Daten vom externen DPM-Server und vom lokalen DPM-Server auf der Registerkarte **Wiederherstellung** durchsuchen.

3. Durchsuchen Sie die Liste der Produktionsserver, die durch den externen DPM-Server geschützt werden, und wählen Sie die entsprechende Datenquelle aus.

    ![Durchsuchen des externen DPM-Servers](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. Wählen Sie nacheinander **Monat und Jahr** aus der Dropdownliste **Wiederherstellungspunkte**, das erforderliche **Wiederherstellungsdatum** der Erstellung des Wiederherstellungspunkts sowie die **Wiederherstellungszeit** aus.

    Im unteren Bereich wird eine Liste von Dateien und Ordnern angezeigt, die Sie durchsuchen und an einem beliebigen Ort wiederherstellen können.

    ![Wiederherstellungspunkte des externen DPM-Servers](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. Klicken Sie mit der rechten Maustaste auf das entsprechende Element, und klicken Sie dann auf **Wiederherstellen**.

    ![Externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/recover.png)

6. Überprüfen Sie die **Wiederherstellungsauswahl**. Überprüfen Sie Datum und Uhrzeit der wiederhergestellten Sicherungskopie sowie die Quelle, aus der die Sicherungskopie erstellt wurde. Wenn die Auswahl fehlerhaft ist, klicken Sie auf **Abbrechen**, und navigieren Sie zur Registerkarte "Wiederherstellung", um dort den richtigen Wiederherstellungspunkt auszuwählen. Wenn die Auswahl richtig ist, klicken Sie auf **Weiter**.

    ![Zusammenfassung zur externen DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. Wählen Sie **An anderem Speicherort wiederherstellen** aus. Wählen Sie über **Durchsuchen** den richtigen Speicherort für die Wiederherstellung aus.

    ![Alternativer Speicherort für externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. Wählen Sie die gewünschte Option aus: **Kopie erstellen**, **Überspringen** oder **Überschreiben**.
    - **Kopie erstellen** erstellt eine Kopie der Datei, falls ein Namenskonflikt besteht.
    - **Überspringen** überspringt die Wiederherstellung der Datei, falls ein Namenskonflikt besteht.
    - **Überschreiben** überschreibt die vorhandene Kopie am angegebenen Speicherort, falls ein Namenskonflikt besteht.

    Wählen Sie die entsprechende Option für **Sicherheit wiederherstellen** aus. Sie können die Sicherheitseinstellungen des Zielcomputers anwenden, auf dem die Daten wiederhergestellt werden, oder die Sicherheitseinstellungen, die für das Produkt zum Zeitpunkt der Erstellung des Wiederherstellungspunkts galten.

    Geben Sie an, ob eine **Benachrichtigung** gesendet werden soll, sobald die Wiederherstellung erfolgreich abgeschlossen wurde.

    ![Benachrichtigungen für externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9. Auf dem Bildschirm **Zusammenfassung** werden die bisher ausgewählten Optionen aufgelistet. Nach dem Klicken auf **Wiederherstellen** werden die Daten am entsprechenden lokalen Speicherort wiederhergestellt.

    ![Zusammenfassung der Optionen für die externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE]Der Wiederherstellungsauftrag kann auf der Registerkarte **Überwachung** des DPM-Servers überwacht werden.

    ![Überwachen der Wiederherstellung](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. Klicken Sie auf der Registerkarte **Wiederherstellung** des DPM-Servers auf **Externen DPM löschen**, um die Ansicht des externen DPM-Servers zu entfernen.

    ![Löschen des externen DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Problembehandlung bei Fehlermeldungen
|Nr. |	Fehlermeldung |	Schritte zur Problembehandlung |
| :-------------: |:-------------| :-----|
|1\.|		Dieser Server ist nicht bei dem Tresor registriert, der durch die Tresoranmeldeinformationen angegeben ist.|	**Ursache**: Dieser Fehler wird angezeigt, wenn die ausgewählte Datei mit den Tresoranmeldeinformationen nicht zu dem Sicherungstresor gehört, dem der DPM-Server zugeordnet ist, auf dem die Wiederherstellung versucht wird. <br> **Lösung**: Laden Sie die Anmeldeinformationsdatei für den Tresor aus dem Sicherungstresor herunter, bei dem der DPM-Server registriert ist.|
|2\.|		Die wiederherzustellenden Daten sind nicht verfügbar, oder der ausgewählte Server ist kein DPM-Server.|	**Ursache**: Es sind keine anderen DPM-Server mit DPM 2012 R2 UR7 bei dem Sicherungstresor registriert, oder die DPM-Server mit DPM 2012 R2 UR7 haben ihre Metadaten noch nicht hochgeladen, oder der ausgewählte Server ist kein DPM-Server (also kein Windows-Server oder Windows-Client). <br> **Lösung**: Wenn noch andere DPM-Server bei dem Sicherungstresor registriert sind, müssen Sie sicherstellen, dass SCDPM 2012 R2 UR7 und der neueste Azure Backup-Agent installiert sind. <br>Sind noch andere DPM-Server mit DPM 2012 R2 UR7 bei dem Sicherungstresor registriert, warten Sie nach der Installation von UR7 einen Tag, um den Wiederherstellungsprozess zu starten. Während der Nacht werden die Metadaten für zuvor geschützte Sicherungen in die Cloud hochgeladen. Die Daten sind dann für die Wiederherstellung verfügbar.|
|3\.|		Es sind keine anderen DPM-Server bei diesem Tresor registriert.|	**Ursache**: Es sind keine anderen DPM-Server mit DPM 2012 R2 UR7 oder höher bei dem Tresor registriert, von dem die Wiederherstellung versucht wird.<br>**Lösung**: Wenn andere DPM-Server bei dem Sicherungstresor registriert sind, müssen Sie sicherstellen, dass SCDPM 2012 R2 UR7 und der neueste Azure Backup-Agent installiert sind.<br>Sind noch andere DPM-Server mit DPM 2012 R2 UR7 bei dem Sicherungstresor registriert, warten Sie nach der Installation von UR7 einen Tag, um den Wiederherstellungsprozess zu starten. Während der Nacht werden die Metadaten für zuvor geschützte Sicherungen in die Cloud hochgeladen. Die Daten sind dann für die Wiederherstellung verfügbar.|
|4\.|		Die angegebene Verschlüsselungspassphrase stimmt nicht mit der Passphrase überein, die dem folgenden Server zugeordnet ist: **<server name>**|	**Ursache**: Die beim Verschlüsseln der wiederherzustellenden Daten auf dem DPM-Server verwendete Verschlüsselungspassphrase stimmt nicht mit der angegebenen Verschlüsselungspassphrase überein. Der Agent kann die Daten nicht entschlüsseln. Daher schlägt die Wiederherstellung fehl.<br>**Lösung**: Geben Sie genau dieselbe Verschlüsselungspassphrase in, die dem DPM-Server zugeordnet ist, dessen Daten wiederhergestellt werden sollen.|

## Häufig gestellte Fragen:
1. **Warum kann ich nach der Installation von UR7 und dem neuesten Azure Backup-Agent keinen externen DPM-Server von einem anderen DPM-Server hinzufügen?**

    A) Für die vorhandene DPM-Server mit Datenquellen, die in der Cloud (mithilfe eines Updaterollups vor Update Rollup 7) geschützt sind, müssen Sie mindestens einen Tag nach der Installation von UR7 und des neuesten Azure Backup-Agents warten, bevor Sie *externe DPM-Server hinzufügen*. Diese Zeit ist erforderlich, um die Metadaten der DPM-Schutzgruppen in Azure hochzuladen. Dies erfolgt das erste Mal während der Nacht.

2. **Welches ist die erforderliche Mindestversion des Azure Backup-Agents?**

    A) Dieses Feature ist ab der Version 2.0.8719.0 des Azure Backup-Agents verfügbar. Sie können die Version des Azure Backup-Agents überprüfen, indem Sie zu **Systemsteuerung >** **Alle Systemsteuerungselemente >** **Programme und Funktionen > Microsoft Azure Recovery Services-Agent** navigieren. Wenn die Version kleiner als 2.0.8719.0 ist, laden Sie den [neuesten Azure Backup-Agent](https://go.microsoft.com/fwLink/?LinkID=288905) herunter und installieren ihn.

    ![Löschen des externen DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Nächste Schritte:
• [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->