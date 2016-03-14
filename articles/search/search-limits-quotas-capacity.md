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
- **Basic (Vorschau)** bietet spezifische Computerressourcen für Produktions-Workloads mit geringerem Umfang. Dieser Tarif wird derzeit in der Vorschau und zu einem [reduzierten Preis](https://azure.microsoft.com/pricing/details/search/) angeboten.
- **Standard** wird auf dedizierten Rechnern ausgeführt, die nur von Ihrem Dienst verwendet werden. Sie bieten höhere Speicher- und Verarbeitungskapazität auf jeder Stufe, einschließlich der Mindestkonfiguration. Standard steht in zwei Stufen (S1 und S2) zur Verfügung. 

## Tarifgrenzwerte

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Abfragen pro Sekunde (QPS) sind vor allem im gemeinsamen Dienst variabel, da der Durchsatz auf der verfügbaren Bandbreite und dem Wettkampf um Systemressourcen basiert. Die Azure-Computing- und Speicherressourcen zur Unterstützung des gemeinsam genutzten Diensts werden von mehreren Abonnenten gemeinsam verwendet, sodass der QPS-Wert für Ihre Lösung stets in Abhängigkeit davon variiert, wie viele andere Workloads gleichzeitig ausgeführt werden. Auf der Standard-Stufe können Sie den QPS-Wert genauer schätzen, da Sie die Kontrolle über mehr Parameter haben. Im Abschnitt mit bewährten Methoden unter [Verwalten der Suchlösung](search-manage.md) finden Sie Anleitungen zur Berechnung des QPS-Werts für Ihre Workloads.

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

<!---HONumber=AcomDC_0302_2016-->