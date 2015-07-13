<properties 
	pageTitle="Einrichten des Computers für die Media Services-Entwicklung mit .NET" 
	description="Lernen Sie die Voraussetzungen für Media Services bei Verwendung des Media Services SDK für .NET kennen. Erfahren Sie zudem, wie Sie eine Visual Studio-App erstellen." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#Media Services-Entwicklung mit .NET 

In diesem Thema wird das Entwickeln von Media Services-Anwendungen mit .NET erläutert.

Die Bibliothek für das **Azure Media Services .NET SDK** ermöglicht es Ihnen, unter Verwendung von .NET für Media Services zu programmieren. Um die Entwicklung mit .NET weiter zu vereinfachen, steht die Bibliothek für **Azure Media Services .NET SDK-Erweiterungen** zur Verfügung. Diese Bibliothek enthält eine Reihe von Erweiterungsmethoden und Hilfsfunktionen, die Ihren .NET-Code vereinfachen. Beide Bibliotheken sind über **NuGet** und **GitHub** verfügbar.
 

##Voraussetzungen

-   Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos](media-services-create-account.md).
-   Betriebssystem: Windows 7, Windows 2008 R2 oder Windows 8.
-   .NET Framework 4.5.
-   Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express). 
  

##Erstellen und Konfigurieren eines Visual Studio-Projekts 

Dieser Abschnitt beschreibt, wie Sie ein Projekt in Visual Studio erstellen und für die Mediendienste-Entwicklung einrichten. Es handelt sich um eine C# Windows-Konsolenanwendung, allerdings gelten für andere Typen von Projekten für Mediendienst-Anwendungen (z. B. eine Windows Forms-Anwendung oder ASP.NET-Webanwendung) dieselben Schritte.

In diesem Abschnitt wird beschrieben, wie Sie mithilfe von **NuGet** das Media Services .NET SDK und andere abhängige Bibliotheken hinzufügen.

Sie können auch die neuesten Media Services .NET SDK-Komponenten von GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) und [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) herunterladen, die Projektmappe erstellen und die Verweise dem Clientprojekt hinzufügen. Beachten Sie, dass alle erforderlichen Abhängigkeiten automatisch heruntergeladen und extrahiert werden.

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1. Geben Sie **Name**, **Ort** und **Lösungsname** ein und klicken Sie auf OK. 

2. Erstellen Sie die Projektmappe.

2. Verwenden Sie **NuGet**, um **Azure Media Services .NET SDK-Erweiterungen** zu installieren und hinzuzufügen. Durch Installieren dieses Pakets werden auch das **Media Services .NET SDK** installiert und alle anderen erforderlichen Abhängigkeiten hinzugefügt.
	1. Stellen Sie sicher, dass Sie die neueste Version von NuGet installiert haben. Weitere Informationen und Installationsanweisungen finden Sie unter [NuGet](http://nuget.codeplex.com/).
	
	2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Namen des Projekts, und wählen Sie „NuGet-Pakete verwalten…“ aus.
	
		Das Dialogfeld NuGet-Pakete verwalten wird geöffnet.

	3. Suchen Sie im Onlinekatalog nach den Azure Media Services-Erweiterungen, wählen Sie die Azure Media Services .NET SDK-Erweiterungen aus, und klicken Sie dann auf die Schaltfläche „Installieren“.
 
		Das Projekt wird geändert, und Verweise auf die Media Services .NET SDK-Erweiterungen, das Media Services .NET-SDK und andere abhängige Assemblys werden hinzugefügt.

	4. Um die Entwicklungsumgebung übersichtlicher zu halten, sollten Sie die Verwendung von NuGet Package Restore in Erwägung ziehen. Weitere Informationen finden Sie unter [NuGet Package Restore](http://docs.nuget.org/consume/package-restore).

3. Fügen Sie einen Verweis auf die Assembly **System.Configuration** hinzu. Diese Assembly enthält die System.Configuration.**ConfigurationManager**-Klasse, die zum Zugriff auf die Konfigurationsdateien (z. B. "App.config") verwendet wird.

	Um Verweise mithilfe des Dialogfelds „Verweise verwalten“ hinzuzufügen, gehen Sie wie folgt vor:

	1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen. Wählen Sie dann „Hinzufügen“ und „Verweise“.

		Das Dialogfeld „Verweise verwalten“ wird angezeigt.

	2. Suchen Sie unter den .NET Framework-Assemblys die System.Configuration-Assembly, und wählen Sie sie aus.
	3. Klicken Sie auf "OK".


4. Öffnen Sie die Datei "App.config" (fügen Sie die Datei dem Projekt hinzu, wenn sie nicht standardmäßig hinzugefügt wurde), und fügen Sie einen *appSettings*-Abschnitt in der Datei hinzu. Legen Sie die Werte für Ihren Azure Media Services-Kontonamen und Schlüssel fest, wie im folgenden Beispiel gezeigt.
	
	Um den **Kontonamen** und Informationen zum **Kontoschlüssel** zu erhalten, öffnen Sie das **Azure-Verwaltungsportal**, wählen Ihr Media Services-Konto aus und klicken auf die Schaltfläche **SCHLÜSSEL VERWALTEN**.


	<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>


5. Überschreiben Sie die existierenden using-Anweisungen am Anfang von Program.cs durch den folgenden Code.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

Nun können Sie mit der Entwicklung einer Mediendienste-Anwendung beginnen.
 

<!---HONumber=July15_HO1-->