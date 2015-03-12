<properties 
	pageTitle="Staging einer Cloud-Dienstbereitstellung (Node.js) - Azure" 
	description="Hier erfahren Sie, wie Sie eine Azure-Anwendung durch einen Austausch der virtuellen IP (VIP) zu einer Stagingumgebung und anschließend zu einer Produktionsumgebung bereitstellen." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Staging von Anwendungen in Azure

Gepackte Anwendungen können in der Stagingumgebung von Azure bereitgestellt werden, um diese zu testen, bevor sie in eine Produktionsumgebung mit Internetzugriff verschoben werden. Die Stagingumgebung entspricht genau der Produktionsumgebung, außer, dass Sie auf die Staginganwendung nur mit einer verborgenen URL zugreifen können, die von Azure generiert wird. Nachdem Sie die korrekte Funktionsweise Ihrer Anwendung geprüft haben, können Sie diese durch einen Austausch der virtuellen IP (VIP) in der Produktionsumgebung bereitstellen.

> [AZURE.NOTE] Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Cloud-Dienst gehostet werden.

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Staging einer Anwendung]
-   [Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs]

<h2><a id="step1"></a>Schritt 1: Staging einer Anwendung</h2>

Dieser Abschnitt beschreibt das Staging einer Anwendung mit der **Microsoft Azure PowerShell**.

1.  Übergeben Sie beim Veröffentlichen eines Diensts einfach den Parameter **-Slot** an das Cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Melden Sie sich am [Azure-Verwaltungsportal] an, und wählen Sie **Cloud Services** aus. Klicken Sie auf den Dienstnamen, nachdem der Cloud-Dienst erstellt und der Status der **Staging**-Spalte in **Running** geändert wurde.

	![portal displaying a running service][cloud-service]

3.  Wählen Sie das **Dashboard** aus und klicken Sie auf **Staging**.

	![cloud service dashboard][cloud-service-dashboard]

4. Notieren Sie sich den Wert rechts im Eintrag **Website-URL**. Der DNS-Name ist eine versteckte, von Azure generierte interne ID.

    ![site url][cloud-service-staging-url]

Mit der URL der Staging-Website können Sie nun prüfen, ob Ihre Anwendung in der Stagingumgebung korrekt funktioniert.

In Upgradeszenarien, in denen die Staginganwendung eine erweiterte Version der bereits in Produktion bereitgestellten Anwendung ist, können [Sie die Anwendung durch Austausch der VIPs in die Produktionsumgebung überführen][Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs].

<h2><a id="step2"></a>Schritt 2: Upgrade einer Anwendung in die Produktionsumgebung durch Austausch der VIPs</h2>

Nachdem Sie die erweiterte Version einer Anwendung in der Stagingumgebung geprüft haben, können Sie diese schnell und einfach in der Produktionsumgebung verfügbar machen, indem Sie die virtuellen IPs (VIPs) von Staging- und Produktionsumgebung austauschen.

> [AZURE.NOTE] Dieser Schritt geht davon aus, dass Sie bereits eine Anwendung in der Produktionsumgebung bereitgestellt haben und dass sich eine erweiterte Version der Anwendung in der Stagingumgebung befindet.

1.  Melden Sie sich am [Azure-Verwaltungsportal] an, klicken Sie auf **Cloud Services**, und wählen Sie dann den Dienstnamen aus.

2.  Wählen Sie im **Dashboard** **Staging** aus und klicken Sie am unteren Seitenrand auf **Swap**. Daraufhin öffnet sich der VIP Swap-Dialog.

    ![vip swap dialog][vip-swap-dialog]

3.  Prüfen Sie die Informationen und klicken Sie auf **OK**. Die beiden Bereitstellungen werden aktualisiert, und die Staging-Bereitstellung wechselt in die Produktionsumgebung und umgekehrt.

Sie haben nun eine Bereitstellung in Staging getestet und eine Produktionsbereitstellung durch den Austausch der VIPs durch eine Stagingbereitstellung ersetzt.

## Zusätzliche Ressourcen

- [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]
- [Übersicht über das Verwalten von Bereitstellungen in Azure]

  [Schritt 1: Staging einer Anwendung]: #step1
  [Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs]: #step2
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Übersicht über das Verwalten von Bereitstellungen in Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
