Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, müssen Sie sie Ihrer Web-App zuweisen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [AZURE.NOTE] Es kann einige Zeit dauern, bis die CNAME-Einträge, die in den vorherigen Schritten erstellt wurden, über das DNS-System weitergegeben werden. Sie können den Domänennamen erst Ihrer Web-App hinzufügen, nachdem der CNAME-Eintrag weitergegeben wurde. Wenn Sie einen A-Datensatz verwenden, können Sie den Domänennamen des A-Datensatzes erst dann der Azure-Website hinzufügen, wenn der im vorigen Schritt erstellte CNAME-Eintrag **awverify** weitergegeben wurde.
> 
> Mithilfe eines Diensts wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Ihrem Browser.

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Custom domains and SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um die Domänennamen einzugeben, die dieser Web-App zugewiesen werden sollen. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Klicken Sie auf das Kontrollkästchen in der rechten unteren Ecke, um die Domänennamenkonfiguration zu speichern.

	Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname im Abschnitt **Domänennamen** des Blatts **Custom domains and SSL** für Ihre Web-App aufgeführt.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und sehen, dass er Sie erfolgreich zu Ihrer Web-App bringt. 

<!--HONumber=49-->