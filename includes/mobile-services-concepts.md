## <a name="what-is"></a>Was ist Mobile Services

Azure Mobile Services ist eine hochgradig skalierbare mobile Anwendungsentwicklungsplattform, mit der Sie Ihren mobilen Anwendungen mithilfe von Azure erweiterte Funktionalität hinzufügen können.

Mit Mobile Services haben Sie folgende Möglichkeiten:

+ **Systemeigene und plattformübergreifende Apps erstellen**: Verbinden Sie Ihre iOS-, Android-, Windows- oder plattformübergreifenden Xamarin- oder Cordova-Apps (Phonegap) mithilfe der systemeigenen SDKs mit dem mobilen Back-End-Dienst.  
+ **Push-Benachrichtigungen an Ihre Benutzer senden:** Senden Sie Push-Benachrichtigungen an die Benutzer Ihrer App.
+ **Benutzer authentifizieren:** Nutzen Sie beliebte Identitätsanbieter wie Facebook und Twitter, um Ihre App-Benutzer zu authentifizieren.
+ **Daten in der Cloud speichern:** Speichern Sie Benutzerdaten in einer SQL-Datenbank (Standard) oder in Mongo DB, DocumentDB, Azure-Tabellen oder Azure-Blobs. 
+ **Offline-Ready-Anwendungen mit Synchronisierung erstellen:** Machen Sie Ihre Apps im Offlinemodus verfügbar, und verwenden Sie Mobile Services, um Daten im Hintergrund zu synchronisieren.
+ **Apps überwachen und skalieren:** Überwachen Sie die App-Verwendung, und skalieren Sie Ihr Back-End bei wachsendem Bedarf. 

## <a name="concepts"> </a>Mobile Services-Konzepte

Mobile Services umfasst folgende wichtige Funktionen und basiert auf den folgenden Konzepten:

+ **Anwendungsschlüssel:** ein eindeutiger Wert, der zum Einschränken des Zugriffs von zufälligen Clients auf den mobilen Dienst verwendet wird. Dieser "Schlüssel" ist kein Sicherheitstoken und wird nicht zum Authentifizieren von Benutzern der Anwendung verwendet.    
+ **Back-End:** die Instanz des mobilen Diensts, die Ihre App unterstützt. Ein mobiler Dienst wird entweder als ASP.NET-Web-API-Projekt (*.NET-Back-End*) oder als Node.js-Projekt (*JavaScript-Back-End*) implementiert.
+ **Identitätsanbieter:** ein externer Dienst mit Mobile Services-Vertrauensstellung, der Ihre App-Benutzer authentifiziert. Folgende Anbieter werden unterstützt: Facebook, Twitter, Google, Microsoft-Konto und Azure Active Directory. 
+ **Pushbenachrichtigung:** Vom Dienst initiierte Nachricht, die über Azure Notification Hubs an ein registriertes Gerät oder einen registrierten Benutzer gesendet wird.
+ **Skalierung:** Die Möglichkeit, gegen eine Zusatzgebühr mehr Rechenleistung, Performance und Speicher hinzuzukaufen, wenn Ihre App am Popularität gewinnt.
+ **Geplanter Auftrag:** Benutzerdefinierter Code, der anhand eines vorab geplanten Termins oder auf Anforderung ausgeführt wird.

<!---HONumber=July15_HO3-->