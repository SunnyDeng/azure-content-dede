<properties 
   pageTitle="Überwachen der Nutzung und Statistiken eines Azure-Suchdiensts | Microsoft Azure | Gehosteter Cloudsuchdienst" 
   description="Verfolgen Sie die Ressourcennutzung und Indexgröße für Azure Search nach, einem in Microsoft Azure gehosteten Cloudsuchdienst." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="11/04/2015"
   ms.author="heidist"/>

# Überwachen der Nutzung und Statistiken eines Azure-Suchdiensts

Durch Nachverfolgen des Wachstums von Indizes und Dokumentgröße können Sie die Kapazität proaktiv anpassen, bevor die Obergrenze, die Sie für den Dienst festgelegt haben, erreicht wird.

Zum Überwachen der Ressourcennutzung können die Zähler und Statistiken einfach im [klassischen Azure-Portal](https://portal.azure.com) angezeigt werden, aber Sie können diese Informationen auch programmgesteuert abrufen, wenn Sie ein benutzerdefiniertes Dienstverwaltungstool erstellen. In diesem Artikel werden die Schritte für beide Verfahren behandelt.

##Anzeigen von Anzahl und Metriken im Portal 

1. Melden Sie sich beim [klassischen Azure-Portal](https://portal.azure.com) an. 

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Die Startseite enthält Kacheln für den Dienst, oder Sie können auch über „Durchsuchen“ auf der Indexleiste zum Dienst navigieren. Schrittweise Anweisungen finden Sie unter [Erstellen eines Diensts](search-create-service-portal.md).

Der Abschnitt „Verwendung“ umfasst einen Verbrauchszähler, aus dem hervorgeht, welcher Teil des verfügbaren Ressourcen zurzeit verwendet wird.

  ![][1]

Denken Sie daran, dass für den freigegebenen Dienst ein Maximum von einem Replikat und jeweils einer Partition gilt. Darüber hinaus können nur insgesamt 10.000 Dokumente oder 50 MB an Daten unterstützt werden, je nachdem, welcher Grenzwert zuerst erreicht wird.

##Abrufen von Indexstatistiken mit der REST-API

Sowohl die REST-API von Azure Search als auch das .NET SDK bieten programmgesteuerten Zugriff auf Dienstmetriken. Bei Verwendung von [Indexern](https://msdn.microsoft.com/library/azure/dn946891.aspx) zum Laden eines Index aus Azure SQL-Datenbank oder DocumentDB steht eine zusätzliche API zum Abrufen der benötigen Zahlen zur Verfügung.

  + [Abrufen von Indexstatistiken](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Dokumentenanzahl](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Abrufen des Indexerstatus](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## Nächste Schritte

Überprüfen Sie [Grenzen und Kapazität](search-limits-quotas-capacity.md), um die Kombination von Partitionen und Replikaten zu bestimmen, die erforderlich sind, wenn die vorhandene Kapazität nicht ausreicht.

Weitere Informationen zur Dienstverwaltung finden Sie unter [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md).

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<!---HONumber=AcomDC_1203_2015-->