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
	ms.date="10/20/2015"
	ms.author="robmcm"/>

# Erstellen einer Java Web-App in Azure App Service

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Dieses Tutorial zeigt die Erstellung einer [Java-Web-App in Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) über das Azure-Vorschauportal Sie können eine Web-App-Vorlage aus dem Azure Marketplace auswählen oder eine generische Web-App erstellen und manuell für Java konfigurieren.

Wenn Sie eines dieser Verfahren verwenden möchten, um beispielsweise Ihren Anwendungscontainer anzupassen, finden Sie weitere Informationen unter [Hochladen einer benutzerdefinierten Java-Web-App in Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Tutorial auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren][] oder [sich für eine kostenlose Testversion registrieren][].
>
> Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen][]. Dort können Sie direkt eine kurzzeitige Start-Web-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Auswählen einer Web-App-Vorlage aus dem Azure Marketplace

Dieser Abschnitt zeigt, wie der Azure Marketplace zum Erstellen einer Java-Web-App eingesetzt wird.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.

2. Klicken Sie auf **Neu > Marketplace**.

	![](./media/web-sites-java-get-started/newmarketplace.png)

3. Klicken Sie auf **Web und mobil**.

	Sie müssen möglicherweise nach links scrollen, um das Blatt **Marketplace** anzuzeigen, auf dem Sie **Web und mobil** auswählen können.

4. Geben Sie im Suchfeld den Namen eines Java-Anwendungsservers ein, beispielsweise **Apache Tomcat** oder **Jetty**, und drücken Sie dann die EINGABETASTE.

5. Klicken Sie in den Suchergebnissen auf den Java-Anwendungsserver.

	![](./media/web-sites-java-get-started/webmobilejetty.png)

6. Klicken Sie im ersten **Apache Tomcat**- oder **Jetty**-Blatt auf **Erstellen**.

	![](./media/web-sites-java-get-started/jettyblade.png)

7. Geben Sie im nächsten **Apache Tomcat**- oder **Jetty**-Blatt im Feld **Web-App** einen Namen für die Web-App ein.

	Der Name muss innerhalb der Domäne "azurewebsites.net" eindeutig sein, da die URL der Web-App "{name}.azurewebsites.net" lauten wird. Wenn der eingegebene Name nicht eindeutig ist, wird im Textfeld ein rotes Ausrufezeichen angezeigt.

8. Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine neue.

	Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](../resource-group-portal.md).

9. Wählen Sie einen **App Service-Plan/-Standort** aus, oder erstellen Sie einen neuen Plan und Standort.

	Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

10. Klicken Sie auf **Erstellen**.

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	Azure beendet die Erstellung der neuen Web-App innerhalb kurzer Zeit, üblicherweise in weniger als einer Minute.

11. Klicken Sie auf **Web-Apps > {Ihre neue Web-App}**.

12. Klicken Sie auf die **URL**, um zur neuen Website zu wechseln.

	![](./media/web-sites-java-get-started/jettyurl.png)

	Wenn Sie Tomcat wählen, sehen Sie eine ähnliche Seite wie im folgenden Beispiel.

	![Web-App mit Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

	Wenn Sie Jetty wählen, wird eine Seite ähnlich der folgenden angezeigt.

	![Web-App mit Jetty](./media/web-sites-java-get-started/jetty.png)

Nachdem Sie nun die Web-App mit einem App-Container erstellt haben, finden Sie im Abschnitt [Nächste Schritte](#next-steps) weitere Informationen zum Hochladen Ihrer Anwendung in die Web-App.

## Erstellen und manuelles Konfigurieren einer Web-App für Java

In diesem Abschnitt wird gezeigt, wie Sie eine Web-App erstellen und manuell für Java konfigurieren.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.

2. Klicken Sie auf **Neu > Web und mobil**.


3. Klicken Sie auf **Web-App**.

4. Geben Sie im Feld **Web-App** einen Namen für die Web-App ein.

	Der Name muss innerhalb der Domäne "azurewebsites.net" eindeutig sein, da die URL der Web-App "{name}.azurewebsites.net" lauten wird. Wenn der eingegebene Name nicht eindeutig ist, wird im Textfeld ein rotes Ausrufezeichen angezeigt.

5. Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine neue.

	Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](../resource-group-portal.md).

6. Wählen Sie einen **App Service-Plan/-Standort** aus, oder erstellen Sie einen neuen Plan und Standort.

	Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

7. Klicken Sie auf **Erstellen**.
 
8. Nachdem die Web-App erstellt wurde, klicken Sie auf **Web-Apps > {Ihre Web-App}**.
 
9. Klicken Sie auf dem Blatt **Web-App** auf **Einstellungen**.

10. Klicken Sie auf **Anwendungseinstellungen**.

11. Klicken Sie auf die gewünschte **Java-Version**.

12. Wählen Sie den gewünschten **Webcontainer** aus.

13. Klicken Sie auf **Speichern**.

	Nach einem kurzen Augenblick ist Ihre Web-App Java-basiert.

14. Klicken Sie auf **Web-Apps > {Ihre neue Web-App}**.

15. Klicken Sie auf die **URL**, um zur neuen Website zu wechseln.

	Diese Webseite bestätigt, dass Sie eine auf Java basierende Web-App erstellt haben.

## Nächste Schritte

Sie führen nun Ihren Java-Anwendungsserver in Ihrer Java-Web-App in Azure App Service aus. Informationen zum Bereitstellen Ihres eigenen Codes für die Web-App finden Sie unter [Hinzufügen einer Anwendung oder Webseite zu Ihrer Java-Web-App](web-sites-java-add-app.md).

Weitere Informationen zum Entwickeln von Java-Anwendungen in Azure finden Sie im [Java Developer Center](/develop/java/).

<!-- External Links -->
[Ihre MSDN-Abonnentenvorteile aktivieren]: http://go.microsoft.com/fwlink/?LinkId=623901
[sich für eine kostenlose Testversion registrieren]: http://go.microsoft.com/fwlink/?LinkId=623901

[Azure App Service-App erstellen]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=Nov15_HO1-->