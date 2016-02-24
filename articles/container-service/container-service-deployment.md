<properties
   pageTitle="Bereitstellen eines Azure Container Service-Clusters | Microsoft Azure"
   description="Bereitstellen eines Azure Container Service-Clusters mithilfe des Azure-Portals, der Azure-Befehlszeilenschnittstelle oder PowerShell"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Container, Microservices, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Bereitstellen eines Azure Container Service-Clusters

Der Azure Container Service ermöglicht eine schnelle Bereitstellung beliebter Open Source-Lösungen für Container-Clustering- und Orchestrierung. Mithilfe von Azure Container Service können Marathon Mesos- und Docker Swarm-Cluster mit Azure-Ressourcen-Manager-Vorlagen oder im Portal bereitgestellt werden. Diese Cluster werden mithilfe von Azure Virtual Machine-Skalierungsgruppen bereitgestellt, und sie ermöglichen es, die Azure Netzwerk- und Speicherangebote zu nutzen. Zum Zugriff auf Azure Container Service benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement haben, registrieren Sie sich [für eine kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

In diesem Dokument werden die Bereitstellung und die Verwendung des [Azure-Portals](#creating-a-service-using-the-azure-portal), der [Azure-Befehlszeilenschnittstelle](#creating-a-service-using-the-azure-cli) und des [Azure PowerShell-Moduls](#creating-a-service-using-powershell) für den Azure Container Service-Cluster schrittweise beschrieben.
   
## Erstellen eines Diensts mithilfe des Azure-Portals
 
Wählen Sie zum Bereitstellen eines Mesos- oder Docker Swarm-Clusters eine der folgenden Vorlagen aus. **Hinweis** – Mit Ausnahme der standardmäßigen Orchestrator-Auswahl sind diese beiden Vorlagen identisch.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Jede Vorlagenseite verfügt über die Schaltfläche „Bereitstellen in Azure“. Durch Betätigen dieser Schaltfläche wird das Azure-Portal mit einem Formular gestartet, das etwa wie folgt aussieht. <br />

![Bereitstellung erstellen](media/create-mesos-params.png) <br />

Füllen Sie das Formular mithilfe dieser Anleitung aus, und wählen Sie „OK“, wenn Sie damit fertig sind. <br />

Feld | Beschreibung
----------------|-----------
DNSNAMEPREFIX | Dies muss ein eindeutiger Wert sein. Dieser wird zum Erstellen von DNS-Namen für jeden wichtigen Teil des Diensts verwendet. Weitere Informationen finden Sie weiter unten.
AGENTCOUNT | Dies ist die Anzahl der virtuellen Maschinen, die in der ACS-Agent-Skalierungsgruppe erstellt werden.
AGENTVMSIZE | Gibt die Größe der virtuellen Agent-Maschinen an. Wählen Sie eine Größe, die genügend Ressourcen bietet, um die größten Container zu hosten.
ADMINUSERNAME | Dies ist der Benutzername, der für ein Konto auf jeder der virtuellen Maschinen und Skalierungsgruppen für virtuelle Maschinen im ACS-Cluster verwendet wird.
ORCHESTRATORTYPE| Wählen Sie den Orchestrator aus, den Sie in Ihrem ACS-Cluster verwenden möchten.
MASTERCOUNT | Dies ist die Anzahl der virtuellen Maschinen, die als Master für den Cluster konfiguriert werden. Sie können 1 auswählen, dies bietet in Ihrem Cluster allerdings keinerlei Ausfallsicherheit und wird nur für Testzwecke empfohlen. Der empfohlene Wert für einen Produktionscluster ist 3 oder 5. 
SSHRSAPUBLICKEY | Für die Authentifizierung für die virtuellen Maschinen muss SSH verwendet werden. Hier fügen Sie Ihren öffentlichen Schlüssel hinzu. Fügen Sie den Schlüsselwert exakt in dieses Feld ein. Manchmal enthält der Inhalt einen Zeilenumbruch, wodurch der Schlüssel geteilt wird. Prüfen Sie, dass Ihr Schlüssel keine Zeilenumbrüche enthält und das Präfix „ssh-rsa“ sowie das Postfix „username@domain“ enthält. Er sollte etwa wie folgt aussehen: „ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'“. Wenn Sie einen SSH-Schlüssel erstellen müssen, finden Sie auf der Website mit der Azure-Dokumentation Informationen zu [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) und [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md).
  
Wenn Sie die entsprechenden Werte für Ihre Parameter festgelegt haben, klicken Sie auf „OK“. Vergeben Sie als Nächstes einen Namen für die Ressourcengruppe, wählen Sie eine Region aus, lesen Sie die rechtlichen Bedingungen, und stimmen Sie diesen zu.

> Während der Vorschau fallen keine Gebühren für Azure Container Service an, sondern nur standardmäßige Compute-Gebühren wie z. B. für virtuelle Maschinen, Speicher, Netzwerk usw.
 
![Ressourcengruppe auswählen](media/resourcegroup.png)
 
Klicken Sie abschließend auf „Erstellen“. Sie kehren zum Dashboard zurück, und sofern Sie die Option „An Dashboard anheften“ auf dem Blatt für die Bereitstellung nicht deaktiviert haben, wird eine animierte Kachel angezeigt, die etwa wie folgt aussieht:

![Bereitstellen](media/deploy.png)
 
Jetzt lehnen Sie sich zurück und entspannen Sie sich, während der Cluster erstellt wird. Nachdem der Vorgang abgeschlossen ist, werden einige Blätter angezeigt, die die Ressourcen anzeigen, welche die ACS-Cluster bilden.
 
![Abgeschlossen](media/final.png)

## Erstellen eines Diensts mithilfe der Azure-Befehlszeilenschnittstelle

Um eine Instanz des Azure Container Service über die Befehlszeile zu erstellen, benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement haben, registrieren Sie sich für eine kostenlose Testversion. Außerdem muss die Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein.
 
Wählen Sie zum Bereitstellen eines Mesos- oder Docker Swarm-Clusters eine der folgenden Vorlagen aus. **Hinweis** – Mit Ausnahme der standardmäßigen Orchestrator-Auswahl sind diese beiden Vorlagen identisch.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Als Nächstes stellen Sie sicher, dass die Azure-Befehlszeilenschnittstelle mit einem Azure-Abonnement verbunden ist. Dies kann mithilfe des folgenden Befehls erfolgen:

```bash
Azure account show
```
Wenn ein Azure-Konto nicht zurückgegeben wird, melden Sie die Befehlszeilenschnittstelle folgendermaßen bei Azure an.
 
```bash
azure login -u user@domain.com
```

Konfigurieren Sie anschließend die Azure-Befehlszeilenschnittstellen-Tools für die Verwendung von Azure-Ressourcen-Manager.
 
```bash
azure config mode arm
```
 
Wenn Sie Ihren Cluster in einer neuen Ressourcengruppe erstellen möchten, müssen Sie zunächst die Ressourcengruppe erstellen. Verwenden Sie diesen Befehl, in dem `GROUP_NAME` der Name der zu erstellenden Ressourcengruppe ist, und `REGION` für die Region steht, in der Sie die Ressourcengruppe erstellen möchten.
 
```bash
azure group create GROUP_NAME REGION
```
 
Nach dem Erstellen der Ressourcengruppe können Sie den Cluster mit diesem Befehl erstellen, wobei Folgendes gilt:

- **RESOURCE\_GROUP** ist der Name der Ressourcengruppe, die für diesen Dienst verwendet werden soll.
- **DEPLOYMENT\_NAME** ist der Name dieser Bereitstellung.
- **TEMPLATE\_URI** ist der Speicherort der Bereitstellungsdatei. **Hinweis** – Dies muss eine Rohdatendatei und kein Zeiger auf die GitHub-UI sein. Wählen Sie zum Suchen dieser URL die Datei „azuredeploy.json“ in GitHub, und klicken Sie auf die Schaltfläche „RAW“:

> Hinweis – Beim Ausführen dieses Befehls werden Sie von der Shell aufgefordert, Parameterwerte für die Bereitstellung einzugeben.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Eingeben von Vorlagenparametern
 
Bei dieser Version des Befehls muss der Benutzer Parameter interaktiv definieren. Wenn Sie Parameter als JSON-formatierte Zeichenfolge eingeben möchten, ist dies mit dem Switch `-p` möglich. Beispiel:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Alternativ können Sie eine JSON-formatierte Parameterdatei mit dem Switch `-e` angeben:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Eine Beispielparameterdatei mit dem Namen `azuredeploy.parameters.json` befindet sich bei den ACS-Vorlagen in GitHub.
 
## Erstellen eines Diensts mithilfe von PowerShell

Ein ACS-Cluster kann auch mit PowerShell bereitgestellt werden. Dieses Dokument basiert auf Azure [PowerShell-Modul](https://azure.microsoft.com/blog/azps-1-0/) Version 1.0 und höher.

Wählen Sie zum Bereitstellen eines Mesos- oder Docker Swarm-Clusters eine der folgenden Vorlagen aus. **Hinweis** – Mit Ausnahme der standardmäßigen Orchestrator-Auswahl sind diese beiden Vorlagen identisch.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)

Prüfen Sie vor dem Erstellen eines Clusters in Ihrem Azure-Abonnement, dass Ihre PowerShell-Sitzung bei Azure angemeldet ist. Dies ist mithilfe des Befehls `Get-AzureRMSubscription` möglich.

```powershell
Get-AzureRmSubscription
```

Wenn Sie sich bei Azure anmelden müssen, verwenden Sie den Befehl `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```
 
Bei der Bereitstellung in einer neue Ressourcengruppe müssen Sie zunächst die Ressourcengruppe erstellen. Verwenden Sie zum Erstellen einer neuen Ressourcengruppe den Befehl `New-AzureRmResourceGroup`, und legen Sie einen Namen für die Ressourcengruppe sowie eine Zielregion fest.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Nach dem Erstellen der Ressourcengruppe können Sie mithilfe des folgenden Befehls Ihren Cluster erstellen. Die Befehlszeilenschnittstelle der gewünschten Vorlage wird für den Parameter `-TemplateUri` festgelegt. Beim Ausführen dieses Befehls werden Sie von PowerShell aufgefordert, Parameterwerte für die Bereitstellung einzugeben.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Nächste Schritte
 
Da Sie nun einen funktionierenden Cluster haben, lesen Sie die folgenden Dokumente, um Informationen zur Verbindung und Verwaltung zu erhalten.
 
- [Connect with the ACS cluster (Verbinden mit dem ACS-Cluster)](./container-service-connect.md)
- [Working with ACS and Mesos (Arbeiten mit ACS und Mesos)](./container-service-mesos-marathon-rest.md)

 

<!----HONumber=AcomDC_0218_2016-->