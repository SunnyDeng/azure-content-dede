<properties
	pageTitle="Notfallwiederherstellung mobiler Dienste | Microsoft Azure"
	description="Erfahren Sie, wie Sie Ihren mobilen Diensts nach einem Notfall wiederherstellen können."
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/08/2015"
	ms.author="christopheranderson"/>

# Notfallwiederherstellung mobiler Dienste

Wenn Sie Azure Mobile Services zum Bereitstellen einer App verwenden, verwenden Sie die integrierten Funktionen, um Geschäftskontinuität im Fall von Verfügbarkeitsproblemen, zum Beispiel Serverausfälle, Netzwerkunterbrechungen, Datenverlust und umfassender Funktionsverlust, sicherzustellen. Durch Bereitstellen Ihrer App mit Azure Mobile nutzen Sie viele Fehlertoleranz- und Infrastrukturfunktionen, die Sie andernfalls entwickeln, implementieren und verwalten müssen, wenn Sie eine herkömmliche Lösung am Standort bereitstellen. Azure minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

## <a name="prepare"></a>Vorbereitung auf mögliche Notfälle

Um die Wiederherstellung im Fall eines Verfügbarkeitsproblems zu vereinfachen, können Sie sich zuvor darauf vorbereiten:

+ **Sichern Sie Ihre Daten in der SQL-Datenbank des mobilen Azure-Dienstes** Ihre Mobildienst-Anwendungsdaten werden in einer Azure-SQL-Datenbank gespeichert. Wie empfehlen, diese Daten wie unter [SQL Database business continuity guidance] beschrieben zu sichern.
+ **Sichern Sie Ihre Skripts für mobile Dienste**. Wir empfehlen, Ihre Skripts für mobile Dienste in einem Quellcodeverwaltungssystem wie [Team Foundation Service] oder [GitHub] zu speichern und sich nicht nur auf die Kopien im mobilen Dienst zu verlassen. Sie können die Skripts über das Azure-Portal mithilfe der [Quellcodeverwaltungsfunktion] oder der [Azure-Befehlszeilenschnittstelle] von Mobile Services herunterladen. Achten Sie auf Funktionen, die im Portal als "Vorschau" bezeichnet werden, da die Wiederherstellung dieser Skripts nicht garantiert werden kann und Sie sie möglicherweise aus Ihrer eigenen, ursprünglichen Quellcodeverwaltung wiederherstellen müssen.
+ **Reservieren Sie einen sekundären mobilen Dienst** Bei einem Verfügbarkeitsproblem mit Ihrem mobilen Dienst müssen Sie ihn möglicherweise in einer alternativen Azure-Region erneut bereitstellen. Um sicherzustellen, dass die Kapazität verfügbar ist (zum Beispiel in den seltenen Fällen des Ausfalls einer ganzen Region), sollten Sie einen sekundären mobilen Dienst in der alternativen Region erstellen und den Modus auf den gleichen oder einen höheren Modus wie beim primären Dienst festlegen. (Falls sich der primäre Dienst im Basic-Modus befindet, können Sie den sekundären Dienst auf den Basic- oder auf den Standard-Modus festlegen. Falls sich der primäre Dienst aber im Standard-Modus befindet, muss auch der sekundäre auf den Standard-Modus festgelegt werden.)

## <a name="watch"></a>Suchen nach Anzeichen für ein Problem

Diese Umstände weisen auf ein Problem hin, das eine Wiederherstellung erfordern könnte:

+ Apps, die mit dem mobilen Dienst verbunden sind, können über einen längeren Zeitraum nicht mit dem Dienst kommunizieren.
+ Der Status des mobilen Dienstes wird als **Unhealthy** im [Azure-Portal] angezeigt.
+ Ein **Unhealthy**-Banner wird oben auf jeder Registerkarte des mobilen Dienstes im Azure-Portal eingeblendet, und Verwaltungsvorgänge führen zu Fehlermeldungen.
+ Das [Azure-Dienstdashboard] weist auf ein Verfügbarkeitsproblem hin.

## <a name="recover"></a>Notfallwiederherstellung

Wenn ein Problem auftritt, erhalten Sie im Dienstdashboard Hilfe und Aktualisierungen.

Wenn Sie vom Dienstdashboard dazu aufgefordert werden, führen Sie die folgenden Schritte aus, um den mobilen Dienst in einer alternativen Azure-Region wieder in den aktiven Status zu versetzen. Wenn Sie zuvor einen sekundären Dienst erstellt haben, wird seine Kapazität verwendet, um den primären Dienst wiederherzustellen. Da die URL und der Anwendungsschlüssel des primären Dienstes nach der Wiederherstellung unverändert ist, müssen Sie keine Apps aktualisieren, die darauf verweisen.

So stellen Sie die mobilen Dienste nach einem Ausfall wieder her:

1. Stellen Sie im Azure-Portal sicher, dass der Status des Dienstes als **Unhealthy** angezeigt wird.

2. Falls Sie bereits einen sekundären mobilen Dienst reserviert haben, können Sie diesen Schritt überspringen.

   Falls Sie noch keinen sekundären mobilen Dienst reserviert haben, erstellen Sie jetzt einen in einer anderen Azure-Region. Legen Sie den Skalierungsmodus auf den gleichen Modus wie den primären Dienst fest.

3. Konfigurieren Sie die Azure-Befehlszeilenschnittstelle (Azure-CLI) für die Verwendung mit Ihrem Abonnement, wie in [Automatisieren von mobilen Diensten mit Befehlszeilentools] beschrieben.

4. Jetzt können Sie den sekundären Dienst verwenden, um den primären wiederherzustellen.

	> [AZURE.IMPORTANT]Zusätzlich zum Migrieren der Dateien aktualisiert der Migrationsbefehl auch den Hostnamen des primären Diensts, um auf den sekundären Dienst zu weisen, sodass Clientanwendungen nicht aktualisiert werden müssen. Allerdings dauert es bis zu 30 Minuten, bis der Hostname in den neuen Dienst aufgelöst wird. Aus diesem Grund empfiehlt es sich, den Migrationsbefehl nur zur Wiederherstellung im Notfall zu verwenden.

	> [AZURE.IMPORTANT]Wenn Sie den Befehl in diesem Schritt ausführen, wird der sekundäre Dienst gelöscht, sodass seine Kapazität verwendet werden kann, um den primären Dienst wiederherzustellen. Wir empfehlen, Ihre Skripts und Einstellungen vor dem Ausführen des Befehls zu sichern, wenn Sie sie behalten möchten.

	Führen Sie diesen Befehl aus, wenn Sie bereit sind:

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE]Es kann nach Abschluss des Befehls einige Minuten dauern, bis die Änderungen im Portal angezeigt werden.

5. Überprüfen Sie, ob alle Skripts korrekt wiederhergestellt wurden, indem Sie sie mit den Originalen in der Quellcodeverwaltung vergleichen. In den meisten Fällen werden Skripts automatisch ohne Datenverlust wiederhergestellt. Falls Sie aber eine Diskrepanz finden, können Sie das entsprechende Skript manuell wiederherstellen.

6. Stellen Sie sicher, dass der wiederhergestellte Dienst mit der Azure-SQL-Datenbank kommuniziert. Der Wiederherstellungsbefehl stellt den mobilen Dienst wieder her, behält aber die Verbindung zur ursprünglichen Datenbank bei. Falls sich das Problem in der primären Azure-Region auch auf die Datenbank auswirkt, funktioniert der wiederhergestellte Dienst möglicherweise nicht ordnungsgemäß. Sie können das Azure-Dienstdashboard verwenden, um den Datenbankstatus für eine bestimmte Region zu untersuchen. Falls die ursprüngliche Datenbank nicht funktioniert, können Sie sie wiederherstellen:
	+ Stellen Sie die Azure-SQL-Datenbank in der Azure-Region wieder her, in der Sie gerade den mobilen Dienst wiederhergestellt haben, wie in [SQL Database business continuity guidance] beschrieben wird.
	+ Wählen Sie im Azure-Portal auf der Registerkarte **Konfigurieren** des mobilen Dienstes die Option "Change Database" und dann die neu wiederhergestellte Datenbank aus.

7. Der mobile Dienst wird jetzt an einem anderen physischen Standort gehostet. Sie müssen Ihre Veröffentlichungs- und/oder Git-Anmeldeinformationen aktualisieren, um das Update der laufenden Site zu ermöglichen.
	+ Bei Verwendung eines **.NET-Back-Ends** richten Sie Ihr Veröffentlichungsprofil erneut ein, wie unter [Veröffentlichen des mobilen Diensts](mobile-services-dotnet-backend-windows-store-dotnet-get-started/#publish-your-mobile-service) beschrieben. Dadurch werden Ihre Veröffentlichungsdetails aktualisiert, um auf den Speicherort zu verweisen.
	+ Bei Verwendung eines **Javascript-Back-Ends** und bei Verwaltung des Diensts mit dem Portal müssen Sie keine zusätzlichen Maßnahmen ergreifen.
	+ Bei Verwendung eines **Javascript-Back-Ends** und der Verwaltung des Diensts mit dem Knoten aktualisieren Sie Ihr Git-Remote, sodass es auf das neue Repository verweist. Entfernen Sie dazu den Dateipfad ".git" aus dem Git-Remote:

		1. Suchen des aktuellen Ursprungs-Remotes: Git remote: git remote -v origin https://myservice.scm.azure-mobile.net/myservice.git (fetch) origin https://myservice.scm.azure-mobile.net/myservice.git (push)
		3. Aktualisieren Sie das Remote mit der gleichen URL, jedoch ohne den endgültigen .git-Dateipfad: git remote set-url origin https://myservice.scm.azure-mobile.net
		4. Führen Sie einen Abruf aus dem Ursprung durch, um sicherzustellen, dass dies ordnungsgemäß funktioniert.

Jetzt sollte der mobile Dienst in einer neuen Azure-Region wiederhergestellt worden sein und verarbeitet nun Datenverkehr von den Store-Apps mithilfe der ursprünglichen URL.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[SQL Database business continuity guidance]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[Quellcodeverwaltungsfunktion]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[Azure-Befehlszeilenschnittstelle]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Azure-Portal]: http://manage.windowsazure.com/
[Azure-Dienstdashboard]: http://www.windowsazure.com/support/service-dashboard/
[Automatisieren von mobilen Diensten mit Befehlszeilentools]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
 

<!---HONumber=August15_HO8-->