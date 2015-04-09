<properties 
	pageTitle="Bereitstellen von DocumentDB und einer Azure-Website mithilfe einer Vorlage des Azure-Ressourcen-Managers | Azure" 
	description="Erfahren Sie, wie Sie ein DocumentDB-Konto, eine Azure-Website und eine Beispielwebanwendung mithilfe einer Vorlage des Azure-Ressourcen-Managers bereitstellen." 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

# Bereitstellen von DocumentDB und einer Azure-Website mithilfe einer Vorlage des Azure-Ressourcen-Managers #

In diesem Lernprogramm wird gezeigt, wie Sie mithilfe einer Vorlage des Azure-Ressourcen-Managers [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), eine [Azure-Website](http://azure.microsoft.com/services/websites/) und eine Beispielwebanwendung bereitstellen und integrieren.

Nach Abschluss dieses Lernprogramms können Sie die folgenden Fragen beantworten:  

-	Wie kann ich mithilfe einer Vorlage des Azure-Ressourcen-Managers ein DocumentDB-Konto und eine Azure-Website bereitstellen und integrieren?
-	Wie kann ich mithilfe einer Vorlage des Azure-Ressourcen-Managers ein DocumentDB-Konto, eine Azure-Website und eine WebDeploy-Anwendung bereitstellen und integrieren?

## <a id="Prerequisites"></a>Voraussetzungen ##
> [AZURE.TIP] Zwar wird für dieses Lernprogramm keine Erfahrung im Umgang mit Vorlagen des Azure-Ressourcen-Managers, JSON oder Azure PowerShell vorausgesetzt, wenn Sie jedoch die hierin verwendeten Vorlagen oder Bereitstellungsoptionen ändern möchten, sind Kenntnisse in jedem dieser Bereiche erforderlich.

Vor dem Ausführen der Anweisungen zu diesem Lernprogramm, müssen Sie sicherstellen, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform.  Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/), [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) oder [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).
- Ein Azure-Speicherkonto. Anweisungen finden Sie unter [Informationen zu Azure-Speicherkonten](storage-whatis-account.md).
- Eine Arbeitsstation mit Azure PowerShell. Anweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md).

## <a id="CreateDB"></a>Schritt 1: Herunterladen und Extrahieren der Beispieldateien ##
Als Erstes laden wir die Beispieldateien herunter, die in diesem Lernprogramm verwendet werden.

1. Laden Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos, einer Website und Bereitstellen einer Demoanwendung](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) in einen lokalen Ordner (z. B. "C:\DocumentDBTemplates") herunter, und extrahieren Sie die Dateien.  Mit diesem Beispiel werden ein DocumentDB-Konto, eine Azure-Website und eine Webanwendung bereitgestellt.  Außerdem wird die Webanwendung automatisch so konfiguriert, dass sie eine Verbindung mit dem DocumentDB-Konto herstellt.

2. Laden Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos und einer Website](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) in einen lokalen Ordner (z. B. "C:\DocumentDBTemplates") herunter, und extrahieren Sie die Dateien.  Mit diesem Beispiel werden ein DocumentDB-Konto und eine Azure-Website bereitgestellt; außerdem wird die Konfiguration der Website so geändert, dass DocumentDB-Verbindungsinformationen problemlos angegeben werden können; eine Webanwendung ist allerdings nicht enthalten.  

> [AZURE.TIP] Beachten Sie, dass Sie je nach den Sicherheitseinstellungen des Computers möglicherweise die extrahierten Dateien entsperren müssen, indem Sie mit der rechten Maustaste darauf klicken und dann auf **Eigenschaften** und auf **Blockierung aufheben** klicken.

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


## <a id="Build"></a>Schritt 2: Beispiel zum Bereitstellen eines DocumentDB-Kontos, einer Website und einer Demoanwendung ##

Jetzt stellen wir unsere erste Vorlage bereit.

> [AZURE.TIP] Die Vorlage überprüft nicht, ob die unten eingegebenen Namen der Website und des DocumentDB-Kontos (a) gültig und (b) verfügbar sind.  Es wird dringend empfohlen, die Verfügbarkeit der gewünschten Namen zu überprüfen, bevor Sie das PowerShell-Bereitstellungsskript ausführen.

1. Öffnen Sie Microsoft Azure PowerShell, und navigieren Sie zu dem Ordner, in den Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos, einer Website und Bereitstellen einer Demoanwendung](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) heruntergeladen und extrahiert haben (z. B. "C:\DocumentDBTemplates\CreateDocDBWebsiteTodo").


2. Wir führen das PowerShell-Skript "CreateDocDBWebsiteTodo.ps1" aus.  Das Skript verwendet die folgenden erforderlichen Parameter:
	- WebsiteName: Gibt den Namen der Website an und wird zum Bilden der URL verwendet, mit der Sie auf die Website zugreifen (wenn Sie z. B. "mydemodocdbwebsite" angeben, lautet die URL für den Zugriff auf die Website mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName: Gibt den Namen der bereitzustellenden Azure-Ressourcengruppe an. Wenn die angegebene Ressourcengruppe nicht existiert, wird sie erstellt.

	- docDBAccountName: Gibt den Namen des zu erstellenden DocumentDB-Kontos an.

	- location: Gibt den Azure-Ort an, an dem die DocumentDB- und Websiteressourcen erstellt werden sollen.  Gültige Werte sind Ostasien, Südostasien, Osten USA, Westen USA, Nordeuropa, Westeuropa (beachten Sie, dass bei dem angegebenen Ortswert die Groß-/Kleinschreibung beachtet werden muss).


3. Hier sehen Sie einen Beispielbefehl zur Ausführung des Skripts:

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Beachten Sie, dass Sie im Rahmen der Skriptausführung zur Eingabe des Benutzernamens und Kennworts für Ihr Azure-Konto aufgefordert werden. Die gesamte Bereitstellung nimmt zwischen 10 und 15 Minuten in Anspruch.  	

4. Hier sehen Sie ein Beispiel der resultierenden Ausgabe: 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Bevor wir die Beispielanwendung näher betrachten, sehen wir uns an, was bei der Vorlagenbereitstellung geschehen ist:

	- Eine Azure-Website wurde erstellt.

	- Ein Azure DocumentDB-Konto wurde erstellt.

	- Ein WebDeploy-Paket wurde in der Azure-Website bereitgestellt.

	- Die Konfiguration der Azure-Website wurde so geändert, dass der Azure DocumentDB-Endpunkt und der primäre Hauptschlüssel als Anwendungseinstellungen angegeben wurden.

	- Eine Reihe von standardmäßigen Überwachungsregeln wurde erstellt.

	
6. Um die Anwendung zu verwenden, navigieren Sie einfach zur Website-URL (im obigen Beispiel wäre die URL http://mydemodocdbwebsite.azurewebsites.net).  Die folgende Webanwendung wird angezeigt:

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. Erstellen Sie nun eine Reihe von Aufgaben, anschließend öffnen wir das [Azure-Vorschauportal](https://portal.azure.com).

8. Durchsuchen Sie die Ressourcengruppen, und wählen Sie die Ressourcengruppe aus, die wir bei der Bereitstellung erstellt haben (im Beispiel oben ist dies myDemoResourceGroup).

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  Beachten Sie, dass die Ressourcenzuordnung im Bereich "Zusammenfassung" alle zugehörigen Ressourcen (DocumentDB-Konto, Website, Überwachung) anzeigt.

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  Klicken Sie auf Ihr DocumentDB-Konto, und starten Sie Abfrage-Explorer (im unteren Bereich des Kontoblatts).

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. Führen Sie die Standardabfrage "SELECT * FROM c" aus, und überprüfen Sie die Ergebnisse.  Beachten Sie, dass die Abfrage die JSON-Darstellung der Aufgaben abgerufen hat, die Sie in Schritt 7 oben erstellt haben.  Sie können gerne mit Abfragen experimentieren; führen Sie SELECT * FROM c WHERE c.isComplete = true aus, um alle Aufgaben zurückzugeben, die als abgeschlossen gekennzeichnet wurden.


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. Erkunden Sie die Oberfläche des DocumentDB-Portals, oder ändern Sie die Todo-Beispielanwendung.  Wenn Sie fertig sind, stellen wir eine andere Vorlage bereit.
	 
## <a id="Build"></a>Schritt 3: Beispiel zum Bereitstellen eines DocumentDB-Kontos und einer Website ##

Jetzt stellen wir unsere zweite Vorlage bereit.

> [AZURE.TIP] Die Vorlage überprüft nicht, ob die unten eingegebenen Namen der Website und des DocumentDB-Kontos (a) gültig und (b) verfügbar sind.  Es wird dringend empfohlen, die Verfügbarkeit der gewünschten Namen zu überprüfen, bevor Sie das PowerShell-Bereitstellungsskript ausführen.

1. Öffnen Sie Microsoft Azure PowerShell, und navigieren Sie zu dem Ordner, in den Sie das [Beispiel zum Erstellen eines DocumentDB-Kontos und einer Website](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) heruntergeladen und extrahiert haben (z. B. C:\DocumentDBTemplates\CreateDocDBWebsite).


2. Wir führen das PowerShell-Skript "CreateDocDBWebsite.ps1" aus.  Das Skript verwendet die gleichen Parameter wie die erste bereitgestellte Vorlage, nämlich:
	- WebsiteName: Gibt den Namen der Website an und wird zum Bilden der URL verwendet, mit der Sie auf die Website zugreifen (wenn Sie z. B. "myotherdocumentdbwebsite" angeben, lautet die URL für den Zugriff auf die Website myotherdocumentdbwebsite.azurewebsites.net).

	- ResourceGroupName: Gibt den Namen der bereitzustellenden Azure-Ressourcengruppe an.  Wenn die angegebene Ressourcengruppe nicht existiert, wird sie erstellt.

	- docDBAccountName: Gibt den Namen des zu erstellenden DocumentDB-Kontos an.

	- 	location: Gibt den Azure-Ort an, an dem die DocumentDB- und Websiteressourcen erstellt werden sollen.  Gültige Werte sind Ostasien, Südostasien, Osten USA, Westen USA, Nordeuropa, Westeuropa (beachten Sie, dass bei dem angegebenen Ortswert die Groß-/Kleinschreibung beachtet werden muss).

3. Hier sehen Sie einen Beispielbefehl zur Ausführung des Skripts:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Beachten Sie, dass Sie im Rahmen der Skriptausführung zur Eingabe des Benutzernamens und Kennworts für Ihr Azure-Konto aufgefordert werden.  Die gesamte Bereitstellung nimmt zwischen 10 und 15 Minuten in Anspruch.  	

4. Die Ausgabe der Bereitstellung sieht der des ersten Vorlagenbeispiels sehr ähnlich. 
5. Bevor wir das Azure-Portal öffnen, sehen wir uns an, was bei der Vorlagenbereitstellung geschehen ist:

	- Eine Azure-Website wurde erstellt.

	- Ein Azure DocumentDB-Konto wurde erstellt.

	- 	Die Konfiguration der Azure-Website wurde so geändert, dass der Azure DocumentDB-Endpunkt, der primäre Hauptschlüssel und der sekundäre Hauptschlüssel als Anwendungseinstellungen angegeben wurden.

	- 	Eine Reihe von standardmäßigen Überwachungsregeln wurde erstellt.

6. Wir öffnen nun das [Azure-Vorschauportal](https://portal.azure.com), durchsuchen die Ressourcengruppen und wählen die Ressourcengruppe aus, die wir bei der Bereitstellung erstellt haben (im Beispiel oben ist dies "myOtherDemoResourceGroup").
7. Klicken Sie im Bereich "Zusammenfassung" auf die soeben bereitgestellte Azure-Website.

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. Klicken Sie auf dem Blatt "Website" auf **Alle Einstellungen** und dann auf **Anwendungseinstellungen**. Beachten Sie, dass Anwendungseinstellungen für den DocumentDB-Endpunkt und jeden der DocumentDB-Hauptschlüssel vorhanden sind.

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. Sie können das Azure-Portal gerne weiter erkunden oder eines der DocumentDB-[Beispiele](http://go.microsoft.com/fwlink/?LinkID=402386) durcharbeiten, um Ihre eigene DocumentDB-Anwendung zu erstellen.

	
	

## <a name="NextSteps"></a>Nächste Schritte

Glückwunsch! Sie haben DocumentDB, Azure-Websites und eine Beispielwebanwendung mithilfe von Vorlagen des Azure-Ressourcen-Managers bereitgestellt.

- Weitere Informationen zu DocumentDB finden Sie [hier](http://azure.com/docdb).
- Weitere Informationen zu Azure-Websites finden Sie [hier](http://go.microsoft.com/fwlink/?LinkId=325362).
- Weitere Informationen zu Vorlagen des Azure-Ressourcen-Managers finden Sie [hier](https://msdn.microsoft.com/library/azure/dn790549.aspx).

<!--HONumber=49-->