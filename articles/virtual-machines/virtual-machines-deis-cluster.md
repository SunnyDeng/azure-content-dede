<properties
   pageTitle="Bereitstellen eines Deis-Clusters mit drei Knoten | Microsoft Azure"
   description="In diesem Artikel wird beschrieben, wie Sie einen Deis-Cluster mit drei Knoten in Azure mit einer Azure-Ressourcen-Manager-Vorlage erstellen."
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# Bereitstellen eines Deis-Clusters mit drei Knoten

Dieser Artikel führt Sie durch die Bereitstellung eines [Deis](http://deis.io/)-Clusters in Azure. Er umfasst alle Schritte von der Erstellung der erforderlichen Zertifikate bis zur Bereitstellung und Skalierung der Beispielanwendung **Go** im neu bereitgestellten Cluster.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen einer Ressource mit dem Ressourcen-Manager-Bereitstellungsmodell.

Das folgende Diagramm zeigt die Architektur des bereitgestellten Systems. Der Systemadministrator verwaltet den Cluster mit Deis-Tools wie **deis** und **deisctl**. Die Verbindungen werden über einen Azure-Load Balancer hergestellt, der die Verbindungen auf einen der Mitgliedsknoten im Cluster weiterleitet. Der Clientzugriff auf bereitgestellte Anwendungen erfolgt ebenfalls über den Load Balancer. In diesem Fall leitet der Load Balancer den Datenverkehr an ein Router-Netz weiter, in dem der Datenverkehr zu entsprechenden im Cluster gehosteten Docker-Containern weitergeleitet wird.

  ![Architekturdiagramm eines bereitgestellten Deis-Clusters](media/virtual-machines-deis-cluster/architecture-overview.png)

Zum Ausführen der folgenden Schritte benötigen Sie Folgendes:

 * Ein aktives Azure-Abonnement. Wenn Sie kein Abonnement besitzen, können Sie eine kostenlose Testversion unter [azure.com](https://azure.microsoft.com) erhalten.
 * Eine Arbeits- oder Schulkonto-ID für die Verwendung von Azure-Ressourcengruppen. Wenn Sie über ein persönliches Konto verfügen, und Sie sich mit einer Microsoft-ID anmelden, müssen Sie [aus der persönlichen ID eine Arbeits-ID erstellen](resource-group-create-work-id-from-personal.md).
 * Je nach Client-Betriebssystem entweder [Azure PowerShell](powershell-install-configure.md) oder [Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows](xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL wird verwendet, um die erforderlichen Zertifikate zu generieren.
 * Ein Git-Client, z. B. [Git Bash](https://git-scm.com/).
 * Um die Beispielanwendung zu testen, benötigen Sie auch einen DNS-Server. Sie können beliebige DNS-Server oder -Dienste verwenden, die Platzhalter-A-Datensätze unterstützen.
 * Einen Computer zum Ausführen der Deis-Clienttools. Sie können einen lokalen oder virtuellen Computer verwenden. Sie können diese Tools auf nahezu jeder Linux-Bereitstellung ausführen, in den folgenden Anweisungen wird jedoch Ubuntu verwendet.

## Bereitstellen des Clusters

In diesem Abschnitt verwenden Sie eine [Azure-Ressourcen-Manager](../resource-group-overview.md)-Vorlage aus dem Open-Source-Repository [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Zunächst müssen die Vorlage kopieren. Anschließend erstellen Sie ein neues SSH-Schlüsselpaar für die Authentifizierung. Danach konfigurieren Sie einen neuen Bezeichner für den Cluster. Abschließend verwenden Sie das Shell-Skript oder das PowerShell-Skript, um den Cluster bereitzustellen.

1. Klonen Sie das Repository: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Navigieren Sie zum Vorlagenordner:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Erstellen Sie ein neues SSH-Schlüsselpaar mit ssh-keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Generieren Sie ein Zertifikat mit dem obigen privaten Schlüssel:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Wechseln Sie zu [https://discovery.etcd.io/new](https://discovery.etcd.io/new), um ein neues Cluster-Token zu erstellen, das in etwas so aussieht:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br /> Jeder CoreOS-Cluster muss über ein eindeutiges Token aus diesem kostenlosen Dienst verfügen. Weitere Informationen finden Sie in der [CoreOS-Dokumentation](https://coreos.com/docs/cluster-management/setup/cluster-discovery/).

6. Ändern Sie die Datei **cloud-config.yaml**, um das vorhandene **discovery**-Token durch das neue Token zu ersetzen.

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Ändern Sie die Datei **azuredeploy-parameters.json**: Öffnen Sie das Zertifikat, das Sie in Schritt 4 in einem Text-Editor erstellt haben. Kopieren Sie den gesamten Text zwischen `----BEGIN CERTIFICATE-----` und `-----END CERTIFICATE-----` in den Parameter **sshKeyData** (Sie müssen alle Zeilenumbruchzeichen entfernen).

8. Ändern Sie den Parameter **newStorageAccountName**. Dies ist das Speicherkonto für Betriebssystem-Datenträger des virtuellen Computers. Dieser Kontoname muss global eindeutig sein.

9. Ändern Sie den Parameter **publicDomainName**. Dieser ist Teil des DNS-Namens des Load Balancers, der mit der öffentlichen IP-Adresse des Load Balancers verknüpft ist. Der endgültige FQDN hat das Format _[Wert dieses Parameters]_._[Region]_.cloudapp.azure.com. Wenn Sie z. B. den Namen "deishbai32" angeben und die Ressourcengruppe in der Region "West US" bereitgestellt wird, lautet der endgültige FQDN für den Load Balancer "deishbai32.westus.cloudapp.azure.com".

10. Speichern Sie die Parameterdatei. Stellen Sie anschließend den Cluster mit Azure PowerShell bereit:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  oder mit der Azure-Befehlszeilenschnittstelle:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Sobald die Ressourcengruppe bereitgestellt wurde, können Sie im Azure-Portal alle Ressourcen in der Gruppe sehen. Wie im folgenden Screenshot gezeigt, enthält die Ressourcengruppe ein virtuelles Netzwerk mit drei virtuellen Computern, die derselben Verfügbarkeitsgruppe angehören. Die Gruppe enthält auch einen Load Balancer, der über eine zugeordnete öffentliche IP verfügt.

  ![Bereitgestellte Ressourcengruppe im Azure-Portal](media/virtual-machines-deis-cluster/resource-group.png)

## Installieren des Clients

Zum Steuern des Deis-Clusters benötigen Sie **deisctl**. deisctl wird automatisch in allen Clusterknoten installiert, es empfiehlt sich jedoch, deisctl auf einem separaten administrativen Computer verwenden. Da alle Knoten ausschließlich mit privaten IP-Adressen konfiguriert sind, müssen Sie darüber hinaus einen SSH-Tunnel durch den Load Balancer verwenden, der über eine öffentliche IP verfügt, um eine Verbindung zu den Computerknoten herzustellen. Die folgenden Schritte beschreiben die Einrichtung von deisctl auf einem separaten physischen oder virtuellen Ubuntu-Computer.

1. Installieren Sie "deisctl:mkdir"-deis:

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Fügen Sie den privaten Schlüssels zum SSH-Agent hinzu:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Konfigurieren Sie deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Die Vorlage definiert die eingehende NAT-Regeln, die 2223 zu Instanz 1, 2224 zu Instanz 2 und 2225 zu Instanz 3 zuordnen. Dies bietet Redundanz für die Verwendung des deisctl-Tools. Sie können diese Regeln im Azure-Portal überprüfen:

![NAT-Regeln des Load Balancers](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE]Die Vorlage unterstützt derzeit nur 3-Knoten-Cluster. Dies liegt an der Beschränkung in der Azure-Ressourcen-Manager-Vorlage für die NAT-Regel-Definition, die keine Schleifensyntax unterstützt.

## Installieren und Starten der Deis-Plattform

Sie können deisctl nun verwenden, um die Deis-Plattform zu installieren und zu verwenden:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE]Das Starten der Plattform dauert einige Zeit (bis zu 10 Minuten). Insbesondere das Starten des Builder-Dienstes kann lange dauern. Unter Umständen sind mehrere Versuche erforderlich: Wenn der Vorgang nicht mehr reagiert, versuchen Sie die Ausführung des Befehls durch Eingabe von `ctrl+c` abzubrechen, und wiederholen Sie den Vorgang.

Sie können `deisctl list` verwenden, um zu überprüfen, ob alle Dienste ausgeführt werden:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Glückwunsch! Sie verfügen nun über einen laufenden Deis-Cluster in Azure! Als Nächstes stellen Sie eine Go-Beispielanwendung bereit, um den Cluster in Aktion zu sehen.

## Bereitstellen und Skalieren einer Hello World-Anwendung

Die folgenden Schritte zeigen, wie Sie die Go-Anwendung "Hello World" im Cluster bereitstellen. Die Schritte basieren auf der [Deis-Dokumentation](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Damit das Routing-Netz ordnungsgemäß funktioniert, müssen Sie einen Platzhalter-A-Datensatz für die Domäne verwenden, der auf die öffentliche IP-Adresse des Load Balancers verweist. Der folgende Screenshot zeigt den A-Datensatz für eine Beispiel-Domänenregistrierung auf GoDaddy:

    ![GoDaddy-A-Datensatz](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. Installieren Sie deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Erstellen Sie einen neuen SSH-Schlüssel, und fügen dann den öffentlichen Schlüssel in GitHub hinzu (Sie können auch vorhandene Schlüssel verwenden). Erstellen Sie das neue SSH-Schlüsselpaar mit:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Fügen Sie "id\_rsa.pub" oder einen öffentlichen Schlüssel Ihrer Wahl zu GitHub hinzu. Verwenden Sie hierzu die Schaltfläche zum Hinzufügen des SSH-Schlüssels im Konfigurationsbildschirm für SSH-Schlüssel:

  ![GitHub-Schlüssel](media/virtual-machines-deis-cluster/github-key.png) <p /> 5. Registrieren Sie einen neuen Benutzer:

        deis register http://deis.[your domain]
<p />
6. Fügen Sie den SSH-Schlüssel hinzu:

        deis keys:add [path to your SSH public key]
  <p />
7. Erstellen Sie eine Anwendung.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. Mit "git push" wird die Erstellung und Bereitstellung von Docker-Images ausgelöst. Dies kann einige Minuten dauern. Der Vorgang kann gelegentlich bei Schritt 10 (Übertragung des Images in das private Repository) hängen bleiben. Beenden Sie in diesem Fall den Prozess, entfernen Sie die Anwendung mit `deis apps:destroy –a <application name>`, und versuchen Sie es erneut. Den Namen der Anwendung können Sie mit `deis apps:list` ermitteln. Wenn alle Prozesse ordnungsgemäß funktioniert haben, sollte am Ende der Befehlsausgabe etwa Folgendes angezeigt werden:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Überprüfen Sie, ob die Anwendung funktioniert:

        curl -S http://[your application name].[your domain]
  Folgendes sollte angezeigt werden:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Skalieren Sie die Anwendung in drei Instanzen:

        deis scale cmd=3
<p />
11. Optional können Sie "deis info" verwenden, um die Details Ihrer Anwendung zu überprüfen. Die folgenden Ausgaben stammen aus der Bereitstellung der Anwendung:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## Nächste Schritte

In diesem Artikel wurden Sie durch alle Schritte der Bereitstellung eines neuen Deis-Clusters mit einer Azure-Ressourcen-Manager-Vorlage geführt. Die Vorlage unterstützt Redundanz für Verbindung zu Tools und Lastenausgleich für bereitgestellte Anwendungen. Die Vorlage vermeidet außerdem die Verwendung öffentlicher IPs auf Mitgliederknoten, sodass wertvolle öffentliche IP-Ressourcen gespart werden und eine sichere Umgebung für das Hosten von Anwendungen gewährleistet ist. Weitere Informationen finden Sie in den folgenden Artikeln:

[Übersicht über den Azure-Ressourcen-Manager][resource-group-overview] [Verwenden der Azure-CLI][azure-command-line-tools] [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=Sept15_HO4-->