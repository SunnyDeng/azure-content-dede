<properties
   pageTitle="Verwenden des Informix-Connectors in Microsoft Azure App Service | Microsoft Azure"
   description="Sie erfahren, wie Sie den Informix-Connector mit Auslösern und Aktionen für Logik-Apps verwenden."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/03/2015"
   ms.author="plarsen"/>

# Informix-Connector

Der Microsoft Connector für Informix ist eine API-App zum Verbinden von Anwendungen per Azure App Service mit Ressourcen, die in einer IBM Informix-Datenbank gespeichert sind. Der Connector enthält einen Microsoft-Client zum Herstellen einer Verbindung mit Informix-Remoteservercomputern über eine TCP/IP-Netzwerkverbindung, z. B. Azure-Hybridverbindungen mit lokalen Informix-Servern per Azure Service Bus Relay. Der Connector unterstützt die folgenden Datenbankvorgänge:

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


## Erstellen des Informix-Connectors
Sie können einen Connector in einer Logik-App oder über den Azure Marketplace definieren. Dies wird im folgenden Beispiel veranschaulicht:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Geben Sie auf dem Blatt **Alles** im Feld **Alles durchsuchen** den Text **informix** ein, und drücken Sie die EINGABETASTE.
3. Wählen Sie im Ergebnisbereich „Alles durchsuchen“ die Option **Informix-Connector** aus.
4. Wählen Sie auf dem Blatt mit der Beschreibung des Informix-Connectors die Option **Erstellen**.
5. Geben Sie auf dem Blatt mit dem Informix-Connectorpaket den Namen (z. B. „InformixConnectorNewOrders“), App Service-Plan und andere Eigenschaften ein.
6. Wählen Sie **Paketeinstellungen**, und geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
ConnectionString | Ja | Informix-Clientverbindungszeichenfolge (z. B. „Network Address=servername;Network Port=9089;User ID=username;Password=password;Initial Catalog=nwind;Default Schema=informix“).
Tabellen | Ja | Durch Kommas getrennte Liste von Tabellen-, Sicht- und Aliasnamen, die für OData-Vorgänge und zum Generieren der Swagger-Dokumentation mit Beispielen erforderlich sind (z. B. „NEWORDERS“).
Prozeduren | Ja | Durch Kommas getrennte Liste mit Prozedur- und Funktionsnamen (z. B. „SPORDERID“).
OnPremise | Nein | Lokale Bereitstellung mit Azure Service Bus Relay
ServiceBusConnectionString | Nein | Azure Service Bus Relay-Verbindungszeichenfolge
PollToCheckData | Nein | SELECT COUNT-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL“).
PollToReadData | Nein | SELECT-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE“).
PollToAlterData | Nein | UPDATE- oder DELETE-Anweisung zum Verwenden mit einem Logik-App-Auslöser (z. B. „UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;“).

7. Wählen Sie **OK** und anschließend **Erstellen**.
8. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus: ![][1]


## Logik-App mit Informix-Connectoraktion zum Hinzufügen von Daten ##
Sie können eine Logik-App-Aktion definieren, um Daten einer Informix-Tabelle mit einem OData-Vorgang vom Typ „API einfügen“ oder „Post an Entität“ hinzuzufügen. Beispielsweise können Sie einen neuen Datensatz mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und den Identitätswert oder die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP]Mit der Informix-Verbindung „*Post an EntitySet*“ wird der Wert der Identitätsspalte zurückgegeben, und mit „*API einfügen*“ werden betroffene Zeilen zurückgegeben.

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „NewOrdersInformix“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen** aus.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **Wiederholung**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **Informix-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **In NEWORDER einfügen**.
7. Erweitern Sie die Parameterliste, um die folgenden Werte einzugeben:  

	Name | Wert
--- | --- 
CUSTID | 10042
SHIPID | 10000
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**.
9. Die Einstellungen sollten wie folgt aussehen: ![][3]  
10. Wählen Sie in der Liste **Alle Testläufe** unter **Vorgänge** den zuerst aufgeführten Eintrag aus (letzte Ausführung). 
11. Wählen Sie auf dem Blatt **Logik-App-Ausführung** das **ACTION**-Element **informixconnectorneworders** aus.
12. Wählen Sie auf dem Blatt **Logik-App-Aktion** die Option **INPUTS LINK**. Der Informix-Connector verwendet die Eingaben, um eine parametrisierte INSERT-­Anweisung zu verarbeiten.
13. Wählen Sie auf dem Blatt **Logik-App-Aktion** die Option **OUTPUTS LINK**. Die Eingaben sollten wie folgt aussehen: ![][4]

#### Wichtige Informationen

- Der Connector schneidet Informix-Tabellennamen ab, wenn die Namen von Logik-App-Aktionen gebildet werden. Ein Vorgang mit dem Namen **Insert into NEWORDERS** wird beispielsweise abgeschnitten und lautet **Insert into NEWORDER**.
- Nach dem Speichern der **Auslöser und Aktionen** der Logik-App verarbeitet die Logik-App den Vorgang. Es kann eine Verzögerung von einigen Sekunden geben (z. B. 3 bis 5 Sekunden), bevor die Logik-App den Vorgang verarbeitet. Optional können Sie auf **Jetzt ausführen** klicken, um den Vorgang zu verarbeiten.
- Der Informix-Connector definiert EntitySet-Member mit Attributen, einschließlich der Angabe, ob der Member einer Informix-Spalte mit einer Standardspalte oder generierten Spalten (z. B. Identität) entspricht. Die Logik-App zeigt ein rotes Sternchen neben dem Namen der EntitySet-Member-ID an, um Informix-Spalten anzugeben, die Werte erfordern. Sie sollten keinen Wert für den ORDID-Member eingeben, der der Informix-Identitätsspalte entspricht. Sie können Werte für andere optionale Member eingeben (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), die den Informix-Spalten mit Standardwerten entsprechen. 
- Der Informix-Connector gibt für die Logik-App die Antwort von „Post an EntitySet“ zurück, die die Werte für die Identitätsspalten enthält (per Ableitung aus DRDA SQLDARD (SQL Data Area Reply Data) in der vorbereiteten SQL INSERT-Anweisung). Der Informix-Server gibt die eingefügten Werte für Spalten mit Standardwerten nicht zurück.  


## Logik-App mit Informix-Connectoraktion zum Hinzufügen von Massendaten ##
Sie können eine Logik-App-Aktion definieren, um Daten einer Informix-Tabelle mit einer API-Masseneinfügung hinzuzufügen. Beispielsweise können Sie zwei neue Datensätze mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung mit einem Array mit Zeilenwerten für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „NewOrdersBulkInformix“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen**.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **Wiederholung**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **Informix-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **Masseneinfügung in NEW**.
7. Geben Sie den Wert **rows** als Array ein. Fügen Sie beispielsweise Folgendes ein:  

	```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
	```
        
8. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][6]

9. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
10. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
11. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **INPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: [][7]
12. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][8]

#### Wichtige Informationen

- Der Connector schneidet Informix-Tabellennamen ab, wenn die Namen von Logik-App-Aktionen gebildet werden. Ein Vorgang mit dem Namen **Bulk Insert into NEWORDERS** wird beispielsweise abgeschnitten und lautet **Bulk Insert into NEW**.
- In einer Informix-Datenbank wird unter Umständen die Groß-/Kleinschreibung von Tabellen- und Spaltennamen berücksichtigt. Beispielsweise kann es sein, dass die Arrayspaltennamen des Vorgangs für die Masseneinfügung nicht in Großbuchstaben („CUSTID“), sondern in Kleinbuchstaben („custid“) angegeben werden müssen.
- Die Informix-Datenbank generiert Werte, indem Identitätsspalten (z. B. ORDID), nullbare Spalten (z. B. SHIPDATE) und Spalten mit Standardwerten (z. B. ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY) weggelassen werden.
- Durch das Angeben von „today“ und „tomorrow“ generiert der Informix-Connector die Funktionen „CURRENT DATE“ und „CURRENT DATE + 1 DAY“ (z. B. REQDATE). 


## Logik-App mit Informix-Connector-Auslöser zum Lesen, Ändern oder Löschen von Daten ##
Sie können einen Logik-App-Auslöser definieren, um Daten aus einer Informix-Tabelle abzufragen und zu lesen, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen und die Datensätze an die Logik-App zurückgeben. Die Informix-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


Sie können auch einen Logik-App-Auslöser definieren, um Daten in einer Informix-Tabelle abzufragen, zu lesen und zu ändern, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen, die Zeilenwerte aktualisieren und die (vor der Aktualisierung) ausgewählten Datensätze an die Logik-App zurückgeben. Die Informix-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


Sie können auch einen Logik-App-Auslöser definieren, um Daten in einer Informix-Tabelle abzufragen, zu lesen und daraus zu entfernen, indem Sie einen zusammengesetzten API-Vorgang zum Abfragen von Daten verwenden. Beispielsweise können Sie einen oder mehrere neue Datensätze für Kundenbestellungen lesen, die Zeilen löschen und die (vor dem Löschen) ausgewählten Datensätze an die Logik-App zurückgeben. Die Informix-Verbindungspaket- bzw. -App-Einstellungen sollten wie folgt aussehen:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

In diesem Beispiel werden die Daten mit der Logik-App in der Informix-Tabelle abgefragt, gelesen, aktualisiert und dann erneut gelesen.

1. Wählen Sie im Azure-Startmenü **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie den Namen (z. B. „ShipOrdersInformix“), den App Service-Plan und andere Eigenschaften ein, und wählen Sie **Erstellen**.
3. Wählen Sie im Azure-Startmenü die gerade erstellte Logik-App aus, und wählen Sie dann **Einstellungen** und **Auslöser und Aktionen**.
4. Wählen Sie auf dem Blatt „Auslöser und Aktionen“ in den Vorlagen der Logik-App die Option **Von Grund auf neu erstellen**.
5. Wählen Sie im Bereich mit den API-Apps die Option **Informix-Connector**, legen Sie eine Häufigkeit und ein Intervall fest, und aktivieren Sie die Option durch das **Häkchen**.
6. Wählen Sie im Bereich mit den API-Apps die Option **Informix-Connector**, erweitern Sie die Liste mit den Vorgängen, und wählen Sie **Aus NEWORDERS auswählen**.
7. Wählen Sie das **Häkchen**, um die Aktionseinstellungen zu speichern, und dann die Option **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][10]
8. Klicken Sie, um das Blatt **Auslöser und Aktionen** zu schließen, und klicken Sie dann, um das Blatt **Einstellungen** zu schließen.
9. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
10. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
11. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][11]


## Logik-App mit Informix-Connectoraktion zum Entfernen von Daten ##
Sie können eine Logik-App-Aktion definieren, um Daten aus einer Informix-Tabelle mit einem OData-Vorgang vom Typ „API löschen“ oder „Post an Entität“ zu entfernen. Beispielsweise können Sie einen neuen Datensatz mit einer Kundenbestellung einfügen, indem Sie eine SQL INSERT-Anweisung für eine Tabelle verarbeiten, die mit einer Identitätsspalte definiert wurde, und den Identitätswert oder die betroffenen Zeilen an die Logik-App zurückgeben (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Erstellen einer Logik-App per Informix-Connector zum Entfernen von Daten ##
Sie können im Azure Marketplace eine neue Logik-App erstellen und den Informix-Connector dann als Aktion zum Entfernen von Kundenbestellungen verwenden. Beispielsweise können Sie den bedingten Informix-Connector-Löschvorgang nutzen, um eine DELETE-SQL-Anweisung zu verarbeiten (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. Klicken Sie im Hub-Menü des Azure-**Start**menüs auf **+** (Pluszeichen), **Web und mobil** und dann auf **Logik-App**. 
2. Geben Sie auf dem Blatt **Logik-App erstellen** einen **Namen** ein, z. B. **RemoveOrdersInformix**.
3. Wählen oder definieren Sie Werte für die anderen Einstellungen (z. B. Dienstplan, Ressourcengruppe).
4. Die Einstellungen sollten wie folgt aussehen: Klicken Sie auf **Erstellen**: ![][12]
5. Klicken Sie auf dem Blatt **Einstellungen** auf **Auslöser und Aktionen**.
6. Klicken Sie auf dem Blatt **Auslöser und Aktionen** in der Liste mit den **Logik-App-Vorlagen** auf **Von Grund auf neu erstellen**.
7. Klicken Sie auf dem Blatt **Auslöser und Aktionen** im Bereich **API-Apps** in der Ressourcengruppe auf **Wiederholung**.
8. Klicken Sie auf der Entwurfsoberfläche der Logik-App auf die Option **Wiederholung**, und legen Sie eine **Häufigkeit** und ein **Intervall** fest, z. B. **Tage** und **1**. Klicken Sie dann auf das **Häkchen**, um die Einstellungen für die Wiederholungsoption zu speichern.
9. Klicken Sie auf dem Blatt **Auslöser und Aktionen** im Bereich **API-Apps** in der Ressourcengruppe auf **Informix-Connector**.
10. Klicken Sie auf der Entwurfsoberfläche der Logik-App auf das Aktionselement **Informix-Connector**, klicken Sie auf die Auslassungszeichen (**...**), um die Liste mit den Vorgängen zu erweitern, und klicken Sie dann auf **Bedingtes Löschen aus N**.
11. Geben Sie im Aktionselement „Informix-Connector“ **ordid ge 10000** für einen **Ausdruck ein, der eine Teilmenge der Einträge identifiziert**.
12. Klicken Sie auf das **Häkchen**, um die Aktionseinstellungen zu speichern, und klicken Sie dann auf **Speichern**. Die Einstellungen sollten wie folgt aussehen: ![][13]
13. Klicken Sie, um das Blatt **Auslöser und Aktionen** zu schließen, und klicken Sie dann, um das Blatt **Einstellungen** zu schließen.
14. Klicken Sie in der Liste **Alle Testläufe** unter **Vorgänge** auf den zuerst aufgeführten Eintrag (letzte Ausführung).
15. Klicken Sie auf dem Blatt **Logik-App-Ausführung** auf das Element **ACTION**.
16. Klicken Sie auf dem Blatt **Logik-App-Aktion** auf die Option **OUTPUTS LINK**. Die Ausgaben sollten wie folgt aussehen: ![][14]

**Hinweis:** Der Logik-App-Designer schneidet Tabellennamen ab. Beispielsweise wird ein Vorgang mit dem Namen **Conditional delete from NEWORDERS** abgeschnitten und lautet **Conditional delete from N**.


> [AZURE.TIP]Verwenden Sie die folgenden SQL-Anweisungen, um die Beispieltabelle und die gespeicherten Prozeduren zu erstellen.

Sie können die NEWORDERS-Beispieltabelle mit den folgenden Informix-SQL-DDL-Anweisungen erstellen:
 
    create table neworders (  
 		ordid serial(10000) unique ,  
 		custid int not null ,  
 		empid int not null default 10000 ,  
 		orddate date not null default today ,  
 		reqdate date default today ,  
 		shipdate date ,  
 		shipid int not null default 10000 ,  
 		freight decimal (9,2) not null default 0.00 ,  
 		shipname char (40) not null ,  
 		shipaddr char (60) not null ,  
 		shipcity char (20) not null ,  
 		shipreg char (15) not null ,  
 		shipzip char (10) not null ,  
 		shipctry char (15) not null default ''USA'' 
 		)


Sie können die gespeicherte Prozedur SPORDERID als Beispiel erstellen, indem Sie die folgende Informix-DDL-Anweisung verwenden:
 
    create procedure sporderid ( ord_id int)  
 		returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
 		define xordid, xcustid, xempid, xshipid int;  
 		define xorddate, xreqdate, xshipdate date;  
 		define xfreight decimal (9,2);  
 		define xshipname char (40);  
 		define xshipaddr char (60);  
 		define xshipcity char (20);  
 		define xshipreg, xshipctry char (15);  
 		define xshipzip char (10);  
 		select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
 			into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
 			from neworders where ordid = ord_id;  
 		return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## Hybridkonfiguration (optional)

> [AZURE.NOTE]Dieser Schritt ist nur bei lokaler Verwendung von DB2-Connector hinter Ihrer Firewall erforderlich.

App Service verwendet den Hybrid-Konfigurations-Manager, um sicher eine Verbindung auf Ihr lokales System herzustellen. Wenn für den Connector ein lokaler IBM DB2-Server für Windows verwendet wird, ist der Hybrid Connection Manager erforderlich.

Informationen finden Sie unter [Hybrid Connection Manager konfigurieren](app-service-logic-hybrid-connection-manager.md).


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png

<!---HONumber=AcomDC_1210_2015-->