<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Verwendung des ASP.NET-Sitzungsstatus mit Azure-Websites
========================================================

In diesem Thema wird erläutert, wie Sie den Azure Cache Service (Vorschau) zur Unterstützung des ASP.NET-Sitzungsstatus-Caching verwenden.

Ohne externen Anbieter wird der Sitzungsstatus prozessintern auf dem Webserver, der die Website hostet, gespeichert. Für Azure-Websites gibt es zwei Probleme mit dem prozessinternen Sitzungsstatus. Erstens ist für Websites mit mehreren Instanzen der auf einer Instanz gespeicherte Sitzungsstatus für die anderen Instanzen nicht sichtbar. Da eine Benutzeranforderung an jede Instanz weitergeleitet werden kann, sind die Sitzungsinformationen nicht unbedingt vorhanden. Zweitens können Änderungen an der Konfiguration dazu führen, dass die Website auf einem ganz anderen Server ausgeführt wird.

Der Cache Service (Vorschau) bietet einen verteilten Zwischenspeicherungsdienst außerhalb der Website. Dies löst das Problem mit dem prozessinternen Sitzungsstatus. Weitere Informationen zur Verwendung des Sitzungsstatus finden Sie unter [Überblick über den ASP.NET-Sitzungsstatus](http://msdn.microsoft.com/de-de/library/ms178581.aspx).

Die grundlegenden Schritte zur Verwendung des Cache Service (Vorschau) für das Zwischenspeichern des Sitzungsstatus umfassen:

-   [Erstellen des Caches](#createcache)
-   [Konfigurieren des ASP.NET-Projekts zur Verwendung von Azure Cache](#configureproject)
-   [Ändern der Datei "web.config"](#configurewebconfig)
-   [Verwenden des Sitzungsobjekts zum Speichern und Abrufen zwischengespeicherter Elemente](#usesessionobject)

Erstellen des Caches
--------------------

1.  Klicken Sie unten im Azure-Verwaltungsportal auf das Symbol **New**.

    ![Symbol "Neu"](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  Klicken Sie auf **Data Services**, **Cache** und anschließend auf **Quick Create**.

    ![Dialogfeld "Neuer Cache"](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  Geben Sie einen eindeutigen Namen für den Cache in das Textfeld **Endpoint** ein. Wählen Sie anschließend die entsprechenden Werte für die anderen Cache-Eigenschaften aus, und klicken Sie auf **Create a New Cache**.

4.  Verwenden Sie das Symbol **Cache** im Verwaltungsportal, um alle Ihre Cache Service-Endpunkte anzuzeigen.

    ![Cache-Symbol](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  Anschließend können Sie einen der Cache Service-Endpunkte auswählen, um dessen Eigenschaften anzuzeigen. In den folgenden Abschnitten werden die Einstellungen aus der Registerkarte **Dashboard** verwendet, um Zwischenspeicherung für ein ASP.NET-Projekt zu konfigurieren.

Konfigurieren des ASP.NET-Projekts
----------------------------------

1.  Vergewissern Sie sich zuerst, dass Sie das neueste **Azure SDK für .NET** [installiert](http://www.windowsazure.com/de-de/downloads/?sdk=net) haben.

2.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das ASP.NET-Projekt, und wählen Sie dann **Manage NuGet Packages** aus. (Bei Verwendung von WebMatrix klicken Sie stattdessen in der Symbolleiste auf die Schaltfläche **NuGet**.)

3.  Geben Sie **WindowsAzure.Caching** in das Bearbeitungsfeld **Search Online** ein.

    ![Dialogfeld "NuGet"](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  Wählen Sie das Paket **Azure Caching** aus, und klicken Sie dann auf **Install**.

Ändern der Datei "web.config"
-----------------------------

Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei "web.config" ein. Um Cache für den Sitzungsstatus zu verwenden, müssen mehrere Änderungen an der Datei "web.config" vorgenommen werden.

1.  Öffnen Sie die Datei **web.config** für das ASP.NET-Projekt.

2.  Suchen Sie das vorhandene **sessionState**-Element und kommentieren Sie es aus (oder entfernen Sie es).

3.  Anschließend müssen Sie das Element **sessionState** auskommentieren, das vom Azure Caching NuGet-Paket hinzugefügt wurde. Das Endergebnis sollte in etwa wie folgender Screenshot aussehen:

    ![SessionStateConfig][SessionStateConfig]

4.  Suchen Sie nun den Abschnitt **dataCacheClients**. Kommentieren Sie das untergeordnete Element **securityProperties** aus.

    ![Cache-Konfiguration](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  Legen Sie im Element **autoDiscover** das Attribut **identifier** auf die Cache-Endpunkt-URL fest. Die Endpunkt-URL erfahren Sie in den Cache-Eigenschaften im Azure-Verwaltungsportal. Kopieren Sie auf der Registerkarte **Dashboard** den Wert für **ENDPOINT URL** in den Abschnitt **quick glance**.

    ![Endpunkt-URL](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  Legen Sie im Element **messageSecurity** das Attribut **authorizationInfo** auf den Cache-Zugriffsschlüssel fest. Den Zugriffsschlüssel für den Cache erfahren Sie im Azure-Verwaltungsportal. Klicken Sie anschließend auf der unteren Leiste auf **Manage Keys**. Klicken Sie neben dem Textfeld **PRIMARY ACCESS KEY** auf die Kopierschaltfläche.

    ![Schlüssel verwalten](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

Verwenden des Sitzungsobjekts im Code
-------------------------------------

Der letzte Schritt ist die Verwendung des Sitzungsobjekts im ASP.NET-Code. Fügen Sie Objekte mit der Methode **Session.Add** zum Sitzungsstatus hinzu. Diese Methode verwendet Schlüssel-Wert-Paare, um Elemente im Sitzungsstatus-Caqche zu speichern.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Der folgende Code ruft den Wert aus dem Sitzungsstatus ab.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Weitere Informationen zur Verwendung des ASP.NET-Sitzungsstatus finden Sie unter [Überblick über den ASP.NET-Sitzungsstatus](http://msdn.microsoft.com/de-de/library/ms178581.aspx).

