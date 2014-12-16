<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Hinzufügen von Elastic Scale-Verweisen der Azure SQL DB zu einem Visual Studio-Projekt" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Gewusst wie: Hinzufügen von Elastic Scale-Verweisen der Azure SQL DB zu einem Visual Studio-Projekt

###Voraussetzungen: 

- Installieren Sie [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) für Visual Studio 

###So fügen Sie einen Elastic Scale-Verweis in Visual Studio hinzu 

1. Erstellen Sie im Dialogfeld "Neues Projekt" unter **Datei** --> **Neu** --> **Projekt** ein neues Projekt 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und wählen Sie **NuGet-Pakete verwalten** aus.
3. Wählen Sie im Menü auf der linken Seite des Fensters "NuGet-Pakete verwalten" **Online** und dann **nuget.org** oder "Alle" aus.
4. Geben Sie im Dialogfeld **Online suchen** **Elastic Scale** ein, wählen Sie die **Elastic Scale-Clientbibliotheken** aus, und klicken Sie auf **Installieren**.
4. Lesen Sie die Lizenz, und klicken Sie auf **Ich stimme zu**. 

Die Elastic Scale-Verweise der Azure SQL-Datenbank wurden nun zu Ihrem Projekt hinzugefügt. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
