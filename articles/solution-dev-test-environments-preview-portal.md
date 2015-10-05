<properties
   pageTitle="Entwicklungs- und Testumgebungen | Microsoft Azure"
   description="Enthält Informationen zum Verwenden von Azure-Ressourcen-Manager-Vorlagen zum schnellen und konsistenten Erstellen und Löschen von Entwicklungs- und Testumgebungen."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/17/2015"
   ms.author="jdial"/>

# Entwicklungs- und Testumgebungen in Microsoft Azure

Vor ihrer Bereitstellung in der Produktion werden benutzerdefinierte Anwendungen in der Regel in mehreren Entwicklungs- und Testumgebungen bereitgestellt. Wenn Umgebungen lokal erstellt werden, werden Computingressourcen entweder erworben oder für jede Umgebung pro Anwendung zugeordnet. Die Umgebungen enthalten häufig mehrere physische oder virtuelle Computer mit speziellen Konfigurationen, die manuell oder über komplexe Automatisierungsskripts bereitgestellt werden. Bereitstellungen nehmen oft Stunden in Anspruch und führen in den verschiedenen Umgebungen zu inkonsistenten Konfigurationen.

## Szenario ##

Wenn Sie die Entwicklungs- und Testumgebungen in Microsoft Azure bereitstellen, zahlen Sie nur für die Ressourcen, die Sie verwenden. In diesem Artikel wird erläutert, wie schnell und konsistent Sie Entwicklungs- und Testumgebungen mithilfe von Vorlagen und Parameterdateien aus Azure-Ressourcen-Manager erstellen, verwalten und löschen können, wie unten gezeigt.

![Szenario](./media/solution-dev-test-environments-preview-portal/scenario.png)

Oben werden drei Entwicklungs- und Testumgebungen gezeigt. Jede verfügt über eine Webanwendung und eine SQL-Datenbank. Die Namen der Anwendung und der Datenbank unterscheiden sich in den einzelnen Umgebungen. Dieser Artikel erläutert, wie Sie mithilfe einer Vorlage dieselbe Ressource in mehreren Umgebungen bereitstellen und wie Sie anhand von eindeutigen Parameterdateien unterschiedliche Konfigurationen für die Ressourcen in den verschiedenen Umgebungen festlegen können.

Wenn Sie nicht mit den Konzepten des Azure-Ressourcen-Managers vertraut sind, empfiehlt es sich, vor diesem Artikel den Artikel [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md) zu lesen.

Vielleicht befolgen Sie als Erstes die in diesem Artikel aufgeführten Schritte, ohne die referenzierten Artikel zu lesen, um schnell Erfahrung mit der Verwendung von Azure-Ressourcen-Manager-Vorlagen zu erlangen. Sicherlich werden viele Fragen auftauchen, während Sie die einzelnen Schritte nachvollziehen. Nachdem Sie die Schritte einmal durchgegangen sind, finden Sie Antworten auf die meisten Ihrer Fragen durch weiteres Experimentieren mit den Schritten und durch Lesen der referenzierten Artikel.

## Planen der Nutzung von Azure-Ressourcen
Nachdem Sie einen allgemeinen Entwurf für Ihre Anwendung besitzen, können Sie Folgendes definieren:

- Die in Ihrer Anwendung enthaltenen Azure-Ressourcen. Sie können Ihre Anwendung erstellen und als Azure-Web-App mit einer Azure SQL-Datenbank bereitstellen. Sie können Ihre Anwendung mit PHP und MySQL oder mit IIS und SQL Server oder anderen Komponenten auf virtuellen Computern erstellen. Der Artikel [Azure App Service, Cloud Services und Virtual Machines im Vergleich](app-service-web/choose-web-site-cloud-service-vm.md) erleichtert Ihnen die Entscheidung, welche Azure-Ressourcen Sie für Ihre Anwendung nutzen möchten.
- Die Anforderungen zum Servicelevel, die die Anwendung erfüllen soll, z. B. Verfügbarkeit, Sicherheit und Skalierung.

## Verwenden von Azure-Ressourcen-Manager-Vorlagen
Eine Azure-Ressourcen-Manager-Vorlage definiert alle Azure-Ressourcen, die von Ihrer Anwendung genutzt werden. Einige Vorlagen sind bereits vorhanden, die Sie direkt im Azure-Vorschauportal bereitstellen oder in ein Quellcodeverwaltungssystem mit Ihrem Anwendungscode herunterladen, ändern und speichern können. Die Wahrscheinlichkeit ist hoch, dass bereits eine Vorlage mit den Ressourcen vorhanden ist, die Sie in Ihrer Anwendung verwenden möchten. Sie finden eine Liste der verfügbaren Vorlagen im GitHub-Repository [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/) .

In der Liste sehen Sie einen Ordner "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Da viele benutzerdefinierte Anwendungen eine Webanwendung und eine SQL-Datenbank enthalten, wird diese Vorlage im weiteren Verlauf dieses Artikels als Beispiel verwendet, um Ihnen die Nutzung von Vorlagen zu erläutern. Es würde über den Rahmen dieses Artikels hinausgehen, alle von dieser Vorlage erstellten und konfigurierten Elemente vollständig zu erklären. Wenn Sie sie jedoch zum Erstellen echter Umgebungen in Ihrer Organisation verwenden möchten, sollten Sie den Artikel [Bereitstellen einer Web-App mit einer SQL-Datenbank](app-service-web/app-service-web-arm-with-sql-database-provision.md) lesen, um sich umfassend mit dieser Vorlage vertraut zu machen.

> [AZURE.NOTE]Sie können die Vorlage direkt in Azure bereitstellen, indem Sie im Artikel [Bereitstellen einer Web-App mit einer SQL-Datenbank](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) auf die Schaltfläche „In Azure bereitstellen“ klicken. Möglicherweise ist dies hilfreich, um Informationen über Vorlagen zu erhalten. Es ermöglicht Ihnen jedoch nicht die Bearbeitung, Versionierung und Speicherung Ihrer Vorlage und der Parameterwerte mit dem Anwendungscode. Die Schritte in diesem Artikel erläutern, wie Sie Ihre Vorlage und die Parameterwerte mit dem Anwendungscode speichern und versionieren können.

  **Schritt 1:** Zeigen Sie den Inhalt der Datei [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) im Ordner "201-web-app-sql-database" an. Dies ist die Azure-Ressourcen-Manager-Vorlagendatei. Klicken Sie im Anzeigemodus auf die Schaltfläche [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json). Wählen Sie mit der Maus den gesamten Inhalt dieser Datei aus, und speichern Sie ihn auf Ihrem Computer in einer Datei namens "TestApp1-Template.json".

Die Vorlagendatei enthält einen Abschnitt "resources", in dem die von dieser Vorlage erstellten Azure-Ressourcen definiert werden. Neben anderen Ressourcentypen erstellt diese Vorlage [Azure-Web-App](app-service-web/app-service-web-overview.md)- und [Azure SQL-Datenbankressourcen](sql-database/sql-database-technical-overview.md).

Außerdem sehen Sie einen Abschnitt "parameters", in dem die Parameter definiert sind, mit denen die einzelnen Ressourcen konfiguriert werden können. Einige der in der Vorlage angegebenen Parameter besitzen defaultValue-Eigenschaften, andere jedoch nicht. Bei der Bereitstellung von Azure-Ressourcen mit einer Vorlage müssen Sie Werte für alle Parameter angeben, die nicht über defaultValue-Eigenschaften verfügen. Wenn Sie keine Werte für Parameter mit defaultValue-Eigenschaften angeben, wird der für den defaultValue-Parameter in der Vorlage angegebene Wert verwendet.

Eine Vorlage definiert, welche Azure-Ressourcen erstellt werden und mit welchen Parametern die einzelnen Ressourcen konfiguriert werden können. In der Regel sollten Sie in jeder Entwicklungs- und Testumgebung dieselben Ressourcen erstellen. Weitere Informationen über Vorlagen und über das Entwerfen Ihrer eigenen Vorlagen finden Sie im Artikel [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

## Erstellen von Parameterdateien

Wahrscheinlich sollen in jeder Umgebung dieselben Azure-Ressourcen erstellt werden, aber möglicherweise soll die Konfiguration der Ressourcen in jeder Umgebung unterschiedlich sein. An dieser Stelle kommen Parameterdateien ins Spiel.

  **Schritt 2:** Zeigen Sie den Inhalt der Datei [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) im Ordner "201-web-app-sql-database" an. Dies ist die Parameterdatei für die Vorlagendatei, die Sie in Schritt 1 gespeichert haben. Klicken Sie im Anzeigemodus auf die Schaltfläche [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json). Wählen Sie mit der Maus den gesamten Inhalt dieser Datei aus, und speichern Sie ihn unter den folgenden Namen in drei separaten Dateien auf Ihrem Computer:

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Schritt 3:** Bearbeiten Sie die in Schritt 2 erstellten Parameterdateien für die Entwicklungs- und Testumgebung mit einem Text- oder JSON-Editor, und ersetzen Sie die vorhandenen Werte in den Dateien durch die unten aufgeführten Werte.

  --TestApp1-Parameters-Development.json--

| Parameter | Beschreibung |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | USA (Mitte) |
| **serverName** | testapp1dev |
| **serverLocation** | USA (Mitte) |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Parameter | Beschreibung |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | USA (Mitte) |
| **serverName** | testapp1test |
| **serverLocation** | USA (Mitte) |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

In Schritt 6 werden diese Parameterdateien verwendet, um eindeutige Konfigurationen für die Azure-Web-App- und die Azure SQL-Datenbank-Ressourcen in den Entwicklungs- und Testumgebungen zu erstellen.

 **Schritt 4:** Bearbeiten Sie die Parameterdatei "TestApp1-Parameters-Pre-Production.json", die Sie in Schritt 2 erstellt haben. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code:

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

In der oben genannten Datei mit Präproduktionsparametern wurden die Parameter **sku** und **requestedServiceObjectiveName** *hinzugefügt*, in den Entwicklungs- und Testparameterdateien hingegen nicht. Der Grund dafür ist, dass für diese Parameter in der Vorlage Standardwerte definiert sind, die in den Entwicklungs- und Testumgebungen verwendet werden. In der Präproduktionsumgebung werden für diese Parameter hingegen keine Standardwerte verwendet,

weil Werte für diese Parameter getestet werden sollen, die Sie für Ihre Produktionsumgebung eventuell bevorzugen. Diese Parameter beziehen sich alle auf die Azure-[Web-App-Hostingpläne](http://azure.microsoft.com/pricing/details/app-service/) oder **sku** und Azure [SQL-Datenbank](http://azure.microsoft.com/pricing/details/sql-database/) oder **requestedServiceObjectiveName**, die von der Anwendung verwendet werden. Verschiedene SKUs und Dienstzielnamen haben unterschiedliche Preise und Funktionen und unterstützen unterschiedliche Servicelevelmetriken.

In der folgenden Tabelle sind die in der Vorlage angegebenen Standardwerte für diese Parameter sowie die Werte aufgeführt, die anstelle der Standardwerte in der Präproduktions-Parameterdatei verwendet werden.

| Parameter | Standardwert | Wert in der Parameterdatei |
|---|---|---|
| **sku** | Kostenlos | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Erstellen von Umgebungen
Alle Azure-Ressourcen müssen innerhalb einer [Azure-Ressourcengruppe](azure-portal/resource-group-portal.md) erstellt werden. Mit Ressourcengruppen können Sie Azure-Ressourcen so gruppieren, dass sie gemeinsam verwaltet werden können. Ressourcengruppen können [Berechtigungen](azure-portal/resource-group-rbac.md) zugewiesen werden, sodass bestimmte Personen in Ihrer Organisation die Ressourcengruppen und die darin enthaltenen Ressourcen erstellen, ändern, löschen oder anzeigen können. Warnungen und Abrechnungsinformationen für Ressourcen in der Ressourcengruppe können im [Azure-Vorschauportal](https://portal.azure.com) angezeigt werden. Ressourcengruppen werden an einem Azure-[Standort](http://azure.microsoft.com/regions/) erstellt. In diesem Artikel werden alle Ressourcen am Standort "USA, Mitte" erstellt. Wenn Sie damit beginnen, tatsächliche Umgebungen zu erstellen, wählen Sie den Standort aus, der Ihren Anforderungen am besten entspricht.

  **Schritt 5:** Erstellen Sie Ressourcengruppen für die Entwicklungs- und Testumgebungen mithilfe einer der folgenden Methoden. Mit beiden Methoden wird genau dasselbe Ergebnis erzielt.

  **Methode 1:** Azure-Befehlszeilenschnittstelle (CLI)

  Stellen Sie sicher, dass die Befehlszeilenschnittstelle auf einem Computer unter Windows, OS X oder Linux [installiert](xplat-cli-install.md) ist und dass Ihre Organisations-ID mit Ihrem Azure-Abonnement [verbunden](xplat-cli-connect.md) ist. Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um die Ressourcengruppe für die Entwicklungsumgebung zu erstellen.

	azure group create "TestApp1-Development" "Central US"

  Bei Erfolg gibt der Befehl Folgendes zurück:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Um die Ressourcengruppe für die Testumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	azure group create "TestApp1-Test" "Central US"

  Um die Ressourcengruppe für die Präproduktionsumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	azure group create "TestApp1-Pre-Production" "Central US"

  **Methode 2:** PowerShell

  Stellen Sie sicher, dass PowerShell auf einem Windows-Computer installiert und mit Ihrem Abonnement verbunden ist, wie im Artikel [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md) ausführlich beschrieben wird. Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um die Ressourcengruppe für die Entwicklungsumgebung zu erstellen.

	New-AzureResourceGroup -Name TestApp1-Development -Location "Central US"

  Bei Erfolg gibt der Befehl Folgendes zurück:

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Um die Ressourcengruppe für die Testumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	New-AzureResourceGroup -Name TestApp1-Test -Location "Central US"

  Um die Ressourcengruppe für die Präproduktionsumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	New-AzureResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Schritt 6:** Stellen Sie den Ressourcengruppen für die einzelnen Umgebungen Azure-Ressourcen bereit. Verwenden Sie dazu die Vorlagendatei für die Anwendung und die Parameterdateien für die einzelnen Umgebungen anhand einer der folgenden Methoden. Mit beiden Methoden wird genau dasselbe Ergebnis erzielt.

  **Methode 1:** Azure-Befehlszeilenschnittstelle (CLI)

  Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Entwicklungsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  Bei Erfolg gibt der Befehl Folgendes zurück:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  Wenn der Befehl nicht erfolgreich ausgeführt wird, lösen Sie alle Fehlermeldungen auf, und versuchen Sie es erneut. Häufig werden Probleme durch Parameterwerte verursacht, die nicht den Namenseinschränkungen für Azure-Ressourcen entsprechen. Weitere Tipps zur Problembehandlung finden Sie im Artikel [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](virtual-machines/resource-group-deploy-debug.md).

  Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Testumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Präproduktionsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Methode 2:** PowerShell

  Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Entwicklungsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  Bei Erfolg gibt der Befehl Folgendes zurück:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Wenn der Befehl nicht erfolgreich ausgeführt wird, lösen Sie alle Fehlermeldungen auf, und versuchen Sie es erneut. Häufig werden Probleme durch Parameterwerte verursacht, die nicht den Namenseinschränkungen für Azure-Ressourcen entsprechen. Weitere Tipps zur Problembehandlung finden Sie im Artikel [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](virtual-machines/resource-group-deploy-debug.md).

  Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Testumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Präproduktionsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Die Vorlage und die Parameterdateien können mit Ihrem Anwendungscode in einem Quellcodeverwaltungssystem versioniert und verwaltet werden. Sie können die oben aufgeführten Befehle auch in Skriptdateien speichern und auch mit Ihrem Code ablegen.

## Warten von Umgebungen
Während der Entwicklung kann die Konfiguration von Azure-Ressourcen in den unterschiedlichen Umgebungen absichtlich oder versehentlich inkonsistent geändert werden. Dies kann während des Anwendungsentwicklungszyklus unnötigerweise zur Problembehandlung und Problembehebung führen.

  **Schritt 7:** Ändern Sie die Umgebungen. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com), und melden Sie sich mit demselben Konto an, das Sie zum Ausführen der oben aufgeführten Schritte verwendet haben. Wie in der folgenden Abbildung gezeigt wird, klicken Sie auf "Alle durchsuchen" --> "Ressourcengruppen" (möglicherweise müssen Sie auf dem Blatt "Durchsuchen" nach unten scrollen, um die Ressourcengruppen anzuzeigen). Sie sehen alle drei Ressourcengruppen, die Sie mithilfe einer der Methoden aus den vorherigen Schritten erstellt haben. Klicken Sie auf die Ressourcengruppe "TestApp1-Development", wie unten dargestellt.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal1.png)

  Nach dem Klicken auf die Ressourcengruppe "TestApp1-Development" sehen Sie das Blatt, auf dem die von der Vorlage erstellten Ressourcen in der Ressourcengruppenbereitstellung aufgeführt werden, die Sie in einem vorherigen Schritt abgeschlossen haben. Löschen Sie die Web-App-Ressource "TestApp1Dev", indem Sie auf "TestApp1Dev" --> "Löschen" klicken, wie unten dargestellt.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Klicken Sie auf "Ja", wenn das Portal Sie auffordert, den Löschvorgang für die Ressource zu bestätigen. Die Ressourcengruppe zeigt jetzt nicht mehr die vorgesehenen Inhalte. Sie können weiter experimentieren, indem Sie mehrere Ressourcen aus mehreren Ressourcengruppen löschen oder sogar Konfigurationseinstellungen für einige der Ressourcen ändern.

> [AZURE.NOTE]Anstatt das Azure-Vorschauportal zum Löschen einer Ressource aus einer Ressourcengruppe zu verwenden, können Sie den PowerShell-Befehl [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) oder den Befehl "azure resource delete" aus der Befehlszeilenschnittstelle einsetzen, um dieselbe Aufgabe zu erfüllen.

  **Schritt 8:** Stellen Sie die Umgebungen für die Ressourcengruppen über dieselben Befehle bereit, die Sie in Schritt 6 verwendet haben, aber ersetzen Sie "Deployment1" durch "Deployment2". Wie im Abschnitt "Zusammenfassung" der folgenden Abbildung gezeigt, sind alle Ressourcen aus der Vorlage erneut in der Ressourcengruppe "TestApp1-Development" vorhanden. Einer der Vorteile der Bereitstellung Ihrer Umgebungen mit Azure-Ressourcen-Manager-Vorlagen besteht darin, dass Sie die Umgebungen einfach zu einem beliebigen Zeitpunkt in einem bekannten Zustand erneut bereitstellen können.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Wenn Sie auf den Text unter "Letzte Bereitstellung" im Bild klicken, wird ein Blatt angezeigt, auf dem der Bereitstellungsverlauf für die Ressourcengruppe zu sehen ist. Da Sie den Namen "Deployment1" für die erste Bereitstellung und "Deployment2" für die zweite Bereitstellung verwendet haben, gibt es zwei Einträge. Durch Klicken auf eine Bereitstellung wird ein Blatt mit den Ergebnissen für jede Bereitstellung angezeigt.

## Löschen von Umgebungen
Sobald eine Umgebung fertig ist, sollten Sie sie löschen, damit keine Nutzungsgebühren für Azure-Ressourcen entstehen, die Sie nicht mehr verwenden. Das Löschen von Umgebungen ist sogar noch einfacher als das Erstellen. In den vorherigen Schritten wurden die einzelnen Azure-Ressourcengruppen für jede Umgebung erstellt. Wenn Sie eine Ressourcengruppe löschen, werden alle darin enthaltenen Ressourcen ebenfalls gelöscht. Dementsprechend werden anhand einer der folgenden Methoden die Umgebungen (Ressourcengruppen) zusammen mit allen darin enthaltenen Azure-Ressourcen gelöscht, die Sie zuvor bereitgestellt haben.

  **Schritt 9:** Löschen Sie die Umgebungen mithilfe einer der folgenden Methoden. Mit beiden Methoden wird genau dasselbe Ergebnis erzielt.

  **Methode 1: Azure-Befehlszeilenschnittstelle**

  Geben Sie an der Befehlszeilenschnittstelle Folgendes ein.

	azure group delete "TestApp1-Development"

  Der Befehl gibt Folgendes zurück, wenn Sie nach Aufforderung "y" eingeben:

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  Geben Sie an der Befehlszeilenschnittstelle Folgendes ein, um die verbleibenden Umgebungen zu löschen:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Methode 2:** PowerShell

  Geben Sie an einer PowerShell-Eingabeaufforderung Folgendes ein:

	Remove-AzureResourceGroup -Name TestApp1-Development

  Der Befehl gibt Folgendes zurück, wenn Sie nach Aufforderung "y" eingeben:

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  Geben Sie an einer PowerShell-Eingabeaufforderung Folgendes ein, um die verbleibenden Umgebungen zu löschen:

	Remove-AzureResourceGroup -Name TestApp1-Test
	Remove-AzureResourceGroup -Name TestApp1-Pre-Production

Unabhängig von der verwendeten Methode sind die Ressourcengruppen und alle darin enthaltenen Ressourcen nach dem Ausführen der Befehle nicht länger vorhanden, sodass keine weiteren Kosten für die Ressourcen entstehen.

Um die Kosten für die Nutzung von Azure-Ressourcen bei der Anwendungsentwicklung auf ein Mindestmaß zu reduzieren, können Sie mit [Azure Automation](automation/automation-intro.md) Aufträge für folgende Aufgaben planen:

- Beenden von virtuellen Computern am Ende des Tages und Neustarten der Computer zu Beginn des Tages.
- Löschen ganzer Umgebungen am Ende des Tages und Neuerstellen der Umgebungen zu Beginn des Tages.
 
Jetzt wissen Sie, wie einfach es ist, Entwicklungs- und Testumgebungen zu erstellen, zu verwalten und zu löschen. Mehr zu den Prozessen, die Sie soeben durchgeführt haben, erfahren Sie durch weiteres Experimentieren mit der oben beschriebenen Schritten und durch Lesen des Referenzmaterials, auf das in diesem Artikel verwiesen wird.

## Nächste Schritte

- [Delegieren von Administratorberechtigungen](role-based-access-control-configure.md) an verschiedene Ressourcen in den einzelnen Umgebungen durch Zuweisen von Microsoft Azure AD-Gruppen oder -Benutzern zu bestimmten Rollen, die eine Teilmenge der Vorgänge für Azure-Ressourcen durchführen können.
- [Zuweisen von Tags](resource-group-using-tags.md) zu den Ressourcengruppen für jede Umgebung und/oder die einzelnen Ressourcen. Sie können den Ressourcengruppen ein Tag "Environment" hinzufügen und dessen Wert auf den Umgebungsnamen festlegen. Tags können besonders hilfreich sein, wenn Sie Ressourcen zur Abrechnung oder Verwaltung organisieren müssen.
- Überwachen von Warnungen und Abrechnungen für Ressourcen aus Ressourcengruppen im [Azure-Vorschauportal](https://portal.azure.com).

## Zusätzliche Ressourcen

- [Erstellen und Bereitstellen von Azure-Ressourcen-Manager-Vorlagen in Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) mit Azure SDK 2.6-Installation.
- Erstellen Ihrer Anwendung mit [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) oder [Web Matrix](http://www.microsoft.com/web/webmatrix/).
- [Bereitstellen einer Web-App](app-service-web/web-sites-deploy.md) für die von Ihnen erstellten Umgebungen.
- Verwenden von [Release Management für Visual Studio](http://msdn.microsoft.com/Library/vs/alm/Release/overview) zum Erstellen verwalteter, kontinuierlicher Bereitstellungspipelines für schnelle, einfache und häufige Releases.
- Anfordern einer Einladung für die Vorschau von [Azure Dev/Test Lab](http://azure.microsoft.com/campaigns/devtest-lab/). Damit können Sie Ihre Entwicklungs- und Testumgebungen mithilfe von Vorlagen verwalten und Kontingente und Richtlinien zur Verwendung in Ihrer Organisation konfigurieren.

<!---HONumber=Sept15_HO4-->