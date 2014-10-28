<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Staging an Application in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Staging von Anwendungen in Azure

Gepackte Anwendungen können in der Stagingumgebung von Azure
bereitgestellt werden, um diese zu testen, bevor sie in die
Produktionsumgebung mit Internetzugriff verschoben werden. Die
Stagingumgebung entspricht genau der Produktionsumgebung, mit der Ausnahme,
dass Sie auf die Staginganwendung nur mit einer verborgenen URL zugreifen
können, die von Azure generiert wird. Nachdem Sie die korrekte
Funktionsweise Ihrer Anwendung geprüft haben, können Sie diese durch
einen Austausch der virtuellen IP (VIP) in der Produktionsumgebung bereitstellen.

<div class="dev-callout">
    <b>Hinweis</b>
    <p>Die Schritte in diesem Beitrag gelten nur f&uuml;r Node-Anwendungen, die als Azure-Clouddienst gehostet werden.</p>
</div>

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Staging einer Anwendung][Schritt 1: Staging einer Anwendung]
-   [Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs][Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs]

## <span id="step1"></span></a>Schritt 1: Staging einer Anwendung

Dieser Abschnitt beschreibt das Staging einer Anwendung mit der **Windows Azure
PowerShell**.

1.  Übergeben Sie beim Veröffentlichen eines Diensts einfach den
    Parameter **-Slot** an das Cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und wählen Sie **Cloud Services** aus. Klicken Sie auf den Dienstnamen, nachdem der Cloud-Dienst erstellt und der Status der **Staging**-Spalte zu **Running** geändert wurde.

    ![Portal mit einem laufenden Dienst][Portal mit einem laufenden Dienst]

3.  Wählen Sie das **Dashboard** aus und klicken Sie auf **Staging**.

    ![Cloud-Dienst-Dashboard][Cloud-Dienst-Dashboard]

4.  Kopieren Sie den Wert unter **Site URL** auf der rechten Seite. Der DNS-Name ist eine versteckte, von Azure generierte interne ID.

    ![Website-URL][Website-URL]

Mit der URL der Staging-Website können Sie nun prüfen, ob Ihre Anwendung in der Stagingumgebung korrekt funktioniert.

In Upgradeszenarien, in denen die Staginganwendung eine erweiterte
Version der bereits in Produktion bereitgestellten Anwendung ist, können
Sie [die Anwendung durch Austausch der VIPs in die Produktionsumgebung
überführen][Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs].

## <span id="step2"></span></a>Schritt 2: Upgrade einer Anwendung in die Produktionsumgebung durch Austausch der VIPs

Nachdem Sie die erweiterte Version einer Anwendung in der
Stagingumgebung geprüft haben, können Sie diese schnell und einfach in der
Produktionsumgebung verfügbar machen, indem Sie die
virtuellen IPs (VIPs) von Staging- und Produktionsumgebung austauschen.

<div class="dev-callout">
<b>Hinweis</b>
<p>In diesem Schritt wird davon ausgegangen, dass Sie bereits eine Anwendung in der
Produktionsumgebung bereitgestellt haben und dass sich eine erweiterte Version
der Anwendung in der Stagingumgebung befindet.</p>
</div>

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal]
     an, klicken Sie auf **Cloud Services**, und wählen Sie den Dienstnamen aus.

2.  Wählen Sie im **Dashboard** **Staging** aus und klicken Sie am unteren Seitenrand auf **Swap**. Daraufhin wird das
    VIP-Swap-Dialogfeld geöffnet.

    ![VIP-Swap-Dialogfeld][VIP-Swap-Dialogfeld]

3.  Prüfen Sie die Informationen und klicken Sie auf **OK**. Die beiden Bereitstellungen
    werden aktualisiert, und die Stagingbereitstellung wechselt in die
    Produktionsumgebung und umgekehrt.

Sie haben nun eine Bereitstellung in Staging getestet und eine
Produktionsbereitstellung durch den Austausch der VIPs durch eine Stagingbereitstellung ersetzt.

## Zusätzliche Ressourcen

-   [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure][Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]
-   [Übersicht über das Verwalten von Bereitstellungen in Azure][Übersicht über das Verwalten von Bereitstellungen in Azure]

  [Schritt 1: Staging einer Anwendung]: #step1
  [Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs]: #step2
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Portal mit einem laufenden Dienst]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [Cloud-Dienst-Dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [Website-URL]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [VIP-Swap-Dialogfeld]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/ee517253.aspx
  [Übersicht über das Verwalten von Bereitstellungen in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/hh386336.aspx
