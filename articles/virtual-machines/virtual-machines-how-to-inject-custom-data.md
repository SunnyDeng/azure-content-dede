<properties
	pageTitle="Einfügen von benutzerdefinierten Daten in Azure Virtual Machines"
	description="Dieses Thema beschreibt das Einfügen von benutzerdefinierten Daten in einem virtuellen Azure-Computer, wenn die Instanz erstellt wird, und das Suchen der benutzerdefinierten Daten auf Windows oder Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2015"
	ms.author="rasquill"/>


#Einfügen von benutzerdefinierten Daten in einem virtuellen Azure-Computer

Das Einfügen eines Skripts oder anderer Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird, ist ein sehr häufiges Szenario, unabhängig davon, ob es sich bei dem Betriebssystem um Microsoft Windows oder eine Linux-Distribution handelt. In diesem Thema wird Folgendes beschrieben:

- Einfügen von Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird

- Abrufen des virtuellen Computers für Windows und Linux

- Verwenden von speziellen Tools, die auf einigen Systemen zum automatischen Ermitteln und Verarbeiten benutzerdefinierter Daten verfügbar sind.

> [AZURE.NOTE]Im vorliegenden Artikel wird auf [diesen Azure-Blogbeitrag](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) über dieses Feature eingegangen. Der Artikel wird regelmäßig aktualisiert, sobald weitere Funktionen vorhanden sind.

## Einfügen benutzerdefinierter Daten in einem virtuellen Azure-Computer

Dieses Feature wird derzeit nur in der [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-sdk-tools-xplat) unterstützt. Sie können für den Befehl `azure vm create` jede der Optionen verwenden. Der folgende Ansatz ist sehr allgemein gehalten.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Verwenden von benutzerdefinierten Daten im virtuellen Computer

+ Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Windows-Computer handelt, wird die benutzerdefinierte Datendatei unter `%SYSTEMDRIVE%\AzureData\CustomData.bin` gespeichert. Auch wenn sie für die Übertragung vom lokalen Computer zum neuen virtuellen Computer base64-codiert war, wird sie automatisch decodiert und kann sofort geöffnet oder verwendet werden.

   >[AZURE.NOTE]Wenn die Datei vorhanden ist, wird sie überschrieben. Die Sicherheit im Verzeichnis ist auf **System:Full Control** und **Administrators:Full Control** festgelegt.

+ Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Linux-Computer handelt, befindet sich die benutzerdefinierte Datendatei an den folgenden zwei Orten, die Daten sind jedoch base64-codiert. Daher müssen Sie die Daten zunächst decodieren.

    + Unter `/var/lib/waagent/ovf-env.xml`
    + Unter `/var/lib/waagent/CustomData`



## Cloud-Init in Azure

Wenn Ihr virtueller Azure-Computer von einem Ubuntu- oder CoreOS-Image erstellt wurde, können Sie eine cloud-config-Datei an Cloud-Init senden. Wenn Ihre benutzerdefinierte Datendatei ein Skript ist, kann dieses einfach von Cloud-Init ausgeführt werden.

### Ubuntu-Cloud-Images

In den meisten Azure Linux-Images bearbeiten Sie "/ /etc/waagent.conf", um den temporären Ressourcendatenträger und die Auslagerungsdatei zu konfigurieren. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux Agent](virtual-machines-linux-agent-user-guide.md).

Bei Ubuntu-Cloud-Images muss zum Konfigurieren des Ressourcendatenträgers (auch als "kurzlebiger" Datenträger bezeichnet) und der Swap-Partition jedoch Cloud-Init verwendet werden. Weitere Details finden Sie auf der folgenden Seite im Ubuntu-Wiki:

 - [Ubuntu-Wiki: Konfigurieren von Swap-Partitionen](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409) (in englischer Sprache)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte: Verwenden von Cloud-Init

Weitere Informationen finden Sie unter [cloud-init documentation for Ubuntu](https://help.ubuntu.com/community/CloudInit) (cloud-init-Dokumentation für Ubuntu) (in englischer Sprache).

<!--Link references-->
[REST-API-Referenz zur Dienstverwaltung mit "Add Role"](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-sdk-tools-xplat)
 

<!---HONumber=July15_HO1-->