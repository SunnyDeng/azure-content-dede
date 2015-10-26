<properties 
	pageTitle="Data Factory-Anwendungsfall – Produktempfehlungen" 
	description="Informationen zu einem Anwendungsfall, der mithilfe von Azure Data Factory zusammen mit anderen Diensten implementiert wurde." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Anwendungsfall – Produktempfehlungen 

Azure Data Factory ist einer der zahlreichen Dienste, die zum Implementieren der Cortana Analytics Suite von Lösungsoptimierungen verwendet werden. Auf der Seite [Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics) finden Sie Details zu dieser Suite. In diesem Dokument wird einen allgemeiner Anwendungsfall beschrieben, den Azure-Benutzer bereits mithilfe von Azure Data Factory und anderen Cortana Analytics-Komponentendiensten in den Griff bekommen und implementiert haben.

## Szenario

Onlinehändler möchten ihre Kunden zum Kauf von Produkten verleiten, indem sie ihnen Produkte präsentieren, an denen sie am wahrscheinlichsten interessiert sind und die sie daher am wahrscheinlichsten kaufen werden. Hierfür müssen Onlinehändler die Onlineerfahrung des jeweiligen Benutzers mithilfe personalisierter Produktempfehlungen anpassen. Dazu werden aktuelle und Verlaufsdaten zum Kaufverhalten, Produktinformationen, neu eingeführte Marken sowie Produkt- und Kundensegmentierungsdaten berücksichtigt, um dem Kunden das relevanteste Produkt zu präsentieren. Darüber hinaus können dem Benutzer Produktempfehlungen basierend auf der Analyse des kombinierten Gesamtverhaltens aller Benutzer gezeigt werden.

Ziel dieser Händler ist es, mehr Interessenten in Käufer umzuwandeln, um einen höheren Umsatzerlös zu generieren. Sie erreichen dies durch Bereitstellung von kontextbezogenen, auf dem Verhalten basierenden Produktempfehlungen auf Grundlage von Kundeninteressen und Aktionen. Für diesen Anwendungsfall verwenden wir Onlinehändler als Beispiel für Unternehmen, die die Produktpräsentation für ihre Kunden optimieren möchten. Diese Prinzipien gelten jedoch für alle Unternehmen, die ihre Kunden an ihre Waren und Dienstleistungen binden und deren Kauferfahrung mit personalisierten Produktempfehlungen verbessern möchten.

## Herausforderungen

Es gibt viele Herausforderungen, mit denen Onlinehänder bei dem Versuch konfrontiert sind, diese Art von Anwendungsfall zu implementieren. Erstens müssen Daten in verschiedenen Größen und Formen aus mehreren Datenquellen lokal und in der Cloud erfasst werden, um Produktdaten, Verlaufsdaten zum Kundenverhalten und Benutzerdaten zu sammeln, während der Kunde die Website des Onlinehändlers durchsucht.

Zweitens müssen personalisierte Produktempfehlungen angemessen und präzise vorausberechnet werden. Zusätzlich zum Produkt, zur Marke, zum Kundenverhalten und zu Suchdaten müssen Onlinehändler auch das Feedback anderer Kunden zu vorherigen Produktkäufen zum Bestimmen der besten Produktempfehlungen für den jeweiligen Benutzer berücksichtigen.

Drittens müssen die Empfehlungen dem Kunden sofort zur Verfügung stehen, um ihm eine nahtlose Such- und Kauferfahrung sowie die aktuellesten und relevantesten Empfehlungen zu bieten. Drittens müssen Onlinehändler die Effektivität des Vorgehens messen, indem sie den Gesamterfolg von Up-Selling und Cross-Selling beim Verkauf nachverfolgen und ihre künftigen Empfehlungen entsprechend ändern.

## Lösungsübersicht

Dieser Beispielanwendungsfall wurde von realen Azure-Benutzern mit Azure Data Factory und anderen Cortana Analytics-Komponentendiensten gelöst und implementiert. Dabei kamen u. a.[HDInsight](http://azure.microsoft.com/services/hdinsight/) und [Power BI](https://powerbi.microsoft.com/) für das Erfassen, Vorbereiten, Transformieren, Analysieren und Veröffentlichen der endgültigen Daten zum Einsatz.

Der Onlinehändler verwendet einen Azure-Blobspeicher, einen lokalen SQL-Server, Azure SQL-Datenbank und einen relationalen Datamart als Datenspeicheroptionen im gesamten Workflow. Der Blobspeicher enthält Kundeninformationen, Daten zum Kundenverhalten und Produktdaten. Die Produktdaten enthalten Informationen zur Produktmarke und einen Produktkatalog, der lokal in einem SQL Data Warehouse gespeichert ist.

Wie in der folgenden Abbildung dargestellt, werden alle Daten kombiniert und einem Produktempfehlungssystem hinzugefügt. Dieses hat den Zweck, personalisierte Empfehlungen basierend auf den Interessen und Aktionen der Kunden zu präsentieren, während der Kunde die Produkte im Katalog auf der Website des Onlinehändlers durchsucht. Den Kunden werden auch ähnliche Produkte gezeigt, die im Zusammenhang mit dem betrachteten Produkt stehen können, und zwar basierend auf allgemeinen Nutzungsmustern der Website, die sich nicht auf einen bestimmten Benutzer beziehen.

![Anwendungsfalldiagramm](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes an unformatierten Webprotokolldateien werden tagtäglich von der Website des Onlinehändlers als teilstrukturierte Daten erzeugt. Die unformatierten Webprotokolldateien sowie die Kunden- und Produktkataloginformationen werden regelmäßig in einem Azure-Blobspeicherkonto erfasst. Dazu wird die global bereitgestellte Datenverschiebungsfunktion des Data Factory-Diensts verwendet. Die unformatierten Protokolldateien für den jeweiligen Tag werden im Blobspeicher für die langfristige Speicherung (nach Jahr und Monat) partitioniert. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) (Hadoop-as-a-Service) dient zum Partitionieren der unformatierten Protokolldateien (für eine bessere Verwaltbarkeit, Verfügbarkeit und Leistung) im Blobspeicher und zum Verarbeiten der erfassten Protokolle nach Maß mithilfe von Hive- und Pig-Skripts. Die partitionierten Webprotokolldaten werden anschließend verarbeitet, um die erforderlichen Eingaben für ein durch maschinelles Lernen gestütztes Empfehlungssystem zu extrahieren, das zum Erzeugen der personalisierten Produktempfehlungen dient.

Das durch maschinelles Lernen gestützte Empfehlungssystem in diesem Beispiel ist eine Open-Source-Empfehlungsplattform mit maschinellem Lernen von [Apache Mahout](http://mahout.apache.org/). Beachten Sie, dass jede [Azure Machine Learning](http://azure.microsoft.com/services/machine-learning/)- oder benutzerdefinierte Lösung genutzt werden kann. Das Mahout-Modell dient zum Vorausberechnen der Ähnlichkeit zwischen Artikeln auf der Website des Onlinehändlers basierend auf allgemeinen Nutzungsmustern und zum Generieren personalisierter Empfehlungen basierend dem jeweiligen Kunden.

Schließlich wird das Resultset der personalisierten Produktempfehlungen in einen relationalen Datamart zur Nutzung auf der Website des Onlinehändlers verschoben. Auf das Resultset im Blobspeicher können andere Anwendungen auch direkt zugreifen. Es kann auch für andere Nutzer und Anwendungsfälle in weitere Datenspeicher verschoben werden.

## Vorteile

Durch Optimieren ihrer Produktempfehlungsstrategie und deren Ausrichtung auf Unternehmensziele erfüllt die Lösung die Verkaufsförderungs- und Marketingziele des Onlinehändlers. Darüber hinaus kann das Unternehmen den Workflow für Produktempfehlungen nun effizient, zuverlässig und wirtschaftlich betreiben und verwalten. Das Modell kann mühelos aktualisiert und seine Effektivität basierend auf den Kennzahlen für die Kundengewinnung und -bindung optimiert werden. Mithilfe von Azure Data Factory konnte der Onlinehändler von seiner zeitaufwändigen und kostspieligen manuellen Cloudressourcenverwaltung zu einer bedarfsgesteuerten Lösung wechseln, wodurch Zeit und Geld gespart und die Zeit bis zur Lösungsbereitstellung verkürzt werden. Ansichten der Datenherkunft und Integrität von Diensten lassen sich dank der intuitiven Data Factory-Benutzeroberfläche für Überwachung und Verwaltung im Azure-Portal einfach visualisieren. Die Lösung kann nun so geplant und verwaltet werden, dass einsatzbereite Daten zuverlässig erzeugt und den Benutzern bereitgestellt werden. Zudem werden Daten- und Verarbeitungsabhängigkeiten ohne Administratoreingriffe automatisch verwaltet.

Durch die Bereitstellung dieses personalisierten Einkaufserlebnisses hat der Onlinehändler eine wettbewerbsfähigere, ansprechende Kundenerfahrung geschaffen, die zu mehr Umsatz und höherer Kundenzufriedenheit geführt hat.



  

<!---HONumber=Oct15_HO3-->