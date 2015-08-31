<properties
   pageTitle="SugarCRM-Connector"
   description="Verwenden des SugarCRM-Connectors"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Verwenden des SugarCRM-Connectors in Logik-Apps

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Mit dem SugarCRM-Connector können Sie unterschiedliche Entitäten erstellen und ändern, z. B. Konten, Leads, Kontakte usw. Unten sind die typischen Integrationsszenarien aufgeführt, bei denen SugarCRM verwendet wird.

- Kontosynchronisierung zwischen SugarCRM und ERP-Systemen, z. B. SAP

- Konten-, Kontakt- und Leads-Synchronisierung zwischen Marketo und SugarCRM

- Datenfluss von der Bestellung bis zur Bezahlung von SugarCRM bis zu ERP-Systemen


Im Rahmen der Connector-Paketeinstellungen kann der Benutzer Entitäten angeben, die der Connector verwalten kann. Aktionen, Eingabeparameter und Ausgabeparameter werden daraufhin dynamisch aufgefüllt.

## SugarCRM-Connector-Aktionen
Im Folgenden werden die verschiedenen im SugarCRM-Connector verfügbaren Aktionen aufgeführt.

- Modul erstellen – Verwenden Sie diese Aktion, um einen neuen Datensatz für das SugarCRM-Modul zu erstellen, z. B. Konten, Leads, Kontakte.

- Modul aktualisieren – Verwenden Sie diese Aktion, um einen vorhandenen Datensatz für das SugarCRM-Modul zu aktualisieren.

- Modul löschen – Verwenden Sie diese Aktion, um einen vorhandenen Datensatz für das SugarCRM-Modul zu löschen.

- Modul auflisten – Verwenden Sie diese Aktion zum Auflisten gefilterter Datensätze. Wenn keine Abfrage angegeben ist, werden alle Datensätze zurückgegeben.

- Modul abrufen – Mit dieser Aktion können Sie einen einzelnen Datensatz aus dem angegebenen Modul abrufen.

- Anzahl von Datensätzen abrufen – Verwenden Sie diese Aktion, um die Anzahl von Datensätzen im Modul abzurufen, die der Abfrage entsprechen. Wenn keine Abfrage angegeben ist, werden alle Datensätze im Modul zurückgegeben.

- Auf doppelte Module überprüfen – Verwenden Sie diese Aktion für eine Überprüfung auf doppelt vorhandene Datensätze im Modul.

*Hinweis*: Weitere Informationen über die unterstützten Argumente in Abfragen finden Sie in der [SugarCRM-REST-API](https://msdn.microsoft.com/library/dn705870)-Dokumentation.

## Erstellen einer SugarCRM-Connector-API-App
1.	Navigieren Sie zu "portal.azure.com". Öffnen Sie den Azure Marketplace mit der Option "+NEU" oben links im Azure-Portal.
2.	Navigieren Sie zu "Marketplace > Alles", und suchen Sie nach "SugarCRM".
3.	Konfigurieren Sie den SugarCRM-Connector, indem Sie die Informationen für den App Service-Plan und die Ressourcengruppe angeben und einen Namen für die API-App eingeben.
4. Konfigurieren Sie die Paketeinstellungen für den SugarCRM-Connector. Im Folgenden sind die Paketeinstellungen aufgeführt, die Sie zum Erstellen des Connectors bereitstellen müssen:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Website-URL | Ja | Geben Sie die URL der SugarCRM-Instanz ein. Geben Sie z. B. Folgendes ein: https://abcde1234.sugarcrm.com.
Client-ID | Ja | Geben Sie den Verbraucherschlüssel des OAuth 2.0-Schlüssels in SugarCRM ein. 
Geheimer Clientschlüssel | Ja | Geben Sie den geheimen Verbraucherschlüssel von OAuth ein.
Benutzername | Ja | Geben Sie den Benutzernamen des SugarCRM-Benutzers ein.
Kennwort | Ja | Geben Sie das Kennwort des SugarCRM-Benutzers ein.
Modulnamen | Ja | Geben Sie die SugarCRM-Module ein (z. B. Benutzerkonten, Kontakte und Produkte), für die der Vorgang ausgeführt werden soll<br><br>Beispiel: Benutzerkonten, Leads, Kontakte  
  
![][9]



## Erstellen einer Logik-App
Wir erstellen eine einfache Logik-App, die einen Kunden in SugarCRM erstellt und die Details zur Rechnungsadresse für denselben Kunden aktualisiert.

1.	Melden Sie sich beim Azure-Portal an, und klicken Sie auf "Neu -> Web + Mobil -> Logik-App"

	![][1]

2.	Stellen Sie auf der Seite "Logik-App erstellen" die erforderlichen Details wie Name, App Service-Plan und -Ort bereit.

	![][2]

3.	Klicken Sie auf "Trigger und Aktionen". Der Bildschirm mit dem Logik-App-Editor wird angezeigt. Wählen Sie "Diese Logik manuell ausführen", sodass diese Logik-App nur manuell aufgerufen werden kann.


5.	Erweitern Sie "API-Apps in dieser Ressourcengruppe" im Katalog, um alle verfügbaren API-Apps anzuzeigen. Wählen Sie "SugarCRM" aus dem Katalog aus, damit der "SugarCRM-Connector" dem Datenfluss hinzugefügt wird.


	![][3]

6.	Wählen Sie die Aktion "Konto erstellen", und die Eingabeparameter werden angezeigt.

	![][4]

12.	Geben Sie als Namen "Microsoft-Konto" an, und klicken Sie auf ✓.

	![][5]

13.	Wählen Sie "SugarCRM-Connector" aus dem Abschnitt "Zuletzt verwendet" im Katalog aus. Eine neue SugarCRM-Aktion wird hinzugefügt.

14.	Wählen Sie "Konto aktualisieren" (unter den erweiterten Aktionen "...") aus der Liste der Aktionen aus, und die Eingabeparameter der Aktion "Konto aktualisieren" werden angezeigt.

	![][6]

15.	Klicken Sie neben "Datensatz-ID" auf "...", um den ID-Wert aus der Ausgabe der Aktion "Konto erstellen" auszuwählen.

	![][7]

16.	Geben Sie Werte für die Abrechnungsadresse an, und klicken Sie auf ✓.

	![][8]

17. Klicken Sie im Bildschirm des Logik-App-Editors auf "OK", und klicken Sie dann auf "Erstellen". Es dauert ungefähr 30 Sekunden, bis die Erstellung abgeschlossen ist.

18. Sehen Sie sich die neu erstellte Logik-App an, und klicken Sie auf "Jetzt ausführen", um eine Ausführung zu initiieren.

19. Überprüfen Sie, ob ein neues Konto mit dem Namen "Microsoft-Konto" im SugarCRM-Konto erstellt wurde. Dieses Konto wird auch mit den Abrechnungsadressinformationen aktualisiert.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=August15_HO8-->