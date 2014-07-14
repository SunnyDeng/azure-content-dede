Es ist zwar möglich, einen MongoLab-URI in Ihren Code einzufügen, aber wir empfehlen zur leichteren Verwaltung, den Code in seiner Umgebung zu konfigurieren. Dies hat den Vorteil, dass Sie bei einer Änderung des URI den Code über das Azure-Portal aktualisieren können, ohne zu ihm gehen zu müssen.

1.  Wählen Sie im Azure-Portal die Option **Websites**. 
2.  Klicken Sie in der Website-Liste auf den Namen der gewünschten Website.  
     ![WebSiteEntry](./media/howto-save-connectioninfo-mongolab/entry-website.png)
    
     Das Website-Dashboard wird angezeigt.

3.  Klicken Sie in der Menüleiste auf **Konfigurieren**.  
     ![WebSiteDashboardConfig](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png)

4.  Scrollen Sie nach unten zum Abschnitt "Verbindungszeichenfolgen".  
     ![WebSiteConnectionStrings](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png)

5.  Geben Sie unter **Name** MONGOLAB\_URI ein. 
6.  Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
7.  Wählen Sie in der Dropdown-Liste **Typ** die Option
    **Benutzerdefiniert** (statt der Standardeinstellung **SQLAzure**).
8.  Klicken Sie in der Symbolleiste auf **Speichern**.  
     ![SaveWebSite](./media/howto-save-connectioninfo-mongolab/button-website-save.png)

**Hinweis:** Azure fügt zu dieser Variable das Präfix **CUSTOMCONNSTR\_** hinzu. Aus diesem Grund verweist der Code oben auf **CUSTOMCONNSTR\_MONGOLAB\_URI.**