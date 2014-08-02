<properties linkid="manage-linux-other-resources-command-line-tools" urlDisplayName="Command-Line Tools" pageTitle="Azure Command-Line Tools for Mac and Linux" metaKeywords="Azure command-line, Azure tools Mac, Azure tools Linux" description="Learn about using the command-line tool for Mac and Linux in Azure." metaCanonical="" services="web-sites,virtual-machines,mobile-services,cloud-services" documentationCenter="" title="" authors="larryfr" solutions="" manager="" editor="" />

Azure-Befehlszeilentool für Mac und Linux
=========================================

Mit den Funktionen dieses Werkzeugs können Sie virtuelle Computer, Websites und Azure Mobile Services auf Mac- und Linux-Desktoprechnern erstellen, bereitstellen und verwalten. Die Funktionen ähneln denen der Windows PowerShell-Cmdlets, die mit den Azure SDKs für .NET, Node.JS und PHP installiert werden.

Um das Tool auf einem Mac zu installieren, laden Sie den [Azure SDK-Installer](http://go.microsoft.com/fwlink/?LinkId=252249) herunter und führen Sie ihn aus.

Für die Installation unter Linux benötigen Sie zunächst die neueste Version von Node.JS und können anschließend NPM verwenden:

    npm install azure-cli -g

Optionale Parameter werden in eckigen Klammern angezeigt (z. B. [Parameter]). Alle anderen Parameter müssen angegeben werden.

Neben den hier dokumentierten befehlsspezifischen optionalen Parametern gibt es drei weitere optionale Parameter für die Anzeige detaillierter Ausgaben wie z. B. Anforderungsoptionen und Statuscodes. Der Parameter -v bietet ausführliche Ausgaben, und der Parameter -vv bietet noch detailliertere ausführliche Ausgaben. Mit der Option --json erfolgt die Ausgabe im reinen JSON-Format.

**Inhaltsverzeichnis:**

-   [Verwalten Ihrer Kontoinformationen und Veröffentlichen von Einstellungen](#Manage_your_account_information_and_publish_settings)
-   [Befehle zum Verwalten Ihrer virtuellen Azure-Computer](#Commands_to_manage_your_Azure_virtual_machines)
-   [Befehle zum Verwalten der Endpunkte Ihrer virtuellen Azure-Computer](#Commands_to_manage_your_Azure_virtual_machine_endpoints)
-   [Befehle zum Verwalten der Images Ihrer virtuellen Azure-Computer](#Commands_to_manage_your_Azure_virtual_machine_images)
-   [Befehle zum Verwalten der Datenlaufwerke Ihrer virtuellen Azure-Computer](#Commands_to_manage_your_Azure_virtual_machine_data_disks)
-   [Befehle zum Verwalten Ihrer Azure-Cloud-Dienste](#Commands_to_manage_your_Azure_cloud_services)
-   [Befehle zum Verwalten Ihrer Azure-Zertifikate](#Commands_to_manage_your_Azure_certificates)
-   [Befehle zum Verwalten Ihrer Websites](#Commands_to_manage_your_web_sites)
-   [Befehle zum Verwalten Ihrer Azure Mobile Services](#Commands_to_manage_mobile_services)
-   [Verwalten der lokalen Tooleinstellungen](#Manage_tool_local_settings)
-   [Befehle zum Verwalten des Servicebus](#Commands_to_manage_service_bus)
-   [Befehle zum Verwalten von SQL-Datenbanken](#Commands_to_manage_sql)
-   [Befehle zum Verwalten Ihrer virtuellen Netzwerke](#Commands_to_manage_vnet)

Verwalten Ihrer Kontoinformationen und Veröffentlichen von Einstellungen
------------------------------------------------------------------------

Das Tool verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen im Azure-Portal in einer Einstellungsveröffentlichungsdatei, wie hier beschrieben. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird vom Tool in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

**account download [Optionen]**

Dieser Befehl startet einen Browser und lädt Ihre .publishsettings-Datei aus dem Azure-Portal herunter.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**account import [Optionen] &lt;Datei\>**

Dieser Befehl importiert eine .publishsettings-Datei oder ein Zertifikat für den zukünftigen Gebrauch durch das Tool.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

**Hinweis**

.publishsettings-Dateien können Details (Abonnementname und -ID) für mehr als ein Abonnement enthalten. Beim Importieren der .publishsettings-Datei wird das erste Abonnement als Standard-Abonnement verwendet. Führen Sie den folgenden Befehl aus, um ein anderes Abonnement zu verwenden.

`~$ azure config set subscription <Abonnement-ID>`

**account clear [Optionen]**

Dieser Befehl löscht die zuvor importierten Veröffentlichungseinstellungen. Verwenden Sie diesen Befehl, wenn Sie das Tool auf einem Computer nicht mehr benötigen und sicherstellen möchten, dass dieses zukünftig nicht mit Ihrem Benutzerkonto verwendet werden kann.

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

**account set [Optionen] &lt;Abonnement\>**

Setzt das aktuelle Abonnement

### Befehle zum Verwalten Ihrer Affinitätsgruppen

**account affinity-group list [Optionen]**

Dieser Befehl listet Ihre Azure-Affinitätsgruppen auf.

Affinitätsgruppe können konfiguriert werden, wenn eine Gruppe von virtuellen Computern mehrere physische Computer umfasst. Die Affinitätsgruppe gibt an, dass die physischen Computer einander möglichst nahe sein sollten, um die Netzwerklatenz zu minimieren.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**account affinity-group create [Optionen] &lt;Name\>**

Dieser Befehl erstellt eine neue Affinitätsgruppe

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**account affinity-group show [Optionen] &lt;Name\>**

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

**account affinity-group delete [Optionen] &lt;Name\>**

Dieser Befehl löscht die angegebene Affinitätsgruppe

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

### Befehle zum Verwalten Ihrer Kontoumgebung

**account env list [Optionen]**

Liste der Kontoumgebungen

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**account env show [Optionen] [Umgebung]**

Zeigt Details der Kontoumgebung an

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/ LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/ LinkId=2544
    info:    account env show command OK

**account env add [Optionen] [Umgebung]**

Dieser Befehl fügt eine Umgebung zum Konto hinzu

**account env set [Optionen] [Umgebung]**

Dieser Befehl setzt die Kontoumgebung

**account env delete [Optionen] [Umgebung]**

Dieser Befehl löscht die angegebene Umgebung vom Konto

Befehle zum Verwalten Ihrer virtuellen Azure-Computer
-----------------------------------------------------

Das folgende Diagramm zeigt, wie virtuelle Azure-Computer in der Produktionsumgebung eines Azure-Cloud-Dienstes gehostet werden.

![Technisches Diagramm Azure](./media/command-line-tools/architecturediagram.jpg)

**create-new** erstellt das Laufwerk im Blobspeicher (E:\\ in diesem Diagramm); **attach** hängt ein bereits erstelltes, aber noch nicht angehängtes Laufwerk an einen virtuellen Computer an.

**vm create [Optionen] &lt;DNS-Name\> &lt;Image\> &lt;Benutzername\> [Passwort]**

Dieser Befehl erstellt einen neuen virtuellen Azure-Computer. Standardmäßig wird jeder virtuelle Computer in einem eigenen Cloud-Dienst erstellt. Sie können jedoch mit der Option -c angeben, dass ein virtueller Computer zu einem existierenden Cloud-Dienst hinzugefügt werden soll.

Beachten Sie, dass der Befehl "vm create", ebenso wie das Azure-Portal, nur virtuelle Computer in der Produktionsumgebung erstellt. Momentan existiert keine Option zum Erstellen eines virtuellen Computers in der Stagingumgebung eines Cloud-Dienstes. Beachten Sie, dass mit diesem Befehl ein Azure-Speicherkonto erstellt wird, falls Ihr Abonnement noch kein solches enthält.

Sie können den Parameter --location für die Angabe eines Ortes verwenden und den Parameter --affinity-group für die Angabe einer Affinitätsgruppe. Falls Sie keinen dieser Parameter verwenden, müssen Sie einen Ort aus einer Liste auswählen.

Das angegebene Passwort muss 8-123 Zeichen lang sein und die Komplexitätsanforderungen des Betriebssystems erfüllen, das Sie auf dem entsprechenden virtuellen Computer verwenden.

Falls Sie SSH für die Verwaltung virtueller Linux-Computer benötigen (dies ist häufig der Fall), müssen Sie SSH bei der Erstellung des virtuellen Computers über die Option -e aktivieren. SSH kann nach der Erstellung des virtuellen Computers nicht mehr aktiviert werden.

Virtuelle Windows-Computer können RDP später aktivieren, indem Port 3389 als Endpunkt hinzugefügt wird.

Der Befehl unterstützt die folgenden optionalen Parameter:

**-c, --connect** Erstellt den virtuellen Computer in einer bereits erstellten Bereitstellung in einem Hostingdienst. Bei der Verwendung dieser Option ohne -vmname wird der name des neuen virtuellen Computers automatisch generiert.

 **-n, --vm-name** Gibt den Namen des virtuellen Computers an. Dieser Parameter nimmt standardmäßig den Namen des Hostingdienstes entgegen. Ohne Angabe von -vmname wird der Name des neuen virtuellen Computers generiert als &lt;Dienstname\>&lt;ID\>, wobei &lt;ID\> die Anzahl existierender virtueller Computer im Dienst plus 1 ist. Wenn Sie mit diesem Befehl z. B. einen neuen virtuellen Computer zu einem Hostingdienst MyService hinzufügen, der bereits einen virtuellen Computer enthält, dann erhält der neue virtuelle Computer den Namen MyService2.

 **-u, --blob-url** Gibt die URL des Blobspeichers an, in dem das Systemlaufwerk des virtuellen Computers erstellt werden soll.

 **-z, --vm-size** Gibt die Größe des virtuellen Computers an. Gültige Werte sind "extrasmall", "small", "medium", "large", "extralarge". Der Standardwert ist "small".

 **-r** Fügt RDP-Konnektivität zu einem virtuellen Windows-Computer hinzu.

 **-e, --ssh** Fügt SSH-Konnektivität zu einem virtuellen Linux-Computer hinzu.

 **-t, --ssh-cert** Gibt das SSH-Zertifikat an.

 **-s** Das Abonnement

 **-o, --community** Beim angegebenen Image handelt es sich um ein Gemeinschaftsimage

 **-w** Der Name des virtuellen Netzwerks

 **-l, --location** Gibt den Ort an (z. B. "North Central US").

 **-a, --affinity-group** Gibt die Affinitätsgruppe an.

 **-w, --virtual-network-name** Gibt an, zu welchem virtuellen Netzwerk der neue virtuelle Computer hinzugefügt werden soll. Virtuelle Netzwerke können im Azure-Portal eingerichtet und verwaltet werden.

 **-b, --subnet-names** Gibt die Subnetznamen an, die dem virtuellen Computer zugewiesen werden sollen.

In diesem Beispiel wird das Image MSFT\_\_Win2K8R2SP1-120514-1520-141205-01-en-us-30GB von der Plattform bereitgestellt. Weitere Informationen zu Betriebssystem-Images finden Sie in der VM-Imageliste.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "Western US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************                                     
    info:   vm create command OK

**vm create-from &lt;DNS-Name\> &lt;Rollendatei\>**

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

**vm show [Optionen] &lt;Name\>**

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

**vm delete [Optionen] &lt;Name\>**

Dieser Befehl löscht einen virtuellen Azure-Computer. Standardmäßig löscht dieser Befehl nicht den Azure-Blob, aus dem Betriebssystem- und Datenlaufwerk erstellt wurden. Verwenden Sie die Option -b, um den Blob gemeinsam mit dem virtuellen Computer zu löschen.

    ~$ azure vm delete my-vm 
    info:   Executing command vm delete
    info:   vm delete command OK

**vm start [Optionen] &lt;Name\>**

Dieser Befehl startet einen virtuellen Azure-Computer.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**vm restart [Optionen] &lt;Name\>**

Dieser Befehl startet einen virtuellen Azure-Computer neu.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**vm shutdown [Optionen] &lt;Name\>**

Dieser Befehl fährt einen virtuellen Azure-Computer herunter. Mit der Option -p können Sie angeben, dass die Ressource beim Herunterfahren nicht freigegeben werden soll.

```
~$ azure vm shutdown my-vm 
info:   Executing command vm shutdown
info:   vm shutdown command OK`
```

**vm capture &lt;VM-Name\> &lt;Zielimage-Name\>**

Dieser Befehl erfasst ein Image eines virtuellen Azure-Computers.

Images von virtuellen Computern können nur erfasst werden, wenn der virtuelle Computer heruntergefahren ist.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**vm export [Optionen] &lt;VM-Name\> &lt;Dateipfad\>**

Dieser Befehl exportiert ein Image eines virtuellen Azure-Computers in eine Datei

```
~$ azure vm export "myvm" "C:\"
info:    Executing command vm export
+ Getting virtual machines
+ + Exporting the VM
+ info:   vm export command OK
```

Befehle zum Verwalten der Endpunkte Ihrer virtuellen Azure-Computer
-------------------------------------------------------------------

Das folgende Diagramm zeigt die Architektur einer typischen Umgebung mit mehreren Instanzen eines virtuellen Computers. Beachten Sie, dass in diesem Beispiel der Port 3389 (RDP-Zugriff) auf allen virtuellen Computern geöffnet ist. Außerdem hat jeder virtuelle Computer eine interne IP-Adresse (z. B. 168.55.11.1), die vom Lastenausgleichsmodul für die Zuweisung des Netzwerkverkehrs an die einzelnen virtuellen Computer verwendet wird. Diese interne IP-Adresse kann auch für die Kommunikation zwischen virtuellen Computern verwendet werden.

![azurenetworkdiagram](./media/command-line-tools/networkdiagram.jpg)

Externe Anforderungen an virtuelle Computer durchlaufen ein Lastenausgleichsmodul. Daher ist es in Umgebungen mit mehreren virtuellen Computern nicht möglich, Anforderungen an einen speziellen virtuellen Computer zu schicken. In Umgebungen mit mehreren virtuellen Computern müssen Sie daher Portzuordnungen zwischen den virtuellen Computern (VM-Port) und dem Lastenausgleichsmodul (LB-Port) konfigurieren.

**vm endpoint create &lt;VM-Name\> &lt;LB-Port\> [VM-Port]**

Dieser Befehl erstellt einen Endpunkt für einen virtuellen Azure-Computer. Mit -u oder --enable-direct-server-return können Sie angeben, ob dieser Endpunkt Direct Server Return verwenden soll. Diese Option ist standardmäßig deaktiviert.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm endpoint create-multiple [Optionen] &lt;VM-Name\> &lt;LB-Port\>[:&lt;VM-Port\>[:&lt;Protokoll\>[:&lt;lb-set-name\>[:&lt;prob-protocol\>:&lt;lb-prob-port\>[:&lt;prob-path\>]]]]] ]{1-\*}**

Erstellt mehrere Endpunkte für virtuelle Computer. Mit -u oder --enable-direct-server-return können Sie angeben, ob dieser Endpunkt Direct Server Return verwenden soll. Diese Option ist standardmäßig deaktiviert.

**vm endpoint delete &lt;VM-Name\> &lt;LB-Port\>**

Dieser Befehl löscht einen Endpunkt eines virtuellen Azure-Computers.

    ~$ azure vm endpoint delete my-vm 8888
    azure vm endpoint delete my-vm 8888
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**vm endpoint list &lt;VM-Name\>**

Dieser Befehl listet alle Endpunkte eines virtuellen Azure-Computers auf. Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port                                        
    data:   ----  -------------  ----------
    data:   ssh   22             22

**vm endpoint update [Optionen] &lt;VM-Name\> &lt;Endpunktname\>**

Dieser Befehl aktualisiert einen Endpunkt eines virtuellen Computers mit den angegebenen Optionen.

    -n, --endpoint-name <Name>           neuer Name für den Endpunkt
    -t, --lb-port <Port>                 neuer Port für das Lastenausgleichsmodul
    -t, --vm-port <Port>                 neuer lokaler Port
    -o, --endpoint-protocol <Protokoll>  neues Transportebenenprotokoll für den Port (TCP oder UDP) 

**vm endpoint show [Optionen] &lt;VM-Name\>**

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

Befehle zum Verwalten der Images Ihrer virtuellen Azure-Computer
----------------------------------------------------------------

Images von virtuellen Computern sind Abbildungen bereits konfigurierter virtueller Computer und können bei Bedarf repliziert werden.

**vm image list [Optionen]**

Dieser Befehl listet Images von virtuellen Computern auf. Insgesamt existieren drei Arten von Images: von Microsoft erstellte Images mit dem Präfix "MSFT", Images von Drittanbietern, normalerweise mit dem Namen des Herstellers als Präfix, und benutzerdefinierte Images. Um ein Image zu erstellen, können Sie entweder einen existierenden virtuellen Computer erfassen oder ein Image aus einer benutzerdefinierten .vhd-Datei erstellen, die Sie in einen Blobspeicher hochgeladen haben. Weitere Informationen zu benutzerdefinierten .vhd-Dateien finden Sie im Abschnitt zur Erstellung von Images für virtuelle Computer. 
Mit der Option --json erfolgt die Ausgabe der Ergebnisse im reinen JSON-Format.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK   

**vm image show [Optionen] &lt;Name\>**

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

**vm image delete [Optionen] &lt;Name\>**

Dieser Befehl löscht ein Image eines virtuellen Computers.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image                                         
    info:   vm image delete command OK

**vm image create &lt;Name\> [Quellpfad]**

Dieser Befehl erstellt ein Image eines virtuellen Computers. Ihre benutzerdefinierten .vhd-Dateien werden in einen Blobspeicher hochgeladen, und das Image wird anschließend von dort aus erstellt. Anschließend können Sie das Image verwenden, um einen virtuellen Computer zu erstellen. Die Parameter für Ort und Betriebssystem müssen angegeben werden.

Manche Systeme haben Grenzen für Dateideskriptoren pro Prozess. Wenn diese Grenze überschritten wird, zeigt das Tool einen entsprechenden Fehler an. Sie können den Befehl erneut ausführen und mit dem Parameter -p &lt;Anzahl\> die Anzahl der parallelen Uploads begrenzen. Der Standardwert für parallele Uploads ist 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

Befehle zum Verwalten der Datenlaufwerke Ihrer virtuellen Azure-Computer
------------------------------------------------------------------------

Datenlaufwerke sind .vhd-Dateien im Blobspeicher, die von virtuellen Computern genutzt werden können. Weitere Informationen zur Bereitstellung von Datenlaufwerken im Blobspeicher finden Sie im bereits gezeigten technischen Azure-Diagramm.

Die Befehle zum Anhängen von Datenlaufwerken (azure vm disk attach und azure vm disk attach-new) weisen dem angehängten Datenlaufwerk eine logische Gerätenummer (Logical Unit Number LUN) zu, wie vom SCSI-Protokoll gefordert. Das erste Datenlaufwerk eines virtuellen Computers erhält die Nummer LUN 0, das nächste Datenlaufwerk die Nummer LUN 1 usw.

Wenn Sie ein Datenlaufwerk mit dem Befehl azure vm disk detach trennen, können Sie mit dem Parameter &lt;lun\> angeben, welches Laufwerk getrennt werden soll.

**Hinweis**

Die Datenlaufwerke sollten stets in umgekehrter Reihenfolge getrennt werden, beginnend mit der höchsten zugewiesenen LUN. Die SCSI-Ebene von Linux unterstützt keine Trennung von niedrigeren LUNs, solange noch eine höhere LUN angehängt ist. Sie sollten z. B. LUN 0 nicht trennen, solange LUN 1 noch angehängt ist.

**vm disk show [Optionen] &lt;Name\>**

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
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**vm disk list [Optionen] &lt;VM-Name\>**

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

**vm disk delete [Optionen] &lt;Name\>**

Dieser Befehl löscht einen Azure-Laufwerk aus einem persönlichen Repository. Das Laufwerk muss vor dem Löschen vom virtuellen Computer getrennt werden.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052                  
    info:   vm disk delete command OK

**vm disk create &lt;Name\> [Quellpfad]**

Dieser Befehl lädt ein Azure-Laufwerk hoch und registriert das Laufwerk. --blob-url, --location oder --affinity-group muss angegeben werden. Wenn Sie diesen Befehl mit [Quellpfad] verwenden, wird die angegebene .vhd-Datei hochgeladen und ein neues Image wird erstellt. Sie können das Image anschließend mit dem Befehl vm disk attach an einen virtuellen Computer anhängen.

Manche Systeme haben Grenzen für Dateideskriptoren pro Prozess. Wenn diese Grenze überschritten wird, zeigt das Tool einen entsprechenden Fehler an. Sie können den Befehl erneut ausführen und mit dem Parameter -p &lt;Anzahl\> die Anzahl der parallelen Uploads begrenzen. Der Standardwert für parallele Uploads ist 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "Western US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB                                                       
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s 
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**vm disk upload [Optionen] &lt;Quellpfad\> &lt;Blob-URL\> &lt;Speicherkonto-Schlüssel\>**

Mit diesem Befehl können Sie ein Laufwerk eines virtuellen Computers hochladen

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload                                                      
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**vm disk attach &lt;VM-Name\> &lt;Laufwerksimage-Name\>**

Dieser Befehl hängt ein existierendes Laufwerk aus dem Blobspeicher an einen existierenden virtuellen Computer in einem Cloud-Dienst an.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**vm disk attach-new &lt;VM-Name\> &lt;Größe-in-GB\> [Blob-URL]**

Dieser Befehl hängt ein Datenlaufwerk an einen virtuellen Azure-Computer an. In diesem Beispiel ist 20 die Größe des neuen anzuhängenden Laufwerks in Gigabyte. Sie können optional als letzten Parameter eine Blob-URL anfügen, um den Ziel-Blob explizit anzugeben. Wenn Sie keine Blob-URL angeben, wird ein Blob-Objekt automatisch generiert.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**vm disk detach &lt;VM-Name\> &lt;LUN\>**

Dieser Befehl trennt ein Datenlaufwerk von einem virtuellen Azure-Computer. &lt;LUN\> identifiziert das zu trennende Laufwerk. Mit dem Befehl vm disk-list &lt;VM-Name\> können Sie vor dem Trennen eine Liste der angehängten Laufwerke abrufen.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

Befehle zum Verwalten Ihrer Azure-Cloud-Dienste
-----------------------------------------------

Azure-Cloud-Dienste sind Anwendungen und Dienste, die in Web- und Workerrollen gehostet sind. Mit den folgenden Befehlen können Sie Azure-Cloud-Dienste verwalten.

**service create [Optionen] &lt;Dienstname\>**

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

**service show [Optionen] &lt;Dienstname\>**

Dieser Befehl zeigt die Details eines Azure-Cloud-Dienstes an

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

**service delete [Optionen] &lt;Name\>**

Dieser Befehl löscht einen Azure-Cloud-Dienst.

    ~$ azure cloud-service delete myservice
    info:   Executing command cloud-service delete myservice 
    info:   cloud-service delete command OK

Befehle zum Verwalten Ihrer Azure-Zertifikate
---------------------------------------------

Azure-Zertifikate sind Zertifikate (in diesem Fall SSL-Zertifikate), die mit Ihrem Azure-Konto verknüpft sind.

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

**service cert create &lt;DNS-Präfix\> &lt;Datei\> [Passwort]**

Dieser Befehl lädt ein Azure-Zertifikat hoch. Lassen Sie das Passwort leer für Zertifikate, die nicht mit Passwort geschützt sind.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password: 
    + Creating certificate                                                         
    info:   service cert create command OK

**service cert delete [Optionen] &lt;Fingerabdruck\>**

Dieser Befehl löscht ein Azure-Zertifikat.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate                                                         
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

Befehle zum Verwalten Ihrer Websites
------------------------------------

Eine Azure-Website ist eine Webkonfiguration, die per URI erreichbar ist. Website werden in virtuellen Computern gehostet, aber Sie müssen sich keine Gedanken über Erstellung und Bereitstellung des virtuellen Computers machen. Azure kümmert sich um diese Details.

**site list [Optionen]**

Dieser Befehl listet Ihre Websites auf.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names                                        
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net     
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net     
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**site set [Optionen] [Name]**

Dieser Befehl setzt Konfigurationsoptionen für Ihre Website [Name]

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

**site create [Optionen] [Name]**

Dieser Befehl erstellt eine neue Website und ein neues lokales Verzeichnis.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

**Hinweis**

Der Name der Website muss eindeutig sein. Sie können keine Website mit dem DNS-Namen einer existierenden Website erstellen.

**site portal [Optionen] [Name]**

Dieser Befehl öffnet das Portal in einem Browser, sodass Sie Ihre Websites verwalten können.

    ~$ azure site portal mysite
    info:   Executing command site portal
    info:   Launching browser to https://windows.azure.net/#Workspaces/WebsiteExtension/Website/mysite/dashboard
    info:   site portal command OK

**site browse [Optionen] [Name]**

Dieser Befehl öffnet Ihre Website in einem Browser.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**site show [Optionen] [Name]**

Dieser Befehl zeigt Details zu einer Website an.

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

**site delete [Optionen] [Name]**

Dieser Befehl löscht eine Website.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

**site start [Optionen] [Name]**

Dieser Befehl startet eine Website.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**site stop [Optionen] [Name]**

Dieser Befehl hält eine Website an.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**site location list [Optionen]**

Dieser Befehl listet Ihre Website-Orte auf

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

### Befehle zum Verwalten Ihrer Website-Anwendungseinstellungen

**site appsetting list [Optionen] [Name]**

Dieser Befehl listet die Anwendungseinstellungen einer Website auf

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting add [Optionen] &lt;Schlüssel-Wert-Paar\> [Name]**

Dieser Befehl fügt Ihrer Website eine Anwendungseinstellung in Form eines Schlüssel-Wert-Paars hinzu

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting delete [Optionen] &lt;Schlüssel\> [Name]**

Dieser Befehl löscht die angegebene Anwendungseinstellung von der Website

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting show [Optionen] &lt;Schlüssel\> [Name]**

Dieser Befehl zeigt Details zu der angegebenen Anwendungseinstellung an

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

### Befehle zum Verwalten Ihrer Website-Zertifikate

**site cert list [Optionen] [Name]**

Dieser Befehl listet Ihre Website-Zertifikate auf

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**site cert add [Optionen] &lt;Zertifikatpfad\> [Name]**

**site cert delete [Optionen] &lt;Fingerabdruck\> [Name]**

**site cert show [Optionen] &lt;Fingerabdruck\> [Name]**

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

### Befehle zum Verwalten Ihrer Website-Verbindungszeichenfolgen

**site connectionstring list [Optionen] [Name]**

**site connectionstring add [Optionen] &lt;Verbindungsname\> &lt;Wert\> &lt;Typ\> [Name]**

**site connectionstring delete [Optionen] &lt;Verbindungsname\> [Name]**

**site connectionstring show [Optionen] &lt;Verbindungsname\> [Name]**

### Befehle zum Verwalten der Standarddokumente Ihrer Website

**site defaultdocument list [Optionen] [Name]**

**site defaultdocument add [Optionen] &lt;Dokument\> [Name]**

**site defaultdocument delete [Optionen] &lt;Dokument\> [Name]**

### Befehle zum Verwalten Ihrer Website-Bereitstellungen

**site deployment list [Optionen] [Name]**

**site deployment show [Optionen] &lt;Commit-ID\> [Name]**

**site deployment redeploy [Optionen] &lt;Commit-ID\> [Name]**

**site deployment github [Optionen] [Name]**

**site deployment user set [Optionen] [Benutzername] [Passwort]**

### Befehle zum Verwalten Ihrer Website-Domänen

**site domain list [Optionen] [Name]**

**site domain add [Optionen] &lt;DN\> [Name]**

**site domain delete [Optionen] &lt;DN\> [Name]**

### Befehle zum Verwalten Ihrer Website-Handlerzuordnungen

**site handler list [Optionen] [Name]**

**site handler add [Optionen] &lt;Erweiterung\> &lt;Prozessor\> [Name]**

**site handler delete [Optionen] &lt;Erweiterung\> [Name]**

### Befehle zum Verwalten Ihrer Website-Diagnose

**site log download [Optionen] [Name]**

Lädt eine .zip-Datei mit Ihrer Website-Diagnose herunter

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**site log tail [Optionen] [Name]**

Dieser Befehl verbindet Ihre Befehlszeile mit dem Logstreamingdienst

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [Optionen] [Name]**

Dieser Befehl konfiguriert die Diagnoseoptionen

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

### Befehle zum Verwalten Ihrer Website-Repositorys

**site repository branch [Optionen] &lt;Verzweigung\> [Name]**

**site repository delete [Optionen] [Name]**

**site repository sync [Optionen] [Name]**

### Befehle zum Verwalten Ihrer Website-Skalierung

**site scale mode [Optionen] &lt;Modus\> [Name]**

**site scale instances [Optionen] &lt;Instanzen\> [Name]**

Befehle zum Verwalten Ihrer Azure Mobile Services
-------------------------------------------------

Azure Mobile Services vereint verschiedene Azure-Dienste, die Back-End-Funktionalitäten für Ihre Anwendungen bereitstellen. Mobile Services-Befehle sind in die folgenden Kategorien unterteilt:

-   [Befehle zum Verwalten von Mobile Services-Instanzen](#Mobile_Services)
-   [Befehle zum Verwalten der Mobile Services-Konfiguration](#Mobile_Configuration)
-   [Befehle zum Verwalten von Mobile Services-Tabellen](#Mobile_Tables)
-   [Befehle zum Verwalten von Mobile Services-Skripts](#Mobile_Scripts)
-   [Befehle zum Verwalten von geplanten Aufgaben](#Mobile_Jobs)
-   [Befehle zum Skalieren eines Mobile Service](#Mobile_Scale)

Die folgenden Optionen sind für die meisten Mobile Services-Befehle gültig:

-   **-h** oder **--help**: Zeigt hilfreiche Informationen zur Befehlsnutzung an.
-   **-s `<id>`** oder **--subscription `<id>`**: Verwenden eines bestimmten Abonnements, angegeben als `<id>`.
-   **-v** oder **--verbose**: Ausführliche Ausgabe.
-   **--json**: JSON-Ausgabe.

### Befehle zum Verwalten von Mobile Services-Instanzen

**mobile locations [Optionen]**

Dieser Befehl listet die von Mobile Services unterstützten geografischen Standorte auf.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US        
    info:    North Europe

**mobile create [Optionen] [Dienstname] [sqlAdminBenutzername] [sqlAdminPasswort]**

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

-   **-r `<sqlServer>`** oder **--sqlServer `<sqlServer>`**: Verwenden eines existierenden SQL-Datenbankservers, angegeben als `<sqlServer>`.
-   **-d `<sqlDb>`** oder **--sqlDb `<sqlDb>`**: Verwenden einer existierenden SQL-Datenbank, angegeben als `<sqlDb>`.
-   **-l `<Ort>`** oder **--location `<Ort>`**: Erstellt den Dienst an einem bestimmten Ort, angegeben als `<Ort>`. Rufen Sie azure mobile locations auf, um eine Liste der verfügbaren Orte zu erhalten.
-   **--sqlLocation `<Ort>`**: Erstellt den SQL Server an einem bestimmten `<Ort>`. Standardwert ist der Ort des Mobile Service.

**mobile delete [Optionen] [Dienstname]**

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

-   **-d** oder **--deleteData**: Löscht alle Daten aus diesem Mobile Service aus der Datenbank.
-   **-a** oder **--deleteAll**: Löscht die SQL-Datenbank und den Server.
-   **-q oder \*\*--quiet**: Keine Bestätigungsaufforderungen. Verwenden Sie diese Option in automatisierten Skripts.

**mobile list [Optionen]**

Dieser Befehl listet Ihre Mobile Services auf.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**mobile show [Optionen] [Dienstname]**

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

**mobile restart [Optionen] [Dienstname]**

Dieser Befehl startet eine Mobile Services-Instanz neu.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**mobile log [Optionen] [Dienstname]**

Dieser Befehl gibt die Mobile Service-Protokolle zurück und filtert alle Protokolltypen mit Ausnahme von `Fehler` heraus.

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

-   **-r `<Abfrage>`** oder **--query `<Abfrage>`**: Führt die angegebene Protokollabfrage aus.
-   **-t `<Typ>`** oder **--type `<Typ>`**: Filtert die Protokolle nach Eintrags-`<Typ>`. Mögliche Typen sind `Information`, `Warnung` oder `Fehler`.
-   **-k `<überspringen>`** oder **--skip `<überspringen>`**: Überspringt die in `<überspringen>` angegebene Anzahl an Zeilen.
-   **-p `<Top>`** oder **--top `<Top>`**: Gibt die in `<Top>` angegebene Anzahl an Zeilen zurück.

**Hinweis**

Der Parameter \*\*--query\*\* hat Vorrang vor \*\*--type\*\*, \*\*--skip\*\* und \*\*--top\*\*.

**mobile key regenerate [Optionen] [Dienstname] [Typ]**

Dieser Befehl generiert den Mobile Service-Anwendungsschlüssel neu.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Mögliche Schlüsseltypen sind `master` und `application`.

**Hinweis**

Wenn Sie einen Schlüssel neu generieren, können Clients, die den alten Schlüssel verwenden, möglicherweise nicht mehr auf Ihren Mobile Service zugreifen. Wenn Sie den Anwendungsschlüssel neu generieren, sollten Sie Ihre Anwendung mit dem neuen Schlüsselwert aktualisieren.

### Befehle zum Verwalten der Mobile Services-Konfiguration

**mobile config list [Optionen] [Dienstname]**

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

**mobile config get [Optionen] [Dienstname] [Schlüssel]**

Dieser Befehl ruft eine bestimmte Konfigurationsoption für einen Mobile Service ab, in diesem Fall das dynamische Schema.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**mobile config set [Optionen] [Dienstname] [Schlüssel] [Wert]**

Dieser Befehl ändert den Wert einer bestimmten Konfigurationsoption für einen Mobile Service, in diesem Fall das dynamische Schema.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK

### Befehle zum Verwalten von Mobile Services-Tabellen

**mobile table list [Optionen] [Dienstname]**

Dieser Befehl listet alle Tabellen in Ihrem Mobile Service auf.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**mobile table show [Optionen] [Dienstname] [Tabellenname]**

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

**mobile table create [Optionen] [Dienstname] [Tabellenname]**

Dieser Befehl erstellt eine Tabelle.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Dieser Befehl unterstützt die folgende zusätzliche Option:

-   **-p `<Berechtigungen>`** oder **--permissions `<Berechtigungen>`**: Durch Kommas getrennte Liste von `<Operation>`=`<Berechtigung>`-Paaren, wobei `<Operation>` entweder `insert`, `read`, `update` oder `delete` sein kann und `<Berechtigungen>` entweder `public`, `application` (Standard), `user` oder `admin` sein kann.

**mobile data read [Optionen] [Dienstname] [Tabellenname] [Abfrage]**

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

-   **-k `<überspringen>`** oder **--skip `<überspringen>`**: Überspringt die in `<überspringen>` angegebene Anzahl an Zeilen.
-   **-t `<Top>`** oder **--top `<Top>`**: Gibt die in `<Top>` angegebene Anzahl an Zeilen zurück.
-   **-l** oder **--list**: Gibt Daten in einem Listenformat zurück.

**mobile table update [Optionen] [Dienstname] [Tabellenname]**

Dieser Befehl setzt die Berechtigungen für eine Tabelle ausschließlich für Administratoren.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

-   **-p `<Berechtigungen>`** oder **--permissions `<Berechtigungen>`**: Durch Kommas getrennte Liste von `<Operation>`=`<Berechtigung>`-Paaren, wobei `<Operation>` entweder `insert`, `read`, `update` oder `delete` sein kann und `<Berechtigungen>` entweder `public`, `application` (Standard), `user` oder `admin` sein kann.
-   **--deleteColumn `<Spalten>`**: Durch Kommas getrennte Liste zu löschender Spalten als `<Spalten>`.
-   **-q** oder **--quiet**: Löscht Spalten ohne Bestätigungsaufforderung.
-   **--addIndex `<Spalten>`**: Durch Kommas getrennte Liste von Spalten, die zum Index hinzugefügt werden sollen.
-   **--deleteIndex `<Spalten>`**: Durch Kommas getrennte Liste von Spalten, die aus dem Index entfernt werden sollen.

**mobile table delete [Optionen] [Dienstname] [Tabellenname]**

Dieser Befehl löscht eine Tabelle.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Verwenden Sie den Parameter -q, um die Tabelle ohne Bestätigung zu löschen. Diese Option ist hilfreich für automatisierte Skripts.

**mobile data truncate [Optionen] [Dienstname] [Tabellenname]**

Dieser Befehl löscht alle Zeilen aus der Tabelle.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK

### Befehle zum Verwalten von Skripts

Mit den Befehlen in diesem Abschnitt können Sie die Serverskripts eines Mobile Service verwalten. Weitere Informationen erhalten Sie unter [Verwenden von Serverskripts in Mobile Services.](http://www.windowsazure.com/en-us/develop/mobile/how-to-guides/work-with-server-scripts/).

**mobile script list [Optionen] [Dienstname]**

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

**mobile script upload [Optionen] [Dienstname] [Skriptname]**

Dieser Befehl lädt ein Skript namens `todoitem.insert.js` aus dem Unterverzeichnis `table` hoch.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

Der Name der Datei muss sich aus Tabellen- und Operationsnamen zusammensetzen, und die Datei muss sich im Unterordner Table relativ zum Verzeichnis befinden, in dem der Befehl ausgeführt wird. Mit dem Parameter **-f `<Datei>`** oder **--file `<Datei>`** können Sie einen Dateinamen und Pfad zu der Datei angeben, die das zu registrierende Skript enthält.

**mobile script download [Optionen] [Dienstname] [Skriptname]**

Dieser Befehl lädt das insert-Skript aus der Tabelle TodoItem in eine Datei namens `todoitem.insert.js` im Unterverzeichnis `table` herunter.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

-   **-p `<Pfad>`** oder **--path `<Pfad>`**: Der Ort, an dem die Skriptdatei gespeichert werden soll, relativ zum aktuellen Arbeitsverzeichnis.
-   **-f `<Datei>`** oder **--file `<Datei>`**: Der Name der Datei, in der das Skript gespeichert werden soll.
-   **-o** oder **--override**: Existierende Datei überschreiben.
-   **-c** oder **--console**: Skript in die Konsole anstatt in eine Datei schreiben.

**mobile script delete [Optionen] [Dienstname] [Skriptname]**

Dieser Befehl löscht das existierende insert-Skript aus der Tabelle TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### Befehle zum Verwalten von geplanten Aufgaben

Mit den Befehlen in diesem Abschnitt können Sie geplanten Aufgaben eines Mobile Service verwalten. Weitere Informationen finden Sie unter [Geplante Aufträge](http://msdn.microsoft.com/en-us/library/windowsazure/jj860528.aspx).

**mobile job list [Optionen] [Dienstname]**

Dieser Befehl listet geplante Aufgaben auf.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**mobile job create [Optionen] [Dienstname] [Aufgabenname]**

Dieser Befehl erstellt eine neue, stündlich auszuführende Aufgabe mit dem Namen `getUpdates`.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates 
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

-   **-i `<Nummer>`** oder **--interval `<Nummer>`**: Das Ausführungsintervall als ganze Zahl, der Standardwert ist `15`.
-   **-u `<Einheit>`** oder **--intervalUnit `<Einheit>`**: Die Einheit für das *Intervall*. Dies kann einer der folgenden Werte sein:
    -   **minute** (Standard)
    -   **hour**
    -   **day**
    -   **month**
    -   **none** (bedarfsgesteuerte Aufgaben)
-   **-t `<Zeitpunkt>`** **--startTime `<Zeitpunkt>`** Die Startzeit der ersten Skriptausführung im ISO-Format, der Standardwert ist `now`.

**Hinweis**

Neue Aufgaben werden bei der Erstellung deaktiviert, da zunächst ein Skript hochgeladen werden muss. Mit dem Befehl **mobile script upload** können Sie ein Skript hochladen, und mit dem Befehl **mobile job update** können Sie die Aufgabe aktivieren.

**mobile job update [Optionen] [Dienstname] [Aufgabenname]**

Der folgende Befehl aktiviert die deaktivierte `getUpdates`-Aufgabe.

    ~$azure mobile job update -a enabled todolist getUpdates 
    info:    Executing command mobile job update
    info:    mobile job update command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

-   **-i `<Nummer>`** oder **--interval `<Nummer>`**: Das Ausführungsintervall als ganze Zahl, der Standardwert ist `15`.
-   **-u `<Einheit>`** oder **--intervalUnit `<Einheit>`**: Die Einheit für das *Intervall*. Dies kann einer der folgenden Werte sein:
    -   **minute** (Standard)
    -   **hour**
    -   **day**
    -   **month**
    -   **none** (bedarfsgesteuerte Aufgaben)
-   **-t `<Zeitpunkt>`** **--startTime `<Zeitpunkt>`** Die Startzeit der ersten Skriptausführung im ISO-Format, der Standardwert ist `now`.
-   **-a `<Status>`** oder **--status `<Status>`**: Der Status der Aufgabe, entweder `enabled (aktiviert)` oder `disabled (deaktiviert)`.

**mobile job delete [Optionen] [Dienstname] [Aufgabenname]**

Dieser Befehl löscht die geplante Aufgabe getUpdates vom Server TodoList.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

**Hinweis**

Beim Löschen einer Aufgabe wird auch das hochgeladene Skript gelöscht.

### Befehle zum Skalieren eines Mobile Service

Mit den Befehlen in diesem Abschnitt können Sie einen Mobile Service skalieren. Weitere Informationen finden Sie unter [Scaling a Mobile Service](http://msdn.microsoft.com/en-us/library/windowsazure/jj193178.aspx) (Skalieren von Mobile Services, in englischer Sprache).

**mobile scale show [Optionen] [Dienstname]**

Dieser Befehl zeigt Skalierungsinformationen an, inklusive dem aktuellen Arbeitsmodus und der Anzahl der Instanzen.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**mobile scale change [Optionen] [Dienstname]**

Dieser Befehl ändert den Skalierungsmodus des Mobile Service von Free zu Premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Dieser Befehl unterstützt die folgenden zusätzlichen Optionen:

-   **-c `<Modus>`** oder **--computeMode `<Modus>`**: Der Arbeitsmodus kann entweder `Free` oder `Reserved` sein.
-   **-i `<Anzahl>` oder \*\*--numberOfInstances `<Anzahl>`**: Die Anzahl der Instanzen bei Ausführung im Reserved-Modus.

**Hinweis**

Wenn Sie den Arbeitsmodus Reserved aktivieren, laufen all Ihre Mobile Services in derselben Region im Premiummodus.

Verwalten der lokalen Tooleinstellungen
---------------------------------------

Die lokalen Einstellungen enthalten Ihre Abonnenten-ID und den Standardnamen für Ihr Speicherkonto.

**config list [Optionen]**

Dieser Befehl zeigt die Konfigurationseinstellungen an.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value                               
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**config set [Optionen] &lt;Name\>,&lt;Wert\>**

Dieser Befehl ändert eine Konfigurationseinstellung.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

Befehle zum Verwalten des Servicebus
------------------------------------

Mit diesen Befehlen können Sie Ihr Servicebuskonto verwalten

**sb namespace create &lt;Name\> &lt;Ort\>**

Erstellt einen neuen Servicebus-Namespace

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

**sb namespace show &lt;Name\>**

Zeigt Details zu einem bestimmten Namespace an

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

**sb namespace list**

Listet alle Namespaces auf, die für Ihr Konto erstellt wurden

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK

**sb namespace delete &lt;Name\>**

Löscht einen Namespace

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**sb namespace location list**

Zeigt eine Liste aller verfügbaren Orte für Namespaces an

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

**sb namespace verify &lt;Name\>**

Prüft, ob der Namespace verfügbar ist

Befehle zum Verwalten von SQL-Datenbanken
-----------------------------------------

Mit diesen Befehlen können Sie Ihre Azure SQL-Datenbanken verwalten

### Befehle zum Verwalten von SQL-Servern

Mit diesen Befehlen können Sie Ihre SQL-Server verwalten

**sql server create &lt;Administratorname\> &lt;Administratorpasswort\> &lt;Ort\>**

Erstellt einen neuen Datenbankserver

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**sql server show &lt;Name\>**

Zeigt Serverdetails an

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**sql server list**

Ruft die Serverliste ab

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**sql server delete &lt;Name\>**

Löscht einen Server

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

### Befehle zum Verwalten von SQL-Datenbanken

Mit diesen Befehlen können Sie Ihre SQL-Datenbanken verwalten

**sql db create [Optionen] &lt;Servername\> &lt;Datenbankname\> &lt;Administratorpasswort\>**

Erstellt eine neue Datenbankinstanz

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**sql db show [Optionen] &lt;Servername\> &lt;Datenbankname\> &lt;Administratorpasswort\>**

Zeigt Datenbankdetails an

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

**sql db list [Optionen] &lt;Servername\> &lt;Administratorpasswort\>**

Listet die Datenbanken auf

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**sql db delete [Optionen] &lt;Servername\> &lt;Datenbankname\> &lt;Administratorpasswort\>**

Löscht eine Datenbank

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

### Befehle zum Verwalten der Firewallregeln für Ihren SQL Server

Mit diesen Befehlen können Sie die Firewallregeln für Ihren SQL Server verwalten

**sql firewallrule create [Optionen] &lt;Servername\> &lt;Regelname\> &lt;AnfangsIPAdresse\> &lt;EndIPAdresse\>**

Erstellt eine neue Firewallregel für einen SQL Server

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**sql firewallrule show [Optionen] &lt;Servername\> &lt;Regelname\>**

Zeigt Details einer Firewallregel an

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

**sql firewallrule list [Optionen] &lt;Servername\>**

Listet die Firewallregeln auf

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**sql firewallrule delete [Optionen] &lt;Servername\> &lt;Regelname\>**

Dieser Befehl löscht eine Firewallregel

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

Befehle zum Verwalten Ihrer virtuellen Netzwerke
------------------------------------------------

Mit diesen Befehlen können Sie Ihre virtuellen Netzwerke verwalten

**network vnet create [Optionen] &lt;Ort\>**

Erstellt ein neues virtuelles Netzwerk

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

**network vnet show &lt;Name\>**

Zeigt die Details eines virtuellen Netzwerks an

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

Listet alle virtuellen Netzwerke auf

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

**network vnet show &lt;Name\>**

Zeigt die Details des angegebenen virtuellen Netzwerks an

    ~$ azure network vnet show opentechvn1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "opentechvn1"
    data:    Id "cab41cb0-396a-413b-83a1-302f0f1c867d"
    data:    AffinityGroup "AG-CLI-456f89eaa7fae2b3"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.100.23.255/27"
    data:    Subnets 0 Name "frontend"
    data:    Subnets 0 AddressPrefix "10.100.23.224/29"
    info:    network vnet show command OK

**network vnet delete &lt;Name\>**

Löscht das angegebene virtuelle Netzwerk

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ? (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**network export [Dateipfad]**

Mit diesem Befehl können Sie Ihre Netzwerkkonfiguration lokal exportieren. Die exportierte Netzwerkkonfiguration enthält DNS-Server-Einstellungen, Einstellungen für virtuelle Netzwerke, lokale Netzwerkstandorteinstellungen und andere Einstellungen.

**network import [Dateipfad]**

Importiert eine lokale Netzwerkkonfiguration.

**network dnsserver register [Optionen] &lt;DNS-IP\>**

Registriert einen DNS-Server, den Sie zur Namensauflösung in Ihrer Netzwerkkonfiguration verwenden möchten

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**network dnsserver list**

Listet alle in Ihrer Netzwerkkonfiguration registrierten DNS-Server aus

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**network dnsserver unregister [Optionen] &lt;DNS-IP\>**

Löscht einen DNS-Servereintrag aus der Netzwerkkonfiguration

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK
