<properties
   pageTitle="Runbookeingabeparameter| Microsoft Azure"
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

Eingabeparameter können in PowerShell-, PowerShell-Workflow und grafischen Runbooks konfiguriert werden. Ein Runbook kann mehrere Parameter mit unterschiedlichen Datentypen oder gar keine Parameter aufweisen. Eingabeparameter können erforderlich oder optional sein, und Sie können optionalen Parametern einen Standardwert zuweisen. Wenn Sie eine der verfügbaren Methoden verwenden, um das Runbook zu starten, können Sie Werte zu den einzelnen Eingabeparametern zuweisen. Sie können das Runbook beispielsweise über eine Benutzeroberfläche oder einen Web Service starten. Sie haben auch die Möglichkeit, ein Runbook als ein untergeordnetes Runbook zu starten, das inline in einem anderen Runbook aufgerufen werden kann.

## Eingabeparameter in PowerShell- und PowerShell-Workflow-Runbooks konfigurieren

PowerShell- und [PowerShell-Workflow-Runbooks](automation-first-runbook-textual.md) in Azure Automation unterstützen Eingabeparameter, die mit den folgenden Attributen definiert werden:

| **Eigenschaft** | **Beschreibung** |
|:--- |:---|
| Typ | Erforderlich. Der für den Parameterwert erwartete Datentyp. Jeder .NET-Typ ist valide. |
| Name | Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein und kann nur Buchstaben, Zahlen oder Unterstriche enthalten. Er muss mit einem Buchstaben beginnen. |
| Erforderlich | Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie diese auf **$true** setzen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie diese auf **$false** setzen, ist ein Wert optional. |
| Standardwert | Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für jeden Parameter festgelegt werden und macht den Parameter unabhängig von der Einstellung „Erforderlich“ optional. |

 Windows PowerShell unterstützt neben den hier genannten Eingabeparametern auch andere wie beispielsweise Validierung, Alias und Parametersätze. Azure Automation hingegen unterstützt zurzeit lediglich die oben genannten Eingabeparameter.

Eine Parameterdefinition in PowerShell-Workflow-Runbooks hat die folgende allgemeine Form, wobei mehrere Parameter durch Kommas getrennt werden.

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
```

>[AZURE.NOTE] Wenn Sie bei der Definition der Parameter nicht das Attribut „**Erforderlich**” angeben, gilt der Parameter standardmäßig als optional. Wenn Sie für einen Parameter in PowerShell-Workflow-Runbooks einen Standardwert vorgeben, wird dieser Parameter von PowerShell als optional behandelt, egal was beim Attribut „**Erforderlich**” steht.

Als Beispiel konfigurieren wir die Eingabeparameter für ein PowerShell-Workflow-Runbook, das Details virtueller Computer ausgibt - entweder eines einzelnen virtuellen Computers oder aller virtuellen Computer in einem Dienst. Dieses Runbook hat, wie im folgenden Screenshot gezeigt, zwei Parameter: den Namen des virtuellen Computers und den Namen des Diensts.

![Automation: PowerShell-Workflow](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

In dieser Parameterdefinition sind „**$VMName**” und „**$ServiceName**” Parameter vom Typ „Zeichenfolge“. PowerShell- und PowerShell-Workflow-Runbooks unterstützen jedoch alle einfachen Typen und komplexe Typen wie „**Objekt**” oder „**PSCredential**” als Eingabeparameter.

Verwenden Sie eine PowerShell-Hashtabelle mit (Name, Wert)-Paaren um einen Wert einzuschreiben, falls Ihr Runbook über einen Eingabeparameter des Typs „[Objekt]“ verfügt Ihr Runbook weist beispielsweise den folgenden Parameter auf:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Dann können Sie den folgenden Wert an den Parameter übergeben:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## Konfigurieren von Eingabeparametern in grafischen Runbooks

Um ein [grafisches Runbook](automation-first-runbook-graphical.md) mit Eingabeparametern zu konfigurieren, erstellen wir ein grafisches Runbook, das als Ausgabewerte Details der virtuellen Computer - entweder eines einzelnen oder aller virtuellen Computer innerhalb eines Dienstes, gibt. Das Konfigurieren eines Runbooks erfodert, wie unten beschrieben, zwei Hauptaktivitäten.

[**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) zur Authentifizierung bei Azure.

[**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) zum Abrufen aller virtuellen Computer.

Sie können mit der Aktivität [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) die Namen von virtuellen Computern abrufen. Die Aktivität **Get-AzureVM** akzeptiert zwei Parameter: den **Namen des virtuellen Computers** und den **Dienstnamen**. Da diese Parameter bei jedem Start des Runbooks unterschiedliche Werte benötigen könnten, können Sie Ihrem Runbook Eingabeparameter hinzufügen. Mit diesen Schritten fügen Sie Eingabeparameter hinzu:

1. Wählen Sie das grafische Runbook im Blatt **Runbooks** aus, und [bearbeiten](automation-graphical-authoring-intro.md) Sie es.

2. Klicken Sie im Blatt „**Bearbeiten**” auf „**Eingabe und Ausgabe**”, um das **Eingabe- und Ausgabe**-Blatt zu öffnen.

    ![Automation: Grafisches Runbook](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)


3. Das Blatt „**Eingabe und Ausgabe**” zeigt eine Liste der für das Runbook definierten Eingabeparameter an. In diesem Blatt können Sie entweder einen neuen Eingabeparameter hinzufügen oder die Konfiguration eines bestehenden Eingabeparameters bearbeiten. Um einen neuen Runbook-Parameter hinzuzufügen, klicken Sie auf „**Eingabe hinzufügen**” und öffnen damit das Blatt „**Runbook Eingabeparameter**”. Hier können Sie die folgenden Parameter konfigurieren:

    | **Eigenschaft** | **Beschreibung** |
    |:--- |:---|
    | Name | Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein und kann nur Buchstaben, Zahlen oder Unterstriche enthalten. Er muss mit einem Buchstaben beginnen. |
    | Beschreibung | Optional. Beschreibung zum Zweck des Eingabeparameters. |
    | Typ | Optional. Der für den Parameterwert erwartete Datentyp. Die Parametertypen „**Zeichenfolge**”, „**Int32**”, „**Int64**”, „**Dezimal**”, „**Boolesch**”, „**Datum/Uhrzeit**”, und „**Objekt**” werden unterstützt. Wenn kein Datentyp ausgewählt wird, wird standardmäßig „**Zeichenfolge**” verwendet. |
    | Erforderlich | Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie **ja** wählen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie **nein** wählen, ist kein Wert erforderlich, wenn das Runbook gestartet wird, und ein Standardwert könnte festgelegt werden. |
    | Standardwert | Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für optionale Parameter vergeben werden. Wählen Sie „**Benutzerdefiniert**”, um einen Standardwert vorzugeben. Dieser Wert wird verwendet, es sei denn, ein anderer Wert wird bereitgestellt, wenn das Runbook gestartet wird. Wählen Sie **Keiner**, wenn Sie keinen Standardwert angeben möchten. |  

    ![Neue Eingabe hinzufügen](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. Erstellen Sie zwei Parameter mit den folgenden Eigenschaften, die von der Aktivität **Get-AzureVM** verwendet werden:

    * **Parameter1:** Name--VMName, Type--Zeichenfolge, Erforderlich--Nein

    * **Parameter2:** Name--ServiceName, Typ--Zeichenfolge, Erforderlich--Nein, Standardwert--Benutzerdefiniert, Benutzerdefinierter Standardwert--<Name des Standarddiensts, der die virtuellen Computer enthält>

5. Wenn Sie die Parameter hinzugefügt haben, klicken Sie auf **OK**. Sie können die Parameter jetzt im Blatt „**Eingabe und Ausgabe**” sehen. Klicken Sie nochmal auf „**OK**”, dann auf „**Speichern**”, und „**Veröffentlichen**” Sie Ihr Runbook.

## Eingabeparametern in Runbooks Werte zuweisen

In den folgenden Szenarien können Sie den Eingabeparametern in Runbooks Werte übergeben:

### Starten eines Runbooks und Zuweisen von Parametern

Ein Runbook kann auf viele verschiedene Arten gestartet werden: Über die Benutzeroberfläche des Azure-Portals, mit einem Webhook, mit den PowerShell-Cmdlets (Erweiterungen für PowerShell), mit der REST-API oder mit einem SDK (Software Development Kit). Unten werden verschiedene Methoden beschrieben, um ein Runbook zu starten und Parameter zuzuweisen.

- **Starten eines veröffentlichten Runbooks über das Azure-Portal und Zuweisen von Parametern**

Wenn Sie das [Runbook starten](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), öffnet sich das Blatt „**Runbook starten**” und Sie können Werte für die eben von Ihnen erstellten Parameter konfigurieren.

![Starten unter Verwendung des Portals](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

In der Beschriftung unter dem Eingabefeld können Sie sehen, welche Attribute für die Parameter festgelegt wurden. Diese können sein: „Erforderlich“ oder „Optional“, „Typ“ und „Standardwert“. In der Hilfesprechblase neben dem Namen des Parameters sehen Sie alle wichtige Informationen, die Sie benötigen, um Entscheidungen über Parametereingabewerte zu treffen. Dies schließt die Information ein, ob es sich um einen erforderlichen Parameter handelt. Sie zeigen auch den Typ, den Standardwert (sofern vorhanden) und andere hilfreiche Hinweise an.

![Hilfesprechblase](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE] Parameter des Typs „Zeichenfolge“ unterstützen **leere** Zeichenfolgenwerte. Die Eingabe einer **[leeren Zeichenfolge]**in das Eingabefeld des Eingabeparameters bewirkt, dass eine leere Zeichenfolge an den Parameter übergeben wird. Außerdem unterstützen Parameter vom Typ „Zeichenfolge“ nicht die Übergabe von **Null**-Werten. Wenn Sie keinen Wert an den Zeichenfolge-Parameter übergeben, interpretiert PowerShell dies als Null.

- **Starten eines veröffentlichten Runbooks durch PowerShell -Cmdlets und Zuweisen von Parametern**

    - **Azure Service Management-Cmdlets:** Sie können ein Automation-Runbook, das in einer Standardressourcengruppe erstellt wurde, mit „[Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx)” starten.

    **Beispiel:**

      ``` $params = @{„VMName“=„WSVMClassic“; „ServiceName“=„WSVMClassicSG“}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```

    - **Azure Resource Manager-Cmdlets:** Sie können ein Automation-Runbook, das in einer Ressourcengruppe erstellt wurde, mit [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) starten.


    **Beispiel:**

      ``` $params = @{„VMName“=„WSVMClassic“;„ServiceName“=„WSVMClassicSG“}

        Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE] Wenn Sie ein Runbook mit PowerShell-Cmdlets starten, wird der Standardparameter „**MicrosoftApplicationManagementStartedBy**” mit dem Wert „**PowerShell**” erstellt. Sie können diesen Parameter im Blatt „**Auftragsdetails**” ansehen.

- **Starten eines Runbooks mit dem SDK und Zuweisen von Parametern**

    - **Azure Service Management-Methode:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs) ansehen.  

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

    - **Azure Resource Manager- Methode:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs) ansehen.  

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

Um diese Methode zu starten, erstellen Sie ein Wörterbuch, um die Runbook-Parameter „**VMName**“ und „**ServiceName**“ und deren Werte zu speichern. Starten Sie jetzt das Runbook. Unten befindet sich ein C#-Codeausschnitt um die oben definierte Methode aufzurufen.

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

- **Starten eines Runbooks mit REST-API und Zuweisen von Parametern**

Ein Runbookauftrag kann mit der REST-API von Azure Automation erstellt und gestartet werden, wobei die **PUT**-Methode mit der folgenden Anforderungs-URI verwendet wird.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

Ersetzen Sie in der Anforderungs-URI die folgenden Parameter:

* **subscription-id**: Ihre Azure-Abonnement-ID.  
* **cloud-service-name:** Der Name des Clouddiensts,an den die Anforderung geschickt werden soll.  
* **automation-account-name**: Name Ihres Automation-Kontos, das im angegebenen Clouddienst gehostet wird.  
* **job-id**: Die GUID für den Auftrag. Die GUID in PowerShell kann mit dem Befehl **[GUID]::NewGuid().ToString()** erstellt werden.

Um Parameter an den Runbookauftrag zu übergeben, verwenden Sie den Anforderungstext. Die folgenden zwei Eigenschaften werden im JSON-Format benötigt:

* **Runbookname**-- Erforderlich. Der Name des Runbooks für den zu startenden Auftrag.  
* **Runbookparameter**--Optional. Ein Wörterbuch der Parameterliste im Format (Name, Wert), wobei der Name vom Typ „Zeichenfolge“ sein sollte, und als Wert jeder gültige JSON-Wert infrage kommt.

Wenn Sie das zuvor erstellte Runbook „**Get-AzureVMTextual**” mit „**VMName**” und „**ServiceName**” als Parameter starten wollen, verwenden Sie das folgende JSON-Format für den Anforderungstext.

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

Wenn der Auftrag erfolgreich erstellt wurde, erhalten Sie als Antwort den HTTP-Statuscode 201. Weitere Informationen zu den Antwortheadern und dem Antworttext finden Sie im Artikel [Erstellen eines Runbooks mit REST-API.](https://msdn.microsoft.com/library/azure/mt163849.aspx).

### Testen eines Runbooks und Zuweisen von Parametern

Wenn Sie mithilfe der Testoption [die Entwurfsversion Ihres Runbooks testen](automation-testing-runbook.md), öffnet sich das Blatt „**Testen**” und Sie können die Werte für Ihre gerade erstellten Parameter konfigurieren.

![Testen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### Verknüpfen eines Zeitplans mit einem Runbook und Zuweisen von Parametern

Sie können einen [Zeitplan mit Ihrem Runbook verknüpfen](automation-scheduling-a-runbook.md),dass Ihr Runbook zu einem bestimmten Zeitpunkt startet. Sie weisen Eingabeparameter bei der Erstellung des Zeitplanes zu, und das Runbook verwendet diese Werte wenn es vom Zeitplan gestartet wird. Sie können den Zeitplan erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Planen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Erstellen eines Webhooks für ein Runbook, und Zuweisen von Parametern

Sie können einen [Webhook](automation-webhooks.md) für Ihr Runbook erstellen und Runbookeingabeparameter konfigurieren. Sie können den Webhook erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Erstellen eines Webhooks und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Wenn Sie einen Webhook verwenden, um ein Runbook ausführen zu lassen, wird der vordefinierte Eingabeparameter „**[Webhookdata](automation-webhooks.md#details-of-a-webhook)**” zusammen mit den von Ihnen definierten Eingabeparametern gesendet. Klicken Sie für die Anzeige weiterer Details zu **WebhookData**-Parametern.

![WebhookData-Parameter](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## Nächste Schritte

- Weitere Informationen zu Runbookeingabe und -ausgabe finden Sie unter [Azure Automation: Runbookeingabe, -ausgabe und geschachtelte Runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
- Weitere Informationen zu den verschieden Methoden ein Runbook zu starten finden Sie unter [Starten eines Runbooks](automation-starting-a-runbook.md).
- Informationen zum Bearbeiten eines Textrunbooks finden Sie unter [Bearbeiten von Textrunbooks](automation-edit-textual-runbook.md).
- Informationen zum Bearbeiten eines grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

<!---HONumber=AcomDC_0224_2016-->