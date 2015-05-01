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
   ms.date="04/01/2015"
   ms.author="vagarw"/>


#Verwenden des SugarCRM-Connectors in Logik-Apps#

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. Mit dem SugarCRM-Connector können Sie verschiedene Entitäten wie z. B. Konten, Leads usw. erstellen und ändern. Im Folgenden werden die typischen Integrationsszenarien mit Salesforce aufgeführt.

- Kontosynchronisierung zwischen SugarCRM und ERP-Systemen, wie z. B. SAP

- Konten-, Kontakt- und Leads-Synchronisierung zwischen Marketo und SugarCRM

- Datenfluss von der Bestellung bis zur Bezahlung von SugarCRM bis zu ERP-Systemen


Im Rahmen der Connector-Paketeinstellungen kann der Benutzer Entitäten angeben, die der Connector verwalten kann. Aktionen, Eingabe- und Ausgabeparameter werden daraufhin dynamisch aufgefüllt. 

##SugarCRM-Connector-Aktionen##
Im Folgenden werden die verschiedenen im SugarCRM-Connector verfügbaren Aktionen aufgeführt.
 
- Modul erstellen - verwenden Sie diese Aktion, um einen neuen Datensatz für das SugarCRM-Modul zu erstellen, z. B. Konten, Leads, Kontakte.

- Modul aktualisieren - verwenden Sie diese Aktion, um einen vorhandenen Datensatz für das SugarCRM-Modul zu aktualisieren.

- Modul löschen - verwenden Sie diese Aktion, um einen vorhandenen Datensatz für das SugarCRM-Modul zu löschen.

- List Module - verwenden Sie diese Aktion zum Auflisten gefilterter Datensätze. Wenn keine Abfrage angegeben ist, werden alle Datensätze zurückgegeben.

- Modul abrufen - mit dieser Aktion können Sie einen einzelnen Datensatz aus dem angegebenen Modul abrufen.

- Anzahl von Datensätzen abrufen - verwenden Sie diese Aktion, um die Anzahl von Datensätzen im Modul abzurufen, die der Abfrage entsprechen. Wenn keine Abfrage angegeben ist, werden alle Datensätze im Modul zurückgegeben. 

- Auf doppelte Module überprüfen - verwenden Sie diese Aktion für eine Überprüfung auf doppelt vorhandene Datensätze im Modul.

*Hinweis*: Weitere Informationen über die unterstützten Argumente in Abfragen finden Sie in der SugarCRM-REST-API-Dokumentation.
   
##Erstellen einer SugarCRM-Connector-API-App##
1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "QuickBooks".
3.	Konfigurieren Sie den SugarCRM-Connector, indem Sie die Informationen für den Webhosting-Plan und die Ressourcengruppe angeben und einen Namen für die API-App wählen.

4. Konfigurieren Sie den SugarCRM-Connector, und klicken Sie auf "Erstellen". Im Folgenden sind die Paketeinstellungen aufgeführt, die Sie zum Erstellen des Connectors bereitstellen müssen:

	<table>
	  <tr>
	    <td><b>Name</b></td>
	    <td><b>Erforderlich</b></td>
	    <td><b>Beschreibung</b></td>
	  </tr>
	  <tr>
	    <td>Website-URL</td>
	    <td>Ja</td>
	    <td>Geben Sie die URL der SugarCRM-Instanz an.: https://abcde1234.sugarcrm.com</td>
	  </tr>
	  <tr>
	    <td>Client-ID</td>
	    <td>Ja</td>
	    <td>Geben Sie den Verbraucherschlüssel des OAuth 2.0-Schlüssels in SugarCRM an. </td>
	  </tr>
	  <tr>
	    <td>Geheimer Clientschlüssel</td>
	    <td>Ja</td>
	    <td>Geben Sie den geheimen Clientschlüssel des OAuth 2.0-Schlüssels in SugarCRM an. </td>
	  </tr>
	<tr>
	    <td>Benutzername</td>
	    <td>Ja</td>
	    <td>Geben Sie den Benutzernamen des SugarCRM-Benutzers an.</td>
	  </tr>
		<tr>
	    <td>Kennwort</td>
	    <td>Ja</td>
	    <td>Geben Sie das Kennwort des SugarCRM-Benutzers an.</td>
	  </tr>
	  <tr>
	    <td>Modulnamen</td>
	    <td>Ja</td>
	    <td>Geben Sie die SugarCRM-Module an, wie Benutzerkonten, Kontakte, Produkte usw., für die der Vorgang ausgeführt werden soll.<br><br>Beispiel: Konten, Leads, Kontakte</td>
	  </tr>
	</table>

	![][9]				



##Erstellen einer Logik-App##
Lassen Sie uns eine einfache Logik-App erstellen, die einen Kunden in SugarCRM erstellt und die Details zur Rechnungsadresse für denselben Kunden aktualisiert.

1.	Melden Sie sich beim Azure-Portal an, und klicken Sie auf 'Neu -> Web + Mobil -> Logik-App'.

	![][1]

2.	Stellen Sie auf der Seite 'Logik-App erstellen' die erforderlichen Details wie Name, App Service-Plan und -Ort bereit.

	![][2]

3.	Klicken Sie auf 'Trigger und Aktionen'. Der Bildschirm mit dem Logik-App-Editor wird angezeigt. Wählen Sie 'Diese Logik manuell ausführen' aus, sodass diese Logik-App nur manuell aufgerufen werden kann.


5.	Erweitern Sie 'API Apps in this resource group' im Katalog, um alle verfügbaren API-Apps anzuzeigen. Wählen Sie 'SugarCRM' aus dem Katalog aus, damit der 'SugarCRM-Connector' dem Datenfluss hinzugefügt wird.


	![][3]

6.	Wählen Sie die Aktion 'Konto erstellen' aus, und die Eingabeparameter werden angezeigt.

	![][4]

12.	Geben Sie als Namen 'Microsoft-Konto' an, und klicken Sie auf ✓. 

	![][5]

13.	Wählen Sie 'SugarCRM-Connector' aus dem Abschnitt 'Zuletzt verwendet' im Katalog aus. Eine neue SugarCRM-Aktion wird hinzugefügt.

14.	Wählen Sie 'Konto aktualisieren' aus der Liste der Aktionen aus, und die Eingabeparameter der Aktion 'Konto aktualisieren' werden angezeigt.

	![][6]

15.	Klicken Sie neben 'Datensatz-ID' auf '+', um den ID-Wert aus der Ausgabe der Aktion 'Konto erstellen' auszuwählen. 

	![][7]

16.	Geben Sie Werte für die Abrechnungsadresse an, und klicken Sie auf ✓.

	![][8]

17. Klicken Sie im Bildschirm des Logik-App-Editors auf "OK", und klicken Sie dann auf  'Erstellen'. Es dauert ungefähr 30 Sekunden, bis die Erstellung abgeschlossen ist.

18. Sehen Sie sich die neu erstellte Logik-App an, und klicken Sie auf  'Ausführen', um einen Testlauf zu initiieren.

19. Überprüfen Sie, ob ein neues Konto mit dem Namen 'Microsoft-Konto' im SugarCRM-Konto erstellt wurde. Dieses Konto wird auch mit den Abrechnungsadressinformationen aktualisiert.

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




<!--HONumber=52-->