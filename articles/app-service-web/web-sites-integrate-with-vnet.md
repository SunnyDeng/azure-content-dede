<properties 
	pageTitle="Integrieren einer Web-App in einem Azure Virtual Network" 
	description="Sie erfahren, wie Sie eine Verbindung zwischen einer Azure-Web-App in Azure App Service und einem neuen oder vorhandenen virtuellen Azure-Netzwerk herstellen." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Integrieren einer Web-App in einem Azure Virtual Network #

Dieses Dokument beschreibt die Vorschaufunktion der Virtual Network-Integration und zeigt, wie Sie diese mit Web-Apps in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) einrichten. Mit Azure Virtual Networks und Ihren lokalen Ressourcen können Sie Hybrid-Lösungen entwickeln, auch wenn Sie nicht mit virtuellen Azure-Netzwerken vertraut sind.

Diese Integration ermöglicht Ihrer Web-App den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen Zugriff auf Ihre Web-App aus dem virtuellen Netzwerk. Einige Standardszenarios sind dort vorgesehen, wo Ihre Web-App Zugriff auf Datenbank- oder Webdienste benötigt, die auf virtuellen Maschinen in Ihrem virtuellen Netzwerk oder sogar in Ihrem eigenen Rechenzentrum ausgeführt werden. Der Einbau eines Laufwerks wird nicht unterstützt. Auch die Integration mit Authentifizierungssystemen in Ihrem virtuellen Netzwerk ist derzeit nicht möglich. Diese Funktion ist allerdings in der Vorschau enthalten und wird vor der GA weiterhin verbessert.

Weitere Informationen zu Azure Virtual Networks, den Vorteilen und Anwendungsfällen finden Sie unter "Virtuelle Netzwerke im Überblick".

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Erste Schritte ##
Sie müssen Folgendes beachten, bevor Sie Ihre Web-App mit einem virtuellen Netzwerk verbinden:

1.	Web-Apps können nur mit einem virtuellen Netzwerk verbunden werden, wenn sie mit einem App Service-Plan des Tarifs **Standard** ausgeführt werden. Web-Apps der Stufen "Kostenlos", "Freigegeben" und "Basic" können nicht mit einem virtuellen Netzwerk verbunden werden.
2.	Wenn Ihr virtuelles Zielnetzwerk bereits vorhanden ist, muss es über Punkt-zu-Standort mit einem dynamischen Routing-Gateway aktiviert werden, bevor es mit einer Web-App verbunden werden kann. Sie können Punkt-zu-Standort-VPN (Virtual Private Network) nicht aktivieren, wenn Ihr Gateway mit statischem Routing konfiguriert ist.
3.	Sie können nur bis zu fünf Netzwerke in Ihrem App Service-Plan konfigurieren. Eine Web-App kann jeweils nur mit einem Netzwerk verbunden werden. Diese fünf Netzwerke können von einer beliebigen Anzahl von Web-Apps im selben App Service-Plan verwendet werden.  

Sie haben die Möglichkeit, sich mit einem neuen oder bereits vorhandenen virtuellen Netzwerk zu verbinden. Wenn Sie ein neues Netzwerk einrichten, wird ein Gateway für Sie vorkonfiguriert. Beachten Sie, dass die Erstellung und Konfiguration eines neuen virtuellen Netzwerks mehrere Minuten dauern wird.

Wenn Ihre Web-App nicht mit einem App Service-Plan des Tarifs "Standard" ausgeführt wird, erhalten Sie einen Hinweis mit einem Link zu den entsprechenden Preisstufen für eine Hochstufung.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png)

## Wie das System funktioniert ##
Unter der Oberfläche verwendet diese Funktion Punkt-zu-Standort-VPN-Technologie, um Ihre Web-App mit Ihrem virtuellen Netzwerk zu verbinden. Web-Apps in Azure App Service nutzen eine Mehrmandanten-Systemarchitektur, die eine Bereitstellung von Web-Apps direkt in einem virtuellen Netzwerk, wie dies bei virtuellen Computern der Fall ist, ausschließt. Durch den Aufbau auf der Punkt-zu-Standort-Technologie beschränken wir den Netzwerkzugriff auf den virtuellen Computer, auf dem die Web-App gehostet wird. Der Zugriff auf das Netzwerk bleibt auf diese Web-App-Hosts beschränkt, sodass Ihre Web-Apps nur auf Netzwerke zugreifen können, für die Sie den Zugriff konfiguriert haben.

Wenn Ihre Netzwerke nur für Web-Apps gesichert sind, die Zugriff benötigen, können keine SMB-Verbindungen hergestellt werden. Während Sie auf Remote-Ressourcen zugreifen können, wird der Einbau eines Remote-Laufwerks nicht unterstützt.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
Wenn Sie keinen DNS-Server mit Ihrem virtuellen Netzwerk konfiguriert haben, müssen Sie eine IP-Adresse verwenden. Achten Sie darauf, die Ports für Ihre gewünschten Endpunkte durch Ihre Firewall zu exponieren. Um Ihre Verbindung zu testen, müssen Sie zurzeit noch eine Web-App oder einen Webauftrag verwenden, um den gewünschten Endpunkt aufzurufen. Tools wie Ping oder nslookup können derzeit nicht über die Kudu-Konsole ausgeführt werden. Diese Optionen sollen in naher Zukunft zur Verfügung stehen.

## Verbindung zu einem bereits bestehenden Netzwerk ##
Um eine Web-App mit einem virtuellen Netzwerk zu verbinden, klicken Sie im Blatt Ihrer Web-App auf die Kachel des virtuellen Netzwerks im Bereich "Netzwerk" und wählen eines Ihrer bereits vorhandenen Netzwerke aus.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
Das System erstellt dann ein Zertifikat, um in Ihrem virtuellen Netzwerk zu authentifizieren, ob es sich um die erste Web-App in Ihrem Abonnement handelt, die eine Verbindung zu diesem Netzwerk herstellt. Um die Übergabe des Zertifikats an das [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) anzuzeigen, navigieren Sie zu "Virtual Networks" und wählen das Netzwerk und die Registerkarte "Zertifikate" aus.

Die Abbildung oben zeigt ein Netzwerk mit dem Namen "cantConnectVnet", das ausgegraut und nicht ausgewählt werden kann. Es gibt hierfür nur zwei Gründe. Es bedeutet, dass Sie entweder Punkt-zu-Standort-VPN nicht in Ihrem Netzwerk aktiviert haben oder dass Sie kein dynamisches Routing-Gateway in Ihrem virtuellen Netzwerk bereitgestellt haben. Wenn beide Voraussetzungen erfüllt sind, können Sie das virtuelle Netzwerk für die Integration mit Ihrer Web-App auswählen.

## Erstellen und Verbinden eines neuen virtuellen Netzwerks ##
Neben der Verbindung mit einem bereits bestehenden virtuellen Netzwerk können Sie auch ein neues virtuelles Netzwerk auf der Benutzeroberfläche des Azure-Portals erstellen und automatisch eine Verbindung damit herstellen. Befolgen Sie hierzu die gleichen Schritte wie bei der Verbindung mit der Benutzeroberfläche des virtuellen Netzwerkes, und wählen Sie "Neues virtuelles Netzwerk erstellen". Mit der angezeigten Benutzeroberfläche können Sie das Netzwerk benennen, den Adressraum spezifizieren und die Adressen für die DNS-Server einrichten, die vom virtuellen Netzwerk verwendet werden sollen.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
Die Erstellung eines neuen virtuellen Netzwerks mit konfigurierten Gateways kann bis zu 30 Minuten in Anspruch nehmen. Während dieser Zeit wird die folgende Meldung angezeigt:

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Sobald das Netzwerk mit der Web-App verbunden wurde, hat diese über TCP oder UDP Zugriff auf Ressourcen in diesem virtuellen Netzwerk. Wenn Sie auf Ressourcen in Ihrem lokalen System zugreifen möchten, die über Standort-zu-Standort-VPN-Zugriff in Ihrem virtuellen Netzwerk verfügbar sind, müssen Sie Routen in Ihrem eigenen Firmennetzwerk hinzufügen, um Datenverkehr von Ihrem Netzwerk zu den in Ihrem virtuellen Netzwerk konfigurierten Punkt-zu-Standort-Adressen zu erlauben.

Nach erfolgreicher Integration wird das Azure-Portal grundlegende Informationen über die Verbindung anzeigen. Sie können die Web-App vom Netz trennen und die Zertifikate für die Verbindungsauthentifizierung synchronisieren. Die Synchronisation kann erforderlich sein, wenn ein Zertifikat abgelaufen ist oder widerrufen wurde.

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

##Verwalten die Verbindung des virtuellen Netzwerks##
Auf dem Blatt zum App Service-Plan können Sie eine Liste aller virtuellen Netzwerke anzeigen, die derzeit Web-Apps in einem App Service-Plan zugeordnet sind. Sie können einem App Service-Plan des Tarifs "Standard" maximal fünf Netzwerke zuordnen.

Sollte der App Service-Plan in einen niedrigeren Plan wie "Kostenlos", "Freigegeben" oder "Basic" geändert werden, werden die virtuellen Netzwerkverbindungen, die von den Web-Apps in diesem Plan verwendet werden, deaktiviert. Falls der Plan wieder zum Standardplan zurückskaliert wird, können diese Netzwerkverbindungen wieder hergestellt werden.

Zu diesem Zeitpunkt ist es in Azure nicht möglich, einen vorhandenen virtuellen Computer in ein virtuelles Netzwerk zu verschieben. Der virtuelle Computer muss bei der Erstellung in diesem virtuellen Netzwerk bereitgestellt werden.

## Zugreifen auf lokale Ressourcen ##
Bei der Arbeit mit einem virtuellen Netzwerk, das mit Standort-zu-Standort-VPN konfiguriert wurde, ist ein zusätzlicher Schritt erforderlich, um Zugriff auf Ihre lokalen Ressourcen von Ihrer Web-App bereitzustellen. Routen müssen Ihrem lokalen Netzwerk hinzugefügt werden, um Datenverkehr von Ihrem Netzwerk zu den in Ihrem virtuellen Netzwerk konfigurierten Punkt-zu-Standort-Adressen zu ermöglichen. Um den IP-Bereich für Ihre Punkt-zu-Standort-Verbindungen anzuzeigen, wechseln Sie im Azure-Portal wie hier dargestellt in den Netzwerkbereich.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Zertifikate ##
Um eine sichere Verbindung mit Ihrem virtuellen Netzwerk herzustellen, werden Zertifikate ausgetauscht. Sie können den Fingerabdruck für das öffentliche Zertifikat, das Web-Apps aus dem aktuellen Netzwerkportal erzeugen, wie unten dargestellt anzeigen.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Wenn die Zertifikate aus irgendeinem Grund, wie versehentlichem Löschen aus dem Netzwerkportal, nicht mehr synchron sind, wird die Verbindung unterbrochen. Über die Aktion "Verbindung synchronisieren" auf der Benutzeroberfläche des virtuellen Netzwerks der Web-App können Sie dieses Problem beheben und die Verbindung wiederherstellen.

Diese Aktion müssen Sie auch verwenden, um Ihrem virtuellen Netzwerk ein DNS oder Ihrem Netzwerk Standort-zu-Standort-VPN hinzuzufügen.

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Mit Hybridverbindungen vergleichen und kontrastieren ##
Auch die Hybridverbindungs-Funktion von Web-Apps ähnelt in mancher Hinsicht der virtuellen Netzwerkintegration. Zwar gibt es einige Überschneidungen bei den Anwendungsfällen, die Funktionen sind jedoch nicht austauschbar. Mit Hybridverbindungen können Sie Verbindungen zu mehreren Anwendungsendpunkten in einer gemischten Netzwerkumgebung herstellen. Über die Funktion "Virtuelle Netzwerke" können Sie Ihre Web-App mit einem virtuellen Netzwerk verbinden, das mit Ihrem lokalen Netzwerk verbunden werden kann. Das funktioniert problemlos, wenn sich Ihre Ressourcen alle im Bereich dieses Netzwerks befinden.

Ein weiterer Unterschied besteht darin, dass Sie einen Relay-Agenten für die Hybridverbindungen installieren müssen. Dieser Agent muss auf einer Windows Server-Instanz ausgeführt werden. Dank der Funktion "Virtuelles Netzwerk" müssen Sie nichts installieren und können unabhängig von Hosting-Betriebssystemen auf Remote-Ressourcen zugreifen.

Es gibt zurzeit auch bei den Preisebenen noch Unterschiede zwischen den beiden Funktionen: Auf der günstigeren Ebene ist die Hybridverbindungs-Funktion äußerst nützlich für Entwicklungs- und Testszenarien. Hier wird nur Zugang zu einer kleinen Anzahl an Endpunkten gewährt. Mit der virtuellen Netzwerkfunktion hingegen haben Sie Zugriff auf alles, was im VNET enthalten oder mit ihm verbunden ist.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=Oct15_HO3-->