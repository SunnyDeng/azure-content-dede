<properties
	pageTitle="Hinzufügen der Office 365-Benutzer-API zu Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die Office 365-Benutzer-API und REST-API-Parameter"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/25/2016"
ms.author="deonhe"/>

# Erste Schritte mit der Office 365-Benutzer-API

Stellen Sie eine Verbindung mit der Office 365-Benutzer-API her, um Profile abzurufen, Benutzer zu suchen und vieles mehr. Die Office 365-Benutzer-API kann verwendet werden von:

- PowerApps 
- Logik-Apps 

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion „2014-12-01-preview“ aufzurufen, klicken Sie auf [Office 365-API](../app-service-logic/app-service-logic-connector-office365.md).


Mit der Office 365-Benutzer-API können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus der Office 365-Benutzer-API abgerufen werden. 
- Verwenden von Aktionen zum Abrufen von direkt unterstellten Mitarbeitern oder Benutzerprofilen von Vorgesetzten und vieles mehr. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Rufen Sie beispielsweise die direkt unterstellten Mitarbeiter einer Person ab, und aktualisieren Sie mit diesen Informationen eine Azure SQL-Datenbank. 
- Fügen Sie die Office 365-Benutzer-API in PowerApps Enterprise hinzu. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Office 365-Benutzer-API verfügt über die folgenden Aktionen. Es gibt keine Trigger.

| Trigger | Aktionen|
| --- | --- |
|Keine | <ul><li>Vorgesetzten abrufen</li><li>Mein Profil abrufen</li><li>Direkt unterstellte Mitarbeiter abrufen</li><li>Benutzerprofil abrufen</li><li>Nach Benutzern suchen</li></ul>|

Alle APIs unterstützen Daten im JSON- und XML-Format.


## Herstellen einer Verbindung mit Office 365-Benutzern

### Hinzufügen zusätzlicher Konfigurationen in PowerApps
Wenn Sie diese API in PowerApps Enterprise hinzufügen, geben Sie die Werte für die **Client-ID** und den **geheimen Clientschlüssel** Ihrer Office 365-AAD-Anwendung (Azure Active Directory) ein. Der Wert von **Umleitungs-URL** wird auch in Ihrer Office 365-Anwendung verwendet. Wenn Sie noch keine Office 365-Anwendung haben, führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Öffnen Sie das [Azure-Portal][5], dann **Active Directory**, und wählen Sie den Namen des Mandanten Ihrer Organisation aus.
2. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus:  
![AAD-Mandanten-Anwendungen][7]

3. Auf der Seite **Anwendung hinzufügen**:  

	1. Geben Sie einen **Namen** für Ihre Anwendung ein.  
	2. Lassen Sie als Anwendungstyp **Web** ausgewählt.  
	3. Wählen Sie **Weiter**.  

	![AAD-Anwendung hinzufügen – App-Info][8]

4. Unter **App-Eigenschaften**:  

	1. Geben Sie unter **ANMELDE-URL** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest.  
	2. Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein.  
	3. Klicken Sie auf **OK**.  

	![AAD-Anwendung hinzufügen – App-Eigenschaften][9]

5. Wenn Sie fertig sind, wird die neue AAD-App geöffnet. Wählen Sie **Konfigurieren** aus:  
![Contoso-AAD-App][10]

6. Legen Sie die **Antwort-URL** im Abschnitt **OAuth 2** auf den Wert der Umleitungs-URL fest, der beim Hinzufügen der Office 365-Benutzer-API im Azure-Portal angezeigt wurde. Wählen Sie **Anwendung hinzufügen** aus:  
![Contoso-AAD-App konfigurieren][11]

7. Wählen Sie unter **Berechtigungen für andere Anwendungen** die Option **Office 365 Unified API (Vorschau)** und dann **OK**.

	Auf der Konfigurationsseite können Sie nun sehen, dass _Office 365 Unified API (Vorschau)_ der Liste _Berechtigungen für andere Anwendungen_ hinzugefügt wurde.

8. Wählen Sie für **Office 365 Exchange Online** die Option **Delegierte Berechtigungen** aus, und wählen Sie dann die Berechtigung **Alle grundlegenden Benutzerprofile lesen**.

Eine neue Azure Active Directory-App wird erstellt. Sie können die **Client-ID** und den **geheimen Clientschlüssel** kopieren und in Ihre Konfiguration der Office 365-Benutzer-API im Azure-Portal einfügen.

Einige hilfreiche Informationen zu AAD-Anwendungen finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../active-directory/active-directory-how-applications-are-added.md).


### Hinzufügen zusätzlicher Konfiguration in Logik-Apps
Wenn Sie Ihren Logik-Apps diese API hinzufügen, müssen Sie sich bei Ihrem Konto für die Office 365-Benutzer-API anmelden und den Logik-Apps das Herstellen einer Verbindung mit Ihrem Konto gestatten.

1. Melden Sie sich bei Ihrem Konto für die Office 365-Benutzer-API an.
2. Erlauben Sie Ihren Logik-Apps, sich mit Ihrem Office 365-Konto zu verbinden und es zu nutzen. 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Eigenschaften für die Office 365-Benutzer-API ein, z. B. die Benutzer-ID. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Verbindung der Office 365-Benutzer-API in anderen Logik-Apps verwenden.


## Office 365-Benutzer-REST-API – Referenz
Gilt für Version: 1.0.

### Mein Profil abrufen 
Ruft das Benutzerprofil für den aktuellen Benutzer ab.  
```GET: /users/me```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|


### Benutzerprofil abrufen 
Ruft ein bestimmtes Benutzerprofil ab.  
```GET: /users/{userId}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|


### Vorgesetzten abrufen 
Ruft das Benutzerprofil des Vorgesetzten des angegebenen Benutzers ab.  
```GET: /users/{userId}/manager```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|



### Direkt unterstellte Mitarbeiter abrufen 
Ruft direkt unterstellte Mitarbeiter ab.  
```GET: /users/{userId}/directReports```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|



### Nach Benutzern suchen 
Ruft die Suchergebnisse für Benutzerprofile ab.  
```GET: /users```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|searchTerm|string|no|query|(Keine)|Suchzeichenfolge (gilt für: Anzeigename, Vorname, Nachname, E-Mail, E-Mail-Spitzname und Benutzerprinzipalname)|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|



## Objektdefinitionen

#### Benutzer: Modellklasse „User“.

| Name | Datentyp |Erforderlich
|---|---|---|
|DisplayName|string|no|
|GivenName|string|no|
|Surname|string|no|
|Mail|string|no|
|MailNickname|string|no|
|TelephoneNumber|string|no|
|AccountEnabled|Boolescher Wert|no|
|ID|string|Ja
|UserPrincipalName|string|no|
|Abteilung|string|no|
|JobTitle|string|no|
|mobilePhone|string|no|


## Nächste Schritte
Nach dem Hinzufügen der Office 365-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen Sie eine Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0302_2016-->


