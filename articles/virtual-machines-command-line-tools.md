<properties
	pageTitle="Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Dienstverwaltung"
	description="Erfahren Sie, wie Sie mit den Befehlszeilentools für Mac, Linux und Windows Azure im ASM-Modus für Azure-Befehlszeilenschnittstellen verwalten."
	services="web-sites, virtual-machines, mobile-services, cloud-services"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="rasquill"/>

# Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Dienstverwaltung

In diesem Thema wird beschrieben, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle im **ASM-Modus** Dienste in der Befehlszeile von Windows-, Mac- und Linux-Computern erstellen, verwalten und löschen können. Die Funktionen ähneln denen der Windows PowerShell-Cmdlets, die mit den Azure SDKs für .NET, Node.JS und PHP installiert werden.

> [AZURE.NOTE]Das Verwenden von Azure-Diensten im **ASM-Modus** ähnelt begrifflich individuellen Azure-Konzepten und -Diensten wie Websites, virtuellen Computern, virtuellen Netzwerken, Speicher usw. Im **ARM-Modus** stehen Ihnen in der Befehlszeile umfangreichere Funktionen mit einem logisch gruppierten und hierarchischen Ressourcenmodell zur Verfügung. Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](xplat-cli-azure-resource-manager.md).

Eine Installationsanleitung finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

Optionale Parameter werden in eckigen Klammern angezeigt (z. B. [Parameter]). Alle anderen Parameter müssen angegeben werden.

Neben den hier dokumentierten befehlsspezifischen optionalen Parametern gibt es drei weitere optionale Parameter für die Anzeige detaillierter Ausgaben wie z. B. Anforderungsoptionen und Statuscodes. Der Parameter -v bietet ausführliche Ausgaben, und der Parameter -vv bietet noch detailliertere ausführliche Ausgaben. Mit der Option --json erfolgt die Ausgabe im reinen JSON-Format.

##<a name="Manage_your_account_information_and_publish_settings"></a>Verwalten Ihrer Kontoinformationen und Veröffentlichen von Einstellungen
Das Tool verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen im Azure-Portal in einer Einstellungsveröffentlichungsdatei, wie hier beschrieben. Sie können die Einstellungsveröffentlichungsdatei als persistente lokale Konfigurationseinstellung importieren, die das Tool dann in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

**account download [Optionen]**

Dieser Befehl startet einen Browser und lädt Ihre .publishsettings-Datei aus dem Azure-Portal herunter.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [Optionen] &lt;Datei>**


Dieser Befehl importiert eine PUBLISHSETTINGS-Datei oder ein Zertifikat für den zukünftigen Gebrauch durch das Tool.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE].publishsettings-Dateien können Details (Abonnementname und -ID) für mehr als ein Abonnement enthalten. Beim Importieren der PUBLISHSETTINGS-Datei wird das erste Abonnement als Standard-Abonnement verwendet. Führen Sie den folgenden Befehl aus, um ein anderes Abonnement zu verwenden: <code>~$ azure config set subscription &lt;Andere Abonnement-ID&gt;</code>

**account clear [Optionen]**

Mit diesem Befehl werden die zuvor importierten Veröffentlichungseinstellungen gelöscht. Verwenden Sie diesen Befehl, wenn Sie das Tool auf einem Computer nicht mehr benötigen und sicherstellen möchten, dass dieses zukünftig nicht mit Ihrem Benutzerkonto verwendet werden kann.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [Optionen]**

Listet die importierten Abonnements auf

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [Optionen] &lt;Abonnement&gt;**

Setzt das aktuelle Abonnement

###Befehle zum Verwalten Ihrer Affinitätsgruppen

**account affinity-group list [Optionen]**

Dieser Befehl listet Ihre Azure-Affinitätsgruppen auf.

Affinitätsgruppe können konfiguriert werden, wenn eine Gruppe von virtuellen Computern mehrere physische Computer umfasst. Die Affinitätsgruppe gibt an, dass die physischen Computer einander möglichst nahe sein sollten, um die Netzwerklatenz zu minimieren.

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [Optionen] &lt;Name&gt;**

Dieser Befehl erstellt eine neue Affinitätsgruppe

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [Optionen] &lt;Name&gt;**

Dieser Befehl zeigt die Details der Affinitätsgruppe an

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [Optionen] &lt;Name&gt;**

Dieser Befehl löscht die angegebene Affinitätsgruppe

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###Befehle zum Verwalten Ihrer Kontoumgebung

**account env list [Optionen]**

Liste der Kontoumgebungen

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [Optionen][environment]**

Zeigt Details der Kontoumgebung an

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [Optionen][environment]**

Dieser Befehl fügt eine Umgebung zum Konto hinzu

**account env set [Optionen][environment]**

Dieser Befehl setzt die Kontoumgebung

**account env delete [Optionen][environment]**

Dieser Befehl löscht die angegebene Umgebung vom Konto

##<a name="Commands_to_manage_your_Azure_virtual_machines"></a>Befehle zum Verwalten Ihrer virtuellen Azure-Computer
Das folgende Diagramm zeigt, wie virtuelle Azure-Computer in der Produktionsumgebung eines Azure-Cloud-Diensts gehostet werden.

![Technisches Diagramm Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** erstellt das Laufwerk im Blobspeicher (E:\ in diesem Diagramm); **attach** hängt ein bereits erstelltes, aber noch nicht angehängtes Laufwerk an einen virtuellen Computer an.

**vm create [Optionen] &lt;DNS-Name> &lt;Image> &lt;Benutzername> [Kennwort]**

Dieser Befehl erstellt einen neuen virtuellen Azure-Computer. Standardmäßig wird jeder virtuelle Computer in einem eigenen Cloud-Dienst erstellt. Sie können jedoch mit der Option -c angeben, dass ein virtueller Computer zu einem existierenden Cloud-Dienst hinzugefügt werden soll.

Der Befehl "vm create" erstellt, ebenso wie das Azure-Portal, nur virtuelle Computer in der Produktionsumgebung. Es existiert keine Option zum Erstellen eines virtuellen Computers in der Stagingumgebung eines Cloud-Diensts. Wenn für Ihr Abonnement kein Azure-Speicherkonto vorhanden ist, wird mit diesem Befehl eines erstellt.

Sie können den Parameter --location für die Angabe eines Ortes verwenden und den Parameter --affinity-group für die Angabe einer Affinitätsgruppe. Falls Sie keinen dieser Parameter verwenden, müssen Sie einen Ort aus einer Liste auswählen.

Das angegebene Passwort muss 8-123 Zeichen lang sein und die Komplexitätsanforderungen des Betriebssystems erfüllen, das Sie auf dem entsprechenden virtuellen Computer verwenden.

Falls Sie SSH für die Verwaltung virtueller Linux-Computer benötigen (dies ist häufig der Fall), müssen Sie SSH bei der Erstellung des virtuellen Computers über die Option -e aktivieren. SSH kann nach der Erstellung des virtuellen Computers nicht mehr aktiviert werden.

Virtuelle Windows-Computer können RDP später aktivieren, indem Port 3389 als Endpunkt hinzugefügt wird.

Der Befehl unterstützt die folgenden optionalen Parameter:

**-c, --connect** Erstellt den virtuellen Computer in einer bereits erstellten Bereitstellung in einem Hostingdienst. Bei der Verwendung dieser Option ohne -vmname wird der Name des neuen virtuellen Computers automatisch generiert.<br /> **-n, --vm-name** Geben Sie den Namen des virtuellen Computers an. Dieser Parameter nimmt standardmäßig den Namen des Hostingdiensts entgegen. Ohne Angabe von -vmname wird der Name des neuen virtuellen Computers als &lt;Dienstname>&lt;ID> generiert, wobei &lt;ID> die Anzahl existierender virtueller Computer im Dienst plus 1 ist. Wenn Sie mit diesem Befehl beispielsweise einen neuen virtuellen Computer zum Hostingdienst MyService hinzufügen, der bereits einen virtuellen Computer enthält, erhält der neue virtuelle Computer den Namen MyService2.<br /> **-u, --blob-url** Geben Sie die URL des Blobspeichers an, in dem das Systemlaufwerk des virtuellen Computers erstellt werden soll. <br /> **-z, --vm-size** Geben Sie die Größe des virtuellen Computers an. Gültige Werte sind: "ExtraSmall", "Small", "Medium", "Large", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2", "Standard_G3", "Standard_G4", "Standard_G55". Der Standardwert ist "Small". <br /> **-r** Fügt einem virtuellen Windows-Computer RDP-Konnektivität hinzu <br /> **-e, --ssh** Fügt einem virtuellen Windows-Computer SSH-Konnektivität hinzu <br /> **-t, --ssh-cert** Legt das SSH-Zertifikat fest <br /> **-s** Das Abonnement <br /> **-o, --community** Das angegebene Bild ist ein Community-Image. <br /> **-w** Der Name des virtuellen Netzwerks <br/> **-l, --location** Gibt den Speicherort an (z. B. "Norden-Mitte USA") <br /> **-a, --affinity-group** Gibt die Affinitätsgruppe an<br /> **-w, --virtual-network-name** Gibt das virtuelle Netzwerk an, dem der neue virtuelle Computer hinzugefügt werden soll Virtuelle Netzwerke können im Azure-Portal eingerichtet und verwaltet werden.<br /> **-b, --subnet-names** Gibt die Subnetznamen an, die dem virtuellen Computer zugewiesen werden sollen.

In diesem Beispiel wird das Image MSFT__Win2K8R2SP1-120514-1520-141205-01-de-de-30GB von der Plattform bereitgestellt. Weitere Informationen zu Betriebssystem-Images finden Sie in der VM-Imageliste.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;DNS-Name> &lt;Rollendatei>**

Dieser Befehl erstellt einen neuen virtuellen Azure-Computer aus einer JSON-Rollendatei.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [Optionen]**

Dieser Befehl listet virtuelle Azure-Computer auf. Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [Optionen]**

Dieser Befehl listet alle verfügbaren Orte für Azure-Konten auf.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [Optionen] &lt;Name>**

Dieser Befehl zeigt Details zu einem virtuellen Azure-Computer an. Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [Optionen] &lt;Name>**

Dieser Befehl löscht einen virtuellen Azure-Computer. Standardmäßig löscht dieser Befehl nicht den Azure-Blob, aus dem Betriebssystem- und Datenlaufwerk erstellt wurden. Verwenden Sie die Option -b, um den Blob gemeinsam mit dem virtuellen Computer zu löschen.

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [Optionen] &lt;Name>**

Dieser Befehl startet einen virtuellen Azure-Computer.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [Optionen] &lt;Name>**

Dieser Befehl startet einen virtuellen Azure-Computer neu.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [Optionen] &lt;Name>**

Dieser Befehl fährt einen virtuellen Azure-Computer herunter. Mit der Option -p können Sie angeben, dass die Ressource beim Herunterfahren nicht freigegeben werden soll.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;VM-Name> &lt;Zielimage-Name>**

Dieser Befehl erfasst ein Image eines virtuellen Azure-Computers.

Das Image eines virtuellen Computers kann nur erfasst werden, wenn der Zustand des virtuellen Computers **Beendet** lautet. Fahren Sie den virtuellen Computer herunter, bevor Sie fortfahren.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [Optionen] &lt;VM-Name> &lt;Dateipfad>**

Dieser Befehl exportiert ein Image eines virtuellen Azure-Computers in eine Datei

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_endpoints"></a>Befehle zum Verwalten der Endpunkte Ihrer virtuellen Azure-Computer
Das folgende Diagramm zeigt die Architektur einer typischen Umgebung mit mehreren Instanzen eines virtuellen Computers. Beachten Sie, dass in diesem Beispiel der Port 3389 (RDP-Zugriff) auf allen virtuellen Computern geöffnet ist. Außerdem hat jeder virtuelle Computer eine interne IP-Adresse (z. B. 168.55.11.1), die vom Lastenausgleichsmodul für die Zuweisung des Netzwerkverkehrs an die einzelnen virtuellen Computer verwendet wird. Diese interne IP-Adresse kann auch für die Kommunikation zwischen virtuellen Computern verwendet werden.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Externe Anforderungen an virtuelle Computer durchlaufen ein Lastenausgleichsmodul. Daher ist es in Umgebungen mit mehreren virtuellen Computern nicht möglich, Anforderungen an einen speziellen virtuellen Computer zu schicken. In Umgebungen mit mehreren virtuellen Computern müssen Sie daher Portzuordnungen zwischen den virtuellen Computern (VM-Port) und dem Lastenausgleichsmodul (LB-Port) konfigurieren.

**vm endpoint create &lt;VM-Name> &lt;LB-Port> [VM-Port]**

Dieser Befehl erstellt einen Endpunkt für einen virtuellen Azure-Computer. Mit -u oder --enable-direct-server-return können Sie angeben, ob dieser Endpunkt Direct Server Return verwenden soll. Diese Option ist standardmäßig deaktiviert.

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [Optionen] &lt;VM-Name> &lt;LB-Port>[:&lt;VM-Port>[:&lt;Protokoll>[:&lt;Direct Server Return aktivieren>[:&lt;Festgelegter LB-Name>[:&lt;Testprotokoll>[:&lt;Testport>[:&lt;Testpfad>[:&lt;Interner LB-Name>]]]]]]]] {1-*}**

Erstellt mehrere Endpunkte für virtuelle Computer.

**vm endpoint delete [Optionen] &lt;VM-Name> &lt;Endpunktname>**

Dieser Befehl löscht einen Endpunkt eines virtuellen Azure-Computers.

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;VM-Name>**

Dieser Befehl listet alle Endpunkte eines virtuellen Azure-Computers auf. Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [Optionen] &lt;VM-Name> &lt;Endpunktname>**

Dieser Befehl aktualisiert einen Endpunkt eines virtuellen Computers mit den angegebenen Optionen.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [Optionen] &lt;VM-Name>**

Dieser Befehl zeigt die Details der Endpunkte eines virtuellen Computers an

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_images"></a>Befehle zum Verwalten der Images Ihrer virtuellen Azure-Computer

Images von virtuellen Computern sind Abbildungen bereits konfigurierter virtueller Computer und können bei Bedarf repliziert werden.

**vm image list [Optionen]**

Dieser Befehl listet Images von virtuellen Computern auf. Insgesamt existieren drei Arten von Images: von Microsoft erstellte Images mit dem Präfix "MSFT", Images von Drittanbietern, normalerweise mit dem Namen des Herstellers als Präfix, und benutzerdefinierte Images. Um ein Image zu erstellen, können Sie entweder einen existierenden virtuellen Computer erfassen oder ein Image aus einer benutzerdefinierten .vhd-Datei erstellen, die Sie in einen Blobspeicher hochgeladen haben. Weitere Informationen zu benutzerdefinierten .vhd-Dateien finden Sie im Abschnitt zur Erstellung von Images für virtuelle Computer. Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-de-de-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.de-de.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.de-de.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-de-de-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-de-de-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-de-de-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [Optionen] &lt;Name>**

Dieser Befehl zeigt die Details eines Images eines virtuellen Computers an.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [Optionen] &lt;Name>**

Dieser Befehl löscht ein Image eines virtuellen Computers.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;Name> [Quellpfad]**

Dieser Befehl erstellt ein Image eines virtuellen Computers. Ihre benutzerdefinierten .vhd-Dateien werden in einen Blobspeicher hochgeladen, und das Image wird anschließend von dort aus erstellt. Anschließend können Sie das Image verwenden, um einen virtuellen Computer zu erstellen. Die Parameter für Ort und Betriebssystem müssen angegeben werden.

Manche Systeme haben Grenzen für Dateideskriptoren pro Prozess. Wenn diese Grenze überschritten wird, zeigt das Tool einen entsprechenden Fehler an. Sie können den Befehl erneut ausführen und mit dem Parameter -p &lt;Anzahl> die Anzahl der parallelen Uploads begrenzen. Der Standardwert für parallele Uploads ist 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_data_disks"></a>Befehle zum Verwalten der Datenträger Ihrer virtuellen Azure-Computer

Datenträger sind VHD-Dateien im Blob-Speicher, die von virtuellen Computern genutzt werden können. Weitere Informationen zur Bereitstellung von Datenlaufwerken im Blobspeicher finden Sie im bereits gezeigten technischen Azure-Diagramm.

Die Befehle zum Anhängen von Datenlaufwerken (azure vm disk attach und azure vm disk attach-new) weisen dem angehängten Datenlaufwerk eine logische Gerätenummer (Logical Unit Number LUN) zu, wie vom SCSI-Protokoll gefordert. Der erste Datenträger eines virtuellen Computers erhält die Nummer LUN 0, der nächste Datenträger die Nummer LUN 1 usw.

Wenn Sie einen Datenträger mit dem Befehl "azure vm disk detach" trennen, können Sie mit dem Parameter &lt;lun&gt; angeben, welcher Datenträger getrennt werden soll.

> [AZURE>NOTE]Die Datenträger sollten stets in umgekehrter Reihenfolge getrennt werden, beginnend mit der höchsten zugewiesenen LUN. Die SCSI-Ebene von Linux unterstützt keine Trennung von niedrigeren LUNs, solange noch eine höhere LUN angehängt ist. Sie sollten z. B. "LUN 0" nicht trennen, solange "LUN 1" noch angehängt ist.

**vm disk show [Optionen] &lt;Name>**

Dieser Befehl zeigt Details zu einem Azure-Laufwerk an.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-de-de-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [Optionen][vm-name]**

Dieser Befehl listet Azure-Laufwerke bzw. Laufwerke auf, die an einen bestimmten virtuellen Computer angehängt sind. Wenn der Parameter VM-Name angegeben wird, listet der Befehl alle an diesen virtuellen Computer angehängten Laufwerke auf. Lun 1 wird mit dem virtuellen Computer erstellt, und alle weiteren Laufwerke werden separat angehängt.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

Wenn der Name des virtuellen Computers nicht angegeben wird, listet der Befehl alle Laufwerke auf.

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [Optionen] &lt;Name>**

Dieser Befehl löscht einen Azure-Laufwerk aus einem persönlichen Repository. Das Laufwerk muss vor dem Löschen vom virtuellen Computer getrennt werden.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;Name> [Quellpfad]**

Dieser Befehl lädt und registriert einen Azure-Datenträger. Hierfür muss --blob-url, --location, oder --affinity-group angegeben werden. Wenn Sie diesen Befehl mit [Quellpfad] verwenden, wird die angegebene VHD-Datei hochgeladen, und es wird ein neues Image erstellt. Sie können das Image anschließend mit dem Befehl vm disk attach an einen virtuellen Computer anhängen.

Manche Systeme haben Grenzen für Dateideskriptoren pro Prozess. Wenn diese Grenze überschritten wird, zeigt das Tool einen entsprechenden Fehler an. Sie können den Befehl erneut ausführen und mit dem Parameter -p &lt;Anzahl> die Anzahl der parallelen Uploads begrenzen. Der Standardwert für parallele Uploads ist 96.

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [Optionen] &lt;Quellpfad> &lt;Blob-URL> &lt;Speicherkontoschlüssel>**

Mit diesem Befehl können Sie ein Laufwerk eines virtuellen Computers hochladen

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;VM-Name> &lt;Datenträgerimage-Name>**

Dieser Befehl hängt ein existierendes Laufwerk aus dem Blob-Speicher an einen existierenden virtuellen Computer in einem Cloud-Dienst an.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;VM-Name> &lt;Größe-in-GB> [Blob-URL]**

Dieser Befehl hängt einen Datenträger an einen virtuellen Azure-Computer an. In diesem Beispiel ist 20 die Größe des neuen anzuhängenden Laufwerks in Gigabyte. Sie können optional als letzten Parameter eine Blob-URL anfügen, um den Ziel-Blob explizit anzugeben. Wenn Sie keine Blob-URL angeben, wird ein Blob-Objekt automatisch generiert.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;VM-Name> &lt;LUN>**

Dieser Befehl trennt einen Datenträger, der mit einem virtuellen Azure-Computer verbunden ist. &lt;LUN> gibt das zu trennende Laufwerk an. Mit dem Befehl "vm disk-list &lt;VM-Name>" können Sie vor dem Trennen eine Liste der verbundenen Datenträger abrufen.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

##<a name="Commands_to_manage_your_Azure_cloud_services"></a>Befehle zum Verwalten Ihrer Azure-Cloud-Dienste

Azure-Cloud-Dienste sind Anwendungen und Dienste, die in Web- und Workerrollen gehostet sind. Mit den folgenden Befehlen können Sie Azure-Cloud-Dienste verwalten.

**service create [Optionen] &lt;Dienstname>**

Dieser Befehl erstellt einen neuen Cloud-Dienst

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [Optionen] &lt;Dienstname>**

Dieser Befehl zeigt die Details eines Azure-Cloud-Diensts an

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [Optionen]**

Dieser Befehl listet Azure-Cloud-Dienste auf.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [Optionen] &lt;Name>**

Dieser Befehl löscht einen Azure-Cloud-Dienst.

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

Um den Löschvorgang zu erzwingen, verwenden Sie den Parameter `-q`.


##<a name="Commands_to_manage_your_Azure_certificates"></a>Befehle zum Verwalten Ihrer Azure-Zertifikate

Zertifikate für Azure-Dienste sind SSL-Zertifikate, die mit Ihrem Azure-Konto verknüpft sind. Weitere Informationen zu Azure-Zertifikaten finden Sie unter [Verwalten von Zertifikaten](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**service cert list [Optionen]**

Dieser Befehl listet Azure-Zertifikate auf.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;DNS-Präfix> &lt;Datei> [Kennwort]**

Dieser Befehl lädt ein Azure-Zertifikat hoch. Lassen Sie das Kennwort leer für Zertifikate, die nicht mit Kennwort geschützt sind.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [Optionen] &lt;Fingerabdruck>**

Dieser Befehl löscht ein Azure-Zertifikat.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK


##<a name="Commands_to_manage_your_web_sites"></a>Befehle zum Verwalten Ihrer Web-Apps

Eine Azure-Web-App ist eine Webkonfiguration, die per URI zugänglich ist. Web-Apps werden auf virtuellen Computern gehostet, die Sie jedoch nicht selbst erstellen und bereitstellen müssen. Azure kümmert sich um diese Details.

**site list [Optionen]**

Dieser Befehl listet Ihre Web-Apps auf.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [Optionen][name]**

Dieser Befehl setzt Konfigurationsoptionen für Ihre Web-App [Name]

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [Optionen]**

Dieser Befehl generiert ein benutzerdefiniertes Bereitstellungsskript

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [Optionen][name]**

Dieser Befehl erstellt eine neue Web-App und ein neues lokales Verzeichnis.

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE]Der Name der Website muss eindeutig sein. Sie können keine Website mit dem DNS-Namen einer existierenden Website erstellen.

**site browse [Optionen][name]**

Dieser Befehl öffnet Ihre Web-App in einem Browser.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [Optionen][name]**

Dieser Befehl zeigt Details zu einer Web-App an.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [Optionen][name]**

Dieser Befehl löscht eine Web-App.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [Optionen][name]**

Dieser Befehl tauscht zwei Web-App-Steckplätze.

Dieser Befehl unterstützt die folgende zusätzliche Option:

**-q oder **--quiet**: Keine Bestätigungsaufforderungen. Verwenden Sie diese Option in automatisierten Skripts.


**site start [Optionen][name]**

Dieser Befehl startet eine Web-App.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [Optionen][name]**

Dieser Befehl beendet eine Web-App.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [Optionen][name]

Dieser Befehl beendet die angegebene Web-App und startet sie dann wieder.

Dieser Befehl unterstützt die folgende zusätzliche Option:

**--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.


**site location list [Optionen]**

Dieser Befehl listet Ihre Web-App-Orte auf.

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###Befehle zum Verwalten Ihrer Web-App-Anwendungseinstellungen

**site appsetting list [Optionen][name]**

Dieser Befehl listet die Anwendungseinstellungen einer Web-App auf.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [Optionen] &lt;Schlüssel-Wert-Paar> [Name]**

Dieser Befehl fügt Ihrer Web-App eine Anwendungseinstellung in Form eines Schlüssel-Wert-Paars hinzu.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [Optionen] &lt;Schlüssel> [Name]**

Dieser Befehl löscht die angegebene Anwendungseinstellung in der Web-App.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [Optionen] &lt;Schlüssel> [Name]**

Dieser Befehl zeigt Details zu der angegebenen Anwendungseinstellung an

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Befehle zum Verwalten Ihrer Web-App-Zertifikate

**site cert list [Optionen][name]**

Dieser Befehl listet Ihre Web-App-Zertifikate auf.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [Optionen] &lt;Zertifikatpfad> [Name]**

**site cert delete [Optionen] &lt;Fingerabdruck> [Name]**

**site cert show [Optionen] &lt;Fingerabdruck> [Name]**

Dieser Befehl zeigt die Details des Zertifikats an

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###Befehle zum Verwalten Ihrer Web-App-Verbindungszeichenfolgen

**site connectionstring list [Optionen][name]**

**site connectionstring add [Optionen] &lt;Verbindungsname> &lt;Wert> &lt;Typ> [Name]**

**site connectionstring delete [Optionen] &lt;Verbindungsname> [Name]**

**site connectionstring show [Optionen] &lt;Verbindungsname> [Name]**

###Befehle zum Verwalten der Standarddokumente Ihrer Web-App

**site defaultdocument list [Optionen][name]**

**site defaultdocument add [Optionen] &lt;Dokument> [Name]**

**site defaultdocument delete [Optionen] &lt;Dokument> [Name]**

###Befehle zum Verwalten Ihrer Web-App-Bereitstellungen

**site deployment list [Optionen][name]**

**site deployment show [Optionen] &lt;Commit-ID> [Name]**

**site deployment redeploy [Optionen] &lt;Commit-ID> [Name]**

**site deployment github [Optionen][name]**

**site deployment user set [Optionen][username][Kennwort]**

###Befehle zum Verwalten Ihrer Web-App-Domänen

**site domain list [Optionen][name]**

**site domain add [Optionen] &lt;DN> [Name]**

**site domain delete [Optionen] &lt;DN> [Name]**

###Befehle zum Verwalten Ihrer Web-App-Handlerzuordnungen

**site handler list [Optionen][name]**

**site handler add [Optionen] &lt;Erweiterung> &lt;Prozessor> [Name]**

**site handler delete [Optionen] &lt;Erweiterung> [Name]**

###Befehle zum Verwalten Ihrer Webaufträge

**site job list [Optionen][name]**

Dieser Befehl listet alle Webaufträge unter einer Web-App auf.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-type** &lt;Auftragstyp>: Optional. Der Typ des Webauftrags. Gültige Werte sind "triggered" und "continuous". Standardmäßig werden Webaufträge aller Typen zurückgegeben.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job show [Optionen] &lt;Auftragsname> &lt;Auftragstyp> [Name]**

Dieser Befehl zeigt die Details eines bestimmten Webauftrags an.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--job-type** &lt;Auftragstyp>: Erforderlich. Der Typ des Webauftrags. Gültige Werte sind "triggered" und "continuous".
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job delete [Optionen] &lt;Auftragsname> &lt;Auftragstyp> [Name]**

Dieser Befehl löscht den angegebenen Webauftrag.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--job-type** &lt;Auftragstyp>: Erforderlich. Der Typ des Webauftrags. Gültige Werte sind "triggered" und "continuous".
+ **-q** oder **--quiet**: Keine Bestätigungsaufforderungen. Verwenden Sie diese Option in automatisierten Skripts.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job upload [Optionen] &lt;Auftragsname> &lt;Auftragstyp> <jobFile> [Name]**

Dieser Befehl löscht den angegebenen Webauftrag.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--job-type** &lt;Auftragstyp>: Erforderlich. Der Typ des Webauftrags. Gültige Werte sind "triggered" und "continuous".
+ **--job-file** &lt;Auftragsdatei>: Erforderlich. Die Auftragsdatei.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job start [Optionen] &lt;Auftragsname> &lt;Auftragstyp> [Name]**

Dieser Befehl startet den angegebenen Webauftrag.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--job-type** &lt;Auftragstyp>: Erforderlich. Der Typ des Webauftrags. Gültige Werte sind "triggered" und "continuous".
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job stop [Optionen] &lt;Auftragsname> &lt;Auftragstyp> [Name]**

Dieser Befehl beendet den angegebenen Webauftrag. Nur fortlaufende Aufträge können beendet werden.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

###Befehle zum Verwalten Ihres Webauftragsverlaufs

**site job history list [Optionen][jobName] [Name]**

Dieser Befehl zeigt einen Ausführungsverlauf des angegebenen Webauftrags an.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

**site job history show [Optionen][jobName] [Ausführungs-ID][name]**

Dieser Befehl zeigt die Details der Auftragsausführung für den angegebenen Webauftrag an.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--job-name** &lt;Auftragsname>: Erforderlich. Der Name des Webauftrags.
+ **--run-id** &lt;Ausführungs-ID>: Optional. Die ID des Ausführungsverlaufs. Wenn diese Option nicht angegeben wird, wird die letzte Ausführung angezeigt.
+ **--slot** &lt;Steckplatz>: Der Name der Steckplatzes, der neu gestartet werden soll.

###Befehle zum Verwalten Ihrer Web-App-Diagnose

**site log download [Optionen][name]**

Lädt eine ZIP-Datei mit Ihrer Web-App-Diagnose herunter.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [Optionen][name]**

Dieser Befehl verbindet Ihre Befehlszeile mit dem Logstreamingdienst.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [Optionen][name]**

Dieser Befehl konfiguriert die Diagnoseoptionen für Ihre Web-App.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###Befehle zum Verwalten Ihrer Web-App-Repositorys

**site repository branch [Optionen] &lt;Verzweigung> [Name]**

**site repository delete [Optionen][name]**

**site repository sync [Optionen][name]**

###Befehle zum Verwalten Ihrer Web-App-Skalierung

**site scale mode [Optionen] &lt;Modus> [Name]**

**site scale instances [Optionen] &lt;Instanzen> [Name]**


##<a name="Commands_to_manage_mobile_services"></a>Befehle zum Verwalten von Azure Mobile Services

Azure Mobile Services vereint verschiedene Azure-Dienste, die Back-End-Funktionalitäten für Ihre Anwendungen bereitstellen. Mobile Services-Befehle sind in die folgenden Kategorien unterteilt:

+ [Befehle zum Verwalten von Mobile Services-Instanzen](#Mobile_Services)
+ [Befehle zum Verwalten der Mobile Services-Konfiguration](#Mobile_Configuration)
+ [Befehle zum Verwalten von Mobile Services-Tabellen](#Mobile_Tables)
+ [Befehle zum Verwalten von Mobile Services-Skripts](#Mobile_Scripts)
+ [Befehle zum Verwalten geplanter Aufträge](#Mobile_Jobs)
+ [Befehle zum Skalieren eines Mobile Service](#Mobile_Scale)

Die folgenden Optionen sind für die meisten Mobile Services-Befehle gültig:

+ **-h** oder **--help**: Zeigt hilfreiche Informationen zur Befehlsnutzung an.
+ **-s `<id>`** oder **--subscription `<id>`**: Verwendet ein bestimmtes Abonnement, angegeben als `<id>`.
+ **-v** oder **--verbose**: Ausführliche Ausgabe.
+ **--json**: JSON-Ausgabe.

###<a name="Mobile_Services"></a>Befehle zum Verwalten von Mobile Services-Instanzen

**mobile locations [Optionen]**

Dieser Befehl listet die von Mobile Services unterstützten geografischen Standorte auf.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [Optionen][servicename] [SQL-Admin-Benutzername][sqlAdminPassword]**

Dieser Befehl erstellt einen Mobile Service inklusive SQL-Datenbank und -Server.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-r `<sqlServer>`** oder **--sqlServer `<sqlServer>`**: Verwendet einen vorhandenen SQL-Datenbankserver, angegeben als `<sqlServer>`.
+ **-d `<sqlDb>`** oder **--sqlDb `<sqlDb>`**: Verwendet eine vorhandene SQL-Datenbank, angegeben als `<sqlDb>`.
+ **-l `<location>`** oder **--location `<location>`**: Erstellt den Dienst an einem bestimmten Ort, angegeben als `<location>`. Führen Sie "azure mobile locations" aus, um eine Liste der verfügbaren Orte zu erhalten.
+ **--sqlLocation `<location>`**: Erstellt den SQL Server an einem bestimmten Ort (`<location>`); Standardwert ist der Ort des Mobile Service.

**mobile delete [Optionen][servicename]**

Dieser Befehl löscht einen Mobile Service inklusive SQL-Datenbank und -Server.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-d** oder **--deleteData**: Löscht alle Daten aus diesem Mobile Service aus der Datenbank.
+ **-a** oder **--deleteAll**: Löscht die SQL-Datenbank und den Server.
+ **-q** oder **--quiet**: Keine Bestätigungsaufforderungen. Verwenden Sie diese Option in automatisierten Skripts.

**mobile list [Optionen]**

Dieser Befehl listet Ihre Mobile Services auf.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [Optionen][servicename]**

Dieser Befehl zeigt Details zu einem Mobile Service an.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [Optionen][servicename]**

Dieser Befehl startet eine Mobile Services-Instanz neu.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [Optionen][servicename]**

Dieser Befehl gibt die Mobile Service-Protokolle zurück und filtert alle Protokolltypen mit Ausnahme von `error` heraus.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-r `<query>`** oder **--query `<query>`**: Führt die angegebene Protokollabfrage aus.
+ **-t `<type>`** oder **--type `<type>`**: Filtert die Protokolle nach `<type>` der Einträge. Mögliche Typen sind `information`, `warning` oder `error`.
+ **-k `<skip>`** oder **--skip `<skip>`**: Überspringt die in `<skip>` angegebene Anzahl von Zeilen.
+ **-p `<top>`** oder **--top `<top>`**: Gibt die in `<top>` angegebene Anzahl von Zeilen zurück.

> [AZURE.NOTE]Der Parameter **--query** hat Vorrang vor **--type**, **--skip** und **--top**.

**mobile recover [Optionen][unhealthyservicename] [Name_des_fehlerfreien_Diensts]**

Dieser Befehl stellt einen fehlerhaften Mobile Service dadurch wieder her, dass dieser in einen fehlerfreien Mobile Service in einer anderen Region verschoben wird.

Dieser Befehl unterstützt die folgende zusätzliche Option:

**-q** oder **--quiet**: Unterdrückt die Aufforderung zur Bestätigung der Wiederherstellung.

**mobile key regenerate [Optionen][servicename] [Typ]**

Dieser Befehl generiert den Mobile Service-Anwendungsschlüssel neu.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

Mögliche Schlüsseltypen sind `master` und `application`.

> [AZURE.NOTE]Wenn Sie einen Schlüssel neu generieren, können Clients, die den alten Schlüssel verwenden, möglicherweise nicht mehr auf Ihren Mobile Service zugreifen. Wenn Sie den Anwendungsschlüssel neu generieren, sollten Sie Ihre Anwendung mit dem neuen Schlüsselwert aktualisieren.

**mobile key set [Optionen][servicename] [Typ][value]**

Dieser Befehl legt den Schlüssel des Mobile Service auf einen bestimmten Wert fest.


###<a name="Mobile_Configuration"></a>Befehle zum Verwalten der Mobile Services-Konfiguration

**mobile config list [Optionen][servicename]**

Dieser Befehl listet Konfigurationsoptionen für einen Mobile Service auf.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [Optionen][servicename] [Schlüssel]**

Dieser Befehl ruft eine bestimmte Konfigurationsoption für einen Mobile Service ab, in diesem Fall das dynamische Schema.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [Optionen][servicename] [Schlüssel][value]**

Dieser Befehl ändert den Wert einer bestimmten Konfigurationsoption für einen Mobile Service, in diesem Fall das dynamische Schema.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


###<a name="Mobile_Tables"></a>Befehle zum Verwalten von Mobile Services-Tabellen

**mobile table list [Optionen][servicename]**

Dieser Befehl listet alle Tabellen in Ihrem Mobile Service auf.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [Optionen][servicename] [Tabellenname]**

Dieser Befehl zeigt Details zu einer bestimmten Tabelle an.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [Optionen][servicename] [Tabellenname]**

Dieser Befehl erstellt eine Tabelle.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Dieser Befehl unterstützt die folgende zusätzliche Option:

+ **-p `&lt;permissions>`** oder **--permissions `&lt;permissions>`**: Durch Kommas getrennte Liste von `<operation>`=`<permission>`-Paaren, wobei `<operation>` entweder `insert`, `read`, `update` oder `delete` und `&lt;permissions>` entweder `public`, `application` (Standard), `user` oder `admin` sein kann.

**mobile data read [Optionen][servicename] [Tabellenname][query]**

Dieser Befehl liest Daten aus einer Tabelle.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-k `<skip>`** oder **--skip `<skip>`**: Überspringt die in `<skip>` angegebene Anzahl von Zeilen.
+ **-t `<top>`** oder **--top `<top>`**: Gibt die in `<top>` angegebene Anzahl von Zeilen zurück.
+ **-l** oder **--list**: Gibt Daten im Listenformat zurück.

**mobile table update [Optionen][servicename] [Tabellenname]**

Dieser Befehl setzt die Berechtigungen für eine Tabelle ausschließlich für Administratoren.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-p `&lt;permissions>`** oder **--permissions `&lt;permissions>`**: Durch Kommas getrennte Liste von `<operation>`=`<permission>`-Paaren, wobei `<operation>` entweder `insert`, `read`, `update` oder `delete` und `&lt;permissions>` entweder `public`, `application` (Standard), `user` oder `admin` sein kann.
+ **--deleteColumn `<columns>`**: Durch Kommas getrennte Liste zu löschender Spalten, angegeben als `<columns>`.
+ **-q** oder **--quiet**: Löscht Spalten ohne Bestätigungsaufforderung.
+ **--addIndex `<columns>`**: Durch Kommas getrennte Liste von Spalten, die dem Index hinzugefügt werden sollen.
+ **--deleteIndex `<columns>`**: Durch Kommas getrennte Liste von Spalten, die aus dem Index entfernt werden sollen.

**mobile table delete [Optionen][servicename] [Tabellenname]**

Dieser Befehl löscht eine Tabelle.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Verwenden Sie den Parameter -q, um die Tabelle ohne Bestätigung zu löschen. Diese Option ist hilfreich für automatisierte Skripts.

**mobile data truncate [Optionen][servicename] [Tabellenname]**

Dieser Befehl löscht alle Zeilen aus der Tabelle.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


###<a name="Mobile_Scripts"></a>Befehle zum Verwalten von Skripts

Mit den Befehlen in diesem Abschnitt können Sie die Serverskripts eines Mobile Service verwalten. Weitere Informationen erhalten Sie unter [Verwenden von Serverskripts in Mobile Services.](mobile-services-how-to-use-server-scripts.md).

**mobile script list [Optionen][servicename]**

Dieser Befehl listet registrierte Skripts auf, sowohl Tabellen- als auch Planerskripts.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [Optionen][servicename] [Skriptname]**

Dieser Befehl lädt das insert-Skript aus der Tabelle TodoItem in eine Datei namens `todoitem.insert.js` im Unterverzeichnis `table` herunter.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-p `<path>`** oder **--path `<path>`**: Der Ort, an dem die Skriptdatei gespeichert werden soll, relativ zum aktuellen Arbeitsverzeichnis.
+ **-f `<file>`** oder **--file `<file>`**: Der Name der Datei, in der das Skript gespeichert werden soll.
+ **-o** oder **--override**: Vorhandene Datei überschreiben.
+ **-c** oder **--console**: Skript in die Konsole anstatt in eine Datei schreiben.

**mobile script upload [Optionen][servicename] [Skriptname]**

Dieser Befehl lädt ein Skript namens `todoitem.insert.js` aus dem Unterverzeichnis `table` hoch.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

Der Name der Datei muss sich aus Tabellen- und Operationsnamen zusammensetzen, und die Datei muss sich im Unterordner "Table" relativ zum Verzeichnis befinden, in dem der Befehl ausgeführt wird. Mit dem Parameter **-f `<file>`** oder **--file `<file>`** können Sie einen anderen Dateinamen und einen Pfad zu der Datei angeben, die das zu registrierende Skript enthält.


**mobile script delete [Optionen][servicename] [Skriptname]**

Dieser Befehl löscht das existierende insert-Skript aus der Tabelle TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

###<a name="Mobile_Jobs"></a>Befehle zum Verwalten geplanter Aufträge

Mit den Befehlen in diesem Abschnitt können Sie geplante Aufträge eines Mobile Service verwalten. Weitere Informationen finden Sie unter [Geplante Aufträge](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [Optionen][servicename]**

Dieser Befehl listet geplante Aufträge auf.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [Optionen][servicename] [Auftragsname]**

Dieser Befehl erstellt einen neuen, stündlich auszuführenden Auftrag mit dem Namen `getUpdates`.

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-i `<number>`** oder **--interval `<number>`**: Das Ausführungsintervall als ganze Zahl, der Standardwert ist `15`.
+ **-u `<unit>`** oder **--intervalUnit `<unit>`**: Die Einheit für das _Intervall_. Dies kann einer der folgenden Werte sein:
	+ **minute** (Standard)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (bedarfsgesteuerte Aufgaben)
+ **-t `<time>`** **--startTime `<time>`** Die Startzeit der ersten Skriptausführung im ISO-Format, der Standardwert ist `now`.

> [AZURE.NOTE]Neue Aufträge werden bei der Erstellung deaktiviert, da zunächst ein Skript hochgeladen werden muss. Mit dem Befehl **mobile script upload** können Sie ein Skript hochladen, und mit dem Befehl **mobile job update** können Sie den Auftrag aktivieren.

**mobile job update [Optionen][servicename] [Auftragsname]**

Der folgende Befehl aktiviert den deaktivierten Auftrag `getUpdates`.

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-i `<number>`** oder **--interval `<number>`**: Das Ausführungsintervall als ganze Zahl, der Standardwert ist `15`.
+ **-u `<unit>`** oder **--intervalUnit `<unit>`**: Die Einheit für das _Intervall_. Dies kann einer der folgenden Werte sein:
	+ **minute** (Standard)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (bedarfsgesteuerte Aufgaben)
+ **-t `<time>`** **--startTime `<time>`** Die Startzeit der ersten Skriptausführung im ISO-Format, der Standardwert ist `now`.
+ **-a `<status>`** oder **--status `<status>`**: Der Status des Auftrags, entweder `enabled` oder `disabled`.

**mobile job delete [Optionen][servicename] [Auftragsname]**

Dieser Befehl löscht den geplanten Auftrag getUpdates vom Server TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE]Beim Löschen eines Auftrags wird auch das hochgeladene Skript gelöscht.

###<a name="Mobile_Scale"></a>Befehle zum Skalieren eines Mobile Service

Mit den Befehlen in diesem Abschnitt können Sie einen Mobile Service skalieren. Weitere Informationen finden Sie unter [Scaling a Mobile Service](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx) (Skalieren von Mobile Services, in englischer Sprache).

**mobile scale show [Optionen][servicename]**

Dieser Befehl zeigt Skalierungsinformationen an, inklusive dem aktuellen Servermodus und der Anzahl der Instanzen.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [Optionen][servicename]**

Dieser Befehl ändert den Skalierungsmodus des Mobile Service von Free zu Premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-c `<mode>`** oder **--computeMode `<mode>`**: Der Arbeitsmodus kann `Free` oder `Reserved` sein.
+ **-i `<count>`** oder **--numberOfInstances `<count>`**: Die Anzahl der Instanzen bei Ausführung im Reserved-Modus.

> [AZURE.NOTE]Wenn Sie den Arbeitsmodus `Reserved` aktivieren, laufen all Ihre Mobile Services in derselben Region im Premiummodus.


###Befehle zum Aktivieren von Vorschaufeatures für Ihren Mobile Service

**mobile preview list [Optionen][servicename]**

Dieser Befehl zeigt die für den angegebenen Dienst verfügbaren Vorschaufeatures an und ob diese aktiviert sind.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [Optionen][servicename] [Featurename]**

Dieser Befehl aktiviert das angegebene Vorschaufeature für einen Mobile Service. Nachdem Vorschaufeatures aktiviert wurden, können sie für einen Mobile Service nicht mehr deaktiviert werden.

###Befehle zum Verwalten Ihrer Mobile Services-APIs

**mobile api list [Optionen][servicename]**

Dieser Befehl zeigt eine Liste benutzerdefinierter Mobile Services-APIs an, die Sie für Ihren Mobile Service erstellt haben.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [Optionen][servicename] [API-Name]**

Erstellt eine benutzerdefinierte Mobile Services-API.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Dieser Befehl unterstützt die folgende zusätzliche Option:

**-p** oder **--permissions** &lt;Berechtigung>: Eine durch Komma getrennte Liste mit &lt;Methode>=&lt;Berechtigung>-Paaren.

**mobile api update [Optionen][servicename] [API-Name]**

Dieser Befehl aktualisiert die angegebene benutzerdefinierte Mobile Services-API.

Dieser Befehl unterstützt die folgende zusätzliche Option:

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-p** oder **--permissions** &lt;Berechtigung>: Eine durch Komma getrennte Liste mit &lt;Methode>=&lt;Berechtigung>-Paaren.
+ **-f** oder **--force**: Überschreibt benutzerdefinierte Änderungen an der Metadatendatei für Berechtigungen.

**mobile api delete [Optionen][servicename] [API-Name]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Dieser Befehl löscht die angegebene benutzerdefinierte Mobile Services-API.

###Befehle zum Verwalten der App-Einstellungen Ihrer mobilen Anwendung

**mobile appsetting list [Optionen][servicename]**

Dieser Befehl zeigt die App-Einstellungen der mobilen Anwendung für den angegebenen Dienst an.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [Optionen][servicename] [Name][value]**

Dieser Befehl fügt eine benutzerdefinierte Anwendungseinstellung für Ihren Mobile Service hinzu.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [Optionen][servicename] [Name]**

Dieser Befehl entfernt die angegebene Anwendungseinstellung für Ihren Mobile Service.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [Optionen][servicename] [Name]**

Dieser Befehl entfernt die angegebene Anwendungseinstellung für Ihren Mobile Service.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

##<a name="Manage_tool_local_settings"></a>Verwalten der lokalen Tooleinstellungen

Die lokalen Einstellungen enthalten Ihre Abonnenten-ID und den Standardnamen für Ihr Speicherkonto.

**config list [Optionen]**

Dieser Befehl zeigt die Konfigurationseinstellungen an.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [Optionen] &lt;Name&gt;,&lt;Wert&gt;**

Dieser Befehl ändert eine Konfigurationseinstellung.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

##<a name ="Commands_to_manage_service_bus"></a>Befehle zum Verwalten von Service Bus

Mit diesen Befehlen können Sie Ihr Servicebuskonto verwalten

**sb namespace check [Optionen] &lt;Name>**

Prüft, ob ein Servicebus-Namespace zulässig und verfügbar ist.

**sb namespace create &lt;Name> &lt;Ort>**

Erstellt einen neuen Servicebus-Namespace.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;Name>**

Löscht einen Namespace.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

Listet alle Namespaces auf, die für Ihr Konto erstellt wurden.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

Zeigt eine Liste aller verfügbaren Orte für Namespaces an.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;Name>**

Zeigt Details zu einem bestimmten Namespace an.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;Name>**

Prüft, ob der Namespace verfügbar ist.

##<a name="Commands_to_manage_your_Storage_objects"></a>Befehle zum Verwalten Ihrer Speicherobjekte

###Befehle zum Verwalten Ihrer Speicherkonten

**storage account list [Optionen]**

Dieser Befehl zeigt die Speicherkonten für Ihr Abonnement an.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [Optionen] <name>**

Dieser Befehl zeigt Informationen zum angegebenen Speicherkonto einschließlich des URI und der Kontoeigenschaften an.

**storage account create [Optionen] <name>**

Dieser Befehl erstellt ein Speicherkonto auf Grundlage der angegebenen Optionen.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-e** oder **--label** &lt;Beschriftung>: Die Beschriftung für das Speicherkonto.
+ **-d** oder **--description** &lt;Beschreibung>: Die Beschreibung für das Speicherkonto.
+ **-l** oder **--location** &lt;Name>: Die geografische Region, in der das Speicherkonto erstellt werden soll.
+ **-a** oder **--affinity-group** &lt;Name>:Die Affinitätsgruppe, der das Speicherkonto zugeordnet werden soll.
+ **--geoReplication**: Gibt an, ob Georeplikation aktiviert ist.
+ **--disable-geoReplication**: Gibt an, ob Georeplikation deaktiviert ist.

**storage account set [Optionen] <name>**

Dieser Befehl aktualisiert das angegebene Speicherkonto.

	~$ azure storage account set mybasestorage --geoReplication
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-e** oder **--label** &lt;Beschriftung>: Die Beschriftung für das Speicherkonto.
+ **-d** oder **--description** &lt;Beschreibung>: Die Beschreibung für das Speicherkonto.
+ **-l** oder **--location** &lt;Name>: Die geografische Region, in der das Speicherkonto erstellt werden soll.
+ **--geoReplication**: Gibt an, ob Georeplikation aktiviert ist.
+ **--disable-geoReplication**: Gibt an, ob Georeplikation deaktiviert ist.

**storage account delete [Optionen] <name>**

Dieser Befehl löscht das angegebene Speicherkonto.

Dieser Befehl unterstützt die folgende zusätzliche Option:

**-q** oder **--quiet**: Keine Bestätigungsaufforderungen. Verwenden Sie diese Option in automatisierten Skripts.

###Befehle zum Verwalten Ihrer Speicherkontoschlüssel

**storage account keys list [Optionen] <name>**

Dieser Befehl listet die primären und sekundären Schlüssel für das angegebene Speicherkonto auf.

**storage account keys renew [Optionen] <name>**

###Befehle zum Verwalten Ihres Speichercontainers

**storage container list [Optionen][prefix]**

Dieser Befehl zeigt die Speichercontainerliste für ein angegebenes Speicherkonto an. Das Speicherkonto wird entweder mit der Verbindungszeichenfolge oder mit dem Speicherkontonamen und dem Kontoschlüssel angegeben.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage container show [Optionen][container]** **storage container create [Optionen][container]**

Dieser Befehl erstellt einen Speichercontainer für das angegebene Speicherkonto. Das Speicherkonto wird entweder mit der Verbindungszeichenfolge oder mit dem Speicherkontonamen und dem Kontoschlüssel angegeben.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage container delete [Optionen][container]**

Dieser Befehl löscht den angegebenen Speichercontainer. Das Speicherkonto wird entweder mit der Verbindungszeichenfolge oder mit dem Speicherkontonamen und dem Kontoschlüssel angegeben.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage container set [Optionen][container]**

Dieser Befehl legt die Zugriffssteuerungsliste für den Speichercontainer fest. Das Speicherkonto wird entweder mit der Verbindungszeichenfolge oder mit dem Speicherkontonamen und dem Kontoschlüssel angegeben.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

###Befehle zum Verwalten Ihres Speicher-Blobs

**storage blob list [Optionen][container] [Präfix]**

Dieser Befehl gibt eine Liste der Speicher-Blobs im angegebenen Speichercontainer zurück.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage blob show [Optionen][container] [Blob]**

Dieser Befehl zeigt die Details des angegebenen Speicher-Blobs an.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-p** oder **-prefix** &lt;Präfix>: Das Präfix des Speichercontainernamens.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage blob delete [Optionen][container] [Blob]**

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-b** oder **--blob** &lt;Blob-Name>: Der Name des zu löschenden Speicher-Blobs.
+ **-q** oder **--quiet**: Entfernt das angegebene Speicher-Blob ohne Bestätigung.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage blob upload [Optionen][file] [Container][blob]**

Dieser Befehl lädt die angegebene Datei in das angegebene Speicher-Blob hoch.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-b** oder **--blob** &lt;Blob-Name>: Der Name des zu löschenden Speicher-Blobs.
+ **-t** oder **--blobtype** &lt;Blob-Typ>: Der Speicher-Blob-Typ: Seite oder Block.
+ **-p** oder **--properties** &lt;Eigenschaften>: Die Speicher-Blob-Eigenschaften für die hochgeladene Datei. Bei Eigenschaften handelt es sich um Schlüssel=Wert-Paare, die durch ein Semikolon (;) voneinander getrennt sind. Die verfügbaren Eigenschaften sind contentType, contentEncoding, contentLanguage und cacheControl.
+ **-m** oder **--metadata** &lt;Metadaten>: Die Speicher-Blob-Metadaten für die hochgeladene Datei. Bei Metadaten handelt es sich um Schlüssel=Wert-Paare, die durch ein Semikolon (;) voneinander getrennt sind.
+ **--concurrenttaskcount** &lt;Anzahl_gleichzeitiger_Aufgaben>: Die maximale Anzahl gleichzeitiger Uploadanforderungen.
+ **-q** oder **--quiet**: Überschreibt das angegebene Speicher-Blob ohne Bestätigung.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

**storage blob download [Optionen][container] [Blob][destination]**

Dieser Befehl aktualisiert das angegebene Speicher-Blob.

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

+ **--container** &lt;Container>: Der Name des zu erstellenden Speichercontainers.
+ **-b** oder **--blob** &lt;Blob-Name>: Der Speicher-Blob-Name.
+ **-d** oder **--destination** [Ziel]: Die Zieldatei oder der Verzeichnispfad für den Download.
+ **-m** oder **--checkmd5**: Die md5-Prüfsumme für die heruntergeladene Datei.
+ **--concurrenttaskcount** &lt;Anzahl_gleichzeitiger_Aufgaben>: Die maximale Anzahl gleichzeitiger Uploadanforderungen.
+ **-q** oder **--quiet**: Überschreibt die Zieldatei ohne Bestätigung.
+ **-a** oder **--account-name** &lt;Kontoname>: Der Speicherkontoname.
+ **-k** oder **--account-key** &lt;Kontoschlüssel>: Der Speicherkontoschlüssel.
+ **-c** oder **--connection-string** &lt;Verbindungszeichenfolge>: Die Speicherverbindungszeichenfolge.
+ **--debug**: Führt den Speicherbefehl im Debugmodus aus.

##<a name ="Commands_to_manage_sql"></a>Befehle zum Verwalten von SQL-Datenbanken

Mit diesen Befehlen können Sie Ihre Azure SQL-Datenbanken verwalten

###Befehle zum Verwalten von SQL-Servern

Mit diesen Befehlen können Sie Ihre SQL-Server verwalten

**sql server create &lt;Administratoranmeldung> &lt;Administratorkennwort> &lt;Ort>**

Erstellt einen neuen Datenbankserver

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;Name>**

Zeigt Serverdetails an.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

Ruft die Serverliste ab.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;Name>**

Löscht einen Server

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Befehle zum Verwalten von SQL-Datenbanken

Mit diesen Befehlen können Sie Ihre SQL-Datenbanken verwalten.

**sql db create [Optionen] &lt;Servername> &lt;Datenbankname> &lt;Administratorkennwort>**

Erstellt eine neue Datenbankinstanz

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [Optionen] &lt;Servername> &lt;Datenbankname> &lt;Administratorkennwort>**

Zeigt Datenbankdetails an.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [Optionen] &lt;Servername> &lt;Administratorkennwort>**

Listet die Datenbanken auf.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [Optionen] &lt;Servername> &lt;Datenbankname> &lt;Administratorkennwort>**

Löscht eine Datenbank.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###Befehle zum Verwalten der Firewallregeln für Ihren SQL Server

Mit diesen Befehlen können Sie die Firewallregeln für Ihren SQL Server verwalten

**sql firewallrule create [Optionen] &lt;Servername> &lt;Regelname> &lt;Start-IP-Adresse> &lt;End-IP-Adresse>**

Erstellt eine neue Firewallregel für einen SQL Server.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [Optionen] &lt;Servername> &lt;Regelname>**

Zeigt Details einer Firewallregel an.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [Optionen] &lt;Servername>**

Listet die Firewallregeln auf.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [Optionen] &lt;Servername> &lt;Regelname>**

Dieser Befehl löscht eine Firewallregel.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

##<a name ="Commands_to_manage_vnet"></a>Befehle zum Verwalten Ihrer virtuellen Netzwerke

Mit diesen Befehlen können Sie Ihre virtuellen Netzwerke verwalten

**network vnet create [Optionen] &lt;Ort>**

Erstellt ein neues virtuelles Netzwerk.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;Name>**

Zeigt die Details eines virtuellen Netzwerks an.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**vnet list**

Listet alle virtuellen Netzwerke auf.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;Name>**

Löscht das angegebene virtuelle Netzwerk.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [Dateipfad]**

Mit diesem Befehl können Sie Ihre Netzwerkkonfiguration lokal exportieren. Die exportierte Netzwerkkonfiguration enthält DNS-Server-Einstellungen, Einstellungen für virtuelle Netzwerke, lokale Netzwerkstandorteinstellungen und andere Einstellungen.

**network import [Dateipfad]**

Importiert eine lokale Netzwerkkonfiguration.

**network dnsserver register [Optionen] &lt;DNS-IP>**

Registriert einen DNS-Server, den Sie zur Namensauflösung in Ihrer Netzwerkkonfiguration verwenden möchten.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

Listet alle in Ihrer Netzwerkkonfiguration registrierten DNS-Server auf.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [Optionen] &lt;DNS-IP>**

Löscht einen DNS-Servereintrag aus der Netzwerkkonfiguration.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=58-->