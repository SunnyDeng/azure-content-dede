<properties pageTitle="Einfügen von benutzerdefinierten Daten in Azure Virtual Machines" description="Dieses Thema beschreibt das Einfügen von benutzerdefinierten Daten in einem virtuellen Azure-Computer, wenn die Instanz erstellt wird, und das Suchen der benutzerdefinierten Daten auf Windows oder Linux." services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="rasquill"/>



#Einfügen von benutzerdefinierten Daten in einem virtuellen Azure-Computer 

Das Einfügen eines Skripts oder anderer Daten in einem virtuellen Azure-Computer, wenn er bereitgestellt wird, ist ein äußerst allgemeines Szenario, unabhängig davon, ob es sich beim Betriebssystem um Microsoft Windows oder eine Linux-Verteilung handelt. In diesem Thema wird Folgendes beschrieben:

- Einfügen von Daten in einem virtuellen Azure-Computer, wenn er bereitgestellt wird

- Abrufen des zuvor genannten für Windows und Linux und 

- Verwenden von speziellen Tools, die auf einigen Systemen zum automatischen Ermitteln und Verarbeiten benutzerdefinierter Daten vorfügbar sind.

> [AZURE.NOTE] Dieses Thema erweitert [diesen Azure-Blogbeitrag](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) über dieses Feature und wird regelmäßig aktualisiert, sobald weitere Funktionen vorhanden sind.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
In diesem Thema:

+ [Einfügen benutzerdefinierter Daten in einem virtuellen Azure-Computer](#injectingCustomData)

+ [Verwenden von benutzerdefinierten Daten im virtuellen Computer](#usingCustomData)

+ [Nächste Schritte](#nextsteps)


## <a id="injectingCustomData"></a>Einfügen benutzerdefinierter Daten in einem virtuellen Azure-Computer

Dieses Feature wird zurzeit nur in der [Microsoft Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-sdk-tools-xplat) unterstützt Auch wenn Sie jede der Optionen für den Befehl  `azure vm create` verwenden können, ist der folgende Ansatz sehr allgemein gehalten. 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>Verwenden von benutzerdefinierten Daten im virtuellen Computer
 
+ Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Computer unter Windows handelt, wird die benutzerdefinierte Datendatei in  `%SYSTEMDRIVE%\AzureData\CustomData.bin` gespeichert, und auch wenn sie base64-codiert war, um eine Übertragung vom lokalen Computer zum neuen virtuellen Computer vorzunehmen, wird sie automatisch decodiert und kann sofort geöffnet oder verwendet werden. 

   > [AZURE.NOTE] Wenn die Datei vorhanden ist, wird sie überschrieben. Die Sicherheit im Verzeichnis ist auf **System:Full Control** und **Administrators:Full Control** festgelegt.

+ Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Linux-Computer handelt, befindet sich die benutzerdefinierte Datendatei an den folgenden zwei Orten, die Daten sind jedoch base64-codiert. Daher müssen Sie die Daten zunächst decodieren.

    + At `/var/lib/waagent/ovf-env.xml`
    + At `/var/lib/waagent/CustomData` 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>Nächste Schritte: Verwenden von cloud-init

Wenn es sich bei Ihrem virtuellen Azure-Computer um ein Ubuntu-Image handelt, können Sie cloud-init zum Starten eines Skripts für die automatische Verwendung (oder Ausführung, wenn Ihre benutzerdefinierte Datei ein Skript ist) Ihrer benutzerdefinierten Daten verwenden. Weitere Informationen finden Sie in der [cloud-init-Dokumentation für Ubuntu](https://help.ubuntu.com/community/CloudInit) (in englischer Sprache).

<!--Link references-->
[Add Role Service Management REST API-Verweis](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Microsoft Azure Cross-Platform Command-line Interface](https://github.com/Azure/azure-sdk-tools-xplat) (Plattformübergreifende Microsoft Azure-Befehlszeilenschnittstelle, in englischer Sprache)





<!--HONumber=42-->
