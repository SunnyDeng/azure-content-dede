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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Erste Schritte mit der Office 365-Benutzer-API

Mit dem Office 365-Benutzerverbindungsanbieter können Sie mit Ihrem Office 365-Konto auf Benutzerprofile in Ihrer Organisation zugreifen. Sie können verschiedene Aktionen ausführen, wie z. B. Abrufen Ihres Profils, eines Benutzerprofils, des Profils des Vorgesetzten des Benutzers oder von direkt unterstellten Mitarbeitern sowie Aktualisieren eines Benutzerprofils.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [Office 365-API](../app-service-logic/app-service-logic-connector-office365.md).


Mit der Office 365-Benutzer-API können Sie folgende Aktionen ausführen:

* Erstellen von Logik-Apps
* Erstellen von Power-Apps

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Office 365-Benutzer-API kann als Aktion verwendet werden. Es gibt keine Trigger. Alle APIs unterstützen Daten im JSON- und XML-Format.

 Die Office 365-Benutzer-API verfügt über die folgenden Aktionen und/oder Trigger:

### Office 365-Benutzeraktionen
Sie können die folgenden Aktionen ausführen:

|Aktion|Beschreibung|
|--- | ---|
|MyProfile|Ruft das Benutzerprofil für den aktuellen Benutzer ab|
|UserProfile|Ruft ein bestimmtes Benutzerprofil ab|
|Manager|Ruft das Benutzerprofil des Vorgesetzten des angegebenen Benutzers ab|
|DirectReports|Ruft direkt unterstellte Mitarbeiter ab|
|SearchUser|Ruft die Suchergebnisse für Benutzerprofile ab|
## Herstellen einer Verbindung mit Office 365-Benutzern
Um die Office 365-Benutzer-API zu verwenden, stellen Sie zunächst eine **Verbindung** her und geben dann die Details für diese Eigenschaften an:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Tokenverschlüsselung|Ja|Office 365-Anmeldeinformationen angeben|


>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Office 365-Benutzer-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.


### Mein Profil abrufen 


 Ruft das Benutzerprofil für den aktuellen Benutzer ab. ```GET: /users/me```

Es gibt keine Parameter für diesen Aufruf.
#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|202|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Benutzerprofil abrufen 


 Ruft ein bestimmtes Benutzerprofil ab. ```GET: /users/{userId}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userId|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|202|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Vorgesetzten abrufen 


 Ruft das Benutzerprofil des Vorgesetzten des angegebenen Benutzers ab. ```GET: /users/{userId}/manager```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|202|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Direkt unterstellte Mitarbeiter abrufen 


 Ruft direkt unterstellte Mitarbeiter ab. ```GET: /users/{userId}/directReports```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userId|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|202|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Nach Benutzern suchen 


 Ruft die Suchergebnisse für Benutzerprofile ab. ```GET: /users```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|searchTerm|string|no|query|(Keine)|Suchzeichenfolge (gilt für: Anzeigename, Vorname, Nachname, E-Mail, E-Mail-Spitzname und Benutzerprinzipalname)|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|202|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



## Objektdefinitionen: 

 **Benutzer:** Modellklasse „User“.

Erforderliche Eigenschaften für „User“:

ID

**Alle Eigenschaften:**


| Name | Datentyp |
|---|---|
|DisplayName|string|
|GivenName|string|
|Surname|string|
|Mail|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|Boolescher Wert|
|ID|string|
|UserPrincipalName|string|
|Abteilung|string|
|JobTitle|string|


## Nächste Schritte
Nach Hinzufügen der Office 365-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->