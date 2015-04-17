<properties 
   pageTitle="Verwenden des Box-Connectors in Logik-Apps" 
   description="Verwenden des Box-Connectors in Logik-Apps" 
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
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# **Verwenden des Box-Connectors in Logik-Apps**

 

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. Es gibt Situationen, in denen Sie möglicherweise mit Box arbeiten müssen, um Daten sicher mit anderen Personen zu teilen - auch außerhalb Ihrer Firewall.

 

Die Box-Galerie-App bietet Ihnen Trigger und Aktionen als Mechanismen für die Interaktion mit Box:

 

1. **Auslösen** **bei verfügbarer Datei**: Verwenden Sie diese Option, wenn Sie eine Logik-App initiieren möchten, sobald Ihrem Box-Ordner eine Datei hinzugefügt wird. Der Trigger überprüft den angegebenen Box-Ordner mit einer konfigurierten Häufigkeit und löst den Datenfluss aus, sobald eine Datei im angegebenen Ordner vorhanden ist. Er gibt den Inhalt und die Eigenschaften der Datei zurück, und nachdem sie erfolgreich an den nächsten Schritt in der Logik-App übergeben wurde, wird die Datei gelöscht.


	<table>
	  <tr>
	    <td><b>Parametername</b></td>
	    <td><b>Beschreibung</b>:</td>
	    <td><b>Erforderlich</b></td>
	  </tr>
	  <tr>
	    <td>Dateipfad</td>
	    <td>Der Pfad des Ordners, in dem die Datei enthalten ist.</td>
	    <td>Ja</td>
	  </tr>
	  <tr>
	    <td>Dateityp</td>
	    <td>Gibt an, ob die Datei im Text- oder im Binärformat vorliegt.</td>
	    <td>Nein</td>
	  </tr>
	  <tr>
	    <td>Häufigkeit</td>
	    <td>Gibt die Häufigkeit an. Diese wird aus den aufgelisteten Typen ausgewählt. Folgende Typen stehen zur Auswahl: Jahre, Monate, Wochen, Tage, Stunden, Minuten oder Sekunden</td>
	    <td>Ja</td>
	  </tr>
	  <tr>
	    <td>Intervall</td>
	    <td>Gibt die Maßeinheit für die Häufigkeit an.</td>
	    <td>Ja</td>
	  </tr>
	</table>


 

2. **Aktionen**: Über die Aktionen können Sie vordefinierte Aktionen für das Box-Konto durchführen, das mit der Logik-App konfiguriert wurde. Im Folgenden sind die Aktionen aufgeführt, die über ein Box-Konto mit dem Box-Connector durchgeführt werden können:

	a. *List Files:* Dieser Vorgang gibt die Informationen aller Dateien in einem Ordner zurück. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
	  <tr>
	    <td><b>Parametername</b></td>
	    <td><b>Beschreibung</b>:</td>
	    <td><b>Erforderlich</b></td>
	  </tr>
	  <tr>
	    <td>Ordnerpfad</td>
	    <td>Pfad des Ordners, für den die Auflistung durchgeführt werden soll.</td>
	    <td>Ja</td>
	  </tr>
	</table>

	*Hinweis: Hierdurch werden keine Dateiinhalte zurückgegeben.*

 

    b. *Get File:* Dieser Vorgang ruft eine Datei mit ihren Inhalten und Eigenschaften ab. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
	  <tr>
	    <td><b>Parametername</b></td>
	    <td><b>Beschreibung</b>:</td>
	    <td><b>Erforderlich</b></td>
	  </tr>
	  <tr>
	    <td>Dateipfad</td>
	    <td>Der Pfad des Ordners, in dem die Datei enthalten ist.</td>
	    <td>Ja</td>
	  </tr>
	  <tr>
	    <td>Dateityp</td>
	    <td>Gibt an, ob die Datei im Text- oder im Binärformat vorliegt.</td>
	    <td>Nein</td>
	  </tr>
	</table>
	*Hinweis: Bei diesem Vorgang wird die Datei nach dem Lesen nicht gelöscht.*

 

    c. Datei hochladen: Wie der Name schon sagt, wird die Datei durch diese Aktion in ein Box-Konto hochgeladen. Wenn die Datei bereits vorhanden ist, wird sie nicht überschrieben, und ein Fehler wird ausgelöst. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
	  <tr>
	    <td><b>Parametername</b></td>
	    <td><b>Beschreibung</b>:</td>
	    <td><b>Erforderlich</b></td>
	  </tr>
	  <tr>
	    <td>Dateipfad</td>
	    <td>Pfad zur Datei.</td>
	    <td>Ja</td>
	  </tr>
	  <tr>
	    <td>Inhalt der Datei</td>
	    <td>Dateiinhalt, der hochgeladen werden soll.</td>
	    <td>Ja</td>
	  </tr>
	  <tr>
	    <td>Codierung für die Inhaltsübertragung</td>
	    <td>Der Codierungstyp des Inhalts, entweder Base64 oder keine Codierung.</td>
	    <td> </td>
	  </tr>
	</table>


    d. Datei löschen: Diese Aktion löscht die angegebene Datei aus einem Ordner. Wenn die Datei/der Ordner nicht gefunden wird, wird eine Ausnahme ausgelöst. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

 	<table>
	  <tr>
	    <td><b>Parametername</b></td>
	    <td><b>Beschreibung</b>:</td>
	    <td><b>Erforderlich</b></td>
	  </tr>
	  <tr>
	    <td>Dateipfad</td>
	    <td>Der Pfad des Ordners, in dem die Datei enthalten ist.</td>
	    <td>Ja</td>
	  </tr>
	</table>


 

## **Erstellen eines Box-Connectors für Ihre Logik-App** ##

Zur Verwendung des Box-Connectors müssen Sie zunächst eine Instanz der Box-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.

2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "Box-Connector".

3. Konfigurieren Sie den Box-Connector, und klicken Sie auf  "Erstellen":

	![][1]

4. Sobald Sie fertig sind, können Sie jetzt in derselben Ressourcengruppe eine Logik-App zur Verwendung des Box-Connectors erstellen.


## **Verwenden des Box-Connectors in Ihrer Logik-App** ##

Sobald Ihre API-App erstellt wurde, können Sie jetzt den Box-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:


1. Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Box-Connector befindet.

2. Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. Der Box-Connector wird im Abschnitt "Zuletzt verwendet" in der Galerie auf der rechten Seite angezeigt. Wählen Sie ihn aus.

3. Wenn der Box-Connector zu Beginn der Logik-App ausgewählt wird, fungiert er als Trigger. Andernfalls können mit dem Connector Aktionen am Box-Konto durchgeführt werden.

4. Der erste Schritt besteht in der Authentifizierung und Autorisierung von Logik-Apps, um Vorgänge in Ihrem Namen durchzuführen. Zum Starten der Autorisierung klicken Sie im Box-Connector auf "Autorisieren".

	![][2]

5. Durch das Klicken auf "Autorisieren" wird das Authentifizierungsdialogfeld von Box angezeigt. Geben Sie die Anmeldeinformationen des Box-Kontos an, mit dem Sie die Vorgänge ausführen möchten.

	![][3]

6. Erteilen Sie den Logik-Apps Zugriff auf Ihr Konto, um Vorgänge in Ihrem Namen auszuführen. 

	![][4]

7. Wenn der Box-Connector als Trigger konfiguriert ist, werden die Trigger angezeigt. Andernfalls wird eine Liste von Aktionen angezeigt, sodass Sie die Operation auswählen können, die durchgeführt werden soll.  

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=49-->