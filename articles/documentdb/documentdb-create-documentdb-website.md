<properties 
	pageTitle="Stellen Sie DocumentDB und Azure App Service-Web-Apps mithilfe einer Vorlage des Azure-Ressourcen-Managers bereit | Microsoft Azure" 
	description="Erfahren Sie, wie Sie ein DocumentDB-Konto, eine Azure App-Service-Web-App und eine Beispielwebanwendung mithilfe einer Vorlage des Azure-Ressourcen-Managers bereitstellen." 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="stbaro"/>

# Bereitstellen von DocumentDB und Azure App Service-Web-Apps mithilfe einer Vorlage des Azure-Ressourcen-Managers #

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe einer Vorlage des Azure-Ressourcen-Managers [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), eine [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-App und eine Beispielwebanwendung bereitstellen und integrieren.

Nach Abschluss dieses Lernprogramms können Sie die folgenden Fragen beantworten:

-	Wie kann ich mithilfe einer Vorlage des Azure-Ressourcen-Managers ein DocumentDB-Konto und eine Web-App in Azure App Service bereitstellen und integrieren?
-	Wie kann ich mithilfe einer Vorlage des Azure-Ressourcen-Managers ein DocumentDB-Konto, eine Web-App in App Service-Web-Apps und eine WebDeploy-Anwendung bereitstellen und integrieren?

<a id="Prerequisites"></a>
## Voraussetzungen ##
> [AZURE.TIP]Zwar wird für dieses Lernprogramm keine Erfahrung im Umgang mit Vorlagen des Azure-Ressourcen-Managers, JSON oder Azure PowerShell vorausgesetzt, wenn Sie jedoch die hierin verwendeten Vorlagen oder Bereitstellungsoptionen ändern möchten, sind Kenntnisse in jedem dieser Bereiche erforderlich.

Vor dem Ausführen der Anweisungen zu diesem Lernprogramm, müssen Sie sicherstellen, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Azure erwerben](http://azure.microsoft.com/pricing/purchase-options/), [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) oder [Einen Monat kostenlos testen!](http://azure.microsoft.com/pricing/free-trial/).
- Ein Azure-Speicherkonto. Anweisungen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-whatis-account.md).
- Eine Arbeitsstation mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

##<a id="CreateDB"></a>Schritt 1: Herunterladen und Extrahieren der Beispieldateien ##
Als Erstes laden wir die Beispieldateien herunter, die in diesem Lernprogramm verwendet werden.

1. Laden Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos, von Web-Apps und zum Bereitstellen einer Demoanwendung](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) in einen lokalen Ordner (z. B. „C:\\DocumentDBTemplates“) herunter, und extrahieren Sie die Dateien. In diesem Beispiel werden ein DocumentDB-Konto, eine App Service-Web-App und eine Webanwendung bereitgestellt. Außerdem wird die Webanwendung automatisch so konfiguriert, dass sie eine Verbindung mit dem DocumentDB-Konto herstellt.

2. Laden Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos und Web-Apps](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) in einen lokalen Ordner (z. B. „C:\\DocumentDBTemplates“) herunter, und extrahieren Sie die Dateien. Mit diesem Beispiel werden ein DocumentDB-Konto und eine App Service-Web-App bereitgestellt; außerdem wird die Konfiguration der Web-App so geändert, dass DocumentDB-Verbindungsinformationen problemlos angegeben werden können, eine Webanwendung ist jedoch nicht enthalten.

> [AZURE.TIP]Beachten Sie, dass Sie je nach den Sicherheitseinstellungen des Computers möglicherweise die extrahierten Dateien entsperren müssen, indem Sie mit der rechten Maustaste darauf klicken und dann auf **Eigenschaften** und auf **Blockierung aufheben** klicken.

![Screenshot des Fensters „Eigenschaften“ mit der hervorgehobenen Schaltfläche „Blockierung aufheben“](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Schritt 2: Bereitstellen des Dokument-Kontos, der App Service-Web-App und der Demoanwendung ##

Jetzt stellen wir unsere erste Vorlage bereit.

> [AZURE.TIP]Die Vorlage überprüft nicht, ob die unten eingegebenen Namen der Web-App und der DocumentDB-Kontoname (a) gültig und (b) verfügbar sind. Es wird dringend empfohlen, die Verfügbarkeit der gewünschten Namen zu überprüfen, bevor Sie das PowerShell-Bereitstellungsskript ausführen.

1. Öffnen Sie Microsoft Azure PowerShell, und navigieren Sie zu dem Ordner, in den Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos, einer App Service-Web-App und Bereitstellen einer Demoanwendung](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) heruntergeladen und extrahiert haben (z. B. „C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo“).


2. Wir führen das PowerShell-Skript "CreateDocDBWebsiteTodo.ps1" aus. Das Skript verwendet die folgenden erforderlichen Parameter:
	- WebsiteName: Gibt den Namen der App Service-Web-App an und wird verwendet, um die URL zu erstellen, die Sie für den Zugriff auf die Web-App verwenden (wenn Sie z. B. „Mydemodocdbwebapp“ angeben, dann lautet die URL, mit der Sie auf die Web-App zugreifen, „mydemodocdbwebapp.azurewebsites.net“).

	- ResourceGroupName: Gibt den Namen der bereitzustellenden Azure-Ressourcengruppe an. Wenn die angegebene Ressourcengruppe nicht existiert, wird sie erstellt.

	- docDBAccountName: Gibt den Namen des zu erstellenden DocumentDB-Kontos an.

	- location: Gibt den Azure-Ort an, an dem die DocumentDB- und Web-App-Ressourcen erstellt werden sollen. Gültige Werte sind Ostasien, Südostasien, Osten USA, Westen USA, Nordeuropa, Westeuropa (beachten Sie, dass bei dem angegebenen Ortswert die Groß-/Kleinschreibung beachtet werden muss).


3. Hier sehen Sie einen Beispielbefehl zur Ausführung des Skripts:

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP]Beachten Sie, dass Sie im Rahmen der Skriptausführung zur Eingabe des Benutzernamens und Kennworts für Ihr Azure-Konto aufgefordert werden. Die gesamte Bereitstellung nimmt zwischen 10 und 15 Minuten in Anspruch.

4. Hier sehen Sie ein Beispiel der resultierenden Ausgabe:

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Bevor wir die Beispielanwendung näher betrachten, sehen wir uns an, was bei der Vorlagenbereitstellung geschehen ist:

	- Eine App Service-Web-App wurde erstellt.

	- Ein DocumentDB-Konto wurde erstellt.

	- Ein WebDeploy-Paket wurde für die App Service-Web-App bereitgestellt.

	- Die Konfiguration der Web-App wurde so geändert, dass der DocumentDB-Endpunkt und der primäre Hauptschlüssel als Anwendungseinstellungen angegeben wurden.

	- Eine Reihe von standardmäßigen Überwachungsregeln wurde erstellt.

	
6. Um die Anwendung zu verwenden, navigieren Sie einfach zur URL der Web-App (im obigen Beispiel würde die URLhttp://mydemodocdbwebapp.azurewebsites.net) lauten. Die folgende Webanwendung wird angezeigt:

	![Todo-Beispielanwendung](./media/documentdb-create-documentdb-website/image2.png)

7. Erstellen Sie nun eine Reihe von Aufgaben, anschließend öffnen wir das [Microsoft Azure-Vorschauportal](https://portal.azure.com).

8. Durchsuchen Sie die Ressourcengruppen, und wählen Sie die Ressourcengruppe aus, die wir bei der Bereitstellung erstellt haben (im Beispiel oben ist dies myDemoResourceGroup).

	![Screenshot des Azure-Portals mit der hervorgehobenen MyDemoResourceGroup](./media/documentdb-create-documentdb-website/image3.png)
9.  Beachten Sie, dass die Ressourcenzuordnung im Bereich „Zusammenfassung“ alle zugehörigen Ressourcen (DocumentDB-Konto, App Service-Web-App, Überwachung) anzeigt.

	![Screenshot der Zusammenfassung](./media/documentdb-create-documentdb-website/image4.png)
10.  Klicken Sie auf Ihr DocumentDB-Konto, und starten Sie Abfrage-Explorer (im unteren Bereich des Kontoblatts).

	![Screenshot der Blätter „Ressourcengruppe“ und „Kontoblätter“ mit der hervorgehobenen Kachel „Abfrage-Explorer“.](./media/documentdb-create-documentdb-website/image8.png)

11. Führen Sie die Standardabfrage "SELECT * FROM c" aus, und überprüfen Sie die Ergebnisse. Beachten Sie, dass die Abfrage die JSON-Darstellung der Aufgaben abgerufen hat, die Sie in Schritt 7 oben erstellt haben. Sie können gerne mit Abfragen experimentieren; führen Sie z. B. SELECT * FROM c WHERE c.isComplete = true aus, um alle Aufgaben zurückzugeben, die als abgeschlossen gekennzeichnet wurden.


	![Screenshot der Blätter „Abfrage-Explorer“ und „Ergebnisse“ mit Anzeige der Abfrageergebnisse](./media/documentdb-create-documentdb-website/image5.png)
12. Erkunden Sie die Oberfläche des DocumentDB-Portals, oder ändern Sie die Todo-Beispielanwendung. Wenn Sie fertig sind, stellen wir eine andere Vorlage bereit.
	
<a id="Build"></a>
## Schritt 3: Bereitstellen des Dokument-Kontos und Web-App-Beispiels ##

Jetzt stellen wir unsere zweite Vorlage bereit.

> [AZURE.TIP]Die Vorlage überprüft nicht, ob die unten eingegebenen Namen der Web-App und der DocumentDB-Kontoname (a) gültig und (b) verfügbar sind. Es wird dringend empfohlen, die Verfügbarkeit der gewünschten Namen zu überprüfen, bevor Sie das PowerShell-Bereitstellungsskript ausführen.

1. Öffnen Sie Microsoft Azure PowerShell, und navigieren Sie zu dem Ordner, in den Sie das Beispiel zum [Erstellen eines DocumentDB-Kontos und eines Web-App-Beispiels](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) heruntergeladen und extrahiert haben (z. B. C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Wir führen das PowerShell-Skript "CreateDocDBWebsite.ps1" aus. Das Skript verwendet die gleichen Parameter wie die erste bereitgestellte Vorlage, nämlich:
	- WebsiteName: Gibt den Namen der App Service-Web-App an und wird verwendet, um die URL zu erstellen, die Sie für den Zugriff auf die Web-App verwenden (wenn Sie z. B. „myobantherdocumentdbwebapp“ angeben, dann lautet die URL, mit der Sie auf die Web-App zugreifen, „myotherdocumentdbwebapp.azurewebsites.net“).

	- ResourceGroupName: Gibt den Namen der bereitzustellenden Azure-Ressourcengruppe an. Wenn die angegebene Ressourcengruppe nicht existiert, wird sie erstellt.

	- docDBAccountName: Gibt den Namen des zu erstellenden DocumentDB-Kontos an.

	- 	location: Gibt den Azure-Ort an, an dem die DocumentDB- und Web-App-Ressourcen erstellt werden sollen. Gültige Werte sind Ostasien, Südostasien, Osten USA, Westen USA, Nordeuropa, Westeuropa (beachten Sie, dass bei dem angegebenen Ortswert die Groß-/Kleinschreibung beachtet werden muss).

3. Hier sehen Sie einen Beispielbefehl zur Ausführung des Skripts:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP]Beachten Sie, dass Sie im Rahmen der Skriptausführung zur Eingabe des Benutzernamens und Kennworts für Ihr Azure-Konto aufgefordert werden. Die gesamte Bereitstellung nimmt zwischen 10 und 15 Minuten in Anspruch.

4. Die Ausgabe der Bereitstellung sieht der des ersten Vorlagenbeispiels sehr ähnlich.
5. Bevor wir das Azure-Vorschauportal öffnen, sehen wir uns an, was bei der Vorlagenbereitstellung geschehen ist:

	- Eine App Service-Web-App wurde erstellt.

	- Ein DocumentDB-Konto wurde erstellt.

	- 	Die Konfiguration der Web-App wurde so geändert, dass der Azure DocumentDB-Endpunkt, der primäre Hauptschlüssel und der sekundäre Hauptschlüssel als Anwendungseinstellungen angegeben wurden.

	- 	Eine Reihe von standardmäßigen Überwachungsregeln wurde erstellt.

6. Wir öffnen nun das [Azure-Vorschauportal](https://portal.azure.com), durchsuchen die Ressourcengruppen und wählen die Ressourcengruppe aus, die wir bei der Bereitstellung erstellt haben (im Beispiel oben ist dies „myOtherDemoResourceGroup“).
7. Klicken Sie im Bereich „Zusammenfassung“ auf die soeben bereitgestellte Web-App.

	![Screenshot der Zusammenfassung mit der hervorgehobenen Web-Apps „Myotherdocumentdbwebapp“](./media/documentdb-create-documentdb-website/image6.png)
8. Klicken Sie auf dem Blatt „Web-App“ auf **Alle Einstellungen** und dann auf **Anwendungseinstellungen**. Beachten Sie, dass Anwendungseinstellungen für den DocumentDB-Endpunkt und jeden der DocumentDB-Hauptschlüssel vorhanden sind.

	![Screenshot der Blätter „Web-App“, „Einstellungen“ und „Anwendungseinstellungen“](./media/documentdb-create-documentdb-website/image7.png)
9. Sie können das Azure-Vorschauportal gerne weiter erkunden oder eines der DocumentDB-[Beispiele](http://go.microsoft.com/fwlink/?LinkID=402386) durcharbeiten, um Ihre eigene DocumentDB-Anwendung zu erstellen.

	
	
<a name="NextSteps"></a>
## Nächste Schritte

Glückwunsch! Sie haben DocumentDB, eine App Service-Web-App und eine Beispielwebanwendung mithilfe von Vorlagen des Azure-Ressourcen-Managers bereitgestellt.

- Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](http://azure.com/docdb).
- Weitere Informationen zu Azure App Service Web-Apps erhalten Sie, indem Sie [hier](http://go.microsoft.com/fwlink/?LinkId=325362) klicken.
- Weitere Informationen zu Vorlagen des Azure-Ressourcen-Managers erhalten Sie, indem Sie [hier](https://msdn.microsoft.com/library/azure/dn790549.aspx) klicken.


## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.
 

<!---HONumber=Oct15_HO3-->