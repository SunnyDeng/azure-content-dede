<properties
   pageTitle="Entwicklungs- und Testumgebungen | Microsoft Azure"
   description="Enthält Informationen zum Verwenden von Azure-Ressourcen-Manager-Vorlagen zum schnellen und konsistenten Erstellen und Löschen von Entwicklungs- und Testumgebungen."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="jdial"/>

# Entwicklungs- und Testumgebungen in Microsoft Azure

Vor ihrer Bereitstellung in der Produktion werden benutzerdefinierte Anwendungen in der Regel in mehreren Entwicklungs- und Testumgebungen bereitgestellt. Wenn Umgebungen lokal erstellt werden, werden Computingressourcen entweder erworben oder für jede Umgebung pro Anwendung zugeordnet. Die Umgebungen enthalten häufig mehrere physische oder virtuelle Computer mit speziellen Konfigurationen, die manuell oder über komplexe Automatisierungsskripts bereitgestellt werden. Bereitstellungen nehmen oft Stunden in Anspruch und führen in den verschiedenen Umgebungen zu inkonsistenten Konfigurationen.

## Szenario ##

Wenn Sie die Entwicklungs- und Testumgebungen in Microsoft Azure bereitstellen, zahlen Sie nur für die Ressourcen, die Sie verwenden. In diesem Artikel wird erläutert, wie schnell und konsistent Sie Entwicklungs- und Testumgebungen mithilfe von Vorlagen und Parameterdateien aus Azure-Ressourcen-Manager erstellen, verwalten und löschen können, wie unten gezeigt.

![Szenario](./media/solution-dev-test-environments/scenario.png)

Oben werden drei Entwicklungs- und Testumgebungen gezeigt. Jede verfügt über Webanwendungs- und SQL-Datenbankressourcen, die in einer Vorlagendatei angegeben sind. Die Namen der Anwendung und Datenbank sind in jeder Umgebung anders und werden jeweils in eindeutigen Parameterdateien angegeben.

Wenn Sie nicht mit den Konzepten des Azure-Ressourcen-Managers vertraut sind, empfiehlt es sich, vor diesem Artikel den Artikel [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md) zu lesen.

Vielleicht befolgen Sie als Erstes die in diesem Artikel aufgeführten Schritte, ohne die referenzierten Artikel zu lesen, um schnell Erfahrung mit der Verwendung von Azure-Ressourcen-Manager-Vorlagen zu erlangen. Nachdem Sie die Schritte einmal durchgegangen sind, finden Sie Antworten auf die meisten Fragen, die sich beim ersten Durcharbeiten ergeben haben, indem Sie weiter mit den Schritten experimentieren und die angegebenen Artikel lesen.

## Planen der Nutzung von Azure-Ressourcen
Nachdem Sie einen allgemeinen Entwurf für Ihre Anwendung besitzen, können Sie Folgendes definieren:

- Die in Ihrer Anwendung enthaltenen Azure-Ressourcen. Sie können Ihre Anwendung erstellen und als Azure-Web-App mit einer Azure SQL-Datenbank bereitstellen. Sie können Ihre Anwendung mit PHP und MySQL oder mit IIS und SQL Server oder anderen Komponenten auf virtuellen Computern erstellen. Der Artikel [Azure App Service, Cloud Services und Virtual Machines im Vergleich](app-service-web/choose-web-site-cloud-service-vm.md) erleichtert Ihnen die Entscheidung, welche Azure-Ressourcen Sie für Ihre Anwendung nutzen möchten.
- Die Anforderungen zum Servicelevel, die die Anwendung erfüllen soll, z. B. Verfügbarkeit, Sicherheit und Skalierung.

## Herunterladen einer vorhandenen Vorlage
Eine Azure-Ressourcen-Manager-Vorlage definiert alle Azure-Ressourcen, die von Ihrer Anwendung genutzt werden. Es sind bereits einige Vorlagen vorhanden, die Sie direkt im Azure-Portal bereitstellen oder in ein Quellcodeverwaltungssystem mit Ihrem Anwendungscode herunterladen, ändern und speichern können. Führen Sie die unten angegebenen Schritte aus, um eine vorhandene Vorlage herunterzuladen.

1. Sie können die vorhandenen Vorlagen im GitHub-Repository unter [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates/) (Azure-Schnellstartvorlagen) durchsuchen. In der Liste sehen Sie einen Ordner "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Da viele benutzerdefinierte Anwendungen eine Webanwendung und eine SQL-Datenbank enthalten, wird diese Vorlage im weiteren Verlauf dieses Artikels als Beispiel verwendet, um Ihnen die Nutzung von Vorlagen zu erläutern. Es würde über den Rahmen dieses Artikels hinausgehen, alle von dieser Vorlage erstellten und konfigurierten Elemente vollständig zu erklären. Wenn Sie sie jedoch zum Erstellen echter Umgebungen in Ihrer Organisation verwenden möchten, sollten Sie den Artikel [Bereitstellen einer Web-App mit einer SQL-Datenbank](app-service-web/app-service-web-arm-with-sql-database-provision.md) lesen, um sich umfassend mit dieser Vorlage vertraut zu machen.
2. Klicken Sie im Ordner „201-web-app-sql-database“ auf die Datei [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json), um ihren Inhalt anzuzeigen. Dies ist die Azure-Ressourcen-Manager-Vorlagendatei. 
3. Klicken Sie im Anzeigemodus auf die Schaltfläche [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json). 
4. Wählen Sie mit der Maus den Inhalt dieser Datei aus, und speichern Sie ihn auf Ihrem Computer in einer Datei mit dem Namen „TestApp1-Template.json“. 
5. Untersuchen Sie den Vorlageninhalt, und achten Sie auf Folgendes:
 - Abschnitt **Ressourcen**: In diesem Abschnitt sind die Azure-Ressourcentypen definiert, die von dieser Vorlage erstellt werden. Neben anderen Ressourcentypen erstellt diese Vorlage [Azure-Web-App](app-service-web/app-service-web-overview.md)- und [Azure SQL-Datenbankressourcen](sql-database/sql-database-technical-overview.md). Falls Sie es vorziehen, Web- und SQL-Server in virtuellen Maschinen auszuführen und zu verwalten, können Sie die Vorlage „[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)“ oder „[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)“ verwenden. Die Anleitung in diesem Artikel basiert aber auf der Vorlage [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database).
 - Abschnitt **Parameter**: In diesem Abschnitt sind die Parameter definiert, mit denen die einzelnen Ressourcen konfiguriert werden können. Einige der in der Vorlage angegebenen Parameter besitzen defaultValue-Eigenschaften, andere jedoch nicht. Bei der Bereitstellung von Azure-Ressourcen mit einer Vorlage müssen Sie Werte für alle Parameter angeben, die nicht über in der Vorlage angegebene defaultValue-Eigenschaften verfügen. Wenn Sie keine Werte für Parameter mit defaultValue-Eigenschaften angeben, wird der für den defaultValue-Parameter in der Vorlage angegebene Wert verwendet.

Eine Vorlage definiert, welche Azure-Ressourcen erstellt werden und mit welchen Parametern die einzelnen Ressourcen konfiguriert werden können. Weitere Informationen über Vorlagen und über das Entwerfen Ihrer eigenen Vorlagen finden Sie im Artikel [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

## Herunterladen und Anpassen einer vorhandenen Parameterdatei

Sie möchten wahrscheinlich, dass in jeder Umgebung die *gleichen* Azure-Ressourcen erstellt werden, aber die Konfiguration der Ressourcen soll in jeder Umgebung vermutlich *unterschiedlich* sein. An dieser Stelle kommen Parameterdateien ins Spiel. Erstellen Sie Parameterdateien mit eindeutigen Werten in jeder Umgebung, indem Sie die unten angegebenen Schritte ausführen.

1. Zeigen Sie den Inhalt der Datei [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.parameters.json) im Ordner „201-web-app-sql-database“ an. Dies ist die Parameterdatei für die Vorlagendatei, die Sie im vorherigen Abschnitt gespeichert haben. 
2. Klicken Sie im Anzeigemodus auf die Schaltfläche [Raw](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.parameters.json). 
3. Wählen Sie mit der Maus den Inhalt dieser Datei aus, und speichern Sie ihn unter den folgenden Namen in drei separaten Dateien auf Ihrem Computer:
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. Bearbeiten Sie die Entwicklungsumgebung-Parameterdatei, die Sie in Schritt 3 erstellt haben, mit einem beliebigen Text- oder JSON-Editor. Ersetzen Sie die Werte, die in der Datei rechts von den Parameterwerten aufgeführt sind, durch die *Werte*, die hier rechts von den **Parametern** angegeben sind: 
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *USA, Mitte*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *USA, Mitte*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *durch Ihr Kennwort ersetzen*
 - **databaseName**: *testapp1devdb*

4. Bearbeiten Sie die Testumgebung-Parameterdatei, die Sie in Schritt 3 erstellt haben, mit einem beliebigen Text- oder JSON-Editor. Ersetzen Sie die Werte, die in der Datei rechts von den Parameterwerten aufgeführt sind, durch die *Werte*, die hier rechts von den **Parametern** angegeben sind:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPla*n
 - **siteLocation**: *USA, Mitte*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *USA, Mitte*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *durch Ihr Kennwort ersetzen*
 - **databaseName**: *testapp1testdb*

5. Bearbeiten Sie die Präproduktion-Parameterdatei, die Sie in Schritt 3 erstellt haben, mit einem beliebigen Text- oder JSON-Editor. Ersetzen Sie den gesamten Inhalt der Datei durch Folgendes:

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
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

weil Werte für diese Parameter getestet werden sollen, die Sie für Ihre Produktionsumgebung eventuell bevorzugen. So ist es möglich, auch diesen Test durchzuführen. Diese Parameter beziehen sich alle auf die Azure-[Web-App-Hostingpläne](https://azure.microsoft.com/pricing/details/app-service/) oder **sku** und Azure [SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/) oder **requestedServiceObjectiveName**, die von der Anwendung verwendet werden. Verschiedene SKUs und Dienstzielnamen haben unterschiedliche Preise und Funktionen und unterstützen unterschiedliche Servicelevelmetriken.

In der folgenden Tabelle sind die in der Vorlage angegebenen Standardwerte für diese Parameter sowie die Werte aufgeführt, die anstelle der Standardwerte in der Präproduktions-Parameterdatei verwendet werden.

| Parameter | Standardwert | Wert in der Parameterdatei |
|---|---|---|
| **sku** | Kostenlos | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Erstellen von Umgebungen
Alle Azure-Ressourcen müssen innerhalb einer [Azure-Ressourcengruppe](azure-portal/resource-group-portal#create-resource-group-and-resources.md) erstellt werden. Mit Ressourcengruppen können Sie Azure-Ressourcen so gruppieren, dass sie gemeinsam verwaltet werden können. Ressourcengruppen können [Berechtigungen](./active-directory/role-based-access-built-in-roles.md) zugewiesen werden, sodass bestimmte Personen in Ihrer Organisation die Ressourcengruppen und die darin enthaltenen Ressourcen erstellen, ändern, löschen oder anzeigen können. Warnungen und Abrechnungsinformationen für Ressourcen in der Ressourcengruppe können im [Azure-Portal](https://portal.azure.com) angezeigt werden. Ressourcengruppen werden in einer Azure-[Region](https://azure.microsoft.com/regions/) erstellt. In diesem Artikel werden alle Ressourcen in der Region „USA, Mitte“ erstellt. Wenn Sie damit beginnen, richtige Umgebungen zu erstellen, können Sie die Region auswählen, die Ihren Anforderungen am besten entspricht.

Erstellen Sie Ressourcengruppen für die einzelnen Umgebungen, indem Sie eines der unten angegebenen Verfahren verwenden. Mit allen Verfahren wird das gleiche Ergebnis erzielt.

###Azure-Befehlszeilenschnittstelle (CLI)

Stellen Sie sicher, dass die Befehlszeilenschnittstelle auf einem Computer unter Windows, OS X oder Linux [installiert](xplat-cli-install.md) ist und dass Ihr [Azure AD-Konto](./active-directory/active-directory-how-subscriptions-associated-directory.md) (auch als Geschäfts- oder Schulkonto bezeichnet) mit Ihrem Azure-Abonnement [verbunden](xplat-cli-connect.md) ist. Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um die Ressourcengruppe für die Entwicklungsumgebung zu erstellen.

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

###PowerShell

Stellen Sie sicher, dass Azure PowerShell 1.01 oder höher auf einem Windows-Computer installiert ist und dass für Ihr [Azure AD-Konto](./active-directory/active-directory-how-subscriptions-associated-directory.md) (auch als Geschäfts- oder Schulkonto bezeichnet) eine Verbindung mit Ihrem Abonnement besteht. Ausführliche Informationen hierzu finden Sie im Artikel [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md). Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um die Ressourcengruppe für die Entwicklungsumgebung zu erstellen.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Bei Erfolg gibt der Befehl Folgendes zurück:

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Um die Ressourcengruppe für die Testumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Um die Ressourcengruppe für die Präproduktionsumgebung zu erstellen, geben Sie den folgenden Befehl ein:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Azure-Portal

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md)-Konto (auch als Geschäfts- oder Schulkonto bezeichnet) an. Klicken Sie auf „Neu“ --> „Verwaltung“ --> „Ressourcengruppe“, und geben Sie im Feld für den Ressourcengruppennamen „TestApp1-Development“ ein. Wählen Sie Ihr Abonnement aus, und wählen Sie dann wie in der Abbildung unten dargestellt im Feld „Ressourcengruppenstandort“ die Option „USA, Mitte“. ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Klicken Sie auf die Schaltfläche „Erstellen“, um die Ressourcengruppe zu erstellen.
3. Klicken Sie auf „Durchsuchen“, führen Sie einen Bildlauf nach unten zu den Ressourcengruppen durch, und klicken Sie wie unten dargestellt auf „Ressourcengruppen“. ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Nach dem Klicken auf „Ressourcengruppen“ wird das Blatt „Ressourcengruppen“ mit Ihrer neuen Ressourcengruppe angezeigt. ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Erstellen Sie die Ressourcengruppen „TestApp1-Test“ und „TestApp1-Pre-Production“ genauso wie oben die Ressourcengruppe „TestApp1-Development“.

##Bereitstellen von Ressourcen in Umgebungen

Stellen Sie den Ressourcengruppen für die einzelnen Umgebungen Azure-Ressourcen bereit. Verwenden Sie hierzu die Vorlagendatei für die Projektmappe und die Parameterdateien für die einzelnen Umgebungen, indem Sie eines der unten angegebenen Verfahren nutzen. Mit beiden Verfahren wird das gleiche Ergebnis erzielt.

###Azure-Befehlszeilenschnittstelle (CLI)

Geben Sie an der Befehlszeilenschnittstelle den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Entwicklungsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Nachdem einige Minuten die Meldung „Auf Abschluss der Bereitstellung wird gewartet“ angezeigt wurde, gibt der Befehl dann Folgendes aus, sofern die Ausführung erfolgreich ist:

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
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
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
  
###PowerShell

Geben Sie an einer Azure PowerShell-Eingabeaufforderung (Version 1.01 oder höher) den folgenden Befehl ein, um für die Ressourcengruppe, die Sie für die Entwicklungsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Nachdem einige Minuten lang ein blinkender Cursor zu sehen war, gibt der Befehl Folgendes zurück, sofern die Ausführung erfolgreich ist:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Wenn der Befehl nicht erfolgreich ausgeführt wird, lösen Sie alle Fehlermeldungen auf, und versuchen Sie es erneut. Häufig werden Probleme durch Parameterwerte verursacht, die nicht den Namenseinschränkungen für Azure-Ressourcen entsprechen. Weitere Tipps zur Problembehandlung finden Sie im Artikel [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](virtual-machines/resource-group-deploy-debug.md).

  Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Testumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Geben Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl ein, um der Ressourcengruppe, die Sie für die Präproduktionsumgebung erstellt haben, Ressourcen bereitzustellen. Ersetzen Sie dabei [path] durch den Pfad zu den Dateien, die Sie in den vorherigen Schritten gespeichert haben.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Die Vorlage und die Parameterdateien können mit Ihrem Anwendungscode in einem Quellcodeverwaltungssystem versioniert und verwaltet werden. Sie können die oben aufgeführten Befehle auch in Skriptdateien speichern und auch mit Ihrem Code ablegen.

> [AZURE.NOTE] Sie können die Vorlage direkt in Azure bereitstellen, indem Sie im Artikel [Bereitstellen einer Web-App mit einer SQL-Datenbank](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) auf die Schaltfläche „In Azure bereitstellen“ klicken. Dies können hilfreiche Informationen sein, um die Verwendung von Vorlagen zu erlernen. Sie sind dann aber noch nicht in der Lage, Ihre Vorlagen- und Parameterwerte für Ihren Anwendungscode zu bearbeiten, mit einer Version zu versehen und zu speichern. Daher enthält dieser Artikel keine weiteren Details zu diesem Verfahren.

## Warten von Umgebungen
Während der Entwicklung kann die Konfiguration von Azure-Ressourcen in den unterschiedlichen Umgebungen absichtlich oder versehentlich inkonsistent geändert werden. Dies kann während des Anwendungsentwicklungszyklus unnötigerweise zur Problembehandlung und Problembehebung führen.

1. Ändern Sie die Umgebungen, indem Sie das [Azure-Portal](https://portal.azure.com) öffnen. 
2. Melden Sie sich mit demselben Konto an, das Sie zum Ausführen der obigen Schritte verwendet haben. 
3. Klicken Sie wie in der folgenden Abbildung gezeigt auf „Durchsuchen“ --> „Ressourcengruppen“ (ggf. müssen Sie zu den Ressourcengruppen nach unten scrollen). ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Nachdem Sie auf „Ressourcengruppen“ geklickt haben (Abbildung oben), wird das Blatt „Ressourcengruppen“ angezeigt. Sie sehen die drei Ressourcengruppen, die Sie im vorherigen Schritt erstellt haben (Abbildung unten). Klicken Sie auf die Ressourcengruppe „TestApp1-Development“. Es wird das Blatt angezeigt, auf dem die von der Vorlage erstellten Ressourcen in der Ressourcengruppenbereitstellung (TestApp1-Development) aufgeführt werden, die Sie in einem vorherigen Schritt durchgeführt haben. Löschen Sie die Web-App-Ressource „TestApp1DevApp“, indem Sie auf dem Ressourcengruppenblatt „TestApp1-Development“ auf „TestApp1DevApp“ klicken und dann auf dem Web-App-Blatt „TestApp1DevApp“ die Option „Löschen“ wählen. ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Klicken Sie auf "Ja", wenn das Portal Sie auffordert, den Löschvorgang für die Ressource zu bestätigen. Wenn Sie das Ressourcengruppenblatt „TestApp1-Development“ schließen und wieder öffnen, wird die gelöschte Web-App nicht mehr angezeigt. Die Ressourcengruppe zeigt jetzt nicht mehr die vorgesehenen Inhalte. Sie können weiter experimentieren, indem Sie mehrere Ressourcen aus mehreren Ressourcengruppen löschen oder sogar Konfigurationseinstellungen für einige der Ressourcen ändern. Anstatt das Azure-Portal zum Löschen einer Ressource aus einer Ressourcengruppe zu verwenden, können Sie den PowerShell-Befehl [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) oder den Befehl „azure resource delete“ aus der Befehlszeilenschnittstelle einsetzen, um dieselbe Aufgabe auszuführen.
6. Gehen Sie wie folgt vor, um alle Ressourcen und die für die Ressourcengruppen bestimmten Konfigurationen wieder in den gewünschten Zustand zu versetzen: Stellen Sie die Umgebungen erneut für die Ressourcengruppen bereit, indem Sie die gleichen Befehle wie im Abschnitt [Bereitstellen von Ressourcen in Umgebungen](#deploy-resources-to-environments) verwenden, dabei aber „Deployment1“ durch „Deployment2“ ersetzen.
7.  Wie im Abschnitt „Zusammenfassung“ des Blatts „TestApp1-Development“ in der Abbildung in Schritt 4 dargestellt, sehen Sie Folgendes: Die Web-App, die Sie im vorherigen Schritt im Portal gelöscht haben, ist wieder vorhanden. Dies gilt auch für andere Ressourcen, die Sie ggf. gelöscht haben. Falls Sie die Konfiguration von Ressourcen geändert haben, können Sie auch überprüfen, ob diese wieder auf die Werte in den Parameterdateien festgelegt wurden. Einer der Vorteile der Bereitstellung Ihrer Umgebungen mit Azure-Ressourcen-Manager-Vorlagen besteht darin, dass Sie die Umgebungen einfach zu einem beliebigen Zeitpunkt in einem bekannten Zustand erneut bereitstellen können.
8. Wenn Sie in der Abbildung unten auf den Text unter „Letzte Bereitstellung“ klicken, wird ein Blatt angezeigt, auf dem der Bereitstellungsverlauf für die Ressourcengruppe zu sehen ist. Da Sie den Namen "Deployment1" für die erste Bereitstellung und "Deployment2" für die zweite Bereitstellung verwendet haben, gibt es zwei Einträge. Durch Klicken auf eine Bereitstellung wird ein Blatt mit den Ergebnissen für jede Bereitstellung angezeigt. ![Portal](./media/solution-dev-test-environments/portal3.png)



## Löschen von Umgebungen
Sobald Sie die Verwendung einer Umgebung abgeschlossen haben, sollten Sie sie löschen, damit keine Nutzungsgebühren für Azure-Ressourcen entstehen, die Sie nicht mehr verwenden. Das Löschen von Umgebungen ist sogar noch einfacher als das Erstellen. In den obigen Schritten wurden einzelne Azure-Ressourcengruppen für jede Umgebung erstellt, und anschließend wurden Ressourcen in den Ressourcengruppen bereitgestellt.

Löschen Sie die Umgebungen mit einem der folgenden Verfahren. Mit allen Verfahren wird das gleiche Ergebnis erzielt.

### Azure-Befehlszeilenschnittstelle

Geben Sie an der Befehlszeilenschnittstelle Folgendes ein.

	azure group delete "TestApp1-Development"

Geben Sie bei Aufforderung „y“ ein, und drücken Sie dann die EINGABETASTE, um die Entwicklungsumgebung und alle dazugehörigen Ressourcen zu entfernen. Nach einigen Minuten gibt der Befehl Folgendes zurück:

	info:    group delete command OK

Geben Sie an der Befehlszeilenschnittstelle Folgendes ein, um die verbleibenden Umgebungen zu löschen:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

Geben Sie an einer Azure PowerShell-Eingabeaufforderung (Version 1.01 oder höher) den unten angegebenen Befehl ein, um die Ressourcengruppe und ihren gesamten Inhalt zu löschen.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Wenn die Aufforderung angezeigt wird und Sie sich sicher sind, dass Sie die Ressourcengruppe entfernen möchten, geben Sie „y“ ein und drücken die EINGABETASTE.

Geben Sie Folgendes ein, um die verbleibenden Umgebungen zu löschen:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Azure-Portal

1. Navigieren Sie im Azure-Portal wie in den vorherigen Schritten zu „Ressourcengruppen“. 
2. Wählen Sie die Ressourcengruppe „TestApp1-Development“ aus, und klicken Sie dann im Ressourcengruppenblatt „TestApp1-Development“ auf „Löschen“. Ein neues Blatt wird angezeigt. Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf die Schaltfläche „Löschen“. ![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Löschen Sie die Ressourcengruppen „TestApp1-Test“ und „TestApp1-Pre-Production“ genauso, wie Sie die Ressourcengruppe „TestApp1-Development“ gelöscht haben.

Unabhängig vom verwendeten Verfahren sind die Ressourcengruppen und alle darin enthaltenen Ressourcen nicht mehr vorhanden, sodass keine weiteren Kosten für die Ressourcen entstehen.

Um die Kosten für die Nutzung von Azure-Ressourcen bei der Anwendungsentwicklung auf ein Mindestmaß zu reduzieren, können Sie mit [Azure Automation](automation/automation-intro.md) Aufträge für folgende Aufgaben planen:

- Beenden von virtuellen Computern am Ende des Tages und Neustarten der Computer zu Beginn des Tages.
- Löschen ganzer Umgebungen am Ende des Tages und Neuerstellen der Umgebungen zu Beginn des Tages.
 
Jetzt wissen Sie, wie einfach es ist, Entwicklungs- und Testumgebungen zu erstellen, zu verwalten und zu löschen. Mehr zu den Prozessen, die Sie soeben durchgeführt haben, erfahren Sie durch weiteres Experimentieren mit der oben beschriebenen Schritten und durch Lesen des Referenzmaterials, auf das in diesem Artikel verwiesen wird.

## Nächste Schritte

- [Delegieren von Administratorberechtigungen](role-based-access-control-configure.md) an verschiedene Ressourcen in den einzelnen Umgebungen durch Zuweisen von Microsoft Azure AD-Gruppen oder -Benutzern zu bestimmten Rollen, die eine Teilmenge der Vorgänge für Azure-Ressourcen durchführen können.
- [Zuweisen von Tags](resource-group-using-tags.md) zu den Ressourcengruppen für jede Umgebung und/oder die einzelnen Ressourcen. Sie können den Ressourcengruppen ein Tag "Environment" hinzufügen und dessen Wert auf den Umgebungsnamen festlegen. Tags können besonders hilfreich sein, wenn Sie Ressourcen zur Abrechnung oder Verwaltung organisieren müssen.
- Überwachen von Warnungen und Abrechnungen für Ressourcen aus Ressourcengruppen im [Azure-Portal](https://portal.azure.com).

<!---HONumber=AcomDC_0128_2016-->