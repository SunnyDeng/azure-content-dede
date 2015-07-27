Nachstehend finden Sie die Verwendungs- und andere Einschränkungen für den Azure Active Directory-Dienst.

### Verzeichnisse

Ein einzelner Benutzer kann maximal nur 20 Azure Active Directory-Verzeichnissen zugeordnet werden. In jedem der folgenden Beispiele kann diesen Grenzwert erreicht werden:

- Ein einzelner Benutzer erstellt 20 Verzeichnisse.
- Ein einzelner Benutzer wird 20 Verzeichnissen als Mitglied hinzugefügt.
- Ein einzelner Benutzer erstellt 10 Verzeichnisse und wird später von anderen Benutzern 10 weiteren Verzeichnissen hinzugefügt.

### Objekte

- Für Abonnenten von Azure Active Directory Premium oder Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Microsoft Intune oder anderen Microsoft-Onlinediensten, die Azure Active Directory für Verzeichnisdienste benötigen, gelten keinerlei Einschränkungen.
- Im Tarif "Free" von Azure Active Directory kann ein Verzeichnis maximal 500.000 Objekte enthalten.
- Ein Benutzer ohne Administratorrechte kann bis zu 250 Objekte erstellen.

###Schemaerweiterungen

Derzeit können die Entitäten "User", "Group", "TenantDetail", "Device", "Application" und "ServicePrincipal" mit dem Typ "String" oder "Binary" mit Einzelwertattributen erweitert werden. Dabei gelten die folgenden Einschränkungen:

- Erweiterungen des Typs "String" sind auf maximal 256 Zeichen begrenzt.
- Erweiterungen des Typs "Binary" sind auf 256 Bytes beschränkt.
- 100 Erweiterungswerte (für ALLE Typen und ALLE Anwendungen) können in jedes einzelne Objekt geschrieben werden.
- Schemaerweiterungen sind nur in der Graph-API-Version "1.21-preview" verfügbar. Der Anwendung muss Schreibzugriff zum Registrieren einer Erweiterung gewährt werden.

### Anwendungen

Maximal 10 Benutzer können Besitzer einer einzelnen Anwendung sein.

### Gruppen

- Maximal 10 Benutzer können Besitzer einer einzelnen Gruppe sein.
- Eine beliebige Anzahl von Objekten kann einer einzelnen Gruppe in Azure Active Directory angehören.


> [AZURE.NOTE]
>
> Die Anzahl der Objekte, die Sie aus Ihrem lokalen Active Directory mit Azure Active Directory synchronisieren können, ist begrenzt.
>
> - Bei Verwendung von DirSync liegt die Beschränkung bei 15.000 Benutzern.
> - Bei Verwendung von Azure AD Connect liegt die Beschränkung bei 50.000 Benutzern.

<br/>
### Anpassung des Zugriffsbereichs

- Für Abonnenten von Azure AD Premium oder der Enterprise Mobility Suite gibt es keine Beschränkung der Anzahl von Anwendungen, die pro Endbenutzer im Zugriffsbereich angezeigt werden.
- Für Endbenutzer mit der Edition "Free" oder "Basic" von Azure Active Directory werden maximal 10 App-Titel (z. B. "Box", "Salesforce" oder "Dropbox") im Zugriffsbereich angezeigt. Diese Beschränkung gilt nicht für Administratorkonten.

### Berichte

In einem Bericht können maximal 1.000 Zeilen angezeigt oder heruntergeladen werden. Weitere Daten werden abgeschnitten.

<!---HONumber=July15_HO3-->