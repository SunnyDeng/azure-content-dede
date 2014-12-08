<properties urlDisplayName="Visual Studio 14 CTP" pageTitle="Installieren von Azure SDK 2.4 für Visual Studio "14" CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Installieren von Azure SDK 2.4 für Visual Studio "14" CTP

Folgen Sie für die Installation von Azure SDK 2.4 für Visual Studio "14" CTP diesen Schritten. Dieses Verfahren installiert SDKs, grundlegende sowie erweiterte Tools für die Azure-Entwicklung mit Visual Studio "14" CTP und ist nicht für den Einsatz mit anderen Version von Visual Studio gedacht.

**Hinweis**: Azure SDK 2.4 ist nicht kompatibel mit Visual Studio "14" CTP1.

Folgen Sie für die Installation von Azure SDK 2.4 für.NET diesen Schritten:

1. Installieren Sie das neueste [Visual Studio "14" CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776).

2. Installieren Sie jede Komponente des Azure SDK mithilfe der Links in der folgenden Liste, in dieser Reihenfolge. Wählen Sie die x86- oder x64-Version jeder der folgenden Komponenten.

       <ul>
        <li>Azure-Entwicklertools: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> oder <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Azure-Serveremulator: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> oder <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>Azure-Clientbibliotheken: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> oder <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>Speicheremulator: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Wenn Sie eine Warnung bezüglich die lokale SQLl-Datenbak erhalten, installieren Sie SQL Server LocalDB 11.0 von <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">dieser Adresse</a> für x86 oder <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">dieser Adresse</a> für x64.</li><li> Azure Tools für Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Bekannte Probleme

1. Wenn Sie Visual Studio "14" CTP2 auf einem Computer installieren, auf dem Visual Studio 2013 installiert ist, können Sie keine mobilen Dienste in Visual Studio "14" CTP2 starten. Dieses Problem beheben Sie, indem Sie eine   Referenz im Projekt mit den mobilen Diensten hinzufügen:

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Remotdebugging für Azure Websites und Mobile Services funktioniert nicht in in Visual Studio "14" CTP2.

## Versionshinweise

Lesen Sie die [Versionenhinweise](http://go.microsoft.com/fwlink/?LinkId=507517) für Azure SDK 2.4.
