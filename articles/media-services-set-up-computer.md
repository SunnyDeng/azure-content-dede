<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="setup-dev"> </a><span class="short header">Einrichten Ihres Computers für die Mediendienste-Entwicklung</span>

Dieser Abschnitt enthält allgemeine Voraussetzungen für die Mediendienste-Entwicklung mithilfe des Mediendienste-SDK für .NET. Außerdem wird die Erstellung einer Visual Studio-Anwendung für die Entwicklung mit dem Mediendienste-SDK beschrieben.

### Voraussetzungen

-   Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos][].
-   Betriebssysteme: Windows 7, Windows 2008 R2 oder Windows 8.
-   .NET Framework 4.5 oder .NET Framework 4.
-   Visual Studio 2012 oder Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express).
-   Verwenden Sie das [windowsazure.mediaservices Nuget][]-Paket, um das Azure SDK für .NET zu installieren. Das folgende Beispiel zeigt, wie Sie das Azure-SDK mit **Nuget** installieren können:

## <a name="setup-account"></a><span class="short header">Einrichten eines Azure-Kontos für Media Services</span>

Verwenden Sie das Azure-Verwaltungsportal, um Ihr Mediendienste-Konto einzurichten (empfohlen). Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos][]. Nach der Erstellung des Kontos im Verwaltungsportal können Sie Ihren Computer für die Mediendienste-Entwicklung einrichten.

### Erstellen einer Anwendung in Visual Studio

Dieser Abschnitt beschreibt, wie Sie ein Projekt in Visual Studio erstellen und für die Mediendienste-Entwicklung einrichten. Es handelt sich um eine C# Windows-Konsolenanwendung, allerdings gelten für andere Typen von Projekten für Mediendienst-Anwendungen (z. B. eine Windows Forms-Anwendung oder ASP.NET-Webanwendung) dieselben Schritte.

1.  Erstellen Sie eine neue C#-**Konsolenanwendung** in Visual Studio 2012 oder Visual Studio 2010 SP1. Geben Sie **Name**, **Ort** und **Lösungsname** ein und klicken Sie auf **OK**.
2.  Fügen Sie einen Verweis auf die Assembly **System.Configuration** hinzu. Um einen Verweis auf **System.Configuration** hinzuzufügen, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise** und wählen Sie **Verweis hinzufügen...** aus. Wählen Sie im Dialogfeld **Verweise verwalten** den Eintrag **System.Configuration** aus und klicken Sie auf **OK**.
3.  Hinzufügen von Verweisen auf das **Azure-SDK für .NET.** (Microsoft.WindowsAzure.StorageClient.dll), **Azure Mediendienste-SDK für .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) und **WCF Data Services 5.0 für OData V3** (Microsoft.Data.OData.dll) mithilfe des [windowsazure.mediaservices Nuget][]-Pakets.

    Um Verweise mit Nuget hinzuzufügen, gehen Sie wie folgt vor. Klicken Sie im **Hauptmenü** in Visual Studio auf **EXTRAS** -\> **Bibliothekspaket-Manager** -\> **Paket-Manager-Konsole**. Geben Sie im Konsolenfenster **Install-Package windowsazure.mediaservices** und drücken Sie die **Eingabetaste**.

4.  Überschreiben Sie die existierenden using-Anweisungen am Anfang von Program.cs durch den folgenden Code.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

Nun können Sie mit der Entwicklung einer Mediendienste-Anwendung beginnen.

## <a name="setup-account"></a><span class="short header">Verbinden mit Mediendiensten</span>

Beinahe alle Aktionen in der Mediendienste-Programmierung benötigen einen Verweis auf das Serverkontext-Objekt. Im Serverkontext erhalten Sie programmatischen Zugriff auf alle Mediendienste-Programmierobjekte.

Um einen Verweis auf den Serverkontext zu erhalten, geben Sie wie im folgenden Codebeispiel gezeigt eine neue Instanz des Kontexttyps zurück. Übergeben Sie Mediendienste-Benutzername und Kontoschlüssel (beides haben Sie bei der Kontoeinrichtung erhalten) an den Konstruktor.

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);
    } 

Es ist oft hilfreich, eine Variable vom Typ **CloudMediaContext** auf Modulebene zu definieren, um einen Verweis auf den Serverkontext z. B. durch Aufrufen von **GetContext** zu speichern. Die restlichen Codebeispiele in diesem Thema verwenden eine Variable namens \*\*\_context\*\*, um auf den Serverkontext zu verweisen.

## Nächste Schritte

Sie haben Ihren Computer eingerichtet und eine Visual Studio-Lösung für die Programmierung von Media Services erstellt und können nun mit dem Thema [Erstellen eines unverschlüsselten Medienobjekts und Hochladen in den Speicher][] fortfahren.

   

  [Vorgehensweise: Erstellen eines Mediendienstekontos]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
  [Erstellen eines Media Services-Kontos]: http://go.microsoft.com/fwlink/?linkid=256662
  [Erstellen eines unverschlüsselten Medienobjekts und Hochladen in den Speicher]:<http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409>