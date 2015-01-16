<properties urlDisplayName="How to create" pageTitle="Erstellen von Websites - Azure-Dienstverwaltung" metaKeywords="Azure creating website, Azure deleting website" description="Erfahren Sie, wie Sie eine Website über das Azure-Verwaltungsportal erstellen." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Erstellen einer Website

In diesem Thema wird das Erstellen einer Website aus der Galerie oder über das Verwaltungsportal erläutert.

Informationen zum Bereitstellen Ihres Inhalts für eine von Ihnen erstellte Azure-Website finden Sie im Abschnitt **Bereitstellen** unter [Azure-Websites](/de-de/documentation/services/web-sites/).

## Inhaltsverzeichnis ##

- [Vorgehensweise: Erstellen einer Website über das Verwaltungsportal](#createawebsiteportal)
- [Vorgehensweise: Erstellen einer Website aus der Galerie](#howtocreatefromgallery)
- [Vorgehensweise: Löschen einer Website](#deleteawebsite)
- [Nächste Schritte](#nextsteps)

##<a name="createawebsiteportal"></a>Vorgehensweise: Erstellen einer Website über das Verwaltungsportal

Führen Sie diese Schritte aus, um eine Website in Azure zu erstellen.
	
1. Melden Sie sich beim [Azure-Verwaltungsportal] an(http://manage.windowsazure.com/).

2. Klicken Sie auf das Symbol **Neu erstellen** unten links im Verwaltungsportal.

3. Klicken Sie auf das Symbol **Website** und dann auf das Symbol **Schnellerfassung**, geben Sie einen Wert für die URL ein, und klicken Sie dann auf das Häkchen neben **Website erstellen** rechts unten auf der Seite.

4. Nach der Erstellung der Website wird der Text **Die Erstellung der Website <*Websitename*> wurde erfolgreich abgeschlossen** angezeigt. Sie können zu der Website navigieren, indem Sie unten auf der Portalseite auf **Durchsuchen** klicken.

5. Klicken Sie im Portal auf den Namen der Website, der in der Liste der Websites aufgeführt wird, um die Verwaltungsseite **Schnellstart** der Website zu öffnen.

6. Auf der Seite **Schnellstart** erhalten Sie Optionen zum Abrufen von Website-Entwicklungstools, Einrichten von Veröffentlichungen für die Website oder Einrichten der Bereitstellung von einem Quellcode-Verwaltungsanbieter wie TFS oder Git. Die FTP-Veröffentlichung wird standardmäßig für Websites eingerichtet, und der FTP-Hostname wird auf der Seite **Dashboard** im Abschnitt **Auf einen Blick** unter **FTP-Hostname** angegeben. Wählen Sie vor der Veröffentlichung auf einem FTP oder Git die Option zum **Zurücksetzen der Bereitstellungsanmeldeinformationen** auf der Seite **Dashboard** aus, sodass Sie eine Authentifizierung beim FTP-Host oder dem Git-Repository durchführen.

7. Die Verwaltungsseite**Konfigurieren** zeigt die Einstellungen für die Website wie beispielsweise:

	- Die .NET Framework- oder PHP-Version der Webanwendung
	- SSL-Bindungen
	- Benutzerdefinierte Domänennamen
	- Anmeldeoptionen
	- App-Einstellungen für die Azure-Umgebung (überschreibt die <appSettings> in der "Web.config" Ihrer Entwicklungsumgebung, zum Beispiel)
	- Verbindungszeichenfolgen (überschreibt die <connectionStrings> in der "Web.config" Ihrer Entwicklungsumgebung, zum Beispiel)
	- Skriptprozessoren für spezifische Dateierweiterungen wie *.php

##<a name="howtocreatefromgallery"></a>Vorgehensweise: Erstellen einer Website aus der Galerie

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Vorgehensweise: Löschen einer Website
Websites werden über das Symbol **Löschen** im Azure-Verwaltungsportal gelöscht. Das Symbol **Löschen** ist im Azure-Portal verfügbar, wenn Sie auf **Websites** klicken, um alle Ihre Websites aufzuführen, sowie unten auf jeder Website-Verwaltungsseite.

##<a name="nextsteps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure-Websites](/de-de/documentation/services/web-sites/).

<!--HONumber=35.1-->

<!--HONumber=35.1-->
