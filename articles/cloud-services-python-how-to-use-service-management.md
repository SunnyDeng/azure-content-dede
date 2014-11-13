<properties urlDisplayName="Service Management" pageTitle="Verwenden der Dienstverwaltungs-API (Python) &ndash; Featureleitfaden" metaKeywords="" description="Hier erfahren Sie, wie die programmgesteuerte Durchf&uuml;hrung g&auml;ngiger Dienstverwaltungsaufgaben aus Python funktioniert." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Verwenden der Dienstverwaltung aus Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Verwenden der Dienstverwaltung aus Python

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python erläutert. Die **ServiceManagementService**-Klasse im [Azure-SDK für Python][Azure-SDK für Python] unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Verwaltungsportal][Verwaltungsportal] zur Verfügung stehen (z. B. **Erstellen, Aktualisieren und Löschen von Clouddiensten, Bereitstellungen, Datenverwaltungsdiensten, virtuellen Computern und Affinitätsgruppen**). Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen.

## Inhaltsverzeichnis

-   [Was ist Dienstverwaltung?][Was ist Dienstverwaltung?]
-   [Konzepte][Konzepte]
-   [Gewusst wie: Herstellen einer Verbindung zur Dienstverwaltung][Gewusst wie: Herstellen einer Verbindung zur Dienstverwaltung]
-   [Gewusst wie: Auflisten verfügbarer Standorte][Gewusst wie: Auflisten verfügbarer Standorte]
-   [Gewusst wie: Erstellen eines Clouddiensts][Gewusst wie: Erstellen eines Clouddiensts]
-   [Gewusst wie: Löschen eines Clouddiensts][Gewusst wie: Löschen eines Clouddiensts]
-   [Gewusst wie: Erstellen einer Bereitstellung][Gewusst wie: Erstellen einer Bereitstellung]
-   [Gewusst wie: Aktualisieren einer Bereitstellung][Gewusst wie: Aktualisieren einer Bereitstellung]
-   [Gewusst wie: Verschieben von Bereitstellungen zwischen Staging und Produktion][Gewusst wie: Verschieben von Bereitstellungen zwischen Staging und Produktion]
-   [Gewusst wie: Löschen einer Bereitstellung][Gewusst wie: Löschen einer Bereitstellung]
-   [Gewusst wie: Erstellen eines Speicherdiensts][Gewusst wie: Erstellen eines Speicherdiensts]
-   [Gewusst wie: Löschen eines Speicherdiensts][Gewusst wie: Löschen eines Speicherdiensts]
-   [Gewusst wie: Erstellen einer Affinitätsgruppe][Gewusst wie: Erstellen einer Affinitätsgruppe]
-   [Gewusst wie: Löschen einer Affinitätsgruppe][Gewusst wie: Löschen einer Affinitätsgruppe]
-   [Gewusst wie: Auflisten verfügbarer Betriebssysteme][Gewusst wie: Auflisten verfügbarer Betriebssysteme]
-   [Gewusst wie: Erstellen eines Betriebssystemimage][Gewusst wie: Erstellen eines Betriebssystemimage]
-   [Gewusst wie: Löschen eines Betriebssystemimage][Gewusst wie: Löschen eines Betriebssystemimage]
-   [Gewusst wie: Erstellen eines virtuellen Computers][Gewusst wie: Erstellen eines virtuellen Computers]
-   [Gewusst wie: Löschen eines virtuellen Computers][Gewusst wie: Löschen eines virtuellen Computers]
-   [Nächste Schritte][Nächste Schritte]

## <a name="WhatIs"> </a>Was ist Dienstverwaltung?

Die Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Verwaltungsportal][Verwaltungsportal] verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure-SDK für Python können Sie Ihre Clouddienste, Speicherkonten und Affinitätsgruppen verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen][ein Azure-Konto erstellen].

## <a name="Concepts"> </a>Konzepte

Das Azure-SDK für Python umfasst die [Azure-Dienstverwaltungs-API][Azure-Dienstverwaltungs-API], eine REST-API. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509s v3-Zertifikaten gegenseitig authentifiziert. Der Zugriff auf die Dienstverwaltung kann über einen in Azure ausgeführten Dienst erfolgen oder direkt über das Internet, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

## <a name="Connect"> </a>Gewusst wie: Herstellen einer Verbindung zur Dienstverwaltung

Um eine Verbindung zum Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und ein gültiges Verwaltungszertifikat. Ihre Abonnement-ID können Sie über das [Verwaltungsportal][Verwaltungsportal] abrufen.

> [WACOM.NOTE] Seit dem Azure-SDK für Python Version 0.8.0 ist es jetzt möglich, bei Ausführung von Windows Zertifikate zu verwenden, die mit OpenSSL erstellt wurden. Dafür ist Python 2.7.4 oder höher erforderlich.

### Verwaltungszertifikate unter Windows (MakeCert)

Mithilfe von `makecert.exe` können Sie auf Ihrem Computer ein selbstsigniertes Verwaltungszertifikat generieren. Öffnen Sie eine **Visual Studio-Eingabeaufforderung** als **Administrator**, und geben Sie den folgenden Befehl ein. Ersetzen Sie dabei *AzureCertificate* durch den gewünschten Zertifikatnamen.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Der Befehl erstellt die `.cer`-Datei und installiert sie im **persönlichen** Zertifikatspeicher. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][Erstellen und Hochladen eines Verwaltungszertifikats für Azure].

Nachdem Sie das Zertifikat erstellt haben, müssen Sie die `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][Verwaltungsportal] zu Azure hochladen.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Speicherort des Zertifikats in Ihrem **persönlichen** Zertifikatspeicher an **ServiceManagementService** übergeben (ersetzen Sie auch hier *AzureCertificate* durch den Namen Ihres Zertifikats):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

### Verwaltungszertifikate unter Windows/Mac/Linux (OpenSSL)

Sie können Ihr Verwaltungszertifikat mithilfe von [OpenSSL][OpenSSL] erstellen. Tatsächlich müssen Sie zwei Zertifikate erstellen, eins für den Server (eine `.cer`-Datei) und eins für den Client (eine `.pem`-Datei). Zum Erstellen der `.pem`-Datei führen Sie den folgenden Befehl aus:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Zum Erstellen des `.cer`-Zertifikats führen Sie den folgenden Befehl aus:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Verwalten von Zertifikaten in Azure][Verwalten von Zertifikaten in Azure]. Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation auf [][]<http://www.openssl.org/docs/apps/openssl.html></a>.

Nachdem Sie diese Dateien erstellt haben, müssen Sie die `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][Verwaltungsportal] zu Azure hochladen und sich den Speicherort der `.pem`-Datei notieren.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung zum Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Pfad zur `.pem`-Datei an **ServiceManagementService** übergeben:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

## <a name="ListAvailableLocations"> </a>Gewusst wie: Auflisten verfügbarer Standorte

Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die Methode **list\_locations**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Wenn Sie einen Clouddienst, einen Speicherdienst oder eine Affinitätsgruppe erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **list\_locations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

-   Westeuropa
-   Südostasien
-   Ostasien
-   USA (Mitte/Norden)
-   Nordeuropa
-   USA (Mitte/Süden)
-   USA (West)
-   USA (Ost)

## <a name="CreateCloudService"> </a>Gewusst wie: Erstellen eines Clouddiensts

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-[Clouddienst][Clouddienst] bezeichnet (in früheren Azure-Versionen als *gehosteter Dienst*). Mit der Methode **create\_hosted\_service** können Sie einen neuen gehosteten Dienst erstellen, indem Sie einen Namen des gehosteten Diensts (der in Azure eindeutig sein muss), eine Bezeichnung (automatisch base64-codiert), eine Beschreibung und einen Standort angeben. Anstelle eines Standorts können Sie auch eine Affinitätsgruppe für den Dienst angeben.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(name, label, desc, location)

Mit der Methode **list\_hosted\_services** können Sie alle gehosteten Dienste für Ihr Abonnement auflisten:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Wenn Sie Informationen über einen bestimmten gehosteten Dienst abrufen möchten, übergeben Sie den Namen des gehosteten Diensts an die Methode **get\_hosted\_service\_properties**:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
    print('Location: ' + hosted_service.hosted_service_properties.location)
            

Nachdem Sie einen Clouddienst erstellt haben, können Sie Ihren Code mit der Methode **create\_deployment** für den Dienst bereitstellen.

## <a name="DeleteCloudService"> </a>Gewusst wie: Löschen eines Clouddiensts

Sie können einen Clouddienst löschen, indem Sie den Dienstnamen an die Methode **delete\_hosted\_service** übergeben:

    sms.delete_hosted_service('myhostedservice')

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. (Nähere Informationen finden Sie unter [Gewusst wie: Löschen einer Bereitstellung][Gewusst wie: Löschen einer Bereitstellung].)

## <a name="CreateDeployment"> </a>Gewusst wie: Erstellen einer Bereitstellung

Die Methode **create\_deployment** lädt ein neues [Dienstpaket][Dienstpaket] hoch und erstellt eine neue Bereitstellung in der Staging- oder Produktionsumgebung. Für diese Methode sind folgende Parameter verfügbar:

-   **name**: Der Name des gehosteten Diensts.
-   **deployment\_name**: Der Name der Bereitstellung.
-   **slot**: Eine Zeichenfolge, die den Slot `staging` oder `production` angibt.
-   **package\_url**: Die URL für das Bereitstellungspaket (eine .cspgk-Datei). Die Paketdatei muss in einem Azure-Blob-Speicherkonto unter demselben Abonnement gespeichert werden wie der gehostete Dienst, in den das Paket hochgeladen wird. Zum Erstellen eines Bereitstellungspakets können Sie die [Azure-PowerShell-Cmdlets][Azure-PowerShell-Cmdlets] oder das [CSPack-Befehlszeilentool][CSPack-Befehlszeilentool] verwenden.
-   **configuration**: Die base64-codierte Dienstkonfigurationsdatei (.cscfg-Datei).
-   **label**: Die Bezeichnung für den Namen des gehosteten Diensts (automatisch base64-codiert).

Im folgenden Beispiel wird eine neue Bereitstellung namens `v1` für einen gehosteten Dienst namens `myhostedservice` erstellt:

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    slot = 'production'
    package_url = 'URL_for_.cspkg_file'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
    label = 'myhostedservice'

    result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **create\_deployment** abgerufen werden kann, indem das von **create\_deployment** zurückgegebene Ergebnis an die Methode **get\_operation\_status** übergeben wird.

Mit den Methoden **get\_deployment\_by\_slot** und **get\_deployment\_by\_name** können Sie auf Bereitstellungseigenschaften zugreifen. Der folgende Beispielcode ruft eine Bereitstellung durch Angabe des Bereitstellungsslots ab. Außerdem iteriert das Beispiel durch alle Instanzen für die Bereitstellung:

    result = sms.get_deployment_by_slot('myhostedservice', 'production')

    print('Name: ' + result.name)
    print('Slot: ' + result.deployment_slot)
    print('Private ID: ' + result.private_id)
    print('Status: ' + result.status)
    print('Instances:')
    for instance in result.role_instance_list:
        print('Instance name: ' + instance.instance_name)
        print('Instance status: ' + instance.instance_status)
        print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>Gewusst wie: Aktualisieren einer Bereitstellung

Ein Bereitstellung kann mit der Methode **change\_deployment\_configuration** oder der Methode **update\_deployment\_status** aktualisiert werden.

Die Methode **change\_deployment\_configuration** ermöglicht das Hochladen einer neuen Dienstkonfigurationsdatei (`.cscfg`), wodurch mehrere Diensteinstellungen geändert werden können (einschließlich der Anzahl der Instanzen in einer Bereitstellung). Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)][Azure-Dienstkonfigurationsschema (.cscfg-Datei)]. Im folgenden Beispiel wird das Hochladen einer neuen Dienstkonfigurationsdatei veranschaulicht:

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

    result = sms.change_deployment_configuration(name, deployment_name, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **change\_deployment\_configuration** abgerufen werden kann, indem das von **change\_deployment\_configuration** zurückgegebene Ergebnis an die Methode **get\_operation\_status** übergeben wird.

Mit der Methode **update\_deployment\_status** können Sie den Status einer Bereitstellung auf RUNNING oder SUSPENDED festlegen. Im folgenden Beispiel wird der Status der Bereitstellung `v1` des gehosteten Diensts `myhostedservice` auf RUNNING festgelegt:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'

    result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Gewusst wie: Verschieben von Bereitstellungen zwischen Staging und Produktion

Azure bietet zwei Bereitstellungsumgebungen: Staging und Produktion. Normalerweise wird ein Dienst in der Stagingumgebung bereitgestellt, um ihn zu testen, bevor er in der Produktionsumgebung bereitgestellt wird. Wenn Sie den Dienst von der Staging- in die Produktionsumgebung hochstufen möchten, ist keine erneute Bereitstellung erforderlich. Sie können hierzu einfach die Bereitstellungen austauschen. (Weitere Informationen zum Austauschen von Bereitstellungen finden Sie unter [Verwalten von Bereitstellungen in Azure][Verwalten von Bereitstellungen in Azure].)

Das folgende Beispiel zeigt, wie Sie mithilfe der Methode **swap\_deployment** zwei Bereitstellungen (namens `v1` und `v2`) austauschen. Im Beispiel befindet sich Bereitstellung `v1` vor dem Aufruf von **swap\_deployment** im Produktionsslot und Bereitstellung `v2` im Stagingslot. Nach Aufruf von **swap\_deployment** befindet sich `v2` im Produktions- und `v1` im Stagingslot.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Gewusst wie: Löschen einer Bereitstellung

Verwenden Sie zum Löschen einer Bereitstellung die Methode **delete\_deployment**. Im folgenden Beispiel wird eine Bereitstellung namens `v1` gelöscht.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Gewusst wie: Erstellen eines Speicherdiensts

Ein [Speicherdienst][Speicherdienst] gewährt Ihnen Zugriff auf Azure-[Blobs][Blobs], -[Tabellen][Tabellen] und -[Warteschlangen][Warteschlangen]. Zum Erstellen eines Speicherdiensts müssen Sie Folgendes angeben: einen Namen für den Dienst (bestehend aus 3 bis 24 Kleinbuchstaben und innerhalb von Azure eindeutig), eine Beschreibung, eine Bezeichnung (maximal 100 Zeichen, automatisch base64-codiert) und entweder einen Standort oder eine Affinitätsgruppe. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt. Wenn Sie eine Affinitätsgruppe verwenden möchten, müssen Sie zunächst eine solche Gruppe erstellen (Informationen finden Sie unter [Gewusst wie: Erstellen einer Affinitätsgruppe][Gewusst wie: Erstellen einer Affinitätsgruppe]) und mit dem Parameter **affinity\_group** festlegen.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **create\_storage\_account** abgerufen werden kann, indem das von **create\_storage\_account** zurückgegebene Ergebnis an die Methode **get\_operation\_status** übergeben wird.

Mit der Methode **list\_storage\_accounts** können Sie Ihre Speicherkonten und deren Eigenschaften auflisten:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Affinity group: ' + account.storage_service_properties.affinity_group)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Gewusst wie: Löschen eines Speicherdiensts

Sie können einen Speicherdienst löschen, indem Sie den Speicherdienstnamen an die Methode **delete\_storage\_account** übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten ebenfalls gelöscht (Blobs, Tabellen und Warteschlangen).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Gewusst wie: Erstellen einer Affinitätsgruppe

Eine Affinitätsgruppe ist eine logische Gruppierung von Azure-Diensten, die Azure anweist, die Dienste für eine optimale Leistung an einem bestimmten Standort zu platzieren. Sie können z. B. eine Affinitätsgruppe am Standort "USA (Westen)" anlegen und dann einen [Clouddienst][Gewusst wie: Erstellen eines Clouddiensts] in dieser Affinitätsgruppe erstellen. Wenn Sie dann in derselben Affinitätsgruppe einen Speicherdienst erstellen, weiß Azure, dass der Dienst am Standort "USA (Westen)" platziert werden und im Datencenter optimiert werden soll, um die beste Leistung mit den Clouddiensten in derselben Affinitätsgruppe zu erzielen.

Zum Erstellen einer Affinitätsgruppe benötigen Sie einen Namen, eine Bezeichnung (automatisch base64-codiert) und einen Standort. Optional können Sie eine Beschreibung angeben:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myAffinityGroup'
    label = 'myAffinityGroup'
    location = 'West US'
    desc = 'my affinity group'

    sms.create_affinity_group(name, label, location, desc)

Nachdem Sie eine Affinitätsgruppe erstellt haben, können Sie beim [Erstellen eines Speicherdiensts][Gewusst wie: Erstellen eines Speicherdiensts] die Gruppe anstelle eines Standorts angeben.

Mit der Methode **list\_affinity\_groups** können Sie Affinitätsgruppen auflisten und deren Eigenschaften überprüfen:

    result = sms.list_affinity_groups()

    for group in result:
        print('Name: ' + group.name)
        print('Description: ' + group.description)
        print('Location: ' + group.location)
        print('')

## <a name="DeleteAffinityGroup"> </a>Gewusst wie: Löschen einer Affinitätsgruppe

Sie können eine Affinitätsgruppe löschen, indem Sie den Gruppennamen an die Methode **delete\_affinity\_group** übergeben. Bevor Sie eine Affinitätsgruppe löschen können, muss diese von allen Diensten getrennt werden (alternativ müssen Dienste, die die Affinitätsgruppe verwenden, gelöscht werden).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Gewusst wie: Auflisten verfügbarer Betriebssysteme

Mit der Methode **list\_operating\_systems** können Sie die für das Hosten von Diensten verfügbaren Betriebssysteme auflisten:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternativ können Sie die Methode **list\_operating\_system\_families** verwenden, die die Betriebssysteme nach Familie gruppiert:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Gewusst wie: Erstellen eines Betriebssystemimage

Wenn Sie dem Imagerepository ein Betriebssystemimage hinzufügen möchten, verwenden Sie die Methode **add\_os\_image**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Mit der Methode **list\_os\_images** können Sie die verfügbaren Betriebssystemimages auflisten. Die Liste umfasst alle Plattformimages und Benutzerimages:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Affinity group: ' + image.affinity_group)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Gewusst wie: Löschen eines Betriebssystemimage

Verwenden Sie zum Löschen eines Betriebssystemimages die Methode **delete\_os\_image**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Gewusst wie: Erstellen eines virtuellen Computers

Zum Erstellen eines virtuellen Computers müssen Sie zunächst einen [Clouddienst][Gewusst wie: Erstellen eines Clouddiensts] erstellen. Anschließend erstellen Sie die Bereitstellung des virtuellen Computers mit der Methode **create\_virtual\_machine\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-de-de-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Gewusst wie: Löschen eines virtuellen Computers

Zum Löschen eines virtuellen Computers löschen Sie zunächst die Bereitstellung mit der Methode **delete\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Anschließend kann der Clouddienst mit der Methode **delete\_hosted\_service** gelöscht werden:

    sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Dienstverwaltung vertraut sind, finden Sie unter den folgenden Links komplexere Aufgaben.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Cloud Services][Dienstpaket]
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Virtuelle Computer][Virtuelle Computer]

  [Azure-SDK für Python]: https://www.windowsazure.com/de-de/develop/python/common-tasks/install-python/
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [Was ist Dienstverwaltung?]: #WhatIs
  [Konzepte]: #Concepts
  [Gewusst wie: Herstellen einer Verbindung zur Dienstverwaltung]: #Connect
  [Gewusst wie: Auflisten verfügbarer Standorte]: #ListAvailableLocations
  [Gewusst wie: Erstellen eines Clouddiensts]: #CreateCloudService
  [Gewusst wie: Löschen eines Clouddiensts]: #DeleteCloudService
  [Gewusst wie: Erstellen einer Bereitstellung]: #CreateDeployment
  [Gewusst wie: Aktualisieren einer Bereitstellung]: #UpdateDeployment
  [Gewusst wie: Verschieben von Bereitstellungen zwischen Staging und Produktion]: #MoveDeployments
  [Gewusst wie: Löschen einer Bereitstellung]: #DeleteDeployment
  [Gewusst wie: Erstellen eines Speicherdiensts]: #CreateStorageService
  [Gewusst wie: Löschen eines Speicherdiensts]: #DeleteStorageService
  [Gewusst wie: Erstellen einer Affinitätsgruppe]: #CreateAffinityGroup
  [Gewusst wie: Löschen einer Affinitätsgruppe]: #DeleteAffinityGroup
  [Gewusst wie: Auflisten verfügbarer Betriebssysteme]: #ListOperatingSystems
  [Gewusst wie: Erstellen eines Betriebssystemimage]: #CreateVMImage
  [Gewusst wie: Löschen eines Betriebssystemimage]: #DeleteVMImage
  [Gewusst wie: Erstellen eines virtuellen Computers]: #CreateVM
  [Gewusst wie: Löschen eines virtuellen Computers]: #DeleteVM
  [Nächste Schritte]: #NextSteps
  [ein Azure-Konto erstellen]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Azure-Dienstverwaltungs-API]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx
  [Erstellen und Hochladen eines Verwaltungszertifikats für Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg551722.aspx
  [OpenSSL]: http://www.openssl.org/
  [Verwalten von Zertifikaten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg981929.aspx
  []: http://www.openssl.org/docs/apps/openssl.html
  [Clouddienst]: http://windowsazure.com/de-de/documentation/articles/cloud-services-what-is
  [Dienstpaket]: http://msdn.microsoft.com/de-de/library/windowsazure/jj155995.aspx
  [Azure-PowerShell-Cmdlets]: https://www.windowsazure.com/de-de/develop/php/how-to-guides/powershell-cmdlets/
  [CSPack-Befehlszeilentool]: http://msdn.microsoft.com/de-de/library/windowsazure/gg432988.aspx
  [Azure-Dienstkonfigurationsschema (.cscfg-Datei)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758710.aspx
  [Verwalten von Bereitstellungen in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433027.aspx
  [Speicherdienst]: https://www.windowsazure.com/de-de/manage/services/storage/what-is-a-storage-account/
  [Blobs]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/blob-service/
  [Tabellen]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/table-service/
  [Warteschlangen]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/queue-service/
  [Virtuelle Computer]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156003.aspx
