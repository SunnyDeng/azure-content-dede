<properties 
	pageTitle="Stream Analytics: Austauschen von Anmeldeinformationen für Ein- und Ausgaben | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Anmeldeinformationen für Stream Analytics-Ein- und Ausgaben aktualisieren können."
	keywords="Anmeldeinformationen"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/04/2015" 
	ms.author="jeffstok"/>

#Austauschen von Anmeldeinformationen

##Zusammenfassung
Derzeit unterstützt Azure Stream Analytics das Austauschen der Anmeldeinformationen für die Eingabe/Ausgabe nicht, während ein Auftrag ausgeführt wird.

Während Azure Stream Analytics zwar das Fortsetzen eines Auftrags von der letzten Ausgabe an unterstützt, wollten wir den gesamten Prozess freigeben, um die Verzögerung zwischen dem Anhalten und Starten des Auftrags und dem Austauschen der Anmeldeinformationen zu verkürzen.

##Teil 1 – Vorbereiten des neuen Anmeldeinformationssatzes:
Dieser Teil gilt für die folgenden Eingaben/Ausgaben:

* Blob-Speicher
* Event Hubs
* SQL-Datenbank
* Table Storage

Für andere Ein-/Ausgaben fahren Sie mit Teil 2 fort.

###Blob-Speicher/Tabellenspeicher
1.  Wechseln Sie zur Storage-Erweiterung des Azure-Verwaltungsportals: ![Grafik1][graphic1]
2.  Suchen Sie nach dem für den Auftrag verwendeten Speicher, und wechseln Sie zu diesem: ![Grafik2][graphic2]
3.  Klicken Sie auf den Befehl "Zugriffsschlüssel verwalten": ![Grafik3][graphic3]
4.  Wählen Sie entweder den primären oder den sekundären Zugriffsschlüssel abhängig davon aus, **welcher für den Auftrag nicht verwendet wird**.
5.  Klicken Sie auf "Neu generieren": ![Grafik4][graphic4]
6.  Kopieren Sie den neu generierten Schlüssel: ![Grafik5][graphic5]
7.  Fahren Sie mit Teil 2 fort.

###Event Hubs
1.  Wechseln Sie zur Service Bus-Erweiterung des Azure-Verwaltungsportals: ![Grafik6][graphic6]
2.  Suchen Sie den vom Auftrag verwendeten Service Bus-Namespace, und wechseln Sie zu diesem: ![Grafik7][graphic7]
3.  Wenn für den Auftrag eine SAS-Richtlinie für den Service Bus-Namespace verwendet wird, wechseln Sie zu Schritt 6.  
4.  Wechseln Sie zur Registerkarte "Event Hubs": ![Grafik8][graphic8]
5.  Suchen Sie nach dem für den Auftrag verwendeten Event Hub, und wechseln Sie zu diesem: ![Grafik9][graphic9]
6.  Wechseln Sie zur Registerkarte "Konfigurieren": ![Grafik10][graphic10]
7.  Suchen Sie im Dropdownlistenfeld "Richtlinienname" nach der SAS-Richtlinie für den Auftrag: ![Grafik11][graphic11]
8.  Wählen Sie entweder den primären oder den sekundären Schlüssel abhängig davon aus, **welcher für den Auftrag nicht verwendet wird**.  
9.  Klicken Sie auf "Neu generieren": ![Grafik12][graphic12]
10. Kopieren Sie den neu generierten Schlüssel: ![Grafik13][graphic13]
11. Fahren Sie mit Teil 2 fort.  

###SQL-Datenbank

>[AZURE.NOTE]Hinweis: Sie müssen eine Verbindung mit dem SQL-Datenbankdienst herstellen. Wir werden die Vorgehensweise anhand der Verwaltungsfunktionen des Azure-Verwaltungsportals erläutern, Sie können jedoch auch clientseitige Tools wie z. B. SQL Server Management Studio verwenden.

1.  Wechseln Sie zur SQL-Datenbank-Erweiterung des Azure-Verwaltungsportals: ![Grafik14][graphic14]
2.  Suchen Sie die für den Auftrag verwendete SQL-Datenbank, und **klicken Sie in derselben Zeile auf den Server**: ![Grafik15][graphic15]
3.  Klicken Sie auf den Befehl "Verwalten": ![Grafik16][graphic16]
4.  Geben Sie den Datenbank-Master ein: ![Grafik17][graphic17]
5.  Geben Sie Ihren Benutzernamen und das Kennwort ein, und klicken Sie auf "Anmelden": ![Grafik18][graphic18]
6.  Klicken Sie auf "Neue Abfrage": ![Grafik19][graphic19]
7.  Geben Sie die folgende Abfrage ein, und ersetzen Sie <login_name> mit Ihrem Benutzernamen und <enterStrongPasswordHere> mit dem neuen Kennwort: `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Klicken Sie auf "Ausführen": ![Grafik20][graphic20]
9.  Kehren Sie zu Schritt 2 zurück, und klicken Sie dieses Mal auf die Datenbank: ![Grafik21][graphic21]
10. Klicken Sie auf den Befehl "Verwalten": ![Grafik22][graphic22]
11. Geben Sie Ihren Benutzernamen und das Kennwort ein, und klicken Sie auf "Anmelden": ![Grafik23][graphic23]
12. Klicken Sie auf "Neue Abfrage": ![Grafik24][graphic24]
13. Geben Sie die folgende Abfrage ein, und ersetzen Sie <user_name> mit einem Namen, mit dem diese Anmeldung im Kontext der Datenbank identifiziert werden soll (Sie können z. B. den gleichen Wert wie für <login_name> eingeben). Ersetzen Sie anschließend <login_name> mit Ihrem neuen Benutzernamen: `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Klicken Sie auf "Ausführen": ![Grafik25][graphic25]
15. Sie sollten nun dem neuen Benutzer die gleichen Rollen und Berechtigungen zuweisen, über die der ursprüngliche Benutzer verfügte.
16. Fahren Sie mit Teil 2 fort.

##Teil 2: Beenden des Stream Analytics-Auftrags
1.  Wechseln Sie zur Stream Analytics-Erweiterung des Azure-Verwaltungsportals: ![Grafik26][graphic26]
2.  Suchen Sie den Auftrag, und wechseln Sie zu diesem: ![Grafik27][graphic27]
3.  Wechseln Sie abhängig davon, ob Sie die Anmeldeinformationen für eine Ein- oder Ausgabe ändern, zur Registerkarte "Eingaben" oder "Ausgaben". ![Grafik28][graphic28]
4.  Klicken Sie auf den Befehl "Beenden", und vergewissern Sie sich, dass der Auftrag beendet wurde: ![Grafik29][graphic29] Warten Sie, bis der Auftrag beendet wurde.
5.  Suchen Sie nach der Ein- oder Ausgabe, deren Anmeldeinformationen geändert werden sollen, und wechseln Sie zu dieser: ![Grafik30][graphic30]
6.  Fahren Sie mit Teil 3 fort.

##Teil 3: Bearbeiten der Anmeldeinformationen für den Stream Analytics-Auftrag

###Blob-Speicher/Tabellenspeicher
1.	Suchen Sie das Feld "Speicherkontoschlüssel", und fügen Sie den neu generierten Schlüssel ein: ![Grafik31][graphic31]
2.	Klicken Sie auf den Befehl "Speichern", und bestätigen Sie das Speichern der Änderungen: ![Grafik32][graphic32]
3.	Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.
4.	Fahren Sie mit Teil 4 fort.

###Event Hubs
1.	Suchen Sie das Feld für den Event Hub-Richtlinienschlüssel, und fügen Sie den neu generierten Schlüssel ein: ![Grafik33][graphic33]
2.	Klicken Sie auf den Befehl "Speichern", und bestätigen Sie das Speichern der Änderungen: ![Grafik34][graphic34]
3.	Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.
4.	Fahren Sie mit Teil 4 fort.

###Power BI
1.	Klicken Sie auf "Renew authorization":  
* ![Grafik35][graphic35]
* Die folgende Bestätigung wird geöffnet:  
* ![Grafik36][graphic36]
2.	Klicken Sie auf den Befehl "Speichern", und bestätigen Sie das Speichern der Änderungen: ![Grafik37][graphic37]
3.	Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.
4.	Fahren Sie mit Teil 4 fort.

###SQL-Datenbank
1.	Suchen Sie die Felder "Benutzername" und "Kennwort", und fügen Sie den neu erstellten Anmeldeinformationssatz ein: ![Grafik38][graphic38]
2.	Klicken Sie auf den Befehl "Speichern", und bestätigen Sie das Speichern der Änderungen: ![Grafik39][graphic39]
3.	Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.  
4.	Fahren Sie mit Teil 4 fort.

##Teil 4: Starten des Auftrags ab dem letzten Anhaltepunkt
1.	Verlassen Sie die Ein-/Ausgabe: ![Grafik40][graphic40]
2.	Klicken Sie auf den Befehl "Start": ![Grafik41][graphic41]
3.	Wählen Sie den letzten Anhaltepunkt aus, und klicken Sie auf "OK": ![Grafik42][graphic42]
4.	Fahren Sie mit Teil 5 fort.  

##Teil 5: Entfernen des alten Anmeldeinformationssatzes
Dieser Teil gilt für die folgenden Ein-/Ausgaben: * Blob-Speicher * Event Hubs * SQL-Datenbank * Tabellenspeicher

###Blob-Speicher/Tabellenspeicher
Wiederholen Sie Teil 1 für den Zugriffsschlüssel, der bislang für den Auftrag verwendet wurde, um den jetzt nicht mehr verwendeten Zugriffsschlüssel zu erneuern.

###Event Hubs
Wiederholen Sie Teil 1 für den Schlüssel, der bislang für den Auftrag verwendet wurde, um den jetzt nicht mehr verwendeten Schlüssel zu erneuern.

###SQL-Datenbank
1.	Wechseln Sie zurück zum Abfragefenster von Teil 1 Schritt 7, geben Sie die folgende Abfrage ein, und ersetzen Sie <previous_login_name> mit dem Benutzernamen, der zuvor für den Auftrag verwendet wurde: `DROP LOGIN <previous_login_name>`  
2.	Klicken Sie auf "Ausführen": ![Grafik43][graphic43]  

Die folgende Bestätigung wird geöffnet:

	Command(s) completed successfully.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 

<!---HONumber=AcomDC_1210_2015-->