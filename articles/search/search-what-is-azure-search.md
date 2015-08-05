<properties
	pageTitle="Was ist Azure Search"
	description="Azure Search: Technische Übersicht und Funktionsübersicht"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Was ist Azure Search?

Der Azure Search-Dienst ist ein vollständig verwalteter Clouddienst, mit dem Entwickler umfassende Suchanwendungen mit einem .NET SDK oder mit REST-APIs erstellen können. Er umfasst eine Volltextsuchfunktion, mit der Sie Ihren gesamten Inhalten durchsuchen können, sowie erweiterte Suchfunktionen, die den Funktionen in kommerziellen Internet-Suchmaschinen ähneln, z. B. Abfragevorschläge basierend auf einer partiellen Begriffseingabe, Hervorhebung von Treffern und Facettennavigation. Die Unterstützung natürlicher Sprachen ist integriert, wobei jeweils die für die angegebene Sprache geltenden linguistischen Regeln verwendet werden.

Sie können den Dienst je nach Bedarf an verbesserten Suchfunktionen oder Speicherkapazität skalieren. Beispielsweise können Einzelhändler die Kapazität erhöhen, um das mit Feiertagseinkäufen oder Werbeveranstaltungen verbundene zusätzliche Volumen verarbeiten zu können.

Azure Search ist ein API-basierter Dienst für Entwickler und Systemintegratoren, die sich mit dem Einsatz von Webdiensten und HTTP auskennen. Azure Search vereinfacht die Verwaltung eines Cloud-Suchdiensts und erleichtert die Erstellung von suchbasierten Webanwendungen und mobilen Anwendungen.

##So funktioniert's

Azure Search ist ein [PaaS-Dienst](https://wikipedia.org/wiki/Platform_as_a_service), der die Verwaltung von Server- und Infrastrukturtechnologien an Microsoft delegiert, sodass Ihnen ein einsatzbereiter Dienst zur Verfügung steht, dem Sie Suchdaten übergeben können und auf den Sie anschließend von Ihrer Anwendung aus zugreifen können. Je nachdem, wie Sie den Dienst konfigurieren, verwenden Sie entweder den kostenlosen Dienst, der auch von anderen Abonnenten Azure Search-Abonnenten verwendet wird, oder den Standardtarif, der dedizierte Ressourcen bietet, die nur von Ihrem Dienst verwendet werden. Die Standardsuche ist skalierbar und stellt Optionen bereit, um höhere Anforderungen von Speicher- oder Abfragelasten erfüllen zu können.

Azure Search speichert die Daten in einem Index, der mit Volltextabfragen durchsucht werden kann. Das Schema dieser Indizes kann entweder im Azure-Portal oder programmgesteuert mithilfe der Clientbibliothek oder REST-APIs erstellt werden. Nachdem das Schema definiert wurde, können Sie Ihre Daten in den Azure Search-Dienst hochladen, wo sie anschließend indiziert werden.

Sie können ein Push- oder Pullmodell zum Hochladen von Daten in den Index verwenden. Das Pullmodell wird über Indexer bereitgestellt, die für bedarfsabhängige oder geplante Aktualisierungen konfiguriert werden können (siehe [Indizierungsvorgänge (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), sodass Sie leicht Daten und Datenänderungen einer Azure-DocumentDB, Azure SQL-Datenbank oder eines in einer Azure-VM gehosteten SQL-Servers erfassen können. Das Pushmodell wird über das SDK oder die REST-APIs bereitgestellt, die zum Senden von aktualisierten Dokumenten an einen Index verwendet werden. Sie können Daten aus praktisch jedem Dataset übertragen, solange diese im JSON-Format vorliegen. Anleitungen zum Laden von Daten finden Sie unter [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://msdn.microsoft.com/library/azure/dn798930.aspx) oder [Verwenden des .NET SDK](search-howto-dotnet-sdk.md).

Manche Entwickler wählen der Einfachheit halber einen Indexer. Andere Entwickler bevorzugen das Pushmodell, obwohl es mit etwas zusätzlichem Aufwand verbunden ist. Es gibt zweierlei Gründe für die Wahl des Pushmodells. Erstens vermeiden Sie die zusätzliche Last, mit denen die Datenserver durch crawler-artige Mechanismen belastet werden. Zweitens vermeiden Sie die inhärente Latenz, die mit der geplanten Indizierung verbunden ist. An Tagen mit sehr hohem Umsatzvolumen soll die Suche den Status des Lagerbestands auf die Sekunde genau widerspiegeln. Ein Pushmodell kann diesen Grad an Genauigkeit bieten.

##Sie erstellen und speichern Folgendes

Der übliche Arbeitsablauf besteht aus dem Erstellen des Schemas und der Dokumente in einer lokalen Entwicklungsumgebung und der Verwendung des .NET SDK oder der REST-API, um die Daten hochzuladen, zu verarbeiten und schließlich abzufragen. Alle indizierten Daten werden in Azure Search gespeichert, um eine bessere Leistung und Konsistenz der Suchvorgänge sicherzustellen.

Sie können mit dem im Portal integrierten Editor das Indexschema und Bewertungsprofile erstellen. Dieser Editor eignet sich hervorragend zum Erstellen von Prototypen. Entwickler, die einen wiederholbaren, automatisierten Ansatz benötigen, sollten den Index besser im Code erstellen. Da neuere Funktionen zuerst der API hinzugefügt werden, ist je nach den Anforderungen Ihrer Anwendung eine programmgesteuerte Methode möglicherweise Ihre einzige Option.

Bei der Erstellung des Schemas definieren Sie die Felder und zugehörigen Attribute, die in der Suchanwendung unterstützt werden. Die Felder enthalten durchsuchbare Daten, z. B. Produktnamen, Beschreibungen, Kundenkommentare, Marken, Preise, Werbe-Benachrichtigungen und so weiter. Attribute liefern Informationen für die Arten von Vorgängen, die ausgeführt werden können. Beispiele für die am häufigsten verwendeten Attribute sind die Angaben, ob ein Feld die Volltextsuche (searchable = true), Filter (filterable = true) oder Facetten (facetable = true) unterstützt.

Felder enthalten auch nicht durchsuchbare Daten, die für Ihre Suchanwendung relevant sind, z. B. intern in Filtern und Bewertungsprofilen verwendete Werte oder URLs von Inhalten, die auf anderen Speicherplattformen (z. B. Video- oder Bilddateien im BLOB-Speicher) gespeichert werden. Ein oft zitiertes Beispiel für ein rein internes Feld ist eine Gewinnspanne oder ein anderer Wert, der das Ertragspotenzial angibt. Möglicherweise muss die Suchanwendung bestimmte Elemente fördern, die Ihrem Unternehmen einen höheren finanziellen Nutzen bringen. Das Schema kann Feldattribute enthalten, die diese Art von Suchverhalten ermöglichen.

Die Dokumente enthalten die detaillierten Daten, die durch vom Suchmodul in den Suchergebnissen zurückgegeben werden. Wenn Ihre Suchanwendung z. B. ein Onlinekatalog ist, müsste für jede SKU ein Dokument vorhanden sein, und die Seite mit Suchergebnissen würde mithilfe von Werten erstellt werden, die von Dokumenten zurückgegeben werden und mit Suchbegriffen übereinstimmen.

##Demo ansehen

Sehen Sie sich unsere Videos an, um mehr über Szenarien und Funktionen zu erfahren. Links zu Videoinhalten finden Sie unter [Azure Search: Lernprogramme, Videodemos und Beispiele](https://msdn.microsoft.com/library/azure/dn818681.aspx).

##Detaillierte Funktionsübersicht

Azure Search ist in mehreren Kategorien von Nutzen, darunter Bereitstellung und Skalierung, Programmierbarkeit, Zugriff und Steuerung und in den Funktionen, die Sie in Ihrer benutzerdefinierten Suchanwendung implementieren.

Mithilfe der folgenden Funktionsprüfliste können Sie ermitteln, ob Azure Search die Anforderungen Ihrer Suchanwendung erfüllt. Ankündigungen neuer Funktionen finden Sie in den neuesten Updates für Azure Search. Sie können auch die Seite mit Azure Search-Featureanforderungen überprüfen, um sich über den Status der noch nicht implementierten Funktionen zu informieren.

Haben Sie Fragen zu einer speziellen Funktion? Besuchen Sie das [Azure Search-Forum auf MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch). Sie können auch die Seite mit [Azure Search-Featureanforderungen](http://feedback.azure.com/forums/263029-azure-search) überprüfen, um sich über den Status der noch nicht implementierten Funktionen zu informieren.

###Kapazitäts- und Skalierungsfunktionen und -einschränkungen

Der Dienst kann entweder als Standard- oder als gemeinsam genutzte Dienstbereitstellung ausgeführt werden. Die Standardsuche unterstützt dedizierte Ressourcen, die je nach der jeweiligen Arbeitsauslastung skaliert werden können. Der gemeinsam genutzte Dienst ist kostenlos und zum Testen der Funktionalität der Dienste vorgesehen, da es keine Leistungsgarantien gibt.

**Skalierbar** in Schritten von Speicher- und Dokumentanzahl (Partitionen) oder Abfrageauslastung (Replikate). Jedes Replikat führt eine Indexkopie aus. Hohe Verfügbarkeit erfordert zwei Replikate für Abfragearbeitsauslastungen und drei Replikate für Lese-/Schreibarbeitsauslastungen(Abfrage und Indizierung). Weitere Informationen zur Kapazitätsplanung finden Sie unter "Limits und Einschränkungen (Azure Search)".

Azure Search nutzt für Indizes und Dokumente automatisch die Partitionen, die Sie für diesen Dienst reserviert haben. Dies bedeutet, dass Sie die Indizes und Dokumente auf einen Satz von Partitionen und Replikaten binden können.

Partitionen und Replikate sind dienstweite Ressourcen, wobei alle Indizes in allen Replikate ausgeführt werden. Wenn Sie einen Index isolieren müssen oder verschiedene Rechenzentren unterschiedliche Anforderungen an die geografische Verteilung der Dienste und Ressourcen haben, könnten Sie einen zweiten Dienst erstellen.

Es gibt Beschränkungen bezüglich des Speichers und der Anzahl der Indizes und Dokumente, die in den Dienst geladen werden. Das tatsächliche Limit wird durch das definiert, was zuerst eintritt: wenn der physische Speicher aufgebraucht ist oder wenn die Obergrenze der Indiz- und Dokumentanzahl erreicht wird. Weitere Details finden Sie im Abschnitt [Limits und Einschränkungen (Azure Search)](https://msdn.microsoft.com/library/azure/dn798934.aspx).

###Programmierbarkeit

Die REST-API besteht aus HTTP-Anforderungen und -Antworten mit Inhalt im JSON-Format. Es gibt eine API für den Zugriff auf den Dienst und eine API zum Verwalten des Diensts. Nähere Informationen finden Sie unter [REST-API für Azure Search-Dienst](https://msdn.microsoft.com/library/azure/dn798935.aspx) und [REST-API für Azure Search-Verwaltung](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Das .NET SDK umfasst Klassen, die die Verwendung des Azure Search-Diensts erleichtern, ohne direkt mit HTTP und JSON arbeiten zu müssen. Weitere Informationen finden Sie unter [Verwenden des Azure Search .NET SDK](search-howto-dotnet-sdk.md).

###Zugriff und Steuerung

Auf Search kann nur über HTTPS zugegriffen werden.

Die Authentifizierung von der Hostanwendung gegenüber Azure Search erfolgt über einen Admin-Api-Schlüssel in einem HTTP-Header. Es gibt weder eine Authentifizierung für einzelne Benutzer oder noch ein Autorisierungsmodell. Sie können jedoch mit $filter den Zugriff anhand der Benutzeridentität beschränken. Sie können auch mehrere Abfrage-Api-Schlüssel verwenden, die Sie verschiedenen Clientanwendungen zuweisen können. Weitere Informationen zur Schlüsselverwaltung finden Sie unter [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md). Ausführliche Informationen zu $filter finden Sie unter [Suchen von Dokumenten (Azure Search-REST-API)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

###Indizes und Dokumente

Sie können mehrere Indizes verwenden (Informationen zu den für die einzelnen Tarife geltenden Limits finden Sie unter [Limits und Einschränkungen (Azure Search)](https://msdn.microsoft.com/library/azure/dn798934.aspx)). Beachten Sie, dass das Verknüpfen von Indizes derzeit nicht unterstützt wird. In einer Suchanforderung kann ein Index angegeben werden.

**Dokumente** enthalten Felder und die zugehörigen Attribute. Felder enthalten durchsuchbaren Text, Werte, die vorwiegend (oder sogar ausschließlich) in Filtern und Bewertungsprofilen verwendet werden, und sehr wahrscheinlich URLs oder Verweise auf Inhalte, z. B. Bilder, in anderen Datenspeichern. Viele Suchanwendungen verwenden mehrere Arten von Speicher. Bilder oder Videos können kostengünstiger in anderen Speichermedien, wie z. B. Azure Blob-Speicher, gespeichert werden.

**Indexer** können verwendet werden, um die Indexaktualisierungen von geänderten Daten in Azure SQL-Datenbank, SQL Server auf Azure VMs oder Azure DocumentDB zu planen. Nähere Informationen finden Sie unter [Indexer-Vorgänge (REST-API für den Azure Search-Dienst)]((https://msdn.microsoft.com/library/azure/dn946891.aspx)

**Abfragen** können unter Verwendung der OData-Syntax für boolesche Filter und einer einfachen Abfragesyntax für die Volltextsuche formuliert werden. Nähere Informationen finden Sie unter [OData-Ausdruckssyntax für Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx) und [Einfache Abfragesyntax in Azure Search](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Sie können bestimmte Dokumente basierend auf deren ID suchen, um bestimmte Elemente schnell abzurufen . Dies ist nützlich, wenn ein Benutzer auf ein bestimmtes Suchergebnis klickt und Sie spezifische Details zu diesem Dokument nachschlagen möchten. Nähere Informationen finden Sie unter [Nachschlagen von Dokumenten (Azure Search-Dienst-REST-API)](https://msdn.microsoft.com/library/azure/dn798929.aspx).

##Funktionen von Suchanwendung

**Volltextsuche** (in der Standardeinstellung für Textfelder aktiviert) ist für jedes durchsuchbare Feld aktiviert. Die Volltextsuche basiert auf einem Präfixabgleich, und das bedeutet, dass Treffer auf dem ersten Teil eines Suchbegriffs und nicht der Mitte oder dem Ende des Worts basieren. Schrittweise Anleitungen zum Definieren eines Index finden Sie unter [Erstellen eines Index (Azure Search-Dienst-REST-API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) oder [Erstellen eines Index im Portal](search-create-index-portal.md).

**Bewertungsprofile** dienen zur Erstellung von Rangfolgemodellen zur Optimierung der Suche basierend auf geschäftlichen Zielen. Beispielsweise kann es wünschenswert sein, dass neuere Produkte oder herabgesetzte Produkte in den Suchergebnissen weiter oben angezeigt werden. Sie können Bewertungsprofile auch mithilfe von Tags für eine personalisierte Bewertung erstellen, die auf den Sucheinstellungen der Kunden basiert, die Sie nachverfolgt und separat gespeichert haben. Details finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex (Azure-Suchdienst-REST-API)](https://msdn.microsoft.com/library/azure/dn798928.aspx).

**Sprachunterstützung** ist für 50 verschiedene Sprachen integriert, wobei mehrere Optionen der Verarbeitung natürlicher Sprache verwendet werden, z. B. die bekannten Lucene-Analysefunktionen und die Analysefunktionen von Microsoft, die in Microsoft Office und Bing (nur Vorschau) verwendet werden. Informationen zu den Verarbeitungsmodulen für natürliche Sprache finden Sie unter [Sprachunterstützung (Azure Search-Dienst-REST-API)](https://msdn.microsoft.com/library/azure/dn879793.aspx) für Lucene und [Azure Search-Dienst-REST-API Version 2015-02-28-Preview](search-api-2015-02-28-Preview.md).

**Facettennavigation** bezieht sich auf eine Kategorisierungsstruktur für die selbstgesteuerte Suche, die häufig auf Marken, Modellen, Größen oder anderen Kategorien basiert, die für Ihre Daten sinnvoll sind. Die Facettennavigation wird durch Attribute im Index in Kombination mit einem Facet-Feld implementiert, das in einer Abfrage angegeben wird. Ausführliche Informationen finden Sie unter [Facettennavigation](search-faceted-navigation.md).

**Vorschläge** für Type-ahead- oder AutoVervollständigungs-Abfragen werden durch Attribute im Index unterstützt. Azure Search unterstützt sowohl Fuzzy-Abgleich als auch Infix-Abgleich (Abgleich eines beliebigen Teils der Feldinhalts). Sie können beide Optionen nutzen. Sie schließen sich nicht gegenseitig aus. Informationen zum Aktivieren der Vorschläge finden Sie unter [Erstellen eines Index (Azure Search-Dienst-REST-API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) und Informationen zu deren Verwendung unter [Vorschläge (Azure Search-Dienst-REST-API)](https://msdn.microsoft.com/library/azure/dn798936.aspx).

**Filter** können zur Implementierung der Facettennavigation, in Abfragen oder global zum Einschränken von Suchvorgängen auf im Code definierte Filterkriterien verwendet werden. Filter werden über das Indexschema für bestimmte Felder und über den Suchparameter $Filter aktiviert. Ausführliche Informationen finden Sie unter [Erstellen eines Index (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798941.aspx) und [Suchen von Dokumenten (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

Die **Sortierfunktion** wird auch über das Indexschema für bestimmte Felder aktiviert und anschließend als Suchparameter über den Parameter $orderBy implementiert. Auch hierzu finden Sie ausführliche Informationen unter [Erstellen eines Index (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798941.aspx) und [Suchen von Dokumenten (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

Die **Anzahl** der für eine Abfrage zurückgegebenen Suchergebnisse und die Möglichkeit zur Einschränkung der Anzahl der Treffer, die jeweils zu einem Zeitpunkt zurückgegeben werden können, werden über $top und $skip implementiert. Ausführliche Informationen finden Sie unter [Suchen von Dokumenten (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

Die **Hervorhebung von Treffern** wird über den Abfrageparameter "highlight" angegeben, und sie ermöglichen es Ihnen, dem Benutzer einen Textausschnitt anzuzeigen, in dem gefundene Schlüsselwörter für die vom Benutzer eingegebenen Suchbegriffe hervorgehoben angezeigt werden. Weitere Informationen zu Abfrageparametern finden Sie unter [Suchen von Dokumenten (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

##Berichterstellung und Analyse

Der Ressourceneinsatz wird im Service-Dashboard angezeigt, sodass Sie schnell einen Eindruck davon erhalten, wie der Speicherplatz genutzt wird.

Speicherverbrauch, Dokumentanzahl und Indexanzahl sind im Portal verfügbar. Sie können auch die API verwenden. Nähere Informationen finden Sie unter [Abrufen von Indexstatistiken (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798942.aspx).

Es gibt keine integrierten Mechanismen zum Messen des Abfragedurchsatzes oder anderer Dienstvorgänge. Zudem wird derzeit die Protokollierung oder Analyse der Suchergebnisse nicht unterstützt (z. B. Abrufen einer Liste von Suchbegriffen, die keine Ergebnisse zurückgegeben, oder Melden des Ursprungs der Suchanforderung).

##Ausprobieren

Besuchen Sie [Erstellen eines Azure Search-Diensts](search-create-service-portal.md), um den Dienst einzurichten, oder nutzen Sie [Testen von Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) für eine kostenlose einstündige Sitzung, für die kein Setup oder Abonnement erforderlich ist.

Sie können auch diese Lernprogramme ausprobieren:

[Verwenden von Azure Search in .NET](search-howto-dotnet-sdk.md) [Erste Schritte mit Azure Search .NET](search-get-started-dotnet.md) [Azure Search: Lernprogramme, Videodemos und Beispiele](https://msdn.microsoft.com/library/azure/dn818681.aspx)

<!---HONumber=July15_HO4-->