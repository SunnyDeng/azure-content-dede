<properties 
   pageTitle="Registrieren von Hostnamen mithilfe von dynamischem DNS"
   description="Hier wird erläutert, wie Sie das dynamische DNS einrichten, um Hostnamen in Ihren eigenen DNS-Servern zu registrieren."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Registrieren von Hostnamen mithilfe von dynamischem DNS
[Azure bietet eine Namensauflösung](virtual-networks-name-resolution-for-vms-and-role-instances.md) für virtuelle Computer und Rolleninstanzen. Wenn Ihre Namensauflösung jedoch mehr leisten muss als die Azure-Namensauflösung ermöglicht, können Sie eigene DNS-Server bereitstellen. Auf diese Weise können Sie eine maßgeschneiderte DNS-Lösung für Ihre speziellen Anforderungen erstellen. Beispielsweise können Sie wie gewünscht auf Ihren lokalen Active Directory-Domänencontroller zugreifen.

Azure verfügt nicht über die Mittel und Möglichkeiten (z. B. Anmeldeinformationen), um Einträge direkt in Ihren DNS-Servern zu registrieren. Daher sind meist andere Vorkehrungen nötig. Im Folgenden werden einige häufigere Szenarien allgemein erörtert.

## Windows-Clients ##
Nicht per Domänenbeitritt in eine Domäne eingebundene Windows-Clients versuchen beim Starten oder bei IP-Adressänderungen, ungesicherte DDNS-Aktualisierungen (Dynamic DNS, dynamisches DNS) vorzunehmen. Der DNS-Name besteht aus dem Hostnamen und dem primären DNS-Suffix. Azure lässt das primäre DNS-Suffix leer, aber dies kann im virtuellen Computer entweder über die [Benutzeroberfläche](https://technet.microsoft.com/library/cc794784.aspx) oder [durch Automatisierung](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix) außer Kraft gesetzt werden.

Per Domänenbeitritt in die Domäne eingebundene Windows-Clients registrieren ihre IP-Adressen mithilfe von sicherem DDNS beim Domänencontroller. Im Zuge des Domänenbeitritts wird das primäre DNS-Suffix auf dem Client festgelegt, und die Vertrauensstellung wird verwaltet.

## Linux-Clients ##
Linux-Clients registrieren sich beim Start in der Regel nicht selbst beim DNS-Server. Es wird davon ausgegangen, dass der DHCP-Server dies übernimmt. Das Blind-Paket enthält ein Tool namens *nsupdate*, mit dem DDNS-Aktualisierungen gesendet werden können. Da das DDNS-Protokoll standardisiert ist, kann *nsupdate* auch dann verwendet werden, wenn Bind auf dem DNS-Server nicht verwendet wird.

Der Linux-DHCP-Client bietet Hooks, die es Ihnen ermöglichen, beim Zuweisen oder Ändern einer IP-Adresse Skripts auszuführen. Zum Beispiel in */etc/dhcp/dhclient-exit-hooks.d/*. Diese Möglichkeit kann zum Registrieren des Hostnamens im DNS verwendet werden. Beispiel:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

Mit dem Befehl *nsupdate* können auch sichere DDNS-Aktualisierungen ausgeführt werden. Wenn Sie z. B. einen Bind-DNS-Server verwenden, wird ein Schlüsselpaar aus öffentlichem und privatem Schlüssel [generiert](http://linux.yyz.us/nsupdate/), und der DNS Server wird mit dem öffentlichen Teil des Schlüssels so [konfiguriert](http://linux.yyz.us/dns/ddns-server.html), dass er die Signatur der Anforderung überprüfen kann. Damit eine DDNS-Aktualisierungsanforderung signiert wird, müssen Sie das Schlüsselpaar an *nsupdate* mit der Option *-k* übergeben.

Wenn Sie einen Windows-DNS-Server verwenden, können Sie die Kerberos-Authentifizierung mit dem Switch *-g* von „nsupdate“ verwenden (nicht verfügbar in der Windows-Version von *nsupdate*). Zu diesem Zweck laden Sie mithilfe von *kinit* die Anmeldeinformationen (z. B. aus der [keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)-Datei), und anschließend übernimmt *nsupdate -g* die Anmeldeinformationen aus dem Cache.

Bei Bedarf können Sie Ihren virtuellen Computern ein DNS-Suchsuffix hinzufügen. Das DNS-Suffix wird in der Datei */etc/resolv.conf* angegeben. In den meisten Linux-Distributionen wird der Inhalt dieser Datei automatisch verwaltet. Daher lässt sie sich in der Regel nicht bearbeiten. Das Suffix kann jedoch mit dem *supersede*-Befehl des DHCP-Clients überschrieben werden. Fügen Sie dazu in */etc/dhcp/dhclient.conf* Folgendes hinzu:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->