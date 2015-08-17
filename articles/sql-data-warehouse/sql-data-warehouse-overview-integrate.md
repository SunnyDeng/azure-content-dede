<properties
   pageTitle="Erstellen von integrierten Lösungen mit SQL Data Warehouse | Microsoft Azure"
   description="Tools und Partner mit Lösungen, die in SQL Data Warehouse integriert werden können."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

#Integrationsdienste mit SQL Data Warehouse
Zusätzlich zur Kernfunktionalität ermöglicht SQL Data Warehouse Benutzern die gleichzeitige Nutzung vieler anderer Dienste in Azure. Insbesondere haben wir aktuell Schritte unternommen, um die weitreichende Integration folgender Dienste voranzubringen:

+ Power BI
+ Azure Data Factory
+ Azure Machine Learning
+ Azure Stream Analytics

Darüber hinaus sind wir dabei, die tiefere Integration mit einer Reihe an anderen Diensten im Azure-Ökosystem zu erarbeiten.

##Power BI
Mit der Power BI-Integration können Benutzer die Rechenleistung von SQL Data Warehouse mit der dynamischen Berichterstellung und Visualisierung der Power BI intensiv nutzen. Die Integration in Power BI umfasst derzeit:

+ **Direct Connect**: Eine erweiterte Verbindung mit logischem Pushdown gegen SQL Data Warehouse. Dadurch sind schnellere Analysen in größerem Umfang möglich.
+ **Open in Power BI**: Die Schaltfläche 'Open in Power BI' übergibt für eine nahtlose Verbindung Instanzinformationen an Power BI. 

Weitere Informationen finden Sie unter [Integration in Power BI](../sql-data-warehouse-integrate-power-bi.md) oder in der [Power BI-Dokumentation](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

##Azure Data Factory
Azure Data Factory bietet Benutzern eine verwaltete Plattform zur Erstellung von Pipelines zum Extrahieren-Transformieren-Laden. Die SQL Data Warehouse-Integration in Azure Data Factory umfasst Folgendes:

+ **Datenverschiebung**: Erstellen Sie einen Zeitplan für die Datenübertragung für eine Reihe lokaler und Azure-Dienste.
+ **Gespeicherte Prozeduren**: Orchestrieren Sie die Ausführung von gespeicherten Prozeduren in SQL Data Warehouse.

Weitere Informationen finden Sie unter[Integration in Azure Data Factory](../sql-data-warehouse-integrate-azure-data-factory.md) oder in der [Azure Data Factory-Dokumentation](https://azure.microsoft.com/documentation/services/data-factory/).

##Azure Machine Learning
Azure Machine Learning ist ein vollständig verwalteter Analytics-Dienst, mit dem Benutzer komplexe Modelle erstellen können, die einen großen Satz von Vorhersagetools nutzen. SQL Data Warehouse wird sowohl als Quelle als auch als Ziel für diese Modelle mit den folgenden Funktionen unterstützt:

+ **Daten lesen:** Skalierte Laufwerkmodelle mit T-SQL gegen SQL Data Warehouse. 
+ **Daten schreiben:** Änderungen von jedem Modell zurück an SQL Data Warehouse geben.

Weitere Informationen finden Sie unter[Integration in Azure Machine Learning](../sql-data-warehouse-integrate-azure-machine-learning.md) oder in der [Azure Machine Learning-Dokumentation](https://azure.microsoft.com/services/machine-learning/).

##Azure Stream Analytics
Azure Stream Analytics ist eine komplexe, vollständig verwaltete Infrastruktur für die Verarbeitung und Nutzung von Azure Event Hub generierten Ereignisdaten. Die Integration mit SQL Data Warehouse ermöglicht das effiziente Streamen von Daten und die Speicherung neben relationalen Daten, sodass eine tiefergehende, erweiterte Analyse durchgeführt werden kann.

+ **Auftragsausgabe:** Die Ausgabe von Stream Analytics-Aufträgen direkt an SQL Data Warehouse senden.

Weitere Informationen finden Sie unter[Integration in Azure Stream Analytics](../sql-data-warehouse-integrate-azure-stream-analytics.md) oder in der [Azure Stream Analytics-Dokumentation](https://azure.microsoft.com/documentation/services/stream-analytics/).

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->