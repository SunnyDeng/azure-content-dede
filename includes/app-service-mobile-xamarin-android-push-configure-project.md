1. Klicken Sie in der Projektmappenansicht (oder in Visual Studio im **Projektmappen-Explorer**) mit der rechten Maustaste auf den **Komponentenordner**, klicken Sie auf **Get More Components...**, suchen Sie nach der Komponente **Google Cloud Messaging Client**, und fügen Sie sie dem Projekt hinzu. 

2. Öffnen Sie die Projektdatei "ToDoActivity.cs", und fügen Sie die folgende "using"-Anweisung hinzu:

		using Gcm.Client;

2. Fügen Sie der **ToDoActivity**-Klasse den folgenden neuen Code hinzu:

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	Dies ermöglicht Ihnen den Zugriff auf die Mobile Services Clientinstanz vom Dienstprozess aus.


4.	Fügen Sie der **OnCreate**-Methode den folgenden Code hinzu, nachdem der **MobileServiceClient** erstellt wurde:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Die **ToDoActivity** ist damit für das Hinzufügen von Pushbenachrichtigungen vorbereitet.

<!---HONumber=Oct15_HO3-->