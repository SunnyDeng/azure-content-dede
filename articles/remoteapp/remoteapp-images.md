<properties
    pageTitle="Was enthalten Azure RemoteApp-Vorlagenimages?"
    description="Erfahren Sie mehr über die in Azure RemoteApp enthaltenen Vorlagenimages."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2015" 
    ms.author="elizapo" />

# Was enthalten Azure RemoteApp-Vorlagenimages?

Ihr Azure RemoteApp-Abonnement beinhaltet drei Vorlagenimages:


- Windows Server 2012
- Microsoft Office 365 ProPlus (Office 365-Abonnement erforderlich)
- Microsoft Office 2013 Professional Plus (nur Testversion)

> [AZURE.IMPORTANT]Ihr Abonnement von Azure RemoteApp gewährt Ihnen Zugriff auf die Software in den Bildern, mit Ausnahme von Office 365 ProPlus, wozu ein eigenes Abonnement benötigt wird und Office 2013, das in der Produktion nicht verwendet werden kann. Dies bedeutet, dass Sie die Programme oder Apps auf den Vorlagenimages für Ihre Benutzer freigeben können. Wenn Sie eine Sammlung erstellen, die das Windows Server 2012 R2-Image verwendet, können Sie z. B. System Center Endpoint Protection für den Zugriff auf über RemoteApp veröffentlichen.
> 
> Weitere Informationen finden Sie unter [Details zur Lizenzierung in RemoteApp](remoteapp-licensing.md). Informationen zur Office-Lizenzierung finden Sie unter [Verwenden von Office mit Azure RemoteApp](remoteapp-o365.md).

Im Folgenden erhalten Sie ausführliche Informationen zum Inhalt der einzelnen Images.

## Windows Server 2012 R2 ("einfaches Image")
Dieses Image basiert auf dem Betriebssystem Microsoft Windows Server 2012 R2 Datacenter und weist die folgenden Rollen und Features auf, die zum Erfüllen der Anforderungen an Azure RemoteApp-Vorlagenimages installiert wurden:


- .NET Framework 4.5, 3.5.1 und 3.5
- Desktopdarstellung
- Freihand- und Handschriftdienste
- Media Foundation
- Remotedesktop-Sitzungshost
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64-Unterstützung

Auf diesem Image sind außerdem die folgenden Anwendungen installiert:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (Abonnement erforderlich)
Office 365 ist die am häufigsten angeforderte Anwendung; daher haben wir für Sie ein "benutzerdefiniertes" Image erstellt, mit dem Sie arbeiten können.

Dieses Image ist eine Erweiterung des einfachen Images und enthält neben den Komponenten, die für das Windows Server 2012 R2-Image beschrieben wurden, die folgenden Komponenten von Microsoft Office 365 ProPlus:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Word
- Microsoft Office-Korrekturhilfen

Das Image umfasst außerdem Visio Pro und Project Pro.

Sowie die folgenden Anwendungen:

- SQL Native Client
- ODBC-Treiber
- SQL Server Data Mining-Client
- MasterDataServices-Client
- Microsoft Publisher
- PowerQuery
- PowerMap


Die vollständige Funktionalität der Office 365 ProPlus-Anwendungen ist nur für Benutzer mit einem Office 365 ProPlus-Plan verfügbar. Weitere Informationen zu Office 365-Abonnementplänen finden Sie unter [Office 365-Servicepläne](http://technet.microsoft.com/library/office-365-plan-options.aspx). Haben Sie noch Fragen? Prüfen Sie die Informationen unter [Office 365 + RemoteApp](remoteapp-o365.md). Lesen Sie auch den neuen Artikel [Verwenden Ihres Office 365-Abonnements mit Azure RemoteApp](remoteapp-officesubscription.md).

Beachten Sie, dass Sie Office 365 ProPlus, Visio Pro und Project Pro separat lizenzieren müssen – sie verfügen jeweils über eine eigene Lizenz.

## Microsoft Office 2013 Professional Plus (nur Testversion)
Während des kostenlosen Testzeitraums können Sie den Dienst mit dem Office 2013-Image testen.

Dieses Image ist eine Erweiterung des einfachen Images und enthält neben den Komponenten, die für das Windows Server 2012 R2-Image beschrieben wurden, die folgenden Komponenten von Microsoft Office 2013 Professional Plus:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office-Korrekturhilfen

> [AZURE.IMPORTANT]**Rechtliche Hinweise**: Dieses Image enthält keine Microsoft Office-Lizenz und *kann nicht für die Produktion verwendet werden*. Das Image von Office 2013 Professional Plus dient nur zu Testzwecken. Wenn Sie Office-Apps in Azure RemoteApp für die Produktion verwenden möchten, müssen Sie das Office 365 ProPlus-Image verwenden. Weitere Informationen zur Office-Lizenzierung finden Sie unter [Verwenden von Office 365 mit Azure RemoteApp](remoteapp-o365.md)
 

<!---HONumber=Oct15_HO3-->