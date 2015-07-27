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
   ms.date="07/02/2015"
   ms.author="andalmia"/>

# Verwenden des Box-Connectors in Logik-Apps



Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Es gibt Situationen, in denen Sie möglicherweise mit Box arbeiten müssen, um Daten sicher mit anderen Personen zu teilen – auch außerhalb Ihrer Firewall.



Die Box-Katalog-App bietet Ihnen Aktionen als Mechanismen für die Interaktion mit Box:



1. **Aktionen**: Über die Aktionen können Sie vordefinierte Aktionen für das Box-Konto ausführen, das mit der Logik-App konfiguriert wurde. Im Folgenden sind die Aktionen aufgeführt, die über ein Box-Konto mit dem Box-Connector durchgeführt werden können:

	a. *Dateien auflisten:* Dieser Vorgang gibt die Informationen aller Dateien in einem Ordner zurück. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
  <tr>
    <td><b>Parametername</b></td>
    <td><b>Beschreibung</b></td>
    <td><b>Erforderlich</b></td>
  </tr>
  <tr>
    <td>Ordnerpfad</td>
    <td>Pfad des Ordners, für den die Auflistung durchgeführt werden soll.</td>
    <td>Ja</td>
  </tr>
</table>>[AZURE.NOTE]Hierdurch werden keine Dateiinhalte zurückgegeben.



    b. *Datei abrufen:* Dieser Vorgang ruft eine Datei mit ihren Inhalten und Eigenschaften ab. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
  <tr>
    <td><b>Parametername</b></td>
    <td><b>Beschreibung</b></td>
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
</table>>[AZURE.NOTE]Bei diesem Vorgang wird die Datei nach dem Lesen nicht gelöscht.



    c. Datei hochladen: Wie der Name schon sagt, wird die Datei durch diese Aktion in ein Box-Konto hochgeladen. Wenn die Datei bereits vorhanden ist, wird sie nicht überschrieben, und ein Fehler wird ausgelöst. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

	<table>
  <tr>
    <td><b>Parametername</b></td>
    <td><b>Beschreibung</b></td>
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
</table>d. Datei löschen: Diese Aktion löscht die angegebene Datei aus einem Ordner. Wenn die Datei/der Ordner nicht gefunden wird, wird eine Ausnahme ausgelöst. Nachfolgend sind die Parameter aufgeführt, die für die Aktion erforderlich sind:

 	<table>
  <tr>
    <td><b>Parametername</b></td>
    <td><b>Beschreibung</b></td>
    <td><b>Erforderlich</b></td>
  </tr>
  <tr>
    <td>Dateipfad</td>
    <td>Vollständiger Dateipfad einschließlich Ordnern.</td>
    <td>Ja</td>
  </tr>
</table>




## Erstellen eines Box-Connectors für Ihre Logik-App

Zur Verwendung des Box-Connectors müssen Sie zunächst eine Instanz der Box-Connector-API-App erstellen. Sie können sie innerhalb oder außerhalb des Logik-App-Designers erstellen. Außerhalb des Designers erfolgt das Erstellen wie folgt:

1. Öffnen Sie auf der Homepage des Azure-Portals Azure Marketplace.

2. Suchen Sie unter „Alles“ nach „Box-Connector“.

3. Konfigurieren Sie den Box-Connector, und klicken Sie auf „Erstellen“:

	![][1]

4. Anschließend können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Box-Connectors erstellen.


## Verwenden des Box-Connectors in Ihrer Logik-App

Sobald Ihre API-App erstellt wurde, können Sie den Box-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:


1. Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Box-Connector befindet.

2. Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. Der Box-Connector wird im Abschnitt "Zuletzt verwendet" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus.

3. Wenn der Box-Connector zu Beginn der Logik-App ausgewählt wird, fungiert er als Trigger. Andernfalls können mit dem Connector Aktionen für das Box-Konto ausgeführt werden. Beachten Sie, dass der Box-Connector über keine Trigger verfügte, als dieser Artikel verfasst wurde.

4. Der erste Schritt besteht in der Authentifizierung und Autorisierung von Logik-Apps, um Vorgänge in Ihrem Namen durchzuführen. Zum Starten der Autorisierung klicken Sie im Box-Connector auf "Autorisieren".

	![][2]

5. Durch das Klicken auf "Autorisieren" wird das Authentifizierungsdialogfeld von Box angezeigt. Geben Sie die Anmeldeinformationen des Box-Kontos an, mit dem Sie die Vorgänge ausführen möchten.

	![][3]

6. Erteilen Sie den Logik-Apps Zugriff auf Ihr Konto, um Vorgänge in Ihrem Namen auszuführen.

	![][4]

7. Die Liste der Aktionen wird angezeigt, und Sie können die entsprechenden Vorgänge auswählen, die Sie ausführen möchten.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=July15_HO3-->