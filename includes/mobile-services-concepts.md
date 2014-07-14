
## <a name="what-is"></a>Azure Mobile Services

Mobile Services ist ein Dienstangebot von Azure, um die Erstellung hochfunktioneller mobiler Apps mit Azure zu vereinfachen. Mobile Services vereint verschiedene Azure-Dienste, die Backendfunktionen für Ihre Apps unterstützen. Mobile Services bieten zur Unterstützung Ihrer Apps die folgenden Backendfunktionen in Azure an:

* Einfache Bereitstellung und Verwaltung von Tabellen zum Speichern von
  App-Daten
* Integration mit Benachrichtigungsdiensten zum Übermitteln von
  Pushbenachrichtigungen an Ihre App
* Integration mit bekannten Identitätsanbietern für
  Authentifizierungszwecke
* Präzise Steuerung zur Autorisierung des Tabellenzugriffs
* Benutzerdefinierte Geschäftslogik auf dem Server
* Integration mit anderen Clouddiensten
* Unterstützt die Skalierung einer mobilen Dienstinstanz
* Dienstüberwachung und -protokollierung

Mobile Services stellt eine Clientbibliothek für jede unterstützte Plattform bereit, was die Entwicklung von Apps auf einer Plattform, die Mobile Services nutzt, vereinfacht.

## <a name="concepts"> </a>Konzepte

Mobile Services umfasst folgende wichtige Funktionen und basiert auf den folgenden Konzepten:

<!--![1][1]-->

* **Anwendungsschlüssel:** Ein eindeutiger Wert, der von Mobile Services generiert, mit Ihrer App verteilt und in vom Client generierten Anforderungen präsentiert wird. Der Schlüssel ist zwar sehr nützlich, um den Zugriff auf Ihren mobilen Dienst von beliebigen Clients aus einzuschränken, er ist allerdings nicht sicher und sollte daher nicht verwendet werden, um die Benutzer Ihrer App zu authentifizieren.

* **Authentifizierungstoken:** Der Zugriffstoken, der von Mobile Services generiert wird, nachdem ein Benutzer authentifiziert wurde.

* **Dynamisches Schema:** Die Fähigkeit von Mobile Services, automatisch eine Spalte zu einer Tabelle basierend auf den Feldern des JSON-Objekts hinzuzufügen, das in einer Einfüge- oder Aktualisierungsanforderung gesendet wurde. Das dynamische Schema sollte deaktiviert werden, wenn Ihre App produktiv geschaltet wird.

* **Identitätsanbieter:** Externer Dienst, z. B. Facebook, Twitter, Google oder ein Microsoft-Konto, der verwendet wird, um Benutzer Ihres mobilen Diensts zu authentifizieren.

* **Berechtigung:** Die minimale Authentifizierungsebene, die zum Aufrufen eines Tabellenvorgangs erforderlich ist.

* **Pushbenachrichtigung:** Vom Dienst initiierte Nachricht, die an ein registriertes Gerät oder einen registrierten Benutzer gesendet wird.

* **Skalierung:** Die Möglichkeit, gegen weitere Kosten mehr Rechenleistung, Performance und Speicher hinzuzukaufen, wenn Ihre App an Beliebtheit zunimmt.

* **Geplanter Auftrag:** Serverskriptcode, der anhand eines vorab geplanten Termins oder auf Anforderung ausgeführt wird.

* **Serverskript:** JavaScript-Code für benutzerdefinierte Geschäftslogik, die auf dem Server gespeichert wird. Dieser Code wird für einen Tabellenvorgang (Lesen, Einfügen, Aktualisieren, Löschen) oder einen geplanten Auftrag registriert.

* **Tabelle:** Benutzerdaten werden in Tabellen in einer SQL-Datenbank gespeichert. Sie können diese Tabellen im Verwaltungsportal erstellen.

* **Tabellenvorgang:** Eine HTTP-Anforderung vom Client zum Lesen, Einfügen, Aktualisieren oder Löschen von Tabellendaten.

<!-- Images. -->