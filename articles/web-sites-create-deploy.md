<properties
	pageTitle="Erstellen einer Web-App - Azure-Dienstverwaltung"
	description="Erfahren Sie, wie Sie im Azure-Portal eine Web-App erstellen."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Erstellen einer Web-App

In diesem Thema wird das Erstellen einer Web-App aus der Galerie oder über das Azure-Portal erläutert.

Informationen zum Bereitstellen Ihres Inhalts für eine von Ihnen erstellte Web-App finden Sie im Abschnitt **Bereitstellen** unter [Azure-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##<a name="createawebsiteportal"></a>Gewusst wie: Erstellen einer Web-App mithilfe des Azure-Portals

Führen Sie diese Schritte aus, um eine Web-App in Azure zu erstellen.

1. Melden Sie sich beim [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) an.

2. Klicken Sie unten links im Azure-Portal auf das Symbol **Neu**.

3. Klicken Sie auf das Symbol **Web + Mobile** und auf das Symbol **Web-App**, und geben Sie einen Wert für die URL ein. Klicken Sie dann unten rechts auf dem Blatt "Erstellen" auf **Erstellen**.

4. Nach der Erstellung der Web-App wird der Text **Deployment to resource group <Ressourcengruppenname> was successful** angezeigt.

5. Klicken Sie im Portal in der Liste der Web-Apps auf den Namen der Web-App, um das zugehörige Blatt zu öffnen.

6. Auf dem Blatt erhalten Sie Optionen zum Abrufen von Web-App-Entwicklungstools, zum Einrichten von Veröffentlichungen für die Web-App und zum Einrichten der Bereitstellung von einem Quellcode-Verwaltungsanbieter wie TFS oder Git. Die FTP-Veröffentlichung ist das Standardverfahren für Web-Apps, und der FTP-Hostname wird im Abschnitt **Essentials** des Web-App-Blatts angezeigt. Vor dem Veröffentlichen mit FTP oder Git wählen Sie auf dem Web-App-Blatt die Option **Reset publish profile**, damit Sie sich beim FTP-Host oder Git-Repository authentifizieren können, wenn Sie Inhalte in Ihrer Web-App bereitstellen.

7. Das Blatt **Einstellungen** enthält Einstellungen für Ihre Web-App, wie z. B.:

	- Version von .NET, PHP, Java oder Python für Ihre Web-App
	- In Visual Studio Online bearbeiten
	- SSL-Bindungen
	- Benutzerdefinierte Domänennamen
	- Authentifizierung/Autorisierung
	- Anwendungs- und Website-Diagnose
	- Überwachen von Endpunkten
	- Anmeldeoptionen
	- App-Einstellungen für die Azure-Umgebung (überschreibt die <appSettings> in der "Web.config" Ihrer Entwicklungsumgebung, zum Beispiel)
	- Verbindungszeichenfolgen (überschreibt die <connectionStrings> in der "Web.config" Ihrer Entwicklungsumgebung, zum Beispiel)
	- Skriptprozessoren für spezifische Dateierweiterungen wie *.php

##<a name="howtocreatefromgallery"></a>Gewusst wie: Erstellen einer Web-App aus der Galerie

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Gewusst wie: Löschen einer Web-App
Web-Apps werden über das Symbol **Löschen** im Azure-Portal gelöscht. Das Symbol **Löschen** finden Sie oben auf dem Web-App-Blatt.

##<a name="nextsteps"></a> Nächste Schritte

Weitere Informationen finden Sie unter [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->