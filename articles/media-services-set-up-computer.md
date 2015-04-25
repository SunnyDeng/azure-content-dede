<properties 
	pageTitle="Einrichten des Computers für Media Services - Azure" 
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
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">Einrichten Ihres Computers für die Mediendienste-Entwicklung</span></h1> 

Dieser Abschnitt enthält allgemeine Voraussetzungen für die Mediendienste-Entwicklung mithilfe des Mediendienste-SDK für .NET. Außerdem wird die Erstellung einer Visual Studio-Anwendung für die Entwicklung mit dem Mediendienste-SDK beschrieben. 

### Voraussetzungen

-   Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Erstellen eines Media Services-Kontos](media-services-create-account.md).
-   Betriebssysteme: Windows 7, Windows 2008 R2 oder Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express). 
-   Verwenden Sie das [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices)-Paket, um das Azure SDK für .NET zu installieren. Das folgende Beispiel zeigt, wie Sie das Azure-SDK mit **Nuget** installieren können.
  
<h2><a name="setup-account"></a><span class="short header">Einrichten eines Azure-Kontos für Media Services</span></h2>

Verwenden Sie das Azure-Verwaltungsportal, um Ihr Mediendienste-Konto einzurichten (empfohlen). Weitere Informationen finden Sie unter [Erstellen eines Media Services-Kontos][]. Nach der Erstellung des Kontos im Verwaltungsportal können Sie Ihren Computer für die Mediendienste-Entwicklung einrichten. 

### Erstellen einer Anwendung in Visual Studio

Dieser Abschnitt beschreibt, wie Sie ein Projekt in Visual Studio erstellen und für die Mediendienste-Entwicklung einrichten.  Es handelt sich um eine C# Windows-Konsolenanwendung, allerdings gelten für andere Typen von Projekten für Mediendienst-Anwendungen (z. B. eine Windows Forms-Anwendung oder ASP.NET-Webanwendung) dieselben Schritte.

   1. Erstellen einer neuen Visual C#-**Konsolenanwendung**:  Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1. Geben Sie **Name**, **Ort** und **Lösungsname** ein und klicken Sie auf **OK**.
   2. Stellen Sie sicher, dass Sie das Zielframework auf .NET Framework 4 festlegen. Klicken Sie dazu mit der rechten Maustaste auf das Visual Studio-Projekt und wählen Sie "Eigenschaften" aus. Wählen Sie auf der Registerkarte "Anwendung" .NET Framework 4 für das Ziel-Framework aus.   
   3. Fügen Sie einen Verweis auf die Assembly **System.Configuration** hinzu. Um einen Verweis auf **System.Configuration** hinzuzufügen, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und wählen Sie **Verweis hinzufügen...** aus. Wählen Sie im Dialogfeld **Verweise verwalten** den Eintrag **System.Configuration** aus und klicken Sie auf **OK**.
   4. Fügen Sie über das Paket [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) Verweise auf **Azure SDK für .NET** hinzu. (alle anderen abhängigen Assemblys werden ebenfalls installiert).  

	Um Verweise mit Nuget hinzuzufügen, gehen Sie wie folgt vor. Klicken Sie im **Hauptmenü** in Visual Studio auf **EXTRAS** -> **Bibliothekspaket-Manager** -> **Paket-Manager-Konsole**. Geben Sie im Konsolenfenster **Install-Package windowsazure.mediaservices** ein, und drücken Sie die **Eingabetaste**.
   4. Überschreiben Sie die existierenden using-Anweisungen am Anfang von Program.cs durch den folgenden Code.

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

Nun können Sie mit der Entwicklung einer Mediendienste-Anwendung beginnen.    
   
<h2><a name="setup-account"></a><span class="short header">Verbinden mit Mediendiensten</span></h2> 

Beinahe alle Aktionen in der Mediendienste-Programmierung benötigen einen Verweis auf das Serverkontext-Objekt. Im Serverkontext erhalten Sie programmatischen Zugriff auf alle Mediendienste-Programmierobjekte.

Um einen Verweis auf den Serverkontext zu erhalten, erstellen Sie wie im folgenden Codebeispiel gezeigt eine neue Instanz des Kontexttyps. Übergeben Sie Mediendienste-Benutzername und Kontoschlüssel (beides haben Sie bei der Kontoeinrichtung erhalten) an den Konstruktor. 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


Es ist oft hilfreich, eine Variable vom Typ **CloudMediaContext** auf Modulebene zu definieren, um einen Verweis auf den Serverkontext zu speichern. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Media Services mit dem Media Services SDK für .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Die restlichen Codebeispiele in diesem Thema verwenden eine Variable namens **_context**, um auf den Serverkontext zu verweisen. 

<h2>Nächste Schritte</h2>
Sie haben Ihren Computer eingerichtet und eine Visual Studio-Lösung für die Programmierung von Mediendiensten erstellt und können nun das Thema [Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher][] lesen.
[Erstellen eines Media Services-Kontos]: ../media-services-create-account/
[Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher]: ../media-services-create-encrypted-asset-upload-storage/


<!--HONumber=42-->
