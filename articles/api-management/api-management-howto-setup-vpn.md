<properties
	pageTitle="Einrichten von VPN-Verbindungen in Azure API Management"
	description="Erfahren Sie, wie Sie eine VPN-Verbindung in Azure API Management einrichten und über die VPN-Verbindung auf Webdienste zugreifen."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="antonba"/>

# Einrichten von VPN-Verbindungen in Azure API Management

Mit der VPN-Unterstützung von API Management können Sie Ihr API Management-Gateway mit einer Azure Virtual Network-Instanz (klassisch) verbinden. Auf diese Weise können Ihre API Management-Kunden eine sichere Verbindung mit ihren Back-End-Webdiensten herstellen, die lokal vorliegen oder auf die aus anderen Gründen nicht über das öffentliche Internet zugegriffen werden kann.

>[AZURE.NOTE] Azure API Management arbeitet mit klassischen VNETs. Informationen zum Erstellen eines klassischen VNET finden Sie unter [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Informationen zum Herstellen einer Verbindung von klassischen VNETs mit ARM-VNETS finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

## <a name="enable-vpn"> </a>Aktivieren von VPN-Verbindungen

>Die VPN-Konnektivität steht nur im Tarif **Premium** zur Verfügung. Um zu diesem Tarif zu wechseln, öffnen Sie Ihren API Management-Dienst im [klassischen Azure-Portal][] und öffnen dann die Registerkarte **Skalieren**. Wählen Sie im Abschnitt **Allgemein** den Premium-Tarif, und klicken Sie auf "Speichern".

Um die VPN-Konnektivität zu aktivieren, öffnen Sie Ihren API Management-Dienst im [klassischen Azure-Portal][] und wechseln zur Registerkarte **Konfigurieren**.

Legen Sie im VPN-Abschnitt die Option **VPN-Verbindung** auf **Ein** fest.

![Registerkarte "Konfigurieren" der API Management-Instanz][api-management-setup-vpn-configure]

Es wird daraufhin eine Liste aller Regionen angezeigt, in denen Ihr API Management-Dienst bereitgestellt wird.

Wählen Sie ein VPN und Subnetz für jede Region. Die Liste der VPNs wird basierend auf den virtuellen Netzwerken aufgefüllt, die in Ihrem Azure-Abonnement für die konfigurierte Region zur Verfügung stehen.

![VPN auswählen][api-management-setup-vpn-select]

Klicken Sie unten auf dem Bildschirm auf **Speichern**. Während des Updates können Sie über das klassische Azure-Portal keine weiteren Vorgänge für den API Management-Dienst durchführen. Das Dienstgateway bleibt verfügbar, und Laufzeitaufrufe sollten nicht beeinträchtigt werden.

Beachten Sie, dass sich die VIP-Adresse des Gateways bei jeder Aktivierung oder Deaktivierung des VPN ändert.

## <a name="connect-vpn"> </a>Herstellen einer Verbindung mit einem Webdienst hinter dem VPN

Nachdem Ihr API Management-Dienst mit dem VPN verbunden wurde, unterscheidet sich der Zugriff auf Webdienste im virtuellen Netzwerk nicht vom Zugriff auf öffentliche Dienste. Geben Sie einfach die lokale Adresse oder den Hostnamen Ihres Webdiensts (falls der DNS-Server für Azure Virtual Network konfiguriert wurde) im Feld **Webdienst-URL** ein, wenn Sie eine neue API erstellen oder eine vorhandene API bearbeiten.

![API über VPN hinzufügen][api-management-setup-vpn-add-api]

## Erforderliche Ports für die API Management-VPN-Unterstützung

Beim Hosten einer API Management-Dienstinstanz in einem VNET werden die in der folgenden Tabelle angegebenen Ports verwendet. Wenn diese Ports gesperrt sind, funktioniert der Dienst möglicherweise nicht ordnungsgemäß. Eine bestehende Sperre für mindestens einen dieser Ports ist die häufigste Fehlkonfiguration, die beim Verwenden von API Management in einem VNET auftritt.

| Port(s) | Richtung | Transportprotokoll | Zweck | Quelle/Ziel |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443 | Eingehend | TCP | Kommunikation zwischen Clients und API Management | INTERNET/VIRTUAL\_NETWORK |
| 80, 443 | Ausgehend | TCP | API Management-Abhängigkeit von Azure Storage und Azure Service Bus | VIRTUAL\_NETWORK/INTERNET |
| 1433 | Ausgehend | TCP | API Management-Abhängigkeiten von SQL | VIRTUAL\_NETWORK/INTERNET |
| 9350, 9351, 9352, 9353, 9354 | Ausgehend | TCP | API Management-Abhängigkeiten von Service Bus | VIRTUAL\_NETWORK/INTERNET |
| 5671 | Ausgehend | AMQP | API Management-Abhängigkeit für Richtlinie zum Anmelden bei Event Hub | VIRTUAL\_NETWORK/INTERNET |
| 6381, 6382, 6383 | Eingehend/ausgehend | UDP | API Management-Abhängigkeiten von Redis Cache | VIRTUAL\_NETWORK/VIRTUAL\_NETWORK |
| 445 | Ausgehend | TCP | API Management-Abhängigkeit von Azure File Share für GIT | VIRTUAL\_NETWORK/INTERNET |


## <a name="related-content"> </a>Verwandte Inhalte


* [Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung mit dem klassischen Azure-Portal][]
* [Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[klassischen Azure-Portal]: https://manage.windowsazure.com/

[Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung mit dem klassischen Azure-Portal]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_0316_2016-->