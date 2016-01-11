<properties 
   pageTitle="Runbookeingabeparameter"
   description="Runbookeingabeparameter erhöhen die Flexibilität von Runbooks, weil Sie mit ihnen Daten an ein Runbook übergeben können, wenn es gestartet wird. In diesem Artikel werden verschiedene Szenarien beschrieben, in denen Eingabeparameter in Runbooks verwendet werden."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/16/2015"
   ms.author="sngun"/>

# Runbookeingabeparameter

Runbookeingabeparameter erhöhen die Flexibilität von Runbooks, weil Sie mit ihnen Daten an ein Runbook übergeben können, wenn es gestartet wird. Mit den Parametern können die Runbookaktionen auf bestimmte Szenarien und Umgebungen abgestimmt werden. In diesem Artikel lernen Sie verschiedene Szenarien kennen, in denen Eingabeparameter in Runbooks verwendet werden.

## Konfigurieren von Eingabeparametern

Eingabeparameter können in PowerShell-, PowerShell-Workflow- und grafischen Runbooks konfiguriert werden. Ein Runbook kann mehrere Parameter mit unterschiedlichen Datentypen oder gar keine Parameter aufweisen. Eingabeparameter können erforderlich oder optional sein, und Sie können optionalen Parametern einen Standardwert zuweisen. Den für ein Runbook konfigurierten Eingabeparametern können Werte zugewiesen werden, wenn Sie ein Runbook mit einer der zur Verfügung stehenden Methoden starten, einschließlich Starten eines Runbooks über Benutzeroberfläche, Webhooks, Webdienst oder als untergeordnetes Runbook in einem anderen Runbook (inline).

## Konfigurieren von Eingabeparametern in PowerShell- und PowerShell-Workflow-Runbooks

PowerShell- und [PowerShell-Workflow-Runbooks](automation-first-runbook-textual.md) in Azure Automation unterstützen Eingabeparameter, die mit den folgenden Attributen definiert werden:

| **Eigenschaft** | **Beschreibung** |
|:--- |:---|
| Typ | Erforderlich. Der für den Parameterwert erwartete Datentyp. Jeder .Net-Typ ist gültig. |
| Name | Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein, kann nur Buchstaben, Zahlen oder Unterstriche enthalten und muss mit einem Buchstaben beginnen. |
| Erforderlich | Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie diese auf **$true** setzen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie diese auf **$false** setzen, ist ein Wert optional. |
| Standardwert | Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für jeden Parameter festgelegt werden und macht den Parameter unabhängig von der Einstellung „Erforderlich“ optional. |

Windows PowerShell unterstützt mehr Eingabeparameterattribute, als hier aufgelistet, z. B. zur Validierung, Aliase und Parametersätze. Azure Automation unterstützt derzeit jedoch nur die oben aufgelisteten.

Eine Parameterdefinition in PowerShell-Workflow-Runbooks hat die folgende allgemeine Form, wobei mehrere Parameter durch Kommas voneinander getrennt sind.

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     ) 
```

>[AZURE.NOTE]Wenn Sie beim Definieren von Parametern nicht das Attribut **Erforderlich** angeben, gilt der Parameter standardmäßig als optional. Auch wenn Sie einen Standardwert für einen Parameter in PowerShell-Workflow-Runbooks festlegen, behandelt PowerShell ihn unabhängig vom Attributwert **Erforderlich** als optionalen Parameter.

Als Beispiel konfigurieren wir die Eingabeparameter für ein PowerShell-Workflow-Runbook, das Details virtueller Computer ausgibt – entweder eines einzelnen virtuellen Computers oder aller virtuellen Computer in einem Dienst. Dieses Runbook verfügt über zwei Parameter: den Namen des virtuellen Computers und den Namen des Diensts, wie im folgenden Screenshot gezeigt.

![Automation: PowerShell-Workflow](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

In dieser Parameterdefinition sind die Parameter **$VMName** und **$ServiceName** einfache Parameter vom Typ Zeichenfolge; allerdings unterstützen PowerShell- und PowerShell-Workflow-Runbooks alle einfachen und komplexen Typen, wie **Objekt** oder **PSCredential**, für Eingabeparameter.

Wenn Ihr Runbook über einen Eingabeparameter des Typs [Objekt] verfügt, verwenden Sie eine PowerShell-Hashtabelle mit (Name, Wert)-Paaren, um einen Wert zu übergeben. Ihr Runbook weist beispielsweise den folgenden Parameter auf:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Dann können Sie den folgenden Wert an den Parameter übergeben:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## Konfigurieren von Eingabeparametern in grafischen Runbooks

Um ein grafisches Runbook mit Eingabeparametern zu konfigurieren, erstellen wir ein [grafisches Runbook](automation-first-runbook-graphical.md), das Details virtueller Computer ausgibt – entweder eines einzelnen virtuellen Computers oder aller virtuellen Computer in einem Dienst. Das Runbook besteht aus zwei Hauptaktivitäten:

* [**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) zur Authentifizierung bei Azure.
* [**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) zum Abrufen aller virtuellen Computer.

Sie können mit der Aktivität [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) die Namen von virtuellen Computern abrufen. Die Aktivität **Get-AzureVM** akzeptiert zwei Parameter: den **Namen des virtuellen Computers** und den **Dienstkontonamen**. Da diese Parameter bei jedem Start des Runbooks unterschiedliche Werte benötigen könnten, können Sie Ihrem Runbook Eingabeparameter hinzufügen. Mit diesen Schritten fügen Sie Eingabeparameter hinzu:

1. Wählen Sie das grafische Runbook im Blatt **Runbooks** aus, und [bearbeiten](automation-graphical-authoring-intro.md) Sie es. 

2. Klicken Sie im Blatt **Bearbeiten** auf **Eingabe und Ausgabe**, um das Blatt **Eingabe und Ausgabe** zu öffnen.

    ![Automation: grafisches Runbook](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)

 
3. Das Blatt **Eingabe und Ausgabe** zeigt eine Liste der Eingabeparameter an, die für das Runbook definiert sind. Von diesem Blatt aus können Sie entweder einen neuen Eingabeparameter hinzufügen oder die Konfiguration eines vorhandenen Eingabeparameters bearbeiten. Um einen neuen Parameter für das Runbook hinzuzufügen, klicken Sie auf **Eingabe hinzufügen**, um das Blatt „Runbook-Eingabeparameter“ zu öffnen, in dem Sie die folgenden Parameter konfigurieren können:

    | **Eigenschaft** | **Beschreibung** |
    |:--- |:---|
    | Name | Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein, kann nur Buchstaben, Zahlen oder Unterstriche enthalten und muss mit einem Buchstaben beginnen. |
    | Beschreibung | Optional. Beschreibung zum Zweck des Eingabeparameters. |
    | Typ | Optional. Der für den Parameterwert erwartete Datentyp. Unterstützte Parametertypen sind Zeichenfolge, Int32, Int64, Dezimal, Boolesch, Datum/Uhrzeit und Objekt. Wenn kein Datentyp ausgewählt wird, wird standardmäßig die Zeichenfolge festgelegt. |
    | Erforderlich | Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie **ja** wählen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie **nein** wählen, ist kein Wert erforderlich, wenn das Runbook gestartet wird, und ein Standardwert könnte festgelegt werden. |
    | Standardwert | Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für einen Parameter festgelegt werden, der nicht zwingend erforderlich ist. Sie können **Benutzerdefiniert** wählen, um einen Standardwert festzulegen. Dieser Wert wird verwendet, es sei denn, ein anderer Wert wird bereitgestellt, wenn das Runbook gestartet wird. Wählen Sie **Keiner**, wenn Sie keinen Standardwert angeben möchten. |  

    ![AddNewInput](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. Erstellen Sie zwei Parameter mit den folgenden Eigenschaften, die von der Aktivität **Get-AzureVM** verwendet werden:

    * **Parameter1:** Name – VMName, Typ – Zeichenfolge, Erforderlich – Nein
	
    * **Parameter2:** Name – ServiceName, Typ – Zeichenfolge, Erforderlich – Nein, Standardwert – Benutzerdefiniert, Benutzerdefinierter Standardwert – <Name des Standarddiensts, der die virtuellen Computer enthält>

5. Wenn Sie die Parameter hinzugefügt haben, klicken Sie auf **OK**. Sie sehen sie nun auf dem Blatt **Eingabe und Ausgabe**. Klicken Sie erneut auf **OK**, und **Speichern** und **Veröffentlichen** Sie Ihr Runbook.

## Zuweisen von Werten zu Eingabeparametern in Runbooks

Sie können in den folgenden Szenarien Eingabeparametern in Runbooks Werte übergeben:

### Starten eines Runbooks und Zuweisen von Parametern

Ein Runbook kann auf viele Arten gestartet werden: über die Benutzeroberfläche des Azure-Portals, mit einem Webhook, mit den PowerShell-Cmdlets, REST-API und SDK. Im Folgenden werden verschiedene Methoden zum Starten eines Runbooks und Zuweisen von Parametern beschrieben.

* **Starten eines veröffentlichten Runbooks über das Azure-Portal und Zuweisen von Parametern**

Wenn Sie [das Runbook starten](automation-starting-a-runbook#starting-a-runbook-with-the-azure-portal.md), wird das Blatt **Runbook starten** geöffnet, in dem Sie Werte für die Parameter konfigurieren können, die Sie gerade erstellt haben.

![Einstieg in die Verwendung des Portals](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

Der Beschriftung unter dem Eingabetextfeld können Sie entnehmen, welche Attribute für den Parameter festgelegt sind: erforderlich oder optional, Typ und Standardwert. In der Hilfesprechblase neben dem Namen des Parameters sehen Sie alle wichtige Informationen, die Sie benötigen, um Entscheidungen über Parametereingabewerte zu treffen. Dies schließt ein, ob ein Parameter erforderlich oder optional ist, außerdem den Typ, jeden Standardwert und andere hilfreiche Hinweise.

![Hilfesprechblase](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE]Parameter vom Typ Zeichenfolge unterstützen **leere** Zeichenfolgenwerte. Bei Eingabe einer **[leeren Zeichenfolge]** in das Eingabeparameter-Textfeld wird eine leere Zeichenfolge an den Parameter übergeben. Außerdem unterstützen Parameter vom Typ Zeichenfolge nicht die Übergabe von **Null**-Werten. Wenn Sie keinen Wert an den Zeichenfolgenparameter übergeben, interpretiert PowerShell dies als null.

* **Starten eines veröffentlichten Runbooks mit PowerShell-Cmdlets und Zuweisen von Parametern**

    * **Azure Service Management-Cmdlets:** Sie können ein Automation-Runbook, das in einer Standardressourcengruppe erstellt wurde, mit [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx) starten.

    **Beispiel:**

      ``` $params = @{„VMName“=„WSVMClassic“; „ServiceName“=„WSVMClassicSG“}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```


    * **Azure-Ressourcen-Manager-Cmdlets:** Sie können ein Automation-Runbook, das in einer Ressourcengruppe erstellt wurde, mit [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) starten.

    **Beispiel:**

      ``` $params = @{„VMName“=„WSVMClassic“;„ServiceName“=„WSVMClassicSG“}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE]Beim Starten eines Runbooks mit PowerShell-Cmdlets zusammen mit den Eingabeparametern, die Sie übergeben haben, wird ein Standardparameter **MicrosoftApplicationManagementStartedBy** mit dem Wert **PowerShell** erstellt. Sie können diesen Parameter im Blatt „Auftragdetails“ anzeigen.

* **Starten eines Runbooks mit dem SDK und Zuweisen von Parametern**

    * **Azure Service Management-Methode:** Sie können ein Runbook mithilfe des SDK einer Programmiersprache starten. Im Folgenden sehen Sie einen C#-Codeausschnitt zum Starten eines Runbooks in Ihrem Automation-Konto. Den vollständigen Code finden Sie in unserem [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```      
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
            var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
            {
                Properties = new JobCreateProperties 
                {
                    Runbook = new RunbookAssociationProperty
                    {
                        Name = runbookName
                    },
                        Parameters = parameters
                }
            });
            return response.Job;
        }
    ```
      
    * **Azure-Ressourcen-Manager-Methode:** Sie können ein Runbook mithilfe des SDK einer Programmiersprache starten. Im Folgenden sehen Sie einen C#-Codeausschnitt zum Starten eines Runbooks in Ihrem Automation-Konto. Den vollständigen Code finden Sie in unserem [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
           var response = AutomationClient.Jobs.Create(resourceGroup, automationAccount, new JobCreateParameters
           {
               Properties = new JobCreateProperties 
               {
                   Runbook = new RunbookAssociationProperty
                   {
                       Name = runbookName
                   },
                       Parameters = parameters
               }
           });
        return response.Job;
        }
    ```

Um diese Methode zu starten, erstellen Sie ein Wörterbuch, um die Parameter des Runbooks, „VMName“ und „ServiceName“, und deren Werte zu speichern, und starten Sie das Runbook. Im Folgenden finden Sie den C#-Codeausschnitt zum Aufrufen der oben definierten Methode.

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

* **Starten eines Runbooks mit der REST-API und Zuweisen von Parametern**

Ein Runbookauftrag kann mit der REST-API von Azure Automation erstellt und gestartet werden, wobei die **PUT**-Methode mit dem folgenden Anforderungs-URI verwendet wird.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

Ersetzen Sie im Anforderungs-URI die folgenden Parameter:
 
* **subscription-id**: Ihre Azure-Abonnement-ID.  
* **cloud-service-name**: Name des Clouddiensts, an den die Anforderung gesendet werden soll.  
* **automation-account-name**: Name Ihres Automation-Kontos, das im angegebenen Clouddienst gehostet wird.  
* **job-id**: Die GUID für den Auftrag. Die GUID in PowerShell kann mit dem Cmdlet **[GUID]::NewGuid().ToString()** erstellt werden.
	
Um Parameter an den Runbookauftrag zu übergeben, verwenden Sie den Anforderungstext, und zwei Eigenschaften müssen im JSON-Format bereitgestellt werden:

* **Runbookname** – Erforderlich. Der Name des Runbooks für den zu startenden Auftrag.  
* **Runbookparameter** – Optional. Ein Wörterbuch der Parameterliste im Format (Name, Wert), wobei der Name vom Typ Zeichenfolge sein sollte, und als Wert jeder gültige JSON-Wert infrage kommt. 

Wenn Sie das zuvor mit „VMName“ und „ServiceName“ als Parameter erstellte Runbook **Get-AzureVMTextual** starten möchten, verwenden Sie das folgende JSON-Format für den Anforderungstext.

```
        {
           "properties":{
           "runbook":{
               "name":"Get-AzureVMTextual"
           },
           "parameters":{
               "VMName":"WSVMClassic",
               "ServiceName":”WSCS1”
           }
          }
       }
```

Der HTTP-Statuscode 201 wird zurückgegeben, wenn der Auftrag erfolgreich erstellt wurde. Weitere Informationen zu den Antwortheadern und zum Antworttext finden Sie in dem Artikel zum [Erstellen eines Runbookauftrags mithilfe der REST-API](https://msdn.microsoft.com/library/azure/mt163849.aspx).

### Testen eines Runbooks und Zuweisen von Parametern

Wenn Sie die Entwurfsversion des Runbooks mithilfe der Testoption [testen](automation-testing-runbook.md), wird das Blatt **Testen** geöffnet, wo Sie Werte für die Parameter konfigurieren können, die Sie gerade erstellt haben.

![Testen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### Verknüpfen eines Zeitplans mit einem Runbook und Zuweisen von Parametern

Sie können mit Ihrem Runbook [einen Zeitplan verknüpfen](automation-scheduling-a-runbook.md), um zu einem bestimmten Zeitpunkt zu starten. Eingabeparameter werden beim Erstellen des Zeitplans zugewiesen, und das Runbook verwendet diese Werte, wenn es vom Zeitplan gestartet wird. Sie können den Zeitplan erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Zeitplan und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Erstellen eines Webhooks für ein Runbook, und Zuweisen von Parametern

Sie können einen [Webhook](automation-webhooks.md) für Ihr Runbook erstellen und Runbookeingabeparameter konfigurieren. Sie können den Webhook erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Erstellen eines Webhooks und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Beim Ausführen eines Runbooks mit einem Webhook wird ein vordefinierter Eingabeparameter **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** zusammen mit den Eingabeparametern gesendet, die Sie definiert haben. Sie können klicken, um weitere Details zum WebhookData-Parameter anzuzeigen.

![WebhookData-Parameter](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## Nächste Schritte

- Weitere Informationen zu Runbookeingabe und -ausgabe finden Sie unter [Azure Automation: Runbookeingabe, -ausgabe und geschachtelte Runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
- Weitere Informationen zu verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks](automation-starting-a-runbook.md).
- Informationen zum Bearbeiten eines Textrunbooks finden Sie unter [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md).
- Informationen zum Bearbeiten eines grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

<!---HONumber=AcomDC_1223_2015-->