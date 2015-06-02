<properties 
	pageTitle="Verwenden der Azure-Dienstverwaltungs-APIs (PHP)" 
	description="Hier erfahren Sie, wie Sie die Azure-PHP-Dienstverwaltungs-APIs für die Verwaltung von Cloud-Diensten und weiteren Azure-Anwendungen nutzen." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tomfitz"/>

# Verwenden der Dienstverwaltung aus PHP

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus PHP erläutert. Die [ServiceManagementRestProxy]-Klasse im [Azure-SDK für PHP][download-SDK-PHP] unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Verwaltungsportal][management-portal] zur Verfügung stehen (z. B. **Erstellen, Aktualisieren und Löschen von Clouddiensten, Bereitstellungen, Speicherdiensten und Affinitätsgruppen**). Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen.

## Was ist Dienstverwaltung?
Die Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Verwaltungsportal][management-portal] verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure-SDK für PHP können Sie Ihre Clouddienste, Speicherkonten und Affinitätsgruppen verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen][win-azure-account].

## Konzepte
Das Azure-SDK für PHP umfasst die [Azure-Dienstverwaltungs-API][svc-mgmt-rest-api], eine REST-API. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509s v3-Zertifikaten gegenseitig authentifiziert. Der Zugriff auf die Dienstverwaltung kann über einen in Azure ausgeführten Dienst erfolgen oder direkt über das Internet, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

## Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die die Azure-Dienstverwaltung verwendet, ist das Referenzieren von Klassen im Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

## Abrufen der Azure-Clientbibliotheken

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## Herstellen einer Verbindung mit der Dienstverwaltung

Um eine Verbindung zum Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und den Pfad zu einem gültigen Verwaltungszertifikat. Sie können die Abonnement-ID über das [Verwaltungsportal][management-portal] abrufen und verschiedene Methoden zum Erstellen von Verwaltungszertifikaten nutzen. In diesem Leitfaden wird [OpenSSL](http://www.openssl.org/) verwendet, das Sie [für Windows herunterladen](http://www.openssl.org/related/binaries.html) und in einer Konsole ausführen können.

Tatsächlich müssen Sie zwei Zertifikate erstellen, eins für den Server (eine `.cer`-Datei) und eins für den Client (eine `.pem`-Datei). Führen Sie zum Erstellen der `.pem`-Datei Folgendes aus:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Führen Sie zum Erstellen des `.cer`-Zertifikats Folgendes aus:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Übersicht über Zertifikate in Windows Azure](http://msdn.microsoft.com/library/azure/gg981929.aspx). Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation auf [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Falls Sie die Datei mit den Veröffentlichungseinstellungen mithilfe der [Azure-Befehlszeilentools][command-line-tools] heruntergeladen und importiert haben, können Sie die von den Tools erstellte `.pem`-Datei verwenden und müssen keine eigene erstellen. Mit den Tools wird eine `.cer`-Datei erstellt, die Sie zu Azure hochladen. Darüber hinaus wird die entsprechende `.pem`-Datei im Verzeichnis `.azure` auf Ihrem Computer (in Ihrem Benutzerverzeichnis) abgelegt.

Nachdem Sie diese Dateien erstellt haben, müssen Sie die `.cer`-Datei über das [Verwaltungsportal][management-portal] zu Azure hochladen und sich den Speicherort der `.pem`-Datei notieren.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt herstellen, indem Sie eine Verbindungszeichenfolge erstellen und an die Methode **createServiceManagementService** in der Klasse **ServicesBuilder** übergeben:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;

	$conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

Im vorstehenden Beispiel ist `$serviceManagementRestProxy` ein [ServiceManagementRestProxy]-Objekt. Die **ServiceManagementRestProxy**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

## Auflisten verfügbarer Standorte

Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die Methode **ServiceManagementRestProxy->listLocations**:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	
		$result = $serviceManagementRestProxy->listLocations();
	
		$locations = $result->getLocations();

		foreach($locations as $location){
		      echo $location->getName()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Wenn Sie einen Clouddienst, einen Speicherdienst oder eine Affinitätsgruppe erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **listLocations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

- USA (beliebig) 
- Europa (beliebig) 
- Westeuropa 
- Asien (beliebig) 
- Südostasien 
- Ostasien 
- USA (Mitte/Norden) 
- Nordeuropa 
- USA (Mitte/Süden) 
- USA (Westen) 
- USA (Osten)

In den folgenden Codebeispielen werden Standorte als Zeichenfolgen an Methoden übergeben. Mithilfe der <code>WindowsAzure\\ServiceManagement\\Models\\Locations</code>-Klasse können Sie Standorte jedoch auch als Enumerationen übergeben. Anstatt beispielsweise "USA (Westen)" an eine Methode zu übergeben, die einen Standort akzeptiert, können Sie <code>Locations::WEST_US</code> übergeben.

## Erstellen eines Clouddiensts

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-[Clouddienst] bezeichnet (in früheren Azure-Versionen als *gehosteter Dienst*). Mit der Methode **createHostedServices** können Sie einen neuen gehosteten Dienst erstellen, indem Sie einen Namen des gehosteten Diensts (der in Azure eindeutig sein muss), eine Bezeichnung (base64-codierter Name des gehosteten Diensts) und ein **CreateServiceOptions**-Objekt angeben. Für das [CreateServiceOptions]-Objekt ist die Angabe des Standorts *oder* der Affinitätsgruppe für den Dienst zulässig.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		// Instead of setLocation, you can use setAffinityGroup
		// to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Sie können alle gehosteten Dienste für Ihr Abonnement mit der **listHostedServices**-Methode auflisten, die ein [ListHostedServicesResult]-Objekt zurückgibt. Durch Aufrufen der **getHostedServices**-Methode können Sie dann eine Schleife durch ein Array von [HostedServices]-Objekten durchlaufen und Diensteigenschaften abrufen:

	$listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

	$hosted_services = $listHostedServicesResult->getHostedServices();

	foreach($hosted_services as $hosted_service){
		echo "Service name: ".$hosted_service->getName()."<br />";
		echo "Management URL: ".$hosted_service->getUrl()."<br />";
		echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
		echo "Location: ".$hosted_service->getLocation()."<br />";
		echo "------<br />";
		}

Wenn Sie Informationen über einen bestimmten gehosteten Dienst abrufen möchten, übergeben Sie den Namen des gehosteten Diensts an die Methode **getHostedServiceProperties**:

	$getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
		
	$hosted_service = $getHostedServicePropertiesResult->getHostedService();
		
	echo "Service name: ".$hosted_service->getName()."<br />";
	echo "Management URL: ".$hosted_service->getUrl()."<br />";
	echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
	echo "Location: ".$hosted_service->getLocation()."<br />";

Nachdem Sie einen Clouddienst erstellt haben, können Sie Ihren Code mit der Methode [createDeployment](#CreateDeployment) für den Dienst bereitstellen.

##<a id="DeleteCloudService"></a>Löschen eines Clouddiensts

Sie können einen Clouddienst löschen, indem Sie den Dienstnamen an die Methode **deleteHostedService** übergeben:

	$serviceManagementRestProxy->deleteHostedService("myhostedservice");

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. (Weitere Informationen finden Sie unter [Löschen einer Bereitstellung](#DeleteDeployment).)

## Erstellen einer Bereitstellung

Die Methode **createDeployment** lädt ein neues [Dienstpaket] hoch und erstellt eine neue Bereitstellung in der Staging- oder Produktionsumgebung. Für diese Methode sind folgende Parameter verfügbar:

* **$name**: Der Name des gehosteten Diensts.
* **$deploymentName**: Der Name der Bereitstellung.
* **$slot**: Eine Enumeration, die den Staging- oder Produktionsslot angibt.
* **$packageUrl**: Die URL für das Bereitstellungspaket (eine .cspgk-Datei). Die Paketdatei muss in einem Azure-Blobspeicherkonto unter demselben Abonnement gespeichert werden wie der gehostete Dienst, in den das Paket hochgeladen wird. Zum Erstellen eines Bereitstellungspakets können Sie die [Azure-PowerShell-Cmdlets] oder das [CSPack-Befehlszeilentool] verwenden.
* **$configuration**: Die Dienstkonfigurationsdatei (.cscfg-Datei).
* **$label**: Der base64-codierte Name des gehosteten Diensts.

Mit dem folgenden Beispiel wird eine neue Bereitstellung im Produktionsslot eines gehosteten Diensts namens `myhostedservice` erstellt:


	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
		$deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
		$packageUrl = "URL_for_.cspkg_file";
		$configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
		$label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
														 $deploymentName,
														 $slot,
														 $packageUrl,
														 $configuration,
														 $label);
		
		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **createDeployment** abgerufen werden kann, indem das von **createDeployment** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **getDeployment** können Sie auf Bereitstellungseigenschaften zugreifen. Mit dem folgenden Beispiel wird eine Bereitstellung durch Angabe des Bereitstellungsslots im [GetDeploymentOptions]-Objekt abgerufen, Sie können stattdessen aber auch den Bereitstellungsnamen angeben. Außerdem iteriert das Beispiel durch alle Instanzen für die Bereitstellung:

	$options = new GetDeploymentOptions();
	$options->setSlot(DeploymentSlot::PRODUCTION);
		
	$getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
	$deployment = $getDeploymentResult->getDeployment();

	echo "Name: ".$deployment->getName()."<br />";
	echo "Slot: ".$deployment->getSlot()."<br />";
	echo "Private ID: ".$deployment->getPrivateId()."<br />";
	echo "Status: ".$deployment->getStatus()."<br />";
	echo "Instances: <br />";
	foreach($deployment->getroleInstanceList() as $roleInstance){
		echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
		echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
		echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
	}
	echo "------<br />";

## Aktualisieren einer Bereitstellung

Ein Bereitstellung kann mit der Methode **changeDeploymentConfiguration** oder der Methode **updateDeploymentStatus** aktualisiert werden.

Die Methode **changeDeploymentConfiguration** ermöglicht das Hochladen einer neuen Dienstkonfigurationsdatei (`.cscfg`), wodurch mehrere Diensteinstellungen geändert werden können (einschließlich der Anzahl der Instanzen in einer Bereitstellung). Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)]. Im folgenden Beispiel wird das Hochladen einer neuen Dienstkonfigurationsdatei veranschaulicht:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myhostedservice";
		$configuration = base64_encode(file_get_contents('path to .cscfg file'));
		$options = new ChangeDeploymentConfigurationOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
		
		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **changeDeploymentConfiguration** abgerufen werden kann, indem das von **changeDeploymentConfiguration** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **updateDeploymentStatus** können Sie den Status einer Bereitstellung auf WIRD AUSGEFÜHRT oder ANGEHALTEN festlegen. Im folgenden Beispiel wird der Status einer Bereitstellung im Produktionsslot eines gehosteten Diensts namens `myhostedservice` auf RUNNING festgelegt:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\Common\ServiceException;
	
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);
		
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Verschieben von Bereitstellungen zwischen Staging und Produktion

Azure stellt zwei Bereitstellungsumgebungen bereit: Staging und Produktion. Normalerweise wird ein Dienst in der Stagingumgebung bereitgestellt, um ihn zu testen, bevor er in der Produktionsumgebung bereitgestellt wird. Wenn Sie den Dienst von der Staging- in die Produktionsumgebung hochstufen möchten, ist keine erneute Bereitstellung erforderlich. Sie können hierzu einfach die Bereitstellungen austauschen. (Weitere Informationen zum Austauschen von Bereitstellungen finden Sie unter [Übersicht über das Verwalten von Bereitstellungen in Windows Azure].)

Das folgende Beispiel zeigt, wie Sie mithilfe der Methode **swapDeployment** zwei Bereitstellungen (namens `v1` und `v2`) austauschen. Vor dem Aufruf von **swapDeployment** befindet sich die Bereitstellung `v1` in dem Beispiel im Produktionsslot und Bereitstellung `v2` im Stagingslot. Nach dem Aufruf von **swapDeployment** ist `v2` in der Produktion und `v1` im Staging.

	require_once 'vendor\autoload.php';	

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Löschen einer Bereitstellung

Verwenden Sie zum Löschen einer Bereitstellung die Methode **deleteDeployment**. Im folgenden Beispiel wird eine Bereitstellung in der Stagingumgebung gelöscht, indem die **setSlot**-Methode für ein [GetDeploymentOptions] aufgerufen und dieses dann an **deleteDeployment** übergeben wird. Anstatt eine Bereitstellung anhand des Slots anzugeben, können Sie die **setName**-Methode für die [GetDeploymentOptions]-Klasse aufrufen, um eine Bereitstellung anhand des Bereitstellungsnamens anzugeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::STAGING);
		
		$result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Erstellen eines Speicherdiensts

Ein [Speicherdienst] gewährt Ihnen Zugriff auf Azure-[Blobs][azure-blobs], -[Tabellen][azure-tables] und -[Warteschlangen][azure-queues]. Zum Erstellen eines Speicherdiensts müssen Sie Folgendes angeben: einen Namen für den Dienst (bestehend aus 3 bis 24 Kleinbuchstaben und innerhalb von Azure eindeutig), eine Bezeichnung (ein base64-codierter Name für den Dienst mit maximal 100 Zeichen) und entweder einen Standort oder eine Affinitätsgruppe. Die Angabe einer Beschreibung für den Dienst ist optional. Der Standort, die Affinitätsgruppe und die Beschreibung werden in einem [CreateServiceOptions]-Objekt festgelegt, das an die Methode **createStorageService** übergeben wird. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt. Wenn Sie eine Affinitätsgruppe verwenden möchten, müssen Sie zuerst eine solche Gruppe erstellen (weitere Informationen finden Sie unter [Erstellen einer Affinitätsgruppe](#CreateAffinityGroup)) und sie dann mit der Methode **CreateServiceOptions->setAffinityGroup** festlegen.

	require_once 'vendor\autoload.php';
	 
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;
	 
	 
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		$options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **createStorageService** abgerufen werden kann, indem das von **createStorageService** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **listStorageServices** können Sie Ihre Speicherkonten und deren Eigenschaften auflisten:

	// Create REST proxy.
	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	$getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
	$storageServices = $getStorageServicesResult->getStorageServices();

	foreach($storageServices as $storageService){
		echo "Service name: ".$storageService->getName()."<br />";
		echo "Service URL: ".$storageService->getUrl()."<br />";
		echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
		echo "Location: ".$storageService->getLocation()."<br />";
		echo "------<br />";
	}

## Löschen eines Speicherdiensts

Sie können einen Speicherdienst löschen, indem Sie den Speicherdienstnamen an die Methode **deleteStorageService** übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten ebenfalls gelöscht (Blobs, Tabellen und Warteschlangen).

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		$serviceManagementRestProxy->deleteStorageService("mystorageservice");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Erstellen einer Affinitätsgruppe

Eine Affinitätsgruppe ist eine logische Gruppierung von Azure-Diensten, die Azure anweist, die Dienste für eine optimale Leistung an einem bestimmten Standort zu platzieren. Sie können z. B. eine Affinitätsgruppe am Standort "USA (West)" anlegen und dann einen [Cloud-Dienst](#CreateCloudService) in dieser Affinitätsgruppe erstellen. Wenn Sie dann in derselben Affinitätsgruppe einen Speicherdienst erstellen, weiß Azure, dass der Dienst am Standort "USA (West)" platziert werden und im Datencenter optimiert werden soll, um die beste Leistung mit den Cloud-Diensten in derselben Affinitätsgruppe zu erzielen.

Zum Erstellen einer Affinitätsgruppe benötigen Sie einen Namen, eine Bezeichnung (den base64-codierten Namen) und einen Standort. Optional können Sie eine Beschreibung angeben:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
	use WindowsAzure\Common\ServiceException;
	 
	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
		$options->setDescription = "My affinity group description.";
		
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Nachdem Sie eine Affinitätsgruppe erstellt haben, können Sie beim [Erstellen eines Speicherdiensts](#CreateStorageService) die Gruppe anstelle eines Standorts angeben.

Sie können Affinitätsgruppen auflisten und ihre Eigenschaften überprüfen, indem Sie die **listAffinityGroups**-Methode und dann die entsprechenden Methoden für die [AffinityGroup]-Klasse aufrufen:

	$result = $serviceManagementRestProxy->listAffinityGroups();
	
	$groups = $result->getAffinityGroups();

	foreach($groups as $group){
		echo "Name: ".$group->getName()."<br />";
		echo "Description: ".$group->getDescription()."<br />";
		echo "Location: ".$group->getLocation()."<br />";
		echo "------<br />";
	}

## Löschen einer Affinitätsgruppe
	
Sie können eine Affinitätsgruppe löschen, indem Sie den Gruppennamen an die Methode **deleteAffinityGroup** übergeben. Bevor Sie eine Affinitätsgruppe löschen können, muss diese von allen Diensten getrennt werden (alternativ müssen Dienste, die die Affinitätsgruppe verwenden, gelöscht werden).

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
		
		// An affinity group must be disassociated from all services 
		// before it can be deleted.
		$serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
[ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[win-azure-account]: /pricing/free-trial/
[storage-account]: storage-create-storage-account.md

[download-SDK-PHP]: php-download-sdk.md
[command-line-tools]: virtual-machines-command-line-tools.md
[Composer]: http://getcomposer.org/
[ServiceManagementSettings]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementSettings.php

[Clouddienst]: cloud-services-what-is.md
[CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/ListHostedServicesResult.php

[Dienstpaket]: http://msdn.microsoft.com/library/windowsazure/gg433093
[Azure-PowerShell-Cmdlets]: install-configure-powershell.md
[CSPack-Befehlszeilentool]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[GetDeploymentOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php

[Übersicht über das Verwalten von Bereitstellungen in Windows Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx
[Speicherdienst]: storage-whatis-account.md
[azure-blobs]: storage-php-how-to-use-blobs.md
[azure-tables]: storage-php-how-to-use-table-storage.md
[azure-queues]: storage-php-how-to-use-queues.md
[AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php


[Azure-Dienstkonfigurationsschema (.cscfg-Datei)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx

<!--HONumber=54-->