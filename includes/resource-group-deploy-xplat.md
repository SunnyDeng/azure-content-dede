## Bereitstellen über die Azure-Befehlszeilenschnittstelle

1. Melden Sie sich bei Ihrem Azure-Konto an.

        azure login

  Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl das Ergebnis der Anmeldung zurück.

        ...
        info:    login command OK

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        azure account set <YourSubscriptionNameOrId>

3. Wechseln Sie zum AzureResourceManager-Modul.

        azure config mode arm

   Der neue Modus wird bestätigt.

        info:     New mode is arm

4. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein.

        azure group create -n ExampleResourceGroup -l "West US"

   Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den folgenden Befehl aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter.

   Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:

   - Verwenden Sie Inlineparameter und eine lokale Vorlage.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Verwenden Sie Inlineparameter und einen Link zu einer Vorlage.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Verwenden Sie eine Parameterdatei.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Abrufen von Informationen über die aktuelle Bereitstellung.

         azure group log show -l ExampleResourceGroup

7. Abrufen detaillierter Informationen über Fehler bei der Bereitstellung.

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=62-->