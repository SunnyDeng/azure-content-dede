<properties 
   pageTitle="Debuggen von Azure Cloud Services | Microsoft Azure"
   description="Debuggen von Azure Cloud Services"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />

# Debuggen von Clouddiensten

Sie können verschiedene Ansätze verwenden, um eine Azure-Anwendung zu debuggen, indem Sie die Azure-Tools für Microsoft Visual Studio und das Azure SDK einsetzen:

- Sie können eine Azure-Anwendung mit Visual Studio so wie eine beliebige Visual C#- oder Visual Basic-Anwendung bei der Entwicklung debuggen. Weitere Informationen finden Sie unter [Debuggen des Clouddiensts auf dem lokalen Computer](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Sie können die Azure-Diagnose verwenden, um ausführliche Informationen zu Code, der in Rollen ausgeführt wird, zu protokollieren, unabhängig davon, ob die Rollen in der Entwicklungsumgebung oder in Azure ausgeführt werden. Weitere Informationen finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Wenn Sie Visual Studio Enterprise zum Schreiben von Rollen verwenden, die auf .NET Framework 4 oder .NET Framework 4.5 ausgerichtet sind, können Sie IntelliTrace aktivieren, sobald Sie einen Clouddienst über Visual Studio bereitstellen. IntelliTrace bietet ein Protokoll, das Sie mit Visual Studio verwenden können, um die Anwendung so zu debuggen, als ob sie in Azure ausgeführt wird. Weitere Informationen finden Sie unter [Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace und Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Sie können das Remotedebuggen für Ihre Clouddienste zu dem Zeitpunkt aktivieren, zu dem Sie den Clouddienst über Visual Studio bereitstellen. Wenn Sie das Remotedebuggen für eine Bereitstellung aktivieren möchten, werden Dienste zum Remotedebuggen auf den virtuellen Computern installiert, auf denen jede Rolleninstanz ausgeführt wird. Diese Dienste, wie z. B. "msvsmon.exe", haben keine Auswirkungen auf die Leistung und führen nicht zu zusätzlichen Kosten. Weitere Informationen finden Sie unter [Debuggen eines Clouddiensts in Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

<!---HONumber=Oct15_HO3-->