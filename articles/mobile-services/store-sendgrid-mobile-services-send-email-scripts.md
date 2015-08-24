<properties 
	pageTitle="Senden von E-Mails mit SendGrid | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mit dem SendGrid-Dienst E-Mails von Ihrer Azure Mobile Services-App senden." 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="Erikre"/>


# Senden von E-Mails in Mobile Services mit SendGrid

Dieses Thema beschreibt die Hinzufügung von E-Mail-Funktionen zu Ihrem mobilen Dienst. In diesem Lernprogramm fügen Sie serverseitige Skripts hinzu, um E-Mails mithilfe von SendGrid zu senden. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine E-Mail.

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Weitere Informationen finden Sie unter <http://sendgrid.com>.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von E-Mail-Funktionen behandelt:

1. [Erstellen von SendGrid-Konten]
2. [Hinzufügen eines Skripts zum Senden von E-Mails]
3. [Einfügen von Daten zum Empfangen von E-Mails]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen.

## <a name="sign-up"></a>Erstellen neuer SendGrid-Konten

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>Registrieren neuer Skripts zum Senden von E-Mails

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

2. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![][1]

3. Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.
   
	![][2]

	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

4. Ersetzen Sie die Einfügefunktion durch den folgenden Code:

        var SendGrid = require('sendgrid').SendGrid;
        
        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       
                
                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. Ersetzen Sie die Platzhalter im Skript oben durch die korrekten Werte:

	- **_username_ und _password_**: die SendGrid-Anmeldeinformationen aus dem Schritt [Erstellen eines SendGrid-Kontos].

	- **_email-address_**: die Adresse, an die die E-Mail gesendet wird. In einer tatsächlichen App können Sie E-Mail-Adressen mithilfe von Tabellen speichern und abrufen. Zum Testen der App können Sie einfach Ihre eigene E-Mail-Adresse verwenden.

	- **_from-address_**: die Adresse, von der aus die E-Mail gesendet wird. Verwenden Sie nach Möglichkeit eine Adresse aus einer registrierten Domäne, die zu Ihrer Organisation gehört.

     >[AZURE.NOTE]Wenn Sie über keine registrierte Domäne verfügen, können Sie stattdessen die Domäne Ihres mobilen Diensts im Format **notifications@_your-mobile-service_.azure-mobile.net* verwenden. Allerdings werden Nachrichten, die an die Domäne Ihres mobilen Dienstes gesendet werden, ignoriert.

6. Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript so konfiguriert, dass jedes Mal, wenn ein Datensatz in die Tabelle **TodoItem** eingefügt wird, eine E-Mail gesendet wird.

## <a name="insert-data"></a>Einfügen von Testdaten zum Empfangen von E-Mails

1. Führen Sie im Client-App-Projekt die Schellstartanwendung aus. 

	In diesem Thema wird die Windows Store-Version des Schnellstarts angezeigt.

2. Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

	![][3]

3. Sie erhalten eine E-Mail, die so aussieht wie in der Benachrichtigung unten.

	![][4]

	Herzlichen Glückwunsch! Sie haben Ihren mobilen Dienst erfolgreich für das Senden von E-Mails mithilfe von SendGrid konfiguriert.

## <a name="nextsteps"> </a>Nächste Schritte

Nun, da Sie wissen, wie leicht die Verwendung des SendGrid-E-Mail-Dienstes mit Mobile Services ist, folgen Sie diesen Links, um mehr über SendGrid zu erfahren:

-   SendGrid API-Dokumentation: <https://sendgrid.com/docs>
-   Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Erstellen eines SendGrid-Kontos]: #sign-up
[Erstellen von SendGrid-Konten]: #sign-up
[Hinzufügen eines Skripts zum Senden von E-Mails]: #add-script
[Einfügen von Daten zum Empfangen von E-Mails]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email

 

<!---HONumber=August15_HO7-->