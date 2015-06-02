Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, müssen Sie sie Ihrer Web-App zuweisen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [AZURE.NOTE]Es kann einige Zeit dauern, bis die CNAME-Einträge, die in den vorherigen Schritten erstellt wurden, über das DNS-System weitergegeben werden. Sie können den Domänennamen Ihrer Web-App erst dann hinzufügen, nachdem CNAME weitergegeben wurde. Wenn Sie einen A-Datensatz verwenden, können Sie den Domänennamen des A-Datensatzes erst dann der Web-App hinzufügen, wenn der im vorigen Schritt erstellte CNAME **awverify** weitergegeben wurde.
> 
> Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Ihrem Browser.

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um die Domänennamen einzugeben, die dieser Web-App zugewiesen werden sollen.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Klicken Sie auf das Kontrollkästchen in der rechten unteren Ecke, um die Domänennamenkonfiguration zu speichern.

	Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname im Abschnitt **Domänennamen** des Blatts **Benutzerdefinierte Domänen und SSL** für Ihre Web-App aufgeführt.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können, sodass Sie erfolgreich zu Ihrer Web-App wechseln können.
<!--HONumber=54-->