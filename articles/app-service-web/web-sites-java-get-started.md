<properties
	pageTitle="Erstellen einer Java-Web-App in Azure App Service | Microsoft Azure"
	description="In diesem Tutorial wird gezeigt, wie Sie eine Java-Web-App in Azure App Service bereitstellen."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# Erstellen einer Java Web-App in Azure App Service

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

In diesem Lernprogramm erfahren Sie, wie Sie eine Web-App in Microsoft Azure mithilfe von Java erstellen, indem Sie entweder den Azure Marketplace oder die Konfigurationsoberfläche der [Web-Apps-Funktion in Azure App Service][] verwenden.

Wenn Sie eines dieser Verfahren verwenden möchten, um beispielsweise Ihren Anwendungscontainer anzupassen, finden Sie weitere Informationen unter [Hochladen einer benutzerdefinierten Java-Web-App in Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Tutorial auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren][] oder [sich für eine kostenlose Testversion registrieren][].

Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen][]. Dort können Sie direkt eine kurzzeitige Start-Web-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Erstellen einer Java-Web-App mithilfe von Azure Marketplace

Hier erfahren Sie, wie Sie Azure Marketplace verwenden, um einen Java-Anwendungscontainer für Ihre Web-App auszuwählen, entweder Apache Tomcat oder Jetty.

Im Folgenden wird gezeigt, wie eine Web-App aussieht, die mithilfe von Tomcat über den Azure Marketplace erstellt wurde:

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

Im Folgenden wird gezeigt, wie eine Web-App aussieht, die mithilfe von Jetty über den Azure Marketplace erstellt wurde:

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. Melden Sie sich beim [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) an.
2. Klicken Sie unten links auf der Seite auf **Neu**.
3. Klicken Sie auf das Blatt **Web + Mobile**.
4. Klicken Sie auf **Azure Marketplace** unten im Blatt **Web + Mobile**.
5. Klicken Sie auf **Web**.
6. Oben auf der Seite **Web** befindet sich ein Textfeld für die Suche. Geben Sie in das Textfeld den gewünschten Java-Anwendungsserver ein, wie z. B. **Apache Tomcat** oder **Jetty**.
4. Klicken Sie auf den gewünschten Java-Anwendungsserver.
5. Klicken Sie auf **Erstellen**.
6. Legen Sie den URL-Namen fest.
6. Wählen Sie eine Region aus. Wählen Sie z. B. **USA, Westen**.
7. Klicken Sie auf **Erstellen**.

Nach einem kurzen Augenblick wird Ihre Web-App erstellt. Zum Anzeigen der Web-App im Azure-Portal klicken Sie im Blatt **Web-Apps** auf die Web-App und dann auf die URL für die Web-App.

Nachdem Sie nun die Web-App mit einem App-Container erstellt haben, finden Sie im Abschnitt **Nächste Schritte** weitere Informationen zum Hochladen Ihrer Anwendung in die Web-App.

## Erstellen einer Java-Web-App über die Azure-Konfigurationsbenutzeroberfläche

Hier erfahren Sie, wie Sie die Azure-Konfigurationsbenutzeroberfläche verwenden, um einen Java-Anwendungscontainer für Ihre Web-App auszuwählen, entweder Apache Tomcat oder Jetty.

1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie unten links auf der Seite auf **Neu**.
3. Klicken Sie auf das Blatt **Web + Mobile**.
4. Klicken Sie auf **Azure Marketplace** unten im Blatt **Web + Mobile**.
5. Klicken Sie auf **Web**.
6. Klicken Sie auf **Web-App**.
7. Klicken Sie auf **Erstellen**.
8. Legen Sie den URL-Namen fest.
9. Wählen Sie eine Region aus. Wählen Sie z. B. **USA, Westen**.
10. Klicken Sie auf **Erstellen**.
11. Nachdem die Web-App erstellt wurde, klicken Sie auf **Alle Einstellungen**.
12. Klicken Sie auf **Anwendungseinstellungen**.
13. Klicken Sie auf die gewünschte Java-Version.
14. Die Optionen für den Web-Container werden angezeigt, beispielsweise Tomcat und Jetty. Wählen Sie den gewünschten **Webcontainer** aus.
15. Klicken Sie auf **Speichern**.

Nach einem kurzen Augenblick ist Ihre Web-App Java-basiert. Klicken Sie auf die zugehörige URL, um dies zu überprüfen. Beachten Sie, dass auf der Seite eine Nachricht mit der Information angezeigt wird, dass die neue Web-App eine Java-basierte Web-App ist.

Nachdem Sie nun die Web-App mit einem App-Container erstellt haben, finden Sie im Abschnitt **Nächste Schritte** weitere Informationen zum Hochladen Ihrer Anwendung in die Web-App.

## Nächste Schritte

Sie führen nun Ihren Java-Anwendungsserver als Ihre Java-Web-App auf Azure aus. Informationen zum Hinzufügen Ihrer eigenen Anwendung oder Webseite finden Sie unter [Hinzufügen einer Anwendung oder Webseite zu Ihrer Java-Web-App](web-sites-java-add-app.md).

Weitere Informationen finden Sie im [Java Developer Center](/develop/java/).

## Änderungen

* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter [Azure App Service und vorhandene Azure-Dienste][].
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal][].

<!-- External Links -->
[Ihre MSDN-Abonnentenvorteile aktivieren]: http://go.microsoft.com/fwlink/?LinkId=623901
[sich für eine kostenlose Testversion registrieren]: http://go.microsoft.com/fwlink/?LinkId=623901
[Web-Apps-Funktion in Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure App Service-App erstellen]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure App Service und vorhandene Azure-Dienste]: http://go.microsoft.com/fwlink/?LinkId=529714
[Referenz zur Navigation im Azure-Portal]: http://go.microsoft.com/fwlink/?LinkId=529715

<!---HONumber=Sept15_HO4-->