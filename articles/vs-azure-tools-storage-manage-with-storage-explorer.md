<properties
	pageTitle="Verwalten von Azure-Speicherressourcen mit dem Speicher-Explorer (Vorschauversion) | Microsoft Azure"
	description="Beschreibt die Erstellung und Verwaltung von Azure-Speicherressourcen mithilfe der Vorschauversion des Microsoft Azure-Speicher-Explorers."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/17/2015"
	ms.author="tarcher" />

# Verwalten von Azure-Speicherressourcen mit dem Speicher-Explorer (Vorschauversion)

Der Microsoft Azure-Speicher-Explorer (Vorschauversion) ist ein eigenständiges Tool zur komfortablen Verwaltung von Azure-Speicherkonten. Das Tool ermöglicht die schnelle Verwaltung von Speicher außerhalb des Azure-Portals (beispielsweise im Rahmen der App-Entwicklung in Visual Studio). Diese Vorschauversion ermöglicht die einfache Verwendung von Blob Storage. Sie können Container erstellen und löschen, Blobs hochladen, herunterladen und löschen sowie alle Ihre Container und Blobs durchsuchen. Erweiterte Features ermöglichen Entwicklern und Operatoren die Verwendung von SAS-Schlüsseln und -Richtlinien. Windows-Entwickler können ihren Code auch mithilfe des Azure-Speicheremulators (unter Verwendung des Speicherkontos für die lokale Entwicklung) testen.

Für die Anzeige oder Verwaltung von Speicherressourcen im Speicher-Explorer benötigen Sie Zugriff auf ein Speicherkonto – entweder in Ihrem Abonnement oder in einem externen Speicherkonto. Falls Sie noch kein Speicherkonto besitzen, können Sie in wenigen Minuten eines erstellen. Wenn Sie ein MSDN-Abonnement besitzen, finden Sie weitere Informationen unter [Monatliche Azure-Gutschrift für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Andernfalls besuchen Sie [Einen Monat kostenlos testen!](https://azure.microsoft.com/pricing/free-trial/).

## Verwalten von Azure-Konten und -Abonnements

Damit Ihre Azure-Speicherressourcen im Speicher-Explorer angezeigt werden, müssen Sie sich bei einem Azure-Konto mit mindestens einem aktiven Abonnement anmelden. Wenn Sie über mehrere Azure-Konto verfügen, können Sie sie im Speicher-Explorer hinzufügen und dann die Abonnements auswählen, die Sie in die Ressourcenansicht im Speicher-Explorer einschließen möchten. Wenn Sie Azure noch nicht verwendet oder Visual Studio noch nicht die erforderlichen Konten hinzugefügt haben, werden Sie aufgefordert, sich bei einem Azure-Konto anzumelden.

### Hinzufügen eines Azure-Kontos zum Speicher-Explorer

1.	Wählen Sie auf der Symbolleiste des Speicher-Explorers das Symbol für **Einstellungen** (Zahnrad) aus.
1.	Wählen Sie den Link **Konto hinzufügen** aus. Melden Sie sich bei dem Azure-Speicherkonto an, dessen Ressourcen Sie durchsuchen möchten. Das Konto, das Sie gerade hinzugefügt haben, sollte in der Dropdownliste der Kontoauswahl ausgewählt sein. Alle Abonnements für dieses Konto werden unter dem Eintrag des Kontos angezeigt.

	![][0]

1.	Aktivieren Sie die Kontrollkästchen für die Kontoabonnements, die Sie durchsuchen möchten, und wählen dann die Schaltfläche **Übernehmen** aus.

	![][1]

	Die Azure-Speicherressourcen für die ausgewählten Abonnements werden im Speicher-Explorer angezeigt.

### Anfügen eines externen Speichers

1. Rufen Sie den Kontonamen und den Schlüssel für das anzufügende Speicherkonto ab.
	1.	Wählen Sie im Azure-Vorschauportal das anzufügende Speicherkonto aus.
	1.	Wählen Sie im Azure-Vorschauportal im Verwaltungsabschnitt des Bereichs **Einstellungen** die Schaltfläche **Schlüssel** aus.
	1.	Kopieren Sie die Werte für **Speicherkontoname** und **Primärer Zugriffsschlüssel**.

		![][2]

1.	Wählen Sie im Speicher-Explorer im Kontextmenü des Knotens **Speicherkonten** den Befehl **Externen Speicher anfügen** aus.

	![][3]

1. Geben Sie im Feld **Kontoname** den Namen des Speicherkontos und im Feld **Kontoschlüssel** den primären Zugriffsschlüssel ein. Wählen Sie zum Fortfahren die Schaltfläche **OK** aus.

	![][4]

	Der externe Speicher wird im Speicher-Explorer angezeigt.

	![][5]

1. Wählen Sie zum Entfernen des externen Speichers im Kontextmenü des externen Speichers den Befehl „Trennen“ aus.

	![][6]

## Anzeigen von und Navigieren zu Speicherressourcen

Erweitern Sie im Speicher-Explorer den Speichertyp, und wählen Sie die Ressource aus, um zu einer Azure-Speicherressource zu navigieren und ihre Informationen anzuzeigen. Die Informationen zur ausgewählten Ressource werden am unteren Rand des Speicher-Explorers auf den Registerkarten **Aktionen** und **Eigenschaften** angezeigt.

![][7]

-	Die Registerkarte **Aktionen** zeigt die Aktionen, die Sie im Speicher-Explorer für die ausgewählte Speicherressource ausführen können (beispielsweise Öffnen, Kopieren oder Löschen). Die Aktionen stehen auch im Kontextmenü der Ressource zur Verfügung.

-	Auf der Registerkarte **Eigenschaften** werden die Eigenschaften der Speicherressource (wie Typ, Gebietsschema, zugeordnete Ressourcengruppe und URL) angezeigt.

Die Aktion **Im Portal öffnen** steht für alle Speicherkonten zur Verfügung. Bei Verwendung dieser Aktion zeigt der Speicher-Explorer das ausgewählte Speicherkonto im Azure-Vorschauportal an.

Je nach ausgewählter Ressource werden ggf. weitere Aktionen und Eigenschaftswerte angezeigt. Für die Knoten „BLOB-Container“, „Warteschlangen“ und „Tabellen“ steht beispielsweise jeweils die Aktion **Erstellen** zur Verfügung. Einzelne Elemente (beispielsweise BLOB-Container) verfügen über Aktionen wie **Öffnen**, **Löschen** und **SAS abrufen**. Wenn Sie ein Speicherkonto-Blob auswählen, wird eine Aktion zum Öffnen des Blob-Editors angezeigt.

## Suchen nach Speicherkonten und BLOB-Containern

Wenn Sie Ihre Azure-Kontoabonnements nach einem Speicherkonto oder BLOB-Container mit einem bestimmten Namen durchsuchen möchten, geben Sie den Namen im Speicher-Explorer in das Feld **Suchen** ein.

![][8]

Wenn Sie Zeichen in das Feld **Suchen** eingeben, werden in der Ressourcenstruktur nur Speicherkonten oder BLOB-Container angezeigt, deren Name diesen Zeichen entspricht. Wählen Sie zum Löschen der Suche im Feld **Suchen** die Schaltfläche **x** aus.

## Bearbeiten von Speicherkonten

Wählen Sie zum Hinzufügen oder zum Ändern des Inhalts eines Speicherkontos den Befehl **Editor öffnen** für den entsprechenden Speichertyp aus. Aktionen können entweder im Kontextmenü des ausgewählten Elements oder auf der Registerkarte **Aktionen** (am unteren Rand des Speicher-Explorers) ausgewählt werden.

![][9]

Sie können BLOB-Container, Warteschlangen und Tabellen erstellen oder löschen. Mithilfe der Aktion **BLOB-Container-Editor öffnen** können Sie im Speicher-Explorer außerdem Blobs bearbeiten.

### Bearbeiten eines BLOB-Containers

1.	Wählen Sie die Aktion **BLOB-Container-Editor öffnen** aus. Der BLOB-Container-Editor wird im rechten Bereich angezeigt.

	![][10]

1.	Wählen Sie die Schaltfläche **Hochladen** und anschließend den Befehl **Dateien hochladen** aus.

	![][11]

	Falls sich die hochzuladenden Dateien in einem einzelnen Ordner befinden, können Sie stattdessen den Befehl „Ordner hochladen“ auswählen.

1. Wählen Sie im Dialogfeld **Dateien hochladen** rechts neben dem Feld „Dateien“ die Schaltfläche mit den Auslassungspunkten (**...**) aus, um die hochzuladenden Dateien auszuwählen. Wählen Sie dann die gewünschte Art des Blob-Uploads (Block, Seite oder Anfügung) aus. Bei Bedarf können die Dateien in einen Ordner im BLOB-Container hochgeladen werden. Geben Sie den Namen des Ordners in das Feld **Hochladen in Ordner (optional)** ein. Falls der Ordner noch nicht vorhanden ist, wird er erstellt.

	![][12]

	Im folgenden Screenshot wurden drei Bilddateien in einen neuen Ordner namens **My New Files** im BLOB-Container **Images** hochgeladen.

	![][13]

	Über die Schaltflächen auf der Symbolleiste des Blob-Editors können Sie unter anderem Dateien auswählen, herunterladen, öffnen, kopieren und löschen. Der Aktivitätsbereich am unteren Rand des Dialogfelds gibt Aufschluss darüber, ob der Vorgang erfolgreich war. Hier können Sie entweder nur die erfolgreichen Aktivitäten aus der Ansicht entfernen oder den Bereich vollständig leeren. Über das Plussymbol (**+**) neben den hochgeladenen Dateien können Sie eine detaillierte Liste hochgeladener Dateien anzeigen.

## Erstellen einer Shared Access Signature (SAS)

Bei bestimmten Vorgängen wird für den Zugriff auf eine Speicherressource unter Umständen eine SAS benötigt. Eine SAS kann im Speicher-Explorer erstellt werden.

1.	Wählen Sie das Element, für das Sie eine SAS erstellen möchten, und anschließend im Bereich **Aktionen** oder im Kontextmenü des Elements den Befehl **SAS abrufen** aus.

	![][14]

1.	Wählen Sie im Dialogfeld **Shared Access Signature** die Richtlinie, das Start- und Enddatum sowie die Zeitzone aus. Aktivieren Sie für die Ressource außerdem die Kontrollkästchen für die gewünschten Zugriffsebenen (schreibgeschützt, Lese-/ Schreibzugriff usw.). Wählen Sie abschließend die Schaltfläche **Erstellen** aus, um die SAS zu erstellen.

	![][15]

1.	Im Dialogfeld **Shared Access Signature** wird der Container zusammen mit der URL und den Abfragezeichenfolgen aufgeführt, mit denen Sie auf die Speicherressource zugreifen können. Wählen Sie die Schaltfläche **Kopieren** aus, um die Zeichenfolgen zu kopieren.

	![][16]

## Verwalten von SAS und Berechtigungen

Mithilfe der Befehle **Zugriffssteuerungsliste verwalten** und **Öffentliche Zugriffsebene festlegen** können Sie den Zugriff auf BLOB-Container steuern.

-	„Zugriffssteuerungsliste verwalten“ ermöglicht das Hinzufügen, Bearbeiten und Entfernen von Zugriffsrichtlinien für den ausgewählten BLOB-Container. (Die Zugriffsrichtlinien geben an, ob der Benutzer beispielsweise Lese- oder Schreibzugriff hat.)
-	Mit „Öffentliche Zugriffsebene festlegen“ können Sie den Zugriff öffentlicher Benutzer auf die Ressource steuern.  

-

1.	Wählen Sie den BLOB-Container und anschließend im Kontextmenü oder im Bereich **Aktionen** den Befehl **Zugriffssteuerungsliste verwalten** aus.

	![][17]

1.	Wählen Sie im Dialogfeld **Zugriffssteuerungsliste** die Schaltfläche **Hinzufügen** aus, um Richtlinien hinzuzufügen. Wählen Sie eine Zugriffsrichtlinie anschließend die gewünschten Berechtigungen aus. Wählen Sie abschließend die Schaltfläche **Speichern** aus.

	![][18]

1.	Wenn Sie eine Zugriffsebene für einen BLOB-Container festlegen möchten, wählen Sie im Speicher-Explorer den gewünschten Container und anschließend im Kontextmenü oder im Bereich **Aktionen** den Befehl **Öffentliche Zugriffsebene festlegen** aus.

	![][19]

1.	Wählen Sie im Dialogfeld **Öffentliche Zugriffsebene für Container festlegen** das Optionsfeld für die Zugriffsebene, die Sie öffentlichen Benutzern gewähren möchten, und anschließend die Schaltfläche **Übernehmen** aus.

	![][20]

## Nächste Schritte
Informieren Sie sich in den Artikeln unter [Einführung in Microsoft Azure Storage](storage-introduction.md) über die Features von Azure Storage Services.

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_1223_2015-->