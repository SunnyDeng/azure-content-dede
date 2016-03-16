<properties
	pageTitle="Hinzufügen von Microsoft Translator zu PowerApps Enterprise- oder Logik-Apps | Microsoft Azure"
	description="Übersicht über die Microsoft Translator-API und REST-API-Parameter"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Erste Schritte mit der Microsoft Translator-API
Stellen Sie eine Verbindung mit Microsoft Translator her, um Text zu übersetzen, eine Sprache zu erkennen und mehr. Die Microsoft Translator-API kann in Folgendem verwendet werden:

- PowerApps 
- Logik-Apps 

Mit Microsoft Translator können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die von Microsoft Translator abgerufen werden. 
- Verwenden von Aktionen, um Text zu übersetzen, eine Sprache zu erkennen und mehr. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn beispielsweise eine neue Datei in Dropbox erstellt wird, können Sie den Text in der Datei mit Microsoft Translator in eine andere Sprache übersetzen.
- Hinzufügen der Microsoft Translator-API zu PowerApps Enterprise. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Microsoft Translator umfasst die folgenden Aktionen. Es gibt keine Trigger.

Trigger | Aktionen
--- | ---
Keine | <ul><li>Sprache erkennen</li><li>Text-to-Speech</li><li>Text übersetzen</li><li>Sprachen abrufen</li><li>Sprachen für Sprachausgabe abrufen</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Erstellen der Verbindung mit Microsoft Translator

### Hinzufügen zusätzlicher Konfigurationen in PowerApps
Wenn Sie Microsoft Translator zu PowerApps Enterprise hinzufügen, geben Sie die Werte für **Client-ID** und **Geheimer Clientschlüssel** der Microsoft Translator-Anwendung ein. Wenn Sie keine Übersetzungsanwendung haben, können Sie eine erstellen:

1. Wechseln Sie zur [Azure Data Market-Entwicklerseite][5] und melden Sie sich mit Ihrem Microsoft-Konto an. 

2. Wählen Sie **Anwendung registrieren** aus:

	1. Geben Sie einen Wert für **Client-ID** ein.
	2. Geben Sie den **Namen** Ihrer Anwendung ein.
	3. Geben Sie einen Dummywert für **Umleitungs-URL** ein. Geben Sie z. B. Folgendes ein: *https://contosoredirecturl*.
	4. Geben Sie eine **Beschreibung** ein.
	5. Klicken Sie auf **Erstellen**.  

	![Anwendung registrieren][6]

Nun können Sie die **Client-ID** und den **geheimen Clientschlüssel** kopieren und in Ihre Translator-Konfiguration im Azure-Portal einfügen.


## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Sprache erkennen    
Erkennt die Ausgangssprache des angegebenen Texts. ```GET: /Detect```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Text, dessen Sprache identifiziert wird|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Text-to-Speech    
Konvertiert einen angegebenen Text als Audiodatenstrom im Wave-Format in Sprache. ```GET: /Speak```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu konvertierender Text|
|Sprache|string|Ja|query|(Keine) |Sprachcode zum Generieren der Sprache (Beispiel: „de-de“)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Text übersetzen    
Konvertiert Text mit Microsoft Translator in eine bestimmte Sprache. ```GET: /Translate```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu übersetzender Text|
|languageTo|string|Ja|query| (Keine)|Code der Zielsprache (Beispiel: „fr“)|
|languageFrom|string|no|query|(Keine) |Quellsprache. Wenn diese nicht angegeben wurde, versucht Microsoft Translator, sie automatisch zu erkennen. (Beispiel: „de“)|
|category|string|no|query|allgemein |Übersetzungskategorie (Standard: „Allgemein“)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Sprachen abrufen    
Ruft alle Sprachen ab, die von Microsoft Translator unterstützt werden. ```GET: /TranslatableLanguages```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Sprachen für Sprachausgabe abrufen    
Ruft die verfügbaren Sprachen für die Sprachsynthese ab. ```GET: /SpeakLanguages```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

## Objektdefinitionen

#### Sprache: Sprachmodell für mit Microsoft Translator übersetzbare Sprachen

| Name | Datentyp | Erforderlich|
|---|---|---|
|Code|string|no|
|Name|string|no|


## Nächste Schritte
Nach Hinzufügen der Microsoft Translator-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0302_2016-->

