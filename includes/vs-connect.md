
   * Melden Sie sich bei Ihrem Azure-Konto an, indem Sie Ihre Anmeldeinformationen eingeben.

     Diese Methode ist schneller und einfacher, dabei werden Azure SQL-Datenbank oder Azure Mobile Services jedoch nicht im Fenster **Server-Explorer** angezeigt.

     Klicken Sie in **Server-Explorer** auf die Schaltfläche **Mit Azure verbinden**. Alternativ können Sie auch mit der rechten Maustaste auf den Knoten **Azure** klicken und im Kontextmenü **Mit Azure verbinden** auswählen.

   * Installieren Sie ein Verwaltungszertifikat, dass den Zugriff auf Ihr Konto ermöglicht.

     Klicken Sie in **Server-Explorer** mit der rechten Maustaste auf den Knoten **Azure**, und wählen Sie im Kontextmenü **Abonnements verwalten** aus. Klicken Sie im Dialogfeld **Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings*-Datei genannt) für Ihr Azure-Konto.

     >[AZURE.NOTE]Laden Sie die Abonnementdatei in einen Ordner außerhalb Ihrer Quellcodeverzeichnisse herunter (beispielsweise in den Ordner "Downloads"), und löschen Sie sie nach Abschluss des Importvorgangs. Böswillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

	Weitere Informationen finden Sie unter [Verbinden mit Azure in Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).

<!---HONumber=July15_HO4-->