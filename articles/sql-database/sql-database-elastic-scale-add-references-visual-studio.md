<properties 
	pageTitle="Hinzufügen von Elastic Scale-Verweisen der Azure SQL DB zu einem Visual Studio-Projekt" 
	description="So fügen Sie .NET-Referenzen für Elastic Scale-APIs über Nuget zu Visual Studio-Projekten hinzu" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Hinzufügen von Verweisen auf die Clientbibliothek für elastische Datenbanken in einem Visual Studio-Projekt 

### Voraussetzungen: 

- Installieren Sie die [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) für Visual Studio 

### Hinzufügen eines Verweises auf die Clientbibliothek für elastische Datenbanken in Visual Studio 

1. Öffnen Sie ein vorhandenes Projekt, oder erstellen Sie ein neues Projekt im Dialogfeld "Neues Projekt", das Sie über **Datei** --> **Neu** --> **Projekt** öffnen. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und wählen Sie **NuGet-Pakete verwalten** aus.
3. Wählen Sie im Menü auf der linken Seite des Fensters "NuGet-Pakete verwalten" **Online** und dann **nuget.org** oder "Alle" aus. 
4. Geben Sie im Dialogfeld **Online suchen** als Suchbegriff **elastische Datenbank** ein, wählen Sie die **Clientbibliothek für elastische Datenbanken** aus, und klicken Sie auf **Installieren**.

	![Onlinesuche][1]
4. Lesen Sie die Lizenz, und klicken Sie auf **Ich stimme zu**. 

Der Verweis auf die Clientbibliothek wurde damit Ihrem Projekt hinzugefügt.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO4-->