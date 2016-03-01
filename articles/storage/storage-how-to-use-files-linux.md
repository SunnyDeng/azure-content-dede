<properties
	pageTitle="Verwenden von Azure Files mit Linux | Microsoft Azure"
        description="Erstellen Sie mit diesem Schritt-für-Schritt-Tutorial eine Azure-Dateifreigabe in der Cloud. Verwalten Sie Ihre freigegebenen Inhalte und stellen Sie eine Dateifreigabe aus einem virtuellen Azure-Computer (VM) unter Linux oder einer lokalen Anwendung bereit, die SMB 3.0 unterstützt."
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="02/15/2016"
      ms.author="jutang" />


# Verwenden des Azure-Dateispeichers unter Linux

## Übersicht

Der Azure-Dateispeicher verfügt über Dateifreigaben in der Cloud unter Verwendung des standardmäßigen SMB-Protokolls. Mit Azure Files können Sie Unternehmensanwendungen migrieren, die auf Dateiservern in Azure basieren. Anwendungen, die in Azure ausgeführt werden, können Dateifreigaben von virtuellen Azure-Computern unter Linux problemlos einbinden. Mit der neuesten Version des Dateispeichers können Sie außerdem eine Dateifreigabe aus einer lokalen Anwendung einbinden, die SMB 3.0 unterstützt.

Sie können Azure-Dateifreigaben mit dem [Azure-Portal](https://portal.azure.com), den Azure Storage-PowerShell-Cmdlets, den Azure Storage-Clientbibliotheken oder der Azure Storage-REST-API erstellen. Da es sich bei den Dateifreigaben um SMB-Freigaben handelt, können Sie darauf außerdem über standardmäßige Dateisystem-APIs zugreifen.

Der Dateispeicher beruht auf der gleichen Technologie wie Blob-, Tabellen-, und Warteschlangendienste. Dies bedeutet, dass der Dateispeicher die Verfügbarkeit, Dauerhaftigkeit, Skalierbarkeit und geografische Redundanz bietet, die in die Azure Storage-Plattform integriert ist. Weitere Informationen zu Leistungszielen und Grenzwerten des Dateispeichers finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md).

Der Dateispeicher ist jetzt allgemein verfügbar und unterstützt sowohl SMB 2.1 als auch SMB 3.0. Zusätzliche Informationen zu File Storage finden Sie unter [Dateidienst-REST-API](https://msdn.microsoft.com/library/azure/dn167006.aspx).

>[AZURE.NOTE] Weil der Linux-SMB-Client noch keine Verschlüsselung unterstützt, muss sich der Client, wenn eine Dateifreigabe aus Linux eingebunden wird, in derselben Azure-Region befinden wie die Dateifreigabe. Allerdings arbeiten die Linux-Entwickler, die für die SMB-Funktionalität verantwortlich sind, an der Verschlüsselungsunterstützung für Linux. Linux-Distributionen, in denen zukünftig Verschlüsselung unterstützt wird, werden ebenfalls von überall eine Azure-Dateifreigabe einbinden können.

## Video: Verwenden des Azure-Dateispeichers unter Linux

Dieses Video veranschaulicht das Erstellen und Verwenden von Azure-Dateifreigaben unter Linux.

> [AZURE.VIDEO azure-file-storage-with-linux]

## Wählen einer zu verwendenden Linux-Distribution ##

Wenn Sie in Azure einen virtuellen Linux-Computer erstellen, können Sie im Azure-Image-Katalog ein Linux-Image angeben, das SMB 2.1 oder höher unterstützt. Dies ist die Liste der empfohlenen Linux-Images:

- Ubuntu Server 14.04
- Ubuntu Server 15.04
- CentOS 7.1
- Open SUSE 13.2
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium-Image)

## Einbinden der Dateifreigabe ##

Um die Dateifreigabe aus einem virtuellen Linux-Computer einzubinden, müssen Sie möglicherweise einen SMB/CIFS-Client installieren, wenn die von Ihnen verwendete Distribution keinen integrierten Client hat. Dies ist der aus Ubuntu auszuführende Befehl, wenn cifs-utils als Client installiert werden soll:

    sudo apt-get install cifs-utils

Danach müssen Sie einen Bereitstellungspunkt (Einbindepunkt, mkdir mymountpoint) erstellen und schließlich den mount-Befehl ausgeben, der in etwa wie folgt aussieht:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Sie können auch Einstellungen in der Konfigurationsdatei „/etc/fstab“ hinzufügen, um die Freigabe einzubinden.

0777 entspricht hier einem Verzeichnis-/Dateiberechtigungscode, der allen Benutzern die Berechtigungen Ausführen/Lesen/Schreiben erteilt. Sie können diesen Code durch einen anderen Dateiberechtigungscode ersetzen. Weitere Informationen hierzu finden Sie in der Dokumentation zu Linux-Dateiberechtigungen.

Soll eine Dateifreigabe nach einem Neustart weiterhin eingebunden sein, fügen Sie der „/etc/fstab“ eine Einstellung hinzu, die so aussieht:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Wenn Sie z. B. mit dem Linux-Image Ubuntu Server 15.04, das im Azure-Imagekatalog verfügbar ist, einen virtuellen Azure-Computer erstellt haben, können Sie die Datei wie folgt einbinden:

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Wenn Sie die CentOS 7.1 verwenden, können Sie die Datei wie folgt einbinden:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Wenn Sie die Open SUSE 13.2 verwenden, können Sie die Datei wie folgt einbinden:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## Verwalten der Dateifreigabe ##

Das [Azure-Portal](https://portal.azure.com) bietet eine Benutzeroberfläche zum Verwalten von Azure File Storage. Sie können in Ihrem Webbrowser die folgenden Aktionen ausführen:

- Hoch- und Herunterladen von Dateien für die Dateifreigabe
- Überwachen der tatsächlichen Nutzung der einzelnen Dateifreigaben
- Anpassen des Kontingents für die Dateifreigabegröße
- Kopieren Sie den `net use`-Befehl, der zum Einbinden Ihrer Dateifreigabe auf einem Windows-Client verwendet werden soll.

Sie können auch die plattformübergreifende Azure-Befehlszeilenschnittstelle (Azure CLI) von Linux verwenden, um die Dateifreigabe zu verwalten. Die Azure-Befehlszeilenschnittstelle bietet eine Reihe plattformübergreifender Open-Source-Befehle für die Arbeit mit Azure Storage, einschließlich File Storage. Sie bietet im Wesentlichen die gleiche Funktionalität wie das Azure-Portal sowie umfangreiche Datenzugriffsfunktionen. Beispiele finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Storage](storage-azure-cli.md).

## Entwickeln mit Dateispeicher ##

Als Entwickler können Sie eine Anwendung mit File Storage erstellen, indem Sie die [Azure Storage-Clientbibliothek für Java](https://github.com/azure/azure-storage-java) verwenden. Codebeispiele finden Sie unter [Verwenden von File Storage aus Java](storage-java-how-to-use-file-storage.md).

Sie können auch die [Azure Storage-Clientbibliothek für Node.js](https://github.com/Azure/azure-storage-node) zum Entwickeln für File Storage verwenden.

## Feedback und weitere Informationen ##

Linux-Benutzer, wir möchten von Ihnen hören!

Der Gruppe "Azure File Storage for Linux users" bietet ein Forum, in dem Sie Ihr Feedback zu File Storage für Linux geben können. Senden Sie eine E-Mail an [Azure File Storage for Linux Users](mailto:azurefileslinuxusers@microsoft.com), um der Benutzergruppe beizutreten.

## Nächste Schritte

Weitere Informationen zum Azure-Dateispeicher erhalten Sie über diese Links.

### Konzeptionelle Artikel und Videos

- [Azure-Dateispeicher: ein reibungsloses Cloud-SMB-Dateisystem für Windows und Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Erste Schritte mit Azure File Storage unter Windows](storage-dotnet-how-to-use-files.md)

### Toolunterstützung für Dateispeicher

- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)
- [Erstellen und Verwalten von Dateifreigaben](storage-azure-cli.md#create-and-manage-file-shares) mithilfe der Azure-Befehlszeilenschnittstelle

### Referenz

- [Referenz zur REST-API des Dateidiensts](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Blogbeiträge

- [Azure-Dateispeicher ist jetzt allgemein verfügbar](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [Azure-Dateispeicher](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [Einführung in den Microsoft Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=AcomDC_0218_2016-->