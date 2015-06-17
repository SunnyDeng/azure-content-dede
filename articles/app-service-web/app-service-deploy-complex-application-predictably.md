<properties
	pageTitle="Bereitstellen einer komplexen Anwendung erwartungsgemäß in Azure"
	description="Erfahren Sie, wie eine komplexe Anwendung in Azure als einzelne Einheit und in einer vorhersagbaren Weise mithilfe von JSON-Gruppe Ressourcenvorlagen und PowerShell-Skripts bereitstellen."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="cephalin"/>


# Bereitstellen einer komplexen Anwendung erwartungsgemäß in Azure #

Dieses Lernprogramm zeigt, wie eine komplexe Anwendung in Azure als einzelne Einheit und in einer vorhersagbaren Weise mithilfe von JSON-Gruppe Ressourcenvorlagen und PowerShell-Skripts bereitstellen.

In DevOps sind die Wiederholbarkeit und Vorhersagbarkeit Schlüssel, um eine erfolgreiche Bereitstellung komplexer hoch skalierbare Anwendungen. [Azure Anwendungsdiensts](/services/app-service/) ermöglicht es Ihnen, komplexe Anwendungen, die mobile Web app enthält, -API und Logik apps erstellen. [Azure Resource Manager](../resource-group-overview.md) können Sie alle Komponenten einer solchen Anwendung als eine Einheit verwalten. Jetzt können Sie auch komplexe Anwendung mit JSON-Vorlagen und einfache PowerShell-Skripts bereitstellen.

## Vorgehensweise ##

In diesem Lernprogramm werden Sie eine Anwendung bereitstellen, die enthält:

-	Zwei Web-apps
-	Ein Back-End-SQL-Datenbank
-	Anwendungseinstellungen, Verbindungszeichenfolgen und Quellcodeverwaltung
-	Application Insights, Warnungen und Einstellungen für die automatische Skalierung

## Tools, die Sie verwenden möchten ##

In diesem Lernprogramm verwenden Sie die folgenden Tools. Da es keine umfassende Darstellung des Tools ist, möchte ich bleiben nach dem End-to-End-Szenario, und nur eine kurze Einführung in jeder, erhalten Sie, wo Sie weiterführende Informationen zu finden.

### Azure-Ressourcen-Manager-Vorlagen (JSON) ###
 
Jedes Mal, wenn Sie eine Webanwendung in Azure-App-Dienst erstellen, z. B. verwendet Azure-Ressourcen-Manager eine JSON-Vorlage zum Erstellen der gesamten Ressourcengruppe mit den Komponentenressourcen. Eine komplexe Vorlage aus der [Azure Marketplace](/marketplace) wie die [skalierbare WordPress](/marketplace/partners/wordpress/scalablewordpress/) app zählen der MySQL-Datenbank, Speicherkonten, die App Dienstplan, Web-app selbst, Warnungsregeln, Anwendungseinstellungen, Skalierungseinstellungen und mehr, und alle diese Vorlagen stehen Ihnen über PowerShell. Informationen zum Herunterladen und verwenden Sie diese Vorlagen finden Sie unter [mit Azure PowerShell mit Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).

Weitere Informationen zu den Azure-Ressourcen-Manager-Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md)

### 2\.6 für Azure SDK für Visual Studio ###

Das neueste SDK enthält Verbesserungen für die Unterstützung des Ressourcen-Manager-Vorlage in den JSON-Editor. Sie können Hiermit können Sie schnell eine ressourcengruppenvorlage erstellen oder öffnen Sie eine vorhandene JSON-Vorlage (z. B. eine Vorlage heruntergeladenen Katalogs) für Datenänderungen, füllen Sie die Datei und sogar die Ressourcengruppe direkt aus einer Ressourcengruppe für Azure-Lösung bereitstellen.

Weitere Informationen finden Sie unter [2\.6 von Azure SDK für Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### Azure PowerShell 0.8.0 oder höher ###

Ab Version 0.8.0, enthält die Azure PowerShell-Installation das Azure-Ressourcen-Manager-Modul zusätzlich zu den Azure-Modul. Dieses neue Modul ermöglicht das Skript für der Bereitstellung von Ressourcengruppen.

Weitere Informationen finden Sie unter [mit Azure PowerShell mit Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md)

### Azure-Ressourcen-Explorer ###

Diese [Vorschautool](https://resources.azure.com) ermöglicht es Ihnen, die JSON-Definitionen aller Ressourcengruppen in Ihrem Abonnement und die einzelnen Ressourcen zu durchsuchen. Im Tool können Sie die JSON-Definitionen einer Ressource bearbeiten, Löschen einer gesamte Hierarchie von Ressourcen und erstellen neue Ressourcen. Die Informationen in diesem Tool verfügbar ist sehr hilfreich für die Vorlage zu erstellen, da sie Eigenschaften zeigt Sie für einen bestimmten Typ der Ressource, die richtigen Werte usw. festlegen müssen. Sie können sogar Erstellen der Ressourcengruppe in der [Azure Preview Portal](https://portal.azure.com), untersuchen Sie die JSON-Definitionen in der Explorer-Tool können Sie die Ressourcengruppe nicht als Vorlage dienen.

### Bereitstellen von Azure-Schaltfläche ###

Wenn Sie GitHub für die Quellcodeverwaltung verwenden, können Sie nehmen eine [Deploy to Azure Schaltfläche](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) in der Infodatei. MD, wodurch eine sofort einsetzbare Bereitstellung UI in Azure. Während Sie dies für eine einfache Webanwendung tun können, können Sie diese Methode für eine gesamte Ressourcengruppe eine azuredeploy.json-Datei in das Repository-Stammverzeichnis Einfügen bereitstellen erweitern. Diese JSON-Datei, die ressourcengruppenvorlage enthält, wird von der Bereitstellung zu Azure-Schaltfläche zum Erstellen der Ressourcengruppe verwendet. Ein Beispiel finden Sie die [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) Beispiel, das Sie in diesem Lernprogramm verwenden werden.

## Beispiel ressourcengruppenvorlage abrufen ##

Nun kommen wir gleich.

1. 	Navigieren Sie zu den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App-Beispiel.

2.	 "Readme.MD", klicken Sie auf **Azure bereitstellen**.
 
3.	Sie getroffen haben, um die [Bereitstellen in Azure](https://deploy.azure.com) site und zur Bereitstellung von Eingabeparametern aufgefordert. Beachten Sie, dass die meisten Felder mit den Namen des Repositorys und einige zufälligen Zeichenfolgen gefüllt werden. Sie können alle Felder ändern, wenn Sie möchten, können nur folgende Dinge eingegeben werden die administrativen SQL Server-Anmeldenamen und das Kennwort, und klicken Sie dann **Weiter**.
 
	![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.	Klicken Sie als Nächstes **Bereitstellen** um den Bereitstellungsprozess zu starten. Sobald der Prozess bis zum Abschluss ausgeführt wird, klicken Sie auf die http://todoapp*XXXX*.azure.websites.net Link zum Durchsuchen der bereitgestellten Anwendung.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

	Die Benutzeroberfläche würde etwas langsam sein, wenn Sie zuerst zu durchsuchen, da lediglich Anwendungen gestartet werden, aber überzeugen sich, dass sie eine voll funktionsfähige Anwendung ist.

5.	Klicken Sie in der Seite Bereitstellung auf der **verwalten** Link, um die neue Anwendung in Azure Preview Portal anzuzeigen.

6.	In der **Essentials** Dropdown-Liste, klicken Sie auf die Verknüpfung der Ressource-Gruppe. Beachten Sie, dass die Webanwendung bereits im GitHub-Repository unter verbunden ist **externen Projekt**.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.	Beachten Sie, dass es bereits zwei Web-apps und eine SQL-Datenbank in der Ressourcengruppe, in das ressourcengruppenfenster.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
Alles, was Sie gerade gesehen haben, in wenigen Minuten eine vollständig bereitgestellte komplexe Anwendung mit allen Komponenten, Abhängigkeiten, Einstellungen, Datenbank und kontinuierliche Veröffentlichung von einer automatisierten Orchestrierung in Azure Resource Manager festgelegt ist. All dies erfolgte durch zwei Dinge:

-	Bereitstellen auf Azure-Schaltfläche
-	azuredeploy.JSON in das Repository-Stammverzeichnis

Sie können diese dieselbe Anwendung bereitstellen Dutzende, Hunderte oder Tausende Male und haben genaue die gleiche Konfiguration jedes Mal. Die Wiederholbarkeit und die Vorhersagbarkeit dieses Ansatzes können Sie hoch skalierbare Anwendungen einfach und zuverlässig bereitstellen.

## AZUREDEPLOY untersuchen (oder bearbeiten). JSON ##

Jetzt sehen wir uns wie GitHub-Repository eingerichtet wurde. Verwenden Sie den JSON-Editor in die Azure .NET SDK, wenn Sie nicht bereits installiert haben [Azure .NET SDK 2.6](/downloads/), tun Sie dies jetzt.

1.	Klon der [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) Repository mit Ihrem bevorzugten Git-Tool. Im Screenshot unten bin ich dies in Team Explorer in Visual Studio 2013 erreicht.

	![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.	Öffnen Sie in das Repository-Stammverzeichnis azuredeploy.json in Visual Studio ein. Wenn der JSON-Gliederungsansicht nicht angezeigt wird, müssen Sie Azure .NET SDK installieren.

	![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Ich werde nicht alle Details des JSON-Formats, beschreiben die [mehr Ressourcen](#resources) Abschnitt enthält Links für die Ressource Gruppe Vorlagensprache lernen. Hier werde nur ich Ihnen zeigen, die interessanten Funktionen, mit denen Sie beginnen, eigene benutzerdefinierte Vorlagen für app-Bereitstellung vornehmen.

### Parameter ###

Sehen Sie sich im Parameterabschnitt zu erkennen, dass die meisten dieser Parameter gibt die **Azure bereitstellen** Schaltfläche werden Sie zur Eingabe aufgefordert. Die Website hinter der **Azure bereitstellen** Schaltfläche füllt die Eingabe-Benutzeroberfläche mit den Parametern, die in azuredeploy.json definiert. Diese Parameter werden in die Ressourcendefinitionen, z. B. Ressourcennamen, Eigenschaftswerte usw. verwendet.

### Ressourcen ###

Knoten "Ressourcen" sehen Sie sich, dass 4 Ressourcen der obersten Ebene definiert sind, einschließlich einer SQL Server-Instanz, eine App Dienstplan und zwei Web-apps.

#### App-Dienstplan ####

Beginnen wir mit einer einfachen Stammebenen-Ressource in die JSON. Klicken Sie in der JSON-Gliederung der App Dienstplan mit dem Namen **[HostingPlanName]** markieren Sie den entsprechenden JSON-Code.

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Beachten Sie, dass das `type` -Element gibt die Zeichenfolge für eine App Service-Plan (nannten es eine Serverfarm einen längeren Zeitraum vor), und andere Elemente und Eigenschaften ausgefüllt mit den Parametern in der JSON-Datei definiert, und diese Ressource nicht geschachtelten Ressourcen haben.

>[AZURE.NOTE]Beachten Sie auch, dass den Wert des `apiVersion` teilt Azure, welche Version der REST-API die Ressourcendefinition JSON mit verwenden, und es kann beeinflussen, wie die Ressource innerhalb formatiert werden soll, die `{}`.

#### SQL Server ####

Klicken Sie als Nächstes auf die SQL Server-Ressource mit dem Namen **SQLServer** in der JSON-Gliederung.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
Beachten Sie die folgenden Informationen zu den hervorgehobenen JSON-Code:

-	Die Verwendung von Parametern wird sichergestellt, dass die erstellten Ressourcen benannt und auf eine Weise, die sie mit anderen konsistent macht konfiguriert werden.
-	Die SQL Server-Ressource hat zwei geschachtelte Ressourcen, jeweils einen anderen Wert für `type`.
-	Geschachtelten Ressourcen `“resources”: […]`, der die Datenbank und die Firewall-Regeln definiert werden, haben eine `dependsOn` Element, das die Ressourcen-ID der Ressource auf der Stammebene SQLServer angibt. Dies weist Azure Resource Manager, "vor dem Erstellen dieser Ressource, die andere Ressource bereits vorhanden sein; und wenn diese anderen Ressource in der Vorlage definiert ist, erstellen Sie eine zuerst".

	>[AZURE.NOTE]Ausführliche Informationen zur Verwendung der `resourceId()` finden Sie im [Azure Resource Manager Vorlagenfunktionen](../resource-group-template-functions.md).

-	Die Auswirkungen der `dependsOn` Element ist, dass Azure Resource Manager wissen, welche Ressourcen parallel erstellt werden können und welche Ressourcen sequenziell erstellt werden müssen.

#### Web-app ####

Nun, betrachten wir nun die tatsächlichen Webanwendungen selbst, deren komplizierter ist. Klicken Sie auf die [variables('apiSiteName')] Web-app in der JSON-Gliederung den JSON-Code zu markieren. Sie werden feststellen, dass viel interessantere Dinge abrufen. Zu diesem Zweck werde ich mehr über die Features einzeln:

##### Root-Ressource #####

Die Web-app hängt von zwei verschiedene Ressourcen. Dies bedeutet, dass Azure Resource Manager die Webanwendung erstellt wird, nachdem der App Dienstplan und SQL Server-Instanz erstellt werden.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### App-Einstellungen #####

Die Anwendungseinstellungen werden auch als geschachtelte Ressource definiert.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

In der `properties` -Element für `config/appsettings`, Sie haben zwei Anwendungseinstellungen im Format `“<name>” : “<value>”`.

-	`PROJECT` ist ein [KUDU-Einstellung](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) weiß Azure-Bereitstellung welches Projekt in Visual Studio-Projektmappe mit mehreren Projekten verwenden. Erfahren Sie, wie später Quellcodeverwaltung konfiguriert ist, aber da der ToDoApp-Code in einem Visual Studio-Projektmappe mit mehreren Projekten ist, benötigen wir diese Einstellung.
-	`clientUrl` ist einfach eine app, die festlegen, die des Anwendungscodes verwendet.

##### Verbindungszeichenfolgen #####

Die Verbindungszeichenfolgen werden auch als geschachtelte Ressource definiert.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

In der `properties` -Element für `config/connectionstrings`, jede Verbindungszeichenfolge wird auch als Name-Wert-Paar, mit dem Format der definierten `“<name>” : {“value”: “…”, “type”: “…”}`. Für das `type` -Element, um mögliche Werte sind `MySql`, `SQLServer`, `SQLAzure`, und `Custom`.

>[AZURE.TIP]Eine endgültige Liste der Zeichenfolge Verbindungstypen, führen Sie den folgenden Befehl in Azure PowerShell: [Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### Quellcodeverwaltung #####

Einstellungen für die Quellcodeverwaltung werden auch als geschachtelte Ressource definiert. Azure-Ressourcen-Manager verwendet diese Ressource so konfigurieren Sie die dauerhafte Veröffentlichung (finden Sie unter Vorbehalt auf `IsManualIntegration` Weiter unten) und auch für die Bereitstellung der Anwendungscode automatisch während der Verarbeitung der JSON-Datei starten.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` und `branch` sollte ziemlich einfach sein und sollte auf das Git-Repository und den Namen der Verzweigung zum Veröffentlichen aus verweisen. In diesem Fall werden diese durch Eingabeparameter definiert.

Beachten Sie in der `dependsOn` Element, zusätzlich zu der Web-app-Ressource selbst `sourcecontrols/web` hängt auch von `config/appsettings` und `config/connectionstrings`. Grund hierfür ist, sobald `sourcecontrols/web` wird konfiguriert, der Prozess der Azure-Bereitstellung wird automatisch versucht, bereitstellen, erstellen und starten den Anwendungscode. Daher bietet das Einfügen dieser Abhängigkeit Sie sicherstellen, dass die Anwendung Zugriff auf die erforderlichen app-Einstellungen und Verbindungszeichenfolgen verfügt, bevor der Anwendungscode ausgeführt wird. [: Zu erledigen überprüfen, ob dies der Fall ist.]

>[AZURE.NOTE]Beachten Sie auch, dass `IsManualIntegration` minFreeThreads auf `true`. Diese Eigenschaft ist in diesem Lernprogramm erforderlich, da Sie nicht tatsächlich GitHub-Repository besitzen, und daher nicht tatsächlich Azure erteilen können so konfigurieren Sie kontinuierliche Veröffentlichung von [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (d. h. automatische Repository Updates in Azure push). Sie können den Standardwert verwenden `false` für das angegebene Repository nur, wenn Sie der Besitzer der Anmeldeinformationen für GitHub in konfiguriert haben die [Azure Preview-Portal](https://portal.azure.com) vor. Anders ausgedrückt, wenn Sie die Quellcodeverwaltung, GitHub oder BitBucket für jede Anwendung in eingerichtet haben die [Azure Preview-Portal](https://portal.azure.com) zuvor mit Ihrer Benutzer-Anmeldeinformationen und Azure die Anmeldeinformationen speichern und verwenden Sie diese, wenn Sie jede Anwendung über GitHub oder BitBucket in Zukunft bereitstellen. Jedoch wenn Sie dies noch nicht geschehen, fehl Bereitstellung von JSON-Vorlage bei Azure-Ressourcen-Manager versucht, die Web-app-Quellcode-Verwaltungseinstellungen zu konfigurieren, da es in GitHub oder BitBucket mit dem Repository-Eigentümer Anmeldeinformationen anmelden kann.

## Vergleichen Sie die JSON-Vorlage bereitgestellten Ressourcengruppe ##

Hier, können Sie alle Web-app Blades in die [Preview-Portal](https://portal.azure.com), aber es ist ein weiteres Tool, das nur als nützlich, If, nicht mehr ist. Klicken Sie auf die [Azure-Ressourcen-Explorer](https://resources.azure.com) Vorschautool, mit dem Sie eine JSON-Darstellung aller Ressourcengruppen in Ihrer Abonnements erhalten, wie sie tatsächlich in der Azure-Back-End vorhanden sind. Sie können auch sehen, wie die Ressourcengruppe JSON-Hierarchie in Azure mit der Hierarchie in der Vorlagendatei entspricht, die verwendet wird, um es zu erstellen.

Angenommen, wenn ich mich der [Azure-Ressourcen-Explorer](https://resources.azure.com) -tool und erweitern Sie die Knoten im Explorer, sehe ich die Ressourcengruppe und die Ressourcen auf der Stammebene, die unter der jeweiligen Ressourcenart gesammelt werden.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Wenn Sie eine Webanwendung Drilldown, muss Web app-Konfigurationsdetails ähnelt finden Sie unter den folgenden Screenshot:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Erneut, die geschachtelten Ressourcen sollten bilden eine Hierarchie, die sehr ähnlich wie in der JSON-Vorlagendatei und sollte die Anwendungseinstellungen, Verbindungszeichenfolgen usw., ordnungsgemäß im JSON-Bereich wiedergegeben. Das Fehlen von Einstellungen hier möglicherweise ein Problem mit der JSON-Datei und die JSON-Vorlagendatei bei der Problembehandlung hilfreich.

## Bereitstellen der Vorlage der Gruppe selbst ##

Die **Azure bereitstellen** Schaltfläche ist großartig, aber sie können ressourcengruppenvorlage in azuredeploy.json bereitgestellt werden, nur dann, wenn Sie bereits azuredeploy.json in GitHub abgelegt haben. Azure .NET SDK stellt auch Tools zur Verfügung, um jede JSON-Vorlagendatei direkt von Ihrem lokalen Computer bereitstellen. Führen Sie hierzu die folgenden Schritte aus:

1.	Klicken Sie in Visual Studio **Datei** > **Neu** > **Projekt**.

2.	Klicken Sie auf **Visual C#-** > **Cloud** > **Azure Ressourcengruppe**, klicken Sie dann auf **OK**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.	In **Azure-Vorlage auswählen**, auf **leere Vorlage** und klicken Sie auf **OK**.

4.	Ziehen Sie azuredeploy.json in die **Vorlage** Ordner des neuen Projekts.

	![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.	Öffnen Sie im Projektmappen-Explorer das kopierte azuredeploy.json an.

6.	Nur aus Gründen der Demo fügen Sie einige Application Insight-Standardressourcen an unsere JSON-Datei durch Klicken auf **Ressource hinzufügen**. Wenn Sie einfach nur an die JSON-Datei bereitgestellt sind, fahren Sie mit den Schritten bereitstellen.

	![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.	Wählen Sie **Application Insights für Webanwendungen**, Sie dann sicher, dass eine vorhandene Anwendungsdiensts Plan und Web-app ausgewählt ist, und klicken Sie dann auf **Hinzufügen**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

	Sie werden nun möglicherweise finden verschiedene neuen Ressourcen, abhängig von der Ressource und der Arbeitsweise, Abhängigkeiten von der App Dienstplan oder Web app verfügen. Diese Ressourcen werden durch die vorhandene Definition nicht aktiviert, und Sie ändern wollen.

	![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.	Klicken Sie in der JSON-Gliederung, **AppInsights automatische Skalierung** hervorheben den JSON-Code. Dies ist die Skalierungseinstellung für Ihre App Dienstplan.

9.	Suchen Sie in den hervorgehobenen JSON-Code, der `location` und `enabled` Eigenschaften, und legen Sie sie wie unten dargestellt.

	![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10.	Klicken Sie in der JSON-Gliederung, **CPUHigh AppInsights** den JSON-Code zu markieren. Dies ist eine Warnung.

11.	Suchen Sie die `location` und `isEnabled` Eigenschaften, und legen Sie sie wie unten dargestellt. Gehen Sie genauso für die anderen drei Warnungen (Lila Glühbirnen).

	![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12.	Sie nun können bereitstellen. Mit der rechten Maustaste in des Projekts, und wählen Sie **Bereitstellen** > **neue Bereitstellung**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13.	Melden Sie sich in Ihrem Azure-Konto, wenn Sie dies nicht bereits getan haben.

14.	Wählen Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement oder erstellen Sie eine neue ein, wählen Sie **azuredeploy.json**, und klicken Sie dann auf **Parameter bearbeiten**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

	Jetzt haben Sie alle in der Vorlagendatei in einer Tabelle mit gut definierten Parameter bearbeiten werden. Parameter, die Standardwerte definieren verfügen bereits über Standardwerte und Parameter, die eine Liste der zulässigen Werte definieren als Dropdownlisten angezeigt werden.

	![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15.	Füllen Sie alle leeren Parameter, und verwenden Sie die [GitHub-Repository-Adresse für ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) in **RepoUrl**. Klicken Sie auf **Speichern**.
 
	![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

	>[AZURE.NOTE]Für die automatische Skalierung ist ein Feature in Angeboten **Standard** Ebene oder höher und Plan auf Warnungen sind die Features von **grundlegende** Ebenen oder höher verwenden, müssen Sie festlegen der **Sku** Parameter **Standard** oder **Premium** um alle neuen App Insights Ressourcen hervorgehoben werden, finden Sie unter.
	
16.	Klicken Sie auf **Bereitstellen**. Bei Auswahl von **Kennwörter speichern**, das Kennwort wird in der Parameterdatei gespeichert werden **im nur-Text**. Andernfalls müssen Sie zur Eingabe des Datenbankkennworts während des Bereitstellungsprozesses.

Fertig! Nun Sie einfach zu wechseln müssen der [vorschauportal](https://portal.azure.com) und die [Azure-Ressourcen-Explorer](https://resources.azure.com) Tool die Einstellungen zum automatischen Skalieren des JSON-OBJEKTS hinzugefügt und der neue Warnungen finden Sie die Anwendung bereitgestellt.

Die Schritte in diesem Abschnitt erreicht hauptsächlich folgende:

1.	Die Vorlagendatei vorbereitet
2.	Parameterdatei mit der Vorlagendatei erstellt
3.	Die Vorlagendatei mit der Parameterdatei bereitgestellt

Der letzte Schritt erfolgt mühelos durch ein PowerShell-Cmdlet. Öffnen Sie zum Anzeigen der vorgegangen Visual Studio bei der Bereitstellung der Anwendung Scripts\\Deploy AzureResourceGroup.ps1 ein. Es ist viel Code vorhanden, aber ich nur alle relevanten Code markieren, den Sie die Vorlagendatei mit der Parameterdatei bereitstellen müssen.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Die letzte Cmdlet `New-AzureResourceGroup`, wird die ausgeführte Aktion. All dies sollte Ihnen zeigen, mit Hilfe des Tools, es relativ einfach, die Cloudanwendung erwartungsgemäß bereitgestellt ist. Jedes Mal, wenn Sie das Cmdlet auf der gleichen Vorlage mit der gleichen Parameterdatei ausführen, machen Sie das gleiche Ergebnis zu erhalten.

## Zusammenfassung ##

In DevOps sind die Wiederholbarkeit und Vorhersagbarkeit Schlüssel, um eine erfolgreiche Bereitstellung komplexer hoch skalierbare Anwendungen. In diesem Lernprogramm haben Sie eine Anwendung in Azure als einzelne Ressourcengruppe mithilfe der Azure-Ressourcen-Manager-Vorlage bereitgestellt. Ich hoffe, es erhält Sie die Kenntnisse, die Sie benötigen, um das Starten die komplexe Anwendung in Azure in eine Vorlage konvertieren und kann bereitstellen erwartungsgemäß in Azure.

<a name="resources"></a>
## Weitere Ressourcen ##

-	[Azure Resource Manager-Vorlagensprache](http://msdn.microsoft.com/library/azure/dn835138.aspx)
-	[Authoring Azure Resource Manager Templates](../resource-group-authoring-templates.md) (in englischer Sprache)
-	[Azure Resource Manager Template Functions](../resource-group-template-functions.md) (Vorlagenfunktionen im Azure-Ressourcen-Manager)
-	[Advanced Template Operations](../resource-group-advanced-template.md) (Erweiterte Anwendungen mit Vorlagen)
-	[Bereitstellen einer Anwendung mit Azure-Ressourcen-Manager-Vorlage](../resource-group-template-deploy.md)
-	[Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md)
-	[Troubleshooting Resource Group Deployments in Azure](../resource-group-deploy-debug.md) (Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure)




 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->