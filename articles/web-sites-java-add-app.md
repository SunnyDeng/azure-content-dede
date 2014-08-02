<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Hinzufügen einer Anwendung zu Ihrer Java-Website auf Azure
==========================================================

In diesem Lernprogramm erfahren Sie, wie Sie eine Webseite oder eine Anwendung zu einer Java-Website hinzufügen, die Sie zuvor mithilfe der Azure-Galerie oder der Azure-Konfigurations-UI erstellt haben.

Beachten Sie, dass Sie die Quellcodeverwaltung zum Hochladen Ihrer Anwendung oder Webseiten verwenden können, auch in Szenarien mit fortlaufender Integration. Anweisungen zur Verwendung der Quellcodeverwaltung mit Ihrer Website finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](../web-sites-publish-source-control). Alternativ zur Verwendung der Quellcodeverwaltung zeigt dieser Artikel, wie Sie die Anwendung mithilfe von FTP hochladen.

Dieses Lernprogramm setzt voraus, dass Sie die Schritte unter [Erste Schritte mit Microsoft Azure-Websites und Java](../web-sites-java-get-started) bereits abgeschlossen haben.

Anpassen einer Website mithilfe von FTP
=======================================

Sie müssen Ihre FTP-Anmeldeinformationen festlegen und diese verwenden, um auf die Website-Inhalte zuzugreifen. Dann können Sie die Inhalte ändern, um Ihre Anwendung auszuführen. In dem hier gezeigten Beispiel wird der Datei-Explorer für FTP verwendet, aber Sie können auch andere FTP-Techniken verwenden.

Verwenden der FTP-Anmeldeinformationen zum Zugriff auf die Inhalte Ihrer Website
--------------------------------------------------------------------------------

1.  Navigieren Sie im Microsoft Azure Verwaltungsportal zur Ansicht **Websites**.
2.  Klicken Sie in der Ansicht **Websites** auf den Namen Ihrer Website.
3.  Klicken Sie auf **Dashboard**.
4.  Klicken Sie in der Ansicht **Dashboard** unter **Schnellübersicht** auf **Veröffentlichungsprofil herunterladen**. Speichern Sie diese Datei lokal. Bewahren Sie diese Datei sicher auf, denn sie enthält den Benutzernamen und das Kennwort, das Sie für Veröffentlichungen auf Ihre Website und zum Kopieren von Inhalten von Ihrer Website benötigen.
5.  Öffnen Sie die heruntergeladene Datei mit den Veröffentlichungseinstellungen mithilfe eines Texteditors. Notieren Sie sich die Werte für **userName** und **userPwd** aus dieser Datei. Es handelt sich dabei um den Benutzernamen und das Kennwort, die Sie verwenden, um auf die Dateien auf der Website zuzugreifen.
6.  Rufen Sie die Dateien Ihrer Website auf, und geben Sie den Benutzernamen und das Kennwort ein, wenn Sie dazu aufgefordert werden. In diesem Beispiel wird über den Internet Explorer auf FTP zugegriffen, aber Sie können auch andere Techniken verwenden. Wenn Sie mithilfe des FTP fortfahren möchten, klicken Sie in der Ansicht **Dashboard** auf die unter **FTP-Hostname** aufgeführte URL. (Sie finden den FTP-Hostnamen auch in der Datei mit den Veröffentlichungseinstellungen. Es handelt sich dabei um den Wert **publishUrl**.)
7.  Wenn Sie aufgefordert werden, den Benutzernamen und das Kennwort einzugeben, verwenden Sie die Werte für **userName** und **userPwd** aus der Datei mit den Veröffentlichungseinstellungen.
8.  Wenn Sie im Internet Explorer zur Datei-Explorer-Ansicht wechseln möchten, klicken Sie auf **Ansicht** und dann auf **FTP-Site im Explorer öffnen**.

Zugriff auf den Ordner "webapps" für Ihre Website
-------------------------------------------------

In der Datei-Explorer-Ansicht Ihrer Website auf Azure können Sie nun Ihre Website anpassen. Sie müssen Ihre Anwendung in den Ordner **webapps** für Ihre Website kopieren. Der Navigationspfad für diese Ordner hängt davon ab, wie Sie Ihre Website eingerichtet haben.

-   Wenn Sie Ihre Website mithilfe der Azure-Anwendungsgalerie eingerichtet haben, doppelklicken Sie im Datei-Explorer auf **site**, **wwwroot**, **bin** und dann auf die Version des Anwendungsservers, den Ihre Website verwendet, und doppelklicken Sie schließlich auf **webapps**.
-   Wenn Sie Ihre Website mithilfe der Azure-Konfigurations-UI eingerichtet haben, doppelklicken Sie im Datei-Explorer auf **site**, **wwwroot** und dann auf **webapps**.
-   Wenn Sie Ihre Website mit einem benutzerdefinierten Upload erstellt haben, navigieren Sie entsprechend zum Ordner **webapps**.

Hinzufügen einer WAR-Datei zu Ihrer Website mithilfe von FTP
------------------------------------------------------------

1.  Navigieren Sie mit der für Ihre Website entsprechenden Technik zum Ordner **webapps**, wie oben beschrieben.
2.  Kopieren Sie Ihre WAR-Datei in den Ordner **webapps**.

Der Anwendungsserver wird erkennen, dass Sie die WAR-Datei hinzugefügt haben, und wird Sie automatisch laden. Dann können Sie Ihre App in Ihrem Browser ausführen. Verwenden Sie hierfür die URL für Ihre Website, und hängen Sie den Namen der WAR-Datei an deren Ende an.

Rufen Sie beispielsweise die URL http://*mysitename*.azurewebsites.net/*mywar* auf, wobei *mysitename* der Name ist, den Sie für Ihre URL festgelegt haben, und *mywar* der Name der von Ihnen kopierten WAR-Datei unter Berücksichtigung von Groß-/Kleinschreibung (ohne die Endung **.war**.

Hinzufügen einer Webseite zu Ihrer Website mithilfe von FTP
-----------------------------------------------------------

1.  Navigieren Sie zum Ordner **webapps**.
2.  Erstellen Sie im Ordner **webapps** einen neuen Ordner.
3.  Öffnen Sie den neuen Ordner.
4.  Fügen Sie Ihre Webseite zu dem neuen Ordner hinzu.

Der Anwendungsserver wird erkennen, dass Sie den neuen Ordner und die Webdatei hinzugefügt haben, und wird sie automatisch laden. Führen Sie dann Ihre JSP-Datei mit der URL in der Form http://*mysitename*.azurewebsites.net/*myfolder*/*myfile.jsp* aus, wobei *mysitename* der Name ist, den Sie für Ihre URL festgelegt haben, *myfolder* der Ordner, den Sie in **webapps** erstellt haben, und *myfile.jsp* der Name der von Ihnen erstellen JSP-Datei.

Beachten Sie, dass Sie beim Kopieren von Dateien in das ROOT-Verzeichnis den Anwendungsserver neu starten müssen, damit diese Dateien verwendet werden. Dies gilt nicht für WAR-Dateien. Die Funktion für das automatische Laden für Java-Websites, die auf Azure ausgeführt werden, basiert auf einer neuen WAR-Datei, die hinzugefügt wurde, oder neuen Dateien oder Verzeichnissen, die zum Ordner **webapps** hinzugefügt wurden.

