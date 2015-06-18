<properties 
	pageTitle="Erstellen eines standortübergreifenden virtuellen Netzwerks mithilfe von Konfigurationstabellen" 
	description="In diesem Thema wird beschrieben, wie ein standortübergreifendes virtuelles Netzwerk mithilfe vordefinierter Konfigurationstabellen konfiguriert wird." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="josephd"/>

# Erstellen eines standortübergreifenden virtuellen Netzwerks mithilfe von Konfigurationstabellen

In diesem Thema lernen Sie Schritt für Schritt, wie ein standortübergreifendes virtuelles Netzwerk mit den Einstellungen erstellt wird, die zuvor in den folgenden Konfigurationstabellen festgelegt wurden:

- Tabelle V: Konfiguration eines standortübergreifenden virtuellen Netzwerks
- Tabelle S: Subnetze des virtuellen Netzwerks
- Tabelle D: Lokale DNS-Server
- Tabelle L: Adresspräfixe für das lokale Netzwerk

Diese Tabellen werden in der Regel in einem Thema ausgefüllt, in dem die Konfiguration einer IT-Arbeitsauslastung in Azure beschrieben wird, die auch ein standortübergreifendes virtuelles Netzwerk umfasst. Ein Beispiel finden Sie unter [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

In den nachfolgend dargestellten Schritten, die Sie durch die Konfiguration des virtuellen Netzwerks führen, wird auf die Informationen in diesen Tabellen verwiesen. Wenn Sie die Einstellungen in diesen Tabellen in noch keinem Thema angegeben haben, aber dennoch ein standortübergreifendes virtuelles Netzwerk konfigurieren möchten, lesen Sie das Thema [Configure a Cross-Premises Site-to-Site connection to an Azure Virtual Network](https://msdn.microsoft.com/library/dn133795.aspx) (Konfiguration einer standortübergreifenden Site-to-Site-Verbindung zu einem virtuellen Azure-Netzwerk).

> [AZURE.NOTE]Dieses Verfahren führt Sie durch die Erstellung eines virtuellen Netzwerks, das eine Site-to-Site-VPN-Verbindung verwendet. Informationen zur Verwendung von ExpressRoute für Ihre Site-to-Site-Verbindung finden Sie unter [ExpressRoute – technische Übersicht](https://msdn.microsoft.com/library/dn606309.aspx).
 
## Erstellen eines neuen standortübergreifenden virtuellen Azure-Netzwerks mit den Einstellungen Ihrer Konfigurationstabellen

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Klicken Sie in der Taskleiste auf **Neu > Netzwerkdienste > Virtuelles Netzwerk > Benutzerdefiniert erstellen**.
3. Auf der Seite „Details zum virtuellen Netzwerk“:
- Geben Sie unter **Name** den Namen von Element 1 aus Tabelle V ein.
- Wählen Sie unter **Standort** die Region von Element 2 aus Tabelle V aus. 
4. Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.
5. Auf der Seite „DNS-Server und VPN-Konnektivität“
- Konfigurieren Sie unter **DNS-Server** für jeden Eintrag in Tabelle D den Anzeigenamen und die IP-Adresse Ihrer lokalen DNS-Server.
- Wählen Sie unter **Site-to-Site-Konnektivität** die Option **Site-to-Site-VPN konfigurieren** aus.
- Wenn Sie bereits ein lokales Netzwerk konfiguriert haben und dieses verwenden möchten, wählen Sie seinen Namen unter **Lokales Netzwerk** aus. Wenn Sie ein neues lokales Netzwerk erstellen möchten, wählen Sie unter **Lokales Netzwerk** die Option **Geben Sie ein neues lokales Netzwerk an** aus.
- Wenn Sie für Ihr Abonnement noch kein lokales Netzwerk konfiguriert haben, wird das Feld **Lokales Netzwerk** nicht angezeigt. 
6. Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.
7. Auf der Seite „Site-to-Site-Konnektivität“ (bei Auswahl von **Geben Sie ein neues lokales Netzwerk an** in Schritt 5):
- Geben Sie unter **Name** den Namen von Element 3 aus Tabelle V ein (Name des lokalen Netzwerks).
- Geben Sie unter **IP-Adresse des VPN-Geräts** die Adresse von Element 4 aus Tabelle V ein.
- Geben Sie unter **Adressraum** für jeden Eintrag in Tabelle L die IP-Adressräume Ihres Unternehmensnetzwerks als Präfix (in **Start-IP-**) und Präfixlänge (in **CIDR (Anzahl Adressen)**) ein.
8. Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.
9. Gehen Sie auf der Seite „Virtuelle Netzwerkadressräume“ folgendermaßen vor:
- Geben Sie unter **Adressraum** den privaten IP-Adressraum des virtuellen Netzwerks von Element 5 aus Tabelle V als Präfix (in **Start-IP**) und Präfixlänge (in **CIDR (Anzahl Adressen)**) ein.
- Unter **Subnetze** für jeden Eintrag in Tabelle S:
	- Geben Sie in der Spalte **Subnetze** statt des Standardnamens den Namen des Subnetzes ein.
	- Geben Sie den privaten IP-Adressraum des Subnetzes als Präfix (in **Start-IP**) und Präfixlänge (in **CIDR (Anzahl Adressen)**) ein.
- Klicken Sie auf **Gatewaysubnetz hinzufügen**.
10. Klicken Sie auf das Häkchen, um die Konfiguration abzuschließen.

## Zusätzliche Ressourcen

[Virtuelle Netzwerke im Überblick](https://msdn.microsoft.com/library/jj156007.aspx)

[Konfigurationsaufgaben für virtuelle Netzwerke](https://msdn.microsoft.com/library/jj156206.aspx)

[Configure a Cross-Premises Site-to-Site connection to an Azure Virtual Network](https://msdn.microsoft.com/library/dn133795.aspx) (Konfiguration einer standortübergreifenden Site-to-Site-Verbindung zu einem virtuellen Azure-Netzwerk)

<!--HONumber=54--> 