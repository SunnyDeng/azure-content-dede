Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, sollten Sie den Browser verwenden können, um zu überprüfen, dass Ihr benutzerdefinierter Domänenname für den Zugriff auf die Website verwendet werden kann.

> [WACOM.NOTE] Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Mithilfe eines Services wie <http://www.digwebinterface.com/> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

Wenn Sie Ihre Website noch nicht als Traffic Manager-Endpunkt hinzugefügt haben, müssen Sie dies durchführen, bevor die Namensauflösung funktionieren kann, da der benutzerdefinierte Domänenname an Traffic Manager geleitet wird. Traffic Manager führt Sie dann zu Ihrer Website. Verwenden Sie die Informationen unter [Hinzufügen und Löschen von Endpunkten](http://msdn.microsoft.com/en-us/library/windowsazure/hh744839.aspx), um dem Traffic Manager-Profil die Website als Endpunkt hinzuzufügen.

> [WACOM.NOTE] Wenn Ihre Website beim Hinzufügen eines Endpunkts nicht aufgeführt ist, überprüfen Sie, dass sie für den Standardmodus konfiguriert wurde. Sie müssen für Ihre Website den Standardmodus verwenden, damit Sie mit Traffic Manager arbeiten können.

