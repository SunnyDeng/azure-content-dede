<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Injecting Custom Data into Azure Virtual Machines" description="This topic describes how to inject custom data into an Azure Virtual Machine when the instance is created and how to locate the custom data on either Windows or Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="dongill" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="rasquill"></tags>

# Einfügen benutzerdefinierter Daten in einen virtuellen Azure-Computer

Das Einfügen eines Skripts oder anderer Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird, ist ein sehr häufiges Szenario, unabhängig davon, ob es sich bei dem Betriebssystem um Microsoft Windows oder eine Linux-Distribution handelt. In diesem Thema wird Folgendes beschrieben:

-   Einfügen von Daten in einen virtuellen Azure-Computer, während dieser bereitgestellt wird

-   Abrufen des virtuellen Computers für Windows und Linux

-   Verwenden spezieller Tools, die auf einigen Systemen verfügbar sind, um benutzerdefinierte Daten automatisch zu erkennen und zu behandeln

> [WACOM.NOTE] Dieses Thema wird bei [diesem Azure-Blogbeitrag][diesem Azure-Blogbeitrag] zu dieser Funktion erweitert und auf dem neuesten Stand gehalten, sobald weitere Funktionen zur Verfügung gestellt werden.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Inhalt dieses Themas:

-   [Einfügen der benutzerdefinierten Daten in Ihren virtuellen Azure-Computer][Einfügen der benutzerdefinierten Daten in Ihren virtuellen Azure-Computer]

-   [Verwenden benutzerdefinierter Daten im virtuellen Computer][Verwenden benutzerdefinierter Daten im virtuellen Computer]

-   [Nächste Schritte][Nächste Schritte]

## <span id="injectingCustomData"></span></a>Einfügen der benutzerdefinierten Daten in Ihren virtuellen Azure-Computer

Diese Funktion wird derzeit nur für die [plattformübergreifende Befehlszeilenschnittstelle von Microsoft Azure][plattformübergreifende Befehlszeilenschnittstelle von Microsoft Azure] unterstützt. Obwohl Sie jede der Optionen für den Befehl `azure vm create` verwenden können, veranschaulicht der folgende Ansatz eine sehr einfache Vorgehensweise.

        PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
        VMNAME=mycustomdataubuntu
        USERNAME=username
        VMIMAGE= An image chosen from among those listed by azure vm image list
        azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22

## <span id="usingCustomData"></span></a>Verwenden benutzerdefinierter Daten im virtuellen Computer

-   Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Windows-Computer handelt, wird die benutzerdefinierte Datendatei in `%SYSTEMDRIVE%\AzureData\CustomData.bin` gespeichert. Obwohl sie für die Übertragung vom lokalen Computer auf den neuen virtuellen Computer eine Base64-Codierung erhalten hat, wird sie automatisch decodiert und kann sofort geöffnet oder verwendet werden.

> [WACOM.NOTE] Wenn die Datei vorhanden ist, wird sie überschrieben. Die Sicherheit für das Verzeichnis wird auf **System:Vollzugriff** und **Administratoren:Vollzugriff** festgelegt.

-   Wenn es sich bei Ihrem virtuellen Azure-Computer um einen virtuellen Linux-Computer handelt, befindet sich die Datendatei an einem der folgenden beiden Speicherorte, aber die Daten erhalten eine Base64-Codierung, daher müssen Sie die Daten zuerst decodieren.

    -   Unter `/var/lib/waagent/ovf-env.xml`
    -   Unter `/var/lib/waagent/CustomData`

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <span id="nextsteps"></span></a>Nächste Schritte: Verwenden von „cloud-init“

Wenn Ihr virtueller Azure-Computer ein Ubuntu-Abbild ist, können Sie „cloud-init“ verwenden, um ein Skript zu starten, damit Ihre benutzerdefinierten Daten automatisch verwendet werden (wenn es sich bei Ihrer benutzerdefinierten Datendatei um ein Skript handelt, führen Sie es aus). Weitere Informationen finden Sie in der [Dokumentation zu „cloud-init“ für Ubuntu][Dokumentation zu „cloud-init“ für Ubuntu].

<!--Link references-->

[Hinzufügen der Referenz zur REST-API für die Rollendienstverwaltung][Hinzufügen der Referenz zur REST-API für die Rollendienstverwaltung]

[Plattformübergreifende Microsoft Azure-Befehlszeilenschnittstelle][plattformübergreifende Befehlszeilenschnittstelle von Microsoft Azure]

  [diesem Azure-Blogbeitrag]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Einfügen der benutzerdefinierten Daten in Ihren virtuellen Azure-Computer]: #injectingCustomData
  [Verwenden benutzerdefinierter Daten im virtuellen Computer]: #usingCustomData
  [Nächste Schritte]: #nextsteps
  [plattformübergreifende Befehlszeilenschnittstelle von Microsoft Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [Dokumentation zu „cloud-init“ für Ubuntu]: https://help.ubuntu.com/community/CloudInit
  [Hinzufügen der Referenz zur REST-API für die Rollendienstverwaltung]: http://msdn.microsoft.com/library/azure/jj157186.aspx
