Mit diesen Schritten wird ein neuer benutzerdefinierter [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) erstellt, der Pushbenachrichtigungen an die App sendet. Sie können ähnlichen Code in einem [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren.

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Controller-Ordner, erweitern Sie **Hinzufügen**, und klicken Sie dann auf **New Scaffolded Item**.

	Der Dialog "Add Scaffold" wird angezeigt.

2. Erweitern Sie **Azure Mobile Services**, klicken Sie auf **Benutzerdefinierter Controller Azure Mobile Services**, klicken Sie dann auf **Hinzufügen**, geben Sie einen **Controllernamen** für `NotifyAllUsersController` an, und klicken Sie anschließend erneut auf **Hinzufügen**.

	![Web-API-Dialogfeld "Add Scaffold"](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

	Dadurch wird eine neue leere Controllerklasse mit der Bezeichnung **NotifyAllUsersController** erstellt.

3. Fügen Sie der neuen Projektdatei "NotifyAllUsersController.cs" folgende **using**-Anweisungen hinzu:

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. Fügen Sie den folgenden Code ein, in dem die POST-Methode für den Controller implementiert ist:

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
				// that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

	>[AZURE.NOTE]Diese POST-Methode kann von jedem beliebigen Client aufgerufen werden, der den Anwendungsschlüssel kennt. Sie ist nicht sicher. Fügen Sie zur Absicherung des Endpunkts der Methode oder Klasse das Attribut `[AuthorizeLevel(AuthorizationLevel.User)]` hinzu, um die Authentifizierung zu erzwingen.

<!---HONumber=August15_HO6-->