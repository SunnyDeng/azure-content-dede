<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Was ist ein Speicherkonto?
==========================

Azure Storage umfasst drei Dienste: Blob-Speicher, Tabellenspeicher und Warteschlangenspeicher. Diese Dienste sind in jedem Speicherkonto enthalten. Ein Speicherkonto stellt den eindeutigen Namespace für die Verwendung von Blobs, Warteschlangen und Tabellen bereit.

Ein Speicherkonto kann bis zu 200 TB Blob-, Warteschlangen- und Tabellendaten enthalten, wenn es am 8. Juni 2012 oder später erstellt wurde. Für Speicherkonten, die vor diesem Datum erstellt wurden, beträgt die Gesamtkapazität 100 TB. Sie können bis zu 20 eindeutig benannte Speicherkonten unter einem einzigen Abonnement erstellen. Weitere Informationen zu Speicherkonten finden Sie unter [Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher](http://msdn.microsoft.com/de-de/library/dn249410.aspx).

Alle Informationen zum Speicherkonto, einschließlich Erstellungsdatum, sind im Verwaltungsportal auf der Seite **Dashboard** für **Speicher** verfügbar.

Speicherkosten basieren auf vier Faktoren: Speicherkapazität, Replikationsschema, Speichertransaktionen und Datenausgang. Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. Transaktionen beziehen sich auf alle Lese- und Schreibvorgänge im Azure-Speicher. Datenausgang bezieht sich auf Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen durchführen, um Daten und Dienste in den gleichen Rechenzentren zu gruppieren und so Datenausgangsgebühren zu reduzieren oder zu eliminieren.)

Die Seite mit den [Speicherpreisdetails](http://www.windowsazure.com/de-de/pricing/details/#storage) bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. In den [Datenübertragungs-Preisdetails](http://www.windowsazure.com/de-de/pricing/details/data-transfers/) finden Sie detaillierte Preisinformationen für den Datenausgang.

Konzepte
--------

-   **georedundanter Speicher (GRS)** Georedundanter Speicher stellt die höchste Speicherstabilität bereit, indem Ihre Daten nahtlos an einem sekundären Standort in der gleichen Region repliziert werden. Auf diese Weise wird Failover im Falle eines größeren Ausfalls am primären Standort ermöglicht. Der sekundäre Standort ist hunderte von Kilometern vom primären Standort entfernt. GRS wird über eine Funktion namens *Georeplikation* implementiert, die standardmäßig für ein Speicherkonto aktiviert wird, aber deaktiviert werden kann, wenn Sie sie nicht verwenden möchten (wenn zum Beispiel Unternehmensrichtlinien die Verwendung verhindern). Weitere Informationen finden Sie unter [Einführung in die Georeplikation für den Azure-Speicher](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx).

-   **lokal redundanter Speicher (LRS)** Lokal redundanter Speicher stellt einen sehr stabilen Speicher mit hoher Verfügbarkeit an einem einzigen Standort bereit. Für den lokal redundanten Speicher werden Kontodaten drei Mal innerhalb des gleichen Rechenzentrums repliziert. Der gesamte Speicher in Azure ist lokal redundant. Für zusätzliche Stabilität können Sie die Georeplikation aktivieren. Lokal redundanter Speicher wird zu günstigen Preisen angeboten. Preisinformationen finden Sie in der [Preisübersicht](http://www.windowsazure.com/de-de/pricing/details/#storage).

-   **Affinitätsgruppe** Eine *Affinitätsgruppe* ist eine geografische Gruppierung von Clouddienstbereitstellungen und Speicherkonten innerhalb von Azure. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Gebühren für den Datenausgang an, wenn ein Dienst, der in der gleichen Affinitätsgruppe ausgeführt wird, auf Daten im Speicherkonto zugreift.

-   **Speicherkontoendpunkte** Die *Endpunkte* für ein Speicherkonto stellen die höchste Stufe des Namespace für den Zugriff auf Blobs, Tabellen oder Warteschlangen dar. Die Standardendpunkte für ein Speicherkonto haben die folgenden Formate:

    -   Blob-Dienst: http://*meinSpeicherkonto*.blob.core.windows.net

    -   Tabellendienst: http://*meinSpeicherkonto*.table.core.windows.net

    -   Warteschlangendienst: http://*meinSpeicherkonto*.queue.core.windows.net

-   **Speicherkonto-URLs** Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt gebildet. Ein Blob-Adresse kann beispielsweise folgenden Format haben: http://*meinSpeicherkonto*.blob.core.windows.net/*meinContainer*/*meinBlob*.

-   **Speicherzugriffsschlüssel** Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn ein Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

-   **minimale vs. ausführliche Kennzahlen** Sie können minimale oder ausführliche Kennzahlen in den Überwachungseinstellungen für Ihr Speicherkonto konfigurieren. *Minimale Kennzahlen* erfassen Kennzahlen zu Daten, zum Beispiel zu Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Tabellen- und Warteschlangendienste aggregiert werden. *Ausführliche Kennzahlen* erfassen zusätzlich zu Dienstebenenzusammenfassungen Details auf Betriebsebene für die gleichen Kennzahlen. Ausführliche Kennzahlen ermöglichen eine nähere Analyse von Problemen, die bei Anwendungsvorgängen auftreten. Eine vollständige Liste der verfügbaren Kennzahlen finden Sie im Thema zu den [Kennzahlen zur Speicheranalyse – Tabellenschema](http://msdn.microsoft.com/de-de/library/windowsazure/hh343264.aspx). Weitere Informationen zur Speicherüberwachung finden Sie in den [Informationen zu Speicheranalysekennzahlen](http://msdn.microsoft.com/de-de/library/windowsazure/hh343258.aspx).

-   **Protokollierung** Protokollierung ist eine konfigurierbare Funktion von Speicherkonten, die die Protokollierung von Anforderungen zum Lesen, Schreiben und Löschen von Blobs, Tabellen und Warteschlangen ermöglicht. Sie konfigurieren die Protokollierung im Azure-Verwaltungsportal, können die Protokolle aber nicht in Verwaltungsportal anzeigen. Die Protokolle werden im Speicherkonto im $logs-Container gespeichert. Dort erfolgt auch der Zugriff. Weitere Informationen finden Sie unter [Übersicht über die Speicheranalyse](http://msdn.microsoft.com/de-de/library/windowsazure/hh343268.aspx).


