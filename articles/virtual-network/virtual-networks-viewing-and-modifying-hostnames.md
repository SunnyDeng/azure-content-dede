<properties 
   pageTitle="Anzeigen und Ändern von Hostnamen"
	description="Beschreibung"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/25/2015"
	ms.author="joaoma"/>

# Anzeigen und Ändern von Hostnamen

Damit auf Ihre Rolleninstanzen mit dem Hostnamen verwiesen werden kann, müssen Sie den Wert für den Hostnamen in der Dienstkonfigurationsdatei für jede Rolle festlegen. Hierzu fügen Sie den gewünschten Hostnamen dem **vmName**-Attribut des **Role**-Elements hinzu. Der Wert des **vmName**-Attributs wird als Grundlage für den Hostnamen der einzelnen Rolleninstanzen verwendet. Wenn **vmName** beispielsweise *webrole* lautet und drei Instanzen dieser Rolle vorhanden sind, lauten die Hostnamen der Instanzen *webrole0*, *webrole1* und *webrole2*. Sie müssen keinen Hostnamen für virtuelle Computer in der Konfigurationsdatei angeben, da der Hostname für einen virtuellen Computer auf Grundlage des Namens des virtuellen Computers erstellt wird. Weitere Informationen zum Konfigurieren eines Microsoft Azure-Diensts finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## Anzeigen von Hostnamen

Sie können die Hostnamen von virtuellen Computern und Rolleninstanzen in einem Clouddienst mit verschiedenen Tools anzeigen: Azure-Portal, Dienstkonfigurationsdatei, Remotedesktop und [REST-API der Azure-Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Azure-Portal

Über das Azure-Portal können Sie die Hostnamen für virtuelle Computer auf der Dashboardseite für die virtuellen Computer anzeigen. Beachten Sie, dass im Dashboard ein Wert für **Name** und ein Wert für **Hostname** angezeigt wird. Diese sind zu Beginn identisch, eine Änderung des Hostnamens führt jedoch nicht dazu, dass auch der Name des virtuellen Computers oder der Rolleninstanz geändert wird.

Rolleninstanzen können auch im Azure-Portal aufgeführt werden, wenn Sie die Instanzen jedoch in einem Clouddienst aufführen, wird der Hostname nicht angezeigt. Es wird ein Name für jede Instanz angezeigt, dieser Name stellt jedoch nicht den Hostnamen dar.

### Dienstkonfigurationsdatei

Sie können die Dienstkonfigurationsdatei für einen bereitgestellten Dienst auf der Seite **Konfigurieren** im Azure-Portal herunterladen. Dann können Sie das **vmName**-Attribut für das **Role name**-Element suchen, um den Hostnamen anzuzeigen. Beachten Sie, dass dieser Hostname als Grundlage für den Hostnamen der einzelnen Rolleninstanzen verwendet wird. Wenn **vmName** beispielsweise *webrole* lautet und drei Instanzen dieser Rolle vorhanden sind, lauten die Hostnamen der Instanzen *webrole0*, *webrole1* und *webrole2*.

### Remotedesktop

Nach der Aktivierung von Verbindungen zu den virtuellen Computern oder Rolleninstanzen über Remotedesktop (Windows), Windows PowerShell-Remoting (Windows) oder SSH (Linux und Windows) haben Sie mehrere Möglichkeiten, um den Hostnamen aus einer aktiven Remotedesktopverbindung anzuzeigen:

- Geben Sie an der Eingabeaufforderung oder im SSH-Terminal "hostname" ein.

- Geben Sie "ipconfig /all" an der Eingabeaufforderung ein (nur Windows).

- Zeigen Sie den Computernamen in den Systemeinstellungen an (nur Windows).

### Azure-Dienstverwaltungs-REST-API

Gehen Sie auf einem REST-Client wie folgt vor:

1. Stellen Sie sicher, dass Sie über ein Clientzertifikat für die Verbindung zum Azure-Portal verfügen. Um ein Clientzertifikat zu erhalten, führen Sie die Schritte unter [Gewusst wie: Herunterladen und Importieren von Veröffentlichungseinstellungen und Abonnementinformationen] (https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)) aus.

1. Legen Sie einen Headereintrag mit dem Namen "x-ms-version" mit einem Wert von "2013-11-01" fest.

1. Senden Sie eine Anforderung im folgenden Format: https://management.core.windows.net/<Abonnements-ID>/services/hostedservices/<Dienstname>?embed-detail=true

1. Suchen Sie das **HostName**-Element für jedes **RoleInstance**-Element.

>[AZURE.WARNING]Sie können auch das interne Domänensuffix für Ihren Clouddienst über die Antwort auf den REST-Aufruf anzeigen, indem Sie das **InternalDnsSuffix**-Element überprüfen. Alternativ können Sie "ipconfig /all" an einer Eingabeaufforderung in einer Remotedesktopsitzung (Windows) oder "running cat /etc/resolv.conf" in einem SSH-Terminal ausführen (Linux).

## Ändern eines Hostnamens

Sie können den Hostnamen für einen virtuellen Computer oder eine Rolleninstanz ändern, indem Sie eine geänderte Dienstkonfigurationsdatei hochladen oder den Computer in einer Remotedesktopsitzung umbenennen.

## Nächste Schritte

[Namensauflösung (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure-Dienstkonfigurationsschema (CSCFG)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Konfigurationsschema für virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/?LinkId=248093)

[Angeben von DNS-Einstellungen mit Netzwerkkonfigurationsdateien](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

<!---HONumber=August15_HO9-->