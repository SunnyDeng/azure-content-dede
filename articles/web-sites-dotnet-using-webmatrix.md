<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET web site with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure web site with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a web site with Microsoft WebMatrix" authors="" solutions="" manager="" editor="" />

Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix
==================================================================

Dieser Leitfaden beschreibt, wie Sie mit Microsoft WebMatrix eine Website erstellen und in Azure bereitstellen. Sie verwenden hierzu eine Beispielanwendung einer WebMatrix-Websitevorlage.

Sie erhalten Informationen zu folgenden Themen:

-   Anmelden bei Azure von WebMatrix aus
-   Erstellen einer Site mit einer in WebMatrix integrierten Vorlage
-   Bereitstellen der benutzerdefinierten Website direkt über WebMatrix in Azure

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Anmelden bei Azure
------------------

1.  Starten Sie WebMatrix.
2.  Wenn Sie WebMatrix 3 zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. Melden Sie sich mit Ihrem ****Microsoft-Konto an.

    ![Konto hinzufügen](./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png)

3.  Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

    ![Anmelden bei Azure](./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png)

Erstellen einer Site für Azure mit einer integrierten Vorlage
-------------------------------------------------------------

1.  Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

    ![Neue Site aus Vorlagenkatalog](./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png)

2.  Im Vorlagenkatalog wird eine Liste der verfügbaren Vorlagen angezeigt, die lokal oder auf Azure ausgeführt werden können. Wählen Sie die Vorlage **Konditorei** aus der Liste, geben Sie **Konditoreibeispiel** im Feld **Websitename** ein, und klicken Sie auf **Weiter**.

    ![Site aus Vorlage erstellen](./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png)

3.  Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure-Website für eine lokale Site erstellen. Wählen Sie einen eindeutigen Namen, und wählen Sie das Data Center aus, in dem die Site erstellt werden soll:

    ![Site auf Azure erstellen](./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png)

    Nachdem WebMatrix die Website erstellt hat, wird die WebMatrix IDE angezeigt:

    ![WebMatrix-IDE](./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png)

Testen der Website
------------------

Das Konditoreibeispiel umfasst ein simuliertes Bestellformular, mit dem eine E-Mail-Nachricht mit der Bestellung an ein Windows Live Hotmail-Konto, das Sie angeben, gesendet wird.

1.  Erweitern Sie im linken Navigationsbereich von WebMatrix den Ordner **Konditoreibeispiel**.

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png)

2.  Öffnen Sie die Seite *Order.cshtml*, indem Sie auf den Dateinamen doppelklicken.

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png)

3.  Suchen Sie nach dem Kommentar "//SMTP Configuration for Hotmail".

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png)

4.  Ändern Sie die Werte in den folgenden Zeilen, sodass sie Ihrem E-Mail-Anbieter entsprechen:

         WebMail.SmtpServer = "smtp.live.com";
         WebMail.SmtpPort  = 25;
         WebMail.EnableSsl = true; 

         //Geben Sie Ihre Hotmail-Anmeldedaten für Benutzername/Kennwort und eine "Von"-Adresse für die E-Mail an
         WebMail.UserName = "";
         WebMail.Password = "";
         WebMail.From = "";

    Ändern Sie den Wert von "WebMail.SmtpServer" in den Namen des E-Mail-Servers, über den Sie normalerweise E-Mail versenden. Geben Sie dann die Werte für den Benutzernamen und das Kennwort ein. Legen Sie für die Eigenschaft "From" Ihre E-Mail-Adresse fest.

5.  Klicken Sie im WebMatrix-Menüband auf **Ausführen**, um die Site zu testen.

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png)

6.  Klicken Sie für eines der Produkte auf **Jetzt bestellen**, und senden Sie die Bestellung an sich selbst.

7.  Überprüfen Sie Ihre E-Mail-Nachrichten darauf, ob Sie die Bestellbestätigung erhalten haben. Wenn Sie Probleme beim Senden von E-Mail-Nachrichten haben, finden Sie weitere Informationen unter [Issues with Sending Email](http://go.microsoft.com/fwlink/?LinkId=253001#email) (Probleme beim Senden von E-Mail, in englischer Sprache) in der Problembehandlung zu ASP.NET-Webseiten (Razor).

Bereitstellen der benutzerdefinierten Website aus WebMatrix in Azure
--------------------------------------------------------------------

1.  Klicken Sie in WebMatrix im Menüband **Startseite** auf **Veröffentlichen**, um das Dialogfeld **Vorschau veröffentlichen** aufzurufen.

    ![WebMatrix-Vorschau veröffentlichen](./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png)

2.  Klicken Sie in das Kontrollkästchen neben "bakery.sdf", und klicken Sie dann auf **Weiter**. Wenn die Veröffentlichung abgeschlossen ist, wird die URL für die aktualisierte Website auf Azure unten in der WebMatrix-IDE angezeigt.

    ![Veröffentlichung abgeschlossen](./media/web-sites-dotnet-using-webmatrix/howtopublished2.png)

3.  Klicken Sie auf den Link, um die Website in Ihrem Browser zu öffnen:

    ![Konditorei-Beispielsite](./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png)

    Sie können die URL für die Website auch im Azure-Portal finden, indem Sie auf **Web Sites** klicken, um alle Websites Ihres Abonnements anzuzeigen. Die URLs der einzelnen Websites werden in der URL-Spalte auf der Seite mit den Websites angezeigt.

Ändern der Website und erneutes Veröffentlichen auf der Azure-Website
---------------------------------------------------------------------

Sie können die Site mit WebMatrix ändern und dann erneut auf der Azure-Website veröffentlichen. Im folgenden Verfahren fügen Sie ein Kontrollkästchen hinzu, mit dem angegeben werden kann, ob es sich bei der Bestellung um ein Geschenk handelt.

1.  Öffnen Sie die Seite *Order.cshtml*.

2.  Suchen Sie die Klassendefinition "shipping". Fügen Sie den folgenden Code direkt nach dem &lt;li\>-Block ein.

         <li class="gift">
             <div class="fieldcontainer" data-role="fieldcontain">
                 <label for="isGift">This is a gift</label>           
                 <div>@Html.CheckBox("isGift")</div>
             </div>
         </li>

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png)

3.  Suchen Sie nach der Zeile "var shipping = Request["orderShipping"];" in der Datei, und fügen Sie die folgende Codezeile direkt danach ein.

         var gift = Request["isGift"];

4.  Direkt nach dem Codeblock, der überprüft, dass die Lieferadresse nicht leer ist, im folgenden Codeausschnitt.

         if(gift != null) {
             body += "This is a gift." + "<br/>";
         }

    Ihre Datei *order.cshtml* sollte in etwa der folgenden Abbildung entsprechen.

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png)

5.  Speichern Sie die Datei, und führen Sie die Site lokal aus. Senden Sie dann eine Testbestellung an sich selbst. Stellen Sie sicher, dass Sie auch das neue Kontrollkästchen prüfen.

    ![](./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png)

6.  Veröffentlichen Sie die Site erneut, indem Sie im Menüband **Startseite** auf **Veröffentlichen** klicken.

7.  Stellen Sie sicher, dass im Dialogfeld **Vorschau veröffentlichen** beide "Order.cshtml" ausgewählt sind, und klicken Sie auf "Weiter".

8.  Klicken Sie auf den Link, um die Website im Browser zu öffnen und das Update auf der Azure-Website zu testen.

Nächste Schritte
================

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix zu Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

-   [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

-   [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)


