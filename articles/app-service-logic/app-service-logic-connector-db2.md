<properties
   pageTitle="Verwenden des DB2-Connectors in Microsoft Azure App Service | Microsoft Azure"
   description="Erfahren Sie, wie Sie den DB2-Connector mit Auslösern und Aktionen für Logik-Apps verwenden."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="plarsen"/>

# DB2-Connector
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Microsoft Connector für DB2 ist eine API-App zum Verbinden von Anwendungen über Azure App Service mit Ressourcen, die in einer IBM DB2-Datenbank gespeichert sind. Der Connector enthält einen Microsoft-Client zum Herstellen einer Verbindung mit DB2-Remoteservercomputern über eine TCP/IP-Netzwerkverbindung, z. B. Azure-Hybridverbindungen mit lokalen DB2-Servern per Azure Service Bus Relay. Der Connector unterstützt die folgenden Datenbankvorgänge:

- Lesen von Zeilen mit SELECT
- Durchführen einer Leseabfrage für Zeilen mit SELECT COUNT gefolgt von SELECT
- Hinzufügen einer Zeile oder mehrerer Zeilen (Massenhinzufügung) per INSERT
- Ändern einer Zeile oder mehrerer Zeilen (Massenänderung) per UPDATE
- Entfernen einer Zeile oder mehrerer Zeilen (Massenentfernung) per DELETE
- Lesen zum Ändern von Zeilen per SELECT CURSOR gefolgt von UPDATE WHERE CURRENT OF CURSOR
- Lesen zum Entfernen von Zeilen per SELECT CURSOR gefolgt von UPDATE WHERE CURRENT OF CURSOR
- Ausführen der Prozedur mit Eingabe- und Ausgabeparametern, Rückgabewert, Resultset per CALL
- Benutzerdefinierte Befehle und zusammengesetzte Vorgänge per SELECT, INSERT, UPDATE, DELETE

## Trigger und Aktionen
Der Connector unterstützt die folgenden Auslöser und Aktionen für Logik-Apps:

Trigger | Aktionen
--- | ---
<ul><li>Umfragedaten</li></ul> | <ul><li>Masseneinfügung</li><li>Einfügen</li><li>Massenaktualisierung</li><li>Aktualisieren</li><li>Aufrufen</li><li>Massenlöschung</li><li>Löschen</li><li>Auswählen</li><li>Bedingte Aktualisierung</li><li>Post an EntitySet</li><li>Bedingte Löschung</li><li>Einzelne Entität auswählen</li><li>Löschen</li><li>Upsert an EntitySet</li><li>Benutzerdefinierte Befehle</li><li>Zusammengesetzte Vorgänge</li></ul>


## Erstellen des DB2-Connectors
Sie können einen Connector in einer Logik-App oder über den Azure Marketplace definieren. Dies wird im folgenden Beispiel veranschaulicht:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Geben Sie auf dem Blatt **Alles** im Feld **Alles durchsuchen** den Text **db2** ein, und drücken Sie die EINGABETASTE.
3. Wählen Sie im Ergebnisbereich „Alles durchsuchen“ die Option **DB2-Connector**.
4. Wählen Sie auf dem Blatt mit der Beschreibung des DB2-Connectors die Option **Erstellen**.
5. Geben Sie auf dem Blatt mit dem DB2-Connectorpaket den Namen (z. B. „Db2ConnectorNewOrders“), App Service-Plan und andere Eigenschaften ein.
6. Wählen Sie **Paketeinstellungen**, und geben Sie die folgenden Paketeinstellungen ein:  

	Name | Erforderlich | Beschreibung
--- | --- | ---
ConnectionString | Ja | DB2-Clientverbindungszeichenfolge (z. B. „Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND“).
Tabellen | Ja | Durch Kommas getrennte Liste von Tabellen-, Sicht- und Aliasnamen, die für OData-Vorgänge und zum Generieren der Swagger-Dokumentation mit Beispielen erforderlich sind (z. B. „*NEWORDERS*“).
Prozeduren | Ja | Durch Kommas getrennte Liste mit Prozedur- und Funktionsnamen (z. B. „SPORDERID“).
OnPremise | Nein | Lokale Bereitstellung mit Azure Service Bus Relay
ServiceBusConnectionString | Nein | Azure Service Bus Relay-Verbindungszeichenfolge
PollToCheckData | Nein | SELECT COUNT-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL“).
PollToReadData | Nein | SELECT-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE“).
PollToAlterData | Nein | UPDATE- oder DELETE-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;“).

7. Wählen Sie **OK** und anschließend **Erstellen**.
8. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus: ![][1]


## Logik-App mit DB2-Connectoraktion zum Hinzufügen von Daten ##
Sie können eine Logik-App-Aktion definieren, um Daten einer DB2-Tabelle mit einem OData-Vorgang vom Typ „API einfügen“ oder „Post an Entität“ hinzuzufügen. Beispielsweise können Sie einen neuen Datensatz mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und den Identitätswert oder die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP] Mit der DB2-Verbindung „ *Post an EntitySet* “ wird der Wert der Identitätsspalte zurückgegeben, und mit „ *API einfügen* “ werden betroffene Zeilen zurückgegeben.

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „NewOrdersDb2“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen**.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **Wiederholung**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **DB2-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **In NEWORDER einfügen**.
7. Erweitern Sie die Parameterliste, um die folgenden Werte einzugeben:  

	Name | Wert
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**.
9. Die Einstellungen sollten wie folgt aussehen: ![][3]

10. Wählen Sie in der Liste **Alle Testläufe** unter **Vorgänge** den zuerst aufgeführten Eintrag aus (letzte Ausführung).
11. Wählen Sie auf dem Blatt **Logik-App-Ausführung** das **ACTION**-Element **db2connectorneworders** aus.
12. Wählen Sie auf dem Blatt **Logik-App-Aktion** die Option **INPUTS LINK**. DB2-Connector verwendet die Eingaben, um eine parametrisierte INSERT-­Anweisung zu verarbeiten.
13. Wählen Sie auf dem Blatt **Logik-App-Aktion** die Option **OUTPUTS LINK**. Die Eingaben sollten wie folgt aussehen: ![][4]

#### Wichtige Informationen

- Der Connector schneidet DB2-Tabellennamen ab, wenn die Namen von Logik-App-Aktionen gebildet werden. Ein Vorgang mit dem Namen **Insert into NEWORDERS** wird beispielsweise abgeschnitten und lautet **Insert into NEWORDER**.
- Nach dem Speichern der **Auslöser und Aktionen** der Logik-App verarbeitet die Logik-App den Vorgang. Es kann eine Verzögerung von einigen Sekunden geben (z. B. 3 bis 5 Sekunden), bevor die Logik-App den Vorgang verarbeitet. Optional können Sie auf **Jetzt ausführen** klicken, um den Vorgang zu verarbeiten.
- Der DB2-Connector definiert EntitySet-Member mit Attributen, einschließlich der Angabe, ob der Member einer DB2-Spalte mit einer Standardspalte oder generierten Spalten (z. B. Identität) entspricht. Die Logik-App zeigt ein rotes Sternchen neben dem Namen der EntitySet-Member-ID an, um DB2-Spalten anzugeben, die Werte erfordern. Sie sollten keinen Wert für den ORDID-Member eingeben, der der DB2-Identitätsspalte entspricht. Sie können Werte für andere optionale Member eingeben (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), die den DB2-Spalten mit Standardwerten entsprechen. 
- Der DB2-Connector gibt für die Logik-App die Antwort von „Post an EntitySet“ zurück, die die Werte für die Identitätsspalten enthält (per Ableitung aus DRDA SQLDARD (SQL Data Area Reply Data) in der vorbereiteten SQL INSERT-Anweisung). Der DB2-Server gibt die eingefügten Werte für Spalten mit Standardwerten nicht zurück.  


## Logik-App mit DB2-Connectoraktion zum Hinzufügen von Massendaten ##
Sie können eine Logik-App-Aktion definieren, um Daten einer DB2-Tabelle mit einer API-Masseneinfügung hinzuzufügen. Beispielsweise können Sie zwei neue Datensätze mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung mit einem Array mit Zeilenwerten für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „NewOrdersBulkDb2“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen**.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **Wiederholung**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **DB2-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **Masseneinfügung in NEW**.
7. Geben Sie den Wert **rows** als Array ein. Fügen Sie beispielsweise Folgendes ein:

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][6]

9. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
10. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
11. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **INPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: [][7]
12. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][8]

#### Wichtige Informationen

- Der Connector schneidet DB2-Tabellennamen ab, wenn die Namen von Logik-App-Aktionen gebildet werden. Ein Vorgang mit dem Namen **Bulk Insert into NEWORDERS** wird beispielsweise abgeschnitten und lautet **Bulk Insert into NEW**.
- Die DB2-Datenbank generiert Werte, indem Identitätsspalten (z. B. ORDID), nullbare Spalten (z. B. SHIPDATE) und Spalten mit Standardwerten (z. B. ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY) weggelassen werden.
- Durch das Angeben von „today“ und „tomorrow“ generiert der DB2-Connector die Funktionen „CURRENT DATE“ und „CURRENT DATE + 1 DAY“ (z. B. REQDATE). 


## Logik-App mit DB2-Connector-Auslöser zum Lesen, Ändern oder Löschen von Daten ##
Sie können einen Logik-App-Auslöser definieren, um Daten aus einer DB2-Tabelle abzufragen und zu lesen, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen und die Datensätze an die Logik-App zurückgeben. Die DB2-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


Sie können auch einen Logik-App-Auslöser definieren, um Daten in einer DB2-Tabelle abzufragen, zu lesen und zu ändern, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen, die Zeilenwerte aktualisieren und die (vor der Aktualisierung) ausgewählten Datensätze an die Logik-App zurückgeben. Die DB2-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


Außerdem können Sie einen Logik-App-Auslöser definieren, um Daten aus einer DB2-Tabelle abzufragen, zu lesen und zu entfernen, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen, die Zeilen löschen und die (vor dem Löschen) ausgewählten Datensätze an die Logik-App zurückgeben. Die DB2-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

In diesem Beispiel werden die Daten mit der Logik-App in der DB2-Tabelle abgefragt, gelesen, aktualisiert und dann erneut gelesen.

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „ShipOrdersDb2“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen**.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **DB2-Connector**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **DB2-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **Aus NEWORDERS auswählen**.
7. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][10]  
8. Klicken Sie, um das Blatt **Auslöser und Aktionen** zu schließen, und klicken Sie dann, um das Blatt **Einstellungen** zu schließen.
9. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
10. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
11. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][11]


## Logik-App mit DB2-Connectoraktion zum Entfernen von Daten ##
Sie können eine Logik-App-Aktion definieren, um Daten aus einer DB2-Tabelle mit einem OData-Vorgang vom Typ „API löschen“ oder „Post an Entität“ zu entfernen. Beispielsweise können Sie einen neuen Datensatz mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und den Identitätswert oder die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Erstellen einer Logik-App per DB2-Connector zum Entfernen von Daten ##
Sie können im Azure Marketplace eine neue Logik-App erstellen und den DB2-Connector dann als Aktion zum Entfernen von Kundenbestellungen verwenden. Beispielsweise können Sie den bedingten DB2-Connector-Löschvorgang nutzen, um eine DELETE-SQL-Anweisung zu verarbeiten (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. Klicken Sie im Hub-Menü des Azure-**Start**menüs auf **+** (Pluszeichen), **Web und mobil** und dann auf **Logik-App**. 
2. Geben Sie auf dem Blatt **Logik-App erstellen** einen **Namen** ein, z. B. **RemoveOrdersDb2**.
3. Wählen oder definieren Sie Werte für die anderen Einstellungen (z. B. Dienstplan, Ressourcengruppe).
4. Die Einstellungen sollten wie folgt aussehen: Klicken Sie auf **Erstellen**: ![][12]  
5. Klicken Sie auf dem Blatt **Einstellungen** auf **Auslöser und Aktionen**.
6. Klicken Sie auf dem Blatt **Auslöser und Aktionen** in der Liste mit den **Logik-App-Vorlagen** auf **Von Grund auf neu erstellen**.
7. Klicken Sie auf dem Blatt **Auslöser und Aktionen** im Bereich **API-Apps** in der Ressourcengruppe auf **Wiederholung**.
8. Klicken Sie auf der Entwurfsoberfläche der Logik-App auf die Option **Wiederholung**, und legen Sie eine **Häufigkeit** und ein **Intervall** fest, z. B. **Tage** und **1**. Klicken Sie dann auf das **Häkchen**, um die Einstellungen für die Wiederholungsoption zu speichern.
9. Klicken Sie auf dem Blatt **Auslöser und Aktionen** im Bereich **API-Apps** in der Ressourcengruppe auf **DB2-Connector**.
10. Klicken Sie auf der Entwurfsoberfläche der Logik-App auf das Aktionselement **DB2-Connector**, klicken Sie auf die Auslassungszeichen (**...**), um die Liste mit den Vorgängen zu erweitern, und klicken Sie dann auf **Bedingtes Löschen aus N**.
11. Geben Sie im Aktionselement „DB2-Connector“ **ORDID ge 10000** für einen **Ausdruck ein, der eine Teilmenge der Einträge identifiziert**.
12. Klicken Sie auf das **Häkchen**, um die Aktionseinstellungen zu speichern, und klicken Sie dann auf **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][13]  
13. Klicken Sie, um das Blatt **Auslöser und Aktionen** zu schließen, und klicken Sie dann, um das Blatt **Einstellungen** zu schließen.
14. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
15. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
16. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][14]

**Hinweis:** Der Logik-App-Designer schneidet Tabellennamen ab. Beispielsweise wird ein Vorgang mit dem Namen **Conditional delete from NEWORDERS** abgeschnitten und lautet **Conditional delete from N**.


> [AZURE.TIP] Verwenden Sie die folgenden SQL-Anweisungen, um die Beispieltabelle und die gespeicherten Prozeduren zu erstellen.

Sie können die NEWORDERS-Beispieltabelle mit den folgenden DB2-SQL-DDL-Anweisungen erstellen:
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Sie können die gespeicherte Prozedur SPOERID als Beispiel erstellen, indem Sie die folgende DB2-DDL-Anweisung verwenden:
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## Hybridkonfiguration (optional)

> [AZURE.NOTE] Dieser Schritt ist nur bei lokaler Verwendung von DB2-Connector hinter Ihrer Firewall erforderlich.

App Service verwendet den Hybrid-Konfigurations-Manager, um sicher eine Verbindung auf Ihr lokales System herzustellen. Wenn für den Connector ein lokaler IBM DB2-Server für Windows verwendet wird, ist der Hybrid Connection Manager erforderlich.

Informationen finden Sie unter [Hybrid Connection Manager konfigurieren](app-service-logic-hybrid-connection-manager.md).


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!-----HONumber=AcomDC_0224_2016-->