<properties
	pageTitle="Entwicklungsbibliotheken und Tools für Azure Batch | Microsoft Azure"
	description="Rufen Sie die benötigten Bibliotheken und Tools zum Entwickeln von Azure Batch-Anwendungen ab"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Entwicklungsbibliotheken und Tools für Azure Batch
<p> Rufen Sie diese Bibliotheken und Tools zum Entwickeln von Azure Batch-Anwendungen ab.

## Batch

+ [Batch-Clientbibliothek für .NET](http://www.nuget.org/packages/Azure.Batch/) \(NuGet\) – Entwickeln von Clientanwendungen zum Ausführen von Aufträgen mit dem Batch-Dienst
+ [Batch-Verwaltungsbibliothek](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) \(NuGet\) – Verwalten von Batch-Konten
+ [Batch-Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) \(GitHub\) – GUI-Anwendung und Beispiel für Suche, Abruf und Aktualisierung wichtiger Elemente in einem Batch-Konto, einschließlich Aufträge und Aufgaben, Computeknoten und -pools sowie Dateien auf einem Computeknoten. Weitere Informationen finden Sie in diesem [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Batch Apps

+ [Cloud-SKD für Batch-Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) \(NuGet\) – Ermöglichen der Auslagerung von Aufträgen an den Batch-Dienst durch Anwendungen
+ [Visual Studio-Erweiterung für Batch-Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) \(Visual Studio Gallery\) – Aktivieren von Anwendungen in Visual Studio für Clouds mithilfe des Cloud-SDK für Batch-Apps
+ [Client-SDK für Batch-Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) \(NuGet\) – Interagieren mit Anwendungen, denen das Auslagern von Aufträgen an den Batch-Dienst ermöglicht wurde
+ [Python-Client für Batch-Apps](https://github.com/Azure/azure-batch-apps-python) \(GitHub\) – Python-Clientmodul für die Interaktion mit Anwendungen, die in einem Batch-App-Dienst eingerichtet wurden

>[AZURE.IMPORTANT]Azure bietet die Batch-Apps-API nur als Vorschau. Sie sollten sie nur zur Entwicklung von Testprojekten oder Machbarkeitsstudien verwenden. Wichtige Batch-Apps-Funktionen werden in künftigen Versionen in die Batch-API integriert.

## Zusätzliche Ressourcen

+ [Codebeispiele](https://github.com/Azure/azure-batch-samples) \(GitHub\)
+ [Azure Batch-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO5-->