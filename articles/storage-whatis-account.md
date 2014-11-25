<properties urlDisplayName="What is a Storage Account" pageTitle="Was ist ein Speicherkonto? | Microsoft Azure" metaKeywords="" description="Erhalten Sie Informationen &uuml;ber die unterschiedlichen Speicherkontotypen, die in Azure verf&uuml;gbar sind, und erhalten Sie Definitionen f&uuml;r wichtige Speicherbegriffe." metaCanonical="" services="storage" documentationCenter="" title="Was ist ein Speicherkonto?" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Was ist ein Speicherkonto?

Azure Storage umfasst drei Dienste: Blob-Speicher, Tabellenspeicher und Warteschlangenspeicher. Diese Dienste sind in jedem Speicherkonto enthalten. Ein Speicherkonto stellt den eindeutigen Namespace für die Verwendung von Blobs, Warteschlangen und Tabellen bereit.

Weitere Informationen zu Speicherkontobegrenzungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher][Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher].

Alle Informationen zum Speicherkonto, einschließlich Erstellungsdatum, sind im Verwaltungsportal auf der Seite **Dashboard** für **Speicher** verfügbar.

Speicherkosten basieren auf vier Faktoren: Speicherkapazität, Replikationsschema, Speichertransaktionen und Datenausgang. Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. Transaktionen beziehen sich auf alle Lese- und Schreibvorgänge im Azure-Speicher. Datenausgang bezieht sich auf Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen durchführen, um Daten und Dienste in den gleichen Rechenzentren zu gruppieren und so Datenausgangsgebühren zu reduzieren oder zu eliminieren.)

Die Seite mit den [Speicherpreisdetails][Speicherpreisdetails] bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. In den [Datenübertragungs-Preisdetails][Datenübertragungs-Preisdetails] finden Sie detaillierte Preisinformationen für den Datenausgang.

## Konzepte für Speicherkonten

### Speicherkonto-Replikationsoptionen

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Speicherkontoendpunkte

Die *Endpunkte* für ein Speicherkonto stellen die höchste Stufe des Namespace für den Zugriff auf Blobs, Tabellen oder Warteschlangen dar. Die Standardendpunkte für ein Speicherkonto haben die folgenden Formate:

-   Blob-Dienst: http://\*meinSpeicherkonto\*.blob.core.windows.net

-   Tabellendienst: http://\*meinSpeicherkonto\*.table.core.windows.net

-   Warteschlangendienst: http://\*meinSpeicherkonto\*.queue.core.windows.net

-   Dateidienst: http://\*meinSpeicherkonto\*.file.core.windows.net

Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Eine Blob-Adresse kann beispielsweise folgendes Format haben: http://\*meinSpeicherkonto*.blob.core.windows.net/*mycontainer*/*myblob\*.

### Speicherkontosicherheit

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

Sie sollten das Weitergeben von Speicherkonto-Zugriffsschlüsseln an andere vermeiden. Wenn Sie den Eindruck haben, dass Ihr Konto nicht mehr sicher ist, können Sie den Zugriffsschlüssel im Portal neu generieren. Wählen Sie das Speicherkonto aus, und führen Sie **Zugriffsschlüssel verwalten** aus.

Um den Zugriff auf Speicherressourcen zu gewähren, ohne den Zugriffsschlüssel weiterzugeben, verwenden Sie eine *Shared Access Signature*. Eine Shared Access Signature (SAS) bietet Zugriff auf eine Ressource in Ihrem Konto für ein von Ihnen definiertes Zeitintervall und mit den von Ihnen festgelegten Berechtigungen. Weitere Informationen finden Sie im [SAS-Lernprogramm][SAS-Lernprogramm].

### Speicherkontometriken und Protokollierung

-   **minimale vs. ausführliche Kennzahlen** Sie können minimale oder ausführliche Kennzahlen in den Überwachungseinstellungen für Ihr Speicherkonto konfigurieren. *Minimale Kennzahlen* erfassen Kennzahlen zu Daten, zum Beispiel zu Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Tabellen- und Warteschlangendienste aggregiert werden. *Ausführliche Kennzahlen* erfassen zusätzlich zu Dienstebenenzusammenfassungen Details auf Betriebsebene für die gleichen Kennzahlen. Ausführliche Metriken ermöglichen eine genauere Analyse von Problemen, die bei Anwendungsvorgängen auftreten. Eine vollständige Liste der verfügbaren Kennzahlen finden Sie im Thema zu den [Kennzahlen zur Speicheranalyse – Tabellenschema][Kennzahlen zur Speicheranalyse – Tabellenschema]. Weitere Informationen zur Speicherüberwachung finden Sie in den [Informationen zu Speicheranalysekennzahlen][Informationen zu Speicheranalysekennzahlen].

-   **Protokollierung** Protokollierung ist eine konfigurierbare Funktion von Speicherkonten, die die Protokollierung von Anforderungen zum Lesen, Schreiben und Löschen von Blobs, Tabellen und Warteschlangen ermöglicht. Sie konfigurieren die Protokollierung im Azure-Verwaltungsportal, können die Protokolle aber nicht in Verwaltungsportal anzeigen. Die Protokolle werden im Speicherkonto im $logs-Container gespeichert. Dort erfolgt auch der Zugriff. Weitere Informationen finden Sie unter [Übersicht über die Speicheranalyse][Übersicht über die Speicheranalyse].

### Affinitätsgruppen für zusammengestellte Azure-Speicher und andere Dienste

Eine *Affinitätsgruppe* ist eine geografische Gruppierung Ihrer Azure-Dienste und VMs mit Ihrem Azure-Konto. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Gebühren für den Datenausgang an, wenn ein Dienst, der zur gleichen Affinitätsgruppe gehört, auf Daten im Speicherkonto zugreift.

  [Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher]: http://msdn.microsoft.com/de-de/library/dn249410.aspx
  [Speicherpreisdetails]: http://www.windowsazure.com/de-de/pricing/details/#storage
  [Datenübertragungs-Preisdetails]: http://www.windowsazure.com/de-de/pricing/details/data-transfers/
  [SAS-Lernprogramm]: ../storage-dotnet-shared-access-signature-part-1/
  [Kennzahlen zur Speicheranalyse – Tabellenschema]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343264.aspx
  [Informationen zu Speicheranalysekennzahlen]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343258.aspx
  [Übersicht über die Speicheranalyse]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343268.aspx
