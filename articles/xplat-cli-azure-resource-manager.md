<properties
	pageTitle="Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager | Microsoft Azure"
	description="Verwenden Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Microsoft Azure, um mehrere Ressourcen als eine Ressourcengruppe bereitzustellen."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>

# Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



In diesem Artikel erfahren Sie, wie Sie Ihre Azure-Ressourcen mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows im Azure-Ressourcen-Manager-Modus erstellen und verwalten.

>[AZURE.NOTE]Für die befehlszeilenbasierte Erstellung und Verwaltung von Azure-Ressourcen benötigen Sie ein Azure-Konto. Eine kostenlose Testversion finden Sie [hier](http://azure.microsoft.com/pricing/free-trial/). Außerdem müssen Sie die [Azure-Befehlszeilenschnittstelle installieren](xplat-cli-install.md) sich [für die Verwendung der mit Ihrem Konto verknüpften Azure-Ressourcen anmelden](xplat-cli-connect.md). Damit sind dann alle nötigen Vorbereitungen abgeschlossen.

## Azure-Ressourcen

Mit dem Azure-Ressourcen-Manager können Sie eine Gruppe von _Ressourcen_ (vom Benutzer verwaltete Entitäten wie etwa virtuelle Computer, Datenbankserver, Datenbanken oder Websites) als einzelne logische Einheit (_Ressourcengruppe_) erstellen und verwalten.

Ein Vorteil des Azure-Ressourcen-Managers besteht darin, dass Sie Ihre Azure-Ressourcen _deklarativ_ erstellen können, indem Sie die Struktur und die Beziehungen einer bereitstellbaren Gruppe von Ressourcen in *JSON-Vorlagen* beschreiben. Die Vorlage gibt Parameter an, die entweder beim Ausführen eines Befehls (Inline) ausgefüllt oder in einer separaten JSON-Datei („azuredeploy-parameters.json“) gespeichert werden. Auf diese Weise können Sie mühelos neue Ressourcen mit derselben Vorlage erstellen, indem Sie unterschiedliche Parameter bereitstellen. So enthält beispielsweise eine Vorlage für die Websiteerstellung Parameter für den Websitenamen und die Region, in der sich die Website befinden soll, sowie andere allgemeine Einstellungen.

Wenn mit einer Vorlage eine Gruppe geändert oder erstellt wird, wird eine _Bereitstellung_ erstellt, die dann auf die Gruppe angewendet wird. Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).

Nach dem Erstellen einer Bereitstellung können Sie genau wie beim klassischen Bereitstellungsmodell (Verwaltungsdienst) die einzelnen Ressourcen imperativ über die Befehlszeile verwalten. Verwenden Sie beispielsweise Befehle der Befehlszeilenschnittstelle des Azure-Ressourcen-Managers, um Ressourcen wie [virtuelle Azure-Ressourcen-Manager-Computer](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md) zu starten, zu beenden oder zu löschen.

## Authentifizierung

Wenn Sie den Azure-Ressourcen-Manager über die Azure-Befehlszeilenschnittstelle nutzen möchten, müssen Sie sich mit einem Geschäfts- oder Schulkonto (Organisationskonto) oder einem Microsoft-Konto (ab Version 0.9.10 der Befehlszeilenschnittstelle) bei Microsoft Azure authentifizieren. Die Authentifizierung mit einem durch eine PUBLISHSETTINGS-Datei installierten Zertifikat ist in diesem Modus nicht möglich.

Weitere Informationen zur Authentifizierung bei Microsoft Azure finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](xplat-cli-connect.md).

>[AZURE.NOTE]Bei Verwendung eines (von Azure Active Directory verwalteten) Geschäfts- oder Schulkontos können Sie auch die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure nutzen, um den Zugriff auf und die Verwendung von Azure-Ressourcen zu verwalten. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](./active-directory/role-based-access-control-configure.md).

## Festlegen des Azure-Ressourcen-Manager-Modus

Da der Azure-Ressourcen-Manager-Modus standardmäßig nicht aktiviert ist, müssen Sie die Ressourcen-Manager-Befehle der Azure-Befehlszeilenschnittstelle mithilfe des folgenden Befehls aktivieren.

	azure config mode arm

>[AZURE.NOTE]Der Azure-Ressourcen-Manager-Modus und der Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

## Suchen der Standorte

Bei den meisten Azure-Ressourcen-Manager-Befehlen muss für die Ressourcenerstellung oder -suche ein gültiger Standort angegeben werden. Verwenden Sie zum Ermitteln aller verfügbaren Standorte für die verschiedenen Azure-Ressourcen den folgenden Befehl.

	azure location list

Damit werden die Azure-Ressourcen und Azure-Regionen aufgelistet, in denen sie verfügbar sind, z. B. „Westen USA“, „Osten USA“ usw.

## Erstellen einer Ressourcengruppe

Bei einer Ressourcengruppe handelt es sich um eine logische Gruppe von Netzwerkressourcen, Speicherressourcen und anderen Ressourcen. Für fast alle Befehle im Azure-Ressourcen-Manager-Modus ist eine Ressourcengruppe erforderlich. Der folgende Befehl erstellt beispielsweise eine Ressourcengruppe namens _testRG_.

	azure group create -n "testRG" -l "West US"

Anschließend können Sie der Gruppe dann Ressourcen hinzufügen und sie für die Konfiguration einer Ressource wie etwa eines neuen virtuellen Computers verwenden.


## Verwenden von Ressourcengruppenvorlagen

### Suchen und Konfigurieren einer Ressourcengruppenvorlage

Sie können [eine eigene Vorlage erstellen](resource-group-authoring-templates.md) oder eine der Vorlagen aus dem [Vorlagenkatalog](https://azure.microsoft.com/documentation/templates/) verwenden, die ebenfalls bei [GitHub](https://github.com/Azure/azure-quickstart-templates) verfügbar sind.

Das Erstellen einer neuen Vorlage würde jedoch den Rahmen dieses Artikels sprengen. Daher verwenden wir zum Einstieg die bei [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) verfügbare Vorlage _101-simple-vm-from-image_. Standardmäßig wird dabei ein einzelner virtueller Ubuntu 14.04.2-LTS-Computer in einem neuen virtuellen Netzwerk mit einem einzelnen Subnetz in der Region „Westen USA“ erstellt. Sie müssen nur die folgenden wenigen Parameter angeben, um diese Vorlage zu verwenden:

* einen eindeutigen Speicherkontonamen
* einen Administratorbenutzernamen für den virtuellen Computer
* ein Kennwort
* einen Domänennamen für den virtuellen Computer

>[AZURE.TIP]Diese Schritte veranschaulichen nur eine Möglichkeit der Verwendung einer VM-Vorlage mit der Azure-Befehlszeilenschnittstelle. Weitere Beispiele finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

1. Laden Sie die Dateien „azuredeploy.json“ und „azuredeploy.parameters.json“ von [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) in einen Arbeitsordner auf Ihrem lokalen Computer herunter.

2. Öffnen Sie die Datei „azuredeploy.parameters.json“ in einem Text-Editor, und geben Sie geeignete Parameterwerte für Ihre Umgebung ein (behalten Sie den Wert **ubuntuOSVersion** bei).

		{
	  	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  	"contentVersion": "1.0.0.0",
	  	"parameters": {
		    "newStorageAccountName": {
		      "value": "MyStorageAccount"
		    },
		    "adminUsername": {
		      "value": "MyUserName"
		    },
		    "adminPassword": {
		      "value": "MyPassword"
		    },
		    "dnsNameForPublicIP": {
		      "value": "MyDomainName"
		    },
		    "ubuntuOSVersion": {
		      "value": "14.04.2-LTS"
		    }
		  }
		}
	```
3. Verwenden Sie nach dem Speichern der Datei „azuredeploy.parameters.json“ den folgenden Befehl, um eine neue Ressourcengruppe basierend auf der Vorlage zu erstellen. Die Option `-e` gibt die Datei „azuredeploy.parameters.json“ an, die Sie im vorherigen Schritt angepasst haben. Ersetzen Sie *testRG* durch den gewünschten Gruppennamen und *testDeploy* durch den Bereitstellungsnamen Ihrer Wahl. Der Standort muss dem Standort aus der Parametervorlagendatei entsprechen.

		azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

	Dieser Befehl gibt „OK“ zurück, nachdem die Bereitstellung hochgeladen wurde, jedoch bevor die Bereitstellung auf die Ressourcen in der Gruppe angewendet wurde.

4. Verwenden Sie den folgenden Befehl, um den Status der Bereitstellung zu prüfen.

		azure group deployment show "testRG" "testDeploy"

	Mit **ProvisioningState** wird der Status der Bereitstellung angezeigt.

	Ist die Bereitstellung erfolgreich, sieht die Ausgabe etwa wie folgt aus:

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE]Wenn Sie feststellen, dass Ihre Konfiguration nicht richtig ist und eine Bereitstellung mit langer Laufzeit angehalten werden muss, verwenden Sie den folgenden Befehl:
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Wenn Sie keinen Bereitstellungsnamen angeben, wird automatisch einer anhand des Namens der Vorlagendatei erstellt. Dieser Name wird als Teil der Ausgabe des Befehls `azure group create` zurückgegeben.

	Sie können jetzt unter Verwendung des angegebenen Domänennamens per SSH auf den virtuellen Computer zugreifen. Beim Herstellen einer Verbindung mit dem virtuellen Computer müssen Sie einen vollqualifizierten Domänennamen im Format `<domainName>.<region>.cloudapp.azure.com` verwenden, z. B. `MyDomainName.westus.cloudapp.azure.com`.

5. Verwenden Sie zum Anzeigen der Gruppe den folgenden Befehl.

		azure group show "testRG"

	Dieser Befehl gibt Informationen zu den Ressourcen in der Gruppe zurück. Sind mehrere Gruppen vorhanden, rufen Sie mit dem Befehl `azure group list` eine Liste mit Gruppennamen ab. Verwenden Sie dann `azure group show`, um die Details einer bestimmten Gruppe anzuzeigen.

Sie können auch eine Vorlage direkt auf [GitHub](https://github.com/Azure/azure-quickstart-templates) verwenden, ohne sie auf Ihren Computer herunterzuladen. Übergeben Sie dazu die URL zur Datei „azuredeploy.json“ für die Vorlage in Ihrem Befehl mithilfe der Option **--template-url**. Öffnen Sie zum Abrufen der URL die Datei „azuredeploy.json“ auf GitHub im _Rohmodus_, und kopieren Sie die URL aus der Adressleiste des Browsers. Mit dieser URL können Sie direkt eine Bereitstellung erstellen, indem Sie ungefähr folgenden Befehl verwenden:

	azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
Sie werden aufgefordert, die erforderlichen Vorlagenparameter einzugeben.

> [AZURE.NOTE]Die JSON-Vorlage muss im _Rohmodus_ geöffnet werden. Im normalen Modus wird in der Adressleiste des Browsers eine andere URL angezeigt. Klicken Sie rechts oben auf die Schaltfläche **Raw**, während die Datei in GitHub angezeigt wird, um sie im _Rohmodus_ zu öffnen.

## Arbeiten mit Ressourcen

Auch wenn Vorlagen es Ihnen ermöglichen, gruppenweite Änderungen an der Konfiguration zu deklarieren, müssen Sie manchmal mit nur einer einzelnen Ressource arbeiten. Verwenden Sie dazu die Befehle vom Typ `azure resource`.

> [AZURE.NOTE]Mit Ausnahme des Befehls `list` müssen Sie allen anderen Befehlen vom Typ `azure resource` mithilfe des Parameters `-o` die API-Version der Ressource angeben, mit der Sie arbeiten. Wenn Sie die zu verwendende API-Version nicht kennen, öffnen Sie die Vorlagendatei und gehen Sie zum Feld **apiVersion** der Ressource.

1. Verwenden Sie den folgenden Befehl, um alle Ressourcen in einer Gruppe aufzulisten.

		azure resource list "testRG"

2. Verwenden Sie etwa folgenden Befehl, um einzelne Ressourcen in der Gruppe anzuzeigen:

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Beachten Sie den Parameter **Microsoft.Compute/virtualMachines**. Mit diesem Parameter wird der Typ der Ressourcen angegeben, für den Sie Informationen anfordern. In der zuvor heruntergeladenen Vorlagendatei wird der gleiche Wert verwendet, um die Art der in der Vorlage beschriebenen VM-Ressource zu definieren.

	Dieser Befehl gibt Informationen zum virtuellen Computer zurück.

3. Wenn Sie Details für eine Ressource anzeigen, ist es oft hilfreich, den Parameter `--json` zu verwenden. Dadurch wird die Ausgabe besser lesbar, da einige Werte als geschachtelte Strukturen oder Sammlungen angegeben werden. Im folgenden Beispiel sehen Sie, wie die Ergebnisse des **show**-Befehls als JSON-Dokument zurückgegeben werden.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE]Sie können die JSON-Daten in einer Datei speichern. Verwenden Sie dazu das Zeichen „ &gt;“, um die Ausgabe in die Datei zu leiten. Beispiel:
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Verwenden Sie etwa folgenden Befehl, um eine bestehende Ressource zu löschen:

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Protokollierung

Verwenden Sie den Befehl `azure group log show`, um protokollierte Informationen zu Vorgängen für eine Gruppe anzuzeigen. Standardmäßig wird dadurch der letzte Vorgang aufgelistet, der für die Gruppe durchgeführt wurde. Mit dem optionalen Parameter `--all` können Sie alle Vorgänge anzeigen. Für die letzte Bereitstellung können Sie `--last-deployment` verwenden. Wenn Sie sich dagegen für eine bestimmte Bereitstellung interessieren, verwenden Sie `--deployment`, und geben Sie den Namen der Bereitstellung an. Im folgenden Beispiel wird ein Protokoll aller Vorgänge zurückgegeben, die für die Gruppe *MyGroup* durchgeführt wurden.

	azure group log show MyGroup --all

## Nächste Schritte

* Weitere Informationen zur Verwendung des Azure-Ressourcen-Managers mit Azure PowerShell finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
* Weitere Informationen zur Verwendung des Azure-Ressourcen-Managers im Azure-Portal finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_1223_2015--->