Es ist zwar möglich, einen MongoLab-URI in Ihren Code einzufügen, aber wir empfehlen zur leichteren Verwaltung, den Code in seiner Umgebung zu konfigurieren. Dies hat den Vorteil, dass Sie bei einer Änderung des URI den Code über das Azure-Portal aktualisieren können, ohne zu ihm gehen zu müssen.


1. Wählen Sie im Azure-Portal die Option **Web-Apps**.
1. Klicken Sie in der Liste der Web-Apps auf den Namen der gewünschten Web-App. ![WebAppEntry][entry-website] Das Web-App-Dashboard wird angezeigt.

1. Klicken Sie in der Menüleiste auf **Konfigurieren**. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Scrollen Sie nach unten zum Abschnitt "Verbindungszeichenfolgen". ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Geben Sie unter **Name** MONGOLAB_URI ein.
1. Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
1. Wählen Sie in der Dropdown-Liste **Typ** die Option **Benutzerdefiniert** (statt der Standardeinstellung **SQLAzure**).
1. Klicken Sie in der Symbolleiste auf **Speichern**. ![SaveWebApp][button-website-save]

**Hinweis:** Azure fügt zu dieser Variablen das Präfix **CUSTOMCONNSTR\\_** hinzu. Aus diesem Grund verweist der Code oben auf **CUSTOMCONNSTR\\_MONGOLAB_URI**.

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=62-->