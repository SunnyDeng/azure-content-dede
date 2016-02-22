<properties
   pageTitle="Tutorial: Verarbeiten von EDIFACT-Rechnungen mit Azure BizTalk Services | Microsoft Azure BizTalk Services"
   description="Erstellen und Konfigurieren des Box-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/02/2015"
   ms.author="Deonhe"/>

# Tutorial: Verarbeiten von EDIFACT-Rechnungen mit Azure BizTalk Services
Sie können X12- und EDIFACT-Vereinbarungen im BizTalk Services-Portal konfigurieren und bereitstellen. In diesem Tutorial untersuchen wir das Erstellen einer EDIFACT-Vereinbarung für das Austauschen von Rechnungen zwischen Handelspartnern. Dieses Tutorial basiert auf einer umfassenden Geschäftslösung zwischen den beiden Handelspartnern Northwind und Contoso, die EDIFACT-Nachrichten austauschen.

## Auf diesem Tutorial basierendes Beispiel
Dieses Tutorial basiert auf dem Beispiel **Process EDIFACT Invoices Using Azure BizTalk Services**, das aus dem [MSDN-Codekatalog](http://go.microsoft.com/fwlink/?LinkId=401005) herunterladen werden kann. Sie können dieses Tutorial anhand des Beispiels durchlaufen, um zu verstehen, wie das Beispiel erstellt wurde. Sie können dieses Tutorial aber auch nutzen, um Ihre eigene Lösung von Grund auf zu erstellen. Dieses Tutorial ist auf den zweiten Ansatz ausgelegt, damit Sie nachvollziehen können, wie diese Lösung entwickelt wurde. Darüber hinaus ist dieses Tutorial so weit wie möglich in Einklang mit dem Beispiel, sodass dieselben Namen für Artefakte (z. B. Schemas und Transformationen) wie im Beispiel verwendet werden.

>[AZURE.NOTE] Da diese Lösung das Senden einer Nachricht von einer EAI-Brücke an eine EDI-Brücke umfasst, wird das Beispiel [BizTalk Services Bridge chaining sample – EAI to EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) wiederverwendet.

## Was ist die Aufgabe der Lösung?

In dieser Lösung empfängt Northwind EDIFACT-Rechnungen von Contoso. Diese Rechnungen liegen nicht in einem EDI-Standardformat vor. Daher muss eine Rechnung vor dem Senden an Northwind in ein EDIFACT-Rechnungsdokument (auch als INVOIC bezeichnet) umgewandelt werden. Bei Empfang muss Northwind die EDIFACT-Rechnung verarbeiten und eine Steuerungsnachricht (auch als CONTRL bezeichnet) an Contoso zurücksenden.

![][1]

Zur Verwirklichung dieses Geschäftsszenarios verwendet Contoso die von Microsoft Azure BizTalk Services gebotenen Funktionen.

*   Contoso verwendet EAI-Brücken, um die ursprüngliche Rechnung in EDIFACT INVOIC umzuwandeln.

*   Die EAI-Brücke sendet die Nachricht dann an eine EDI-Sendebrücke, die als Teil einer Vereinbarung bereitgestellt wird, die im BizTalk Services-Portal konfiguriert wurde.

*   Die EDI-Sendebrücke verarbeitet die EDIFACT INVOIC und leitet sie an Northwind.

*   Nach Empfang der Rechnung sendet Northwind eine CONTRL-Nachricht an die EDI-Empfangsbrücke zurück, die als Teil der Vereinbarung bereitgestellt wurde.

> [AZURE.NOTE] Optional veranschaulicht diese Lösung auch das Verwenden der Batchverarbeitung zum Senden von Rechnungen in Batches anstatt einzelner getrennter Rechnungen.

Zum Vervollständigen dieses Szenarios verwenden wir Service Bus-Warteschlangen zum Senden der Rechnung von Contoso an Northwind bzw. zum Empfang einer Bestätigung von Northwind. Diese Warteschlangen können mithilfe einer Clientanwendung erstellt werden, die als Download verfügbar und im Beispielpaket enthalten ist, das als Teil dieses Tutorials zur Verfügung steht.

## Voraussetzungen

*   Sie benötigen einen Service Bus-Namespace. Anweisungen zum Erstellen eines Namespace finden Sie unter [Vorgehensweise: Erstellen oder Ändern eines Service Bus-Dienstnamespace](https://msdn.microsoft.com/library/hh690931.aspx). Angenommen, Sie haben bereits einen Service Bus-Namespace mit dem Namen **edifactbts** bereitgestellt.

*   Sie benötigen ein BizTalk Services-Abonnement. Anweisungen finden Sie unter [Erstellen eines BizTalk Service im klassischen Azure-Portal](http://go.microsoft.com/fwlink/?LinkID=302280). Lassen Sie uns für dieses Tutorial annehmen, dass Sie ein BizTalk Services-Abonnement mit dem Namen **contosowabs** haben.

*   Registrieren Sie Ihr BizTalk Services-Abonnement im BizTalk Services-Portal. Eine Anleitung finden Sie unter [Registrieren einer BizTalk-Service-Bereitstellung im BizTalk Services-Portal](https://msdn.microsoft.com/library/hh689837.aspx).

*   Visual Studio muss installiert sein.

*   Das BizTalk Services SDK muss installiert sein. Sie können das SDK von [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057) herunterladen.

## Schritt 1: Erstellen der Service Bus-Warteschlangen  
Diese Lösung verwendet zum Austauschen von Nachrichten zwischen Handelspartnern Service Bus-Warteschlangen. Contoso und Northwind senden Nachrichten an die Warteschlangen, die von den EAI- und/oder EDI-Brücken genutzt werden. Für diese Lösung benötigen Sie drei Service Bus-Warteschlangen:

*   **northwindreceive**: Northwind empfängt die Rechnung von Contoso über diese Warteschlange.

*   **contosoreceive**: Contoso empfängt die Bestätigung von Northwind über diese Warteschlange.

*   **suspended**: Alle angehaltenen Nachrichten werden an diese Warteschlange weitergeleitet. Nachrichten werden angehalten, wenn während der Verarbeitung ein Fehler auftritt.

Sie können diese Service Bus-Warteschlangen mithilfe einer im Beispielpaket enthaltenen Clientanwendung erstellen.

1.  Öffnen Sie am Speicherort, von dem Sie das Beispiel heruntergeladen haben, das Tutorial **Sending Invoices Using BizTalk Services EDI Bridges.sln**.

2.  Drücken Sie **F5**, um die Anwendung **Tutorialclient** zu erstellen und zu starten.

3.  Geben Sie auf dem Bildschirm den Service Bus-ACS-Namespace, den Ausstellernamen und den Ausstellerschlüssel ein.

    ![][2]  
4.  In einem Meldungsfeld werden Sie informiert, dass drei Warteschlangen in Ihrem Service Bus-Namespace erstellt werden. Klicken Sie auf **OK**.

5.  Führen Sie den Tutorialclient weiter aus. Öffnen Sie das Portal, klicken Sie auf **Service Bus** > **_Ihr Service Bus-Namespace_** > **Warteschlangen**, und überprüfen Sie, ob die drei Warteschlangen erstellt wurden.

## Schritt 2: Erstellen und Bereitstellen der Handelspartnervereinbarung
Erstellen Sie eine Handelspartnervereinbarung zwischen Contoso und Northwind. In einer Handelspartnervereinbarung legen Geschäftspartner z. B. fest, welches Nachrichtenschema und welches Protokoll verwendet werden soll usw. Eine Handelspartnervereinbarung sieht zwei EDI-Brücken vor, eine zum Senden von Nachrichten an Handelspartner (als **EDI-Sendebrücke** bezeichnet) und eine zum Empfangen von Nachrichten von Handelspartnern (als **EDI-Empfangsbrücke** bezeichnet).

Im Zusammenhang mit dieser Lösung entspricht die EDI-Sendebrücke der Sendeseite der Vereinbarung und wird zum Senden der EDIFACT-Rechnung von Contoso an Northwind verwendet. Analog dazu entspricht die EDI-Empfangsbrücke der Empfangsseite der Vereinbarung und wird zum Empfangen von Bestätigungen von Northwind verwendet.

### Erstellen der Handelspartner

Erstellen Sie zunächst Handelspartner für Contoso und Northwind.

1.  Klicken Sie im BizTalk Services-Portal auf der Registerkarte **Partner** auf **Hinzufügen**.

2.  Geben Sie auf der Seite **Neuer Partner** „Contoso“ als Namen des Partners ein, und klicken Sie dann auf **Speichern**.

3.  Wiederholen Sie diesen Schritt, um den zweiten Partner, **Northwind**, zu erstellen.

### Erstellen der Vereinbarung
Handelspartnervereinbarungen werden zwischen Geschäftsprofilen von Handelspartnern erstellt. Die Lösung verwendet die standardmäßigen Partnerprofile, die beim Erstellen der Partner automatisch erstellt wurden.

1.  Klicken Sie im BizTalk Services-Portal auf **Vereinbarungen** > **Hinzufügen**.

2.  Geben Sie auf der Seite **Allgemeine Einstellungen** der Vereinbarung die Werte wie in der Abbildung unten dargestellt ein, und klicken Sie dann auf **Weiter**.

    ![][3]

    Nachdem Sie auf **Weiter** geklickt haben, werden die beiden Registerkarten **Empfangseinstellungen** und **Sendeeinstellungen** aktiviert.

3.  Erstellen Sie die Sendevereinbarung zwischen Contoso und Northwind. Diese Vereinbarung legt fest, wie Contoso die EDIFACT-Rechnung an Northwind sendet.

    1.  Klicken Sie auf **Sendeeinstellungen**.

    2.  Übernehmen Sie die Standardwerte der Registerkarten **Eingehende URL**, **Transformation** und **Batchverarbeitung**.

    3.  Laden Sie auf der Registerkarte **Protokoll** im Abschnitt **Schemas** das Schema **EFACT\_D93A\_INVOIC.xsd** hoch. Dieses Schema steht im Beispielpaket zur Verfügung.

        ![][4]  
    4.  Geben Sie auf der Registerkarte **Transport** die Details für die Service Bus-Warteschlangen an. Für den sendeseitigen Vertrag verwenden wir die Warteschlange **northwindreceive**, um die EDIFACT-Rechnung an Northwind zu senden, und die Warteschlange **suspended**, an die alle Nachrichten weitergeleitet werden, bei deren Verarbeitung ein Fehler auftritt und die deswegen angehalten werden. Sie haben diese Warteschlangen in [Schritt 1: Erstellen der Azure Service Bus-Warteschlangen](#BKMK_Queue) erstellt.

        ![][5]

        Wählen Sie unter **Transporteinstellungen > Transporttyp** und **Nachrichtenunterbrechungseinstellungen > Transporttyp** „Azure Service Bus“ aus, und geben Sie die in der Abbildung dargestellten Werte ein.

4.  Erstellen Sie die Empfangsvereinbarung zwischen Contoso und Northwind. Diese Vereinbarung legt fest, wie Contoso die Bestätigung von Northwind empfängt.

    1.  Klicken Sie auf **Empfangseinstellungen**.

    2.  Übernehmen Sie auf den Registerkarten **Transport** und **Transformation** die Standardwerte.

    3.  Laden Sie auf der Registerkarte **Protokoll** im Abschnitt **Schemas** das Schema **EFACT\_4.1\_CONTRL.xsd** hoch. Dieses Schema steht im Beispielpaket zur Verfügung.

    4.  Erstellen Sie auf der Registerkarte **Weiterleitung** einen Filter, um sicherzustellen, dass nur Bestätigungen von Northwind an Contoso weitergeleitet werden. Klicken Sie unter **Weiterleitungseinstellungen** auf **Hinzufügen**, um den Weiterleitungsfilter zu erstellen.

        ![][6] 1. Geben Sie, wie in der Abbildung dargestellt, Werte für **Regelname**, **Weiterleitungsregel** und **Weiterleitungsziel** an.

        2.  Klicken Sie auf **Speichern**.

    5.  Geben Sie dann auf der Registerkarte **Weiterleitung** an, wohin angehaltene Bestätigungen (Bestätigungen, bei deren Verarbeitung ein Fehler auftritt) weitergeleitet werden sollen. Legen Sie den Transporttyp auf „Azure Service Bus“, den Typ des Weiterleitungsziels auf **Warteschlange**, den Authentifizierungstyp auf **Shared Access Signature** (SAS) fest. Geben Sie die SAS-Verbindungszeichenfolge für den Service Bus-Namespace an, und geben Sie dann den Warteschlangennamen als **suspended** an.

5.  Klicken Sie schließlich auf **Bereitstellen**, um die Vereinbarung bereitzustellen. Notieren Sie sich die Endpunkte, an denen Sende- und Empfangsvereinbarungen bereitgestellt werden.

    *   Notieren Sie auf der Registerkarte **Sendeeinstellungen** unter **Eingehende URL** den Endpunkt. Zum Senden einer Nachricht von Contoso an Northwind mithilfe der EDI-Sendebrücke müssen Sie eine Nachricht an diesen Endpunkt senden.

    *   Notieren Sie auf der Registerkarte **Empfangseinstellungen** unter **Transport** den Endpunkt. Zum Senden einer Nachricht von Northwind an Contoso mithilfe der EDI-Empfangsbrücke müssen Sie eine Nachricht an diesen Endpunkt senden.

## Schritt 3: Erstellen und Bereitstellen des BizTalk Services-Projekts

Im vorhergehenden Schritt haben Sie die EDI-Sende- und Empfangsvereinbarungen zum Verarbeiten von EDIFACT-Rechnungen und Bestätigungen bereitgestellt. Diese Vereinbarungen können nur Nachrichten verarbeiten, die dem EDIFACT-Standardnachrichtenschema entsprechen. Gemäß dem Szenario für diese Lösung sendet Contoso jedoch eine Rechnung mit einem intern proprietären Schema an Northwind. Bevor die Nachricht an die EDI-Sendebridge gesendet wird, muss sie daher aus dem intern verwendeten Schema in das EDIFACT-Standardschema für Rechnungen umgewandelt werden. Dies erfolgt im BizTalk Services-EAI-Projekt.

Das BizTalk Services-Projekt **InvoiceProcessingBridge**, das die Umformung der Nachricht erledigt, ist ebenfalls im heruntergeladenen Beispiel enthalten. Das Projekt umfasst folgende Artefakte:

*   **INHOUSEINVOICE. XSD**: Schema der internen Rechnung, die an Northwind gesendet wird.

*   **EFACT\_D93A\_INVOIC. XSD**: Schema der EDIFACT-Standardrechnung.

*   **EFACT\_4.1\_CONTRL. XSD**: Schema der EDIFACT-Bestätigung, die Northwind an Contoso sendet.

*   **INHOUSEINVOICE\_to\_D93AINVOIC. TRFM**: Die Transformation, mit deren Hilfe das interne Rechnungsschema dem EDIFACT-Standardrechnungsschema zugeordnet wird.

### Erstellen des BizTalk Services-Projekts
1.  Erweitern Sie in der Visual Studio-Projektmappe das Projekt „InvoiceProcessingBridge“, und öffnen Sie dann die Datei **MessageFlowItinerary.bcs**.

2.  Klicken Sie auf eine beliebige Stelle des Zeichenbereichs, und legen Sie die **BizTalk Service-URL** im Eigenschaftenfeld fest, um Ihren BizTalk Services-Abonnementnamen anzugeben. Beispiel: `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  Ziehen Sie aus der Toolbox eine **unidirektionale XML-Brücke** auf den Zeichenbereich. Legen Sie die Eigenschaften **Entitätsname** und **Relative Adresse** der Brücke auf **ProcessInvoiceBridge** fest. Doppelklicken Sie auf **ProcessInvoiceBridge**, um die Konfigurationsoberfläche der Brücke zu öffnen.

4.  Klicken Sie im Feld **Nachrichtentypen** auf das Pluszeichen (**+**), um das Schema der eingehenden Nachricht anzugeben. Da die eingehende Rechnung für die EAI-Brücke immer die interne Rechnung ist, legen Sie diese auf **INHOUSEINVOICE** fest.

    ![][8]  
5.  Klicken Sie auf das Shape **XML-Transformation**, und klicken Sie im Eigenschaftenfeld für die Eigenschaft **Zuordnung** auf die Auslassungsschaltfläche (**...**). Wählen Sie im Dialogfeld **Zuordnungsauswahl** die Transformationsdatei **INHOUSEINVOICE\_to\_D93AINVOIC** aus, und klicken Sie dann auf **OK**.

    ![][9]  
6.  Wechseln Sie wieder zu **MessageFlowItinerary.bcs**, und ziehen Sie aus der Toolbox einen **bidirektionalen externen Dienstendpunkt** auf die rechte Seite von **ProcessInvoiceBridge**. Legen Sie seine Eigenschaft **Entitätsname** auf **EDIBridge** fest.

7.  Erweitern Sie im Projektmappen-Explorer **MessageFlowItinerary.bcs**, und doppelklicken Sie auf die Datei **EDIBridge.config**. Ersetzen Sie den Inhalt von **EDIBridge.config** durch Folgendes.

    > [AZURE.NOTE] Warum muss ich die CONFIG-Datei bearbeiten? Der externe Dienstendpunkt, den wir dem Zeichenbereich im Brücken-Designer hinzugefügt haben, stellt die EDI-Brücken dar, die wir zuvor bereitgestellt hatten. EDI-Brücken sind bidirektionale Brücken mit einer Sende- und einer Empfangsseite. Die EAI-Brücke, die wir dem Brücken-Designer hinzugefügt haben, ist jedoch eine unidirektionale Brücke. Zum Verarbeiten der verschiedenen Nachrichtenaustauschmuster der beiden Brücken verwenden wir ein benutzerdefiniertes Brückenverhalten, indem wir ihre Konfiguration in die CONFIG-Datei aufnehmen. Darüber hinaus übernimmt das benutzerdefinierte Verhalten auch die Authentifizierung beim Endpunkt der EDI-Sendebrücke. Dieses benutzerdefinierte Verhalten steht als eigenes Beispiel unter [BizTalk Services Bridge chaining sample – EAI to EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) (BizTalk Services Bridge-Verkettungsbeispiel – EAI zu EDI) zur Verfügung. Das Beispiel wird in dieser Projektmappe wiederverwendet.
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Aktualisieren der Datei „EDIBridge.config“ mit Konfigurationsdetails

    *   Geben Sie unter _<behaviors>_ den ACS-Namespace und den dem BizTalk Services-Abonnement zugeordneten Schlüssel an.

    *   Geben Sie unter _<client>_ den Endpunkt an, an dem der EDI-Sendevertrag bereitgestellt ist.

    Speichern Sie die Änderungen, und schließen Sie die Konfigurationsdatei.

9.  Klicken Sie in der Toolbox auf den **Connector**, und verbinden Sie die Komponenten **ProcessInvoiceBridge** und **EDIBridge** miteinander. Wählen Sie den Connector aus, und legen Sie im Feld „Eigenschaften“ den Parameter **Filterbedingung** auf** Alle zuordnen** fest. Dadurch wird sichergestellt, dass alle von der EAI-Brücke gesendeten Nachrichten an die EDI-Brücke weitergeleitet werden.

    ![][10]  
10.  Speichern Sie Änderungen an der Lösung.  

### Bereitstellen des Projekts

1.  Laden Sie auf dem Computer, auf dem das BizTalk Services-Projekt erstellt wurde, das SSL-Zertifikat für Ihr BizTalk Services-Abonnement herunter, und installieren Sie es. Klicken Sie im unter BizTalk Services auf **Dashboard** und dann auf **SSL-Zertifikat herunterladen**. Doppelklicken Sie auf das Zertifikat, und folgen Sie der Aufforderung zum Abschließen der Installation. Achten Sie darauf, dass Sie das Zertifikat unter dem Zertifikatspeicher **Vertrauenswürdige Stammzertifizierungsstellen** installieren.

2.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **InvoiceProcessingBridge**, und klicken Sie dann im Kontextmenü auf **Bereitstellen**.

3.  Geben Sie die Werte wie in der Abbildung dargestellt ein, und klicken Sie dann auf **Bereitstellen**. Sie können die ACS-Anmeldeinformationen für BizTalk Services abrufen, indem Sie im BizTalk Services-Dashboard auf **Verbindungsinformationen** klicken.

    ![][11]

    Kopieren Sie im Ausgabebereich den Endpunkt, an dem die EAI-Brücke bereitgestellt ist, beispielsweise `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Sie benötigen diese Endpunkt-URL zu einem späteren Zeitpunkt.

## Schritt 4: Testen der Lösung


In diesem Thema sehen wir uns an, wie die Lösung mithilfe der Anwendung **Tutorialclient** getestet wird, die im Beispiel zur Verfügung steht.

1.  Drücken Sie in Visual Studio F5, um den **Tutorialclient** zu starten.

2.  Die Werte auf dem Bildschirm müssen von dem Schritt, in dem die Service Bus-Warteschlangen erstellt wurden, bereits aufgefüllt sein. Klicken Sie auf **Weiter**.

3.  Geben Sie im nächsten Fenster ACS-Anmeldeinformationen für das BizTalk Services-Abonnement und die Endpunkte an, an denen die EAI- und EDI-Brücken (Empfangsbrücken) bereitgestellt sind.

    Sie hatten den Endpunkt der EAI-Bridge im vorhergehenden Schritt kopiert. Für den Endpunkt der EDI-Empfangsbrücke navigieren Sie im BizTalk Services-Portal zu „Vereinbarung > Empfangseinstellungen > Transport > Endpunkt“.

    ![][12]  
4.  Klicken Sie im nächsten Fenster unter Contoso auf die Schaltfläche **Interne Rechnung senden**. Öffnen Sie im Dialogfeld „Datei öffnen“ die Datei „INHOUSEINVOICE.txt“. Untersuchen Sie den Inhalt der Datei, und klicken Sie dann auf **OK**, um die Rechnung zu senden.

    ![][13]  
5.  Nach wenigen Sekunden wird die Rechnung bei Northwind empfangen. Klicken Sie auf den Link **Nachricht anzeigen**, um die von Northwind empfangene Rechnung anzuzeigen. Beachten Sie, dass die von Northwind empfangene Rechnung dem EDIFACT-Standardschema entspricht, während die von Contoso gesendete Rechnung einem internen Schema entsprach.

    ![][14]  
6.  Wählen Sie die Rechnung aus, und klicken Sie dann auf **Bestätigung senden**. Beachten Sie, dass in dem jetzt angezeigten Dialogfeld die Austausch-ID der empfangenen Rechnung und der zu sendenden Bestellung gleich ist. Klicken Sie im Dialogfeld **Bestätigung senden** auf „OK“.

    ![][15]  
7.  Nach wenigen Sekunden wird die Bestätigung erfolgreich bei Contoso empfangen.

    ![][16]

## Schritt 5 (optional): Batchversand von EDIFACT-Rechnungen 
BizTalk Services-EDI-Brücken unterstützen auch den Batchversand ausgehender Nachrichten. Dieses Feature ist für Empfangspartner nützlich, die Nachrichten lieber in einem Batch (der einem bestimmten Kriterium entspricht) als einzeln empfangen.

Der wichtigste Aspekt beim Arbeiten mit Batches besteht in der eigentlichen Freigabe des Batches, die auch als Freigabekriterium bezeichnet wird. Das Freigabekriterium können daraus abgeleitet werden, wie der Empfangspartner Nachrichten empfangen möchte. Wenn die Batchverarbeitung aktiviert ist, sendet die EDI-Brücke die ausgehende Nachricht nicht an den Empfangspartner, bis das Freigabekriterium erfüllt ist. Beispielsweise wird bei einem Batchkriterium, das auf der Nachrichtengröße basiert, ein Batch nur dann zugestellt, wenn „n“ Nachrichten zusammengefasst werden. Ein Batchkriterium kann auch zeitbasiert sein, so dass ein Batch täglich zu einer bestimmten Zeit gesendet wird. In dieser Lösung verwenden wir das auf der Nachrichtengröße basierende Kriterium.

1.  Klicken Sie im BizTalk Services-Portal auf die Vereinbarung, die Sie zuvor erstellt haben. Klicken Sie auf „Sendeeinstellungen > Batchverarbeitung > Batch hinzufügen“.

2.  Geben Sie als Batchnamen **InvoiceBatch** ein, geben Sie eine Beschreibung an, und klicken Sie dann auf **Weiter**.

3.  Geben Sie ein Batchkriterium an, das definiert, welche Nachrichten als Batch gesendet werden müssen. In dieser Lösung werden alle Nachrichten im Batch gesendet. Wählen Sie also die Option „Erweiterte Definitionen verwenden“ aus, und geben Sie **1 = 1** ein. Dies ist eine Bedingung, die immer wahr ist. Daher werden alle Nachrichten als Batch gesendet. Klicken Sie auf **Weiter**.

    ![][17]  
4.  Geben Sie ein Batchfreigabekriterium ein. Wählen Sie im Dropdownfeld **MessageCountBased** aus, und geben Sie als **Anzahl** den Wert **3** an. Dies bedeutet, dass ein aus drei Nachrichten bestehender Batch an Northwind gesendet wird. Klicken Sie auf **Weiter**.

    ![][18]  
5.  Überprüfen Sie die Zusammenfassung, und klicken Sie dann auf **Speichern**. Klicken Sie auf **Bereitstellen**, um die Vereinbarung erneut bereitzustellen.

6.  Wechseln Sie wieder zum **Tutorialclient** zurück, klicken Sie auf **Interne Rechnung senden**, und befolgen Sie die Anweisungen zum Senden der Rechnung. Sie werden feststellen, dass bei Northwind keine Rechnung empfangen wird, da die Batchgröße nicht erreicht wird. Wiederholen Sie diesen Schritt noch zweimal, damit drei Rechnungsnachrichten an Northwind gesendet werden. Dadurch wird das Batchfreigabekriterium der 3 Nachrichten erfüllt, und Sie sollten jetzt Rechnungen bei Northwind sehen.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

<!---HONumber=AcomDC_0211_2016-->