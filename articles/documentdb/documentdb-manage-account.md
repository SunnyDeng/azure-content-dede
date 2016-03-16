<properties
	pageTitle="Verwalten eines DocumentDB-Kontos über das Azure-Portal | Microsoft Azure"
	description="Erfahren Sie, wie Sie Ihr DocumentDB-Konto über das Azure-Portal verwalten. Sie erhalten eine Anleitung, wie das Azure-Portal verwendet werden kann, um Konten anzuzeigen, zu kopieren, zu löschen und um auf Konten zuzugreifen."
	keywords="Azure-Portal, DocumentDB, Azure, Microsoft Azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="anhoh"/>

# Verwalten eines DocumentDB-Kontos

Lernen Sie den Umgang mit Schlüsseln und Konsistenzebenen. Erfahren Sie, wie Sie ein Konto im Azure-Portal löschen.

## <a id="keys"></a>Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln
Wenn Sie ein DocumentDB-Konto erstellen, generiert der Dienst zwei Hauptzugriffsschlüssel, die für die Authentifizierung verwendet werden können, wenn der Zugriff auf das DocumentDB-Konto erfolgt. Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht DocumentDB Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Zugriffs auf das DocumentDB-Konto.

Greifen Sie im [Microsoft Azure-Portal](https://portal.azure.com/) auf das Blatt **Schlüssel** der Leiste **Essentials** des Blatts **DocumentDB-Konto** zu, um die für den Zugriff auf Ihr DocumentDB-Konto verwendeten Schlüssel anzuzeigen, zu kopieren und erneut zu generieren.

![Screenshot mit Azure-Portal, Blatt „Schlüssel“](./media/documentdb-manage-account/keys.png)

Eine weitere Option ist das Zugreifen auf den Eintrag **Schlüssel** auf dem Blatt **Alle Einstellungen**.

![Alle Einstellungen, Blatt „Schlüssel“](./media/documentdb-manage-account/allsettingskeys.png)

Beachten Sie, dass das Blatt **Schlüssel** auch die primären und sekundären Verbindungszeichenfolgen enthält, die für die Verbindung mit Ihrem Konto aus dem [Migrationsprogramm](documentdb-import-data.md) verwendet werden können.

Zudem enthält es Nur-Lese-Schlüssel, um Benutzern den schreibgeschützten Zugriff auf DocumentDB zu ermöglichen. Lesevorgänge und Abfragen sind schreibgeschützte Vorgänge, während das Erstellen, Löschen und Ersetzen Schreib- und Lesevorgänge sind.

### Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal

1.      Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie auf der Leiste **Essentials** des Blatts **DocumentDB-Konto** auf **Schlüssel**.

3.      Klicken Sie im Blatt **Schlüssel** rechts neben dem Schlüssel, den Sie kopieren möchten, auf die Schaltfläche **Kopieren**.

  ![Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal, Blatt „Schlüssel“](./media/documentdb-manage-account/copykeys.png)

### Erneutes Generieren von Zugriffsschlüsseln

Sie sollten regelmäßig die Zugriffsschlüssel für Ihr DocumentDB-Konto ändern, um dafür zu sorgen, dass Ihre Verbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, damit Sie Verbindungen zum DocumentDB-Konto mit einem Zugriffsschlüssel aufrechterhalten können, während Sie den anderen Zugriffsschlüssel neu generieren.

> [AZURE.WARNING] Das erneute Generieren der Zugriffsschlüssel wirkt sich auf alle Anwendungen aus, die vom aktuellen Schlüssel abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das DocumentDB-Konto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

Falls Sie über Webanwendungen oder Cloud-Dienste verfügen, die das DocumentDB-Konto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln – es sei denn, Sie führen einen Rollup für die Schlüssel aus. Die folgenden Schritte stellen den Prozess für das Rollup der Schlüssel dar.

1.      Aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den sekundären Zugriffsschlüssel des DocumentDB-Kontos verweist.

2.      Generieren Sie den primären Zugriffsschlüssel für Ihr DocumentDB-Konto neu. Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu.

3.      Klicken Sie auf der Leiste **Essentials** des Blatts **DocumentDB-Konto** auf **Schlüssel**.

4.      Klicken Sie im Blatt **Schlüssel** auf den Befehl **Primären Zugriffsschlüssel neu generieren**, und klicken Sie dann auf **OK**, um das Generieren eines neuen Schlüssels zu bestätigen.

5.      Sobald Sie sichergestellt haben, dass der neue Schlüssel verwendet werden kann (etwa 5 Minuten nach der erneuten Erzeugung), aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den neuen primären Zugriffsschlüssel verweist.

6.      Generieren Sie den sekundären Zugriffsschlüssel neu.

*Beachten Sie, dass die Bereitstellung eines neu generierten Schlüssels einige Minuten in Anspruch nehmen kann, bevor Sie damit auf Ihr DocumentDB-Konto zugreifen können.*

## <a id="consistency"></a>Verwalten von DocumentDB-Konsistenzeinstellungen
DocumentDB unterstützt vier detailliert definierte, benutzerkonfigurierbare Datenkonsistenzebenen. Mit diesen können Anwendungsentwickler gut zwischen Konsistenz, Verfügbarkeit und Latenz abwägen.

- **Strong** garantiert, dass Lesevorgänge stets den zuletzt geschriebenen Wert zurückgeben.

- **Bounded Staleness** garantiert, dass gelesene Werte nicht zu veraltet sind. Dadurch wird insbesondere garantiert, dass die Lesevorgänge nicht älter als *K*-Versionen der zuletzt geschriebenen Version sind.

- **Session** garantiert monotone Lesevorgänge (es werden niemals alte Daten, dann neue, dann wieder alte gelesen) und monotone Schreibvorgänge (Schreibvorgänge werden geordnet). Und es wird garantiert, dass die neuesten Schreibvorgänge bezogen auf den Status eines einzelnen Clients gelesen werden.

- **Eventual** garantiert, dass Lesevorgänge stets ein gültiges Subset von Schreibvorgängen ergeben und schließlich konvergieren.

*Beachten Sie, dass DocumentDB-Konten standardmäßig über die Konsistenzebene „Session“ verfügen. Weitere Informationen zu DocumentDB-Konsistenzeinstellungen finden Sie im Abschnitt [Konsistenzebenen](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### So legen Sie die Standardkonsistenz für ein DocumentDB-Konto fest

1.      Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu. 

2.      Falls auf dem Blatt „Konto“ das Blatt **Einstellungen** nicht bereits geöffnet ist, klicken Sie auf das Symbol **Einstellungen** auf der oberen Befehlsleiste.

3.      Klicken Sie auf dem Blatt **Alle Einstellungen** auf den Eintrag **Standardkonsistenz** unter **Feature**.

![Standardkonsistenz - Sitzung](./media/documentdb-manage-account/chooseandsaveconsistency.png)

4.      Wählen Sie im Blatt **Standardkonsistenz** die neue Konsistenzebene aus und klicken Sie auf **Speichern**.

5.      Sie können den Fortschritt des Vorgangs im Notification Hub des Azure-Portals überwachen.

*Beachten Sie, dass es einige Minuten in Anspruch nehmen kann, bis sich die Änderung der Standardkonsistenz auf Ihr DocumentDB-Konto auswirkt.*

## <a id="delete"></a>So löschen Sie ein DocumentDB-Konto im Azure-Portal
Um ein nicht mehr verwendetes DocumentDB-Konto aus dem Azure-Portal zu entfernen, verwenden Sie den Befehl **Löschen** im Blatt **DocumentDB-Konto**.

![So löschen Sie ein DocumentDB-Konto im Azure-Portal](./media/documentdb-manage-account/deleteaccountconfirmation.png)

1.      Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf das zu löschende DocumentDB-Konto zu. 

2.      Klicken Sie im Blatt **DocumentDB-Konto** auf den Befehl **Löschen**.

3.      Geben Sie im daraufhin angezeigten Bestätigungsblatt den Namen des DocumentDB-Kontos ein, um zu bestätigen, dass Sie das Konto löschen möchten.

4.      Klicken Sie im Bestätigungsfenster auf die Schaltfläche **Löschen**.

## <a id="next"></a>Nächste Schritte

Erfahren Sie mehr auf der Seite [Erste Schritte mit dem DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Weitere Informationen zu DocumentDB finden Sie in der Azure DocumentDB-Dokumentation auf [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

<!---HONumber=AcomDC_0302_2016-->
