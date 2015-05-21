<properties
   pageTitle="Bereitstellen einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows"
   description="Beschreibt die grundlegenden Schritte zum Bereitstellen oder Aktualisieren von Vorlagen."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="04/21/2015"
   ms.author="rasquill"/>

#Bereitstellen einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

##Installieren von curl, wget und anderen Dowloadprogrammen
In diesem Thema wird **curl** verwendet. Verwenden Sie den Paket-Manager Ihres Betriebssystems, oder laden Sie es [hier](http://curl.haxx.se/download.html) herunter.

##Herunterladen der Vorlagenparameterdatei \(oder ggf. der Vorlage\)

Die folgenden Schritte ermöglichen das Bereitstellen einer Azure-Vorlage, auch einer sehr komplexen. In diesem Thema wird als Beispiel die Vorlage zum Erstellen eines grundlegenden Ubuntu-Servers mit der CustomScript-Erweiterung für virtuelle Computer installiert. Die Dateien sind zu Referenzzwecken auch am Ende des Themas angefügt.

###Herunterladen der Datei "azuredeploy-parameters.json"

Laden Sie die Datei "azuredeploy parameters.json" herunter, sofern eine solche für die Vorlage, die Sie bereitstellen möchten, vorhanden ist.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json
    
##Angeben der Bereitstellungsinformationen für die Ressourcengruppe
    
Öffnen Sie diese Datei mit Ihrem bevorzugten Editor. Sie müssen einen Wert für mehrere Schlüssel angeben. Insbesondere müssen Sie den **adminUsername**, das **adminPassword** \(beachten Sie die Komplexitätsregeln!\) sowie den Namen des Speicherkontos und die gewünschten DNS-Namen bereitstellen.
    
    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-de-de-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }
    
Sie können neue Werte hinzufügen – Azure erstellt dann neue Speicher- und DNS-Ressourcen für Sie \(sofern möglich\) – oder bereits erstellte Ressourcen verwenden. Die folgende Datei "azuredeploy-parameters.json" stellt ein Beispiel dar:




Die folgende URL ruft die Parameterdatei aus der leeren Datei "azuredeploy-parameters.json" ab, die mit der interaktiven Methode verwendet werden kann. Bei der Downloadmethode mit benutzerdefinierter Parameterdatei müssen Sie stattdessen die Option mit einer Vorlagendatei <template-file> verwenden. Ich habe einige Skripts geschrieben, die einzelne Abschnitte eines beliebigen Teils aus diesen Dateien extrahieren – und zwar abhängig davon, was Sie vorhaben. Sie benötigen für das Analysieren von json noch jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq.


###Bereitstellen Ihrer Vorlagen- und Parameterdateien


[AZURE.NOTE]Für einige Vorlagen gibt es möglicherweise keine zugehörige Datei "azuredeploy parameters.json".

Parameter können festgelegt werden, oder sie sind bereits Teil der Vorlage selbst, je nachdem, welche Vorlagen Sie verwenden. In diesen Fällen haben Sie verschiedene Möglichkeiten.

Wenn Ihre Vorlage Parameter direkt enthält oder Sie die Parameterdaten selbst extrahieren möchten, finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx) weiterführende Informationen zur Struktur von Vorlagen.


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json \(oder nur die Datei "azuredeploy parameters.json"\). Sie können den Parameterabschnitt aus der Vorlage extrahieren – in diesem Fall müssen Sie ihn wieder in die Vorlage selbst oder als separate Datei "azuredeploy-parameters.json" speichern. Sie benötigen die Werte, die Sie in die Parameter einfügen möchten.

##Ändern der Datei "azuredeploy-templates.json"

Erfassen Sie die Werte, die Sie benötigen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##Nächste Schritte

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [Informationen zu Azure-Speicherkonten](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--HONumber=52-->
