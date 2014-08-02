<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Verwenden der Zwischenspeicherung der ASP.NET-Webformularausgabe mit Azure-Websites
===================================================================================

In diesem Thema wird erläutert, wie Sie den Azure Cache Service (Vorschau) zur Unterstützung der ASP.NET-Seitenausgabe-Zwischenspeicherung für ASP.NET-Webformulare verwenden. Die Seitenausgabe-Zwischenspeicherung ist eine Optimierung, die für eine bestimmte Zeitdauer eine zwischengespeicherte Ausgabeseite direkt zurückgibt. Dies ist sinnvoll in Situationen, in denen der Zugriff auf eine Seite häufiger erfolgt als deren normale Änderung. Es ist wichtig festzuhalten, dass die Seitenausgabe-Zwischenspeicherung bei ASP.NET-MVC-Anwendungen nicht unterstützt wird.

Der Cache Service (Vorschau) bietet einen verteilten Zwischenspeicherungsdienst außerhalb der Website. So können alle Instanzen der Website auf die gleiche zwischengespeicherte Ausgabe einer Seite zugreifen.

Die grundlegenden Schritte zur Verwendung des Cache Service (Vorschau) für das Zwischenspeichern der Seitenausgabe umfassen:

-   [Erstellen des Caches](#createcache)
-   [Konfigurieren des ASP.NET-Projekts zur Verwendung von Azure Cache](#configureproject)
-   [Ändern der Datei "web.config"](#configurewebconfig)
-   [Verwenden der Ausgabezwischenspeicherung für die temporäre Rückgabe zwischengespeicherter Versionen einer Seite](#useoutputcaching)

Erstellen des Caches
--------------------

1.  Klicken Sie unten im Azure-Verwaltungsportal auf das Symbol **New**.

    ![Symbol "Neu"](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  Klicken Sie auf **Datendienste**, **Cache** und anschließend auf **Schnellerfassung**.

    ![Dialogfeld "Neuer Cache"](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  Geben Sie einen eindeutigen Namen für den Cache in das Textfeld **Endpunkt** ein. Wählen Sie anschließend die entsprechenden Werte für die anderen Cache-Eigenschaften aus, und klicken Sie auf **Create a New Cache**.

4.  Verwenden Sie das Symbol **Cache** im Verwaltungsportal, um alle Ihre Cache Service-Endpunkte anzuzeigen.

    ![Cache-Symbol](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  Anschließend können Sie einen der Cache Service-Endpunkte auswählen, um dessen Eigenschaften anzuzeigen. In den folgenden Abschnitten werden die Einstellungen aus der Registerkarte **Dashboard** verwendet, um Zwischenspeicherung für ein ASP.NET-Projekt zu konfigurieren.

Konfigurieren des ASP.NET-Projekts
----------------------------------

1.  Vergewissern Sie sich zuerst, dass Sie das neueste **Azure SDK für .NET** [installiert](http://www.windowsazure.com/en-us/downloads/?sdk=net) haben.

2.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das ASP.NET-Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus. (Bei Verwendung von WebMatrix klicken Sie stattdessen in der Symbolleiste auf die Schaltfläche **NuGet**.)

3.  Geben Sie **WindowsAzure.Caching** in das Bearbeitungsfeld **Online suchen** ein.

    ![Dialogfeld "NuGet"](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  Wählen Sie das Paket **Azure Caching** aus, und klicken Sie dann auf **Installieren**.

Ändern der Datei "web.config"
-----------------------------

Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei "web.config" ein. Um Cache für die ASP.NET-Seitenausgabe-Zwischenspeicherung zu verwenden, müssen mehrere Änderungen an der Datei "web.config" vorgenommen werden.

1.  Öffnen Sie die Datei **web.config** für das ASP.NET-Projekt.

2.  Wenn die Datei die Elemente **caching** und **outputCache** enthält, kommentieren Sie sie aus (oder entfernen Sie sie).

3.  Anschließend müssen Sie das Element **caching** auskommentieren, das vom Azure Caching NuGet-Paket hinzugefügt wurde. Das Endergebnis sollte in etwa wie folgender Screenshot aussehen:

    ![Ausgabekonfiguration](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  Suchen Sie nun den Abschnitt **dataCacheClients**. Kommentieren Sie das untergeordnete Element **securityProperties** aus.

    ![Cache-Konfiguration](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  Legen Sie im Element **autoDiscover** das Attribut **identifier** auf die Cache-Endpunkt-URL fest. Die Endpunkt-URL erfahren Sie in den Cache-Eigenschaften im Azure-Verwaltungsportal. Kopieren Sie auf der Registerkarte **Dashboard** den Wert für **ENDPOINT URL** in den Abschnitt **Auf einen Blick**.

    ![Endpunkt-URL](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  Legen Sie im Element **messageSecurity** das Attribut **authorizationInfo** auf den Cache-Zugriffsschlüssel fest. Den Zugriffsschlüssel für den Cache erfahren Sie im Azure-Verwaltungsportal. Klicken Sie anschließend auf der unteren Leiste auf **Schlüssel verwalten**. Klicken Sie neben dem Textfeld **PRIMÄRER ZUGRIFFSSCHLÜSSEL** auf die Kopierschaltfläche.

    ![Schlüssel verwalten](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

Verwenden der Ausgabezwischenspeicherung
----------------------------------------

Im letzten Schritt werden Seiten in der ASP.NET-Webformularanwendung für die Ausgabezwischenspeicherung konfiguriert. Dazu wird das Attribut **OutputCache** am Anfang der .ASPX-Quelle hinzugefügt. Beispiel:

    <%@ OutputCache Duration="45" VaryByParam="*" %>

Mit dem obigen Beispielcode wird die Seite 45 Sekunden lang zwischengespeichert. Da **VaryByParam** auf "\*" festgelegt ist, ändert sich diese zwischengespeicherte Seitenausgabe nicht, auch wenn unterschiedliche Abfrageparameter übergeben werden. Mit dem folgenden Beispielcode wird jedoch bei jedem Wert für den Parameter "UserId" eine andere Version der Seite zwischengespeichert:

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
