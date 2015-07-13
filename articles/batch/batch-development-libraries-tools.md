<properties 
	pageTitle="Entwicklungsbibliotheken und Tools für Azure Batch" 
	description="Verwenden Sie diese Bibliotheken und Tools zum Entwickeln von Azure Batch- und Batch-Apps-Anwendungen." 
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
<p> Verwenden Sie diese Bibliotheken und Tools zum Entwickeln von Azure Batch- und Batch-Apps-Anwendungen.

## Batch

+ [Batch-Clientbibliothek für .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – Entwickeln von Clientanwendungen zum Ausführen von Aufträgen mit dem Batch-Dienst
+ [Batch-Verwaltungsbibliothek](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Verwalten von Batch-Dienstkonten
+ [Batch-Explorer](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – GUI-Anwendung und Beispiel für Suche, Abruf und Aktualisierung wichtiger Elemente in einem Batch-Konto, einschließlich Aufträge und Aufgaben, virtuelle Aufgabencomputer (Task Virtual Machines, VTMs) und Pools sowie Dateien auf einem TVM.

> [AZURE.TIP]Batch-Explorer ist ein hervorragendes Tool, wenn Sie das erste Mal in Batch arbeiten oder eine Überwachung oder Problembehandlung der Batch-Aktivität ausführen möchten. Da es sich um ein Codebeispiel handelt, zeigt der Quellcode, wie die Features mithilfe der Batch-.NET-API implementiert werden. Weitere Informationen finden Sie in diesem [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Cloud-SKD für Batch-Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – Ermöglichen der Auslagerung von Aufträgen an den Batch-Dienst durch Anwendungen
+ [Visual Studio-Erweiterung für Batch-Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio Gallery) – Aktivieren von Anwendungen in Visual Studio für Clouds mithilfe des Cloud-SDK für Batch-Apps
+ [Client-SDK für Batch-Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – Interagieren mit Anwendungen, denen das Auslagern von Aufträgen an den Batch-Dienst ermöglicht wurde
+ [Python-Client für Batch-Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub) – Python-Clientmodul für die Interaktion mit Anwendungen, die in einem Batch-App-Dienst eingerichtet wurden
+ [Batch-Apps-Beispiel für Blender](https://github.com/Azure/azure-batch-apps-blender) (GitHub) – Add-On für die Open-Source-Renderingsoftware Blender, die das Batch-Apps-SDK und einen Python-Client zum Einrichten einer cloudbasierten Renderingplattform nutzt


## Zusätzliche Ressourcen

+ [Codebeispiele](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Azure Batch-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Erste Schritte mit der Azure Batch-Bibliothek für .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=62-->