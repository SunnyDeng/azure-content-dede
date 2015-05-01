Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, sollten Sie den Browser verwenden können, um zu überprüfen, dass Ihr benutzerdefinierter Domänenname für den Zugriff auf die Web-App in Azure App Service verwendet werden kann.

> [AZURE.NOTE] Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Mithilfe eines Diensts wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

Wenn Sie Ihre Web-App noch nicht als Traffic Manager-Endpunkt hinzugefügt haben, müssen Sie dies durchführen, bevor die Namensauflösung funktionieren kann, da der benutzerdefinierte Domänenname an Traffic Manager geleitet wird. Traffic Manager leitet dann zu Ihrer Web-App weiter. Verwenden Sie die Informationen unter [Hinzufügen und Löschen von Endpunkten](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx), um dem Traffic Manager-Profil die Web-App als Endpunkt hinzuzufügen.

> [AZURE.NOTE] Wenn Ihre Web-App beim Hinzufügen eines Endpunkts nicht aufgeführt ist, überprüfen Sie, ob sie für den App Service-Planmodus **Standard** konfiguriert wurde. Sie müssen für Ihre Web-App den Modus **Standard** verwenden, damit sie mit Traffic Manager verwendet werden kann.

<!--HONumber=52--> 
