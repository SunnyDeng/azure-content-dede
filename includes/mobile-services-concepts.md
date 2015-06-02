## <a name="what-is"></a>Was ist Mobile Services

Mobile Services ist ein Dienstangebot von Azure, um die Erstellung hochfunktioneller mobiler Apps mit Azure zu vereinfachen. Mobile Services vereint verschiedene Azure-Dienste, die Backendfunktionen für Ihre Apps unterstützen. Mobile Services stellen die folgenden Funktionen zur Unterstützung Ihrer Apps bereit:

+ Einfache Bereitstellung und Verwaltung von Tabellen zum Speichern von App-Daten 
+ Integration mit Benachrichtigungsdiensten zum Übermitteln von Pushbenachrichtigungen an Ihre App
+ Integration mit bekannten Identitätsanbietern für Authentifizierungszwecke
+ Präzise Steuerung zur Autorisierung des Tabellenzugriffs
+ Benutzerdefinierte Geschäftslogik auf dem Server
+ Integration mit anderen Clouddiensten
+ Unterstützt die Skalierung einer mobilen Dienstinstanz
+ Dienstüberwachung und -protokollierung

Mobile Services stellt eine Clientbibliothek für jede unterstützte Plattform bereit, was die Entwicklung von Apps auf einer Plattform, die Mobile Services nutzt, vereinfacht.

## <a name="concepts"> </a>Konzepte

Mobile Services umfasst folgende wichtige Funktionen und basiert auf den folgenden Konzepten:

<!--![1][1]-->

+ **.NET backend:** Diese Art des mobilen Dienstes wird als ASP.NET Web API-Projekt implementiert. Backend-Code wird als WebAPI-Controller implementiert.
+ **Anwendungsschlüssel:** Ein eindeutiger Wert, der von Mobile Services generiert, mit Ihrer App verteilt und in vom Client generierten Anforderungen präsentiert wird. Der Schlüssel ist zwar sehr nützlich, um den Zugriff auf Ihren mobilen Dienst von beliebigen Clients aus einzuschränken, er ist allerdings nicht sicher und sollte daher nicht verwendet werden, um die Benutzer Ihrer App zu authentifizieren.    
+ **Authentifizierungstoken:** Das Zugriffstoken, das von Mobile Services generiert wird, nachdem ein Benutzer authentifiziert wurde.
+ **Identitätsanbieter:** Externer Dienst, z. B. Facebook, Twitter, Google, ein Microsoft-Konto oder Azure Active Directory, der verwendet wird, um Benutzer Ihres mobilen Diensts zu authentifizieren.
+ **JavaScript-Back-End:** Diese Art des mobilen Dienstes wird als node.js implementiert. Backend-Code wird in JavaScript geschrieben.
+ **Berechtigung:** Die minimale Authentifizierungsebene, die zum Aufrufen eines Tabellenvorgangs oder einer benutzerdefinierten API erforderlich ist.  
+ **Pushbenachrichtigung:** Vom Dienst initiierte Nachricht, die über Azure Notification Hubs an ein registriertes Gerät oder einen registrierten Benutzer gesendet wird.
+ **Skalierung:** Die Möglichkeit, gegen eine Zusatzgebühr mehr Rechenleistung, Performance und Speicher hinzuzukaufen, wenn Ihre App am Popularität gewinnt.
+ **Geplanter Auftrag:** Benutzerdefinierter Code, der anhand eines vorab geplanten Termins oder auf Anforderung ausgeführt wird.
+ **Serverskript:** Benutzerdefinierter Code mit Geschäftslogik in einem mobilen Dienst mit JavaScript-Back-End. Dieser Code wird für einen Tabellenvorgang (Lesen, Einfügen, Aktualisieren, Löschen) oder einen geplanten Auftrag registriert.
+ **Tabelle:** Benutzerdaten werden in Tabellen in einer SQL-Datenbank gespeichert. Sie können diese Tabellen im Verwaltungsportal erstellen.


<!-- Images. -->

<!--HONumber=54-->