<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong"></tags>

# Verwalten eines DocumentDB-Kontos

## Inhaltsverzeichnis

-   [Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von DocumentDB-Zugriffsschlüsseln][Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von DocumentDB-Zugriffsschlüsseln]
-   [Gewusst wie: Verwalten von DocumentDB-Konsistenzeinstellungen][Gewusst wie: Verwalten von DocumentDB-Konsistenzeinstellungen]
-   [Gewusst wie: Verwalten von DocumentDB-Kapazitätseinstellungen][Gewusst wie: Verwalten von DocumentDB-Kapazitätseinstellungen]
-   [Gewusst wie: Löschen eines DocumentDB-Kontos][Gewusst wie: Löschen eines DocumentDB-Kontos]
-   [Nächste Schritte][Nächste Schritte]

## <span id="keys"></span></a>Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln

Wenn Sie ein DocumentDB-Konto erstellen, werden zwei Hauptzugriffsschlüssel
generiert, die für die Authentifizierung verwendet werden können,
wenn der Zugriff auf das DocumentDB-Konto erfolgt. Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht
DocumentDB Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des
Zugriffs auf das DocumentDB-Konto.

Greifen Sie im [Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal]
auf den Bereich **Schlüssel** des DocumentDB-Kontofensters zu,
um die für den Zugriff auf Ihr DocumentDB-Konto verwendeten Schlüssel
anzuzeigen, zu kopieren und neu zu generieren.

![][]

### Anzeigen und Kopieren eines Zugriffsschlüssels

1.      Greifen Sie im [Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal] auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie in der Zusammenfassung auf **Schlüssel**.

3.      Klicken Sie im Fenster "Schlüssel" auf die Schaltfläche **Kopieren** rechts
neben dem zu kopierenden Schlüssel.

4.      Drücken Sie STRG+C, um den Schlüssel zu kopieren.

  ![][1]

### Erneutes Generieren von Zugriffsschlüsseln

Sie sollten regelmäßig die Zugriffsschlüssel für Ihr DocumentDB-Konto
ändern, um dafür zu sorgen, dass Ihre Verbindungen möglichst sicher sind. Zwei Zugriffsschlüssel
werden zugewiesen, damit Sie Verbindungen zum
DocumentDB-Konto mit einem Zugriffsschlüssel aufrechterhalten können, während Sie den anderen Zugriffsschlüssel neu generieren.

**Warnung**

Das erneute Generieren der Zugriffsschlüssel wirkt sich auf alle
Anwendungen aus, die vom aktuellen Schlüssel abhängen. Alle Clients, die den Zugriffsschlüssel verwenden,
um auf das DocumentDB-Konto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

Falls Sie über Webanwendungen oder Clouddienste verfügen, die das DocumentDB-Konto
verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln –
es sei denn, Sie führen einen Rollup für die Schlüssel aus. Sie können wie folgt vorgehen:

1.      Aktualisieren Sie den Zugriffsschlüssel im Anwendungscode,
damit er auf den sekundären Zugriffsschlüssel des DocumentDB-Kontos verweist.

2.      Generieren Sie den primären Zugriffsschlüssel für Ihr DocumentDB-Konto.
Greifen Sie im [Azure-Verwaltungsvorschau-Portal][Azure-Verwaltungsvorschau-
Portal]
 auf Ihr DocumentDB-Konto zu.

3.      Klicken Sie in der Zusammenfassung auf **Schlüssel**.

4.      Klicken Sie im Fenster Schlüssel auf **Primären Zugriffsschlüssel neu generieren**
und dann auf **OK**, um das Generieren eines neuen Schlüssels zu bestätigen.

5.      Sobald Sie sichergestellt haben, dass der neue Schlüssel verwendet werden kann
(etwa 5 Minuten nach der erneuten Erzeugung), aktualisieren Sie den Zugriffsschlüssel im Anwendungscode,
damit er auf den neuen primären Zugriffsschlüssel verweist.

6.      Generieren Sie den sekundären Zugriffsschlüssel neu.

*Beachten Sie, dass die Bereitstellung eines neu generierten Schlüssels einige Minuten in Anspruch
nehmen kann, bevor Sie damit auf Ihr DocumentDB-Konto zugreifen können.*

## <span id="consistency"></span></a>Gewusst wie: Verwalten von DocumentDB-Konsistenzeinstellungen

DocumentDB unterstützt vier detailliert definierte, benutzerkonfigurierbare Datenkonsistenzebenen.
Mit diesen können Anwendungsentwickler gut zwischen Konsistenz,
Verfügbarkeit und Latenz abwägen.

·         **Strong** garantiert, dass Lesevorgänge stets
den zuletzt geschriebenen Wert zurückgeben.

·         **Bounded Staleness** garantiert,
dass gelesene Werte nicht zu veraltet sind. Dadurch wird insbesondere garantiert, dass die
Lesevorgänge nicht älter als K-Versionen der zuletzt geschriebenen Version sind.

·         **Session** garantiert monotone Lesevorgänge (es werden niemals
alte Daten, dann neue, dann wieder alte gelesen) und monotone Schreibvorgänge (Schreibvorgänge
werden geordnet). Und es wird garantiert, dass die neuesten Schreibvorgänge bezogen auf den Status
eines einzelnen Clients gelesen werden.

·         **Eventual** garantiert, dass Lesevorgänge stets ein gültiges
Subset von Schreibvorgängen ergeben und schließlich konvergieren.

*Beachten Sie, dass DocumentDB-Konten standardmäßig über die Konsistenzebene "Session"
verfügen.  Weitere Informationen zu DocumentDB-Konsistenzeinstellungen finden
Sie im Abschnitt [Konsistenzebene.
][Konsistenzebene.
]*

### Festlegen der standardmäßigen Konsistenz für ein DocumentDB-Konto

1.      Greifen Sie im [Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal] auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie in der Konfiguration auf **Schlüssel**.

3.      Wählen Sie im Fenster "Standardkonsistenz" die für das DocumentDB-Konto
standardmäßig zu verwendende Konsistenzebene.

4.      Klicken Sie auf **Speichern**.

5.      Sie können den Fortschritt des Vorgangs im Benachrichtigungs-Hub
des Azure-Verwaltungsvorschau-Portals überwachen.

![][2]

![][3]

*Beachten Sie, dass es einige Minuten in Anspruch nehmen kann, bis sich
die Änderung der Standardkonsistenz auf Ihr DocumentDB-Konto auswirkt.*

## <span id="capacity"></span></a>Gewusst wie: Verwalten von DocumentDB-Kapazitätseinstellungen

Microsoft Azure DocumentDB ermöglicht Ihnen eine flexible Skalierung
entsprechend den Anforderungen Ihrer Anwendung über ihrem gesamten Lebenszyklus. Die Skalierung
von DocumentDB erfolgt durch eine Erhöhen der Kapazität Ihres DocumentDB-
Datenbankkontos im Azure-Verwaltungsvorschau-Portal.

Wenn Sie ein Datenbankkonto erstellen, wird es mit Datenbank-
Speicherplatz und reserviertem Durchsatz bereitgestellt. Sie können jederzeit den bereitgestellten
Datenbank-Speicherplatz und Durchsatz für Ihr Konto ändern,
indem Sie Kapazitätseinheiten über das Azure-Verwaltungsvorschau-Portal hinzufügen oder entfernen. 

### Hinzufügen oder Entfernen von Kapazitätseinheiten

1.      Greifen Sie im [Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal] auf Ihr DocumentDB-Konto zu. 

2.      Klicken Sie im Bereich "Nutzung" auf **Skalieren**.

3.      Geben Sie im Skalierungsfenster die Anzahl der für Ihr
DocumentDB-Konto zu verwendenden Kapazitätseinheiten an.

4.      Klicken Sie auf **Speichern**. (Beachten Sie, dass es ggf. einige Minuten dauert,
bis der Skalierungsvorgang abgeschlossen ist. Sie können den Fortschritt im Benachrichtigungs-Hub des
Azure-Verwaltungsvorschau-Portals überwachen.)

*Beachten Sie, dass die DocumentDB-Vorschau maximal 5 Kapazitäts-
einheiten je DocumentDB-Konto unterstützt.*

![][4]

 

## <span id="delete"></span></a>Gewusst wie: Löschen eines DocumentDB-Kontos

Um ein nicht mehr verwendetes DocumentDB-Konto zu entfernen, verwenden Sie
**Löschen** im DocumentDB-Kontofenster.

**Warnung**

*In der Vorschauversion gibt es keine Möglichkeit zur Wiederherstellung des Inhalts
aus einem gelöschten DocumentDB-Konto.  Beim Löschen eines DocumentDB-Kontos
werden alle Ressourcen des Kontos einschließlich Datenbanken, Sammlungen,
Dokumenten und Anhängen gelöscht.*

![][5]

1.      Greifen Sie im [Azure-Verwaltungsvorschau-
Portal][Azure-Verwaltungsvorschau-
Portal] auf das zu löschende
DocumentDB-Konto zu. 

2.      Klicken Sie im Konto-Fensterbereich auf den Befehl **Löschen**.

3.      Geben Sie im daraufhin angezeigten Bestätigungsfenster den Namen des
DocumentDB-Kontos ein, um zu bestätigen, dass Sie das Konto löschen möchten.

4.      Klicken Sie im Bestätigungsfenster auf die Schaltfläche **Löschen**.

## <span id="next"></span></a>Nächste Schritte

-   Erfahren Sie mehr über die [ersten Schritte mit Ihrem DocumentDB-
    Konto][ersten Schritte mit Ihrem DocumentDB-
    Konto].
-   Weitere Informationen über DocumentDB finden Sie in der Azure DocumentDB-
    Dokumentation unter
    [azure.com][azure.com]

 

  [Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von DocumentDB-Zugriffsschlüsseln]: #keys
  [Gewusst wie: Verwalten von DocumentDB-Konsistenzeinstellungen]: #consistency
  [Gewusst wie: Verwalten von DocumentDB-Kapazitätseinstellungen]: #capacity
  [Gewusst wie: Löschen eines DocumentDB-Kontos]: #delete
  [Nächste Schritte]: #next
  [Azure-Verwaltungsvorschau-
  Portal]: https://portal.azure.com/
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [1]: ./media/documentdb-manage-account/image004.jpg
  [Konsistenzebene.
  ]: http://go.microsoft.com/fwlink/p/?LinkId=402365
  [2]: ./media/documentdb-manage-account/image005.png
  [3]: ./media/documentdb-manage-account/image006.png
  [4]: ./media/documentdb-manage-account/image007.png
  [5]: ./media/documentdb-manage-account/image009.png
  [ersten Schritte mit Ihrem DocumentDB-
  Konto]: http://go.microsoft.com/fwlink/p/?LinkId=402364
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409
