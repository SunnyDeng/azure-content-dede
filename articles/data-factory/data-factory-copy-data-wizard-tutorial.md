<properties 
	pageTitle="Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten" 
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit einer Kopieraktivität, indem Sie den von der Data Factory unterstützten Kopier-Assistenten verwenden." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe des Data Factory-Kopier-Assistenten
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-get-started.md)
- [Verwenden des Data Factory-Editors](data-factory-get-started-using-editor.md)
- [Mithilfe von PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Verwenden von Visual Studio](data-factory-get-started-using-vs.md)
- [Verwenden des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md)

In diesem Tutorial verwenden Sie den Data Factory-Kopier-Assistenten, um eine Pipeline mit einer Kopieraktivität in einer Data Factory zu erstellen. Zuerst erstellen Sie eine Data Factory mit dem Azure-Portal. Anschließend verwenden Sie den Kopier-Assistenten, um verknüpfte Data Factory-Dienste, Datasets und eine Pipeline mit einer Kopieraktivität zu erstellen, mit der Daten aus einer Azure Blob Storage-Einheit in eine Azure SQL-Datenbank kopiert werden.

> [AZURE.IMPORTANT] Lesen Sie sich den Artikel [Übersicht über das Tutorial](data-factory-get-started.md) durch, und führen Sie die vorbereitenden Schritte aus, bevor Sie mit diesem Tutorial beginnen.

## Erstellen einer Data Factory
In diesem Schritt erstellen Sie im Azure-Portal eine Azure Data Factory namens **ADFTutorialDataFactory**.

1.	Nach der Anmeldung beim [Azure-Portal](https://portal.azure.com) klicken Sie links oben auf **+ NEU**, wählen auf dem Blatt **Erstellen** die Option **Datenanalyse** aus und klicken auf dem Blatt **Datenanalyse** auf **Data Factory**. 

	![Neu -> Data Factory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. Gehen Sie auf dem Blatt **Neue Data Factory** so vor:
	1. Geben Sie **ADFTutorialDataFactory** als **Namen** ein. 
	
  		![Blatt "Neue Data Factory"](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und führen Sie die folgenden Schritte aus:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie **ADFTutorialResourceGroup** auf dem Blatt **Ressourcengruppe erstellen** als **Namen** der Ressourcengruppe ein, und klicken Sie auf **OK**. 

			![Ressourcengruppe erstellen](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

		Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../resource-group-overview.md).  
7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.
8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "ADFTutorialDataFactory" ist nicht verfügbar** ändern Sie den Namen der Data Factory (z. B.in "IhrNameADFTutorialDataFactory") und wiederholen den Vorgang. Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
	 
	![Data Factory-Name nicht verfügbar](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
	
	> [AZURE.NOTE] Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

9. Klicken Sie auf der linken Seite auf **BENACHRICHTIGUNGEN**, und suchen Sie nach Benachrichtigungen zum Erstellungsprozess. Klicken Sie auf **X**, um das Blatt **Benachrichtigungen** zu schließen, falls es geöffnet ist.
10. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

    ![Data Factory-Startseite](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Erstellen der Pipeline

1. Klicken Sie auf der Data Factory-Startseite auf die Kachel **Daten kopieren**, um den **Kopier-Assistenten** zu starten. 
2. Auf der Seite **Eigenschaften**:
	1. Geben Sie unter **Aufgabenname** den Namen **CopyFromBlobToAzureSql** ein.
	2. Geben Sie eine **Beschreibung** ein (optional).
	3. Achten Sie auf **Startdatum/-uhrzeit** und **Enddatum/-uhrzeit**. Ändern Sie **Enddatum/-uhrzeit** so, dass der Zeitpunkt am nächsten Tag nach **Startdatum/-uhrzeit** liegt. 
	3. Klicken Sie auf **Weiter**.  

	![Kopiertool – Seite „Eigenschaften“](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf die Kachel **Azure Blob Storage**. Sie können diese Seite verwenden, um den Quelldatenspeicher für die Kopieraufgabe anzugeben. Sie können einen verknüpften Datenspeicherdienst verwenden oder einen neuen Datenspeicher angeben. Klicken Sie zum Verwenden eines vorhandenen verknüpften Diensts auf **ÜBER VORHANDENE VERKNÜPFTE DIENSTE**, und wählen Sie den richtigen verknüpften Dienst aus. 

	![Kopiertool – Seite „Quelldatenspeicher“](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. Auf der Seite **Azure Blob Storage-Konto angeben**:
	1. Geben Sie unter **Name des verknüpften Diensts** als Name **AzureStorageLinkedService** ein.
	2. Überprüfen Sie, ob unter **Kontoauswahlmethode** die Option **Über Azure-Abonnements** angezeigt wird. 
	3. Wählen Sie in der Liste mit den Azure-Speicherkonten, die im ausgewählten Abonnement verfügbar sind, ein **Azure-Speicherkonto** aus. Sie können sich auch für das manuelle Eingeben von Speicherkontoeinstellungen entscheiden, indem Sie unter **Kontoauswahlmethode** die Option **Manuell eingeben** auswählen und dann auf **Weiter** klicken. 

	![Kopiertool – Azure Blob Storage-Konto angeben](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. Auf der Seite **Eingabedatei oder -ordner auswählen**:
	1. Navigieren Sie zum Ordner **adftutorial**.
	2. Wählen Sie **emp.txt** aus, und klicken Sie auf **Auswählen**.
	3. Klicken Sie auf **Weiter**. 

	![Kopiertool – Eingabedatei- oder -ordner auswählen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. Wählen Sie auf der Seite **Dateiformateinstellungen** die **Standard**werte aus, und klicken Sie auf **Weiter**.

	![Kopiertool – Dateiformateinstellungen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Klicken Sie auf der Seite „Zieldatenspeicher“ auf die Kachel **Azure SQL-Datenbank** und dann auf **Weiter**.
9. Auf der Seite **Azure SQL-Datenbank angeben**:
	1. Geben Sie im Feld **Name des verknüpften Diensts** als Name **AzureSqlLinkedService** ein. 
	2. Stellen Sie sicher, dass die **Server-/Datenbankauswahlmethode** auf **Über Azure-Abonnements** festgelegt ist.
	3. Wählen Sie **Servername** und **Datenbank** aus.
	4. Geben Sie **Benutzername** und **Kennwort** ein.
	5. Klicken Sie auf **Weiter**.  
9. Wählen Sie auf der Seite **Tabellenzuordnung** für das Feld **Ziel** in der Dropdownliste die Option **emp** aus, und klicken Sie auf den **Pfeil nach unten** (optional), um das Schema und eine Vorschau der Daten anzuzeigen.

	![Kopiertool – Tabellenzuordnung](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. Klicken Sie auf der Seite **Schemazuordnung** auf **Weiter**.
11. Überprüfen Sie die Informationen auf der Seite **Zusammenfassung**, und klicken Sie auf **Fertig stellen**. Es werden zwei verknüpfte Dienste, zwei Datasets (Eingabe und Ausgabe) und eine Pipeline in der Data Factory erstellt (von der aus Sie den Kopier-Assistenten gestartet haben). 
12. Klicken Sie auf der Seite **Bereitstellung erfolgreich** auf **Zum Überwachen der Kopierpipeline hier klicken**.

	![Kopiertool – Bereitstellung erfolgreich](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. Verwenden Sie die Anleitung unter [Überwachen und Verwalten der Pipeline mit der Überwachungs-App](data-factory-monitor-manage-app.md), um zu erfahren, wie Sie die gerade erstellte Pipeline überwachen können.

	![Überwachungs-App](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)
  

<!---HONumber=AcomDC_0323_2016-->