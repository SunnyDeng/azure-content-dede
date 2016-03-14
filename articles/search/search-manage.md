<properties 
	pageTitle="Verwalten Ihres Suchdiensts in Microsoft Azure | Microsoft Azure | Gehosteter Cloudsuchdienst" 
	description="Verwalten von Azure Search, einem in Microsoft Azure gehosteten Cloudsuchdienst" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="heidist"/>

# Verwalten Ihres Suchdiensts in Microsoft Azure
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST-API](search-get-started-management-api.md)

Azure Search ist ein cloudbasierter Dienst mit einer HTTP-basierten API, die Sie in eigenen Suchanwendungen verwenden können. Unser Suchdienst bietet ein Modul für Volltextsuche, Textanalyse, erweiterte Suchfunktionen, Speicherung von Suchdaten und eine Befehlssyntax für Abfragen.

In diesem Artikel wird die Verwaltung eines Search-Diensts im [Azure-Portal](https://portal.azure.com) erläutert. Sie können auch die neue Funktion zum Durchsuchen der Datenverkehrsanalyse verwenden, um Einblicke in die Aktivitäten auf Indexebene zu erhalten. Die ersten Schritte finden Sie unter [„Datenverkehrsanalyse durchsuchen“ für Azure Search](search-traffic-analytics.md).

Alternativ können Sie die Management-REST-API verwenden. Detaillierte Informationen finden Sie unter [Erste Schritte mit der Azure Search Management-REST-API](search-get-started-management-api.md) und [Azure Search Management REST-API](http://msdn.microsoft.com/library/azure/dn832684.aspx).

<a id="sub-1"></a>
## Fügen Sie den Suchdienst zu Ihrem Abonnement hinzu

Als Administrator beim Einrichten des Search-Diensts ist eine der ersten Entscheidungen die Auswahl eines Tarifs. Optionen umfassen die Tarife "Free" und "Standard".

Bestehenden Abonnenten steht kostenlos ein gemeinsam genutzter Dienst zur Auswahl, den Sie zu Lernzwecken, zum Testen von Machbarkeitsstudien oder zur Entwicklung kleiner Suchprojekte nutzen können. Der gemeinsam genutzte Dienst ist beschränkt auf 50 MB Speicherplatz, drei Indizes und maximal 10.000 Dokumente, selbst wenn dabei die erlaubten 50 MB noch nicht erreicht sind. Für den Shared-Dienst gelten keine Leistungsgarantien. Für Suchanwendungen in Produktionsumgebungen sollten Sie daher die Standardsuche verwenden.

Basis- und Standardsuche sind kostenpflichtig, da Sie sich für fest zugeordnete Ressourcen und Infrastruktur registrieren, die Ihrem Abonnement exklusiv zur Verfügung stehen. Basis- und Standardsuche werden in benutzerdefinierten Paketen aus Partitionen (Speicher) und Replikaten (Dienstworkloads) zugewiesen und pro Sucheinheit abgerechnet. Sie können Partitionen und Replikate unabhängig voneinander heraufskalieren und mehr von der jeweils benötigten Ressource hinzufügen.

Wir empfehlen die folgenden Links für die Kapazitätsplanung und zum Verständnis der entstehenden Kosten:

+	[Limits und Einschränkungen](search-limits-quotas-capacity.md)
+	[Preisübersicht](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Wenn Sie sich anmelden möchten, finden Sie entsprechende Informationen unter [Erstellen eines Azure-Suchdiensts im Portal](search-create-service-portal.md).

##Suchanalyse

Sie können während der Suchaktivitäten der Benutzer Daten sammeln, um besser zu verstehen, wie Ihr leistungsfähig Ihr Suchdienst ist, welche Begriffe gesucht werden und ob für diese Suchbegriffe Treffer zurückgegeben werden. Am besten lassen sich diese Daten mithilfe eines Power BI-Inhaltspakets analysieren und visualisieren. Der erste Schritt besteht im Aktivieren einer Analyse des Suchdatenverkehrs. Informationen zur Vorgehensweise finden Sie unter [Analyzing your Azure Search traffic](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/) (Analysieren Ihres Azure Search-Datenverkehrs).

<a id="sub-2"></a>
## Verwaltungsaufgaben

Obwohl einige Dienst Co-Administratoren haben können, hat ein Azure-Suchdienst nur einen Administrator pro Abonnement. Sie müssen ein Administrator sein, um die in diesem Abschnitt beschriebenen Aufgaben ausführen zu können. Neben dem Hinzufügen von Search zum Abonnement ist ein Administrator für diese zusätzlichen Aufgaben zuständig:

+	Verteilung der Dienst-URL (bei der Diensteinrichtung definiert).
+	Verwaltung und Verteilung der API-Schlüssel.
+	Überwachen der Ressourcenauslastung
+	Herauf- oder Herabskalieren (nur für die Standardsuche)
+	Starten bzw. Stoppen des Dienstes
+	Festlegen von Rollen zum Steuern des Verwaltungszugriffs

<a id="sub-3"></a>
## Dienst-URL

Die Dienst-URL wird bei der Erstellung des Dienstes als konstante Eigenschaft definiert und kann später nicht mehr geändert werden.

Entwickler benötigen für die Erstellung von Suchanwendungen die Dienst-URL für HTTP-Anforderungen. Sie finden die Dienst-URL schnell und einfach im Dienst-Dashboard.

So finden Sie die Dienst-URL im Dienst-Dashboard:

1.	Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.	Klicken Sie auf **Durchsuchen** | **Alles** | **Dienste suchen**.
3.	Klicken Sie auf den Namen des Dienstes, um das Dashboard zu öffnen.
4.	Klicken Sie auf **EIGENSCHAFTEN**, um eine Eigenschaftenseite zu öffnen. Die Dienst-URL befindet sich oben auf der Seite. Sie können diese Seite für den späteren Zugriff anheften.

    ![][8]

Möglicherweise werden Sie von Entwicklern nach der API-Version gefragt. Für die Azure Search-API müssen Sie in Ihren Anfragen immer die API-Version angeben. Damit wird sichergestellt, dass Entwickler weiterhin eine ältere Version verwenden können, und zu einem selbst gewählten Zeitpunkt auf eine neuere Version wechseln können.

Die API-Version wird nicht in den Portalseiten angezeigt, daher können Sie diese Information nicht bereitstellen. Weitere Informationen zur aktuellen und zu vorherigen API-Versionen finden Sie unter [Azure Search REST-API](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<a id="sub-4"></a>
## Verwalten der API-Schlüssel

Entwickler benötigen für die Erstellung von Suchanwendungen einen API-Schlüssel für den Zugriff auf die Suche. Jede HTTP-Anforderung an Ihren Suchdienst benötigt einen API-Schlüssel, der speziell für Ihren Dienst generiert wurde. Dieser API-Schlüssel ist der einzige Authentifizierungsmechanismus für die URL Ihres Suchdienstes.

Zwei Arten von Schlüsseln werden für den Zugriff auf Ihren Suchdienst verwendet:

+	Admin (gültig für alle Operationen)
+	Abfrage (nur für Abfrage-Anforderungen gültig)

Der Admin-API-Schlüssel wird zusammen mit dem Dienst erstellt. Es existiert ein primärer und ein sekundärer Schlüssel. Beide Schlüssel können gleichwertig verwendet werden und bieten dieselbe Zugriffsebene. Dies ist hilfreich, wenn Sie z. B. Schlüssel austauschen möchten. Sie können beide Admin-Schlüssel austauschen, können allerdings keine weiteren Schlüssel hinzufügen. Pro Dienst können maximal zwei Admin-Schlüssel existieren.

Abfrage-Schlüssel werden für die Verwendung in Clientanwendungen generiert, die die Suche direkt aufrufen. Sie können bis zu 50 Abfrage-Schlüssel generieren.

Öffnen Sie das Dienst-Dashboard, um API-Schlüssel abzurufen oder zu generieren. Klicken Sie auf **SCHLÜSSEL**, um die Schlüsselverwaltungsseite zu öffnen. Sie finden die Befehle zum Generieren bzw. erneuten Generieren von Schlüsseln am oberen Seitenrand.

 ![][9]


<a id="sub-5"></a>
## Überwachen der Ressourcenauslastung

Die Ressourcenüberwachung in der öffentlichen Vorschau ist beschränkt auf die Informationen im Dienst-Dashboard und einige weitere Metriken, die Sie durch Abfragen an den Dienst erhalten.

Im Bereich Nutzung im Dienst-Dashboard können Sie direkt ablesen, ob die Partitionsressourcen für Ihre Anwendung angemessen sind.

Mit der Suchdienst-API können Sie Gesamtzahlen für Dokumente und Indizes abrufen. Für diese Werte gelten feste Obergrenzen basierend auf dem Tarif. In den [Einschränkungen für Suchdienste](search-limits-quotas-capacity.md) finden Sie weitere Informationen.

+	[Abrufen von Indexstatistiken](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Dokumentenanzahl](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Durch Caching-Eigenheiten können die Obergrenzen vorübergehend überschritten werden. Wenn Sie z. B. den gemeinsam genutzten Dienst verwenden, kann es passieren, dass eine Dokumentenanzahl oberhalb der Grenze von 10.000 Dokumenten angezeigt wird. Dies ist vorübergehend und wird bei der nächsten Einhaltungsprüfung für die Obergrenze entdeckt.


<a id="sub-6"></a>
## Herauf- oder Herunterskalieren

Jeder Suchdienst enthält zunächst ein Minimum von einem Replikat und einer Partition. Wenn Sie sich mit den Tarifen [Basic oder Standard](search-limits-quotas-capacity.md) für fest zugeordnete Ressourcen angemeldet haben, können Sie im Dienstdashboard auf die Kachel **SKALIEREN** klicken, um die Anzahl der Replikate und Partitionen für Ihren Dienst anzupassen.

Wenn Sie zusätzliche Kapazität über eine dieser Ressourcen hinzufügen, wird diese vom Dienst automatisch verwendet. Sie müssen nichts weiter tun. Allerdings kann eine kleine Verzögerung auftreten, bevor die Auswirkungen der neuen Ressource spürbar sind. Die Bereitstellung zusätzlicher Ressourcen kann 15 Minuten oder mehr in Anspruch nehmen.

 ![][10]

### Hinzufügen von Replikaten

Fügen Sie Replikate hinzu, um die Anzahl der Abfragen pro Sekunde (Queries per Second QPS) zu steigern oder hohe Verfügbarkeit einzurichten. Jedes Replikat enthält eine Kopie eines Index. Jedes zusätzliche Replikat bedeutet also einen zusätzlichen Index, der zur Beantwortung von Abfragen verwendet werden kann. Derzeit gilt die Faustregel, dass Sie für hohe Verfügbarkeit mindestens drei Replikate benötigen (weitere Informationen finden Sie unter [Kapazitätsplanung](search-capacity-planning.md)).

Suchdienste mit mehr Replikaten können Abfragen per Lastenausgleich auf eine größere Anzahl von Indizes verteilen. Für ein gegebenes Abfragevolumen ist ein höherer Durchsatz an Abfragen möglich, wenn mehr Kopien des Index zur Beantwortung bereitstehen. Wenn Sie eine hohe Latenz in Ihren Abfragen bemerken, können Sie eine positive Auswirkung auf die Leistung erwarten, sobald die zusätzlichen Replikate online sind.

Auch wenn der Abfragedurchsatz beim Hinzufügen von Replikaten ansteigt, wird dieser mit zusätzlichen Replikaten nicht exakt verdoppelt, verdreifacht usw. Alle Suchanwendungen unterliegen externen Faktoren, die die Abfrageleistung beeinträchtigen können. Komplexe Abfragen und Netzwerklatenz sind zwei Faktoren, die die Antwortzeiten von Abfragen beeinflussen.

### Hinzufügen von Partitionen

Die meisten Dienstanwendungen benötigen eher zusätzliche Replikate als zusätzliche Partitionen, da die meisten Suchanwendungen leicht in eine einzige Partition passen, die bis zu 15 Millionen Dokumente unterstützt.

In Fällen, in denen Sie eine zusätzliche Anzahl von Dokumenten benötigen, können Sie Partitionen hinzufügen, falls Sie für den Standarddienst registriert sind. In der Preisstufe Basic stehen keine zusätzlichen Partitionen zur Verfügung.

In der Preisstufe Standard werden Partitionen jeweils in Teilern von 12 hinzugefügt (1, 2, 3, 4, 6 oder 12). Dies ist ein Artefakt der horizontalen Partitionierung. Indizes werden in 12 Shards erstellt, die entweder in 1 Partition gespeichert oder in gleichen Teilen auf 2, 3, 4, 6 oder 12 Partitionen (1 Shard pro Partition) verteilt werden können.

### Entfernen von Replikaten

Nach Zeiten mit hohem Abfragevolumen kann es passieren, dass Sie Replikate entfernen möchten, wenn sich die Last wieder normalisiert hat (z. B. nach Abschluss des Weihnachtsgeschäfts).

Schieben Sie dazu einfach den Schieberegler für Replikate auf eine niedrigere Nummer. Keine weiteren Schritte sind erforderlich. Beim Senken der Replikatanzahl werden virtuelle Computer im Rechenzentrum freigegeben. Ihre Abfrage- und Datenerfassungsoperationen werden nun auf weniger VMs ausgeführt als zuvor. Die Untergrenze liegt bei einem Replikat.

### Entfernen von Partitionen

Im Gegensatz zum Entfernen von Replikaten, bei dem Sie keine weiteren Schritte ausführen müssen, erfordert die Senkung des verbrauchten Speicherplatzes unter Umständen einigen Arbeitsaufwand. Wenn Ihre Lösung z. B. drei Partitionen verwendet, und Sie diese Anzahl auf eine oder zwei Partitionen absenken, erhalten Sie einen Fehler, falls Sie mehr Speicherplatz verbrauchen, als von der niedrigeren Anzahl an Partitionen bereitgestellt werden kann. In diesem Fall können Sie entweder Indizes oder Dokumente innerhalb eines Index löschen, um Speicherplatz freizugeben, oder die aktuelle Konfiguration beibehalten.

Es ist nicht möglich, herauszufinden, welche Index-Shards auf welcher Partition gespeichert sind. Jede Partition bietet ca. 25 GB an Speicherplatz an. Sie müssen Ihren Speicherverbrauch also auf einen Wert senken, der von Ihren konfigurierten Partitionen unterstützt werden kann. Wenn Sie auf eine Partition zurückschalten, müssen alle 12 Shards in diese Partition passen.

Für die zukünftige Planung können Sie den Speicherverbrauch prüfen (siehe [Abrufen der Index-Statistiken](http://msdn.microsoft.com/library/dn798942.aspx)), um herauszufinden, wie viel Speicher Sie tatsächlich nutzen.

### Bewährte Methoden zum Skalieren und Bereitstellen des Diensts über mehrere Datencenter hinweg (Video)

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="sub-7"></a>
## Starten bzw. Stoppen des Dienstes

Sie können Dienste mithilfe der Befehle im Dienst-Dashboard starten, anhalten oder sogar löschen.

 ![][11]


Beim Starten oder Anhalten von Diensten wird die Abrechnung nicht deaktiviert. Sie müssen den Dienst komplett löschen, um Kosten komplett zu vermeiden. Alle zu Ihrem Dienst gehörenden Daten werden bei der Außerbetriebnahme Ihres Dienstes gelöscht.

<a id="sub-8"></a>
## Festlegen von Rollen für administrativen Zugriff

Azure bietet ein globales rollenbasiertes Autorisierungsmodell für alle Dienste, die über das Portal oder in der Azure Ressource Manager-API verwaltet werden, wenn Sie ein benutzerdefiniertes Verwaltungstool verwenden. Die Rollen Besitzer, Mitwirkender und Leser legen die Ebene der Dienstverwaltung für die Active Directory-Benutzer, Gruppen und Sicherheitsprinzipale fest, die Sie jeder Rolle zuweisen. Weitere Informationen zur Rollenmitgliedschaft finden Sie unter [Rollenbasierte Zugriffssteuerung über das klassische Azure-Portal](../active-directory/role-based-access-control-configure.md).

Bei Azure Search bestimmt die rollenbasierte Zugriffssteuerung die folgenden Verwaltungsaufgaben:


Rolle|Aufgabe
---|---
Besitzer|Starten, Beenden oder Löschen des Diensts.<p>Erstellen und Anzeigen von Administrator- und Abfrageschlüsseln.<p>Anzeigen des Dienststatus, einschließlich Indexanzahl, Indexnamen, Dokumentanzahl und Speichergröße.<p>Hinzufügen oder Löschen von Rollenmitgliedschaften (Nur ein Besitzer kann die Rollenmitgliedschaften verwalten).<p>Abonnement- und Dienstadministratoren sind automatisch Mitglieder der Besitzerrolle.
Mitwirkender|Verfügt über die gleiche Zugriffsebene wie Besitzer mit Ausnahme der Rollenverwaltung. So kann z. B. ein Mitwirkender den `api-key` anzeigen und neu generieren, aber nicht die Rollenmitgliedschaften ändern.
Reader|Dienststatus und Abfrageschlüssel anzeigen. Mitglieder dieser Rolle können einen Dienst nicht starten oder beenden und auch keine Admin-Schlüssel anzeigen.

Beachten Sie, dass die Rollen keine Zugriffsrechte für den Dienstendpunkt erteilen. Suchdienstoperationen, wie z. B. die Indexverwaltung, Auffüllung des Indexes und Abfragen von Suchdaten werden durch die API-Schlüssel und nicht durch Rollen gesteuert. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung über das Azure-Vorschauportal](../active-directory/role-based-access-control-configure.md) im Abschnitt "Autorisierung für Verwaltungsvorgänge vs. Datenvorgänge".

Rollen bieten Zugriffssteuerung, nachdem der Dienst erstellt wurde. Nur Abonnementmanager können einen Suchdienst zu einem Abonnement hinzufügen.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7
[Set roles to control administrative access]: #sub-8

<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


 

<!---HONumber=AcomDC_0302_2016-->