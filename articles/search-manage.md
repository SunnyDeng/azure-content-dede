<properties 
	pageTitle="Verwalten Ihres Suchdiensts in Microsoft Azure" 
	description="Verwalten Ihres Suchdiensts in Microsoft Azure"
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Verwalten Ihres Suchdiensts in Microsoft Azure


[WACOM.INCLUDE [In diesem Artikel wird das Azure Preview-Portal verwendet.](../includes/preview-portal-note.md)]

Azure Search ist ein cloudbasierter Dienst und eine HTTP-basierte API, die Sie in eigenen Suchanwendungen verwenden können. Unser Suchdienst bietet ein Modul für Volltextsuche, Textanalyse, erweiterte Suchfunktionen, Speicherung von Suchdaten und eine Befehlssyntax für Abfragen. 

Dieser Artikel beschreibt die Verwaltung eines Suchdienstes im neuen [Azure-Vorschauportal](https://portal.azure.com).

Alternativ können Sie die Management-REST-API verwenden. Detaillierte Informationen finden Sie unter [Erste Schritte mit der Azure Search Management-REST-API](http://azure.microsoft.com/documentation/articles/search-get-started-management-api/) und [Azure Search Management REST-API](http://msdn.microsoft.com/library/azure/dn832684.aspx).

<!--TOC-->

+ [Fügen Sie den Suchdienst zu Ihrem Abonnement hinzu](#sub-1)
+ [Verwaltungsaufgaben](#sub-2)
+ [Dienst-URL](#sub-3)
+ [Verwalten der API-Schlüssel](#sub-4)
+ [Überwachen der Ressourcenauslastung](#sub-5)
+ [Herauf- oder Herunterskalieren](#sub-6)
+ [Starten bzw. Stoppen des Dienstes](#sub-7)
+ [Festlegen von Rollen zum Steuern des Verwaltungszugriffs](#sub-8)

<h2 id="sub-1">Fügen Sie den Suchdienst zu Ihrem Abonnement hinzu</h2>

Als Administrator beim Einrichten des Search-Diensts ist eine der ersten Entscheidungen die Auswahl einer Preisstufe. Optionen umfassen die Preisstufen Kostenlos und Standard.

Bestehenden Abonnenten steht kostenlos ein gemeinsam genutzter Dienst zur Auswahl, den Sie zu Lernzwecken, zum Testen von Machbarkeitsstudien oder zur Entwicklung kleiner Suchprojekte nutzen können. Der gemeinsam genutzte Dienst ist beschränkt auf 50 MB Speicherplatz, drei Indizes und maximal 10.000 Dokumente, selbst wenn dabei die erlaubten 50 MB noch nicht erreicht sind. Für den gemeinsam genutzten Dienst gelten keine Leistungsgarantien. Für Suchanwendungen in Produktionsumgebungen sollten Sie daher die Standardsuche verwenden.

Die Standardsuche ist kostenpflichtig, da Sie sich für fest zugeordnete Ressourcen und Infrastruktur registrieren, die Ihrem Abonnement exklusiv zur Verfügung steht. Die Standardsuche wird in benutzerdefinierten Paketen aus Partitionen (Speicher) und Replikaten (Dienst-Arbeitslasten) zugewiesen und pro Sucheinheit abgerechnet. Sie können Partitionen und Replikate unabhängig voneinander heraufskalieren und mehr von der jeweils benötigten Ressource hinzufügen.

Wir empfehlen die folgenden Links für die Kapazitätsplanung und zum Verständnis der entstehenden Kosten:

[Limits und Einschränkungen](http://msdn.microsoft.com/library/dn798934.aspx)
[Preisdetails](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Wenn Sie sich anmelden möchten, finden Sie Informationen unter [Erste Schritte mit Azure Search](../search-get-started/).


<h2 id="sub-2">Verwaltungsaufgaben</h2>

Obwohl einige Dienst Co-Administratoren haben können, hat ein Azure-Suchdienst nur einen Administrator pro Abonnement. Sie müssen ein Administrator sein, um die in diesem Abschnitt beschriebenen Aufgaben ausführen zu können.
Neben dem Hinzufügen von Search zum Abonnement ist ein Administrator für diese zusätzlichen Aufgaben zuständig:

Verteilung der Dienst-URL (bei der Diensteinrichtung definiert).
Verwaltung und Verteilung der API-Schlüssel.
Überwachen der Ressourcenauslastung
Herauf- oder Herabskalieren (nur für die Standardsuche)
Starten bzw. Stoppen des Dienstes
Festlegen von Rollen zum Steuern des Verwaltungszugriffs

<h2 id="sub-3">Dienst-URL</h2>

Die Dienst-URL wird bei der Erstellung des Dienstes als konstante Eigenschaft definiert und kann später nicht mehr geändert werden. 

Entwickler benötigen für die Erstellung von Suchanwendungen die Dienst-URL für HTTP-Anforderungen. Sie finden die Dienst-URL schnell und einfach im Dienst-Dashboard.

So finden Sie die Dienst-URL im Dienst-Dashboard:

1.	Melden Sie sich auf dem [Azure-Vorschauportal](https://portal.azure.com) an.
2.	Klicken Sie auf **Durchsuchen** | **Alles ** | **Suchdienste**.
3.	Klicken Sie auf den Namen des Dienstes, um das Dashboard zu öffnen.
4.	Klicken Sie auf **EIGENSCHAFTEN**, um eine Eigenschaftenseite zu öffnen. Die Dienst-URL befindet sich oben auf der Seite. Sie können diese Seite für den späteren Zugriff anheften.

    ![][8]

Möglicherweise werden Sie von Entwicklern nach der API-Version gefragt. Für die Azure Search-API müssen Sie in Ihren Anfragen immer die API-Version angeben. Damit wird sichergestellt, dass Entwickler weiterhin eine ältere Version verwenden können, und zu einem selbst gewählten Zeitpunkt auf eine neuere Version wechseln können.

Die API-Version wird nicht in den Portalseiten angezeigt, daher können Sie diese Information nicht bereitstellen. Weitere Informationen zur aktuellen und zu vorherigen API-Versionen finden Sie unter [Azure Search REST-API](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<!---->
<h2 id="sub-4">Verwalten der API-Schlüssel</h2>

Entwickler benötigen für die Erstellung von Suchanwendungen einen API-Schlüssel für den Zugriff auf die Suche. Jede HTTP-Anforderung an Ihren Suchdienst benötigt einen API-Schlüssel, der speziell für Ihren Dienst generiert wurde. Dieser API-Schlüssel ist der einzige Authentifizierungsmechanismus für die URL Ihres Suchdienstes.

Zwei Arten von Schlüsseln werden für den Zugriff auf Ihren Suchdienst verwendet:

Admin (gültig für alle Operationen)
Abfrage (nur für Abfrage-Anforderungen gültig)

Der Admin-API-Schlüssel wird zusammen mit dem Dienst erstellt. Es existiert ein primärer und ein sekundärer Schlüssel. Beide Schlüssel können gleichwertig verwendet werden und bieten dieselbe Zugriffsebene. Dies ist hilfreich, wenn Sie z. B. Schlüssel austauschen möchten. Sie können beide Admin-Schlüssel austauschen, können allerdings keine weiteren Schlüssel hinzufügen. Pro Dienst können maximal zwei Admin-Schlüssel existieren.

Abfrage-Schlüssel werden für die Verwendung in Clientanwendungen generiert, die die Suche direkt aufrufen. Sie können bis zu 50 Abfrage-Schlüssel generieren.

Öffnen Sie das Dienst-Dashboard, um API-Schlüssel abzurufen oder zu generieren. Klicken Sie auf **SCHLÜSSEL**, um die Schlüsselverwaltungsseite zu öffnen. Sie finden die Befehle zum Generieren bzw. erneuten Generieren von Schlüsseln am oberen Seitenrand.

 ![][9]


<!---->
<h2 id="sub-5">Überwachen der Ressourcenauslastung</h2>

Die Ressourcenüberwachung in der öffentlichen Vorschau ist beschränkt auf die Informationen im Dienst-Dashboard und einige weitere Metriken, die Sie durch Abfragen an den Dienst erhalten.

Im Bereich Nutzung im Dienst-Dashboard können Sie direkt ablesen, ob die Partitionsressourcen für Ihre Anwendung angemessen sind.

Mit der Suchdienst-API können Sie Gesamtzahlen für Dokumente und Indizes abrufen. Für diese Werte gelten feste Obergrenzen basierend auf der Preisebene. Weitere Details finden Sie im Abschnitt [Limits und Einschränkungen](http://msdn.microsoft.com/library/dn798934.aspx). 

[Abrufen der Index-Statistiken](http://msdn.microsoft.com/library/dn798942.aspx)
[Dokumentenanzahl](http://msdn.microsoft.com/library/dn798924.aspx)

> [WACOM.NOTE] Durch Caching-Eigenheiten können die Obergrenzen vorübergehend überschritten werden. Wenn Sie z. B. den gemeinsam genutzten Dienst verwenden, kann es passieren, dass eine Dokumentenanzahl oberhalb der Grenze von 10.000 Dokumenten angezeigt wird. Dies ist vorübergehend und wird bei der nächsten Einhaltungsprüfung für die Obergrenze entdeckt. 


<!---->
<h2 id="sub-6">Herauf- oder Herunterskalieren</h2>

Jeder Suchdienst enthält zunächst ein Minimum von einem Replikat und einer Partition. Wenn Sie sich mit der Preisebene Standard für fest zugeordnete Ressourcen angemeldet haben, können Sie die Kachel **SKALIEREN** im Dienst-Dashboard anklicken, um die Anzahl der Replikate und Partitionen für Ihren Dienst anzupassen.

Wenn Sie zusätzliche Ressourcen hinzufügen, werden diese vom Dienst automatisch verwendet. Sie müssen nichts weiter tun. Allerdings kann eine kleine Verzögerung auftreten, bevor die Auswirkungen der neuen Ressource spürbar sind. Die Bereitstellung zusätzlicher Ressourcen kann 15 Minuten oder mehr in Anspruch nehmen.

 ![][10]

<h3>Hinzufügen von Replikaten</h3>

Fügen Sie Replikate hinzu, um die Anzahl der Abfragen pro Sekunde (Queries per Second QPS) zu steigern oder hohe Verfügbarkeit einzurichten. Jedes Replikat enthält eine Kopie eines Index. Jedes zusätzliche Replikat bedeutet also einen zusätzlichen Index, der zur Beantwortung von Abfragen verwendet werden kann. Momentan gilt die Faustregel, dass Sie für hohe Verfügbarkeit mindestens 3 Replikate benötigen. 

Suchdienste mit mehr Replikaten können Abfragen per Lastenausgleich auf eine größere Anzahl von Indizes verteilen. Für ein gegebenes Abfragevolumen ist ein höherer Durchsatz an Abfragen möglich, wenn mehr Kopien des Index zur Beantwortung bereitstehen. Wenn Sie eine hohe Latenz in Ihren Abfragen bemerken, können Sie eine positive Auswirkung auf die Leistung erwarten, sobald die zusätzlichen Replikate online sind.

Auch wenn der Abfragedurchsatz beim Hinzufügen von Replikaten ansteigt, wird dieser mit zusätzlichen Replikaten nicht exakt verdoppelt, verdreifacht usw. Alle Suchanwendungen unterliegen externen Faktoren, die die Abfrageleistung beeinträchtigen können. Komplexe Abfragen und Netzwerklatenz sind zwei Faktoren, die die Antwortzeiten von Abfragen beeinflussen.

<h3>Hinzufügen von Partitionen</h3>

Die meisten Dienstanwendungen benötigen eher zusätzliche Replikate als zusätzliche Partitionen, da die meisten Suchanwendungen leicht in eine einzige Partition passen, die bis zu 15 Millionen Dokumente unterstützt. 

Falls Sie mehr Dokumente benötigen, können Sie Partitionen hinzufügen. Die Partitionen werden jeweils in Teilern von 12 hinzugefügt (genauer gesagt 1, 2, 3, 4, 6 oder 12). Dies ist ein Artefakt der horizontalen Partitionierung. Indizes werden in 12 Shards erstellt, die entweder in 1 Partition gespeichert oder in gleichen Teilen auf 2, 3, 4, 6 oder 12 Partitionen (1 Shard pro Partition) verteilt werden können.

<h3>Entfernen von Replikaten </h3>

Nach Zeiten mit hohem Abfragevolumen kann es passieren, dass Sie Replikate entfernen möchten, wenn sich die Last wieder normalisiert hat (z. B. nach Abschluss des Weihnachtsgeschäfts).

Schieben Sie dazu einfach den Schieberegler für Replikate auf eine niedrigere Nummer. Keine weiteren Schritte sind erforderlich. Beim Senken der Replikatanzahl werden virtuelle Computer im Rechenzentrum freigegeben. Ihre Abfrage- und Datenerfassungsoperationen werden nun auf weniger VMs ausgeführt als zuvor. Die Untergrenze liegt bei einem Replikat.

<h3>Entfernen von Partitionen</h3>

Im Gegensatz zum Entfernen von Replikaten, bei dem Sie keine weiteren Schritte ausführen müssen, erfordert die Senkung des verbrauchten Speicherplatzes unter Umständen einigen Arbeitsaufwand. Wenn Ihre Lösung z. B. drei Partitionen verwendet, und Sie diese Anzahl auf eine oder zwei Partitionen absenken, erhalten Sie einen Fehler, falls Sie mehr Speicherplatz verbrauchen, als von der niedrigeren Anzahl an Partitionen bereitgestellt werden kann. In diesem Fall können Sie entweder Indizes oder Dokumente innerhalb eines Index löschen, um Speicherplatz freizugeben, oder die aktuelle Konfiguration beibehalten.

Es ist nicht möglich, herauszufinden, welche Index-Shards auf welcher Partition gespeichert sind. Jede Partition bietet ca. 25 GB an Speicherplatz an. Sie müssen Ihren Speicherverbrauch also auf einen Wert senken, der von Ihren konfigurierten Partitionen unterstützt werden kann. Wenn Sie auf eine Partition zurückschalten, müssen alle 12 Shards in diese Partition passen.

Für die zukünftige Planung können Sie den Speicherverbrauch prüfen (siehe [Abrufen der Index-Statistiken](http://msdn.microsoft.com/library/dn798942.aspx)), um herauszufinden, wie viel Speicher Sie tatsächlich nutzen. 


<!---->
<h2 id="sub-7">Starten bzw. Stoppen des Dienstes</h2>

Sie können Dienste mithilfe der Befehle im Dienst-Dashboard starten, anhalten oder sogar löschen.

 ![][11]


Beim Starten oder Anhalten von Diensten wird die Abrechnung nicht deaktiviert. Sie müssen den Dienst komplett löschen, um Kosten komplett zu vermeiden. Alle zu Ihrem Dienst gehörenden Daten werden bei der Außerbetriebnahme Ihres Dienstes gelöscht.

<!---->
<h2 id="sub-8">Festlegen von Rollen für administrativen Zugriff</h2>

Azure bietet ein globales rollenbasiertes Autorisierungsmodell für alle Dienste, die über das Vorschauportal oder in der Azure Ressource Manager-API verwaltet werden, wenn Sie ein benutzerdefiniertes Verwaltungstool verwenden. Die Rollen Besitzer, Mitwirkender und Leser legen die Ebene der Dienstverwaltung für die Active Directory-Benutzer, Gruppen und Sicherheitsprinzipale fest, die Sie jeder Rolle zuweisen. Weitere Informationen zur Rollenmitgliedschaft finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/).

Bei Azure Search bestimmt die rollenbasierte Zugriffssteuerung die folgenden Verwaltungsaufgaben:

<table>
<tr>
<td>Besitzer</td>
<td>
Starten, Beenden oder löschen des Diensts</br>
Generieren und Anzeigen von Admin-Schlüssel und Abfrageschlüsseln</br>
Anzeigen des Dienststatus, einschließlich Indexanzahl, Indexnamen, Dokumentanzahl und Speichergröße</br>
Hinzufügen oder Löschen von Rollenmitgliedschaften (Nur ein Besitzer kann die Rollenmitgliedschaften verwalten.)</br>
</br>
Abonnement- und Dienstadministratoren haben automatisch die Mitgliedschaft in der Besitzerrolle.
</td>
</tr>
<tr>
<td>Mitwirkender</td>
<td>Verfügt über die gleiche Zugriffsebene wie Besitzer mit Ausnahme der Rollenverwaltung. Z. B. kann ein Mitwirkender API-Schlüssel anzeigen und neu generieren, aber nicht die Rollenmitgliedschaften ändern.
</td>
</tr>
<tr>
<td>Leser</td>
<td>Dienststatus und Abfrageschlüssel anzeigen. Mitglieder dieser Rolle können einen Dienst nicht starten oder beenden und auch keine Admin-Schlüssel anzeigen.
</td>
</tr>
</table>

Beachten Sie, dass die Rollen keine Zugriffsrechte für den Dienstendpunkt erteilen. Suchdienstoperationen, wie z. B. die Indexverwaltung, Auffüllung des Indexes und Abfragen von Suchdaten werden durch die API-Schlüssel und nicht durch Rollen gesteuert. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) im Abschnitt "Autorisierung für Verwaltungsvorgänge vs. Datenvorgänge".

Rollen bieten Zugriffssteuerung, nachdem der Dienst erstellt wurde. Nur Abonnementmanager können einen Suchdienst zu einem Abonnement hinzufügen.


<!--Anchors-->
[Fügen Sie den Suchdienst zu Ihrem Abonnement hinzu]: #sub-1
[Verwaltungsaufgaben]: #sub-2
[Dienst-URL]: #sub-3
[Verwalten der API-Schlüssel]: #sub-4
[Überwachen der Ressourcenauslastung]: #sub-5
[Herauf- oder Herunterskalieren]: #sub-6
[Starten bzw. Stoppen des Dienstes]: #sub-7
[Festlegen von Rollen zum Steuern des Verwaltungszugriffs]: #sub-8


<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


<!--Link references-->
[Erste Schritte mit Azure Search]: ../search-get-started/
[Azure Search - Arbeitsablauf für Entwicklung]: ../search-workflow/
[Erstellen Sie Ihre erste Azure Search-Lösung]: ../search-create-first-solution/


<!--HONumber=46--> 
