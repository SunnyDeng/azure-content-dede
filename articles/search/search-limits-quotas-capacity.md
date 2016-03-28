<properties
	pageTitle="Grenzwerte für den Azure Search-Dienst | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Grenzwerte für den Dienst, die bei der Kapazitätsplanung verwendet werden, sowie Höchstwerte für Anforderungen und Antworten für Azure Search."
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
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Grenzwerte für den Azure Search-Dienst

Die Höchstwerte für Speicher, Workloads und Mengen von Indizes, Dokumenten und anderen Objekten hängen davon ab, ob Sie Azure Search zum **Free**-, **Basic**- oder **Standard**-Tarif hinzufügen.

- **Free** ist ein gemeinsamer mehrinstanzfähiger Dienst, der Teil Ihres Azure-Abonnements ist. Es handelt sich um eine Option für vorhandene Abonnenten ohne zusätzliche Kosten, die es Ihnen ermöglicht, den Dienst zu testen, bevor Sie sich für spezifische Ressourcen anmelden. 
- **Basic (Vorschau)** bietet spezifische Computerressourcen für Produktions-Workloads mit geringerem Umfang. Dieser Tarif befindet sich derzeit in der Vorschau und wird [in der Vorschauphase zu einem um 50 % reduzierten Preis](https://azure.microsoft.com/pricing/details/search/) angeboten.
- **Standard** wird auf dedizierten Computern ausgeführt. Sie bieten höhere Speicher- und Verarbeitungskapazität auf jeder Stufe, einschließlich der Mindestkonfiguration. Standard steht in zwei Stufen (S1 und S2) zur Verfügung. 

Mit Ausnahme des S2-Tarifs, der ein Supportticket erfordert, können alle Tarife [im Portal bereitgestellt werden](search-create-service-portal.md). Senden Sie eine E-Mail an azuresearch_contact@microsoft.com, um mit S2 zu starten.

## Tarifgrenzwerte

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

## Grenzwerte für API-Schlüssel

API-Schlüssel werden für die Dienstauthentifizierung verwendet. Es gibt zwei Arten. Administratorschlüssel werden im Anforderungsheader angegeben und gewähren vollständigen Lese-und Schreibzugriff für den Dienst. Abfrageschlüssel sind schreibgeschützt, die in der URL angegeben und normalerweise an Clientanwendungen verteilt werden.

- Maximal 2 Administratorschlüssel pro Dienst
- Maximal 50 Abfrageschlüssel pro Dienst

## Grenzwerte für Anforderungen

- Maximal 16 MB pro Anforderung <sup>1</sup>
- Maximale URL-Länge von 8 KB
- Maximal 1000 Dokumente pro Batch von Hochlade-, Zusammenführungs- oder Löschvorgängen für Indizes
- Maximal 32 Felder in $orderby-Klausel
- Maximale Suchbegriffgröße ist 32.766 Bytes (32 KB minus 2 Bytes) von UTF-8-codiertem Text

## Grenzwerte für Antworten

- Maximale Rückgabe von 1000 Dokumenten pro Seite mit Suchergebnissen
- Maximale Rückgabe von 100 Vorschlägen pro Anforderung der Vorschlags-API

<sup>1</sup> In Azure Search darf der Inhalt einer Anforderung nicht größer als 16 MB sein. Dies beschränkt möglicherweise den Inhalt einzelner Felder oder Sammlungen, für die ansonsten keine theoretischen Beschränkungen gelten. (Weitere Informationen zur Feldzusammensetzung und den Beschränkungen finden Sie unter [Unterstützte Datentypen (Azure Search)](https://msdn.microsoft.com/library/azure/dn798938.aspx)).

## Abfragen pro Sekunde

Auch wenn in der Preisübersicht und im obigen Diagramm mit den [Tarifgrenzwerten](#TierLimits) grobe Schätzungen bereitgestellt werden, sind die tatsächlichen Abfragen pro Sekunde (QPS) schwierig zu bestimmen. Dies gilt vor allem für den gemeinsamen Dienst im Free-Tarif, bei dem der Durchsatz auf der verfügbaren Bandbreite und der Konkurrenz um Systemressourcen basiert. Die Compute- und Speicherressourcen zur Unterstützung des gemeinsamen Diensts werden von mehreren Abonnenten gemeinsam verwendet, sodass der QPS-Wert für Ihre Lösung stets in Abhängigkeit davon variiert, wie viele andere Workloads gleichzeitig ausgeführt werden.

Auf der Standard-Stufe können Sie den QPS-Wert genauer schätzen, da Sie die Kontrolle über mehr Parameter haben. Im Abschnitt mit bewährten Methoden unter [Verwalten der Suchlösung](search-manage.md) finden Sie Anleitungen zur Berechnung des QPS-Werts für Ihre Workloads.

<!---HONumber=AcomDC_0316_2016-->