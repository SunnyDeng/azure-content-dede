## Bereitstellen mit PowerShell

1. Melden Sie sich bei Ihrem Azure-Konto an.

          Add-AzureAccount

   Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl die Informationen zu Ihrem Konto zurück.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Wechseln Sie zum AzureResourceManager-Modul.

          Switch-AzureMode AzureResourceManager

4. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den Befehl **New-AzureResourceGroupDeployment** aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter. 
   
   Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:
   
   - Verwenden Sie Inlineparameter.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Verwenden Sie ein Parameterobjekt.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Verwenden Sie eine Parameterdatei.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Abrufen von Informationen über Fehler bei der Bereitstellung.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Abrufen detaillierter Informationen über Fehler bei der Bereitstellung.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

<!---HONumber=July15_HO4-->