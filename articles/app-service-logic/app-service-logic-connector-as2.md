<properties 
   pageTitle="Verwenden des AS2-Connectors in Microsoft Azure App Service" 
   description="Verwenden des AS2-Connectors" 
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
   ms.date="06/29/2015"
   ms.author="rajram"/>

# Microsoft AS2-Connector
Der Microsoft Azure AS2-Connector kann bei der Business-to-Business-Kommunikation Nachrichten über das AS2-Transportprotokoll (Applicability Statement 2) empfangen und senden. Daten werden sicher und zuverlässig über das Internet übertragen. Die Sicherheit wird mithilfe von digitalen Zertifikaten und Verschlüsselung gewährleistet.

## Trigger und Aktionen
Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie dem Eingang einer AS2-Nachricht von einem Partner. Eine Aktion ist das Ergebnis. Nach dem Empfang einer AS2-Nachricht wird die Nachricht beispielsweise mit AS2 gesendet.

Der AS2-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Der AS2-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Empfangen und decodieren | Codieren und senden

## Anforderungen für den Einstieg
Diese Elemente müssen von Ihnen erstellt werden, bevor sie vom AS2-Connector verwendet werden können:

Voraussetzung | Beschreibung
--- | ---
TPM-API-App | Bevor Sie einen AS2-Connector erstellen, müssen Sie einen [BizTalk-Handelspartnerverwaltungs-Connector][1] erstellen.<br/><br/>**Hinweis** Sie müssen den Namen Ihrer TPM-API-App kennen. 
Azure SQL-Datenbank | Speichert B2B-Elemente, einschließlich Partner, Schemas, Zertifikate und Vereinbarungen. Jede der B2B-API-Apps erfordert eine eigene Azure-SQL-Datenbank. <br/><br/>**Hinweis** Kopieren Sie die Verbindungszeichenfolge in diese Datenbank.<br/><br/>[Erstellen einer Azure SQL-Datenbank](../sql-database-create-configure.md)
Azure-Blobspeichercontainer | Speichert Nachrichteneigenschaften, wenn die AS2-Archivierung aktiviert ist. Wenn Sie die AS2-Nachrichtenarchivierung nicht benötigen, ist kein Speichercontainer erforderlich. <br/><br/>**Hinweis** Wenn Sie die Archivierung aktivieren, kopieren Sie die Verbindungszeichenfolge in diesen Blobspeicher.<br/><br/>[Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md).

## Erstellen des AS2-Connectors

Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus, und suchen Sie nach "AS2-Connector".
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie die folgenden Paketeinstellungen ein:

	Eigenschaft | Beschreibung
--- | --- 
Datenbankverbindungszeichenfolge | Geben Sie die ADO.NET-Verbindungszeichenfolge zur Azure-SQL-Datenbank ein, die Sie erstellt haben. Wenn Sie die Verbindungszeichenfolge kopieren, wird das Kennwort nicht der Verbindungszeichenfolge hinzugefügt. Achten Sie vor dem Einfügen darauf, dass das Kennwort in der Verbindungszeichenfolge eingegeben wird.
Aktivieren der Archivierung für eingehende Nachrichten | Optional. Aktivieren Sie diese Eigenschaft, damit Nachrichteneigenschaften einer eingehenden AS2-Nachricht gespeichert werden, die von einem Partner empfangen wird. 
Verbindungszeichenfolge zum Azure-Blobspeicher | Geben Sie die Verbindungszeichenfolge zum Azure-Blobspeichercontainer ein, den Sie erstellt haben. Wenn die Archivierung aktiviert ist, werden die codierten und decodierten Nachrichten in diesem Speichercontainer gespeichert.
TPM-Instanzname | Geben Sie den Namen der API-App **BizTalk Trading Partner Management** ein, die Sie zuvor erstellt haben. Wenn Sie den AS2-Connector erstellen, führt dieser Connector nur die AS2-Vereinbarungen innerhalb dieser spezifischen TPM-Instanz aus.

5. Klicken Sie auf **Erstellen**.

Handelspartner sind die Entitäten, die an einer B2B-Kommunikation (Business-to-Business) beteiligt sind. Wenn zwei Partner eine Beziehung eingehen, wird dies als "Vereinbarung" bezeichnet. Diese Vereinbarung basiert auf der Kommunikation, die beide Partner wünschen, und ist protokoll- oder transportspezifisch.

Die Schritte zum Erstellen einer Handelspartnervereinbarung sind [hier][2] dokumentiert.

## Verwenden des Connectors als Trigger

1. Beim Erstellen oder Bearbeiten einer Logik-App wählen Sie im rechten Bereich den von Ihnen erstellten AS2-Connector aus: <br/> ![Triggereinstellungen][3]

2. Klicken Sie auf den Pfeil nach rechts →: <br/> ![Triggeroptionen][4]

3. Der AS2-Connector macht einen einzelnen Trigger verfügbar. Wählen Sie *Empfangen und decodieren* aus: <br/> ![Eingaben empfangen und decodieren][5]

4. Dieser Trigger weist keine Eingaben auf. Klicken Sie auf den Pfeil nach rechts →: <br/> ![Konfigurierte Elemente empfangen und decodieren][6]

Als Teil der Ausgabe gibt der Connector die AS2-Nutzlast sowie die AS2-spezifischen Metadaten zurück.

Der Trigger wird ausgelöst, wenn eine AS2-Nutzlast wie ein POST an "https://{Host-URL}/decode" ist. Sie finden die Host-URL in den API-App-Einstellungen. Sie müssen möglicherweise auch die Zugriffsebene der API-App in den Anwendungseinstellungen in "Öffentlich" (authentifiziert oder anonym) ändern.

## Verwenden des Connectors als Aktion
1. Fügen Sie nach dem Trigger (oder wählen Sie "Diese Logik manuell ausführen") den im rechten Bereich erstellten AS2-Connector hinzu: <br/> ![Aktionseinstellungen][7]

2. Klicken Sie auf den Pfeil nach rechts →: <br/> ![Liste der Aktionen][8]

3. Der AS2-Connector unterstützt nur eine Aktion. Wählen Sie *Codieren und senden* aus: <br/> ![Eingaben codieren und senden][9]

4. Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie: <br/> ![Konfigurierte Elemente codieren und senden][10]

Parameter:

Parameter | Typ | Beschreibung
--- | --- | ---
Nutzlast | Objekt| Der Inhalt der Nutzlast, die codiert und zum konfigurierten Endpunkt übermittelt werden soll. Die Nutzlast muss als JSON-Objekt bereitgestellt werden.
AS2-From | string | Die AS2-Identität des Absenders der AS2-Nachricht. Dieser Parameter wird verwendet, um die entsprechende Vereinbarung zum Senden der Nachricht suchen.
AS2-To | string | Die AS2-Identität des Empfängers der AS2-Nachricht. Dieser Parameter wird verwendet, um die entsprechende Vereinbarung zum Senden der Nachricht suchen.
Partner-URL | string | Der Endpunkt des Partners, an den die Nachricht gesendet werden muss.
Archivierung aktivieren | Boolescher Wert | Bestimmt, ob die ausgehende Nachricht archiviert werden soll.

Die Aktion gibt bei erfolgreichem Abschluss einen HTTP 200-Antwortcode zurück.

## Mehr mit Ihrem Connector machen
Mehr zu Logik-Apps erfahren Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=July15_HO4-->