<properties 
	pageTitle="Verwenden der Dienstverwaltungs-API (Python) - Featureleitfaden" 
	description="Hier erfahren Sie, wie die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python funktioniert." 
	services="cloud-services" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>




# Verwenden der Dienstverwaltung aus Python

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python erläutert. Die **ServiceManagementService**-Klasse im [Azure SDK für Python][download-SDK-Python] unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Verwaltungsportal][management-portal] zur Verfügung stehen (z. B. **Erstellen, Aktualisieren und Löschen von Cloud-Diensten, Bereitstellungen, Datenverwaltungsdiensten, virtuellen Computern und Affinitätsgruppen**). Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen. 

## Inhaltsverzeichnis

* [Was ist Dienstverwaltung?][]
* [Konzepte][]
* [Vorgehensweise: Herstellen einer Verbindung zur Dienstverwaltung][]
* [Vorgehensweise: Auflisten verfügbarer Standorte][]
* [Vorgehensweise: Erstellen eines Cloud-Diensts][]
* [Vorgehensweise: Löschen eines Cloud-Diensts][]
* [Vorgehensweise: Erstellen einer Bereitstellung][]
* [Vorgehensweise: Aktualisieren einer Bereitstellung][]
* [Vorgehensweise: Verschieben von Bereitstellungen zwischen Staging und Produktion][]
* [Vorgehensweise: Löschen einer Bereitstellung][]
* [Vorgehensweise: Erstellen eines Speicherdiensts][]
* [Vorgehensweise: Löschen eines Speicherdiensts][]
* [Vorgehensweise: Erstellen einer Affinitätsgruppe][]
* [Vorgehensweise: Löschen einer Affinitätsgruppe][]
* [Vorgehensweise: Auflisten verfügbarer Betriebssysteme][]
* [Vorgehensweise: Erstellen eines Betriebssystemimage][]
* [Vorgehensweise: Löschen eines Betriebssystemimage][]
* [Vorgehensweise: Erstellen eines virtuellen Computers][]
* [Vorgehensweise: Löschen eines virtuellen Computers][]
* [Nächste Schritte][]

## <a name="WhatIs"> </a>Was ist Dienstverwaltung?
Die Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Verwaltungsportal][management-portal] verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure-SDK für Python können Sie Ihre Cloud-Dienste, Speicherkonten und Affinitätsgruppen verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen](http://azure.microsoft.com/pricing/free-trial/). 

## <a name="Concepts"> </a>Konzepte
Das Azure-SDK für Python umfasst die [Azure-Dienstverwaltungs-API][svc-mgmt-rest-api], eine REST-API. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509s v3-Zertifikaten gegenseitig authentifiziert. Der Zugriff auf die Dienstverwaltung kann über einen in Azure ausgeführten Dienst erfolgen oder direkt über das Internet, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

## <a name="Connect"> </a>Vorgehensweise: Herstellen einer Verbindung zur Dienstverwaltung
Um eine Verbindung zum Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und ein gültiges Verwaltungszertifikat. Ihre Abonnement-ID können Sie über das [Verwaltungsportal][management-portal] abrufen.

> [AZURE.NOTE] Seit dem Azure-SDK für Python Version 0.8.0 ist es jetzt möglich, bei Ausführung von Windows Zertifikate zu verwenden, die mit OpenSSL erstellt wurden.  Dafür ist Python 2.7.4 oder höher erforderlich.

### Verwaltungszertifikate unter Windows (MakeCert)

Mithilfe von  `makecert.exe` können Sie auf Ihrem Computer ein selbstsigniertes Verwaltungszertifikat generieren.  Öffnen Sie eine **Visual Studio-Eingabeaufforderung** als **Administrator**, und geben Sie den folgenden Befehl ein. Ersetzen Sie dabei  *AzureCertificate* durch den gewünschten Zertifikatnamen.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Der Befehl erstellt die  `.cer`-Datei und installiert sie im **persönlichen** Zertifikatspeicher. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx).

Nachdem Sie das Zertifikat erstellt haben, müssen Sie die  `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][management-portal] zu Azure hochladen.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die  `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Speicherort des Zertifikats in Ihrem **persönlichen** Zertifikatspeicher an **ServiceManagementService** übergeben (ersetzen Sie auch hier  *AzureCertificate* durch den Namen Ihres Zertifikats):

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist  `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten. 

### Verwaltungszertifikate unter Windows/Mac/Linux (OpenSSL)
Sie können Ihr Verwaltungszertifikat mithilfe von [OpenSSL](http://www.openssl.org/) erstellen.  Tatsächlich müssen Sie zwei Zertifikate erstellen, eins für den Server (eine  `.cer`-Datei) und eins für den Client (eine  `.pem`-Datei). Zum Erstellen der  `.pem`-Datei führen Sie den folgenden Befehl aus:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Zum Erstellen des  `.cer`-Zertifikats führen Sie den folgenden Befehl aus:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Verwalten von Zertifikaten in Azure](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx). Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation auf [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Nachdem Sie diese Dateien erstellt haben, müssen Sie die  `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][management-portal] zu Azure hochladen und sich den Speicherort der  `.pem`-Datei notieren.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die  `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung zum Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Pfad zur  `.pem`-Datei an **ServiceManagementService** übergeben:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist  `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten. 

## <a name="ListAvailableLocations"> </a>Vorgehensweise: Auflisten verfügbarer Standorte

Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die Methode **list_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Wenn Sie einen Cloud-Dienst, einen Speicherdienst oder eine Affinitätsgruppe erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **list_locations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

- Westeuropa 
- Südostasien 
- Ostasien 
- USA (Mitte/Norden) 
- Nordeuropa 
- USA (Mitte/Süden) 
- USA (West) 
- USA (Ost)

## <a name="CreateCloudService"> </a>Vorgehensweise: Erstellen eines Cloud-Diensts

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-[Cloud-Dienst] bezeichnet (in früheren Azure-Versionen als  *hosted service*). Mit der Methode **create_hosted_service** können Sie einen neuen gehosteten Dienst erstellen, indem Sie einen Namen des gehosteten Diensts (der in Azure eindeutig sein muss), eine Bezeichnung (automatisch base64-codiert), eine Beschreibung und einen Standort angeben. Anstelle eines Standorts können Sie auch eine Affinitätsgruppe für den Dienst angeben. 

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

Mit der Methode **list_hosted_services** können Sie alle gehosteten Dienste für Ihr Abonnement auflisten:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Wenn Sie Informationen über einen bestimmten gehosteten Dienst abrufen möchten, übergeben Sie den Namen des gehosteten Diensts an die Methode **get_hosted_service_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
Nachdem Sie einen Cloud-Dienst erstellt haben, können Sie Ihren Code mit der Methode **create_deployment** für den Dienst bereitstellen.

## <a name="DeleteCloudService"> </a>Vorgehensweise: Löschen eines Cloud-Diensts

Sie können einen Cloud-Dienst löschen, indem Sie den Dienstnamen an die Methode **delete_hosted_service** übergeben:

	sms.delete_hosted_service('myhostedservice')

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. (Nähere Informationen finden Sie unter [Vorgehensweise: Löschen einer Bereitstellung](#DeleteDeployment) .)

## <a name="CreateDeployment"> </a>Vorgehensweise: Erstellen einer Bereitstellung

Die Methode **create_deployment** lädt ein neues [Dienstpaket] hoch und erstellt eine neue Bereitstellung in der Staging- oder Produktionsumgebung. Für diese Methode sind folgende Parameter verfügbar:

* **name**: Der Name des gehosteten Diensts.
* **deployment_name**: Der Name der Bereitstellung.
* **slot**: Eine Zeichenfolge, die den Slot  `staging` oder  `production` angibt.
* **package_url**: Die URL für das Bereitstellungspaket (eine .cspgk-Datei). Die Paketdatei muss in einem Azure-Blob-Speicherkonto unter demselben Abonnement gespeichert werden wie der gehostete Dienst, in den das Paket hochgeladen wird. Zum Erstellen eines Bereitstellungspakets können Sie die [Azure-PowerShell-Cmdlets] oder das [cspack-Befehlszeilentool] verwenden.
* **configuration**: Die base64-codierte Dienstkonfigurationsdatei (.cscfg-Datei).
* **label**: Die Bezeichnung für den Namen des gehosteten Diensts (automatisch base64-codiert).

Das folgende Beispiel erstellt eine neue Bereitstellung `v1` for a hosted service called `myhostedservice`:

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

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **create_deployment** abgerufen werden kann, indem das von **create_deployment** zurückgegebene Ergebnis an die Methode **get_operation_status** übergeben wird.

Mit den Methoden **get_deployment_by_slot** und **get_deployment_by_name** können Sie auf Bereitstellungseigenschaften zugreifen. Der folgende Beispielcode ruft eine Bereitstellung durch Angabe des Bereitstellungsslots ab. Außerdem iteriert das Beispiel durch alle Instanzen für die Bereitstellung:

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

## <a name="UpdateDeployment"> </a>Vorgehensweise: Aktualisieren einer Bereitstellung

Eine Bereitstellung kann mit der Methode **change_deployment_configuration** oder der Methode **update_deployment_status** aktualisiert werden.

Die Methode **change_deployment_configuration** ermöglicht das Hochladen einer neuen Dienstkonfigurationsdatei (`CSCFG`), wodurch mehrere Diensteinstellungen geändert werden können (einschließlich der Anzahl der Instanzen in einer Bereitstellung). Weitere Informationen finden Sie unter [Azure-Dienstkonfigurationsschema (.cscfg-Datei)]. Im folgenden Beispiel wird das Hochladen einer neuen Dienstkonfigurationsdatei veranschaulicht:

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


Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **change_deployment_configuration** abgerufen werden kann, indem das von **create_deployment_configuration** zurückgegebene Ergebnis an die Methode **get_operation_status** übergeben wird.

Mit der Methode **update_deployment_status** können Sie den Status einer Bereitstellung auf RUNNING oder SUSPENDED festlegen. Im folgenden Beispiel wird veranschaulicht, wie der Status für eine Bereitstellung mit dem Namen  `v1` of a hosted service called `myhostedservice` auf RUNNING festgelegt wird:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Vorgehensweise: Verschieben von Bereitstellungen zwischen Staging und Produktion

Azure stellt zwei Bereitstellungsumgebungen bereit: Staging und Produktion. Normalerweise wird ein Dienst in der Stagingumgebung bereitgestellt, um ihn zu testen, bevor er in der Produktionsumgebung bereitgestellt wird. Wenn Sie den Dienst von der Staging- in die Produktionsumgebung hochstufen möchten, ist keine erneute Bereitstellung erforderlich. Sie können hierzu einfach die Bereitstellungen austauschen. (Weitere Informationen zum Austauschen von Bereitstellungen finden Sie unter [Verwalten von Bereitstellungen in Azure].)

Das folgende Beispiel zeigt, wie Sie mithilfe der Methode **swap_deployment** zwei Bereitstellungen (namens `v1` und `v2`) austauschen. Im Beispiel befindet sich Bereitstellung **v1** vor dem Aufruf von `swap_deployment` im Produktionsslot und Bereitstellung `v2` im Stagingslot. Nach Aufruf von **swap_deployment** befindet sich `v2` im Produktions- und `v1` im Stagingslot.  

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Vorgehensweise: Löschen einer Bereitstellung

Verwenden Sie zum Löschen einer Bereitstellung die Methode **delete_deployment**. Im folgenden Beispiel wird eine Bereitstellung namens `v1` gelöscht.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Vorgehensweise: Erstellen eines Speicherdiensts

Ein [Speicherdienst] gewährt Ihnen Zugriff auf Azure-[Blobs][azure-blobs], -[Tabellen][azure-tables] und -[Warteschlangen][azure-queues]. Zum Erstellen eines Speicherdiensts müssen Sie Folgendes angeben: einen Namen für den Dienst (bestehend aus 3 bis 24 Kleinbuchstaben und innerhalb von Azure eindeutig), eine Beschreibung, eine Bezeichnung (maximal 100 Zeichen, automatisch base64-codiert) und entweder einen Standort oder eine Affinitätsgruppe. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt. Wenn Sie eine Affinitätsgruppe verwenden möchten, müssen Sie zunächst eine solche Gruppe erstellen (Informationen finden Sie unter [Gewusst wie: Erstellen einer Affinitätsgruppe](#CreateAffinityGroup)) und mit dem Parameter **affinity_group** festlegen.

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

Beachten Sie, dass im vorstehenden Beispiel der Status des Vorgangs **create_storage_account** abgerufen werden kann, indem das von **create_storage_account** zurückgegebene Ergebnis an die Methode **get_operation_status** übergeben wird.  

Mit der Methode **list_storage_accounts** können Sie Ihre Speicherkonten und deren Eigenschaften auflisten:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Vorgehensweise: Löschen eines Speicherdiensts

Sie können einen Speicherdienst löschen, indem Sie den Speicherdienstnamen an die Methode **delete_storage_account** übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten ebenfalls gelöscht (Blobs, Tabellen und Warteschlangen).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Vorgehensweise: Erstellen einer Affinitätsgruppe

Eine Affinitätsgruppe ist eine logische Gruppierung von Azure-Diensten, die Azure anweist, die Dienste für eine optimale Leistung an einem bestimmten Standort zu platzieren. Sie können z. B. eine Affinitätsgruppe am Standort "USA (West)" anlegen und dann einen [Cloud-Dienst](#CreateCloudService) in dieser Affinitätsgruppe erstellen. Wenn Sie dann in derselben Affinitätsgruppe einen Speicherdienst erstellen, weiß Azure, dass der Dienst am Standort "USA (Westen)" platziert werden und im Datencenter optimiert werden soll, um die beste Leistung mit den Cloud-Diensten in derselben Affinitätsgruppe zu erzielen.

Zum Erstellen einer Affinitätsgruppe benötigen Sie einen Namen, eine Bezeichnung (automatisch base64-codiert) und einen Standort. Optional können Sie eine Beschreibung angeben:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

Nachdem Sie eine Affinitätsgruppe erstellt haben, können Sie beim [Erstellen eines Speicherdiensts](#CreateStorageService).

Mit der Methode **list_affinity_groups** können Sie Affinitätsgruppen auflisten und deren Eigenschaften überprüfen:

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>Vorgehensweise: Löschen einer Affinitätsgruppe
	
Sie können eine Affinitätsgruppe löschen, indem Sie den Gruppennamen an die Methode **delete_affinity_group** übergeben. Bevor Sie eine Affinitätsgruppe löschen können, muss diese von allen Diensten getrennt werden (alternativ müssen Dienste, die die Affinitätsgruppe verwenden, gelöscht werden).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Vorgehensweise: Auflisten verfügbarer Betriebssysteme

Mit der Methode **list_operating_systems** können Sie die für das Hosten von Diensten verfügbaren Betriebssysteme auflisten:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

Alternativ können Sie die Methode **list_operating_system_families** verwenden, die die Betriebssysteme nach Familie gruppiert:

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Vorgehensweise: Erstellen eines Betriebssystemimage

Wenn Sie dem Imagerepository ein Betriebssystemimage hinzufügen möchten, verwenden Sie die Methode **add_os_image**:

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

Mit der Methode **list_os_images** können Sie die verfügbaren Betriebssystemimages auflisten. Die Liste umfasst alle Plattformimages und Benutzerimages:

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

## <a name="DeleteVMImage"> </a>Vorgehensweise: Löschen eines Betriebssystemimage

Verwenden Sie zum Löschen eines Betriebssystemimages die Methode **delete_os_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Vorgehensweise: Erstellen eines virtuellen Computers

Zum Erstellen eines virtuellen Computers müssen Sie zunächst einen [Cloud-Dienst] erstellen.(#CreateCloudService).  Anschließend erstellen Sie die Bereitstellung des virtuellen Computers mit der Methode **create_virtual_machine_deployment**:

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

## <a name="DeleteVM"> </a>Vorgehensweise: Löschen eines virtuellen Computers

Zum Löschen eines virtuellen Computers löschen Sie zunächst die Bereitstellung mit der Methode **delete_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

Anschließend kann der Cloud-Dienst mit der Methode **delete_hosted_service** gelöscht werden:

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Dienstverwaltung vertraut sind, finden Sie unter den folgenden Links komplexere Aufgaben.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Cloud-Dienste][]
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Virtuelle Computer][]

[Was ist Dienstverwaltung?]: #WhatIs
[Konzepte]: #Concepts
[Vorgehensweise: Herstellen einer Verbindung zur Dienstverwaltung]: #Connect
[Vorgehensweise: Auflisten verfügbarer Standorte]: #ListAvailableLocations
[Vorgehensweise: Erstellen eines Cloud-Diensts]: #CreateCloudService
[Vorgehensweise: Löschen eines Cloud-Diensts]: #DeleteCloudService
[Vorgehensweise: Erstellen einer Bereitstellung]: #CreateDeployment
[Vorgehensweise: Aktualisieren einer Bereitstellung]: #UpdateDeployment
[Vorgehensweise: Verschieben von Bereitstellungen zwischen Staging und Produktion]: #MoveDeployments
[Vorgehensweise: Löschen einer Bereitstellung]: #DeleteDeployment
[Vorgehensweise: Erstellen eines Speicherdiensts]: #CreateStorageService
[Vorgehensweise: Löschen eines Speicherdiensts]: #DeleteStorageService
[Vorgehensweise: Erstellen einer Affinitätsgruppe]: #CreateAffinityGroup
[Vorgehensweise: Löschen einer Affinitätsgruppe]: #DeleteAffinityGroup
[Vorgehensweise: Auflisten verfügbarer Betriebssysteme]: #ListOperatingSystems
[Vorgehensweise: Erstellen eines Betriebssystemimage]: #CreateVMImage
[Vorgehensweise: Löschen eines Betriebssystemimage]: #DeleteVMImage
[Vorgehensweise: Erstellen eines virtuellen Computers]: #CreateVM
[Vorgehensweise: Löschen eines virtuellen Computers]: #DeleteVM
[Nächste Schritte]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/de-de/develop/python/common-tasks/install-python/
[Cloud-Dienst]:http://windowsazure.com/de-de/documentation/articles/cloud-services-what-is
[Dienstpaket]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Cloud-Dienste]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure-PowerShell-Cmdlets]: https://www.windowsazure.com/de-de/develop/php/how-to-guides/powershell-cmdlets/
[Cspack-Befehlszeilentool]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Verwalten von Bereitstellungen in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[Speicherdienst]: https://www.windowsazure.com/de-de/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/de-de/develop/python/how-to-guides/queue-service/
[Azure-Dienstkonfigurationsschema (.cscfg-Datei)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Cloud Services]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Virtuelle Computer]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=45--> 
