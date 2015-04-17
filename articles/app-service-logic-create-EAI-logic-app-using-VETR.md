<properties 
   pageTitle="Erstellen einer EAI-Logik-App mithilfe von VETR" 
   description="In diesem Thema werden die Überprüfungs-, Codier- und Transformationsfunktionen BizTalk XML Services behandelt." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/23/2015"
   ms.author="rajram"/>


# Erstellen einer EAI-Logik-App mithilfe von VETR

In den meisten Szenarien für Enterprise Application Integration (EAI, Unternehmensanwendungsintegration) werden Daten zwischen einer Quelle und einem Ziel vermittelt. Solche Szenarien weisen häufig einen gemeinsamen Satz von Anforderungen auf:

- Sicherstellen, dass Daten aus anderen Systemen ein ordnungsgemäßes Format haben
- Anwenden von Nachschlagevorgängen auf eingehende Daten, um Entscheidungen zu treffen
- Konvertieren von Daten aus einem Format in ein anderes (z. B. vom Datenformat eines CRM-Systems in ein Datenformat eines ERP-Systems)
- Weiterleiten von Daten an die gewünschten Anwendungen oder Systeme

In diesem Artikel untersuchen wir ein gängiges Integrationsmuster: "unidirektionale Nachrichtenvermittlung" bzw. VETR (Validate, Enrich, Transformation, Route [Überprüfen, Verfeinern, Transformieren, Weiterleiten]). Das VETR-Muster vermittelt Daten zwischen einer Quellentität und einer Zielentität. In der Regel sind die Quelle und das Ziel Datenquellen. 

Nehmen wir eine Website, die Bestellungen annimmt. Benutzer senden über HTTP Bestellungen an das System. Hinter den Kulissen überprüft das System die eingehenden Daten auf Richtigkeit, normalisiert sie und stellt sie zur weiteren Verarbeitung in eine Service Bus-Warteschlange. Das System entnimmt der Warteschlange Bestellungen, die in einem bestimmten Format erwartet werden. Der Datenfluss von A bis Z sieht folglich so aus:

HTTP -> Überprüfen -> Transformieren -> Service Bus

![Basic VETR Flow][1]

Die folgenden BizTalk-API-Apps unterstützen die Erstellung dieses Musters:

*HTTP-Trigger* - Quelle für das Auslösen des Nachrichtenereignisses
*Überprüfung* - Überprüft die Richtigkeit der eingehenden Daten
*Transformation* - Transformiert Daten aus dem Eingangsformat in das vom nachgelagerten System geforderte Format
*Service Bus-Connector* - Zielentität, an die Daten gesendet werden


## Erstellen eines einfachen VETR-Musters
### Die Grundlagen

Klicken Sie im Azure-Verwaltungsportal im linken unteren Bereich des Bildschirms auf die Schaltfläche **+Neu**, und klicken Sie auf "Logik-App". Wählen Sie einen Namen, ein Abonnement, eine Ressourcengruppe und einen Standort, der funktioniert. Ressourcengruppen dienen als Container für Ihre Anwendungen, und alle Ressourcen für Ihre Anwendung befinden sich in der gleichen Ressourcengruppe.

Als Nächstes fügen wir Trigger und Aktionen hinzu.


## Hinzufügen eines HTTP-Triggers

1. Wählen Sie in der Galerie **HTTP-Listener** aus, um einen neuen Listener zu erstellen. Nennen Sie ihn **HTTP1**.
2. Belassen Sie die Einstellung **Antwort automatisch senden?** auf "False" festgelegt. Konfigurieren Sie die Triggeraktion, indem Sie "HTTP-Methode" auf "POST" und die Einstellung "Relative URL" auf "OneWayPipeline" festlegen.

![HTTP Trigger][2]


## Hinzufügen der Überprüfungsaktion

Lassen Sie uns nun Aktionen eingeben, die ausgeführt werden, wenn der Trigger ausgelöst wird, d. h. wenn ein Aufruf am HTTP-Endpunkt empfangen wird.

1. Fügen Sie **BizTalk XML Validator** aus der Galerie hinzu, und wählen Sie die Benennung "(Validate1)", um eine Instanz zu erstellen.
2. Konfigurieren Sie ein XSD-Schema, um die eingehenden XML-Nachrichten zu überprüfen. Wählen Sie die Aktion "Überprüfen" und dann "triggers('httplistener').outputs.Content" als Wert für den Parameter "inputXml" aus.

Die Überprüfungsaktion ist nun die erste Aktion hinter dem HTTP-Listener. Auf ähnliche Weise fügen wir den Rest der Aktionen hinzu.

![BizTalk XML Validator][3]


## Hinzufügen der Transformationsaktion
Nun wollen wir Transformationen zum Normalisieren der eingehenden Daten konfigurieren.

1. Fügen Sie **Transformation** aus der Galerie hinzu. 
2. Um eine Transformation zum Transformieren der eingehenden XML-Nachrichten zu konfigurieren, wählen Sie die Aktion **Transformation** als die Aktion aus, die ausgeführt wird, wenn diese API aufgerufen wird. Wählen Sie `Triggers('httplistener').outputs.Content` als Wert für "inputXml" aus. "Map" ist ein optionaler Parameter, da die eingehenden Daten mit allen konfigurierten Transformationen abgeglichen werden. Nur diejenigen, die mit dem Schema übereinstimmen, werden angewendet.
3. Schließlich erfolgt die Transformation nur nach erfolgreicher Überprüfung. Um diese Bedingung zu konfigurieren, klicken Sie rechts oben das Zahnradsymbol neben "Eine zu erfüllende Bedingung hinzufügen". Legen Sie die Bedingung auf `equals(actions('xmlvalidator').status,'Succeeded')` fest.


![BizTalk Transforms][4]


## Hinzufügen des Service Bus-Connectors
Als Nächstes fügen wir eine Service Bus-Warteschlange als das Ziel hinzu, in das Daten geschrieben werden.

1. Fügen Sie einen **Service Bus-Connector** aus der Galerie hinzu. Legen Sie "Name" auf "Servicebus1", "Verbindungszeichenfolge" auf die Verbindungszeichenfolge für Ihre Service Bus-Instanz und "Entitätsname" auf "Warteschlange" fest. Überspringen Sie "Abonnementname". 
2. Wählen Sie die Aktion **Nachricht senden**, und legen das Feld "Nachricht" für die Aktion auf "actions('transformservice').outputs.OutputXml" fest.

![Service Bus][5]


## Senden einer HTTP-Antwort
Nach Abschluss der Pipelineverarbeitung senden wir eine HTTP-Antwort für "Erfolg" und "Fehler" mit den folgenden Schritten zurück:

1. Fügen Sie einen **HTTP-Listener** aus der Galerie hinzu, und wählen Sie die Aktion **HTTP-Antwort senden**.
2. Legen Sie "Antwortinhalt" auf "Pipelineverarbeitung abgeschlossen", "Antwortstatuscode" auf "200", um "HTTP 200 OK" anzugeben, und die Bedingung auf den Ausdruck `@equals(actions('servicebusconnector').status,'Succeeded')` fest.
	
Wiederholen Sie die oben genannten Schritte, um eine HTTP-Antwort auch bei einem Fehler zu senden. Ändern Sie die Bedingung in `@not(equals(actions('servicebusconnector').status,'Succeeded'))`.


## Abschluss
Jedes Mal, wenn jemand eine Nachricht an den HTTP-Endpunkt sendet, wird die App ausgelöst, und es erfolgen die Aktionen, die wir gerade festgelegt haben. Zum Verwalten solcher Logik-Apps, die Sie erstellen, klicken Sie im Azure-Verwaltungsportal auf **Durchsuchen** und dann auf **Logik-Apps**. Klicken Sie auf Ihre App, um weitere Informationen anzuzeigen.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG


<!--HONumber=49-->