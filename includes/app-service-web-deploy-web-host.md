### App Service-Plan

Erstellt den Dienstplan zum Hosten der Web-App. Geben Sie den Namen des Plans über den **hostingPlanName**-Parameter an. Der Speicherort des Plans ist mit dem für die Web-App identisch. Der Tarif und die Workergröße werden in den Parametern **sku** und **workerSize** angegeben.

    {
       "apiVersion":"2015-04-01",
       "name":"[parameters('hostingPlanName')]",
       "type":"Microsoft.Web/serverfarms",
       "location":"[parameters('siteLocation')]",
       "properties":{
         "name":"[parameters('hostingPlanName')]",
         "sku":"[parameters('sku')]",
         "workerSize":"[parameters('workerSize')]",
         "numberOfWorkers":1
       }
    }

<!---HONumber=July15_HO4-->