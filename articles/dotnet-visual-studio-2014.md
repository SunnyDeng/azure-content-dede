<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

## Installieren von Azure SDK 2.4 für Visual Studio „14“ CTP

Folgen Sie für die Installation von Azure SDK 2.4 für Visual Studio „14“ CTP diesen Schritten. Dieses Verfahren installiert SDKs, grundlegende sowie erweiterte Tools für die Azure-Entwicklung mit Visual Studio „14“ CTP und ist nicht für den Einsatz mit anderen Version von Visual Studio gedacht.

**Hinweis**: Azure SDK 2.4 ist nicht kompatibel mit Visual Studio „14“ CTP1.

Folgen Sie für die Installation von Azure SDK 2.4 für.NET diesen Schritten:

1.  Installieren Sie das neueste [Visual Studio „14“ CTP][Visual Studio „14“ CTP].

2.  Installieren Sie jede Komponente des Azure SDK mithilfe der Links in der folgenden Liste, in dieser Reihenfolge. Wählen Sie die x86- oder x64-Version jeder der folgenden Komponenten.

    -   Azure-Entwicklertools: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] oder [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    -   Azure-Serveremulator: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] oder [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    -   Azure-Clientbibliotheken: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] oder [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].
    -   Speicheremulator: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. Wenn Sie eine Warnung hinsichtlich lokaler SQL-Datenbanken erhalten, installieren Sie SQL Server LocalDB 11.0 von [hier][hier] für x86 oder von [hier][1] für x64.
    -   Azure Tools für Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Bekannte Probleme

1.  Wenn Sie Visual Studio „14“ CTP2 auf einem Computer installieren, auf dem Visual Studio 2013 installiert ist, können Sie keine mobilen Dienste in Visual Studio „14“ CTP2 starten. Dieses Problem beheben Sie, indem Sie eine Referenz zu den folgenden Assemblys im Projekt mit den mobilen Diensten hinzufügen:

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Remotdebugging für Azure Websites und Mobile Services funktioniert nicht in in Visual Studio „14“ CTP2.

## Versionshinweise

Lesen Sie die [Versionshinweise][Versionshinweise] für Azure SDK 2.4.

  [Visual Studio „14“ CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [hier]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [Versionshinweise]: http://go.microsoft.com/fwlink/?LinkId=507517
