<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="Service Management" pageTitle="How to use Azure service management APIs (PHP)" metaKeywords="" description="Learn how to use the Azure PHP Service Management APIs to manage cloud services and other Azure applications." metaCanonical="" services="" documentationCenter="PHP" title="How to use Service Management from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

Verwenden der Dienstverwaltung aus PHP
======================================

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus PHP erläutert. Die [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php)-Klasse im [Azure-SDK für PHP](../php-download-sdk/) unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Verwaltungsportal](https://manage.windowsazure.com/) zur Verfügung stehen (z. B. **Erstellen, Aktualisieren und Löschen von Clouddiensten, Bereitstellungen, Speicherdiensten und Affinitätsgruppen**). Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen.

Inhaltsverzeichnis
------------------

-   [Was ist Dienstverwaltung?](#WhatIs)
-   [Konzepte](#Concepts)
-   [Erstellen einer PHP-Anwendung](#CreateApplication)
-   [Abrufen der Azure-Clientbibliotheken](#GetClientLibraries)
-   [Vorgehensweise: Herstellen einer Verbindung zur Dienstverwaltung](#Connect)
-   [Vorgehensweise: Auflisten verfügbarer Standorte](#ListAvailableLocations)
-   [Vorgehensweise: Erstellen eines Clouddiensts](#CreateCloudService)
-   [Vorgehensweise: Löschen eines Clouddiensts](#DeleteCloudService)
-   [Vorgehensweise: Erstellen einer Bereitstellung](#CreateDeployment)
-   [Vorgehensweise: Aktualisieren einer Bereitstellung](#UpdateDeployment)
-   [Vorgehensweise: Verschieben von Bereitstellungen zwischen Staging und Produktion](#MoveDeployments)
-   [Vorgehensweise: Löschen einer Bereitstellung](#DeleteDeployment)
-   [Vorgehensweise: Erstellen eines Speicherdiensts](#CreateStorageService)
-   [Vorgehensweise: Löschen eines Speicherdiensts](#DeleteStorageService)
-   [Vorgehensweise: Erstellen einer Affinitätsgruppe](#CreateAffinityGroup)
-   [Vorgehensweise: Löschen einer Affinitätsgruppe](#DeleteAffinityGroup)

Was ist Dienstverwaltung?
-------------------------

Die Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Verwaltungsportal](https://manage.windowsazure.com/) verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure-SDK für PHP können Sie Ihre Clouddienste, Speicherkonten und Affinitätsgruppen verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen](/en-us/pricing/free-trial/).

Konzepte
--------

Das Azure-SDK für PHP umfasst die [Azure-Dienstverwaltungs-API](http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx), eine REST-API. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509s v3-Zertifikaten gegenseitig authentifiziert. Der Zugriff auf die Dienstverwaltung kann über einen in Azure ausgeführten Dienst erfolgen oder direkt über das Internet, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

Erstellen einer PHP-Anwendung
-----------------------------

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die die Azure-Dienstverwaltung verwendet, ist das Referenzieren von Klassen im Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

Abrufen der Azure-Clientbibliotheken
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Vorgehensweise: Herstellen einer Verbindung zur Dienstverwaltung
----------------------------------------------------------------

Um eine Verbindung zum Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und den Pfad zu einem gültigen Verwaltungszertifikat. Sie können die Abonnement-ID über das [Verwaltungsportal](https://manage.windowsazure.com/) abrufen und verschiedene Methoden zum Erstellen von Verwaltungszertifikaten nutzen. In diesem Leitfaden wird [OpenSSL](http://www.openssl.org/) verwendet, das Sie [für Windows herunterladen](http://www.openssl.org/related/binaries.html) und in einer Konsole ausführen können.

Tatsächlich müssen Sie zwei Zertifikate erstellen, eins für den Server (eine `.cer`-Datei) und eins für den Client (eine `.pem`-Datei). Zum Erstellen der `.pem`-Datei führen Sie den folgenden Befehl aus:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Zum Erstellen des `.pem`-Zertifikats führen Sie diesen Befehl aus:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Übersicht über Zertifikate in Windows Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg981935.aspx). Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation auf <http://www.openssl.org/docs/apps/openssl.html>.

Falls Sie die Datei mit den Veröffentlichungseinstellungen mithilfe der [Azure-Befehlszeilentools](../command-line-tools/) heruntergeladen und importiert haben, können Sie die von den Tools erstellte `.pem`-Datei verwenden und müssen keine eigene erstellen. Die Tools erstellen eine `.cer`-Datei und laden sie nach Azure hoch; außerdem legen sie die entsprechende `.pem`-Datei im `.azure`-Verzeichnis auf Ihrem Computer ab (in Ihrem Benutzerverzeichnis).

Nachdem Sie diese Dateien erstellt haben, müssen Sie die `.cer`-Datei über das [Verwaltungsportal](https://manage.windowsazure.com/) nach Azure hochladen und sich den Speicherort der `.pem`-Datei notieren.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei nach Azure hochgeladen haben, können Sie eine Verbindung zum Azure-Verwaltungsendpunkt herstellen, indem Sie eine Verbindungszeichenfolge erstellen und an die **createServiceManagementService**-Methode in der **ServicesBuilder**-Klasse übergeben:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

Im vorstehenden Beispiel ist `$serviceManagementRestProxy` ein [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php)-Objekt. Die **ServiceManagementRestProxy**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

Vorgehensweise: Auflisten verfügbarer Standorte
-----------------------------------------------

Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die Methode **ServiceManagementRestProxy-\>listLocations**:

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
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Wenn Sie einen Clouddienst, einen Speicherdienst oder eine Affinitätsgruppe erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **listLocations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

-   USA (beliebig)
-   Europa (beliebig)
-   Westeuropa
-   Asien (beliebig)
-   Südostasien
-   Ostasien
-   USA (Mitte/Norden)
-   Nordeuropa
-   USA (Mitte/Süden)
-   USA (Westen)
-   USA (Osten)

> [WACOM.NOTE] In den folgenden Codebeispielen werden Standorte als Zeichenfolgen an Methoden übergeben. Mithilfe der `WindowsAzure\ServiceManagement\Models\Locations`-Klasse können Sie Standorte jedoch auch als Aufzählungen übergeben. Anstatt beispielsweise "USA (Westen)" an eine Methode zu übergeben, die einen Standort akzeptiert, können Sie `Locations::WEST_US` übergeben.

Vorgehensweise: Erstellen eines Clouddiensts
--------------------------------------------

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-[Clouddienst](../cloud-services-what-is/) bezeichnet (in früheren Azure-Versionen als *gehosteter Dienst*). Mit der Methode **createHostedServices** können Sie einen neuen gehosteten Dienst erstellen, indem Sie einen Namen des gehosteten Diensts (der in Azure eindeutig sein muss), eine Bezeichnung (base64-codierter Name des gehosteten Diensts) und ein **CreateServiceOptions**-Objekt angeben. Für das [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php)-Objekt ist die Angabe des Standorts *oder* der Affinitätsgruppe für den Dienst zulässig.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Anstelle von setLocation kann setAffinityGroup zum
        // Angeben einer Affinitätsgruppe verwendet werden.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Sie können alle gehosteten Dienste für Ihr Abonnement mit der **listHostedServices**-Methode auflisten, die ein [ListHostedServicesResult](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php)-Objekt zurückgibt. Durch Aufrufen der **getHostedServices**-Methode können Sie dann eine Schleife durch ein Array von [HostedServices]-Objekten durchlaufen und Diensteigenschaften abrufen:

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

Vorgehensweise: Löschen eines Clouddiensts
------------------------------------------

Sie können einen Clouddienst löschen, indem Sie den Dienstnamen an die Methode **deleteHostedService** übergeben:

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. (Nähere Informationen finden Sie unter [Vorgehensweise: Löschen einer Bereitstellung](#DeleteDeployment).)

Vorgehensweise: Erstellen einer Bereitstellung
----------------------------------------------

Die Methode **createDeployment** lädt ein neues [Dienstpaket](http://msdn.microsoft.com/de-de/library/windowsazure/gg433093) hoch und erstellt eine neue Bereitstellung in der Staging- oder Produktionsumgebung. Für diese Methode sind folgende Parameter verfügbar:

-   **\$name**: Der Name des gehosteten Diensts.
-   **\$deploymentName**: Der Name der Bereitstellung.
-   **\$slot**: Eine Aufzählung, die den Staging- oder Produktionsslot angibt.
-   **\$packageUrl**: Die URL für das Bereitstellungspaket (eine .cspgk-Datei). Die Paketdatei muss in einem Azure-Blob-Speicherkonto unter demselben Abonnement gespeichert werden wie der gehostete Dienst, in den das Paket hochgeladen wird. Zum Erstellen eines Bereitstellungspakets können Sie die [Azure-PowerShell-Cmdlets](../install-configure-powershell/) oder das [CSPack-Befehlszeilentool](http://msdn.microsoft.com/de-de/library/windowsazure/gg432988.aspx) verwenden.
-   **\$configuration**: Die Dienstkonfigurationsdatei (.cscfg-Datei).
-   **\$label**: Der base64-codierte Name des gehosteten Diensts.

Mit dem folgenden Beispiel wird eine neue Bereitstellung im Produktionsslot eines gehosteten Diensts namens `myhostedservice` erstellt:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
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
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **createDeployment** abgerufen werden kann, indem das von **createDeployment** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **getDeployment** können Sie auf Bereitstellungseigenschaften zugreifen. Mit dem folgenden Beispiel wird eine Bereitstellung durch Angabe des Bereitstellungsslots im [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php)-Objekt abgerufen, Sie können stattdessen aber auch den Bereitstellungsnamen angeben. Außerdem iteriert das Beispiel durch alle Instanzen für die Bereitstellung:

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

Vorgehensweise: Aktualisieren einer Bereitstellung
--------------------------------------------------

Ein Bereitstellung kann mit der Methode **changeDeploymentConfiguration** oder der Methode **updateDeploymentStatus** aktualisiert werden.

Die Methode **changeDeploymentConfiguration** ermöglicht das Hochladen einer neuen Dienstkonfigurationsdatei (`.cscfg`), wodurch mehrere Diensteinstellungen geändert werden können (einschließlich der Anzahl der Instanzen in einer Bereitstellung). Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](http://msdn.microsoft.com/de-de/library/windowsazure/ee758710.aspx). Im folgenden Beispiel wird das Hochladen einer neuen Dienstkonfigurationsdatei veranschaulicht:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
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
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **changeDeploymentConfiguration** abgerufen werden kann, indem das von **changeDeploymentConfiguration** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **updateDeploymentStatus** können Sie den Status einer Bereitstellung auf WIRD AUSGEFÜHRT oder ANGEHALTEN festlegen. Im folgenden Beispiel wird der Status einer Bereitstellung im Produktionsslot des gehosteten Diensts `myhostedservice` auf WIRD AUSGEFÜHRT festgelegt:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Verschieben von Bereitstellungen zwischen Staging und Produktion
--------------------------------------------------------------------------------

Azure bietet zwei Bereitstellungsumgebungen: Staging und Produktion. Normalerweise wird ein Dienst in der Stagingumgebung bereitgestellt, um ihn zu testen, bevor er in der Produktionsumgebung bereitgestellt wird. Wenn Sie den Dienst von der Staging- in die Produktionsumgebung hochstufen möchten, ist keine erneute Bereitstellung erforderlich. Sie können hierzu einfach die Bereitstellungen austauschen. (Weitere Informationen zum Austauschen von Bereitstellungen finden Sie unter [Übersicht über das Verwalten von Bereitstellungen in Windows Azure](http://msdn.microsoft.com/de-de/library/windowsazure/hh386336.aspx).)

Das folgende Beispiel zeigt, wie Sie mithilfe der Methode **swapDeployment** zwei Bereitstellungen (namens `v1` und `v2`) austauschen. Im Beispiel befindet sich Bereitstellung `v1` vor dem Aufruf von **swapDeployment** im Produktionsslot und Bereitstellung `v2` im Stagingslot. Nach Aufruf von **swapDeployment** befindet sich `v2` im Produktions- und `v1` im Stagingslot.

    require_once 'vendor\autoload.php';  

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Löschen einer Bereitstellung
--------------------------------------------

Verwenden Sie zum Löschen einer Bereitstellung die Methode **deleteDeployment**. Im folgenden Beispiel wird eine Bereitstellung in der Stagingumgebung gelöscht, indem die **setSlot**-Methode für ein [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php) aufgerufen und dieses dann an **deleteDeployment** übergeben wird. Anstatt eine Bereitstellung anhand des Slots anzugeben, können Sie die **setName**-Methode für die [GetDepolymentOptions]-Klasse aufrufen, um eine Bereitstellung anhand des Bereitstellungsnamens anzugeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Erstellen eines Speicherdiensts
-----------------------------------------------

Ein [Speicherdienst](../storage-whatis-account/) gewährt Ihnen Zugriff auf Azure-[Blobs](../storage-php-how-to-use-blobs/), -[Tabellen](../storage-php-how-to-use-table-storage/) und -[Warteschlangen](../storage-php-how-to-use-queues/). Zum Erstellen eines Speicherdiensts müssen Sie Folgendes angeben: einen Namen für den Dienst (bestehend aus 3 bis 24 Kleinbuchstaben und innerhalb von Azure eindeutig), eine Bezeichnung (ein base64-codierter Name für den Dienst mit maximal 100 Zeichen) und entweder einen Standort oder eine Affinitätsgruppe. Die Angabe einer Beschreibung für den Dienst ist optional. Der Standort, die Affinitätsgruppe und die Beschreibung werden in einem [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php)-Objekt festgelegt, das an die Methode **createStorageService** übergeben wird. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt. Wenn Sie eine Affinitätsgruppe verwenden möchten, müssen Sie zunächst eine solche Gruppe erstellen (Informationen finden Sie unter [Vorgehensweise: Erstellen einer Affinitätsgruppe](#CreateAffinityGroup)) und mit der Methode **CreateServiceOptions-\>setAffinityGroup** festlegen.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // REST-Proxy erstellen.
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
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **createStorageService** abgerufen werden kann, indem das von **createStorageService** zurückgegebene Ergebnis an die Methode **getOperationStatus** übergeben wird.

Mit der Methode **listStorageServices** können Sie Ihre Speicherkonten und deren Eigenschaften auflisten:

    // REST-Proxy erstellen.
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

Vorgehensweise: Löschen eines Speicherdiensts
---------------------------------------------

Sie können einen Speicherdienst löschen, indem Sie den Speicherdienstnamen an die Methode **deleteStorageService** übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten ebenfalls gelöscht (Blobs, Tabellen und Warteschlangen).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Erstellen einer Affinitätsgruppe
------------------------------------------------

Eine Affinitätsgruppe ist eine logische Gruppierung von Azure-Diensten, die Azure anweist, die Dienste für eine optimale Leistung an einem bestimmten Standort zu platzieren. Sie können z. B. eine Affinitätsgruppe am Standort "USA (Westen)" anlegen und dann einen [Clouddienst](#CreateCloudService) in dieser Affinitätsgruppe erstellen. Wenn Sie dann in derselben Affinitätsgruppe einen Speicherdienst erstellen, weiß Azure, dass der Dienst am Standort "USA (Westen)" platziert werden und im Rechenzentrum optimiert werden soll, um die beste Leistung mit den Clouddiensten in derselben Affinitätsgruppe zu erzielen.

Zum Erstellen einer Affinitätsgruppe benötigen Sie einen Namen, eine Bezeichnung (den base64-codierten Namen) und einen Standort. Optional können Sie eine Beschreibung angeben:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Nachdem Sie eine Affinitätsgruppe erstellt haben, können Sie beim [Erstellen eines Speicherdiensts](#CreateStorageService) die Gruppe anstelle eines Standorts angeben.

Sie können Affinitätsgruppen auflisten und ihre Eigenschaften überprüfen, indem Sie die **listAffinityGroups**-Methode und dann die entsprechenden Methoden für die [AffinityGroup](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php)-Klasse aufrufen:

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

Vorgehensweise: Löschen einer Affinitätsgruppe
----------------------------------------------

Sie können eine Affinitätsgruppe löschen, indem Sie den Gruppennamen an die Methode **deleteAffinityGroup** übergeben. Bevor Sie eine Affinitätsgruppe löschen können, muss diese von allen Diensten getrennt werden (alternativ müssen Dienste, die die Affinitätsgruppe verwenden, gelöscht werden).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // REST-Proxy erstellen.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // Eine Affinitätsgruppe muss von allen Diensten getrennt werden, 
        // bevor sie gelöscht werden kann.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
