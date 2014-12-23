<properties urlDisplayName="Send Email Using SendGrid" pageTitle="Senden von E-Mails mit SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid-Dienst, Azure E-Mail, Mobile Services-E-Mail" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/27/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />


# Senden von E-Mails in Mobile Services mit SendGrid

Dieses Thema beschreibt, wie Sie E-Mail-Funktionen zu Ihrem mobilen Dienst hinzufügen können. In diesem Lernprogramm fügen Sie serverseitige Skripts hinzu, um E-Mails mithilfe von SendGrid zu senden. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann eine E-Mail, wenn ein Datensatz eingefügt wird.

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die eine benutzerdefinierte Integration erleichtern. Weitere Informationen finden Sie unter <http://sendgrid.com>.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von E-Mail-Funktionen behandelt:

1. [SendGrid-Konto erstellen]
2. [Hinzufügen eines Skripts zum Senden von E-Mails]
3. [Einfügen von Daten zum Empfangen von E-Mails]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen. 

<h2><a name="sign-up"></a>Erstellen eines neuen SendGrid-Kontos</h2>

[WACOM.INCLUDE [SendGrid-Anmeldung](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a>Registrieren eines neuen Skripts, das E-Mails sendet</h2>

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

2. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**. 

	![][1]

3. Klicken Sie in **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.
   
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

     <div class="dev-callout"><b>Hinweis</b>
     <p>Wenn Sie nicht über eine registrierte Domäne verfügen, können Sie stattdessen die Domäne Ihres mobilen Dienstes im Format <strong>notifications@<i>your-mobile-service</i>.azure-mobile.net</strong> verwenden. Allerdings werden Nachrichten, die an die Domäne Ihres mobilen Dienstes gesendet werden, ignoriert.</p>
    </div> 

6. Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript so konfiguriert, dass jedes Mal, wenn ein Datensatz in die Tabelle **TodoItem** eingefügt wird, eine E-Mail gesendet wird.

<h2><a name="insert-data"></a>Einfügen von Testdaten zum Empfangen von E-Mails</h2>

1. Führen Sie im Client-App-Projekt die Schellstartanwendung aus. 

	In diesem Thema wird die Windows Store-Version des Schnellstarts angezeigt.

2. Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

	![][3]

3. Sie erhalten eine E-Mail, die so aussieht wie in der Benachrichtigung unten. 

	![][4]

	Herzlichen Glückwunsch! Sie haben Ihren mobilen Dienst erfolgreich für das Senden von E-Mails mithilfe von SendGrid konfiguriert.

## <a name="nextsteps"> </a>Nächste Schritte

Nun, da Sie wissen, wie leicht die Verwendung des SendGrid-E-Mail-Dienstes mit Mobile Services ist, folgen Sie diesen Links, um mehr über SendGrid zu erfahren:

-   SendGrid-API-Dokumentation:
    <https://sendgrid.com/docs>
-   Spezielles SendGrid-Angebot für Azure-Kunden:
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[SendGrid-Konto erstellen]: #sign-up
[Hinzufügen eines Skripts zum Senden von E-Mails]: #add-script
[Einfügen von Daten zum Empfangen von E-Mails]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Anmeldeseite]: https://sendgrid.com/windowsazure.html
[Mehrseitige Benutzeranmeldeinformationen]: https://sendgrid.com/credentials
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email

