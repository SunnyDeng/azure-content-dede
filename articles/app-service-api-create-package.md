<properties 
	pageTitle="Erstellen eines API-App-Pakets" 
	description="Erfahren Sie, wie Sie ein API-App-Paket erstellen." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Erstellen eines API-App-Pakets

## Übersicht

Dieser Artikel zeigt, wie Sie ein API-App-Paket erstellen, sodass es im [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) veröffentlicht werden kann.

- Informationen zum Erstellen einer API-App finden Sie unter [Erstellen einer API-App mit Visual Studio](app-service-dotnet-create-api-app.md)
- Informationen zum Veröffentlichen eines API-App-Pakets im Azure Marketplace finden Sie unter [Veröffentlichen eines API-App-Pakets im Azure Marketplace](app-service-api-publish-package).

## Ordnerstruktur

Ein Nuget-Paket (*.nupkg*-Datei) für eine API-App weist die folgenden Dateien und Ordner unterhalb des Ordners *Content* auf:

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Eine *.nupkg*-Datei mit dieser Ordnerstruktur wird erstellt, wenn Sie ein Visual Studio-Projekt paketieren, das über die Elemente *apiapp.json* und *Metadata* im Projektordner verfügt, wie die nachfolgende Abbildung zeigt:

!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-create-package/metadatainse.png)

In den folgenden Abschnitten werden alle Dateien und Ordner in der API-App-Ordnerstruktur dokumentiert. Anschließend erhalten Sie einen Überblick über das Vorgehen zum Paketieren und Bereitstellen eines Projekts.

## apiapp.json

Dies ist die Manifestdatei für Ihre API-App.

|Name (Fettformatierung = erforderlich)|Typ|Format|Beschreibung|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9_.]|Die ID des Pakets. Die ID muss innerhalb eines Namespace eindeutig sein und darf nur alphanumerische Zeichen, "_" und "." enthalten. Die ID muss mit einem alphanumerischen Zeichen beginnen.|
|**namespace**|string|Domänenname|Der Namespace, der die API-App gemeinsam mit der **id**-Eigenschaft eindeutig identifiziert. Es muss sich um einen Domänennamen des AAD-Mandanten des Herausgebers handeln.|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|Die Version des Pakets. Wenn die Option für automatische Upgrades für dieses Paket aktiviert wurde, gilt dies nur für neue Versionen innerhalb derselben Hauptversion.|
|**gateway**|string|2015-01-14|Die Gateway-API-Version, die das Paket verwendet. Ein Gateway ist eine spezielle Web-App, mit der alle Anforderungen für API-Apps in einer Ressourcengruppe geroutet werden. Eine der Hauptfunktionen des Gateways besteht darin, die Authentifizierung zu übernehmen. Momentan steht nur die Gatewayversion 2015-01-14 zur Verfügung. Wenn in Zukunft neue Gatewayversionen veröffentlicht werden, können Sie mithilfe dieser Eigenschaft Funktionsbeeinträchtigungen verhindern und weiterhin die vorherige Gateway-API verwenden.| 
|**title**|string||Der Anzeigename der API-App.|
|**summary**|string|max. 100 Zeichen|Eine kurze Zusammenfassung der API-App.
|description|string|max. 1.500 Zeichen|Die vollständige Beschreibung der API-App. Kann HTML enthalten. Zulässige Elemente und Attribute: "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target|href]", "br", "strong", "em", "b", "i".|
|**author**|string|max. 256 Zeichen|Der bzw. die Autor(en) der API-App.|
|homepage|string|URL|Die Startseite der API-App.|
|endpoints|object||Eine Liste der Endpunkte, die die API-App-Plattform nach Informationen zu Methoden und Status der API-App abfragen kann.|
|endpoints.apiDefinition|string|URL-Pfad|Relative URL einer von der API-App verfügbar gemachten API, die eine Swagger 2.0-API-Definition auf eine GET-Anforderung zurückgibt (beispielsweise "/swagger/docs/v1"). Falls gesetzt, wird diese anstelle der statischen apiDefinition.swagger.json-Datei im Paket verwendet (sofern vorhanden).|
|endpoints.status|string|URL-Pfad|Relative URL einer von der API-App verfügbar gemachten API, die Laufzeitstatusinformationen zur API-App auf eine GET-Anforderung zurückgibt.|
|categories|string[]|community, social, enterprise, integration, protocol, app-datasvc, other|Die Azure Marketplace-Kategorie, in der dieses API-App-Paket angezeigt wird. Standardmäßig wird die API-App immer in der Community-Kategorie angezeigt. Sobald die API-App genehmigt wurde, wird sie in der angegebenen Kategorie angezeigt.|
|license|object||Die Lizenz der API-App.|
|**license.type**|string||Der SPDX-Lizenzbezeichner, z. B. MIT.|
|license.url|string|URL|Die absolute URL, die auf den vollständigen Lizenztext verweist.|
|license.requireAcceptance|bool|true, false|Gibt an, ob vor der Installation eine Lizenzvereinbarung akzeptiert werden muss. Standardwert: false.|
|links|object[]||Ein Array aus Links, die zur Marketplace-Seite hinzugefügt werden sollen.|
|**links.text**|string||Der Text des Links.|
|**links.url**|string|URL|Die absolute URL des Links.|
|authentication|object[]||Ein Array, das angibt, welche Art der Authentifizierung für diese API-App erforderlich ist, um ausgehende API-Aufrufe durchzuführen. Aktuell wird pro API-App-Paket nur eine Authentifizierung unterstützt.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|Die für die API-App erforderliche Authentifizierung. Aktuell werden 11 Authentifizierungstypen unterstützt. Weitere Typen werden in Zukunft hinzugefügt.| 
|authentication.scopes|string[]||Ein Array mit für den Authentifizierungstyp spezifischen Bereichen.|
|copyright|string|Der Urheberrechtshinweis für die API-App
|brandColor|string|Ein beliebiges CSS-kompatibles Format|Eine optionale Markenfarbe für die Benutzeroberfläche zur Verbesserung des Benutzererlebnisses. Beispielsweise verwendet der Logik-App-Designer diese Eigenschaft für die Hintergrundfarbe der Kartenüberschrift.|

## metadata/apiDefinition.swagger.json

Sie können hier optional eine statische Swagger 2.0-JSON-Datei bereitstellen, um die API-Definition Ihrer API-App verfügbar zu machen. Die Plattform prüft zunächst, ob die Eigenschaft **endpoints.apiDefinition** in **apiapp.json** konfiguriert ist. Falls ja, wird die API-Definition von der URL abgerufen, die in der Eigenschaft angegeben ist. Falls nicht, wird versucht, diese Datei zu ermitteln.

- Informationen zum Swagger 2.0-Standard finden Sie unter [http://swagger.io/](http://swagger.io/). <!--todo provide URLs
- Informationen zum Anpassen der API-Definition, um diese für Logik-Apps zu optimieren, finden Sie unter [Titel des Dokuments]().
- Informationen dazu, wie Sie eine dynamische API-Definition verfügbar machen, finden Sie unter [Titel des Dokuments](). -->

## metadata/icons

Sie können optional eigene Symbolsätze für Ihr API-App-Paket bereitstellen. Wenn die erforderlichen Symboldateien nicht bereitgestellt werden, wird ein Standardsymbol wie das unten gezeigte verwendet.

![Großes Standardsymbol für API-App](./media/app-service-api-create-package/api-app-default-large-icon.png)

|Datei |Breite|Höhe|Beschreibung|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|Erforderlich, wenn Sie eigene Symbole verwenden möchten.|
|metadata/icons/medium.png|90px|90px|Erforderlich, wenn Sie eigene Symbole verwenden möchten.|
|metadata/icons/large.png|115px|115px|Erforderlich, wenn Sie eigene Symbole verwenden möchten.|
|metadata/icons/wide.png|255px|115px|Erforderlich, wenn Sie eigene Symbole verwenden möchten.|
|metadata/icons/hero.png|815px|290px|Optional, wenn Sie eigene Symbole verwenden möchten.|

## metadata/screenshots

Sie können optional bis zu 5 Screenshots für Ihr API-App-Paket bereitstellen.

|Datei|Breite|Höhe|Beschreibung|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/*.png|533px|324px|Screenshots Ihres API-App-Pakets.|

## metadata/deploymentTemplates

Für einige API-App-Pakete ist bei der Bereitstellung eine benutzerdefinierte Konfiguration erforderlich. Beispielsweise erfordert der [Azure Storage-Blobconnector](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) den URI zum Azure Storage-Blobcontainer. Optional können Sie auch einen Zugriffsschlüssel konfigurieren.

Um dieses Szenario zu unterstützen, können Sie eine Liste der JSON-Vorlagendateien für den Azure-Ressourcen-Manager (ARM) in diesem Ordner hinzufügen, um die API-App-Bereitstellung anzupassen. Die API-App-Plattform führt Ihre benutzerdefinierten ARM-Vorlagen mit den Systemvorlagen zusammen, um eine finale Vorlage für die Bereitstellung zu erzeugen. Alle Parameter, die in Ihren benutzerdefinierten ARM-Vorlagen definiert sind (erwartet für **$system**), werden automatisch auch im Blatt **Erstellen** im Azure-Vorschauportal angefordert, sodass Benutzer Ihrer API-App die Werte eingeben können.

Nachstehend wird eine ARM-Beispielvorlage gezeigt, die veranschaulicht, wie Sie während der API-App-Bereitstellung der Blobcontainer-URI und der Zugriffsschlüssel angefordert werden.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

Das entsprechende Blatt zum Erstellen im Azure-Vorschauportal wird im nachstehenden Screenshot gezeigt. (Der Screenshot zeigt ein API-App-Paket mit Verwendung von UIDefinition.json zur Verbesserung des Blatts "Erstellen".) Weitere Informationen finden Sie unter [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Beispiel für eine benutzerdefinierte ARM-Vorlage im Blatt "Erstellen"](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Wenn Sie benutzerdefinierte ARM-Vorlagen zum Festlegen einer benutzerdefinierten Konfiguration für die API-App-Bereitstellung benötigen, fordert das Blatt **Erstellen** im Azure-Vorschauportal automatisch die ARM-Vorlagenparameter an, sodass die Benutzer Ihrer API-App die Werte eingeben können. Sie können die Datei *UIDefinition.json* verwenden, um die Oberfläche für das Blatt **Erstellen** zu verbessern, indem Sie Standardwerte, QuickInfos, Validierungen usw. hinzufügen.

Beginnen Sie zum Bereitstellen der *UIDefinition.json-Datei* mit dem folgenden Grundgerüst, und verwenden Sie dann die nachstehende Tabelle, um jeden Parameter zu konfigurieren.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Name |Typ|Beschreibung|
|:---------------------|:-------|:------------|
|defaultValue|string|Standardwert des Eingabesteuerelements im Blatt "Erstellen" des Azure-Vorschauportals.|
|displayName|string|Beschriftung des Eingabesteuerelements im Blatt "Erstellen" des Azure-Vorschauportals. Diese sollte kurz sein.|
|description|string|Beschreibung des Eingabesteuerelements.|
|tooltip|string|QuickInfo für das Eingabesteuerelements im Blatt "Erstellen" des Azure-Vorschauportals. Die QuickInfos werden angezeigt, wenn ein Benutzer auf die Informationsblase rechts neben der Beschriftung klickt. Diese kann lang und detailliert sein.|
|constraints|object|Bedingungen, auf die der Parameter geprüft wird.|
|constraints.required|bool|Gibt an, ob der Parameter erforderlich ist oder nicht. Die Standardeinstellung ist "false".|

Als Beispiel wird hier die Datei *UIDefinition.json* für den [Azure Storage-Blobconnector](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) gezeigt:

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

Hierdurch wird das Blatt **Erstellen** im Azure-Vorschauportal zur Anzeige des folgenden Blatts **Paketeinstellungen** konfiguriert.

![Beispiel für "UIDefinition" im Blatt "Erstellen"](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Erstellen eines API-App-Pakets

So erstellen Sie ein API-App-Paket

1. [Laden Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle herunter, und installieren Sie sie.](xplat-cli)
2. Wechseln Sie in den Azure-Ressourcen-Manager-Modus, indem Sie den folgenden Befehl ausführen:

        azure config mode arm

3. Erstellen Sie ein nuget-Paket für Ihre API-App, indem Sie den folgenden Befehl ausführen:

        azure apiapp package create -p <package folder> -o <destination folder>

    Beispiel:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

Hierdurch werden die Paketinhalte überprüft, um Folgendes sicherzustellen:

- Die Inhalten entsprechen dem oben beschriebenen Format
- "Metadata\\apiDefinition.swagger.json" (falls vorhanden) enthält eine gültige Swagger 2.0-API-Definition

Falls ein Problem vorliegt, werden die Details angezeigt, damit Sie das Problem beheben und ein gültiges API-App-Paket erstellen können.

## Nächste Schritte

Ihr API-App-Paket ist jetzt bereit für die Veröffentlichung im Azure Marketplace. Informationen zur Vorgehensweise finden Sie im Lernprogramm [Veröffentlichen eines API-App-Pakets im Azure Marketplace](app-service-api-publish-package).

<!--HONumber=52-->
