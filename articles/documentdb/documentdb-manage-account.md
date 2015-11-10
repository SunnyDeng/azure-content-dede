<properties 
	pageTitle="Verwalten eines DocumentDB-Kontos über das Azure-Vorschauportal | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Ihr DocumentDB-Konto über das Azure-Vorschauportal verwalten. Sie erhalten eine Anleitung, wie das Azure-Vorschauportal verwendet werden kann, um Konten anzuzeigen, zu kopieren, zu löschen sowie auf Konten zuzugreifen." 
	keywords="Azure-Vorschauportal,DocumentDB,Azure,Microsoft Azure"
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
	ms.date="10/22/2015" 
	ms.author="anhoh"/>

# Verwalten eines DocumentDB-Kontos im Azure-Vorschauportal

Erfahren Sie, wie Sie mit Schlüsseln und Konsistenzeinstellungen arbeiten, und wie Sie ein Konto im Azure-Vorschauportal löschen.

## <a id="keys"></a>Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln
Wenn Sie ein DocumentDB-Konto erstellen, generiert der Dienst zwei Hauptzugriffsschlüssel, die für die Authentifizierung verwendet werden können, wenn der Zugriff auf das DocumentDB-Konto erfolgt. Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht DocumentDB Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Zugriffs auf das DocumentDB-Konto.

Greifen Sie im [Microsoft Azure-Vorschauportal](https://portal.azure.com/) auf den Bereich **Schlüssel** des Blatts **DocumentDB-Konto** zu, um die für den Zugriff auf Ihr DocumentDB-Konto verwendeten Schlüssel anzuzeigen, zu kopieren und neu zu generieren.

![Screenshot mit Azure-Vorschauportal, Blatt „Schlüssel“](media/documentdb-manage-account/keys.png)

### Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Vorschauportal

1.      Greifen Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie im Fokus **Zusammenfassung** auf **Schlüssel**.

3.      Klicken Sie im Blatt **Schlüssel** rechts neben dem Schlüssel, den Sie kopieren möchten, auf die Schaltfläche **Kopieren**.

  ![Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Vorschauportal, Blatt „Schlüssel“](./media/documentdb-manage-account/image004.jpg)

### Erneutes Generieren von Zugriffsschlüsseln

Sie sollten regelmäßig die Zugriffsschlüssel für Ihr DocumentDB-Konto ändern, um dafür zu sorgen, dass Ihre Verbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, damit Sie Verbindungen zum DocumentDB-Konto mit einem Zugriffsschlüssel aufrechterhalten können, während Sie den anderen Zugriffsschlüssel neu generieren.

> [AZURE.WARNING]Das erneute Generieren der Zugriffsschlüssel wirkt sich auf alle Anwendungen aus, die vom aktuellen Schlüssel abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das DocumentDB-Konto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

Falls Sie über Webanwendungen oder Clouddienste verfügen, die das DocumentDB-Konto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln – es sei denn, Sie führen einen Rollup für die Schlüssel aus. Die folgenden Schritte stellen den Prozess für das Rollup der Schlüssel dar.

1.      Aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den sekundären Zugriffsschlüssel des DocumentDB-Kontos verweist.

2.      Generieren Sie den primären Zugriffsschlüssel für Ihr DocumentDB-Konto neu. Greifen Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu.

3.      Klicken Sie in der Zusammenfassung auf **Schlüssel**.

4.      Klicken Sie im Blatt **Schlüssel** auf den Befehl **Primären Zugriffsschlüssel neu generieren**, und klicken Sie dann auf **OK**, um das Generieren eines neuen Schlüssels zu bestätigen.

5.      Sobald Sie sichergestellt haben, dass der neue Schlüssel verwendet werden kann (etwa 5 Minuten nach der erneuten Erzeugung), aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den neuen primären Zugriffsschlüssel verweist.

6.      Generieren Sie den sekundären Zugriffsschlüssel neu.

*Beachten Sie, dass die Bereitstellung eines neu generierten Schlüssels einige Minuten in Anspruch nehmen kann, bevor Sie damit auf Ihr DocumentDB-Konto zugreifen können.*

## <a id="consistency"></a>Verwalten von DocumentDB-Konsistenzeinstellungen
DocumentDB unterstützt vier detailliert definierte, benutzerkonfigurierbare Datenkonsistenzebenen. Mit diesen können Anwendungsentwickler gut zwischen Konsistenz, Verfügbarkeit und Latenz abwägen.

- **Strong** garantiert, dass Lesevorgänge stets den zuletzt geschriebenen Wert zurückgeben.

- **Bounded Staleness** garantiert, dass gelesene Werte nicht zu veraltet sind. Dadurch wird insbesondere garantiert, dass die Lesevorgänge nicht älter als *K*-Versionen der zuletzt geschriebenen Version sind.

- **Session** garantiert monotone Lesevorgänge (es werden niemals alte Daten, dann neue, dann wieder alte gelesen) und monotone Schreibvorgänge (Schreibvorgänge werden geordnet). Außerdem es wird garantiert, dass die neuesten Schreibvorgänge bezogen auf den Status eines einzelnen Clients gelesen werden.

- **Eventual** garantiert, dass Lesevorgänge stets ein gültiges Subset von Schreibvorgängen ergeben und schließlich konvergieren.

*Beachten Sie, dass DocumentDB-Konten standardmäßig über die Konsistenzebene „Session“ verfügen. Weitere Informationen zu DocumentDB-Konsistenzeinstellungen finden Sie im Abschnitt [Konsistenzebenen](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### So legen Sie die Standardkonsistenz für ein DocumentDB-Konto fest

1.      Greifen Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie im Fokus **Konfiguration** auf **Standardkonsistenz**.

3.      Wählen Sie im Blatt **Standardkonsistenz** die für das DocumentDB-Konto standardmäßig zu verwendende Konsistenzebene aus.

![Standardkonsistenz - Sitzung](./media/documentdb-manage-account/image005.png)

![Standardkonsistenz - Gebunden](./media/documentdb-manage-account/image006.png)

4.      Klicken Sie auf **Speichern**.

5.      Sie können den Fortschritt des Vorgangs im Notification Hub des Azure-Vorschauportals überwachen.

*Beachten Sie, dass es einige Minuten in Anspruch nehmen kann, bis sich die Änderung der Standardkonsistenz auf Ihr DocumentDB-Konto auswirkt.*

## <a id="delete"></a> So löschen Sie ein DocumentDB-Konto im Azure-Vorschauportal
Um ein nicht mehr verwendetes DocumentDB-Konto aus dem Azure-Vorschauportal zu entfernen, verwenden Sie im Blatt **DocumentDB-Konto** den Befehl **Löschen**.

![So löschen Sie ein DocumentDB-Konto im Azure-Vorschauportal](./media/documentdb-manage-account/image009.png)

1.      Greifen Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf das zu löschende DocumentDB-Konto zu. 

2.      Klicken Sie im Blatt **DocumentDB-Konto** auf den Befehl **Löschen**.

3.      Geben Sie im daraufhin angezeigten Bestätigungsblatt den Namen des DocumentDB-Kontos ein, um zu bestätigen, dass Sie das Konto löschen möchten.

4.      Klicken Sie auf dem Bestätigungsblatt auf die Schaltfläche **Löschen**.

## <a id="next"></a>Nächste Schritte

Erfahren Sie mehr auf der Seite [Erste Schritte mit dem DocumentDB-Konto](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Weitere Informationen zu DocumentDB finden Sie in der Azure DocumentDB-Dokumentation auf [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 
 

<!----HONumber=Nov15_HO1-->