<properties 
	pageTitle="Erste Schritte mit der Azure Search Management-REST-API" 
	description="Erste Schritte mit der Azure Search Management-REST-API" 
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
	ms.date="01/16/2015" 
	ms.author="heidist"/>
# Erste Schritte mit der Azure Search Management-REST-API

Die Azure Search Management-REST-API ist eine programmatische Alternative zum Ausführen von Verwaltungsaufgaben im Verwaltungsportal. Dienstverwaltungsvorgänge umfassen Erstellen oder Löschen des Dienstes, Skalieren des Dienstes und Verwalten von Schlüsseln. Dieses Lernprogramm enthält eine Beispielclientanwendung, die die Dienstverwaltungs-API veranschaulicht. Darüber hinaus sind die erforderlichen Konfigurationsschritte zum Ausführen des Beispiels in der lokalen Entwicklungsumgebung enthalten.

Um dieses Lernprogramm abzuschließen, benötigen Sie:

- Visual Studio 2012 oder 2013
- Beispielclientanwendung zum Herunterladen

Im Verlauf des Lernprogramms werden zwei Dienste bereitgestellt: Azure Search und Azure Active Directory (AD). Darüber hinaus erstellen Sie eine AD-Anwendung, die eine Vertrauensstellung zwischen Ihrer Clientanwendung und dem Ressourcenmanagerendpunkt in Azure herstellt.

Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können.


<h2 id="download">Herunterladen der Beispielanwendung</h2>

Dieses Lernprogramm basiert auf einer Windows-Konsolenanwendung, geschrieben in C#, die Sie bearbeiten und in Visual Studio 2012 oder 2013 ausführen können

Die Client-Anwendung finden Sie auf Codeplex unter [Azure Search Management API Demo](https://azuresearchmgmtapi.codeplex.com/).


<h2 id="config">Konfigurieren der Anwendung</h2>

Bevor Sie die Beispielanwendung ausführen können, müssen Sie Authentifizierung aktivieren, damit von der Clientanwendung an den Ressourcenmanagerendpunkt gesendete Anforderungen akzeptiert werden können. Die Authentifizierungsanforderung stammt aus dem [Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), der die Grundlage für alle über eine API angeforderten Portalvorgänge bildet, einschließlich jener im Zusammenhang mit der Search-Dienstverwaltung. Die Dienstverwaltungs-API für Azure Search ist einfach eine Erweiterung des Azure Resource Manager und erbt daher seine Abhängigkeiten.  

Azure Resource Manager benötigt den Azure Active Directory-Dienst als Identitätsanbieter. 

Um ein Zugriffstoken zu erhalten, mit dem Anforderungen den Ressourcenmanager erreichen können, enthält die Clientanwendung ein Codesegment, das Active Directory aufruft. DasCodesegment sowie die erforderlichen Schritte zur Verwendung des Codesegments wurden aus diesem Artikel übernommen: [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Sie können die Anweisungen aus dem oben aufgeführten Link oder die Schritte in diesem Dokument verwenden, wenn Sie es vorziehen, das Lernprogramm Schritt für Schritt zu durchlaufen.

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

1. Erstellen eines AD-Diensts
1. Erstellen einer AD-Anwendung
1. Konfigurieren Sie die AD-Anwendung durch die Registrierung von Details über die Beispielclientanwendung, die Sie heruntergeladen haben.
1. Laden Sie die Beispielclientanwendung mit Werten, die dazu verwendet werden, Autorisierungen für ihre Anforderungen zu erhalten.

> [WACOM.NOTE] Diese Links bieten Hintergrundinformationen zur Verwendung von Azure Active Directory zum Authentifizieren von Clientanforderungen an den Ressourcenmanager: [Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx) und [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

<h3>Erstellen eines Active Directory-Diensts</h3>

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

2. Führen Sie einen Bildlauf nach unten im linken Navigationsbereich aus, und klicken Sie auf **Active Directory**.

4. Klicken Sie auf **NEU**, um **App-Dienste** | **Active Directory** zu öffnen. In diesem Schritt erstellen Sie einen neuen Active Directory-Dienst. Dieser Dienst hostet die AD-Anwendung, die Sie einige Schritte weiter definieren. Das Erstellen eines neuen Diensts hilft dabei, das Lernprogramm von anderen Anwendungen zu isolieren, die Sie bereits in Azure hosten.

5. Klicken Sie auf **Verzeichnis** | **Custom Create**.

6. Geben Sie einen Dienstnamen, eine Domäne und den geografischen Standort ein. Die Domäne muss eindeutig sein. Aktivieren Sie das Kontrollkästchen, um die Warteschlange zu erstellen.

     ![][5]

<h3>Erstellen einer neuen AD-Anwendung für diesen Dienst</h3>

1. Wählen Sie den Active Directory-Dienst "SearchTutorial", den Sie gerade erstellt haben.

2. Klicken Sie im oberen Menü auf **Anwendungen**. 
 
3. Klicken Sie auf **Eine Anwendung hinzufügen**. Eine AD-Anwendung speichert Informationen über die Clientanwendungen, die sie als Identitätsanbieter verwenden.  
 
4. Wählen Sie **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen** aus. Diese Option bietet die Registrierungseinstellungen für Anwendungen, die nicht in der Anwendungsgalerie veröffentlicht werden. Da die Client-Anwendung nicht Teil der Galerie ist, ist dies die richtige Wahl für dieses Lernprogramm.

     ![][6]
 
5. Geben Sie einen Namen ein, z. B. "Azure-Search-Manager".

6. Wählen Sie als Anwendungstyp **Systemeigenen Clientanwendung** aus. Dies ist korrekt für die Beispielanwendung. Es ist eine Windows-Clientnwendung (Konsole), keine Webanwendung.

     ![][7]
 
7. Geben Sie als Umleitungs-URI "http://localhost/Azure-Search-Manager-App" an. Dies ist ein URI, zu dem Azure Active Directory den Benutzer-Agent als Antwort auf eine OAuth 2.0-Autorisierungsanforderung umleitet. Der Wert muss kein physischer Endpunkt, aber unbedingt ein gültiger URI sein. 

Für die Zwecke dieses Lernprogramms ist der Wert beliebig. Was Sie eingeben wird jedoch eine erforderliche Eingabe für die administrative Verbindung in der Beispielanwendung. 
 
7. Klicken Sie auf das Häkchen, um die Active Directory-Anwendung zu erstellen. Im linken Navigationsbereich sollte "Azure-Search-Manager-App" angezeigt werden.

<h3>Konfigurieren der AD-Anwendung</h3>
 
9. Klicken Sie auf die AD-Anwendung "Azure-Search-Manager-App", die Sie gerade erstellt haben. Sie sollte im linken Navigationsbereich angezeigt werden.

10. Klicken Sie im oberen Menü auf **Konfigurieren**.
 
11. Führen Sie einen Bildlauf nach unten zu den Berechtigungen durch, und wählen Sie **Windows Azure Management-API**. In diesem Schritt geben Sie die API an (in diesem Fall die Azure Resource Manager-API), auf welche die Clientanwendung Zugriff benötigt, sowie die erforderliche Ebene des Zugriffs.

12. Klicken Sie in den delegierten Berechtigungen auf die Dropdownliste, und wählen Sie **Access Azure Service Management (Vorschau**) aus.
 
     ![][8]
 
13. Speichern Sie die Änderungen. 

Lassen Sie die Konfigurationsseite geöffnet. Im nächsten Schritt kopieren Sie die Werte auf dieser Seite und geben sie in der Beispielanwendung ein.

<h3>Laden der Beispielanwendung mit Registrierungs- und Abonnementwerten</h3>

In diesem Abschnitt bearbeiten Sie die Projektmappe in Visual Studio und setzen gültige Werte ein, die Sie aus dem Verwaltungsportal abgerufen haben.
Die Werte, die Sie hinzufügen sollen, werden am Anfang der Datei "Program.cs" angezeigt:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Wenn Sie die [Beispielanwendung von Codeplex] (LinkTBD) noch nicht heruntergeladen haben, ist dieser Schritt erforderlich.

1. Öffnen Sie die Datei **ManagementAPI.sln** in Visual Studio.

2. Öffnen Sie die Datei "Program.cs".

3. Geben Sie die "ClientId" ein. Kopieren Sie von der AD-Konfigurationsseite, die noch vom vorherigen Schritt geöffnet ist, die Client-ID der Konfigurationsseite der AD-Anwendung im Verwaltungsportal, und fügen Sie diese in die Datei "Program.cs" ein.

4. Geben Sie die "RedirectUrl" ein. Kopieren Sie den Umleitungs-URI von der gleichen Portalseite, und fügen Sie ihn in die Datei "Program.cs" ein.

	![][9]

5. Geben Sie die "TenantID" ein. 
	- Gehen Sie zurück zu Active Directory | SearchTutorial (Dienst). 
	- Klicken Sie in der oberen Leiste auf **Anwendungen**. 
	- Klicken Sie unten auf der Seite auf **Endpunkte anzeigen**. 
	- Kopieren Sie den OAUTH 2.0-Autorisierungsendpunkt unten auf der Liste. 
	- Fügen Sie den Endpunkt unter "TenantID" ein, und verkürzen Sie dabei den Wert aller URI-Parameter, mit Ausnahme der Mandanten-ID.

    Wenn "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0" ist, löschen Sie alles mit Ausnahme von "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

	![][10]

6. Geben Sie die "SubscriptionID" an.
	- Wechseln Sie zur Hauptseite des Portals.
	- Klicken Sie unten im linken Navigationsbereich auf **Einstellungen **.
	- Kopieren Sie die Abonnement-ID aus der Registerkarte "Abonnements", und fügen Sie diese in die Datei "Program.cs" ein.

7. Speichern und erstellen Sie die Projektmappe.


<h3>Beheben von Buildfehlern</h3>

Probleme mit Assemblyverweisen können verhindern, dass eine Lösung erstellt wird. In diesem Abschnitt werden mögliche Problemumgehungen für einige bereitgestellt.

- Microsoft.IdentityModel.Clients.ActiveDirectory muss installiert sein (Unter [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx) finden Sie Details zum Installieren der [Active Directory-Authentifizierungsbibliothek ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)).
- Neuinstallation einer Assembly kann ein Problem auch beheben. Gehen Sie zu **Extras** | **NuGet-Paket-Manager** | **Paket-Manager-Konsole**. Geben Sie an der Paket-Manager-Eingabeaufforderung folgenden Befehl ein: *Update-package -reinstall Microsoft.IdentityModel.Clients.ActiveDirectory*.
-  Auf Newtonsoft.Json wird auch in dieser Lösung verwiesen. Wenn für diese Assembly ein Fehler auftritt, löschen Sie diese aus dem Projekt und fügen Sie sie anschließend erneut hinzu. Klicken Sie mit der rechten Maustaste auf **Verweise** | **NuGet-Pakete verwalten** | **Installierte Pakete**. Wählen Sie **Json.Net** und deinstallieren sie dies. Erweitern Sie **Online**, wählen Sie **Json.Net** | **Installieren**.


<h2 id="explore">Erkunden der Anwendung</h2>

Fügen Sie einen Haltepunkt am ersten Methodenaufruf ein, sodass Sie das Programm schrittweise durchgehen können. Drücken Sie **F5**, um die Anwendung auszuführen, und drücken Sie **F11**, um den Code schrittweise zu durchlaufen.

Die Beispielanwendung erstellt einen kostenlosen Azure Search-Dienst für ein vorhandenes Azure-Abonnement. Wenn ein kostenloser Dienst für Ihr Abonnement bereits vorhanden ist, schlägt die Beispielanwendung fehl. Nur ein kostenloser Search-Dienst pro Abonnement ist zulässig.

1. Öffnen Sie im Projektmappen-Explorer "Program.cs", und gehen Sie zur Funktion Main (string[] void). 
 
3. Beachten Sie, dass **ExecuteArmRequest** verwendet wird, um Anforderungen für den Azure Resource Manager-Endpunkt "https://management.azure.com/subscriptions" für eine angegebene "SubscriptionID" auszuführen. Diese Methode wird in der gesamten Anwendung für Operationen mit der Azure Resource Manager-API oder der Search Management-API verwendet.

3. Anforderungen an den Azure Resource Manager müssen authentifiziert und autorisiert werden. Dies geschieht mithilfe der Methode **GetAuthorizationHeader**, die von der Methode **ExecuteArmRequest** aufgerufen wird, aus [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](http://msdn.microsoft.com/library/azure/dn790557.aspx). Beachten Sie, dass **GetAuthorizationHeader** "https://management.core.windows.net" aufruft, um ein Zugriffstoken zu erhalten.

4. Sie werden aufgefordert, Sie sich mit einem Benutzernamen und Kennwort anzumelden, das für Ihr Abonnement gültig ist.

5. Als Nächstes wird ein neuer Azure Search-Dienst beim Azure Resource Manager-Anbieter registriert. Dies ist wiederum die **ExecuteArmRequest**-Methode. Dieses Mal wird sie verwendet, um den Search-Dienst in Azure für Ihr Abonnement über "providers/Microsoft.Search/register" zu erstellen. 

6. Der Rest des Programms verwendet die [Azure Search Management-REST-API](http://msdn.microsoft.com/library/dn832684.aspx). Beachten Sie, dass sich die "api-version" für diese API von der des Azure Resource Manager unterscheidet. Z. B. zeigt "/listAdminKeys?api-version=2014-07-31-Preview" die "api-version" der Azure Search Management-REST-API.

	Die nächste Folge von Vorgängen ruft die Dienstdefinition ab, die Sie gerade erstellt haben, die Admin-API-Schlüssel, regeneriert und ruft Schlüssel ab, ändert das Replikat und die Partition und löscht schließlich den Dienst.

Wenn Sie die Anzahl der Dienstreplikate oder Partitionen ändern, wird davon ausgegangen, dass diese Aktion fehlschlägt, wenn Sie die kostenlose Edition verwenden. Sie können nur mit der Standard-Edition zusätzliche Partitionen und Replikate verwenden.

	Das Löschen des Diensts ist der letzte Vorgang.

<h2 id="next-steps">Nächste Schritte</h2>

Nach Abschluss dieses Lernprogramms empfiehlt es sich, mehr über die Dienstverwaltung oder die Authentifizierung mit dem Active Directory-Dienst zu erfahren:

- Erfahren Sie mehr über die Integration einer Clientanwendung mit Active Directory. Siehe [Integrieren von Anwendungen in Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Erfahren Sie mehr zu anderen Dienstverwaltungsvorgängen in Azure. Siehe [Verwalten von Diensten](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Herunterladen der Beispielanwendung]: #Download
[Konfigurieren der Anwendung]: #config
[Erkunden der Anwendung]: #explore
[Nächste Schritte]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Verwalten Ihrer Suchlösung in Microsoft Azure]: ../search-manage/
[Azure Search-Entwicklungsworkflow]: ../search-workflow/
[Erstellen Sie Ihre erste Azure Search-Lösung]: ../search-create-first-solution/
[Erstellen einer geografischen Suchanwendung mit Azure Search]: ../search-create-geospatial/



<!--HONumber=46--> 
