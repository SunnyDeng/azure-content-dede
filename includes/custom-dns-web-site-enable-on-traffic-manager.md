Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, sollten Sie den Browser verwenden können, um zu überprüfen, dass Ihr benutzerdefinierter Domänenname für den Zugriff auf die Web-App in Azure App Service verwendet werden kann.

> [AZURE.NOTE]Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Mithilfe eines Diensts wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.

Wenn Sie Ihre Web-App noch nicht als Traffic Manager-Endpunkt hinzugefügt haben, müssen Sie dies durchführen, bevor die Namensauflösung funktionieren kann, da der benutzerdefinierte Domänenname an Traffic Manager geleitet wird. Traffic Manager leitet dann zu Ihrer Web-App weiter. Verwenden Sie die Informationen unter [Hinzufügen und Löschen von Endpunkten](../traffic-manager/traffic-manager-endpoints.md), um dem Traffic Manager-Profil die Web-App als Endpunkt hinzuzufügen.

> [AZURE.NOTE]Wenn Ihre Web-App beim Hinzufügen eines Endpunkts nicht aufgeführt ist, überprüfen Sie, ob sie für den App Service-Planmodus **Standard** konfiguriert wurde. Sie müssen für Ihre Web-App den Modus **Standard** verwenden, damit sie mit Traffic Manager verwendet werden kann.

1. Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com).

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Klicken Sie auf dem Blatt **Benutzerdefinierte Domänen und SSL** auf **Externe Domänen verwenden**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Verwenden Sie die Textfelder **DOMÄNENNAMEN**, um den Traffic Manager-Domänennamen (contoso.trafficmanager.net) dieser Web-App zuzuordnen.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Klicken Sie auf **Speichern**, um die Domänennamenkonfiguration zu speichern.

	Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Abschnitt **Hostnamenbindungen** Ihrer Web-App aufgeführt.

Jetzt sollten Sie den Traffic Manager-Domänennamen (contoso.trafficmanager.net) in Ihren Browser eingeben können und auf diese Weise erfolgreich zu Ihrer Web-App gelangen.

<!---HONumber=Oct15_HO3-->