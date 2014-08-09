<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" authors="" />

Konzepte für Speicherkonten
===========================

-   **georedundanter Speicher (GRS)** Georedundanter Speicher bietet die höchste Speicherstabilität, indem Ihre Daten nahtlos an einem sekundären Standort in der gleichen Region repliziert werden. Auf diese Weise wird Failover im Falle eines größeren Ausfalls am primären Standort ermöglicht. Der sekundäre Standort ist hunderte von Kilometern vom primären Standort entfernt. GRS wird über eine Funktion namens *Georeplikation* implementiert, die standardmäßig für ein Speicherkonto aktiviert wird, aber deaktiviert werden kann, wenn Sie dieses Feature nicht benötigen (wenn zum Beispiel Unternehmensrichtlinien die Verwendung verhindern). Weitere Informationen finden Sie unter [Einführung in die Georeplikation für Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx).

-   **lokal redundanter Speicher (LRS)** Lokal redundanter Speicher bietet einen sehr stabilen Speicher mit hoher Verfügbarkeit an einem einzigen Standort. Für den lokal redundanten Speicher werden Kontodaten drei Mal innerhalb des gleichen Rechenzentrums repliziert. Der gesamte Speicher in Azure ist lokal redundant. Für zusätzliche Stabilität können Sie die Georeplikation aktivieren. Lokal redundanter Speicher wird zu günstigen Preisen angeboten. Preisinformationen finden Sie in der [Azure-Preisübersicht](http://www.windowsazure.com/de-de/pricing/details/).

-   **Affinitätsgruppe** Eine *Affinitätsgruppe* ist eine geografische Gruppierung von Clouddienstbereitstellungen und Speicherkonten innerhalb von Azure. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Kosten für den Ausgangsverkehr an.

-   **Speicherkontoendpunkte** Die *Endpunkte* für ein Speicherkonto stellen die höchste Ebene des Namespace für den Zugriff auf Blobs, Tabellen oder Warteschlangen dar. Die Standardendpunkte für ein Speicherkonto haben die folgenden Formate:

    -   Blob-Dienst: http://*meinspeicherkonto*.blob.core.windows.net

    -   Tabellendienst: http://*meinspeicherkonto*.table.core.windows.net

    -   Warteschlangendienst: http://*meinspeicherkonto*.queue.core.windows.net

-   **Speicherkonto-URLs** Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Ein Blob-Adresse kann beispielsweise das folgende Format haben: http://*meinspeicherkonto*.blob.core.windows.net/*meincontainer*/*meinblob*.

-   **Speicherzugriffsschlüssel** Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die beim Zugriff auf das Speicherkonto für die Authentifizierung verwendet werden. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

-   **minimale vs. ausführliche Metriken** Sie können minimale oder ausführliche Metriken in den Überwachungseinstellungen für Ihr Speicherkonto konfigurieren. *Minimale Metriken* erfassen Kennzahlen zu Daten, zum Beispiel zu Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Tabellen- und Warteschlangendienste aggregiert werden. *Ausführliche Metriken* erfassen zusätzlich zu Dienstebenenzusammenfassungen Details auf Betriebsebene für die gleichen Metriken. Ausführliche Metriken ermöglichen eine genauere Analyse von Problemen, die bei Anwendungsvorgängen auftreten. Eine vollständige Liste der verfügbaren Metriken finden Sie unter [Metriken zur Speicheranalyse – Tabellenschema](http://msdn.microsoft.com/de-de/library/windowsazure/hh343264.aspx). Weitere Informationen zur Speicherüberwachung finden Sie unter [Informationen zu Metriken der Speicheranalyse](http://msdn.microsoft.com/de-de/library/windowsazure/hh343258.aspx).

-   **Protokollierung** Protokollierung ist eine konfigurierbare Funktion von Speicherkonten, mit der Sie Anforderungen zum Lesen, Schreiben und Löschen von Blobs, Tabellen und Warteschlangen protokollieren können. Sie konfigurieren die Protokollierung im Azure-Verwaltungsportal, können die Protokolle aber nicht in Verwaltungsportal anzeigen. Die Protokolle werden im Speicherkonto im \$logs-Container gespeichert. Dort erfolgt auch der Zugriff. Weitere Informationen finden Sie unter [Übersicht über die Speicheranalyse](http://msdn.microsoft.com/de-de/library/windowsazure/hh343268.aspx).


