<properties 
	pageTitle="Notfallwiederherstellung mobiler Dienste - Azure Mobile Services" 
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
	ms.date="04/24/2015" 
	ms.author="christopheranderson"/>

# Notfallwiederherstellung mobiler Dienste

Wenn Sie Azure Mobile Services zum Bereitstellen einer App verwenden, verwenden Sie die integrierten Funktionen, um Geschäftskontinuität im Fall von Verfügbarkeitsproblemen, zum Beispiel Serverausfälle, Netzwerkunterbrechungen, Datenverlust und umfassender Funktionsverlust, sicherzustellen. Durch Bereitstellen Ihrer App mit Azure Mobile nutzen Sie viele Fehlertoleranz- und Infrastrukturfunktionen, die Sie andernfalls entwickeln, implementieren und verwalten müssen, wenn Sie eine herkömmliche Lösung am Standort bereitstellen. Azure minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

## <a name="prepare"></a>Vorbereitung auf mögliche Notfälle

Um die Wiederherstellung im Fall eines Verfügbarkeitsproblems zu vereinfachen, können Sie sich zuvor darauf vorbereiten:

+ **Sichern Sie Ihre Daten in der SQL-Datenbank des mobilen Azure-Dienstes** Ihre Mobildienst-Anwendungsdaten werden in einer Azure-SQL-Datenbank gespeichert. Wie empfehlen, diese Daten wie unter [SQL Database business continuity guidance] beschrieben zu sichern.
+ **Sichern Sie Ihre Skripts für mobile Dienste** Wir empfehlen, Ihre Skripts für mobile Dienste in einem Quellcodeverwaltungssystem wie [Team Foundation Service] oder [GitHub] zu speichern und sich nicht nur auf die Kopien im mobilen Dienst zu verlassen. Sie können die Skripts über das Azure-Portal mithilfe der [Quellcodeverwaltungsfunktion] oder dem [Azure-Befehlszeilentool] von Mobile Services herunterladen. Achten Sie auf Funktionen, die im Portal als "Vorschau" bezeichnet werden, da die Wiederherstellung dieser Skripts nicht garantiert werden kann und Sie sie möglicherweise aus Ihrer eigenen, ursprünglichen Quellcodeverwaltung wiederherstellen müssen.
+ **Reservieren Sie einen sekundären mobilen Dienst** Bei einem Verfügbarkeitsproblem mit Ihrem mobilen Dienst müssen Sie ihn möglicherweise in einer alternativen Azure-Region erneut bereitstellen. Um sicherzustellen, dass die Kapazität verfügbar ist (zum Beispiel in den seltenen Fällen des Ausfalls einer ganzen Region), sollten Sie einen sekundären mobilen Dienst in der alternativen Region erstellen und den Modus auf den gleichen oder einen höheren Modus wie beim primären Dienst festlegen. (Falls sich der primäre Dienst im Shared-Modus befindet, können Sie den sekundären Dienst auf den Shared- oder auf den Reserved-Modus festlegen. Falls sich der primäre Dienst aber im Reserved-Modus befindet, muss auch der sekundäre auf den Reserved-Modus festgelegt werden.)


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

   Falls Sie noch keinen sekundären mobilen Dienst reserviert haben, erstellen Sie jetzt einen in einer anderen Azure-Region. Legen Sie seinen Modus auf den gleichen oder einen höheren Modus wie beim primären Dienst fest. (Falls sich der primäre Dienst im Shared-Modus befindet, können Sie den sekundären Dienst auf den Shared- oder auf den Reserved-Modus festlegen. Falls sich der primäre Dienst aber im Reserved-Modus befindet, muss auch der sekundäre auf den Reserved-Modus festgelegt werden.)

3. Konfigurieren Sie die Azure-Befehlszeilentools für die Verwendung mit Ihrem Abonnement, wie in [Automatisieren von mobilen Diensten mit Befehlszeilentools] beschrieben wird.

4. Jetzt können Sie den sekundären Dienst verwenden, um den primären wiederherzustellen.

    > [AZURE.IMPORTANT] Wenn Sie den Befehl in diesem Schritt ausführen, wird der sekundäre Dienst gelöscht, sodass seine Kapazität verwendet werden kann, um den primären Dienst wiederherzustellen. Wir empfehlen, Ihre Skripts und Einstellungen vor dem Ausführen des Befehls zu sichern, wenn Sie sie behalten möchten.
    
   Führen Sie diesen Befehl aus, wenn Sie bereit sind:

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] Es kann nach Abschluss des Befehls einige Minuten dauern, bis die Änderungen im Portal angezeigt werden.

5. Überprüfen Sie, ob alle Skripts korrekt wiederhergestellt wurden, indem Sie sie mit den Originalen in der Quellcodeverwaltung vergleichen. In den meisten Fällen werden Skripts automatisch ohne Datenverlust wiederhergestellt. Falls Sie aber eine Diskrepanz finden, können Sie das entsprechende Skript manuell wiederherstellen.

6. Stellen Sie sicher, dass der wiederhergestellte Dienst mit der Azure-SQL-Datenbank kommuniziert. Der Wiederherstellungsbefehl stellt den mobilen Dienst wieder her, behält aber die Verbindung zur ursprünglichen Datenbank bei. Falls sich das Problem in der primären Azure-Region auch auf die Datenbank auswirkt, funktioniert der wiederhergestellte Dienst möglicherweise nicht ordnungsgemäß. Sie können das Azure-Dienstdashboard verwenden, um den Datenbankstatus für eine bestimmte Region zu untersuchen. Falls die ursprüngliche Datenbank nicht funktioniert, können Sie sie wiederherstellen:
	+ Stellen Sie die Azure-SQL-Datenbank in der Azure-Region wieder her, in der Sie gerade den mobilen Dienst wiederhergestellt haben, wie in [SQL Database business continuity guidance] beschrieben wird.
	+ Wählen Sie im Azure-Portal auf der Registerkarte **Konfigurieren** des mobilen Dienstes die Option "Change Database" und dann die neu wiederhergestellte Datenbank aus.

Jetzt sollte der mobile Dienst in einer neuen Azure-Region wiederhergestellt worden sein und verarbeitet nun Datenverkehr von den Store-Apps mithilfe der ursprünglichen URL.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[SQL Database business continuity guidance]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[Quellcodeverwaltungsfunktion]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[Azure-Befehlszeilentool]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Azure-Portal]: http://manage.windowsazure.com/
[Azure-Dienstdashboard]: http://www.windowsazure.com/support/service-dashboard/
[Automatisieren von mobilen Diensten mit Befehlszeilentools]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!--HONumber=54--> 