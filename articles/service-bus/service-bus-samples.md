<properties 
   pageTitle="service-bus-samples"
   description="Kategorisiert und beschreibt Service Bus-Beispiele und bietet entsprechende Links."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2015"
   ms.author="sethm" />

# Service Bus-Beispiele:

In den Service Bus-Beispielen werden die Hauptfeatures in Service Bus (Clouddienst) und Service Bus für Windows Server veranschaulicht. Dieses Thema kategorisiert und beschreibt die verfügbaren Beispiele und stellt Links zu jedem der Beispiele zur Verfügung.

>[AZURE.NOTE]Die Service Bus-Beispiele werden nicht zusammen mit dem SDK installiert. Zum Abrufen der Beispiele besuchen Sie die [Azure SDK-Beispielseite](https://code.msdn.microsoft.com/windowsazure).

## Service Bus-Brokermessaging

In den folgenden Beispielen wird das Schreiben von Anwendungen dargestellt, die Service Bus verwenden.

Beachten Sie, dass die Beispiele für Brokermessaging eine Verbindungszeichenfolge für den Zugriff auf den Service Bus-Dienstnamespace erfordern.

### So rufen Sie eine Verbindungszeichenfolge für Azure Service Bus ab

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com) an.

1. Klicken Sie in der linken Spalte auf **Service Bus**.

1. Klicken Sie in der Liste auf den Namen Ihres Dienstnamespaces.

1. Klicken Sie auf **Verbindungsinformationen**. Kopieren Sie im Dialogfeld **Auf Verbindungsinformationen zugreifen** die Verbindungszeichenfolge in die Zwischenablage.

1. Fügen Sie die Verbindungszeichenfolge in die Datei "App.config" für das Beispiel ein.

### So rufen Sie eine Verbindungszeichenfolge für Service Bus für Windows Server ab

1. Führen Sie das folgende PowerShell-Cmdlet aus:

	```
	get-sbClientConfiguration
	```

2. Fügen Sie die Verbindungszeichenfolge in die Datei "App.config" für das Beispiel ein.

### Beispiele für erste Schritte

Diese Beispiele beschreiben grundlegende Messaging- und Relayfunktionen.

|Name des Beispiels|Beschreibung|Mindestversion des SDK|Verfügbarkeit|
|---|---|---|---|
|[Erste Schritte: Messaging mit Warteschlangen](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Zeigt, wie Microsoft Azure Service Bus zum Senden und Empfangen von Nachrichten aus einer Warteschlange verwendet wird.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Erste Schritte: Messaging mit Themen](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Zeigt, wie Microsoft Azure Service Bus zum Senden und Empfangen von Nachrichten aus einer Thema mit mehreren Abonnements verwendet wird.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Erste Schritte mit Event Hubs](http://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097)|Stellt die grundlegenden Möglichkeiten von Event Hubs vor, wie etwa das Erstellen eines Event Hubs, das Senden von Ereignissen an einen Event Hub und das Nutzen von Ereignissen mithilfe des Ereignisprozessors.|2\.4|Microsoft Azure Servicebus|

### Erkunden von Features

Die folgenden Beispiele zeigen verschiedene Features von Service Bus.

|Name des Beispiels|Beschreibung|Mindestversion des SDK|Verfügbarkeit|
|---|---|---|---|
|[HTTP-Tokenanbieter](http://code.msdn.microsoft.com/windowsazure/Service-Bus-HTTP-Token-38f2cfc5)|Zeigt die verschiedenen Arten der Authentifizierung eines HTTP/REST-Clients bei Service Bus.|2\.1|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Service Bus-HTTP-Client](http://code.msdn.microsoft.com/windowsazure/Service-Bus-HTTP-client-fe7da74a)|Zeigt, wie Nachrichten über HTTP/REST an Service Bus gesendet und von Service Bus empfangen werden.|2\.3|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Automatische Service Bus-Weiterleitung](http://code.msdn.microsoft.com/windowsazure/Service-Bus-Autoforwarding-b9df470b)|Zeigt, wie Nachrichten von einer Warteschlange, einem Abonnement oder einer Warteschlange für unzustellbare Nachrichten automatisch an eine andere Warteschlange oder an ein anderes Thema weitergeleitet werden. Außerdem wird gezeigt, wie eine Nachricht über eine Übertragungswarteschlange an eine Warteschlange oder ein Thema gesendet wird.|2\.3|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Beispiel für eine WCF-Kanalsitzung](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Zeigt, wie Microsoft Azure Service Bus mithilfe von WCF-Kanälen (Windows Communication Foundation) verwendet wird. Das Beispiel zeigt, wie WCF-Kanäle zum Senden und Empfangen von Nachrichten über eine Service Bus-Warteschlange verwendet werden. Das Beispiel zeigt die sitzungsbasierte und nicht sitzungsbasierte Kommunikation über Service Bus.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Transaktionen](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Zeigt die Verwendung von Microsoft Azure Service Bus-Messagingfunktionen in einem Transaktionsbereich, um sicherzustellen, dass das Commit für Batches von Messagingvorgängen atomar erfolgt.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Verwaltungsvorgänge mithilfe von REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Zeigt, wie Verwaltungsvorgänge für Service Bus mithilfe von REST ausgeführt werden.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Ressourcenanbieter-REST-APIs](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Zeigt, wie die neuen RDFE-REST-APIs von Service Bus verwendet werden, um die Namespaces und Messagingentitäten zu verwalten.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Beispiel für eine WCF-Dienstsitzung](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Zeigt, wie Microsoft Azure Service Bus mithilfe des WCF-Dienstmodells verwendet wird. Das Beispiel zeigt die Verwendung des WCF-Dienstmodells zum Erzielen einer sitzungsbasierten Kommunikation über eine Service Bus-Warteschlange.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Anforderung/Antwort](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Zeigt, wie Microsoft Azure Service Bus und die Funktion "Anforderung/Antwort" verwendet werden. Das Beispiel zeigt einfache Clients und Server, die über eine Service Bus-Warteschlange kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Warteschlange für unzustellbare Nachrichten](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Zeigt die Verwendung von Microsoft Azure Service Bus und der Messagingfunktion "Warteschlange für unzustellbare Nachrichten". Das Beispiel zeigt einen einfachen Absender und Empfänger, die über eine Service Bus-Warteschlange kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Zurückgestellten Nachrichten](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Zeigt, wie das Feature zur Nachrichtenverzögerung von Microsoft Azure Service Bus verwendet wird. Das Beispiel zeigt einen einfachen Absender und Empfänger, die über eine Service Bus-Warteschlange kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Sitzungsnachrichten](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Zeigt, wie Microsoft Azure Service Bus und die Funktion "Nachrichtensitzung" verwendet werden. Das Beispiel zeigt einfache Absender und Empfänger, die über eine Service Bus-Warteschlange kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Anforderung/Antwort (Thema)](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Zeigt, wie das Muster "Anforderung/Antwort" mithilfe von Microsoft Azure Service Bus-Themen und -Abonnements implementiert wird. Das Beispiel zeigt einfache Clients und Server, die über ein Service Bus-Thema kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Anforderung/Antwort (Warteschlange)](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Zeigt, wie Microsoft Azure Service Bus und die Funktion "Anforderung/Antwort" verwendet werden. Das Beispiel zeigt einfache Clients und Server, die über zwei Service Bus-Warteschlangen kommunizieren.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Duplikaterkennung](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Zeigt, wie die Erkennung doppelt vorhandener Nachrichten in Microsoft Azure Service Bus mit Warteschlangen verwendet wird. Es werden zwei Warteschlangen erstellt: eine Warteschlange mit aktivierter Duplikaterkennung und eine Warteschlange ohne Duplikaterkennung.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Asynchrones Messaging](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Zeigt, wie Microsoft Azure Service Bus zum asynchronen Senden und Empfangen von Nachrichten aus einer Warteschlange verwendet wird. Die Warteschlange stellt entkoppelte, asynchrone Kommunikation zwischen einem Absender und einer beliebigen Anzahl von Empfängern bereit (in diesem Fall einem einzigen Empfänger).|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Erweiterte Filter](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Zeigt, wie erweiterte Filter für Microsoft Azure Service Bus-Veröffentlichung/-Abonnements verwendet werden. Ein Thema und drei Abonnements werden mit verschiedenen Filterdefinitionen erstellt, Nachrichten an das Thema gesendet und alle Nachrichten aus Abonnements empfangen.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Brokermessaging: Vorabruf von Nachrichten](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Zeigt, wie die Vorabruffunktion für Nachrichten von Microsoft Azure Service Bus verwendet wird. Zeigt, wie die Vorabruffunktion für Nachrichten beim Empfang verwendet wird.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|

## Service Bus Relay

Beispiele, die das Service Bus Relay demonstrieren.

### Erste Schritte

|Name des Beispiels|Beschreibung|Mindestversion des SDK|Verfügbarkeit|
|---|---|---|---|
|[Relaymessaging: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Veranschaulicht das Ausführen eines Service Bus-Clients und -Diensts unter Azure. In diesem Beispiel wird Service Bus programmgesteuert konfiguriert. Nur Umgebungs- und Sicherheitsinformationen sind in den Konfigurationsdateien gespeichert.|1\.8|Microsoft Azure Servicebus|
|[Authentifizierung durch Relaymessaging: Gemeinsamer geheimer Schlüssel](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Zeigt, wie ein Ausstellername und ein geheimer Schlüssel des Ausstellers für die Authentifizierung bei Service Bus verwendet werden.|1\.8|Microsoft Azure Servicebus|
|[Authentifizierung durch Relaymessaging: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Zeigt das Verfügbarmachen eines HTTP-Diensts, der keine Clientbenutzerauthentifizierung erfordert.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Zeigt die Verwendung der Bindung **WebHttpRelayBinding** zum Zurückgeben von Binärdaten mithilfe des Webprogrammiermodells.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: NetTcp-Relay](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Zeigt die Verwendung der Bindung **NetTcpRelayBinding**.|1\.8|Microsoft Azure Servicebus|

### Erkunden von Features

Beispiele, die verschiedene Features von Service Bus Relay demonstrieren.

|Name des Beispiels|Beschreibung|Mindestversion des SDK|Verfügbarkeit|
|---|---|---|---|
|[Authentifizierung durch Relaymessaging: Simple Web Token (SWT)](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Zeigt die Verwendung von SWT-Anmeldeinformationen (Simple Web Token) für die Authentifizierung bei Service Bus. Dieses Beispiel ähnelt mit Ausnahme einiger Änderungen dem Echobeispiel. Insbesondere fügt dieses Beispiel den ServiceHost- (Dienst) und ChannelFactory-Anwendungen (Client) ein Verhalten hinzu.|1\.8|Microsoft Azure Servicebus|
|[Relaymessaging: Lastenausgleich](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Zeigt die Verwendung von Microsoft Azure Service Bus zum Routen von Nachrichten an mehrere Empfänger. Das Beispiel zeigt mehrere Instanzen eines einfachen Diensts, der mit einem Client über die Bindung **NetTcpRelayBinding** kommuniziert.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: NetEvent](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Zeigt die Verwendung der Bindung **NetEventRelayBinding** für Microsoft Azure Service Bus.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: WS2007Http-Sitzung](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Veranschaulicht die Verwendung der Bindung **WS2007HttpRelayBinding** mit aktivierten zuverlässigen Sitzungen. Das Beispiel zeigt außerdem, wie Service Bus-Anmeldeinformationen in der Konfigurationsdatei statt programmgesteuert angegeben werden.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Zeigt, wie die Bindung **WS2007HttpRelayBinding** mit Nachrichtensicherheit zum Absichern von End-to-End-Nachrichten verwendet wird, wobei die Authentifizierung von Clients bei Service Bus weiterhin erforderlich ist.|1\.8|Microsoft Azure Servicebus|
|[Relaymessaging: Metadatenaustausch](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Veranschaulicht das Verfügbarmachen eines Metadatenendpunkts, der die Relaybindung verwendet. **MetadataExchange** wird in den folgenden Relaybindungen unterstützt: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** und **WS2007HttpRelayBinding**.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: NetTcp direkt](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Zeigt die Konfiguration der Bindung **NetTcpRelayBinding** zum Unterstützen des **hybriden** Verbindungsmodus, durch den zuerst eine Relayverbindung eingerichtet und anschließend nach Möglichkeit automatisch zu einer direkten Verbindung zwischen einem Client und einem Dienst umgeschaltet wird.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Zeigt die Verwendung der Bindung **NetTcpRelayBinding** mit Nachrichtensicherheit.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: NetOneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Zeigt das Bereitstellen und das Verwenden eines Dienstendpunkts mithilfe der Bindung **NetOnewayRelayBinding**.|1\.8|Microsoft Azure Servicebus|
|[Relaymessagingbindungen: WS2007Http einfach](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Zeigt die Verwendung der Bindung **WS2007HttpRelayBinding**. Zeigt einen einfachen Dienst, der keine Sicherheitsoptionen verwendet und keine Authentifizierung von Clients erfordert.|1\.8|Microsoft Azure Servicebus|

## Service Bus-Referenztools

Die folgenden Beispiele zeigen verschiedene weitere Features des Diensts.

|Name des Beispiels|Beschreibung|Mindestversion des SDK|Verfügbarkeit|
|---|---|---|---|
|[Service Bus-Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Dienstnamespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features (z. B. Import-/Exportfunktionen) und Testmöglichkeiten für Messagingentitäten und Relaydienste zur Verfügung.|1\.8|Microsoft Azure Service Bus; Service Bus für Windows Server|
|[Autorisierung: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Dieses Beispiel zeigt das Erstellen und Verwalten von Dienstidentitäten in Microsoft Azure Active Directory Access Control (auch Access Control Service oder ACS) für die Verwendung mit Service Bus.|N/V|Microsoft Azure Servicebus|

## Nächste Schritte

Weitere Übersichten über Service Bus finden Sie in den folgenden Themen.

- [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
- [Service Bus-Architektur](service-bus-architecture.md)
- [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=Sept15_HO2-->