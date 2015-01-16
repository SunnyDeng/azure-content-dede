<properties urlDisplayName="Website with WebMatrix" pageTitle=".NET-Website mit WebMatrix - Azure-Lernprogramme" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure-Website WebMatrix Azure-Website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Erfahren Sie mehr über die Entwicklung und Bereitstellung einer Azure-Website mit WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/27/2014" ms.author="tomfitz" />





#Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix
Dieser Leitfaden beschreibt, wie Sie mit Microsoft WebMatrix eine Website erstellen und in Azure bereitstellen.  Sie verwenden hierzu eine Beispielanwendung einer WebMatrix-Websitevorlage.

Sie erhalten Informationen zu folgenden Themen:

* Anmelden bei Azure von WebMatrix aus
* Erstellen einer Site mit einer in WebMatrix integrierten Vorlage 
* Bereitstellen der benutzerdefinierten Website direkt über WebMatrix in Azure


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Anmelden bei Azure

1. Starten Sie WebMatrix.
2. Wenn Sie WebMatrix 3 zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden.  Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**.  Wählen Sie **Anmelden** für die Anmeldung mit Ihrem Microsoft-Konto aus.

	![Add Account][addaccount]

3. Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

	![Sign into Azure][signin]	


## Erstellen einer Site für Azure mit einer integrierten Vorlage

1. Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog** aus, um eine neue Website aus dem Vorlagenkatalog zu erstellen:

	![New site from Template Gallery][sitefromtemplate]

2. Im Vorlagenkatalog wird eine Liste der verfügbaren Vorlagen angezeigt, die lokal oder auf Azure ausgeführt werden können.  Wählen Sie die Vorlage **Konditorei** in der Liste aus, geben Sie **Konditoreibeispiel** im Feld **Websitename** ein, und klicken Sie auf **Weiter**.

	![Create Site from Template][sitefromtemplatedetails]

3. Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure-Website für eine lokale Site erstellen.  Wählen Sie einen eindeutigen Namen und das Rechenzentrum aus, in dem die Site erstellt werden soll: 

	![Create site on Azure][sitefromtemplateazure]

	Nachdem WebMatrix die Website erstellt hat, wird die WebMatrix-IDE angezeigt:

	![WebMatrix IDE][howtowebmatrixide] 

## Einrichten von E-Mail

Das Konditoreibeispiel umfasst ein simuliertes Bestellformular, das eine E-Mail-Nachricht mit dem bestellten Artikel sendet. Sie verwenden den SendGrid-E-Mail-Dienst in Azure zum Senden von E-Mails von Ihrer Website aus.

1. Führen Sie die Schritte im Lernprogramm [Senden von E-Mails mit SendGrid in Azure][sendgridexample] aus, um ein SendGrid-Konto einzurichten und die Verbindungsinformationen abzurufen. Sie müssen nicht das gesamte Lernprogramm ausführen - nur bis zum Abrufen von Verbindungsinformationen.

2. Fügen Sie das SendGrid NuGet-Paket zum WebMatrix-Projekt hinzu. Klicken Sie zunächst die Schaltfläche "NuGet".

    ![Add SendGrid][addsendgrid]

    Suchen und installieren Sie SendGrid.

    ![Install SendGrid][installsendgrid]

    Nachdem das Paket installiert wurde, sehen Sie, dass die SendGrid-Assemblys zu "bin" hinzugefügt wurden.

    ![SendGrid added][binsendgrid]

3. Öffnen Sie die Seite *Order.cshtml*, indem Sie auf den Dateinamen doppelklicken.

	![][modify2]

4. Fügen Sie am Anfang der Datei folgenden Code hinzu:

        @using SendGrid;
        @using System.Net.Mail;

4. Suchen Sie den Kommentar "//SMTP Configuration for Hotmail", und löschen oder kommentieren Sie den gesamten Code für die Verwendung von WebMail.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Fügen Sie Code zum Verwenden von SendGrid anstatt WebMail für das Senden von E-Mails hinzu. Fügen Sie den folgenden Code anstelle des Codes hinzu, den Sie im vorherigen Schritt gelöscht haben.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. Klicken Sie im WebMatrix-Menüband auf **Ausführen**, um die Website zu testen.

	![][modify4]

7. Klicken Sie für eines der Produkte auf **Jetzt bestellen**, und senden Sie die Bestellung an sich selbst.

8. Überprüfen Sie Ihre E-Mail-Nachrichten darauf, ob Sie die Bestellbestätigung erhalten haben. Wenn Sie Probleme beim Senden von E-Mail-Nachrichten haben, finden Sie weitere Informationen unter [Issues with Sending Email][sendmailissues] (Probleme beim Senden von E-Mail, in englischer Sprache) in der Problembehandlung zu ASP.NET-Webseiten (Razor).
 

## Bereitstellen der benutzerdefinierten Website direkt über WebMatrix in Azure

1. Klicken Sie in WebMatrix im Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** aufzurufen.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Klicken Sie in das Kontrollkästchen neben "bakery.sdf", und klicken Sie dann auf **Weiter**.  Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die aktualisierte Website auf Azure unten in der WebMatrix-IDE angezeigt.  

	![Publishing Complete][publishcomplete]

3. Klicken Sie auf den Link, um die Website in Ihrem Browser zu öffnen:

	![Bakery Sample Site][bakerysample]

Sie können die URL für die Website auch im Azure-Portal finden, indem Sie auf **Websites** klicken, um alle Websites Ihres Abonnements anzuzeigen. Die URLs der einzelnen Websites befinden sich in der URL-Spalte auf der Seite mit den Websites.

## Ändern der Website und erneute Veröffentlichung auf der Azure-Website

Sie können die Site mit WebMatrix ändern und dann erneut auf der Azure-Website veröffentlichen. Im folgenden Verfahren fügen Sie ein Kontrollkästchen hinzu, mit dem angegeben werden kann, ob es sich bei der Bestellung um ein Geschenk handelt.

1. Öffnen Sie die Seite *Order.cshtml*.

2. Suchen Sie die Klassendefinition "shipping". Fügen Sie den folgenden Code direkt nach dem &lt;li&gt;-Block ein.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Suchen Sie in der Datei nach der Zeile "var shipping = Request["orderShipping"];", und fügen Sie die folgende Codezeile direkt danach ein.

		var gift = Request["isGift"];

4. Direkt nach dem Codeblock, der überprüft, dass die Lieferadresse nicht leer ist, im folgenden Codeausschnitt.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Ihre Datei *order.cshtml* sollte in etwa der folgenden Abbildung entsprechen.

	![][modify6]

5. Speichern Sie die Datei, und führen Sie die Website lokal aus. Senden Sie dann eine Testbestellung an sich selbst. Stellen Sie sicher, dass Sie auch das neue Kontrollkästchen testen.

	![][modify7]

6. Veröffentlichen Sie die Website erneut, indem Sie im Menüband **Startseite** auf **Veröffentlichen** klicken.

7. Stellen Sie sicher, dass im Dialogfeld **Vorschau veröffentlichen** beide "Order.cshtml" ausgewählt sind, und klicken Sie auf "Weiter".

8. Klicken Sie auf den Link, um die Website im Browser zu öffnen und das Update auf der Azure-Website zu testen.

# Nächste Schritte

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix auf Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

* [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/en-us/documentation/articles/sendgrid-dotnet-how-to-send-email/
