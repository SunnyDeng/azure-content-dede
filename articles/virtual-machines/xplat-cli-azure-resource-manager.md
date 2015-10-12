<properties
	pageTitle="Befehlszeilenschnittstelle für Mac, Linux und Windows | Microsoft Azure"
	description="Verwenden der Microsoft Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit dem Azure-Ressourcen-Manager."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In diesem Artikel ist das Verwalten von Ressourcen mit dem Ressourcen-Manager-Bereitstellungsmodell beschrieben.

In diesem Artikel erfahren Sie, wie Sie Ihre Azure-Ressourcen und Ihre virtuellen Computer mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows im Azure-Ressourcen-Manager-Modus erstellen, verwalten und löschen können.

>[AZURE.NOTE]Für die befehlszeilenbasierte Erstellung und Verwaltung von Azure-Ressourcen benötigen Sie ein Azure-Konto. Eine kostenlose Testversion finden Sie [hier](http://azure.microsoft.com/pricing/free-trial/). Außerdem müssen Sie die [Azure-Befehlszeilenschnittstelle installieren](../xplat-cli-install.md) sich [für die Verwendung der mit Ihrem Konto verknüpften Azure-Ressourcen anmelden](../xplat-cli-connect.md). Damit sind dann alle nötigen Vorbereitungen abgeschlossen.

## Azure-Ressourcen

Mit dem Azure Ressourcen-Manager können Sie eine Gruppe von _Ressourcen_ (vom Benutzer verwaltete Entitäten wie etwa virtuelle Computer, Datenbankserver, Datenbanken oder Websites) als einzelne logische Einheit (_Ressourcengruppe_) verwalten. Diese Ressourcen können genau wie im Azure-Ressourcen-Manager-Modus imperativ über die Befehlszeile erstellt, verwaltet und gelöscht werden.

Im Azure-Ressourcen-Manager-Modus können Sie Ihre Azure-Ressourcen außerdem _deklarativ_ verwalten, indem Sie die Struktur und die Beziehungen einer bereitstellbaren Gruppe von Ressourcen in *JSON-Vorlagen* beschreiben. Die Vorlage beschreibt Parameter, die entweder beim Ausführen eines Befehls (Inline) ausgefüllt oder in einer separaten JSON-Datei („azuredeploy-parameters.json“) gespeichert werden. Auf diese Weise können Sie mühelos neue Ressourcen mit derselben Vorlage erstellen, indem Sie unterschiedliche Parameter bereitstellen. So enthält beispielsweise eine Vorlage für die Websiteerstellung Parameter für den Websitenamen und die Region, in der sich die Website befinden soll, sowie andere allgemeine Parameter.

Wenn mit einer Vorlage eine Gruppe geändert oder erstellt wird, wird eine _Bereitstellung_ erstellt, die dann auf die Gruppe angewendet wird. Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).

## Authentifizierung

Wenn Sie den Azure-Ressourcen-Manager über die Azure-Befehlszeilenschnittstelle nutzen möchten, müssen Sie sich mit einem Geschäfts- oder Schulkonto bei Microsoft Azure authentifizieren. Die Authentifizierung mit einem durch eine PUBLISHSETTINGS-Datei installierten Zertifikat ist nicht möglich.

Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md).

> [AZURE.NOTE]Dank der Verwendung eines (von Azure Active Directory verwalteten) Geschäfts- oder Schulkontos können Sie auch die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure nutzen, um den Zugriff auf und die Verwendung von Azure-Ressourcen zu verwalten. Ausführlichere Informationen finden Sie unter [Verwalten und Überwachen des Ressourcen-Zugriffs](../resource-group-rbac.md).

## Festlegen des Azure-Ressourcen-Manager-Modus

Da der Azure-Ressourcen-Manager-Modus standardmäßig nicht aktiviert ist, müssen Sie die Ressourcen-Manager-Befehle der Azure-Befehlszeilenschnittstelle mithilfe des folgenden Befehls aktivieren.

	azure config mode arm

>[AZURE.NOTE]Der Azure-Ressourcen-Manager-Modus und der Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

## Suchen der Standorte

Bei den meisten Azure-Ressourcen-Manager-Befehlen muss für die Ressourcenerstellung oder -suche ein gültiger Standort angegeben werden. Verwenden Sie zum Ermitteln aller verfügbaren Standorte den folgenden Befehl.

	azure location list

Dadurch erhalten Sie eine regionsspezifische Standortliste (beispielsweise für den Westen der USA, für den Osten der USA usw.).

## Erstellen einer Ressourcengruppe

Bei einer Ressourcengruppe handelt es sich um eine logische Gruppe von Netzwerkressourcen, Speicherressourcen und anderen Ressourcen. Für fast alle Befehle im Azure-Ressourcen-Manager-Modus ist eine Ressourcengruppe erforderlich. Der folgende Befehl erstellt beispielsweise eine Ressourcengruppe namens _testrg_.

	azure group create -n "testrg" -l "West US"

Anschließend können Sie der Gruppe dann Ressourcen hinzufügen und sie für die Konfiguration eines neuen virtuellen Computers verwenden.

## Erstellen virtueller Computer

Virtuelle Computer können im Azure-Ressourcen-Manager-Modus auf zwei Arten erstellt werden:

1. Mithilfe einzelner Befehle der Azure-Befehlszeilenschnittstelle.
2. Mithilfe der Ressourcengruppenvorlagen.

Erstellen Sie vor der Verwendung einer dieser Methoden mindestens eine Ressourcengruppe.

### Einzelne Befehle der Azure-Befehlszeilenschnittstelle

Dies ist die grundlegende Methode zum Konfigurieren und Erstellen eines virtuellen Computers für Ihre individuellen Anforderungen. Im Azure-Ressourcen-Manager-Modus müssen zunächst einige erforderliche Ressourcen wie etwa das Netzwerk konfiguriert werden, bevor Sie den Befehl **vm create** verwenden können.

>[AZURE.NOTE]Wenn Sie für Ihr Abonnement erstmals Ressourcen über die Befehlszeile erstellen, erhalten Sie unter Umständen eine Registrierungsaufforderung für bestimmte Ressourcenanbieter. In diesem Fall können Sie einfach den Anbieter registrieren und den nicht erfolgreich ausgeführten Befehl wiederholen, wie im folgenden Beispiel dargestellt.
>
> `azure provider register Microsoft.Storage`
>
> Führen Sie den folgenden Befehl aus, um die Liste mit den registrierten Anbietern für Ihr Abonnement zu erhalten.
>
> `azure provider list`


#### Erstellen einer öffentlichen IP-Ressource

Sie müssen eine öffentliche IP-Adresse erstellen, um eine SSH-Verbindung mit Ihrem neuen virtuellen Computer herstellen und diesen sinnvoll verwenden zu können. Das Erstellen einer öffentlichen IP-Adresse ist ganz einfach. Für den Befehl müssen Sie eine Ressourcengruppe, einen Namen für Ihre öffentliche IP-Ressource und einen Standort (in dieser Reihenfolge) angeben.

	azure network public-ip create "testrg" "testip" "westus"

#### Erstellen einer NIC-Ressource (Netzwerkschnittstellenkarte)

Für die Netzwerkschnittstellenkarte (Network Interface Card, NIC) müssen zunächst ein Subnetz und ein virtuelles Netzwerk erstellt werden. Erstellen Sie mithilfe des Befehls **network vnet create** ein virtuelles Netzwerk an einem bestimmten Standort und in einer bestimmten Ressourcengruppe.

	azure network vnet create "testrg" "testvnet" "westus"

Anschließend können Sie mithilfe des Befehls **network vnet subnet create** ein Subnetz in diesem virtuellen Netzwerk erstellen.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

Mithilfe des Befehls **network nic create** können Sie dann eine Netzwerkschnittstellenkarte erstellen, die diese Ressourcen verwendet.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]Optional, aber überaus wichtig: Übergeben Sie den öffentlichen IP-Namen als Parameter an den Befehl **network nic create**. Dadurch wird die NIC an diese IP-Adresse gebunden und kann später für eine SSH-Verbindung mit dem virtuellen Computer verwendet werden, die unter Verwendung dieser NIC erstellt wurde.

Weitere Informationen zu Befehlen vom Typ **network** finden Sie in der Befehlszeilenhilfe oder unter [Verwenden der Azure-Befehlszeilenschnittstelle mit der Azure-Ressourcenverwaltung](azure-cli-arm-commands.md).

#### Suchen des Betriebssystemimage

Derzeit kann ein Betriebssystem nur auf der Grundlage des Imageherausgebers gesucht werden. Führen Sie daher den folgenden Befehl aus, um eine Liste mit Herausgebern von Betriebssystemimages für den gewünschten Standort zu erhalten.

	azure vm image list-publishers "westus"

Wählen Sie dann einen Herausgeber aus der Liste aus, und generieren Sie eine Liste mit den Images dieses Herausgebers, indem Sie den folgenden Befehl ausführen.

	azure vm image list "westus" "CoreOS"

Wählen Sie abschließend wie im folgenden Beispiel ein Betriebssystemimage aus der Liste aus.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

Speichern Sie den URN-Namen des Image, das Sie auf Ihrem virtuellen Computer laden möchten. Sie werden ihn später in diesem Artikel benötigen.

#### Erstellen eines virtuellen Computers

Nun können Sie den Befehl **vm create** mit allen erforderlichen Informationen ausführen und einen virtuellen Computer erstellen. An dieser Stelle ist die Übergabe der öffentlichen IP-Adresse optional, da die NIC bereits über diese Information verfügt. Der Befehl sieht in etwa wie in folgendem Beispiel aus. _testvm_ ist hierbei der Name des virtuellen Computers, der in der Ressourcengruppe _testrg_ erstellt wurde.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

Führen Sie den folgenden Befehl aus, um den virtuellen Computer zu starten:

	azure vm start "testrg" "testvm"

Führen Sie anschließend den Befehl "**ssh username@ipaddress**" aus, um eine SSH-Verbindung herzustellen. Mit dem folgenden Befehl können Sie schnell die IP-Adresse der öffentlichen IP-Ressource anzeigen.

	azure network public-ip show "testrg" "testip"

Das Verwalten dieses virtuellen Computers ist ganz einfach mit **VM**-Befehlen. Weitere Informationen finden Sie unter [Verwenden der Azure-CLI mit der Azure-Ressourcenverwaltung](azure-cli-arm-commands.md).

### Schnellerstellung mit „vm quick-create“

Mit dem neuen Befehl **vm quick-create** fallen bei der Erstellung virtueller Computer die meisten der imperativen Schritte weg. Dies ist hilfreich, wenn Sie einfache virtuelle Computer zu Testzwecken erstellen möchten oder die Netzwerkkonfiguration nebensächlich ist. Der Befehl ist interaktiv, und Sie müssen vor der Ausführung lediglich den URN des Betriebssystemimage ermitteln.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

Die Azure-Befehlszeilenschnittstelle erstellt einen virtuellen Computer mit Standardgröße. Außerdem werden ein Speicherkonto, eine NIC, ein virtuelles Netzwerk und Subnetz sowie eine öffentliche IP-Adresse erstellt. Nach dem Start des virtuellen Computers können Sie über die öffentliche IP-Adresse eine SSH-Verbindung herstellen.

### Verwenden von Ressourcengruppenvorlagen

#### Suchen und Konfigurieren einer Ressourcengruppenvorlage

1. Sie können eine eigene Vorlage erstellen, eine Vorlage aus dem Katalog auswählen oder die bei [GitHub](https://github.com/azurermtemplates/azurermtemplates) verfügbaren Vorlagen verwenden. Wir verwenden für den Anfang eine Vorlage namens „CoreOS.CoreOSStable.0.2.40-preview“ aus dem Vorlagenkatalog. Verwenden Sie zum Auflisten der verfügbaren Vorlagen im Katalog den folgenden Befehl. Da hier unzählige Vorlagen zur Auswahl stehen, empfiehlt es sich, die Ergebnisse auf Seiten umzubrechen. Für die Suche nach interessanten Vorlagen können Sie auch **grep** oder **findstr** (unter Windows) oder Ihren bevorzugten Befehl für die Zeichenfolgensuche verwenden. Alternativ können Sie die Option **--json** verwenden und die gesamte Liste im JSON-Format herunterladen, um die Suche zu vereinfachen.

		azure group template list

	In der Antwort werden die Namen des Herausgebers und der Vorlage ähnlich wie im nachfolgenden Beispiel gezeigt aufgeführt (es gibt allerdings bedeutend mehr Ergebnisse).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. Verwenden Sie den folgenden Befehl, um die Details der Vorlage anzuzeigen:

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	Dadurch werden beschreibende Informationen zur Vorlage zurückgegeben. Die verwendete Vorlage erstellt einen virtuellen Linux-Computer.

3. Wenn Sie eine Vorlage ausgewählt haben, können Sie sie mit dem folgenden Befehl herunterladen.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	Nach Herunterladen der Vorlage können Sie sie ändern, um sie besser an Ihre Anforderungen anzupassen. So können Sie beispielsweise der Vorlage eine andere Ressource hinzufügen.

	>[AZURE.NOTE]Falls Sie die Vorlage ändern, überprüfen Sie sie anschließend mit dem Befehl `azure group template validate`, bevor Sie damit eine Ressourcengruppe erstellen oder eine bereits vorhandene Ressourcengruppe ändern.

4. Um die Ressourcengruppenvorlage für Ihre Verwendung zu konfigurieren, öffnen Sie die Vorlagendatei in einem Text-Editor. Beachten Sie die **JSON-Parametersammlung** am oberen Rand. Diese enthält eine Liste der Parameter, die diese Vorlage erwartet, um die von der Vorlage beschriebenen Ressourcen zu erstellen. Einige Parameter besitzen unter Umständen Standardwerte, andere geben dagegen die Art des Werts oder einen zulässigen Wertebereich an.

	Wenn Sie eine Vorlage verwenden, können Sie Parameter als Teil der Befehlszeilenparameter bereitstellen oder eine Datei angeben, die die Parameterwerte enthält. Wertfelder** können** zwar auch direkt in den **Parameterabschnitt** der Vorlage geschrieben werden, dadurch wird die Vorlage allerdings stark an eine bestimmte Bereitstellung gebunden und lässt sich nicht mehr ohne Weiteres wiederverwenden. In beiden Fällen müssen die Parameter im JSON-Format sein und Sie müssen Ihre eigenen Werte für solche Schlüssel angeben, die nicht über Standardwerte verfügen.

	Wenn Sie also beispielsweise eine Datei mit den Parametern für die Vorlage „CoreOS.CoreOSStable.0.2.40-preview“ erstellen möchten, können Sie auf der Grundlage der folgenden Daten eine Datei namens „params.json“ erstellen. Ersetzen Sie die Werte in diesem Beispiel durch Ihre eigenen Werte. Geben Sie für **Location** eine Azure-Region in Ihrer Nähe an (beispielsweise **North Europe** oder **South Central US**). (In diesem Beispiel wird **West US** verwendet.)

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. Verwenden Sie nach dem Speichern der Datei params.json den folgenden Befehl, um eine neue Ressourcengruppe basierend auf der Vorlage zu erstellen. Der Parameter `-e` gibt die im vorherigen Schritt erstellte Datei "params.json" an. Ersetzen Sie **testRG** durch den gewünschten Gruppennamen und **TestDeploy** durch den Namen Ihrer Bereitstellung. Der Standort muss dem Standort aus der Parametervorlagendatei „params.json“ entsprechen.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	Dieser Befehl gibt „OK“ zurück, nachdem die Bereitstellung hochgeladen wurde, jedoch bevor die Bereitstellung auf die Ressourcen in der Gruppe angewendet wurde. Verwenden Sie den folgenden Befehl, um den Status der Bereitstellung zu prüfen.

		azure group deployment show "testRG" "testDeploy"

	Mit **ProvisioningState** wird der Status der Bereitstellung angezeigt.

	War die Bereitstellung erfolgreich, erscheint eine Ausgabe wie im folgenden Beispiel.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]Wenn Sie erkennen, dass Ihre Konfiguration nicht richtig ist und eine lang laufende Bereitstellung angehalten werden muss, verwenden Sie den folgenden Befehl.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Wenn Sie keinen Bereitstellungsnamen angeben, wird automatisch einer anhand des Namens der Vorlagendatei erstellt. Dieser Name wird als Teil der Ausgabe des Befehls `azure group create` zurückgegeben.

6. Verwenden Sie zum Anzeigen der Gruppe den folgenden Befehl.

		azure group show "testRG"

	Dieser Befehl gibt Informationen zu den Ressourcen in der Gruppe zurück. Sind mehreren Gruppen vorhanden, können Sie mit dem Befehl `azure group list` eine Liste mit Gruppennamen abrufen. Verwenden Sie dann `azure group show`, um die Details einer bestimmten Gruppe anzuzeigen.

7. Anstelle der Vorlagen aus der Vorlagenbibliothek können Sie auch aktuelle Vorlagen über GitHub beziehen. Suchen Sie auf [GitHub.com](http://www.github.com) nach "AzureRmTemplates". Wählen Sie das Repository „AzureRmTemplates“ aus, und suchen Sie nach Vorlagen, die für Sie interessant sind (beispielsweise _101-simple-vm-from-image_). Wenn Sie die Vorlage anklicken, sehen Sie, dass sie unter anderem die Datei „azuredeploy.json“ enthält. Das ist die Vorlage, die Sie in Ihrem Befehl mit einer Option vom Typ **--template-url** verwenden möchten. Öffnen Sie die Vorlage im _Rohmodus_, und kopieren Sie die URL aus der Adressleiste des Browsers. Mit dieser URL können Sie direkt eine Bereitstellung erstellen, anstatt eine Vorlage aus einem Katalog herunterzuladen. Verwenden Sie hierzu einen Befehl wie im folgenden Beispiel.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]Die JSON-Vorlage muss im _Rohmodus_ geöffnet werden. Im normalen Modus wird in der Adressleiste des Browsers eine andere URL angezeigt. Klicken Sie rechts oben auf die Schaltfläche **Raw**, während die Datei in GitHub angezeigt wird, um sie im _Rohmodus_ zu öffnen.

#### Arbeiten mit Ressourcen

Auch wenn Vorlagen es Ihnen ermöglichen, gruppenweite Änderungen an der Konfiguration zu deklarieren, müssen Sie manchmal mit nur einer einzelnen Ressource arbeiten. Verwenden Sie dazu die Befehle vom Typ `azure resource`.

> [AZURE.NOTE]Mit Ausnahme des Befehls `list` müssen Sie allen anderen Befehlen vom Typ `azure resource` mithilfe des Parameters `-o` die API-Version der Ressource angeben, mit der Sie arbeiten. Wenn Sie die zu verwendende API-Version nicht kennen, öffnen Sie die Vorlagendatei und gehen Sie zum Feld **apiVersion** der Ressource.

1. Verwenden Sie den folgenden Befehl, um alle Ressourcen in einer Gruppe aufzulisten.

		azure resource list "testRG"

2. Verwenden Sie den folgenden Befehl, um einzelne Ressourcen in der Gruppe anzuzeigen:

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	Beachten Sie den Parameter **Microsoft.ClassicCompute/virtualMachines**. Mit diesem Parameter wird der Typ der Ressourcen angegeben, für den Sie Informationen anfordern. In der zuvor heruntergeladenen Vorlagendatei wird der gleiche Wert verwendet, um die Art der in der Vorlage beschriebenen VM-Ressource zu definieren.

	Dieser Befehl gibt Informationen zum virtuellen Computer zurück.

3. Bei der Betrachtung der Details einer Ressource empfiehlt sich häufig die Verwendung des Parameters `--json`. Er macht die Ausgabe leichter lesbar, da es sich bei einigen Werten um geschachtelte Strukturen oder Sammlungen handelt. Im folgenden Beispiel sehen Sie, wie die Ergebnisse des **show**-Befehls als JSON-Dokument zurückgegeben werden.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]Sie können die JSON-Daten in einer Datei speichern. Verwenden Sie dazu das Zeichen „ &gt;“, um die Ausgabe in die Datei zu leiten. Beispiel:
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. Verwenden Sie den folgenden Befehl, um eine bestehende Ressource zu löschen.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## Protokollierung

Verwenden Sie den Befehl `azure group log show`, um protokollierte Informationen zu Vorgängen für eine Gruppe anzuzeigen. Standardmäßig wird dadurch der letzte Vorgang aufgelistet, der für die Gruppe durchgeführt wurde. Mit dem optionalen Parameter `--all` können Sie alle Vorgänge anzeigen. Für die letzte Bereitstellung können Sie `--last-deployment` verwenden. Wenn Sie sich dagegen für eine bestimmte Bereitstellung interessieren, verwenden Sie `--deployment`, und geben Sie den Namen der Bereitstellung an. Im folgenden Beispiel wird ein Protokoll aller Vorgänge zurückgegeben, die für die Gruppe "MyGroup" durchgeführt wurden.

	azure group log show mygroup --all

## Nächste Schritte

* Weitere Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle][clisetup].
* Weitere Informationen zur Verwendung des Azure-Ressourcen-Managers mit Azure PowerShell finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
* Weitere Informationen zur Verwendung des Azure-Ressourcen-Managers im Azure-Portal finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli-install.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Oct15_HO1-->