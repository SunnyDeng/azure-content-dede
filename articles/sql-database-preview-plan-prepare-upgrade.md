<properties 
	pageTitle="Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12" 
	description="Beschreibt die Vorbereitungen und Einschränkungen im Zusammenhang mit einem Upgrade auf die Azure SQL-Datenbank V12." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


#Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12


<!--
GeneMi, 2015-March-05 Thursday 20:06pm
Entfernen Sie 'Azure' aus dem Titel.
-->


In diesem Thema werden die Planung und die Vorbereitungen beschrieben, die Sie ausführen müssen, um das Upgrade Ihrer Azure SQL-Datenbanken von Version V11 auf V12 vorzunehmen.


Es ist ein neues [Azure-Portal](http://portal.azure.com/) verfügbar, um Ihr Upgrade auf V12 zu unterstützten.


> [AZURE.NOTE]
> Testdatabanken, Datenbankkopien oder neue Datenbanken eignen sich gut für das Upgrade auf die Preview. Upgrades für geschäftsrelevante Produktionsdatenbanken sollten wie üblich erst nach der Previewphase durchgeführt werden.<br/><br/>
> Für die V12-Versionsfamilie können Sie anhand der Regionstabelle in [Neuerungen in SQL-Datenbank V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable) feststellen, ob Ihre geografische Region den Preview- oder GA-Status aufweist.


Die folgende Tabelle führt weitere Hilfethemen für V12 auf und beschreibt diese.


| Titel und Link | Beschreibung des Inhalts |
| :--- | :--- |
| [Neuerungen in SQL-Datenbank V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Beschreibt die Details, wie V12 die Azure SQL-Datenbank der vollständigen Funktionalität von Microsoft SQL Server 2014 weiter annähert. |
| [Exemplarische Vorgehensweise: Registrierung für das aktuelle SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Beschreibt die Schritte, die ausgeführt werden müssen, wenn ein Upgrade Ihrer Azure SQL-Datenbank auf die aktuelle Preview ausgeführt werden soll. |
| [Erstellen einer Datenbank im aktuellen SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Beschreibt, wie eine neue Azure SQL-Datenbank erstellt werden kann, die die neuen Funktionen der aktuellen Preview umfasst. In diesem Thema werden die verschiedenen Optionen beschrieben, die über eine leere Datenbank hinausgehen. |
| [Upgrade auf das aktuelle SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Beschreibt, wie Sie ein Upgrade Ihrer Azure SQL-Datenbanken auf die aktuelle Preview ausführen können. Es wird darauf hingewiesen, dass Sie zuerst eine Sicherung Ihrer Datenbank erstellen sollten, weil das Upgrade dauerhaft ist und nicht rückgängig gemacht werden kann. |


##A. Vorausplanung


In den folgenden Unterabschnitten werden die Lern- und Entscheidungsvorgänge beschrieben, die berücksichtigt werden müssen, bevor Aktionen für ein Upgrade Ihrer Azure SQL-Datenbank auf V12 ausgeführt werden.

###A.1 Versionsabklärung


Dieses Dokument beschäftigt sich mit dem Upgrade von Microsoft Azure SQL-Datenbank aus der Version V11 auf V12. Formal ähneln die Versionsnummern den folgenden beiden Werten, die mithilfe der Transact-SQL-Anweisung **SELECT @@version;** abgerufen werden:


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(oder etwas höher, V12)*


###A.2 Planung der Dienstebene


Ab V12 unterstützt die Azure SQL-Datenbank nur die Dienstebenen namens Basic, Standard und Premium. Die Web- und Business-Dienstebene wird unter V12 nicht unterstützt. Wenn Sie ein Upgrade Ihrer Azure SQL-Datenbank planen, die zurzeit die Web- oder Business-Dienstebene verwendet, müssen Sie daher entscheiden, welche neue Dienstebene genutzt werden soll.


Ausführliche Informationen zu den Dienstebenen Basic, Standard und Premium finden Sie hier:


- [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Preise für die Azure SQL-Datenbank](http://azure.microsoft.com/pricing/details/sql-database/)


###A.3 Überprüfen der Georeplikationskonfiguration


Wenn Ihre Azure SQL-Datenbank für Georeplikation konfiguriert ist, sollten Sie ihre aktuelle Konfiguration dokumentieren und die Georeplikation dann beenden, bevor Sie mit den Vorbereitungsaktionen für das Upgrade beginnen. Nachdem das Upgrade abgeschlossen ist, müssen Sie Ihre Datenbank erneut für Georeplikation konfigurieren.


Die Strategie besteht darin, die Quelle intakt zu lassen und den Test mit einer Kopie der Datenbank auszuführen.


###A.4 Direktes Upgrade im Vergleich zum Kopieren der Datenbank auf einen neuen Server


Sie haben zwei Möglichkeiten für das Upgrade von Azure SQL-Datenbank von V11 auf 12:


- Direktes Upgrade: Nachdem dieser Vorgang abgeschlossen ist, ist Ihre V11-Datenbank nicht mehr vorhanden. Der SQL-Datenbankserver verwendet V12.
- Kopieren der Datenbank auf den V12-Server: Bei diesem Vorgang bleibt Ihre V11-Datenbank unverändert, und es wird eine aktualisierte Kopie Ihrer V11-Datenbank auf einem V12 Azure SQL-Datenbankserver erstellt. <br/> Beachten Sie, dass die DNS-Informationen, die von Ihrem Clientcode für den Zugriff auf die Datenbank verwendet werden, nicht mehr auf die V11-Datenbank zugreifen, und Bearbeitungen der DNS-Informationen wären erforderlich, um auf die V11-Datenbank zuzugreifen.


##B. Vorbereitungsmaßnahmen


Nachdem Sie Ihre Planung abgeschlossen haben, können Sie die Aktionsschritte ausführen, die in den folgenden Unterabschnitten beschrieben werden, um die endgültige Upgradephase vorzubereiten.


Ausführliche Beschreibungen der endgültigen Upgradephase sind in den Hilfethemen verfügbar, auf die die Links oben in diesem Hilfethema verweisen.


###B.1 Dienstebenenaktionen


Die Preisstufe des Web- und Business-Diensts wird unter V12 nicht unterstützt.


Wenn Ihre V11 Azure SQL-Datenbank eine Web- oder Business-Datenbank ist, bietet Ihnen der Upgradevorgang an, Ihre Datenbank in eine unterstützte Ebene umzuschalten. Das Upgrade empfiehlt eine Ebene, die dem Arbeitsauslastungsverlauf Ihrer Datenbank entspricht. Sie können jedoch jede gewünschte Ebene auswählen, die unterstützt wird.


Sie können die Schritte verringern, die während des Upgrades erforderlich sind, indem Sie Ihre V11-Datenbank von der Web- und Business-Ebene abkoppeln, bevor Sie mit dem Upgrade beginnen. Dies kann mithilfe des Azure-Portals erfolgen.


Wenn Sie sich nicht sicher sind, in welche Dienstebene Sie wechseln sollten, ist die S0-Stufe der Standardebene ggf. eine vernünftige Wahl für den Anfang.


###B.2 Anhalten der Georeplikation während des Upgrades


Das Upgrade auf V12 kann nicht ausgeführt werden, wenn Georeplikation für Ihre Datenbank aktiv ist. Sie müssen Ihre Datenbank zuvor so konfigurieren, dass die Georeplikation nicht mehr verwendet wird.


Nachdem das Upgrade abgeschlossen ist, können Sie Ihre Datenbank erneut für die Verwendung von Georeplikation konfigurieren.


##<a id="limitations"></a>C. Einschränkungen während des Upgrades und nach dem Upgrade auf V12


In diesem Abschnitt werden die Einschränkungen in Verbindung mit dem Upgrade auf Azure SQL-Datenbank V12 beschrieben.


###C.1 Vorschauportal für V12


Nur das erste der folgenden beiden Azure-Verwaltungsportale unterstützt V12-Datenbanken:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Dieses neuere Portal weist noch den Vorschaustatus auf und hat noch nicht das Stadium der allgemeinen Verfügbarkeit erreicht.<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Dieses ältere Portal wird nicht hinsichtlich der Unterstützung von V12 aktualisiert.


Wir empfehlen Kunden, die Verbindung zu ihren Azure SQL-Datenbanken mit Visual Studio 2013 (VS2013) herzustellen. VS2013 kann für folgende Aufgaben verwendet werden:


- Ausführen einer T-SQL-Anweisung
- Entwerfen eines Schemas
- Entwickeln einer Datenbank, entweder online oder offline


Sie können die Verbindung stattdessen mit [Visual Studio Community 2013](https://www.visualstudio.com/de-de/news/vs2013-community-vs.aspx/) herstellen, bei dem es sich um eine kostenlose, voll funktionsfähige Version von VS2013 handelt.


Im älteren Azure-Verwaltungsportal können Sie auf der Datenbankseite auf **In Visual Studio öffnen** klicken, um VS2013 auf Ihrem Computer zu starten, damit die Verbindung mit Azure SQL-Datenbank hergestellt wird.


Als andere Alternative können Sie SQL Server Management Studio (SSMS) 2014 mit [CU6](http://support.microsoft.com/kb/3031047/) verwenden, um die Verbindung zu Azure SQL-Datenbank herzustellen. Weitere Informationen finden Sie in folgendem Blogbeitrag:<br/>[Updates für Clienttools für Azure SQL-Datenbank](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


###C.2 Einschränkung *during* des Upgrades auf V12

In der folgenden Tabelle werden die Einschränkungen beschrieben, die während des Upgradevorgangs gelten.


| Einschränkung | Beschreibung |
| :--- | :--- |
| Dauer des Upgrades | Für eine sehr große Datenbank (größer als ungefähr 50 GB) kann der Upgradevorgang bis zu 24 Stunden dauern. |
| Verzögerung bei der Aktualisierung des DNS-Eintrags | Nachdem das Upgrade abgeschlossen ist, dauert es mehrere Minuten, bis das System den DNS-Eintrag für Ihre V12-Datenbank für die Verbindung von Ihrer Clientanwendung aktualisiert. |
| Keine Datenbankerstellung | Während das Upgrade ausgeführt wird, sind die folgenden Aktionen zum Erstellen einer Datenbank auf dem V12 Azure SQL-Zieldatenbankserver nicht verfügbar: <p></p> * Erstellen einer neuen Datenbank <br/> * Kopieren einer Datenbank auf den Server <br/> * Wiederherstellen einer gelöschten Datenbank <br/> * Wiederherstellen einer Datenbank zu einem bestimmten Zeitpunkt <br/><br/> Unterstützung für die Wiederherstellung einer Datenbank zu einem bestimmten Zeitpunkt während des Upgrades ist jedoch eine Funktion, die ggf. vor dem Ende des Zeitraums der V12 Preview unterstützt wird. |
| Keine Georeplikation | Georeplikation wird auf einem V12-Server nicht unterstützt, für den aktuell ein Upgrade aus V11 ausgeführt wird. |
| Warnungsregeln | Einer V12-Datenbank können keine Warnungsregeln hinzugefügt werden. |


###C.3 Einschränkung *after* dem Upgrade auf V12

In der folgenden Tabelle werden die Einschränkungen beschrieben, die nach dem Upgradevorgang gelten.


| Einschränkung | Beschreibung |
| :--- | :--- |
| DNS für V11-Datenbank | Nach dem Abschluss eines Upgrades durch Kopieren der V11-Datenbank auf einen neuen V12-Server beziehen sich die DNS-Informationen, die Ihr Clientcode zum Herstellen einer Verbindung verwendet, auf die V12-Datenbank. <p> </p> Ihr Clientcode muss bearbeitete DNS-Informationen verwenden, um erneut auf die V11-Datenbank zu verweisen. |
| Keine Zurücksetzung auf V11 | Nachdem das Upgrade abgeschlossen wurde, kann das Ergebnis nicht mehr zurückgesetzt oder rückgängig gemacht werden. |
| Web- oder Business-Ebene | Sobald das Upgrade gestartet wurde, kann der Server für die neue V12-Datenbank die Web- oder Business-Dienstebene nicht mehr erkennen oder akzeptieren. |
| Der 50 %ige Rabatt ist aus den im Azure-Portal angegebenen Preisstufen nicht ersichtlich | Im Zeitraum der Preview gilt für Datenbanken, die für die aktuelle Preview des Azure SQL-Datenbankupdates (V12) registriert sind, ein Previewrabatt von 50 %. Auch wenn der Rabatt nicht im Preview-Portal im Bereich "Dienstpreisstufe" angezeigt wird, gilt er dennoch.<br/><br/> Der 50 %ige Rabatt ist in allen geografischen Regionen bis zum **31. März 2015** gültig. Danach läuft er für alle Regionen ab. Der Rabatt ist auch in Regionen wirksam, für die der GA-Status (General Availability, allgemeine Verfügbarkeit) bekannt gegeben wurde.<br/><br/> (*Die Nutzung des neuesten Azure SQL-Datenbankupdates V12 unterliegt den Bestimmungen in Ihrem Preview-Lizenzvertrag (z. B. Enterprise Agreement, Microsoft Azure-Vertrag oder Microsoft Online-Abonnementvertrag) sowie etwaigen gültigen [ergänzenden Nutzungsbedingungen für Microsoft Azure Previews](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).  Für die Dauer der Preview berechnet Microsoft alle für diese Preview registrierten Datenbanken zur Hälfte des GA (General Availability)-Tarifs und gewährt Ihnen (oder ggf. Ihrem Händler) einen 50 %igen Rabatt. 30 Tage vor Ablauf des Previewzeitraums und des reduzierten Tarifs werden Sie von Microsoft per E-Mail benachrichtigt.) |


###C.4 Exportieren und Importieren *after* dem Upgrade auf V12


Sie können eine V12-Datenbank mithilfe des [Azure-Webportals](http://portal.azure.com/) exportieren oder importieren. Alternativ können Sie den Export oder Import mithilfe der folgenden Tools ausführen:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Zur Verwendung der Tools müssen Sie allerdings zuerst die aktuellen Updates installieren, um sicherzustellen, dass die neuen V12-Funktionen unterstützt werden:


- [Kumulatives Update 6 für SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Update von Februar 2015: SQL Server-Datenbanktools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Februar 2015: Data-Tier Application Framework (DacFx) für Azure SQL-Datenbank V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Die vorherigen Toollinks wurden am oder nach dem 2. März 2015 aktualisiert. Es wird empfohlen, dass Sie die neueren Updates der Tools verwenden.


####C.4.1 Beim Import treten temporäre Probleme mit BACPAC-Dateien auf


 (Stand Mittwoch, 18. Februar 2015)


Beim Exportieren von BACPAC-Dateien von einem auf V12 aktualisierten Azure SQL-Datenbankserver tritt ein bekanntes Problem auf. Exportierte BACPAC-Dateien enthalten ein fehlerhaftes Objekt mit dem Namen "script_deployment_databases". BACPAC-Dateien, die dieses fehlerhafte Objekt enthalten, können nicht mithilfe der Tools SQL Server Management Studio (SSMS), "SqlPackage.exe" oder der Data-Tier Application Framework (DacFx)-API importiert werden.


Allerdings kann eine betroffene BACPAC-Datei über das Azure-Webportal in Azure SQL-Datenbank importiert werden. Wir werden voraussichtlich ab Freitag, den 27. Februar 2015 eine dauerhafte Problembehebung sowie ein Update für die betroffenen Tools veröffentlicht haben. In der Zwischenzeit wenden Sie sich an den Microsoft Support, wenn Sie Unterstützung beim Wiederherstellen einer bereits exportierten Datei benötigen, die über die Dateinamenerweiterung BACPAC verfügt.


Zusätzlich zum Exportproblem tritt bei einer begrenzten Anzahl von Servern und Kunden, die kürzlich auf Version 12 aktualisiert haben, beim Versuch, eine BACPAC-Datei zu importieren, ein anderer Fehler auf. Dies ist ein vorübergehender Fehler im Zusammenhang mit Berechtigungen, der auf einem betroffenen Server normalerweise innerhalb eines Tages behoben wird. Wir erwarten, dass dieses Problem ebenfalls in der Woche ab Montag, den 23. Februar 2015 dauerhaft behoben wird. In der Zwischenzeit können Sie den Importvorgang möglicherweise wiederholen und erfolgreich abschließen. Wenn Sie weitere Unterstützung beim Importieren einer BACPAC-Datei in Azure SQL-Datenbank benötigen, wenden Sie sich an den Microsoft Support.


Befolgen Sie ggf. diese Schritte, um Kontakt mit dem Microsoft Support aufzunehmen:


1. Navigieren Sie zum Azure-Portal.
2. Klicken Sie mit der rechten Maustaste in der oberen rechten Ecke auf den Kontonamen.
3. Klicken Sie im angezeigten Kontextmenü auf den gewünschten Support.
 - Sie können beispielsweise zwischen **Microsoft Support kontaktieren** oder **Hilfe und Support** wählen.


> [AZURE.NOTE] (Montag, 2. März, 2015)
> 
> Das in diesem Abschnitt C.4.1 beschriebene Problem wurde behoben. Es sind keine speziellen Kenntnisse oder Aktionen für Kunden erforderlich, um wunschgemäß fortfahren zu können.
> 
> Der automatisierte Import/Export funktioniert normalerweise unabhängig davon, wie alt Ihre BACPAC-Datei ist.
> 
> Kunden, die ihre BACPAC-Datei mithilfe von DacFx verarbeiten, sollten die aktualisierten Tools herunterladen. Die Links für den Download sind in Abschnitt C.4 verfügbar.


###C.5 Wiederherstellung einer gelöschten V11-Datenbank auf V12

Im folgenden Szenario wird erläutert, dass eine gelöschte V11 Azure SQL-Datenbank auf einem V12 Azure SQL-Datenbankserver wiederhergestellt werden kann.

1. Angenommen, Sie verwenden einen V11 Azure SQL-Datenbankserver. <br/> Auf dem Server ist eine Datenbank in der veralteten Web- und Business-Dienstebene gespeichert.
2. Sie löschen diese Datenbank.
3. Sie führen ein Upgrade des Servers auf V12 aus.
4. Im nächsten Schritt stellen Sie die Datenbank auf den Server wieder her. <br/> Auf diese Weise erfolgt ein Upgrade der Datenbank auf V12 mit der S0-Stufe der Standarddienstebene.
5. Sie können die Datenbank in jede unterstützte Dienstebene umschalten, wenn S0 nicht Ihre Wahl ist.



##D. Fehlerbehebung

Wenn aus einem bestimmten Grund ein Upgradefehler auftritt, bleibt Ihre V11-Datenbank aktiv und ist wie üblich verfügbar.


##Verwandte Links

- Microsoft Azure-[Funktionen der Preview](http://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Unterüberschrift 1]: #subheading-1
<!--HONumber=47-->
