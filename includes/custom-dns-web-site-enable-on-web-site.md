Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, müssen Sie sie Ihrer Web-App zuweisen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [AZURE.NOTE] Es kann einige Zeit dauern, bis die CNAME-Einträge, die in den vorherigen Schritten erstellt wurden, über das DNS-System weitergegeben werden. Sie können den Domänennamen Ihrer Web-App erst dann hinzufügen, nachdem CNAME weitergegeben wurde. Wenn Sie einen A-Datensatz verwenden, können Sie den Domänennamen des A-Datensatzes erst dann der Web-App hinzufügen, wenn der im vorigen Schritt erstellte CNAME **awverify** weitergegeben wurde.
>
> Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.

1. Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com).

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Klicken Sie auf dem Blatt **Benutzerdefinierte Domänen und SSL** auf **Externe Domänen verwenden**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um die Domänennamen einzugeben, die dieser Web-App zugewiesen werden sollen.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Klicken Sie auf **Speichern**, um die Domänennamenkonfiguration zu speichern.

	Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Abschnitt **Hostnamenbindungen** Ihrer Web-App aufgeführt.

Jetzt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und und auf diese Weise erfolgreich zu Ihrer Web-App gelangen.

<!---HONumber=AcomDC_0224_2016-->