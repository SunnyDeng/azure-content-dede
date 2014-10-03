Nachdem die Einträge für Ihren Domänennamen propagiert wurden, müssen Sie sie Ihrer Website zuordnen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [WACOM.NOTE] Es kann einige Zeit dauern, bis die CNAME-Einträge, die in den vorherigen Schritten erstellt wurden, über das DNS-System weitergegeben wurden. Sie können den Domänennamen erst Ihrer Azure-Website hinzufügen, nachdem der CNAME-Eintrag propagiert wurde. Wenn Sie einen A-Eintrag verwenden, können Sie den A-Eintrag-Domänennamen der Azure-Website erst hinzufügen, nachdem der im vorigen Schritt erstellte CNAME-Eintrag **awverify** weitergegeben wurde.
>
> Mithilfe eines Services wie <http://www.digwebinterface.com/> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

1.  Öffnen Sie das [Azure-Verwaltungsportal][] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Seitenrand **Domänen verwalten** aus.

    ![][]

3.  Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um die Domänennamen einzugeben, die dieser Website zugewiesen werden sollen.

    ![][1]

4.  Klicken Sie auf das Kontrollkästchen in der rechten unteren Ecke, um die Domänennamenkonfiguration zu speichern.

    Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname im Abschnitt **Domänennamen** der Seite **Konfigurieren** Ihrer Website.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und sehen, dass er Sie erfolgreich zu Ihrer Azure-Website bringt.

  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-7.png
