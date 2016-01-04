<properties
	pageTitle="Einfügen benutzerdefinierter Daten in virtuelle Computer | Microsoft Azure"
	description="Dieses Thema beschreibt das Einfügen von benutzerdefinierten Daten in einem virtuellen Azure-Computer, wenn die Instanz erstellt wird, und das Suchen der benutzerdefinierten Daten auf Windows oder Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="rasquill"/>


#Einfügen benutzerdefinierter Daten in einen virtuellen Azure-Computer

Das Einfügen eines Skripts oder anderer Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird, ist ein sehr häufiges Szenario, unabhängig davon, ob es sich bei dem Betriebssystem um Windows oder eine Linux-Distribution handelt.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In diesem Thema wird Folgendes beschrieben:

- Das Einfügen von Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird.

- Das Abrufen des virtuellen Computers für Windows und Linux.

- Verwenden von speziellen Tools, die auf einigen Systemen zum automatischen Ermitteln und Verarbeiten benutzerdefinierter Daten verfügbar sind.

> [AZURE.NOTE]Dieser Artikel beschreibt, wie benutzerdefinierte Daten mithilfe eines virtuellen Computers, der mit der Azure-Dienstverwaltungs-API erstellt wurde, injiziert werden können. Informationen zur Verwendung der Azure-Dienstverwaltungs-API finden Sie in [der Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Einfügen benutzerdefinierter Daten in einem virtuellen Azure-Computer

Dieses Feature wird derzeit nur in der [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-xplat-cli) unterstützt. Sie können für den Befehl `azure vm create` jede der Optionen verwenden. Der folgende Ansatz ist sehr allgemein gehalten.

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

+ Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Linux-Computer handelt, befindet sich die benutzerdefinierte Datendatei an den folgenden zwei Orten. Die Daten sind jedoch base64-codiert, sodass Sie sie zunächst decodieren müssen.

    + Unter `/var/lib/waagent/ovf-env.xml`
    + Unter `/var/lib/waagent/CustomData`



## Cloud-Init in Azure

Wenn Ihr virtueller Azure-Computer von einem Ubuntu- oder CoreOS-Image erstellt wurde, können Sie eine cloud-config-Datei an Cloud-Init senden. Wenn Ihre benutzerdefinierte Datendatei ein Skript ist, kann dieses einfach von Cloud-Init ausgeführt werden.

### Ubuntu-Cloud-Images

In den meisten Azure Linux-Images bearbeiten Sie "/ /etc/waagent.conf", um den temporären Ressourcendatenträger und die Auslagerungsdatei zu konfigurieren. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md).

Bei Ubuntu-Cloud-Images muss zum Konfigurieren des Ressourcendatenträgers (auch als "kurzlebiger" Datenträger bezeichnet) und der Swap-Partition jedoch Cloud-Init verwendet werden. Weitere Details finden Sie auf der folgenden Seite im Ubuntu-Wiki: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte: Verwenden von Cloud-Init

Weitere Informationen finden Sie unter [cloud-init documentation for Ubuntu](https://help.ubuntu.com/community/CloudInit) (cloud-init-Dokumentation für Ubuntu) (in englischer Sprache).

<!--Link references-->
[REST-API-Referenz zur Dienstverwaltung mit "Add Role"](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_1217_2015-->