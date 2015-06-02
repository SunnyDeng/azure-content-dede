<properties 
	pageTitle="Microsoft Azure-API-Apps – Protokollconnectors | API-Apps-Microservice" 
	description="Erfahren Sie, wie Sie Microsoft Azure-Protokollconnector-API-Apps erstellen und die API-App Ihrer Logik-App hinzufügen; Microservices&quot;" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Protokollconnectors in Microsoft Azure App Service


## Was ist ein Protokollconnector?
Protokollconnectors sind "API-Apps", die Sie verwenden können, um Apps zu erstellen, die über eine Vielzahl von Protokollen wie HTTP, SMTP, FTP usw. kommunizieren können.

Sie finden die Protokollconnectors im Microsoft Azure-Portal, indem Sie auf **Neu** > **Web und Mobil** > **Azure Marketplace** klicken. Geben Sie das Suchstichwort **Protokoll** ein, und drücken Sie die EINGABETASTE.

Die folgenden Protokollconnectors sind in Azure Marketplace verfügbar:

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- Datei

Es folgt eine Kurzbeschreibung der einzelnen Protokollconnectors:

<table>
<tr>
<th> Name</th>
<th> Beschreibung</th>
<th> Trigger</th>
<th> Aktionen</th>

<tr>
<td>SFTP
<td>Ein SFTP-Connector ermöglicht Ihnen das Herunterladen von Dateien vom bzw. Hochladen von Dateien auf einen SFTP-Server.
<td>Neue Datei im SFTP-Ordner verfügbar

<td>Dateien: hochladen, löschen, auflisten und abrufen 

</tr>

<tr>
<td>POP3
<td>Mit einem POP3-Connector können Sie E-Mail von einem POP3-Server herunterladen.
<td>Neue Nachricht geht ein
<td>N/V
</tr>

<tr>
<td>FTP
<td>Ein FTP-Connector ermöglicht Ihnen das Herunterladen von Dateien vom bzw. Hochladen von Dateien auf einen FTP-Server.
<td>Neue Datei im FTP-Ordner verfügbar
<td>Datei: hochladen, abrufen, löschen und auflisten
</tr>

<tr>
<td>HTTP
<td>Mit einem HTTP-Connector können Sie Daten über das Protokoll HTTP oder HTTPS an HTTP-Server senden.
<td>Keine
<td>Bereitstellen, abrufen, löschen, ablegen
</tr>

<tr>
<td>SMTP
<td>Ein SMTP-Connector ermöglicht das Senden von E-Mail von einem SMTP-Server.
<td>N/V
<td>Senden von E-Mail
</tr>

<tr>
<td>Datei
<td>Ein Dateiconnector ermöglicht das Herstellen einer Verbindung mit dem lokalen Dateiserver und Ausführen von Aktionen, wie z. B. Dateien hochladen, abrufen, löschen und auflisten. Es gibt auch einen Trigger zum Abrufen von Dateien.
<td>Neue Datei zum Gerät hinzugefügt
<td>Datei: hochladen, abrufen, löschen und auflisten
</tr>


</table>

Nun, da Sie eine Vorstellung haben, was unsere Protokollconnectors leisten können, wollen wir einen Blick auf einige einfache Anwendungsfälle für diese Connectors werfen.

### Überwachung von Kundenfeedback ###
Angenommen, Ihr Unternehmen hat vor Kurzem eine neue App veröffentlicht, und das Team möchte wissen, was Kunden in sozialen Medien über die App zu sagen haben. Jedes Teammitglied kann in regelmäßigen Abständen die verschiedenen Websites sozialer Medien überprüfen und raten, welche Stichworte Kunden zum Diskutieren Ihrer App ggf. verwenden. Eleganter ist allerdings das Erstellen eines Twitter-Connectors, der für eine Überwachung von Twitter auf bestimmte Hashtags, Erwähnungen und Stichworte konfiguriert wird. Sie können dann den SMTP-Connector zum Erstellen einer E-Mail-Nachricht verwenden, die den Inhalt der übereinstimmenden Tweets enthält und dann an die Teammitglieder gesendet wird. Anschließend können Sie einen HTTP-Connector zum Veröffentlichen dieser Tweets auf einer internen Unternehmenswebsite nutzen. Sie erreichen dies alles, ohne eine einzige Codezeile schreiben zu müssen.

Lassen Sie uns anfangen.

## Erstellen eines Connectors

Ein Connector kann im Azure-Portal erstellt werden.

### Erstellen eines Connectors im Azure-Portal

Betrachten wir nun die Erstellung eines SMTP-Connectors mithilfe von Azure Marketplace.

1. Melden Sie sich beim Microsoft Azure-[Portal](https://manage.windowsazure.com) an.
2. Wählen Sie **NEU** > **Web und Mobil** > **Azure Marketplace**.
3. Geben Sie in das **Suchfeld** den Begriff **Protokoll** ein, und drücken Sie die **EINGABETASTE**. Sie können es auch in der Liste auswählen. Nach dem Auswählen wird ein neues Blatt bzw. Fenster geöffnet. Klicken Sie auf **Erstellen**. 
4. Geben Sie die folgenden Eigenschaften ein:

<table>
<tr><th>Eigenschaft</th> <th>Beschreibung</th> </tr>
<tr><td>Name</td> <td>Beschreibung</td> </tr>
<tr><td>Paketeinstellungen</td> <td>Verwenden Sie die Eigenschaft "Paketeinstellungen", um für den SMTP-Server alle Authentifizierungsdetails einzugeben, wie z. B. Benutzername, Kennwort, Portnummer, Serveradresse usw. </td> </tr>
<tr><td>App Service-Plan</td> <td>Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.</th> </td>
<tr><td>Preisstufe</td> <td>Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet.</td> </tr>
<tr><td>Ressourcengruppe</td> <td>Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Unter "Verwenden von Ressourcengruppen" wird diese Eigenschaft erläutert.</td> </tr>
<tr><td>Abonnement</td> <td>Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.</td> </tr>
<tr><td>Standort</td> <td>Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird.</th> </td>
<tr><td>Zum Startmenü hinzufügen</td> <td>Wählen Sie diese Option aus, um die API-App Ihrem Startmenü (der Startseite) hinzufügen.</td></tr>
</table>

### Zugreifen auf Connectors mit REST-APIs
[Zugreifen auf Connectors mit REST-APIs](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Hinzufügen eines Connectors zu Ihrer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese Connectors verwenden können. Sie können z. B. eine *Logik*-App erstellen, indem Sie einen oder mehrere Ihrer Connectors in einer einzigen Anwendung *logisch* kombinieren.

Zum Verwenden Ihrer Connectors innerhalb Ihrer *Logik*-App wählen Sie in der Liste einen vorkonfigurierten Connector aus, fügen ihn Ihrem Entwurfsworkflow hinzu, nehmen die erforderlichen Konfigurationsänderungen vor, und schon ist er einsatzbereit.

Um diese Schritte durchführen zu können, benötigen Sie eine Web-App, mobile App oder Logik-App. Unter <> finden Sie die einzelnen Schritte. Sobald Ihre Anwendung verfügbar ist, fügen Sie die Connectors hinzu. Das geht so:

Gehen Sie folgendermaßen vor, um einen Connector einer Logik-App hinzuzufügen:

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre Logik-, mobile oder Web-App. 

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. Unter [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) werden die Schritte erläutert.

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus.
3. Wählen Sie im **Katalog** den Connector aus. Er wird Ihrer App hinzugefügt.
4. Konfigurieren Sie den Connector:
5. Jeder Connector hat Eigenschaften, die spezifisch für den Dienst und die Umgebung sind, mit der er eine Verbindung herstellt. Geben Sie die Details der Eigenschaften ein. Bedenken Sie, dass einige Eigenschaften optional sind.
6. Klicken Sie zum Speichern der Änderungen auf **OK**.


## Sicherheit
Connectors verwenden entweder OAuth oder Benutzernamen und Kennwörter.

## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md) | [Websites und Web-Apps in Azure App Service](app-service-web-app-azure-portal.md) |


## Weitere Connectors

[BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md) | [Enterprise-Connectors](app-service-logic-enterprise-connectors.md) | [Business-to-Business-Connectors](app-service-logic-b2b-connectors.md) | [Connectors für soziale Netzwerke](app-service-logic-social-connectors.md) | [App und Data Services-Connectors](app-service-logic-data-connectors.md) | [Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)<br/><br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->