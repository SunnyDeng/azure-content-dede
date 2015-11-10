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

Dieses Tutorial veranschaulicht das Erstellen einer [Java Web-App in Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) über das [Azure-Vorschauportal](https://portal.azure.com/). Das Azure-Vorschauportal ist eine Weboberfläche, über die Sie Azure-Ressourcen verwalten können.

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren][] oder [sich für eine kostenlose Testversion registrieren][].
>
> Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen][]. Dort können Sie direkt eine kurzzeitige Start-Web-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Optionen für Java-Anwendungen

Es gibt verschiedene Möglichkeiten, wie Sie eine Java-Anwendung in einer App Service-Web-App einrichten können.

1. Verwenden einer Vorlage aus dem Azure Marketplace

	Der Azure Marketplace bietet Vorlagen, die automatisch Java-Web-Apps mit Tomcat- oder Jetty-Webcontainern erstellen und konfigurieren. Die durch die Vorlagen eingerichteten Webcontainer sind konfigurierbar. Weitere Informationen finden Sie in diesem Tutorial im Abschnitt [Verwenden einer Java-Vorlage aus dem Azure Marketplace](#marketplace).
 
1. Erstellen einer App und anschließendes Konfigurieren der **Anwendungseinstellungen**

	App Service bietet verschiedene Tomcat- und Jetty-Versionen mit Standardkonfigurationen. Wenn die zu hostende Anwendung mit einer der integrierten Versionen funktioniert, ist diese Methode zum Einrichten eines Webcontainers am einfachsten. Allerdings fehlen hier die Konfigurationsmöglichkeiten der anderen Methoden. Bei dieser Methode erstellen Sie eine App im Portal und öffnen dann das Blatt **Anwendungseinstellungen**, um Ihre Version von Java und den gewünschten Java-Webcontainer auszuwählen. Wenn Sie diese Methode nutzen, wird die App auf der lokalen Festplatte ausgeführt, die vom Worker zum Hosten der App verwendet wird. Dadurch wird kein Speicherplatz vom Mandanten beansprucht. Beim Einsatz dieses Modells fehlt Ihnen der Zugriff, um Dateien in diesem Teil des Dateisystems zu bearbeiten. Das heißt, Sie können z. B. nicht die Datei *server.xml* konfigurieren oder Bibliotheksdateien im Ordner */lib* ablegen. Weitere Informationen finden Sie weiter unten in diesem Tutorial unter [Erstellen und Konfigurieren einer Java-Web-App](#appsettings).
  
3. Erstellen einer App und manuelles Kopieren und Bearbeiten der Konfigurationsdateien

	Unter Umständen möchten Sie eine benutzerdefinierte Java-Anwendung hosten, die sich in keinem der von App Service angebotenen Webcontainern bereitstellen lässt. Dies kann z. B. aus folgenden Gründen der Fall sein:
	
	* Ihre Java-Anwendung erfordert eine Version von Tomcat oder Jetty, die nicht direkt von App Service unterstützt oder nicht im Katalog angeboten wird.
	* Ihre Java-Anwendung nimmt HTTP-Anforderungen an und lässt sich nicht als WAR (Web Application Archive) in einem bereits vorhandenen Webcontainer bereitstellen.
	* Sie möchten den Webcontainer von Grund auf selbst konfigurieren. 
	* Sie möchten eine Version von Java verwenden, die nicht von App Service unterstützt wird, und Sie möchten sie selbst hochladen.

	Für Fälle wie diese können Sie im Portal eine App erstellen und anschließend die geeigneten Laufzeitdateien manuell bereitstellen. Dabei werden die Dateien auf die Speicherplatzkontingente für Ihren App Service-Plan angerechnet. Weitere Informationen erhalten Sie unter [Hochladen einer benutzerdefinierten Java-Web-App in Azure](https://acom-sandbox.azurewebsites.net/de-DE/documentation/articles/web-sites-java-custom-upload/).

## <a name="marketplace"></a> Verwenden einer Java-Vorlage aus dem Azure Marketplace

Dieser Abschnitt zeigt, wie der Azure Marketplace zum Erstellen einer Java-Web-App eingesetzt wird. Derselbe allgemeine Ablauf kann auch verwendet werden, um eine Java-basierte mobile App oder eine API-App zu erstellen.

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

8. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.

	Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](../resource-group-portal.md).

9. Wählen Sie einen **App Service-Plan/Standort** aus, oder erstellen Sie einen neuen Plan und Standort.

	Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

10. Klicken Sie auf **Erstellen**.

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	Azure beendet die Erstellung der neuen Web-App innerhalb kurzer Zeit, üblicherweise in weniger als einer Minute.

11. Klicken Sie auf **Web-Apps > {Ihre neue Web-App}**.

12. Klicken Sie auf die **URL**, um zur neuen Website zu wechseln.

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat wird mit einer Reihe von Standardseiten ausgeliefert. Wenn Sie Tomcat wählen, wird daher eine ähnliche Seite wie im folgenden Beispiel angezeigt.

	![Web-App mit Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

	Wenn Sie Jetty wählen, wird eine Seite ähnlich der folgenden angezeigt. Für Jetty ist keine Standardseite eingerichtet. Daher wird dieselbe JSP wiederverwendet, die für eine leere Java-Site verwendet wird.

	![Web-App mit Jetty](./media/web-sites-java-get-started/jetty.png)

Nachdem Sie nun die Web-App mit einem App-Container erstellt haben, finden Sie im Abschnitt [Nächste Schritte](#next-steps) Informationen zum Hochladen Ihrer Anwendung in die Web-App.

## <a name="portal"></a> Erstellen und Konfigurieren einer Java-Web-App

In diesem Abschnitt wird gezeigt, wie Sie eine Web-App erstellen und mit dem Blatt **Anwendungseinstellungen** des Portals für Java konfigurieren.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.

2. Klicken Sie auf **Neu > Web und mobil > Web-App**.

	![](./media/web-sites-java-get-started/newwebapp.png)

4. Geben Sie im Feld **Web-App** einen Namen für die Web-App ein.

	Der Name muss innerhalb der Domäne "azurewebsites.net" eindeutig sein, da die URL der Web-App "{name}.azurewebsites.net" lauten wird. Wenn der eingegebene Name nicht eindeutig ist, wird im Textfeld ein rotes Ausrufezeichen angezeigt.

5. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.

	Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](../resource-group-portal.md).

6. Wählen Sie einen **App Service-Plan/Standort** aus, oder erstellen Sie einen neuen Plan und Standort.

	Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../azure-web-sites-web-hosting-plans-in-depth-overview.md).

7. Klicken Sie auf **Erstellen**.

	![](./media/web-sites-java-get-started/newwebapp2.png)
 
8. Nachdem die Web-App erstellt wurde, klicken Sie auf **Web-Apps > {Ihre Web-App}**.
 
	![](./media/web-sites-java-get-started/selectwebapp.png)

9. Klicken Sie auf dem Blatt **Web-App** auf **Einstellungen**.

10. Klicken Sie auf **Anwendungseinstellungen**.

11. Wählen Sie die gewünschte **Java-Version** aus.

12. Wählen Sie die gewünschte **Java-Nebenversion** aus. Wenn Sie **Neueste** auswählen, verwendet Ihre App die neueste Nebenversion, die in App Service für die betreffende Java-Hauptversion zur Verfügung steht.

12. Wählen Sie den gewünschten **Webcontainer** aus. Wenn Sie einen Containernamen auswählen, der mit **Neueste** beginnt, wird Ihre App auf der neuesten Version der Webcontainer-Hauptversion gehalten, die in App Service zur Verfügung steht.

	![](./media/web-sites-java-get-started/versions.png)

13. Klicken Sie auf **Speichern**.

	In wenigen Augenblicken basiert Ihre Web-App auf Java und ist für die Verwendung des ausgewählten Webcontainers konfiguriert.

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

<!---HONumber=Nov15_HO2-->