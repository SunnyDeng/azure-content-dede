<properties  linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create web sites - Azure service management" metaKeywords="Azure creating web site, Azure deleting website" description="Learn how to create a web site using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Web Site" authors="timamm" solutions="" manager="" editor="" />

# Erstellen einer Website

In diesem Thema wird das Erstellen einer Website aus dem Katalog oder
über das Verwaltungsportal erläutert.

Informationen zum Bereitstellen Ihres Inhalts für eine von Ihnen erstellte Azure-Website finden Sie im Abschnitt **Bereitstellen** unter
[Azure-Websites](/de-de/documentation/services/web-sites/).

## Inhaltsverzeichnis

* [Gewusst wie: Erstellen einer Website über das
  Verwaltungsportal](#createawebsiteportal)
* [Gewusst wie: Erstellen einer Website aus dem
  Katalog](#howtocreatefromgallery)
* [Gewusst wie: Löschen einer Website](#deleteawebsite)
* [Nächste Schritte](#nextsteps)

## <a name="createawebsiteportal"></a>Gewusst wie: Erstellen einer Website über das Verwaltungsportal

Führen Sie diese Schritte aus, um eine Website in Azure zu erstellen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][1] an.

2.  Klicken Sie auf das Symbol **Neu erstellen** unten links im
    Verwaltungsportal.

3.  Klicken Sie auf das Symbol **Website** und dann auf das Symbol
    **Schnellerfassung**, geben Sie einen Wert für die URL ein, und
    klicken Sie dann auf das Häkchen neben **Website erstellen** rechts
    unten auf der Seite.

4.  Nach der Erstellung der Website wird der Text **Creating web site
    <*web site name*> succeeded** angezeigt. Sie können zu der
    Website navigieren, indem Sie unten auf der Portalseite auf
    **Durchsuchen** klicken.

5.  Klicken Sie im Portal auf den Namen der Website, der in der Liste
    der Websites aufgeführt wird, um die Verwaltungsseite
    **Schnellstart** der Website zu öffnen.

6.  Auf der Seite **Schnellstart** erhalten Sie Optionen zum Abrufen von
    Websiteentwicklungstools, Einrichten von Veröffentlichungen für die
    Website oder Einrichten der Bereitstellung von einem
    Quellcode-Verwaltungsanbieter wie TFS oder Git. Die
    FTP-Veröffentlichung wird standardmäßig für Websites eingerichtet,
    und der FTP-Hostname wird auf der Seite **Dashboard** im Abschnitt
    **Quick Glance** unter **FTP Host Name** angegeben. Wählen Sie vor
    der Veröffentlichung auf einem FTP oder Git die Option zum
    Zurücksetzen der Bereitstellungsanmeldeinformationen auf der Seite
    **Dashboard** aus, sodass Sie eine Authentifizierung beim FTP-Host
    oder dem Git-Repository durchführen können, wenn Sie den Inhalt der
    Website bereitstellen.

7.  Auf der Verwaltungsseite **Configure** werden Einstellungen für Ihre
    Website in den folgenden Kategorien verfügbar gemacht:

* **Allgemein**: Legen Sie die für die Webanwendung erforderliche .NET
  Framework- oder PHP-Version fest. Bei Websites im Standardmodus können
  Sie über die Option **Plattform** eine 32-Bit- oder 64-Bit-Umgebung
  auswählen.

* **Zertifikate**: Im Standardmodus können Sie SSL-Zertifikate für
  benutzerdefinierte Domänen hochladen.

* **Domänennamen**: Im freigegebenen und Standardmodus können Sie die
  Namen von benutzerdefinierten Domänen für Ihre Website anzeigen und
  verwalten.

* **SSL Bindings**: Im Standardmodus können Sie den Namen von
  benutzerdefinierten Domänen entweder IP-basiert oder über
  SNI-basiertes SSL SSL-Zertifikate zuweisen.

* **Bereitstellungen**: Wenn Sie die Bereitstellung über die
  Quellcodeverwaltung einrichten, können Sie Ihre Bereitstellungen an
  dieser Stelle konfigurieren.

* **Application Diagnostics**: Legen Sie die Optionen zum Erfassen von
  Diagnoseablaufverfolgungen aus einer Webanwendung fest, die mit
  Ablaufverfolgungen instrumentiert wurde.

* **Site Diagnostics**: Legen Sie Protokollierungsoptionen zum Erfassen
  von Diagnoseinformationen für Ihre Website fest. Zu den Optionen
  zählen Webserverprotokollierung, detaillierte Fehlermeldungen und
  Ablaufverfolgung für fehlerhafte Anforderungen.

* **Überwachung**: Bei Websites im Standardmodus können Sie mit diesem
  Feature die Verfügbarkeit von HTTP- oder HTTPS-Endpunkten testen.

* **App-Einstellungen**: Geben Sie Name-Wert-Paare an, die beim Start
  von der Webanwendung geladen werden. Bei .NET-Websites werden diese
  Einstellungen zur Laufzeit in die **AppSettings** der
  .NET-Konfiguration eingeführt, wobei vorhandene Einstellungen
  überschrieben werden. Bei PHP- und Node-Websites sind diese
  Einstellungen als Umgebungsvariablen in der Laufzeit verfügbar.

* **Connection Strings**: Zeigen Sie Verbindungszeichenfolgen an und
  bearbeiten Sie sie. Bei .NET-Websites werden diese
  Verbindungszeichenfolgen zur Laufzeit in die **connectionStrings** der
  .NET-Konfiguration eingeführt, wobei vorhandene Einträge überschrieben
  werden, wenn der Schlüssel dem verknüpften Datenbanknamen entspricht.
  Bei PHP- und Node-Websites sind diese Einstellungen als
  Umgebungsvariablen zur Laufzeit verfügbar.

* **Default Documents**: Das Standarddokument einer Website ist die
  Seite, die standardmäßig angezeigt wird, wenn ein Benutzer zu einer
  Website navigiert. Fügen Sie das Standarddokument Ihrer Website dieser
  Liste hinzu, wenn es noch nicht darauf vorhanden ist. Das
  Standarddokument Ihrer Website sollte an erster Stelle auf der Liste
  stehen.

* **Handlerzuordnungen**: Geben Sie Skriptprozessoren an, von denen
  Anforderungen für spezifische Dateierweiterungen wie \*.php
  verarbeitet werden.

## <a name="howtocreatefromgallery"></a>Gewusst wie: Erstellen einer Website aus dem Katalog

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>Gewusst wie: Löschen einer Website

Websites werden über das Symbol **Löschen** im Azure-Verwaltungsportal gelöscht. Das Symbol **Löschen** ist im Azure-Portal verfügbar, wenn Sie auf **Websites** klicken, um alle Ihre Websites aufzuführen, sowie unten auf jeder Website-Verwaltungsseite.

## <a name="nextsteps"></a>Nächste Schritte

Weitere Informationen finden Sie unter
[Azure-Websites](/de-de/documentation/services/web-sites/).



[1]: http://manage.windowsazure.com/