<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="Node.js" title="Staging an Application in Azure" authors="" solutions="" manager="" editor="" />

Staging von Anwendungen in Azure
================================

Gepackte Anwendungen können in der Stagingumgebung von Azure bereitgestellt werden, um diese zu testen, bevor sie in eine Produktionsumgebung mit Internetzugriff verschoben werden. Die Stagingumgebung entspricht genau der Produktionsumgebung, außer, dass Sie auf die Staginganwendung nur mit einer verborgenen URL zugreifen können, die von Azure generiert wird. Nachdem Sie die korrekte Funktionsweise Ihrer Anwendung geprüft haben, können Sie diese durch einen Austausch der virtuellen IP (VIP) in der Produktionsumgebung bereitstellen.

**Hinweis**

Die Schritte in diesem Beitrag gelten nur für Node-Anwendungen, die als Azure-Clouddienst gehostet werden.

Die vorliegende Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Staging einer Anwendung](#step1)
-   [Schritt 2: Bereitstellen eine Anwendung in der Produktionsumgebung durch Austausch der VIPs](#step2)

Schritt 1: Staging einer Anwendung
----------------------------------

Dieser Abschnitt beschreibt das Staging einer Anwendung mit der **Azure PowerShell**.

1.  Übergeben Sie beim Veröffentlichen eines Dienstes einfach den **-Slot**-Parameter an das **Publish-AzureServiceProject**-Cmdlet.

    **Publish-AzureServiceProject -Slot staging**

2.  Melden Sie sich am [Azure-Verwaltungsportal](http://manage.windowsazure.com) an, und wählen Sie **Cloud Services** aus. Klicken Sie auf den Dienstnamen, nachdem der Cloud-Dienst erstellt und der Status der **Staging**-Spalte zu **Running** geändert wurde.

    ![Portal mit einem laufenden Dienst](./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png)

3.  Wählen Sie das **Dashboard** aus und klicken Sie auf **Staging**.

    ![Cloud-Dienst-Dashboard](./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png)

4.  Kopieren Sie den Wert unter **Site URL** auf der rechten Seite. Der DNS-Name ist eine versteckte, von Azure generierte interne ID.

    ![Website-URL](./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png)

Mit der URL der Staging-Website können Sie nun prüfen, ob Ihre Anwendung in der Stagingumgebung korrekt funktioniert.

In Upgrade-Szenarien, in denen die Staging-Anwendung eine erweiterte Version der bereits in Produktion bereitgestellten Anwendung ist, können Sie [die Anwendung durch Austausch der VIPs in die Produktionsumgebung überführen](#step2).

Schritt 2: Upgrade einer Anwendung in die Produktionsumgebung durch Austausch der VIPs
--------------------------------------------------------------------------------------

Nachdem Sie die erweiterte Version einer Anwendung in der Stagingumgebung geprüft haben, können Sie diese schnell und einfach in der Produktionsumgebung verfügbar machen, indem Sie die virtuellen IPs (VIPs) von Staging- und Produktionsumgebung austauschen.

**Hinweis**

Dieser Schritt geht davon aus, dass Sie bereits eine Anwendung in der Produktionsumgebung bereitgestellt haben und dass sich eine erweiterte Version der Anwendung in der Stagingumgebung befindet.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an, klicken Sie auf **Mobile Services** und wählen Sie den Dienstnamen aus.

2.  Wählen Sie im **Dashboard** **Staging** aus und klicken Sie am unteren Seitenrand auf **Swap**. Daraufhin öffnet sich der VIP Swap-Dialog.

    ![VIP Swap-Dialog](./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png)

3.  Prüfen Sie die Informationen und klicken Sie auf **OK**. Die beiden Bereitstellungen werden aktualisiert, und die Staging-Bereitstellung wechselt in die Produktionsumgebung und umgekehrt.

Sie haben nun eine Bereitstellung in Staging getestet und eine Produktionsbereitstellung durch den Austausch der VIPs durch eine Stagingbereitstellung ersetzt.

Zusätzliche Ressourcen
----------------------

-   [Bereitstellen eines Dienstupgrades in der Produktionsumgebung durch Austausch der VIPs in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee517253.aspx)
-   [Übersicht über das Verwalten von Bereitstellungen in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh386336.aspx)


