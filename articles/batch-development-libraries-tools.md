<properties 
	pageTitle="Entwicklungsbibliotheken und Tools für Azure Batch" 
	description="Abrufen von Bibliotheken und Tools, die Sie zum Entwickeln von Azure Batch und Batch-Apps Anwendungen benötigen" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Entwicklungsbibliotheken und Tools für Azure Batch 
<p> Erhalten Sie diese Bibliotheken und Tools zum Entwickeln von Anwendungen für Azure-Batch und Batch-Apps.

## Batch

+ [Batch-Clientbibliothek für .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – Entwickeln von Clientanwendungen, die zum Ausführen von Aufträgen mit dem Batch-Dienst
+ [Batch-Verwaltungsbibliothek](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Verwalten von Batch-Dienstkonten
+ [Batch Explorer](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN)-GUI-Anwendung Beispiel zu durchsuchen, zugreifen und Hauptelemente innerhalb einer Batch-Kontos, einschließlich der Aufträge und Tasks, tvms (TVMs) und Pools und Dateien für eine TVM zu aktualisieren.

> [AZURE.TIP]Batch-Explorer ist ein hervorragendes Tool, wenn Sie neue Stapel oder überwachen oder Aktivität beheben möchten. Da es sich um ein Codebeispiel ist, der Quellcode erfahren Sie, wie die Funktionen sind mit der Batch .NET API implementiert. Siehe die [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Batch Apps Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – ermöglichen es Anwendungen, die Aufträge an den Dienst für die Batch-Abladung
+ [Batch Apps-Erweiterung für Visual Studio](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio Gallery) – Cloud-Anwendungen in Visual Studio mit dem Cloud-SDK von Batch-Apps
+ [Batch Apps Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interagieren Sie mit Anwendungen, die Aufträge an den Dienst Batch ausgelagerte aktiviert
+ [Batch Apps Python-Client](https://github.com/Azure/azure-batch-apps-python) (GitHub) – Python-Client-Moduls für die Interaktion mit Anwendungen in einem Batch Apps Dienst einrichten
+ [Batch Apps Blender Beispiel](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Addon, Blender open Source-Rendering-Software, die den Batch-Apps SDK und Python-Client verwendet, um eine Cloud-basierte Rendering-Plattform einzurichten


## Zusätzliche Ressourcen

+ [Codebeispiele](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Azure-Batch-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Erste Schritte mit der Azure-Batch-Bibliothek für .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->