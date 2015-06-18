<properties 
	pageTitle="Verwenden der plattformübergreifenden Microsoft Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager" 
	description="Verwenden der plattformübergreifenden Microsoft Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="rasquill"/>

#Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">Plattformübergreifende Befehlszeilenschnittstelle</a></div>

Dieser Artikel beschreibt, wie Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (Xplat-CLI) verwenden können, um mit dem Ressourcen-Manager zu arbeiten. 

>[AZURE.NOTE] Der Ressourcen-Manager befindet sich derzeit in der Vorschau und bietet nicht denselben Umfang an Verwaltungsfunktionen wie die Azure-Dienstverwaltung. 
>
> Wenn die Xplat-CLI noch nicht installiert und konfiguriert wurde, finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][xplatsetup] weitere Anweisungen zum Installieren, Konfigurieren und Verwenden der Xplat-CLI.


##Ressourcen-Manager

Der Ressourcen-Manager ermöglicht Ihnen, eine Gruppe von "Ressourcen" (vom Benutzer verwaltete Entitäten wie Datenbankserver, Datenbank oder Website) als einzelne logische Einheit, oder "Ressourcengruppe", zu verwalten. Eine Ressourcengruppe kann beispielsweise eine Website sowie eine SQL-Datenbank als Ressourcen enthalten.

Zur Unterstützung einer deklarativeren Methode zur Beschreibung von Änderungen an Ressourcen innerhalb einer Ressourcengruppe verwendet der Ressourcen-Manager  *templates*, wobei es sich um JSON-Dokumente handelt. Die Vorlagensprache ermöglicht es Ihnen auch, Parameter zu beschreiben, die beim Ausführen eines Befehls inline ausgefüllt werden oder die in einer separaten JSON-Datei gespeichert werden. Auf diese Weise können Sie mühelos neue Ressourcen mit derselben Vorlage erstellen, indem Sie unterschiedliche Parameter bereitstellen. Eine Vorlage, mit der eine Website erstellt wird, enthält beispielsweise Parameter für den Websitenamen und die Region, in der sich die Website befinden wird, sowie andere allgemeine Parameter.

>[AZURE.NOTE] Einzelheiten zur Vorlagensprache sind derzeit noch nicht dokumentiert. Sobald die Dokumentation verfügbar ist, wird dieses Thema aktualisiert, um einen Link zur Referenzdokumentation bereitzustellen.
>
> Mit dem Befehl  `azure group template download` können Sie jedoch Vorlagen von Microsoft und Partnern aus der Vorlagengalerie herunterladen und ändern.

Wenn mit einer Vorlage eine Gruppe geändert oder erstellt wird, wird eine Bereitstellung erstellt, die dann auf die Gruppe angewendet wird.

##Authentifizierung

Für die Arbeit mit dem Ressourcen-Manager über die Xplat-CLI ist es derzeit erforderlich, dass Sie sich mit einem Geschäfts- oder Schulkonto bei Microsoft Azure authentifizieren. Die Authentifizierung mit einem Microsoft-Konto oder einem Zertifikat über eine .publishsettings-Datei funktioniert nicht.

Weitere Informationen für die Authentifizierung mit einem Organisationskonto finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][xplatsetup].

##Suchen und Konfigurieren einer Ressourcengruppenvorlage

1. Da der Ressourcen-Manager-Modus nicht standardmäßig aktiviert ist, müssen Sie den folgenden Befehl verwenden, um Ressourcen-Manager-Befehle der plattformübergreifenden Befehlszeilenschnittstelle (Xplat-CLI) zu aktivieren.

		azure config mode arm

	>[AZURE.NOTE] Der Ressourcen-Manager-Modus und der Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

2. Für die Arbeit mit Vorlagen können Sie Ihre eigene Vorlage erstellen oder eine aus dem Vorlagenkatalog auswählen. In diesem Fall verwenden wir eine Vorlage aus dem Vorlagenkatalog. Verwenden Sie zum Auflisten der verfügbaren Vorlagen im Katalog den folgenden Befehl. (Da Tausende von Vorlagen verfügbar sind, müssen Sie die Ergebnisse paginieren, oder verwenden Sie **grep** oder **findstr**[ in Windows] oder Ihren bevorzugten Befehl zur Zeichenfolgensuche, um interessante Vorlagen zu finden. Alternativ können Sie die Option **--json** verwenden und die gesamte Liste im JSON-Format für eine einfachere Suche herunterladen. Im folgenden Beispiel wird die Vorlage mit der Bezeichnung **Microsoft.WebSiteSQLDatabase.0.2.6-preview** verwendet.)	

		azure group template list

	In der Antwort werden die Namen des Herausgebers und der Vorlage ähnlich wie nachfolgend gezeigt aufgeführt (es gibt allerdings bedeutend mehr Ergebnisse).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Verwenden Sie den folgenden Befehl, um Details zu einer Vorlage anzuzeigen, mit der eine Azure-Website erstellt wird.

		azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-Vorschau

	Dadurch werden beschreibende Informationen zur Vorlage zurückgegeben. 

4. Wenn Sie eine Vorlage ausgewählt haben (**die Azure-Gruppenvorlage zeigt Microsoft.WebSiteSQLDatabase.0.2.6-preview**), können Sie sie mit dem folgenden Befehl herunterladen.

		azure group template download Microsoft.WebSiteSQLDatabase.0.2.6-preview

	Nach Herunterladen der Vorlage können Sie sie ändern, um sie besser an Ihre Anforderungen anzupassen. So können Sie beispielsweise der Vorlage eine andere Ressource hinzufügen.

	>[AZURE.NOTE] Wenn Sie die Vorlage ändern, sollten Sie sie mit dem Befehl  `azure group template validate` validieren, bevor Sie damit eine Ressourcengruppe erstellen oder eine bestehende Ressourcengruppe ändern.

5. Um die Ressourcengruppenvorlage für Ihre Verwendung zu konfigurieren, öffnen Sie die Vorlagendatei in einem Text-Editor. Beachten Sie die JSON-Sammlung mit **Parametern** am oberen Rand. Diese enthält eine Liste der Parameter, die diese Vorlage erwartet, um die von der Vorlage beschriebenen Ressourcen zu erstellen. Einige Parameter wie **sku** haben Standardwerte, während andere nur den Typ des Werts angeben, z. B. **siteName**. 
	
	Wenn Sie eine Vorlage verwenden, können Sie Parameter als Teil der Befehlszeilenparameter bereitstellen oder eine Datei angeben, die die Parameterwerte enthält. In beiden Fällen müssen die Parameter im JSON-Format sein und Sie müssen Ihre eigenen Werte für solche Schlüssel angeben, die nicht über Standardwerte verfügen.

	Um zum Beispiel eine Datei zu erstellen, die die Parameter für die Vorlage **Microsoft.WebSiteSQLDatabase.0.2.6-preview** enthält, verwenden Sie die folgenden Daten zur Erstellung einer Datei mit dem Namen **params.json**. Ersetzen Sie Werte unten, die mit **_My_** beginnen, z. B. **_MyWebSite_**, durch Ihre eigenen Werte. Mit **siteLocation** sollte eine Azure-Region in Ihrer Nähe festgelegt werden, z. B. **North Europe** oder **South Central US**. (In diesem Beispiel wird **(West US)** verwendet)

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "West US"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "West US"
		  },
		  "administratorLogin": {
		    "value": "MySQLAdmin"
		  },
		  "administratorLoginPassword": {
		    "value": "MySQLAdminPassword"
		  },
		  "databaseName": {
		    "value": "MySQLDB"
		  }
		}


1. Verwenden Sie nach dem Speichern der Datei **params.json** den folgenden Befehl, um eine neue Ressourcengruppe basierend auf der Vorlage zu erstellen. Der Parameter "-e" bestimmt die **params.json**-Datei, die Sie im vorherigen Schritt erstellt haben. Ersetzen Sie **MyGroupName** durch den Gruppennamen, den Sie verwenden möchten, und **MyDataCenter** durch den **siteLocation**-Wert, den Sie in Ihrer Vorlagenparameterdatei **params.json** festgelegt haben.

		azure group create MyGroupName "West US" -f Microsoft.WebSiteSQLDatabase.0.2.6-preview.json -d MyDeployment -e params.json

	>[AZURE.NOTE] Dieser Befehl gibt "OK" zurück, nachdem die Bereitstellung hochgeladen wurde, jedoch bevor die Bereitstellung auf die Ressourcen in der Gruppe angewendet wurde. Verwenden Sie den folgenden Befehl, um den Status der Bereitstellung zu prüfen.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> Mit **ProvisioningState** wird der Status der Bereitstellung angezeigt.
	> 
	> Wenn Sie erkennen, dass Ihre Konfiguration nicht richtig ist und eine lang laufende Bereitstellung angehalten werden muss, verwenden Sie den folgenden Befehl.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> Wenn Sie keinen Bereitstellungsnamen angeben, wird automatisch einer anhand des Namens der Vorlagendatei erstellt. Dieser Name wird als Teil der Ausgabe des Befehls  `azure group create` zurückgegeben.

3. Verwenden Sie zum Anzeigen der Gruppe den folgenden Befehl.

		azure group show MyGroupName

	Dieser Befehl gibt Informationen zu den Ressourcen in der Gruppe zurück. Bei mehreren Gruppen können Sie mit dem Befehl  `azure group list` eine Liste der Gruppennamen abrufen. Verwenden Sie dann  `azure group show`, um die Details einer bestimmten Gruppe anzuzeigen.

##Arbeiten mit Ressourcen

Auch wenn Vorlagen es Ihnen ermöglichen, gruppenweite Änderungen an der Konfiguration zu deklarieren, müssen Sie manchmal mit nur einer einzelnen Ressource arbeiten. Verwenden Sie dazu die  `azure resource`-Befehle.

> [AZURE.NOTE] Wenn Sie die  `azure resource`-Befehle verwenden, mit Ausnahme des Befehls  `list`, müssen Sie mit dem Parameter die API-Version der Ressource angeben, mit der Sie arbeiten. Wenn Sie die zu verwendende API-Version nicht kennen, öffnen Sie die Vorlagendatei und gehen Sie zum Feld **apiVersion** der Ressource.

1. Verwenden Sie den folgenden Befehl, um alle Ressourcen in einer Gruppe aufzulisten.

		azure resource list MyGroupName

1. Verwenden Sie den folgenden Befehl, um einzelne Ressourcen, z. B. die Website, in der Gruppe anzuzeigen.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	Beachten Sie den Parameter **Microsoft.Web/sites**. Mit diesem Parameter wird der Typ der Ressourcen angegeben, für den Sie Informationen anfordern. Wenn Sie die zuvor heruntergeladene Vorlagendatei anschauen, werden Sie feststellen, dass derselbe Wert verwendet wird, um den Typ der in der Vorlage beschriebenen Websiteressource zu definieren.

	Mit diesem Befehl werden Informationen in Bezug auf die Website zurückgegeben. Das Feld **hostNames** sollte beispielsweise die URL für die Website enthalten. Verwenden Sie dies mit Ihrem Browser, um zu überprüfen, ob die Website aktiv ist.

2. Bei der Anzeige von Details einer Ressource ist es oftmals nützlich, den Parameter "--json" zu verwenden, da dadurch die Ausgabe besser lesbar wird, weil es sich bei einigen Werten um eine verschachtelte Struktur oder Sammlung handelt. Im Folgenden sehen Sie, wie die Ergebnisse des show-Befehls als JSON-Dokument zurückgegeben werden.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[AZURE.NOTE] Sie können die JSON-Daten in einer Datei speichern. Verwenden Sie dazu das Zeichen &gt;, um die Ausgabe in die Datei zu leiten. Beispiel:
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. Verwenden Sie den folgenden Befehl, um eine bestehende Ressource zu löschen.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##Protokollierung

Verwenden Sie den Befehl  `azure group log show`, um protokollierte Informationen zu Vorgängen für eine Gruppe anzuzeigen. Standardmäßig wird dadurch der letzte Vorgang aufgelistet, der für die Gruppe durchgeführt wurde. Verwenden Sie zur Anzeige aller Vorgänge den optionalen Parameter "--all". Verwenden Sie für die letzte Bereitstellung "--last-deployment". Verwenden Sie für eine bestimmte Bereitstellung "--deployment", und geben Sie den Namen der Bereitstellung an. Im folgenden Beispiel wird ein Protokoll aller Vorgänge zurückgegeben, die für die Gruppe  'MyGroup' durchgeführt wurden.

	azure group log show mygroup --all

##Nächste Schritte

* Weitere Informationen über die Arbeit mit der plattformübergreifenden Azure-Befehlszeilenschnittstellen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][xplatsetup].
* Weitere Informationen über die Arbeit mit dem Ressourcen-Manager in der Microsoft Azure PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell mit dem Ressourcen-Manager][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!--HONumber=47-->
 