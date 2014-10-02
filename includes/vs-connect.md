-   Melden Sie sich bei Ihrem Azure-Konto an, indem Sie Ihre Anmeldeinformationen eingeben.

    Diese Methode ist schneller und einfacher, dabei werden Azure SQL-Datenbank oder Azure Mobile Services jedoch nicht im Fenster **Server-Explorer** angezeigt.

    Klicken Sie in **Server-Explorer** auf die Schaltfläche **Mit Azure verbinden**. Alternativ können Sie auch mit der rechten Maustaste auf den Knoten **Azure** klicken und im Kontextmenü **Mit Azure verbinden** auswählen.

-   Installieren Sie ein Verwaltungszertifikat, dass den Zugriff auf Ihr Konto ermöglicht.

    Klicken Sie in **Server-Explorer** mit der rechten Maustaste auf den Knoten **Azure**, und wählen Sie im Kontextmenü **Abonnements verwalten** aus. Klicken Sie im Dialogfeld **Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings*-Datei genannt) für Ihr Azure-Konto.

    <div class="dev-callout"><strong>Sicherheitshinweis:</strong><br /> <p>Laden Sie die Abonnementdatei in einen Ordner au&szlig;erhalb Ihrer Quellcodeverzeichnisse herunter (beispielsweise in den Ordner &quot;Downloads&quot;), und l&ouml;schen Sie sie nach Abschluss des Importvorgangs. B&ouml;swillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, k&ouml;nnen Ihre Azure-Services bearbeiten, erstellen und l&ouml;schen.</p></div>

    Weitere Informationen finden Sie unter [Verbinden mit Azure in Visual Studio][].

  [Verbinden mit Azure in Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=324796
