<properties title="Integrate your Azure Website with an Azure Virtual Network" pageTitle="Integrate Azure Website with Azure VNet" description="Shows you how to connect an Azure Website to a new or existing Azure virtual network" metaKeywords="" services="web-sites,virtual-network" solutions="web,integration,infrastructure" documentationCenter="" authors="cephalin" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="cephalin" />

# Integrieren Sie Ihre Azure-Website in ein virtuelles Azure-Netzwerk

Dieses Dokument beschreibt die Vorschaufunktion der virtuellen Netzwerkintegration und zeigt, wie Sie diese mit Ihrer Azure-Website einrichten. Mit Azure Virtual Networks und Ihren lokalen Ressourcen können Sie Hybrid-Lösungen entwickeln, auch wenn Sie nicht mit virtuellen Azure-Netzwerken vertraut sind.

Diese Integration verschafft Ihrer Website Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen Zugriff auf Ihre Website aus dem virtuellen Netzwerk. Einige Standardszenarien sind dort vorgesehen, wo Ihre Website Zugriff auf Datenbank- oder Webdienste benötigt, die in virtuellen Maschinen in Ihrem VNET oder sogar in Ihrem eigenen Rechenzentrum ausgeführt werden. Der Einbau eines Laufwerks wird nicht unterstützt. Auch die Integration mit Authentifizierungssystemen in Ihrem VNET ist derzeit nicht möglich. Diese Funktion ist allerdings in der Vorschau enthalten und wird vor der GA weiterhin verbessert.

Weitere Informationen zu Azure Virtual Networks, den Vorteilen und Anwendungsfällen finden Sie unter "Virtuelle Netzwerke im Überblick".

## Erste Schritte

Sie müssen Folgendes beachten, bevor Sie Ihre Website mit einem virtuellen Netzwerk verbinden:

1.  Standorte können nur mit einem virtuellen Netzwerk verbunden werden, wenn sie mit einem Webhostingplan der Preisstufe "Standard" ausgeführt werden. Standorte der Stufen Free, Shared und Basic können nicht mit einem virtuellen Netzwerk verbunden werden.
2.  Wenn Ihr virtuelles Zielnetzwerk bereits vorhanden ist, muss es über Punkt-zu-Standort mit einem dynamischen Routing-Gateway aktiviert werden, bevor es mit einer Website verbunden werden kann. Sie können Punkt-zu-Standort-VPN nicht aktivieren, wenn Ihr Gateway mit Statischem Routing konfiguriert ist.
3.  Sie können nur bis zu 5 Netzwerke in Ihrem Webhostingplan konfigurieren. Eine Website kann jeweils nur mit einem Netzwerk verbunden werden. Diese 5 Netzwerke können von einer beliebigen Anzahl von Websites im selben Webhostingplan verwendet werden.

Sie haben die Möglichkeit, sich mit einem neuen oder bereits vorhandenen virtuellen Netzwerk zu verbinden. Wenn Sie ein neues Netzwerk einrichten, wird ein Gateway für Sie vorkonfiguriert. Beachten Sie, dass die Erstellung und Konfiguration eines neuen virtuellen Netzwerks mehrere Minuten dauern wird.

Wenn Ihre Website nicht auf einer Standardebene ausgeführt wird, erhalten Sie einen Hinweis mit einem Link zu den entsprechenden Preisebenen.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png)

## Wie das System funktioniert

Unter der Oberfläche verwendet dieses Funktion Punkt-zu-Standort-VPN-Technologie, um Ihre Azure-Website mit Ihrem VNET zu verbinden. Die Azure Websites-Systemarchitektur ist von Natur aus mandantenfähig. Die direkte Bereitstellung von Websites in einem VNET, wie mit virtuellen Maschinen üblich, ist daher ausgeschlossen. Durch den Aufbau auf der Punkt-zu-Standort-Technologie beschränken wir den Netzwerkzugriff auf die virtuelle Maschine, auf der die Website gehostet wird. Der Zugriff auf das Netzwerk bleibt auf diese Websitehosts beschränkt, sodass Ihre Websites nur auf Netzwerke zugreifen können, für die Sie den Zugriff konfiguriert haben.

Wenn Ihre Netzwerke nur für Websites gesichert sind, die Zugriff benötigen, können keine SMB-Verbindungen erstellt werden. Während Sie auf Remote-Ressourcen zugreifen können, wird der Einbau eines Remote-Laufwerks nicht unterstützt.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)

Wenn Sie keinen DNS-Server mit Ihrem virtuellen Netzwerk konfiguriert haben, müssen Sie eine IP-Adresse verwenden. Achten Sie darauf, die Ports für Ihre gewünschten Endpunkte durch Ihre Firewall zu exponieren. Um Ihre Verbindung zu testen, müssen Sie eine Website oder einen Webjob verwenden, um die gewünschten Endpunkte aufzurufen. Tools wie Ping oder nslookup können derzeit nicht über die Kudu-Konsole ausgeführt werden. Diese Optionen sollen in naher Zukunft zur Verfügung stehen.

## Verbindung zu einem bereits bestehenden Netzwerk

Um einen Standort mit einem virtuellen Netzwerk zu verbinden, klicken Sie in Ihrem Websitefenster auf das virtuelle Netzwerk-Tile im Abschnitt "Netzwerk" und wählen eines Ihrer bereits vorhandenen Netzwerke aus.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)

Das System erstellt dann ein Zertifikat, um mit Ihrem VNET zu authentifizieren, ob es sich um die erste Website in Ihrem Abonnement handelt, um eine Verbindung zu diesem Netzwerk herzustellen. Um die Übergabe des Zertifikats an das aktuelle Portal anzuzeigen, navigieren Sie zu "Virtuelle Netzwerke" und wählen das Netzwerk und die Registerkarte "Zertifikate" aus.

Die Abbildung oben zeigt ein Netzwerk mit dem Namen "cantConnectVnet", das ausgegraut und nicht ausgewählt werden kann. Es gibt hierfür nur zwei Gründe. Es bedeutet, dass Sie entweder Punkt-zu-Standort-VPN nicht in Ihrem Netzwerk aktiviert haben oder dass Sie kein dynamisches Routing-Gateway in Ihrem VNET bereitgestellt haben. Wenn beide Voraussetzungen erfüllt sind, können Sie das VNET für die Integration mit Ihrer Website auswählen.

## Erstellung und Verbindung mit einem neuen VNET

Neben der Verbindung mit einem bereits bestehenden VNET können Sie auch ein neues VNET von der Website-Benutzeroberfläche aus erstellen und sich automatisch mit ihm verbinden. Befolgen Sie hierzu die gleichen Schritte wie bei der Verbindung mit der Benutzeroberfläche des virtuellen Netzwerkes, und wählen Sie "Neues virtuelles Netzwerk erstellen". Mit der angezeigten Benutzeroberfläche können Sie das Netzwerk benennen, den Adressraum spezifizieren und die Adressen für die DNS-Server einrichten, die vom virtuellen Netzwerk verwendet werden sollen.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)

Die Erstellung eines neuen virtuellen Netzwerks mit konfigurierten Gateways kann bis zu 30 Minuten in Anspruch nehmen. Während dieser Zeit wird die folgende Meldung angezeigt:

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Sobald das Netzwerk mit der Website verbunden wurde, wird diese über Zugriff auf Ressourcen in diesem VNET über TCP oder UDP verfügen. Wenn Sie auf Ressourcen in Ihrem lokalen System zugreifen möchten, die über Standort-zu-Standort-VPN-Zugriff auf Ihrem VNET verfügbar sind, müssen Sie Routen in Ihrem eigenen Firmennetzwerk hinzufügen, um Datenverkehr von Ihrem Netzwerk zu den in Ihrem VNET konfigurierten Punkt-zu-Standort-Adressen zu erlauben.

Nach erfolgreicher Integration wird das Portal grundlegende Informationen über die Verbindung anzeigen. Sie können die Website vom Netz trennen und die Zertifikate für die Verbindungsauthentifizierung synchronisieren. Die Synchronisation kann erforderlich sein, wenn ein Zertifikat abgelaufen ist oder widerrufen wurde.

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

Verwaltung der virtuellen Netzwerkverbindung
Im Webhostingplan-Fenster können Sie eine Liste mit allen virtuellen Netzwerken anzeigen, die aktuell Standorten in einem Webhostingplan zugeordnet sind. Sie können einem Standard-Webhostingplan maximal 5 Netzwerke zuordnen.

Sollte der Webhostingplan in einen niedrigeren Plan wie Free, Shared oder Basic skaliert werden, werden die virtuellen Netzwerkverbindungen, die von den Websites in diesem Plan verwendet werden, deaktiviert. Falls der Plan wieder zum Standardplan zurückskaliert wird, können diese Netzwerkverbindungen wieder hergestellt werden.

Zu diesem Zeitpunkt ist es in Azure nicht möglich, einen vorhandenen virtuellen Computer in ein virtuelles Netzwerk zu verschieben. Der virtuelle Computer muss bei der Erstellung in diesem virtuellen Netzwerk bereitgestellt werden.

## Zugriff auf Betriebsressourcen

Bei der Arbeit mit einem VNET, das mit Standort-zu-Standort-VPN konfiguriert wurde, ist ein zusätzlicher Schritt erforderlich, um Zugriff auf Ihre lokalen Ressourcen von Ihrer Azure-Website bereitzustellen. Routen müssen zu Ihrem lokalen Netzwerk hinzugefügt werden, um Datenverkehr von Ihrem Netzwerk zu den in Ihrem VNET konfigurierten Punkt-zu-Standort-Adressen zu ermöglichen. Um Ihren IP-Bereich für Ihre Punkt-zu-Standort-Konnektivität anzuzeigen, gehen Sie wie hier dargestellt auf den Netzwerkbereich im aktuellen Portal.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Zertifikate

Um eine sichere Verbindung mit Ihrem VNET zu etablieren, werden Zertifikate ausgetauscht. Sie können den Fingerabdruck für das öffentliche Zertifikat, das Azure Websites aus dem aktuellen Netzwerkportal erzeugt, wie unten dargestellt anzeigen.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Wenn die Zertifikate aus irgendeinem Grund, wie versehentlichem Löschen aus dem Netzwerkportal, nicht mehr synchron sind, wird die Konnektivität unterbrochen. Über die Aktion "Verbindung synchronisieren" auf der Benutzeroberfläche des virtuellen Netzwerks von Azure Websites können Sie dieses Problem beheben und die Konnektivität wiederherstellen.

Diese Aktion müssen Sie auch verwenden, um Ihrem virtuellen Netzwerk ein DNS oder Ihrem Netzwerk Standort-zu-Standort-VPN hinzuzufügen.

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Mit Hybridverbindungen vergleichen und kontrastieren

Auch die Hybridverbindungs-Funktion von Azure Websites ähnelt in mancher Hinsicht der virtuellen Netzwerkintegration. Zwar gibt es einige Überschneidungen bei den Anwendungsfällen, die Funktionen sind jedoch nicht austauschbar. Mit Hybridverbindungen können Sie Verbindungen zu mehreren Anwendungsendpunkten in einer gemischten Netzwerkumgebung herstellen. Über die Funktion "Virtuelle Netzwerke" können Sie Ihre Website mit einem VNET verbinden, das mit Ihrem lokalen Netzwerk verbunden werden kann. Das funktioniert problemlos, wenn sich Ihre Ressourcen alle im Bereich dieses Netzwerks befinden.

Ein weiterer Unterschied besteht darin, dass Sie einen Relay-Agenten für die Hybridverbindungen installieren müssen. Dieser Agent muss auf einer Windows Server-Instanz ausgeführt werden. Dank der Funktion "Virtuelles Netzwerk" müssen Sie nichts installieren und können unabhängig von Hosting-Betriebssystemen auf Remote-Ressourcen zugreifen.

Es gibt zurzeit auch bei den Preisebenen noch Unterschiede zwischen den beiden Funktionen: Auf der günstigeren Ebene ist die Hybridverbindungs-Funktion äußerst nützlich für Entwicklungs- und Testszenarien. Hier wird nur Zugang zu einer kleinen Anzahl an Endpunkten gewährt. Mit der virtuellen Netzwerkfunktion hingegen haben Sie Zugriff auf alles, was im VNET enthalten oder mit ihm verbunden ist.

  []: ./media/web-sites-integrate-with-vnet/upgrade-to-standard.png
