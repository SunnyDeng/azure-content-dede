<properties 
	pageTitle="Erste Schritte mit der Azure Search Management-REST-API | Microsoft Azure" 
	description="Verwalten Ihres in der Cloud gehosteten Azure Search-Diensts mithilfe einer Verwaltungs-REST-API" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Erste Schritte mit der Azure Search Management-REST-API

Die Azure Search Management-REST-API ist eine programmatische Alternative zum Ausführen von Verwaltungsaufgaben im Verwaltungsportal. Dienstverwaltungsvorgänge umfassen Erstellen oder Löschen des Dienstes, Skalieren des Dienstes und Verwalten von Schlüsseln. Dieses Lernprogramm enthält eine Beispielclientanwendung, die die Dienstverwaltungs-API veranschaulicht. Darüber hinaus sind die erforderlichen Konfigurationsschritte zum Ausführen des Beispiels in der lokalen Entwicklungsumgebung enthalten.

Um dieses Lernprogramm abzuschließen, benötigen Sie:

- Visual Studio 2012 oder 2013
- Beispielclientanwendung zum Herunterladen

Im Verlauf des Lernprogramms werden zwei Dienste bereitgestellt: Azure Search und Active Directory (AD). Darüber hinaus erstellen Sie eine AD-Anwendung, die eine Vertrauensstellung zwischen Ihrer Clientanwendung und dem Ressourcenmanagerendpunkt in Azure herstellt.

Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können:


##Herunterladen der Beispielanwendung

Dieses Lernprogramm basiert auf einer Windows-Konsolenanwendung, geschrieben in C#, die Sie bearbeiten und in Visual Studio 2012 oder 2013 ausführen können

Die Clientanwendung finden Sie auf Codeplex unter [Azure Search Management API Demo](https://azuresearchmgmtapi.codeplex.com/).


##Konfigurieren der Anwendung

Bevor Sie die Beispielanwendung ausführen können, müssen Sie Authentifizierung aktivieren, damit von der Clientanwendung an den Ressourcenmanagerendpunkt gesendete Anforderungen akzeptiert werden können. Die Authentifizierungsanforderung stammt aus dem [Azure-Ressourcen-Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), der die Grundlage für alle über eine API angeforderten Portalvorgänge bildet, einschließlich jener im Zusammenhang mit der Search-Dienstverwaltung. Die Dienstverwaltungs-API für Azure Search ist einfach eine Erweiterung des Azure Resource Manager und erbt daher seine Abhängigkeiten.

Azure Resource Manager benötigt den Azure Active Directory-Dienst als Identitätsanbieter.

Um ein Zugriffstoken zu erhalten, mit dem Anforderungen den Ressourcenmanager erreichen können, enthält die Clientanwendung ein Codesegment, das Active Directory aufruft. Das Codesegment sowie die erforderlichen Schritte zur Verwendung des Codesegments wurden aus diesem Artikel übernommen: [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Sie können die Anweisungen aus dem oben aufgeführten Link oder die Schritte in diesem Dokument verwenden, wenn Sie es vorziehen, das Lernprogramm Schritt für Schritt zu durchlaufen.

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

1. Erstellen eines AD-Diensts
1. Erstellen einer AD-Anwendung
1. Konfigurieren Sie die AD-Anwendung durch die Registrierung von Details über die Beispielclientanwendung, die Sie heruntergeladen haben.
1. Laden Sie die Beispielclientanwendung mit Werten, die dazu verwendet werden, Autorisierungen für ihre Anforderungen zu erhalten.

> [AZURE.NOTE]Diese Links bieten Hintergrundinformationen zur Verwendung von Azure Active Directory zum Authentifizieren von Clientanforderungen an den Ressourcen-Manager: [Azure-Ressourcen-Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), [Authentifizieren von Azure-Ressourcen-Manager-Anforderungen](http://msdn.microsoft.com/library/azure/dn790557.aspx) und [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###Erstellen eines Active Directory-Diensts

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

2. Führen Sie im linken Navigationsbereich einen Bildlauf nach unten aus, und klicken Sie auf **Active Directory**.

4. Klicken Sie auf **NEU**, um **App-Dienste** | **Active Directory** zu öffnen. In diesem Schritt erstellen Sie einen neuen Active Directory-Dienst. Dieser Dienst hostet die AD-Anwendung, die Sie einige Schritte weiter definieren. Das Erstellen eines neuen Diensts hilft dabei, das Lernprogramm von anderen Anwendungen zu isolieren, die Sie bereits in Azure hosten.

5. Klicken Sie auf **Verzeichnis** | **Benutzerdefiniert erstellen**.

6. Geben Sie einen Dienstnamen, eine Domäne und den geografischen Standort ein. Die Domäne muss eindeutig sein. Aktivieren Sie das Kontrollkästchen, um die Warteschlange zu erstellen.

     ![][5]

###Erstellen einer neuen AD-Anwendung für diesen Dienst

1. Wählen Sie den Active Directory-Dienst "SearchTutorial", den Sie gerade erstellt haben.

2. Klicken Sie im oberen Menü auf **Anwendungen**.
 
3. Klicken Sie auf **Eine Anwendung hinzufügen**. Eine AD-Anwendung speichert Informationen über die Clientanwendungen, die sie als Identitätsanbieter verwenden.
 
4. Wählen Sie **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen** aus. Diese Option bietet die Registrierungseinstellungen für Anwendungen, die nicht in der Anwendungsgalerie veröffentlicht werden. Da die Client-Anwendung nicht Teil der Galerie ist, ist dies die richtige Wahl für dieses Lernprogramm.

     ![][6]
 
5. Geben Sie einen Namen ein, z. B. "Azure-Search-Manager".

6. Wählen Sie als Anwendungstyp **Systemeigene Clientanwendung** aus. Dies ist korrekt für die Beispielanwendung. Es ist eine Windows-Clientnwendung (Konsole), keine Webanwendung.

     ![][7]
 
7. Geben Sie als Umleitungs-URI "http://localhost/Azure-Search-Manager-App" an. Dies ist ein URI, zu dem Azure Active Directory den Benutzer-Agent als Antwort auf eine OAuth 2.0-Autorisierungsanforderung umleitet. Der Wert muss kein physischer Endpunkt, aber unbedingt ein gültiger URI sein.

    Für die Zwecke dieses Lernprogramms ist der Wert beliebig. Was Sie eingeben wird jedoch eine erforderliche Eingabe für die administrative Verbindung in der Beispielanwendung.
 
7. Klicken Sie auf das Häkchen, um die Active Directory-Anwendung zu erstellen. Im linken Navigationsbereich sollte "Azure-Search-Manager-App" angezeigt werden.

###Konfigurieren der AD-Anwendung
 
9. Klicken Sie auf die AD-Anwendung "Azure-Search-Manager-App", die Sie gerade erstellt haben. Sie sollte im linken Navigationsbereich angezeigt werden.

10. Klicken Sie im oberen Menü auf **Konfigurieren**.
 
11. Führen Sie einen Bildlauf nach unten zu den Berechtigungen durch, und wählen Sie **Azure Management-API**. In diesem Schritt geben Sie die API an (in diesem Fall die Azure Resource Manager-API), auf welche die Clientanwendung Zugriff benötigt, sowie die erforderliche Ebene des Zugriffs.

12. Klicken Sie in den delegierten Berechtigungen auf die Dropdownliste, und wählen Sie **Access Azure Service Management (Preview)** aus.
 
     ![][8]
 
13. Speichern Sie die Änderungen.

Lassen Sie die Konfigurationsseite geöffnet. Im nächsten Schritt kopieren Sie die Werte auf dieser Seite und geben sie in der Beispielanwendung ein.

###Laden der Beispielanwendung mit Registrierungs- und Abonnementwerten

In diesem Abschnitt bearbeiten Sie die Projektmappe in Visual Studio und setzen gültige Werte ein, die Sie aus dem Verwaltungsportal abgerufen haben. Die Werte, die Sie hinzufügen sollen, werden am Anfang der Datei "Program.cs" angezeigt:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Wenn Sie die Beispielanwendung noch nicht [von Codeplex heruntergeladen](https://azuresearchmgmtapi.codeplex.com/) haben, ist dieser Schritt jetzt erforderlich.

1. Öffnen Sie die Datei **ManagementAPI.sln** in Visual Studio.

2. Öffnen Sie die Datei Program.cs.

3. Geben Sie `ClientId` an. Kopieren Sie von der AD-Konfigurationsseite, die noch vom vorherigen Schritt geöffnet ist, die Client-ID der Konfigurationsseite der AD-Anwendung im Verwaltungsportal, und fügen Sie diese in die Datei "Program.cs" ein.

4. Geben Sie `RedirectUrl` an. Kopieren Sie den Umleitungs-URI von der gleichen Portalseite, und fügen Sie ihn in die Datei "Program.cs" ein.

	![][9]

5. Angabe von `TenantID.`
	- Gehen Sie zurück zu Active Directory | SearchTutorial (Dienst). 
	- Klicken Sie in der oberen Leiste auf **Anwendungen**. 
	- Klicken Sie unten auf der Seite auf **Endpunkte anzeigen**. 
	- Kopieren Sie den OAUTH 2.0-Autorisierungsendpunkt unten auf der Liste. 
	- Fügen Sie den Endpunkt unter "TenantID" ein, und verkürzen Sie dabei den Wert aller URI-Parameter, mit Ausnahme der Mandanten-ID.

    Wenn "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0" ist, löschen Sie alles mit Ausnahme von "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

	![][10]

6. Geben Sie `SubscriptionID` an.
	- Wechseln Sie zur Hauptseite des Portals.
	- Klicken Sie unten im linken Navigationsbereich auf **Einstellungen** .
	- Kopieren Sie die Abonnement-ID aus der Registerkarte "Abonnements", und fügen Sie diese in die Datei "Program.cs" ein.

7. Speichern und erstellen Sie die Projektmappe.


##Erkunden der Anwendung

Fügen Sie einen Haltepunkt am ersten Methodenaufruf ein, sodass Sie das Programm schrittweise durchgehen können. Drücken Sie **F5**, um die Anwendung auszuführen, und drücken Sie **F11**, um den Code schrittweise zu durchlaufen.

Die Beispielanwendung erstellt einen kostenlosen Azure Search-Dienst für ein vorhandenes Azure-Abonnement. Wenn ein kostenloser Dienst für Ihr Abonnement bereits vorhanden ist, schlägt die Beispielanwendung fehl. Nur ein kostenloser Search-Dienst pro Abonnement ist zulässig.

1. Öffnen Sie im Projektmappen-Explorer "Program.cs", und gehen Sie zur Funktion Main (string void). 
 
3. Beachten Sie, dass **ExecuteArmRequest** verwendet wird, um Anforderungen für den Azure-Ressourcen-Manager-Endpunkt `https://management.azure.com/subscriptions` für eine angegebene `subscriptionID` auszuführen. Diese Methode wird in der gesamten Anwendung für Operationen mit der Azure Resource Manager-API oder der Search Management-API verwendet.

3. Anforderungen an den Azure Resource Manager müssen authentifiziert und autorisiert werden. Dies geschieht mithilfe der **GetAuthorizationHeader**-Methode, die von der **ExecuteArmRequest**-Methode aufgerufen wird, die aus [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx) übernommen wurde. Beachten Sie, dass **GetAuthorizationHeader** `https://management.core.windows.net` aufruft, um ein Zugriffstoken zu erhalten.

4. Sie werden aufgefordert, Sie sich mit einem Benutzernamen und Kennwort anzumelden, das für Ihr Abonnement gültig ist.

5. Als Nächstes wird ein neuer Azure Search-Dienst beim Azure Resource Manager-Anbieter registriert. Dies ist wiederum die **ExecuteArmRequest**-Methode. Dieses Mal wird sie verwendet, um den Search-Dienst in Azure über `providers/Microsoft.Search/register` für Ihr Abonnement zu erstellen.

6. Der Rest des Programms verwendet die [Azure Search Management-REST-API](http://msdn.microsoft.com/library/dn832684.aspx). Beachten Sie, dass sich die `api-version` für diese API von der API-Version des Azure-Ressourcen-Managers unterscheidet. Beispielsweise enthält `/listAdminKeys?api-version=2014-07-31-Preview` die `api-version` der Azure Search Management-REST-API.

	Die nächste Folge von Vorgängen ruft die Dienstdefinition ab, die Sie gerade erstellt haben, die Admin-API-Schlüssel, regeneriert und ruft Schlüssel ab, ändert das Replikat und die Partition und löscht schließlich den Dienst.

	Wenn Sie die Anzahl der Dienstreplikate oder Partitionen ändern, wird davon ausgegangen, dass diese Aktion fehlschlägt, wenn Sie die kostenlose Edition verwenden. Sie können nur mit der Standard-Edition zusätzliche Partitionen und Replikate verwenden.

	Das Löschen des Diensts ist der letzte Vorgang.

##Nächste Schritte

Nach Abschluss dieses Lernprogramms empfiehlt es sich, mehr über die Dienstverwaltung oder die Authentifizierung mit dem Active Directory-Dienst zu erfahren:

- Erfahren Sie mehr über die Integration einer Clientanwendung mit Active Directory. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Erfahren Sie mehr zu anderen Dienstverwaltungsvorgängen in Azure. Weitere Informationen finden Sie unter [Verwalten von Diensten](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 

<!---HONumber=Nov15_HO2-->