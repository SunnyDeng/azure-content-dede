<properties
	pageTitle="DocumentDB-Automatisierung – Ressourcen-Manager – CLI | Microsoft Azure"
	description="Verwenden Sie Azure-Ressourcen-Manager-Vorlagen oder die CLI zur Bereitstellung eines DocumentDB-Datenbankkontos. DocumentDB ist eine cloudbasierte NoSQL-Datenbank für JSON-Daten."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mimig"/>

# Automatisieren der Erstellung von DocumentDB-Datenbankkonten mithilfe von Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

In diesem Artikel erfahren Sie, wie Sie ein DocumentDB-Konto mithilfe von Azure-Ressourcen-Manager-Vorlagen oder der Azure-CLI (Command-Line Interface, Befehlszeilenschnittstelle) erstellen können.

- [Erstellen eines DocumentDB-Kontos über die CLI](#quick-create-documentdb-account)
- [Erstellen eines DocumentDB-Kontos über eine ARM-Vorlage](#deploy-documentdb-from-a-template)

DocumentDB-Datenbankkonten sind derzeit die einzige DocumentDB-Ressource, die mithilfe von ARM-Vorlagen und der Azure-CLI erstellt werden kann.

## Vorbereitung

Vor der Verwendung der Azure-CLI mit Azure-Ressourcengruppen benötigen Sie die richtige Version der Azure-CLI und ein Azure-Konto. Wenn Sie nicht über die Azure-CLI verfügen, [installieren Sie sie](../xplat-cli-install.md).

### Aktualisieren der Azure-CLI-Version

Geben Sie an der Eingabeaufforderung `azure --version` ein, um zu überprüfen, ob Sie bereits Version 0.9.11 oder höher installiert haben.

	azure --version
    0.9.11 (node: 0.12.7)

Wenn Ihre Version nicht 0.9.11 oder höher entspricht, müssen Sie entweder die [Azure-CLI installieren](../xplat-cli-install.md) oder mit einem der systemeigenen Installer bzw. mit **npm** und Eingabe von `npm update -g azure-cli` ein Update durchführen oder die CLI mit `npm install -g azure-cli` installieren.

### Festlegen Ihres Azure-Kontos und -Abonnements

Wenn Sie noch kein Azure-Abonnement, aber ein Visual Studio-Abonnement besitzen, können Sie Ihre [Visual Studio-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren. Oder Sie registrieren sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).

Zum Verwenden der Azure-Ressourcen-Manager-Vorlagen benötigen Sie eine Geschäfts-, Schul- oder Microsoft-Kontoidentität. Wenn Sie eines dieser Konten besitzen, geben Sie den folgenden Befehl ein.

	azure login

Die folgende Ausgabe wird erzeugt:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE]Wenn Sie kein Azure-Konto besitzen, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass Sie einen anderen Kontotyp benötigen. Informationen zum Erstellen eines Kontos auf der Basis Ihres aktuellen Azure-Kontos finden Sie unter [Erstellen von Arbeits- oder Schulidentitäten in Azure Active Directory](../resource-group-create-work-id-from-personal.md).

Öffnen Sie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) in einem Browser, und geben Sie den in der Befehlsausgabe angegebenen Code ein.

![Screenshot mit dem Geräteanmeldungsbildschirm für die Microsoft Azure-CLI](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Nach der Eingabe des Codes wählen Sie die gewünschte Identität im Browser aus und geben bei Bedarf Ihren Benutzernamen und Ihr Kennwort an.

![Screenshot zur Auswahl des Microsoft-Identitätskontos, das dem gewünschten Azure-Abonnement zugewiesen ist](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Bei erfolgreicher Anmeldung wird der folgende Bestätigungsbildschirm angezeigt. Sie können das Browserfenster jetzt schließen.

![Screenshot mit der Bestätigung der Anmeldung bei der plattformübergreifenden Microsoft Azure-Befehlszeilenschnittstelle](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

Auf der Befehlsshell wird außerdem die folgende Ausgabe angezeigt.

    -info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Zusätzlich zu der hier beschriebenen interaktiven Anmeldemethode sind zusätzliche Azure-CLI-Anmeldemethoden verfügbar. Informationen zu weiteren Methoden und zur Behandlung mehrerer Abonnements finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure-Befehlszeilenschnittstelle (Azure-CLI)](../xplat-cli-connect.md).

### Wechseln in den Azure CLI-Ressourcengruppenmodus

Standardmäßig startet die Azure-CLI im Service Management-Modus (**asm**-Modus). Geben Sie Folgendes ein, um in den Ressourcengruppen-Modus zu wechseln.

	azure config mode arm

Die folgende Ausgabe wird zurückgegeben:

	info:    New mode is arm

Sie können zum Standardbefehlssatz wechseln, indem Sie `azure config mode asm` eingeben.

## <a id="quick-create-documentdb-account"></a>Aufgabe: Erstellen eines DocumentDB-Kontos über die CLI

Befolgen Sie die Anweisungen in diesem Abschnitt, um ein DocumentDB-Konto über die Azure-CLI zu erstellen.

### Schritt 1: Erstellen oder Abrufen der Ressourcengruppe

Wenn Sie ein DocumentDB-Konto erstellen möchten, benötigen Sie zuerst eine Ressourcengruppe. Wenn Sie den Namen der gewünschten Ressourcengruppe bereits kennen, fahren Sie mit [Schritt2](#create-documentdb-account-cli) fort.

Wenn Sie eine Liste aller aktuellen Ressourcengruppen anzeigen möchten, führen Sie den folgenden Befehl aus, und notieren Sie sich den Namen der gewünschten Ressourcengruppe:

    azure group list

Wenn Sie eine neue Ressourcengruppe erstellen möchten, führen Sie den folgenden Befehl aus, und geben Sie den Namen der neu zu erstellenden Ressourcengruppe und die Region an, in der Sie die Ressourcengruppe erstellen möchten:

	azure group create <resourcegroupname> <location>

Beispiel:

	azure group create new_res_group westus

Die folgende Ausgabe wird erzeugt:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Beim Auftreten von Fehlern finden Sie weitere Informationen unter [Problembehandlung](#troubleshooting).

### <a id="create-documentdb-account-cli"></a> Schritt 2: Erstellen eines DocumentDB-Kontos über die CLI

Erstellen Sie ein DocumentDB-Konto in der neuen oder vorhandenen Ressourcengruppe, indem Sie den folgenden Befehl an der Eingabeaufforderung eingeben:

> [AZURE.TIP]Wenn Sie diesen Befehl in Azure PowerShell oder Windows PowerShell ausführen, erhalten Sie eine Fehlermeldung über ein unerwartetes Token. Führen Sie den Befehl stattdessen an der Windows-Eingabeaufforderung aus.

    azure resource create -g <resourceGroupName> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

Beispiel:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

Bei der Bereitstellung Ihres neuen Kontos wird die folgende Ausgabe erzeugt:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Beim Auftreten von Fehlern finden Sie weitere Informationen unter [Problembehandlung](#troubleshooting).

Nachdem der Befehl ausgeführt wurde, befindet sich das Konto einige Minuten im Zustand **Wird erstellt**, bis es in den Zustand **Online** wechselt und verwendet werden kann. Sie können den Status des Kontos im [Azure-Portal](https://portal.azure.com) auf dem Blatt **DocumentDB-Konten** überprüfen.

## <a id="deploy-documentdb-from-a-template"></a>Aufgabe: Erstellen eines DocumentDB-Kontos über eine ARM-Vorlage

Befolgen Sie die Anweisungen in diesem Abschnitt, um ein DocumentDB-Konto mithilfe einer Azure-Ressourcen-Manager (ARM)-Vorlage und einer optionalen Parameterdatei zu erstellen. In beiden Fällen handelt es sich um JSON-Dateien. Mithilfe einer Vorlage können Sie genaue Vorgaben machen und diese ohne Fehler wiederverwenden.

### Grundlegendes zu ARM-Vorlagen und -Ressourcengruppen

Die meisten Anwendungen basieren auf einer Kombination verschiedener Ressourcentypen (z. B. mindestens ein DocumentDB-Konto, Speicherkonten, ein virtuelles Netzwerk oder ein Content Delivery Network). Die standardmäßige Azure-Service-Management-API und das Azure-Portal stellte diese Elemente mithilfe eines Ansatzes auf Dienstbasis dar. Bei dieser Vorgehensweise müssen Sie die einzelnen Dienste einzeln und nicht als eine logische Bereitstellungseinheit bereitstellen und verwalten (oder weitere Tools suchen, die dies ermöglichen).

*Azure-Ressourcen-Manager-Vorlagen* ermöglichen es Ihnen, diese verschiedenen Ressourcen als eine logische Bereitstellungseinheit auf deklarative Weise bereitzustellen und zu verwalten. Anstatt Azure imperativisch mitzuteilen, wie ein Befehl nach dem anderen bereitgestellt wird, beschreiben Sie die gesamte Bereitstellung in einer JSON-Datei – alle Ressourcen und zugeordnete Konfigurations- und Bereitstellungsparameter – und geben an Azure weiter, dass diese Ressourcen als eine Gruppe bereitgestellt werden sollen.

Weitere Informationen zu Azure-Ressourcengruppen und wie Sie sie nutzen können finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md). Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

### Schritt 1: Erstellen einer Vorlage und Parameterdatei

Erstellen Sie eine lokale Vorlagendatei mit folgendem Inhalt: Geben Sie der Datei den Namen "azuredeploy.json".

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Diese Vorlage erfordert nur einen Parameter, nämlich den Namen des zu erstellenden Datenbankkontos. Der Speicherort des neuen Datenbankkontos entspricht dem Speicherort der Ressourcengruppe.

Da die Vorlage nur einen Parameter akzeptiert, können Sie entweder den Wert in die Befehlszeile eingeben oder eine Parameterdatei erstellen, die den Wert angibt.

Wenn Sie eine Parameterdatei erstellen möchten, kopieren Sie den folgenden Inhalt in eine neue Datei, und geben Sie ihr den Namen "azuredeploy.parameters.json". Wenn Sie den Datenbankkontonamen an der Eingabeaufforderung angeben möchten, können Sie den Vorgang ohne Erstellen dieser Datei fortsetzen.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

Ändern Sie in der Datei "azuredeploy.parameters.json" den Wert "samplearmacct", sodass er dem gewünschten Datenbanknamen entspricht, und speichern Sie die Datei.

> [AZURE.TIP]Namen von Datenbankkonten dürfen nur Kleinbuchstaben, Ziffern und den Bindestrich "-" enthalten und müssen zwischen 3 und 50 Zeichen lang sein.

### Schritt 2: Erstellen oder Abrufen der Ressourcengruppe

Wenn Sie ein DocumentDB-Konto erstellen möchten, benötigen Sie zuerst eine Ressourcengruppe. Wenn Sie den Namen der gewünschten Ressourcengruppe bereits kennen, fahren Sie mit [Schritt3](#create-account-from-template) fort.

Wenn Sie eine Liste aller aktuellen Ressourcengruppen anzeigen möchten, führen Sie den folgenden Befehl aus, und notieren Sie sich den Namen der gewünschten Ressourcengruppe:

    azure group list

Wenn Sie eine neue Ressourcengruppe erstellen möchten, führen Sie den folgenden Befehl aus, und geben Sie den Namen der neu zu erstellenden Ressourcengruppe und die Region an, in der Sie die Ressourcengruppe erstellen möchten:

	azure group create <resourcegroupname> <location>

Beispiel:

	azure group create new_res_group westus

Die folgende Ausgabe wird erzeugt:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Beim Auftreten von Fehlern finden Sie weitere Informationen unter [Problembehandlung](#troubleshooting).

### <a id="create-account-from-template"></a>Schritt 3: Erstellen des DocumentDB-Kontos über eine ARM-Vorlage

Wenn Sie ein DocumentDB-Konto in der Ressourcengruppe erstellen möchten, führen Sie den folgenden Befehl aus, und geben Sie den Pfad der Vorlagendatei, den Pfad der Parameterdatei oder den Parameterwert, den Namen der Ressourcengruppe für die Bereitstellung und einen Bereitstellungsnamen (-n ist optional) an.

So verwenden Sie eine Parameterdatei

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

Beispiel:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

ODER führen Sie den folgenden Befehl aus, um den Namensparameter für das Datenbankkonto ohne Parameterdatei anzugeben. In diesem Fall werden Sie zur Eingabe des Werts aufgefordert:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Beispiel (Eingabeaufforderung und Eintrag eines Datenbankkontos mit dem Namen "new\_db\_acct"):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: newarmacct

Bei der Bereitstellung des Kontos werden folgende Informationen angezeigt:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  newarmacct
    data:    location             String  West US
    info:    group deployment create command OK

Beim Auftreten von Fehlern finden Sie weitere Informationen unter [Problembehandlung](#troubleshooting).

Nachdem der Befehl ausgeführt wurde, befindet sich das Konto einige Minuten im Zustand **Wird erstellt**, bis es in den Zustand **Online** wechselt und verwendet werden kann. Sie können den Status des Kontos im [Azure-Portal](https://portal.azure.com) auf dem Blatt **DocumentDB-Konten** überprüfen.

## Problembehandlung

Wenn Sie bei der Erstellung der Ressourcengruppe oder des Datenbankkontos Fehlermeldungen wie `Deployment provisioning state was not successful` erhalten, verwenden Sie den folgenden Befehl, um das Protokoll der Ressourcengruppe anzuzeigen.

    azure group log show <resourcegroupname> --last-deployment

Beispiel:

    azure group log show new_res_group --last-deployment

Zusätzliche Informationen finden Sie unter [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](../resource-group-deploy-debug.md).

Beachten Sie, dass Namen von Datenbankkonten nur Kleinbuchstaben, Ziffern und den Bindestrich "-" enthalten dürfen und zwischen 3 und 50 Zeichen lang sein müssen.

Fehlerinformationen sind auch im Azure-Portal verfügbar, wie im folgenden Screenshot dargestellt. So navigieren Sie zu Fehlerinformationen: Klicken Sie auf der Navigationsleiste auf "Ressourcengruppen", wählen Sie die fehlerhafte Ressourcengruppe aus, und klicken Sie dann im Bereich "Zusammenfassung" des Blatts "Ressourcengruppe" auf das Datum der letzten Bereitstellung, und wählen Sie auf dem Blatt "Bereitstellungsverlauf" die fehlerhafte Bereitstellung aus. Klicken Sie dann auf dem Blatt "Bereitstellung" auf das mit einem roten Ausrufezeichen gekennzeichnete Vorgangsdetail. Die Statusmeldung der fehlerhaften Bereitstellung wird auf dem Blatt "Vorgangsdetails" angezeigt.

![Screenshot des Azure-Portals mit dem Navigationspfad zur Bereitstellungsfehlermeldung](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Nächste Schritte

Nachdem Sie nun über ein DocumentDB-Datenbankkonto verfügen, besteht der nächste Schritt darin, eine DocumentDB-Datenbank zu erstellen. Mit einer der folgenden Methoden können Sie eine Datenbank erstellen:

- Im Azure-Portal, wie unter [Erstellen einer DocumentDB-Datenbank über das Azure-Portal](documentdb-create-database.md) beschrieben.
- Mit den C# .NET-Beispielen im Projekt [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) des Repositorys [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) auf GitHub.
- Die [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. 

Nach dem Erstellen der Datenbank müssen Sie der Datenbank [mindestens eine Sammlung hinzufügen](documentdb-create-collection.md) und den Sammlungen dann [Dokumente hinzufügen](documentdb-view-json-document-explorer.md).

Nachdem eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Vorschauportal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) verwenden.

Weitere Informationen zu DocumentDB finden Sie in folgenden Ressourcen:

-	[Lernpfad für DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Ressourcenmodell und Konzepte von DocumentDB](documentdb-resources.md)

Weitere Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1203_2015-->