<properties
	pageTitle="Verwenden der Dienstverwaltungs-API (Python) – Featureleitfaden"
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
	ms.date="11/03/2015"
	ms.author="huvalo"/>

# Verwenden der Dienstverwaltung aus Python

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Dienstverwaltungsaufgaben aus Python erläutert. Die **ServiceManagementService**-Klasse im [Azure-SDK für Python](../python-how-to-install.md) unterstützt den programmgesteuerten Zugriff auf viele der Dienstverwaltungsfunktionen, die im [Verwaltungsportal][management-portal] zur Verfügung stehen (z. B. **Erstellen, Aktualisieren und Löschen von Clouddiensten, Bereitstellungen, Datenverwaltungsdiensten und virtuellen Computern**). Diese Funktionalität kann bei der Erstellung von Anwendungen hilfreich sein, die programmgesteuert auf Dienstverwaltungsfunktionen zugreifen müssen.

> [AZURE.NOTE]Die Dienstverwaltungs-API wird durch die neue Ressourcenverwaltungs-API ersetzt, die derzeit als Vorschauversion verfügbar ist. Weitere Informationen zur Verwendung der neuen Ressourcenverwaltungs-API von Python finden Sie in der [Dokumentation zur Azure-Ressourcenverwaltung](http://azure-sdk-for-python.readthedocs.org/).


## <a name="WhatIs"> </a>Was ist Dienstverwaltung?
Die Dienstverwaltungs-API bietet programmgesteuerten Zugriff auf viele der im [Verwaltungsportal][management-portal] verfügbaren Dienstverwaltungsfunktionen. Mithilfe des Azure-SDK für Python können Sie Ihre Clouddienste und Speicherkonten verwalten.

Um die Dienstverwaltungs-API verwenden zu können, müssen Sie [ein Azure-Konto erstellen](http://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Konzepte
Das Azure-SDK für Python umfasst die [Azure-Dienstverwaltungs-API][svc-mgmt-rest-api], eine REST-API. Alle API-Vorgänge werden über SSL ausgeführt und mithilfe von X.509s v3-Zertifikaten gegenseitig authentifiziert. Der Zugriff auf die Dienstverwaltung kann über einen in Azure ausgeführten Dienst erfolgen oder direkt über das Internet, und zwar von jeder Anwendung aus, die HTTPS-Anforderungen senden und HTTPS-Antworten empfangen kann.

## <a name="Connect"> </a>Herstellen einer Verbindung mit der Dienstverwaltung
Um eine Verbindung zum Dienstverwaltungs-Endpunkt herzustellen, benötigen Sie Ihre Azure-Abonnement-ID und ein gültiges Verwaltungszertifikat. Ihre Abonnement-ID können Sie über das [Verwaltungsportal][management-portal] abrufen.

> [AZURE.NOTE]Seit dem Azure-SDK für Python Version 0.8.0 ist es jetzt möglich, bei Ausführung von Windows Zertifikate zu verwenden, die mit OpenSSL erstellt wurden. Dafür ist Python 2.7.4 oder höher erforderlich. Es wird empfohlen, dass Benutzer OpenSSL anstelle von PFX verwenden, da die Unterstützung für die PFX-Zertifikate wahrscheinlich in der Zukunft entfernt werden wird.

### Verwaltungszertifikate unter Windows/Mac/Linux (OpenSSL)
Sie können Ihr Verwaltungszertifikat mithilfe von [OpenSSL](http://www.openssl.org/) erstellen. Tatsächlich müssen Sie zwei Zertifikate erstellen, eins für den Server (eine `.cer`-Datei) und eins für den Client (eine `.pem`-Datei). Führen Sie zum Erstellen der `.pem`-Datei Folgendes aus:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Führen Sie zum Erstellen des `.cer`-Zertifikats Folgendes aus:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Übersicht über Zertifikate für Azure Cloud Services](./cloud-services-certs-create.md). Eine vollständige Beschreibung von OpenSSL-Parametern finden Sie in der Dokumentation auf [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Nachdem Sie diese Dateien erstellt haben, müssen Sie die `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][management-portal] zu Azure hochladen und sich den Speicherort der `.pem`-Datei notieren.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung zum Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Pfad zur `.pem`-Datei an **ServiceManagementService** übergeben:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

### Verwaltungszertifikate unter Windows (MakeCert)

Mithilfe von `makecert.exe` können Sie auf Ihrem Computer ein selbstsigniertes Verwaltungszertifikat generieren. Öffnen Sie eine **Visual Studio-Eingabeaufforderung** als **Administrator**, und geben Sie den folgenden Befehl ein. Ersetzen Sie dabei *AzureCertificate* durch den gewünschten Zertifikatnamen.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Mit dem Befehl wird die `.cer`-Datei erstellt und im **persönlichen** Zertifikatspeicher installiert. Weitere Informationen finden Sie unter [Übersicht über Zertifikate für Azure Cloud Services](./cloud-services-certs-create.md).

Nachdem Sie das Zertifikat erstellt haben, müssen Sie die `.cer`-Datei mit der Aktion "Upload" auf der Registerkarte "Einstellungen" des [Verwaltungsportals][management-portal] zu Azure hochladen.

Nachdem Sie Ihre Abonnement-ID abgerufen, ein Zertifikat erstellt und die `.cer`-Datei zu Azure hochgeladen haben, können Sie eine Verbindung mit dem Azure-Verwaltungsendpunkt herstellen, indem Sie die Abonnement-ID und den Speicherort des Zertifikats in Ihrem **persönlichen** Zertifikatspeicher an **ServiceManagementService** übergeben (ersetzen Sie auch hier *AzureCertificate* durch den Namen Ihres Zertifikats):

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

Im vorstehenden Beispiel ist `sms` ein **ServiceManagementService**-Objekt. Die **ServiceManagementService**-Klasse ist die primäre Klasse für die Verwaltung von Azure-Diensten.

## <a name="ListAvailableLocations"> </a>Auflisten verfügbarer Standorte

Um die für das Hosten von Diensten verfügbaren Standorte aufzulisten, verwenden Sie die Methode **list\_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Wenn Sie einen Clouddienst oder einen Speicherdienst erstellen, müssen Sie einen gültigen Standort angeben. Die Methode **list\_locations** gibt stets eine aktuelle Liste der derzeit verfügbaren Standorte zurück. Zum Zeitpunkt der Erstellung dieses Dokuments waren folgende Standorte verfügbar:

- Westeuropa
- Nordeuropa
- Südostasien
- Ostasien
- USA (Mitte)
- USA Nord Mitte
- USA (Mitte/Süden)
- USA (Westen)
- USA (Osten)
- Japan (Osten)
- Japan (Westen)
- Brasilien (Süden)
- Australien (Osten)
- Australien (Südosten)

## <a name="CreateCloudService"> </a>Erstellen eines Clouddiensts

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-[Clouddienst] bezeichnet (in früheren Azure-Versionen als *gehosteter Dienst*). Mit der Methode **create\_hosted\_service** können Sie einen neuen gehosteten Dienst erstellen, indem Sie einen Namen des gehosteten Diensts (der in Azure eindeutig sein muss), eine Bezeichnung (automatisch base64-codiert), eine Beschreibung und einen Standort angeben.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

Mit der Methode **list\_hosted\_services** können Sie alle gehosteten Dienste für Ihr Abonnement auflisten:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Wenn Sie Informationen über einen bestimmten gehosteten Dienst abrufen möchten, übergeben Sie den Namen des gehosteten Diensts an die Methode **get\_hosted\_service\_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

Nachdem Sie einen Clouddienst erstellt haben, können Sie Ihren Code mit der Methode **create\_deployment** für den Dienst bereitstellen.

## <a name="DeleteCloudService"> </a>Löschen eines Clouddiensts

Sie können einen Clouddienst löschen, indem Sie den Dienstnamen an die Methode **delete\_hosted\_service** übergeben:

	sms.delete_hosted_service('myhostedservice')

Bevor Sie einen Dienst löschen können, müssen zunächst alle Bereitstellungen für den Dienst gelöscht werden. (Weitere Informationen finden Sie unter [Löschen einer Bereitstellung](#DeleteDeployment).)

## <a name="DeleteDeployment"> </a>Löschen einer Bereitstellung

Verwenden Sie zum Löschen einer Bereitstellung die Methode **delete\_deployment**. Im folgenden Beispiel wird eine Bereitstellung namens `v1` gelöscht.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Erstellen eines Speicherdiensts

Ein [Speicherdienst](../storage/storage-create-storage-account.md) gewährt Ihnen Zugriff auf Azure-[Blobs](../storage/storage-python-how-to-use-blob-storage.md), -[Tabellen](../storage/storage-python-how-to-use-table-storage.md) und -[Warteschlangen](../storage/storage-python-how-to-use-queue-storage.md). Zum Erstellen eines Speicherdiensts müssen Sie Folgendes angeben: einen Namen für den Dienst (bestehend aus 3 bis 24 Kleinbuchstaben und innerhalb von Azure eindeutig), eine Beschreibung, eine Bezeichnung (maximal 100 Zeichen, automatisch base64-codiert) und einen Standort. Im folgenden Beispiel wird ein Speicherdienst durch Angabe eines Standorts erstellt.

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
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Löschen eines Speicherdiensts

Sie können einen Speicherdienst löschen, indem Sie den Speicherdienstnamen an die Methode **delete\_storage\_account** übergeben. Beim Löschen eines Speicherdiensts werden alle im Dienst gespeicherten Daten ebenfalls gelöscht (Blobs, Tabellen und Warteschlangen).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Auflisten verfügbarer Betriebssysteme

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

## <a name="CreateVMImage"> </a>Erstellen eines Betriebssystemimage

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
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>Löschen eines Betriebssystemimage

Verwenden Sie zum Löschen eines Betriebssystemimages die Methode **delete\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Erstellen eines virtuellen Computers

Zum Erstellen eines virtuellen Computers müssen Sie zunächst einen [Clouddienst](#CreateCloudService) erstellen. Anschließend erstellen Sie die Bereitstellung des virtuellen Computers mit der Methode **create\_virtual\_machine\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-de-DE-30GB.vhd'

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

## <a name="DeleteVM"> </a>Löschen eines virtuellen Computers

Zum Löschen eines virtuellen Computers löschen Sie zunächst die Bereitstellung mit der Methode **delete\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

Anschließend kann der Clouddienst mit der Methode **delete\_hosted\_service** gelöscht werden:

	sms.delete_hosted_service(service_name='myvm')

##Erstellen eines virtuellen Computers aus einem erfassten Image eines virtuellen Computers

Um ein VM-Image zu erfassen, rufen Sie zuerst die **capture\_vm\_image**-Methode auf:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

Um sicherzustellen, dass Sie das Image erfolgreich erfasst haben, verwenden Sie dann die **list\_vm\_images**-API und stellen sicher, dass das Image in den Ergebnissen angezeigt wird:

	images = sms.list_vm_images()

Um schließlich den virtuellen Computer mithilfe des erfassten Image zu erstellen, verwenden die **create\_virtual\_machine\_deployment**-Methode wie zuvor, übergeben Sie diesmal jedoch stattdessen "vm\_image\_name".

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Weitere Informationen zum Erfassen eines virtuellen Linux-Computers finden Sie unter [Erfassen eines virtuellen Linux-Computers](../virtual-machines/virtual-machines-linux-capture-image.md).

Weitere Informationen zum Erfassen eines virtuellen Windows-Computers finden Sie unter [Erfassen eines virtuellen Windows-Computers](../virtual-machines/virtual-machines-capture-image-windows-server.md).

## <a name="What's Next"> </a>Nächste Schritte

Da Sie nun mit den Grundlagen der Dienstverwaltung vertraut sind, können Sie auf die [vollständige API-Referenzdokumentation für das Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) zugreifen und komplexe Aufgaben mühelos durchführen, um Ihre Python-Anwendung zu verwalten.

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[Clouddienst]: https://azure.microsoft.com/de-DE/documentation/services/cloud-services/

<!---HONumber=Nov15_HO3-->