## <a name="what-is"></a>Windows Azure Mobile Services

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

+ **.NET-Back-End:** Diese Art des mobilen Dienstes wird als ASP.NET Web API-Projekt implementiert. Back-End-Code wird als WebAPI-Controller implementiert.
+ **Anwendungsschlüssel:** ein eindeutiger Wert, der von Mobile Services generiert, mit Ihrer App verteilt und in vom Client generierten Anforderungen präsentiert wird. Der Schlüssel ist zwar sehr nützlich, um zu verhindern, dass beliebige Clients auf Ihren mobilen Dienst zugreifen können, er ist allerdings nicht sicher und sollte daher nicht verwendet werden, um die Benutzer Ihrer App zu authentifizieren.    
+ **Authentifizierungstoken:** das Zugriffstoken, das von Mobile Services generiert wird, nachdem ein Benutzer authentifiziert wurde
+ **Identitätsanbieter:** externer Dienst, z. B. Facebook, Twitter, Google, ein Microsoft-Konto oder Azure Active Directory, der verwendet wird, um Benutzer Ihres mobilen Diensts zu authentifizieren
+ **JavaScript-Back-End:** Diese Art des mobilen Dienstes wird als node.js implementiert. Back-End-Code wird in JavaScript geschrieben.
+ **Berechtigung:** die minimale Authentifizierungsebene, die zum Aufrufen eines Tabellenvorgangs oder einer benutzerdefinierten API erforderlich ist
+ **Pushbenachrichtigung:** vom Dienst initiierte Nachricht, die über einen Azure-Benachrichtigungs-Hub an ein registriertes Gerät oder einen registrierten Benutzer gesendet wird
+ **Skalierung:** die Möglichkeit, gegen weitere Kosten mehr Rechenleistung, Performance und Speicher hinzuzukaufen, wenn Ihre App an Beliebtheit zunimmt
+ **Geplanter Auftrag:** benutzerdefinierter Code, der anhand eines vorab geplanten Termins oder auf Anforderung ausgeführt wird
+ **Serverskript:** benutzerdefinierter Code mit Geschäftslogik in einem mobilen Dienst mit JavaScript-Back-End. Dieser Code wird für einen Tabellenvorgang (Lesen, Einfügen, Aktualisieren, Löschen), eine benutzerdefinierte API oder einen geplanten Auftrag registriert.
+ **Tabelle:** Benutzerdaten werden in Tabellen in einer SQL-Datenbank gespeichert. Sie können diese Tabellen im Verwaltungsportal erstellen.


<!-- Images. -->


  

<!--HONumber=42-->
