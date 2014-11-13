<properties umbracoNaviHide="0" pageTitle="Konzepte f&uuml;r Speicherkonten | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Erfahren Sie mehr &uuml;ber Konzepte f&uuml;r Speicherkonten." urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Konzepte f&uuml;r Speicherkonten" services="storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Konzepte für Speicherkonten

## Speicherkonto-Replikationsoptionen

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Speicherkontoendpunkte

Die *Endpunkte* für ein Speicherkonto stellen die höchste Stufe des Namespace für den Zugriff auf Blobs, Tabellen oder Warteschlangen dar. Die Standardendpunkte für ein Speicherkonto haben die folgenden Formate:

-   Blob-Dienst: [http://\*meinSpeicherkonto][http://\*meinSpeicherkonto]\*.blob.core.windows.net

-   Tabellendienst: [http://\*meinSpeicherkonto][http://\*meinSpeicherkonto]\*.table.core.windows.net

-   Warteschlangendienst: [http://\*meinSpeicherkonto][http://\*meinSpeicherkonto]\*.queue.core.windows.net

-   Dateidienst: [http://\*meinSpeicherkonto][http://\*meinSpeicherkonto]\*.file.core.windows.net

Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Eine Blob-Adresse kann beispielsweise folgendes Format haben: [http://\*meinSpeicherkonto][http://\*meinSpeicherkonto]*.blob.core.windows.net/*mycontainer*/*myblob\*.

## Speicherkontosicherheit

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

Sie sollten das Weitergeben von Speicherkonto-Zugriffsschlüsseln an andere vermeiden. Wenn Sie den Eindruck haben, dass Ihr Konto nicht mehr sicher ist, können Sie den Zugriffsschlüssel im Portal neu generieren. Wählen Sie das Speicherkonto aus, und führen Sie **Zugriffsschlüssel verwalten** aus.

Um den Zugriff auf Speicherressourcen zu gewähren, ohne den Zugriffsschlüssel weiterzugeben, verwenden Sie eine *Shared Access Signature*. Eine Shared Access Signature (SAS) bietet Zugriff auf eine Ressource in Ihrem Konto für ein von Ihnen definiertes Zeitintervall und mit den von Ihnen festgelegten Berechtigungen. Weitere Informationen finden Sie im [SAS-Lernprogramm][SAS-Lernprogramm].

## Speicherkontometriken und Protokollierung

-   **minimale vs. ausführliche Kennzahlen** Sie können minimale oder ausführliche Kennzahlen in den Überwachungseinstellungen für Ihr Speicherkonto konfigurieren. *Minimale Kennzahlen* erfassen Kennzahlen zu Daten, zum Beispiel zu Eingang/Ausgang, Verfügbarkeit, Latenz sowie Erfolgsprozentwerte, die für die Blob-, Tabellen- und Warteschlangendienste aggregiert werden. *Ausführliche Kennzahlen* erfassen zusätzlich zu Dienstebenenzusammenfassungen Details auf Betriebsebene für die gleichen Kennzahlen. Ausführliche Metriken ermöglichen eine genauere Analyse von Problemen, die bei Anwendungsvorgängen auftreten. Eine vollständige Liste der verfügbaren Kennzahlen finden Sie im Thema zu den [Kennzahlen zur Speicheranalyse – Tabellenschema][Kennzahlen zur Speicheranalyse – Tabellenschema]. Weitere Informationen zur Speicherüberwachung finden Sie in den [Informationen zu Speicheranalysekennzahlen][Informationen zu Speicheranalysekennzahlen].

-   **Protokollierung** Protokollierung ist eine konfigurierbare Funktion von Speicherkonten, die die Protokollierung von Anforderungen zum Lesen, Schreiben und Löschen von Blobs, Tabellen und Warteschlangen ermöglicht. Sie konfigurieren die Protokollierung im Azure-Verwaltungsportal, können die Protokolle aber nicht in Verwaltungsportal anzeigen. Die Protokolle werden im Speicherkonto im $logs-Container gespeichert. Dort erfolgt auch der Zugriff. Weitere Informationen finden Sie unter [Übersicht über die Speicheranalyse][Übersicht über die Speicheranalyse].

## Affinitätsgruppen für zusammengestellte Azure-Speicher und andere Dienste

Eine *Affinitätsgruppe* ist eine geografische Gruppierung Ihrer Azure-Dienste und VMs mit Ihrem Azure-Konto. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Gebühren für den Datenausgang an, wenn ein Dienst, der zur gleichen Affinitätsgruppe gehört, auf Daten im Speicherkonto zugreift.

  [http://\*meinSpeicherkonto]: http://*mystorageaccount
  [SAS-Lernprogramm]: ../storage-dotnet-shared-access-signature-part-1/
  [Kennzahlen zur Speicheranalyse – Tabellenschema]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343264.aspx
  [Informationen zu Speicheranalysekennzahlen]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343258.aspx
  [Übersicht über die Speicheranalyse]: http://msdn.microsoft.com/de-de/library/windowsazure/hh343268.aspx
