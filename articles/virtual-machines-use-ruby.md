<properties urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="Was ist Azure Multi-Factor Authentication?" metaKeywords="" description="Erfahren Sie mehr &uuml;ber die Azure Multi-Factor Authentication. Hierbei handelt es sich um eine Authentifizierungsmethode, welche die Verwendung von mehr als einer Verifizierungsmethode erfordert und eine wichtige zweite Sicherheitsebene f&uuml;r Benutzeranmeldungen und Transaktionen darstellt." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="Verwalten von virtuellen Azure-Computern mit Ruby" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Verwalten von virtuellen Azure-Computern mit Ruby

In diesem Leitfaden wird die programmgesteuerte Durchführung gängiger Verwaltungsaufgaben für virtuelle Azure-Computer erläutert, z. B. das Erstellen und Konfigurieren von virtuellen Computern sowie das Hinzufügen von Datenlaufwerken. Das Azure SDK for Ruby bietet Zugriff auf Dienstverwaltungsfunktionen für eine Vielzahl unterschiedlicher Azure-Dienste wie virtuelle Azure-Computer.

## Inhaltsverzeichnis

-   [Was ist Dienstverwaltung?][Was ist Dienstverwaltung?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Verwaltungszertifikats][Erstellen eines Verwaltungszertifikats]
-   [Erstellen einer Ruby-Anwendung][Erstellen einer Ruby-Anwendung]
-   [Konfigurieren der Anwendung für das SDK][Konfigurieren der Anwendung für das SDK]
-   [Einrichten einer Azure-Verwaltungsverbindung][Einrichten einer Azure-Verwaltungsverbindung]
-   [Gewusst wie: Arbeiten mit virtuellen Computern][Gewusst wie: Arbeiten mit virtuellen Computern]
-   [Gewusst wie: Arbeiten mit Images und Datenträgern][Gewusst wie: Arbeiten mit Images und Datenträgern]
-   [Gewusst wie: Arbeiten mit Cloud-Diensten][Gewusst wie: Arbeiten mit Cloud-Diensten]
-   [Gewusst wie: Arbeiten mit Speicherdiensten][Gewusst wie: Arbeiten mit Speicherdiensten]
-   [Nächste Schritte][Nächste Schritte]

## <a name="what-is"> </a>Was ist Dienstverwaltung?

Azure bietet [REST-APIs für Dienstverwaltungsvorgänge][REST-APIs für Dienstverwaltungsvorgänge], darunter die Verwaltung von virtuellen Azure-Computern. Das Azure SDK for Ruby zeigt Verwaltungsvorgänge für virtuelle Computer mittels der Klasse **Azure::VirtualMachineService** an. Der Großteil der Verwaltungsfunktionen eines virtuellen Computers, die im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] zur Verfügung stehen, kann über diese Klasse aufgerufen werden.

Auch wenn die Dienstverwaltungs-API dazu verwendet werden kann, eine Vielzahl an auf Azure gehosteten Diensten zu verwalten, enthält dieses Dokument nur Informationen über die Verwaltung von virtuellen Azure-Computern.

## <a name="concepts"> </a>Konzepte

Virtuelle Azure-Computer werden als "Rollen" in einem Cloud-Dienst implementiert. Jeder Cloud-Dienst kann eine oder mehrere Rollen enthalten, die logisch in Bereitstellungen gruppiert werden. Die Rolle definiert die physischen Gesamtmerkmale des virtuellen Computers, z. B. wie viel Speicher zur Verfügung steht oder wie viele CPU-Cores es gibt usw.

Jeder virtuelle Computer verfügt auch über einen Betriebssystemdatenträger, der das bootfähige Betriebssystem umfasst. Ein virtueller Computer kann mehrere Datenlaufwerke haben, d. h. zusätzliche Datenträger, die zum Speichern von Anwendungsdaten verwendet werden sollen. Die Datenträger werden als virtuelle Festplatten (VHD) im Azure-Blob-Speicher gespeichert. VHDs können auch als "Images" angezeigt werden, wobei es sich um Vorlagen handelt, die zum Erstellen von Datenträgern für einen virtuellen Computer bei der VM-Erstellung verwendet werden. Wenn Sie beispielsweise einen neuen virtuellen Computer mit einem Ubuntu-Image erstellen, wird aus dem Ubuntu-Image ein neuer Betriebssystemdatenträger erstellt.

Die meisten Images werden von Microsoft oder Partnern bereitgestellt; Sie können jedoch auch Ihre eigenen Images erstellen oder ein Image aus einem in Azure gehosteten virtuellen Computer erstellen.

## <a name="setup-certificate"> </a>Erstellen eines Azure-Verwaltungszertifikats

Wenn Sie Dienstverwaltungsvorgänge ausführen, z. B. solche, die durch die Klasse **Azure::VirtualMachineService** angezeigt werden, müssen Sie Ihre Azure-Abonnement-ID und eine Datei mit einem Verwaltungszertifikat für Ihr Abonnement angeben. Beides wird vom SDK für die Authentifizierung an der Azure-REST-API verwendet.

Verwenden Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli), um die Abonnement-ID und ein Verwaltungszertifikat abzurufen. Weitere Informationen zum Installieren und Konfigurieren der xplat-cli finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle].

Nachdem die xplat-cli konfiguriert wurde, können Sie die folgenden Schritte ausführen, um Ihre Azure-Abonnement-ID abzurufen und ein Verwaltungszertifikat zu exportieren:

1.  Verwenden Sie folgenden Befehl, um die Abonnement-ID abzurufen:

        azure account list

2.  Verwenden Sie den folgenden Befehl, um das Verwaltungszertifikat zu exportieren:

        azure account cert export

    Nach Ausführen des Befehls wird das Zertifikat in eine Datei namens "\<azure-abonnement-name\>.pem" exportiert. Wenn Ihr Abonnement beispielsweise **meinfantastischesabonnement** heißt, erhält die erstellte Datei den Namen **meinfantastischesabonnement.pem**.

Schreiben Sie die Abonnement-ID und den Speicherort der PEM-Datei mit dem exportierten Zertifikat auf, da diese zu einem späteren Zeitpunkt in diesem Dokument benötigt werden.

## <a name="create-app"></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine neue Ruby-Anwendung. Die in diesem Dokument verwendeten Beispiele können in eine einzelne **.rb**-Datei implementiert werden.

## <a name="configure-access"></a>Konfigurieren der Anwendung

Zum Verwalten von Azure-Diensten müssen Sie das Azure-Gem herunterladen und verwenden, das das Azure SDK for Ruby enthält.

### Verwenden des Gem-Befehls zum Installieren des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Verwenden Sie den folgenden Befehl, um das Azure-Gem zu installieren:

        gem install azure

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        Fetching: mini_portile-0.5.1.gem (100%)
        Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
        Fetching: mime-types-1.25.gem (100%)
        Fetching: systemu-2.5.2.gem (100%)
        Fetching: macaddr-1.6.1.gem (100%)
        Fetching: uuid-2.3.7.gem (100%)
        Fetching: azure-0.5.0.gem (100%)
        Successfully installed mini_portile-0.5.1
        Successfully installed nokogiri-1.6.0-x86-mingw32
        Successfully installed mime-types-1.25
        Successfully installed systemu-2.5.2
        Successfully installed macaddr-1.6.1
        Successfully installed uuid-2.3.7
        Successfully installed azure-0.5.0
        7 gems installed

    <div class="dev-callout">

    **Hinweis**
    Wenn ein Fehler bezüglich fehlender Berechtigungen angezeigt wird, verwenden Sie stattdessen `sudo gem install azure`.

    </div>

### Anfordern des Gem

Fügen Sie mit einem Texteditor Folgendes am Anfang Ihrer Ruby-Anwendungsdatei ein. Dadurch wird das Azure-Gem geladen und das Azure SDK for Ruby wird für Ihre Anwendung zur Verfügung gestellt:

    require 'azure'

## <a name="setup-connection"> </a>Gewusst wie: Herstellen einer Verbindung zur Dienstverwaltung

Für eine erfolgreiche Durchführung von Dienstverwaltungsvorgängen mit Azure müssen Sie die Abonnement-ID und das Zertifikat angeben, die Sie im Abschnitt [Erstellen eines Azure-Verwaltungszertifikats][Erstellen eines Verwaltungszertifikats] abgerufen haben. Der einfachste Weg besteht darin, die ID und den Pfad zur Zertifikatsdatei mit den folgenden Umgebungsvariablen anzugeben:

-   AZURE\_MANAGEMENT\_CERTIFICATE - Der Pfad zur PEM-Datei mit dem Verwaltungszertifikat.

-   AZURE\_SUBSCRIPTION\_ID - Die Abonnement-ID für Ihr Azure-Abonnement.

Sie können diese Werte auch mit dem folgenden Befehl programmgesteuert in Ihrer Anwendung festlegen:

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

## <a name="virtual-machine"> </a>Gewusst wie: Arbeiten mit virtuellen Computern

Verwaltungsvorgänge für Azure Virtual Machines werden mit der Klasse **Azure::VirtualMachineService** durchgeführt.

### Gewusst wie: Neuen virtuellen Computer erstellen

Verwenden Sie die **create\_virtual\_machine**-Methode, um einen neuen virtuellen Computer zu erstellen. Diese Methode lässt ein Hash zu, das die folgenden Parameter enthält, und gibt eine **Azure::VirtualMachineManagement::VirtualMachine**-Instanz zurück, die den erstellten virtuellen Computer beschreibt:

**Parameter**

-   **:vm\_name** - Der Name des virtuellen Computers

-   **:vm\_user** - Der Root- oder Admin-Benutzername

-   **:password** - Das Kennwort für den Root- oder Admin-Benutzer

-   **:image** - Das Betriebssystem-Image, das zum Erstellen des Betriebssystemdatenträgers für diesen virtuellen Computer verwendet wird. Der Betriebssystemdatenträger wird in einer VHD gespeichert, die in einem Blob-Speicher erstellt wurde.

-   **:location** - Die Region, in der der virtuelle Computer erstellt wird. Dabei sollte es sich um dieselbe Region handeln wie die des Speicherkontos, das die von diesem virtuellen Computer verwendeten Datenträger enthält.

Es folgt ein Beispiel für die Erstellung eines neuen virtuellen Computers mithilfe dieser Parameter:

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-de-de-30GB',
      :location = 'East US'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params)

    puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
    puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
    puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**Optionen**

Per Hash-Methode können Sie optionale Parameter angeben, mit denen Sie das Standardverhalten beim Erstellen eines virtuellen Computers außer Kraft setzen können, z. B. die Angabe eines bestehenden Speicherkontos, anstatt ein neues zu erstellen.

Im Folgenden finden Sie die für die **create\_virtual\_machine**-Methode verfügbaren Optionen:

-   **:storage\_account\_name** - Der Name des Speicherkontos, das zum Speichern der Datenträger-Images verwendet werden soll. Wenn das Speicherkonto noch nicht vorhanden ist, wird ein neues erstellt. Wenn dies nicht angegeben wird, wird ein neues Speicherkonto mit einem Namen basierend auf dem Parameter ":vm\_name" erstellt.

-   **:cloud\_service\_name** - Der Name des Cloud-Dienstes, der zum Hosten des virtuellen Computers verwendet werden soll. Wenn der Cloud-Dienst noch nicht vorhanden ist, wird ein neuer erstellt. Wenn dies nicht angegeben wird, wird ein neues Cloud-Dienstkonto mit einem Namen basierend auf dem Parameter ":vm\_name" erstellt.

-   **:deployment\_name** - Der Name der Bereitstellung, die zum Bereitstellen der Konfiguration des virtuellen Computers verwendet werden soll.

-   **:tcp\_endpoints** - Die TCP-Ports, die für diesen virtuellen Computer öffentlich angezeigt werden sollen. Der SSH-Endpunkt (bei Linux-basierten virtuellen Computern) und der WinRM-Endpunkt (bei Windows-basierten virtuellen Computern) müssen nicht angegeben werden; sie werden automatisch erstellt. Sie können mehrere Ports angeben, die durch ein Komma voneinander getrennt sind. Verwenden Sie das Format **öffentlicher Port:interner Port**, um einen internen Port einem öffentlichen Port mit einer anderen Portnummer zuzuweisen. Mit 80:8080 wird beispielsweise der interne Port 8080 als öffentlicher Port 80 angezeigt.

-   **:service\_location** - Der Zielspeicherort für das Zertifikat auf dem virtuellen Computer. Dies gilt nur für Windows-basierte virtuelle Computer.

-   **:ssh\_private\_key\_file** - Die Datei mit dem privaten Schlüssel, der zum Sichern des SSH-Zugriffs auf den Linux-basierten virtuellen Computer verwendet wird. Dieser Parameter wird auch dazu verwendet, das Zertifikat anzugeben, das zum Sichern von WinRM genutzt wird, wenn eine HTTPS-Übertragung gewählt wurde. Wenn **:ssh\_private\_key\_file** und **:ssh\_certificate\_file** nicht angegeben werden, nutzt SSH nur die Kennwortauthentifizierung.

-   **:ssh\_certificate\_file** - Die Datei mit der Zertifikatsdatei, die zum Sichern des SSH-Zugriffs auf den Linux-basierten virtuellen Computer verwendet wird. Dieser Parameter wird auch dazu verwendet, das Zertifikat anzugeben, das zum Sichern von WinRM genutzt wird, wenn eine HTTPS-Übertragung gewählt wurde. Wenn **:ssh\_private\_key\_file** und **:ssh\_certificate\_file** nicht angegeben werden, nutzt SSH nur die Kennwortauthentifizierung.

-   **:ssh\_port** - Der öffentliche Port, der für die SSH-Kommunikation verwendet wird. Wenn dieser Parameter nicht angegeben wird, wird standardmäßig der SSH-Port 22 verwendet.

-   **:vm\_size** - Die Größe des virtuellen Computers. Dadurch werden die Speichergröße, die Anzahl der Cores, die Bandbreite und andere physische Merkmale des virtuellen Computers bestimmt. Informationen zu verfügbaren Größen und physischen Merkmalen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Windows Azure][Größen virtueller Computer und Cloud-Dienste für Windows Azure].

-   **:winrm\_transport** - Ein Array der verfügbaren Übertragungsmöglichkeiten für WinRM. Gültige Übertragungsprotokolle sind "http" und "https". Wenn "https" als Übertragungsprotokoll angegeben wurde, müssen Sie auch **:ssh\_private\_key\_file** und **:ssh\_certificate\_file** verwenden, um das zum Sichern der HTTPS-Kommunikation verwendete Zertifikat anzugeben.

Im Folgenden finden Sie ein Beispiel für das Erstellen eines neuen virtuellen Computers, der eine kleine Serverinstanz verwendet, der Ports öffentlich für den HTTP- (lokaler Port 8080, öffentlicher Port 80) und den HTTPS-Datenverkehr (443) anzeigt und der die Zertifikatauthentifizierung für SSH-Sitzungen mit den angegeben Zertifikatdateien aktiviert:

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-de-de-30GB',
      :location = 'East US'
    }

    vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

### Gewusst wie: Auflisten virtueller Computer

Verwenden Sie zum Auflisten bestehender virtueller Computer für Ihr Azure-Abonnement die **list\_virtual\_machines**-Methode. Mit dieser Methode wird ein Array an **Azure::VirtualMachineManagement::VirtualMachine**-Objekten zurückgegeben:

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### Gewusst wie: Abrufen von Informationen über einen virtuellen Computer

Um eine Instanz von **Azure::VirtualMachineManagement::VirtualMachine** für einen bestimmten virtuellen Computer abzurufen, verwenden Sie die **get\_virtual\_machine**-Methode und geben Sie die Namen des virtuellen Computers und des Cloud-Dienstes an:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### Gewusst wie: Löschen eines virtuellen Computers

Verwenden Sie zum Löschen eines virtuellen Computers die **delete\_virtual\_machine**-Methode und geben Sie die Namen des virtuellen Computers und des Cloud-Dienstes an:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

<div class="dev-callout">

**Warnung**
Mit der **delete\_virtual\_machine**-Methode werden der Cloud-Dienst und alle mit dem virtuellen Computer verbundenen Datenträger gelöscht.

</div>

### Gewusst wie: Herunterfahren eines virtuellen Computers

Verwenden Sie zum Herunterfahren eines virtuellen Computers die **shutdown\_virtual\_machine**-Methode und geben Sie die Namen des virtuellen Computers und des Cloud-Dienstes an:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### Gewusst wie: Starten eines virtuellen Computers

Verwenden Sie zum Starten eines virtuellen Computers die **start\_virtual\_machine**-Methode und geben Sie die Namen des virtuellen Computers und des Cloud-Dienstes an:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

## <a name="vm-images"> </a>Gewusst wie: Arbeiten mit Images und Datenträgern

Vorgänge für Images von virtuellen Computern werden mit der Klasse **Azure::VirtualMachineImageService** durchgeführt. Vorgänge für Datenträger werden mit der Klasse **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService** durchgeführt.

### Gewusst wie: Auflisten von Images virtueller Computer

Verwenden Sie zum Auflisten von Images virtueller Computer die **list\_virtual\_machine\_images**-Methode. Mit dieser Methode wird ein Array an **Azure::VirtualMachineImageService**-Objekten zurückgegeben.

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### Gewusst wie: Auflisten von Datenträgern

Verwenden Sie zum Auflisten von Datenträgern für Ihr Azure-Abonnement die **list\_virtual\_machine\_disks**-Methode. Mit dieser Methode wird ein Array an **Azure::VirtualMachineImageManagement::VirtualMachineDisk**-Objekten zurückgegeben.

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### Gewusst wie: Löschen eines Datenträgers

Verwenden Sie zum Löschen eines Datenträgers die **delete\_virtual\_machine\_disk**-Methode und geben Sie den Namen des zu löschenden Datenträgers an:

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

## <a name="cloud-services"> </a>Gewusst wie: Arbeiten mit Cloud-Diensten

Verwaltungsvorgänge für Azure Cloud Services werden mit der Klasse **Azure::CloudService** durchgeführt.

### Gewusst wie: Erstellen eines Clouddiensts

Verwenden Sie zum Erstellen eines neuen Cloud-Dienstes die **create\_cloud\_service**-Methode und geben Sie einen Namen und mittels Hash-Methode verschiedene Optionen an. Gültige Optionen:

-   **:location** - *Erforderlich*. Die Region, in der der Cloud-Dienst erstellt wird.

-   **:description** - Eine Beschreibung des Cloud-Dienstes.

Mit dem folgenden Code wird ein neuer Cloud-Dienst in der Region "East US" erstellt:

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### Gewusst wie: Auflisten von Cloud-Diensten

Verwenden Sie zum Auflisten von Cloud-Diensten für Ihr Azure-Abonnement die **list\_cloud\_services**-Methode. Mit dieser Methode wird ein Array an **Azure::CloudServiceManagement::CloudService**-Objekten zurückgegeben:

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### Gewusst wie: Überprüfen, ob bereits ein Cloud-Dienst vorhanden ist

Verwenden Sie die **get\_cloud\_service**-Methode, um zu überprüfen, ob ein bestimmter Cloud-Dienst bereits vorhanden ist, und geben Sie den Namen des Cloud-Dienstes an. Wenn ein Cloud-Dienst mit dem angegebenen Namen vorhanden ist, wird **true** zurückgegeben; anderenfalls wird **false** zurückgegeben.

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### Gewusst wie: Löschen eines Clouddiensts

Verwenden Sie zum Löschen eines Cloud-Dienstes die **delete\_cloud\_service**-Methode und geben Sie den Namen des Cloud-Dienstes an:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### Gewusst wie: Löschen einer Bereitstellung

Verwenden Sie zum Löschen einer Bereitstellung für einen Cloud-Dienst die **delete\_cloud\_service\_deployment**-Methode und geben Sie den Namen des Cloud-Dienstes an:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

## <a name="storage-services"> </a>Gewusst wie: Arbeiten mit Speicherdiensten

Verwaltungsvorgänge für Azure Storage Services werden mit der Klasse **Azure::StorageService** durchgeführt.

### Gewusst wie: Speicherkonto erstellen

Verwenden Sie zum Erstellen eines neuen Speicherdienstes die **create\_storage\_account**-Methode und geben Sie einen Namen und mittels Hash-Methode verschiedene Optionen an. Gültige Optionen:

-   **:location** - *Erforderlich*. Die Region, in der der Speicherdienst erstellt wird.

-   **:description** - Eine Beschreibung des Speicherdiensts.

Mit dem folgenden Code wird ein neuer Speicherdienst in der Region "East US" erstellt:

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### Gewusst wie: Auflisten von Speicherkonten

Um eine Liste der Speicherkonten für Ihr Azure-Abonnement abzurufen, verwenden Sie die **list\_storage\_accounts**-Methode. Mit dieser Methode wird ein Array an **Azure::StorageManagement::StorageAccount**-Objekten zurückgegeben:

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### Gewusst wie: Überprüfen, ob ein Speicherkonto vorhanden ist

Verwenden Sie die **get\_storage\_account**-Methode, um zu überprüfen, ob ein bestimmtes Speicherkonto vorhanden ist, und geben Sie den Namen des Speicherkontos an. Wenn das Speicherkonto vorhanden ist, wird **true** zurückgegeben, anderenfalls **false**.

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### Gewusst wie: Löschen eines Speicherkontos

Verwenden Sie zum Löschen eines Speicherkontos die **delete\_storage\_account**-Methode und geben Sie den Namen des Speicherkontos an:

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

## <a name="next-steps"> </a>Nächste Schritte

Da Sie jetzt die Grundlagen der programmgesteuerten Erstellung von virtuellen Azure-Computern erlernt haben, folgen Sie diesen Links, um mehr über die Arbeit mit virtuellen Computern zu erfahren.

-   Besuchen Sie die Seite zu [virtuellen Computern][virtuellen Computern].
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Virtuelle Computer][Virtuelle Computer]
-   Lernen Sie, wie Sie eine [Ruby on Rails-Anwendung auf einem virtuellen Computer][Ruby on Rails-Anwendung auf einem virtuellen Computer] hosten können.

  [Was ist Dienstverwaltung?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Verwaltungszertifikats]: #setup-certificate
  [Erstellen einer Ruby-Anwendung]: #create-app
  [Konfigurieren der Anwendung für das SDK]: #configure-access
  [Einrichten einer Azure-Verwaltungsverbindung]: #setup-connection
  [Gewusst wie: Arbeiten mit virtuellen Computern]: #virtual-machine
  [Gewusst wie: Arbeiten mit Images und Datenträgern]: #vm-images
  [Gewusst wie: Arbeiten mit Cloud-Diensten]: #cloud-services
  [Gewusst wie: Arbeiten mit Speicherdiensten]: #storage-services
  [Nächste Schritte]: #next-steps
  [REST-APIs für Dienstverwaltungsvorgänge]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle]: http://www.windowsazure.com/de-de/manage/install-and-configure-cli/
  [Größen virtueller Computer und Cloud-Dienste für Windows Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/dn197896.aspx
  [virtuellen Computern]: http://www.windowsazure.com/de-de/documentation/services/virtual-machines/
  [Virtuelle Computer]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156003.aspx
  [Ruby on Rails-Anwendung auf einem virtuellen Computer]: http://www.windowsazure.com/de-de/develop/ruby/tutorials/web-app-with-linux-vm/
