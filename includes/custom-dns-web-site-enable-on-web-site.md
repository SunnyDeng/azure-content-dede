Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, müssen Sie sie Ihrer Website zuweisen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [WACOM.NOTE] Es kann einige Zeit dauern, bis die CNAME-Datensätzen, die in den vorherigen Schritten erstellt wurden, über das DNS-System weitergegeben wurden. Sie können den Domänennamen Ihrer Azure-Website erst hinzufügen, nachdem CNAME weitergegeben wurde. Wenn Sie einen A-Datensatz verwenden, können Sie den A-Datensatz-Domänennamen der Azure-Website erst hinzufügen, nachdem die CNAME-Datensätze **awverify.www** oder **www**, die im vorherigen Schritt erstellt wurden, weitergegeben wurden.
>
> Mithilfe eines Services wie <http://www.digwebinterface.com/> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

1.  Öffnen Sie das [Azure Verwaltungsportal](https://manage.windowsazure.com) in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website, wählen Sie dann **Dashboard** und unten auf der Seite **Domänen verwalten** aus.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um die Domänennamen einzugeben, die dieser Website zugewiesen werden sollen.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Klicken Sie auf das Kontrollkästchen in der rechten unteren Ecke, um die Domänennamenkonfiguration zu speichern.

    Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Bereich der Domänennamen der Konfigurationsseite Ihrer Website angezeigt.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können, der Sie erfolgreich zu Ihrer Azure-Website bringt.

