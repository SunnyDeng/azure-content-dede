<properties 
   pageTitle="Gewusst wie: Verwalten der Eigenschaften eines Virtual Network (VNet)"
   description="Enthält Informationen zum Anzeigen und Bearbeiten der Einstellungen für virtuelle Netzwerke."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Gewusst wie: Verwalten der Eigenschaften eines Virtual Network (VNet)
Sie können VNet-Einstellungen anzeigen und ändern, indem Sie das Verwaltungsportal verwenden.

## Anzeigen von VNet-Einstellungen

1. Melden Sie sich am Verwaltungsportal an.

1. Klicken Sie im Navigationsbereich auf **Netzwerke**, und klicken Sie dann in der Spalte **Name** auf den VNet-Namen.

1. Klicken Sie auf **Dashboard**, um die folgenden Informationen anzuzeigen:

	- **Virtuelles Netzwerk:** Dies umfasst das virtuelle Netzwerk und die Subnetze sowie das lokale Netzwerk und den DNS-Server.

	- **Eingehende/ausgehende Daten:** Ist sichtbar, wenn Sie über standortübergreifende Konnektivität verfügen. Dies sind die eingehenden und ausgehenden Daten Ihres virtuellen Netzwerks.

	- **Gateway-IP-Adresse:** Ist sichtbar, wenn Sie ein Gateway konfiguriert haben. Dies ist die Gateway-IP-Adresse. Hierbei handelt es sich um die IP-Adresse, die Sie zum Konfigurieren Ihres VPN-Geräts für die standortübergreifende Konnektivität verwenden.

	- **Ressourcen:** Dies sind die Azure-Ressourcen, die mit diesem virtuellen Netzwerk verbunden sind.

	- **Auf einen Blick:** Hier finden Sie schnell die folgenden Informationen:

		- **Client-VPN-Paket herunterladen:** Dieses Paket wird beim Erstellen von standortübergreifenden Punkt-zu-Site-VPN-Verbindungen verwendet. Laden Sie das Paket herunter, das zum Client gehört.

		- **VPN-Geräteskript herunterladen:** Diese Vorlagen dienen als Unterstützung beim Konfigurieren von VPN-Geräten für standortübergreifende Site-to-Site-Verbindungen.

		- **Status**

		- **Abonnement-ID**
		
		- **ID des virtuellen Netzwerks**
		
		- **Speicherort**

1. Klicken Sie auf **Konfigurieren**, um die folgenden Informationen anzuzeigen:

	- **DNS-Server:** Dies sind die DNS-Server und die dazugehörigen IP-Adressen, die von den Clients in Ihrem virtuellen Netzwerk für die Namensauflösung verwendet werden.

	- **Punkt-zu-Site-Konnektivität:** Ist sichtbar, wenn Sie Ihr virtuelles Netzwerk für Punkt-zu-Site-Verbindungen konfiguriert haben.

	- **Site-to-Site-Konnektivität:** Ist sichtbar, wenn Sie Ihr virtuelles Netzwerk für Site-to-Site-Verbindungen konfiguriert haben.

	- **Lokales Netzwerk:** Wird angezeigt, wenn Sie über Site-to-Site-Konnektivität verfügen. Dies ist das ausgewählte lokale Netzwerk, das Ihren lokalen Standort darstellt. Sie können die Einstellungen für dieses lokale Netzwerk auf der Seite **Lokale Netzwerke** unter **Netzwerke** konfigurieren.
	
	- **Adressräume von Virtual Network:** Dies sind der Adressraum und die Subnetze Ihres virtuellen Netzwerks.

1. Klicken Sie auf **Zertifikate**, um die folgenden Informationen anzuzeigen:

	- **Hochgeladenes Stammzertifikat:** Wird für Punkt-zu-Site-VPN-Konfigurationen verwendet.
	
	- ** Zertifikatfingerabdruck**
	
	- **Ablauf des Zertifikats**
	
	- **Hochladen:** Hier können Sie ein Stammzertifikat hochladen.
	
	- **Löschen:** Hier können Sie ein ausgewähltes Stammzertifikat löschen.

## Bearbeiten von VNet-Einstellungen

Nach der Bereitstellung des virtuellen Netzwerks können nur bestimmte Einstellungen geändert werden. Die folgenden Schritte dienen Ihnen als Hilfe beim Bearbeiten der Konfigurationseinstellungen Ihres virtuellen Netzwerks mit dem Verwaltungsportal.

1. Melden Sie sich am Verwaltungsportal an.

1. Klicken Sie im Navigationsbereich auf **Netzwerke**, und klicken Sie dann in der Spalte **Name** auf den Namen des virtuellen Netzwerks.

1. Klicken Sie auf **Konfigurieren**.

	- Unter **DNS-Server** können Sie zusätzliche DNS-Server für die Namensauflösung hinzufügen. Wenn Sie zusätzliche DNS-Server hinzufügen, nachdem Sie virtuelle Maschinen für Ihr virtuelles Netzwerk bereitgestellt haben, müssen Sie jeden virtuellen Computer neu starten. Dies ist erforderlich, damit der neue zusätzliche DNS-Server erkannt wird.
	
	- Im Abschnitt **Punkt-zu-Site-Konnektivität** können Sie den Adressraum konfigurieren, den Sie für Punkt-zu-Site-Verbindungen festlegen möchten.
	
	- Im Abschnitt **Site-to-Site-Konnektivität** können Sie Site-to-Site-Einstellungen konfigurieren.
	
	- Im Abschnitt **Adressräume von Virtual Network** können Sie zusätzliche Adressräume und Subnetze hinzufügen. Klicken Sie zum Hinzufügen von Subnetzen auf **Subnetze hinzufügen**, und konfigurieren Sie den Adressraum, den Sie verwenden möchten. Klicken Sie zum Hinzufügen eines Gatewaysubnetzes für die standortübergreifende Konnektivität auf **Gatewaysubnetz hinzufügen**. Sie können pro virtuellem Netzwerk nur ein Gatewaysubnetz hinzufügen.

1. Klicken Sie am unteren Bildschirmrand auf **SPEICHERN**, um die Konfigurationsänderungen zu speichern.

## Nächste Schritte

[Gewusst wie: Verwalten von DNS-Servern, die von einem virtuellen Netzwerk (VNet) verwendet werden](../virtual-networks-manage-dns-in-vnet)

[Verwenden von öffentlichen IP-Adressen in einem virtuellen Netzwerk](../virtual-networks-public-ip-within-vnet)

[Gewusst wie: Löschen eines virtuellen Netzwerks (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=August15_HO6-->