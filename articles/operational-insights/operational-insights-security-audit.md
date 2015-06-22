<properties 
   pageTitle="Entdecken Sie die Sicherheits- und Überwachungsdaten in Operational Insights"
   description="Erfahren Sie, wie Sie mit dem Sicherheits- und Überwachungspack mit integrierten Suchabfragen für relevante Probleme, die Ihre Aufmerksamkeit erfordern, einen umfassenden Einblick in die Lage der IT-Sicherheit Ihres Unternehmens bekommen."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Entdecken Sie die Sicherheits- und Überwachungsdaten in Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Das Sicherheits- und Überwachungspack von Operational Insights bietet mit integrierten Suchabfragen für relevante Probleme, die Ihre Aufmerksamkeit erfordern, einen umfassenden Einblick in die Lage der IT-Sicherheit Ihres Unternehmens.

Folgendes wird in diesem Artikel beschrieben:

- Einfache Untersuchung einer verdächtigen ausführbaren Datei
- Bewährte Methoden für die forensische Analyse
- Bewährte Methoden für die Untersuchung von Sicherheitsverletzungsmustern
- Bewährte Methoden für Überwachungsszenarien

## Einfache Untersuchung einer verdächtigen ausführbaren Datei

1. Anmelden bei Operational Insights.
2. Prüfen Sie auf der Seite **Übersicht**  die Informationen in der Kachel **Sicherheit und Überwachung** und klicken Sie sie an. ![Bild der Seite "Übersicht"](./media/operational-insights-security-audit/sec-audit-dash02.png)
3. Prüfen Sie auf der Seite **Sicherheit und Überwachung** die Informationen auf dem Blatt **RELEVANTE PROBLEME**. Im Beispielbild sehen Sie 6 relevante Probleme für heute, 2 von gestern. In diesem Beispiel gibt es 1 verdächtige ausführbare Datei. Klicken Sie im Blatt **RELEVANTE PROBLEME** auf **Verdächtige ausführbare Dateien**. ![Bild der Seite "Sicherheit und Überwachung"](./media/operational-insights-security-audit/sec-audit-dash03.png)
4. In der Suche werden die Abfrage sowie die Ergebnisse für die verdächtige ausführbare Datei, auf die Sie geklickt haben, angezeigt. Im Beispiel gibt es 1 Ergebnis, der Dateihash wird angezeigt. Klicken Sie auf die **DATEIHASH**-ID. ![Bild der Suchergebnisse "Dateihash"](./media/operational-insights-security-audit/sec-audit-search01.png) 
5. In der Suche werden nun weitere Informationen über die ausführbare Datei angezeigt, u.a. der Dateipfad und der Prozessname. Klicken Sie auf **Prozess &lt;Dateiname&gt;**. In diesem Beispiel ist das HEXEDIT.EXE. ![Bild der Suchergebnisse "Prozess"](./media/operational-insights-security-audit/sec-audit-search02.png) 
6. In der Suche wird die Abfrage um den Prozessnamen in Anführungszeichen ergänzt. In diesem Beispiel "**HEXEDIT.EXE"**. ![Bild der Suchabfrage](./media/operational-insights-security-audit/sec-audit-search03.png)
7. Lassen Sie im Feld für die Suchabfrage nur den Prozessnamen und die Anführungszeichen stehen und klicken Sie dann auf das Suchsymbol. ![Bild der detaillierten Suchinformationen](./media/operational-insights-security-audit/sec-audit-search04.png)
8. In der Suche werden ausführliche Informationen über den Prozess angezeigt, u.a. der oder die Computer, wo der Prozess lief, das entsprechende Benutzerkonto sowie Datum und Uhrzeit, zu der das Ereignis für den Prozess erstellt wurde.
9. Mit den gewonnenen Informationen können Sie nun die gewünschten Maßnahmen ergreifen. Falls Sie zum Beispiel feststellen, dass es sich bei der ausführbaren Datei um Schadsoftware handelt, können Sie sie von allen betroffenen Computersystemen entfernen lassen. Wenn die ausführbare Datei entfernt wurde und Operational Insights aktuelle Protokoll- und Überwachungsereignisse für Ihr Computersystem erhält, werden sich die Werte auf dem Blatt RELEVANTE PROBLEME am folgenden Tag ändern.

## Bewährte Methoden für die forensische Analyse

**Spurensuche**

Wenn Sie mithilfe des Sicherheits- und Überwachungspacks eine forensische Analyse durchführen, suchen Sie nach Spuren, die potenziell böswillige Benutzer hinterlassen. Unabhängig davon, was Benutzer in ihrer IT-Umgebung tun, generieren viele ihrer Aktivitäten Sicherheitsartefakte. Beachten Sie, dass Angreifer den Inhalt der Protokolle oft löschen. Dies ist meist der erste Schritt, mit dem sie ihre Spur verwischen wollen.

Ganz gleich, ob die Benutzer direkt oder per Remote auf ihre Computer zugreifen, der Zugriff wird in Ereignisprotokollen gespeichert. Operational Insights sammelt diese Artefakte *sobald sie auftreten*, noch bevor sie jemand manipulieren kann, und ermöglicht Ihnen, mittels Datenkorrelation über mehrere Computer verschiedene Analysen durchzuführen.

**Grundlegendes zum Konfigurieren und Erfassen von Überwachungsereignissen**

Um das Sicherheits- und Überwachungspack optimal zu nutzen, sollten Sie mithilfe der folgenden Ressourcen die Ebene der von Ihrer Windows-Umgebung erhobenen Überwachungsereignisse konfigurieren, so dass sie Ihren Anforderungen am besten entspricht.

- [Konfigurieren der Sicherheitsrichtlinieneinstellungen](https://technet.microsoft.com/library/dn135243(v=ws.10).aspx)

- [Erweiterte Überwachungsrichtlinienkonfiguration](https://technet.microsoft.com/library/jj852202(v=ws.10).aspx)

- [Empfehlungen zu Überwachungsrichtlinien](https://technet.microsoft.com/library/dn487457.aspx)

**Aktivieren von AppLocker**

Sie sollten auch AppLocker-Ereignisse aktivieren, um umfassende Informationen über Prozessausführungen in Ihrer IT-Umgebung zu erhalten. Weitere Informationen finden Sie unter [Konfigurieren einer AppLocker-Richtlinie ausschließlich zur Überwachung](https://technet.microsoft.com/library/hh994622.aspx)

**Konfigurieren der Überwachungsebene für Windows-Ereignisse**

Die Windows-Umgebung ermöglicht Ihnen, den Umfang der sicherheitsrelevanten Aufzeichnungen selbst zu bestimmen. Beispielsweise können Sie die Umgebung so konfigurieren, dass jedes Mal, wenn ein Benutzer auf eine Datei zugreift, sie öffnet oder liest, ein Ereignis erstellt wird. Die zu erfassende Detailmenge ändert sich ganz nach Ihrem Bedarf. Allerdings bedeutet jede weitere aktivierte Option auch höhere Kosten, da die von Ihnen gesammelten Daten alle gespeichert werden müssen. Daher verzichten viele Unternehmen darauf, Lese- oder Schreibvorgänge bei ihren Objekten zu erfassen. Bei jedem einzelnen Dateizugriff können tausende von Ereignissen generiert werden, von denen etliche nutzlos sind. Für welchen Umfang zu erfassender Daten Sie sich entscheiden, hängt von Ihrer Einschätzung ab.

>[AZURE.NOTE]Bei Verwendung des Direktagenten muss der Proxyserver Ihres Unternehmens so konfiguriert werden, dass er dem Agenten den Zugriff auf Operational Insights erlaubt. Weitere Informationen finden Sie unter [Konfigurieren von Proxy- und Firewalleinstellungen](operational-insights-proxy-firewall.md).

## Bewährte Methoden für die Untersuchung von Sicherheitsverletzungsmustern

**Untersuchung ungewöhnlicher Aktivitätsmuster**

Sicherheitsverletzungen sind gewöhnlich auf legitime Anmeldedaten zurückzuführen und erfordern, dass ein böswilliger Benutzer versucht, durch Angriffe erhöhte Rechte zu erlangen. Der Schwerpunkt des Sicherheits- und Überwachungspacks liegt nicht auf der Angriffserkennung, die Funktion "relevante Probleme" hilft Ihnen jedoch dabei, Muster ungewöhnlicher Aktivitäten zu entdecken und zu untersuchen. So sollten Sie folgende und alle weiteren ungewöhnlichen Aktivitäten unter **Relevante Probleme** untersuchen.

- ungewöhnliche Anmeldungen an einem Computer, den der Benutzer normalerweise nicht verwendet

- ungewöhnliche Netzwerkaufzählung von untypischen Benutzern oder Computern

- Erstellung neuer Benutzerkonten mit Administratorrechten

- Änderungen an den Protokollierungs- oder Sicherheitsrichtlinien

- verdächtige ausführbare Dateien

## Bewährte Methoden für Überwachungsszenarien

Sie müssen wahrscheinlich Computer- und Netzwerkkompatibilitätsrichtlinien und -vorschriften Ihres Unternehmens einhalten, die umfassende Überwachungsaufzeichnungen erfordern. Ein Finanzunternehmen zum Beispiel muss wahrscheinlich Aufzeichnungen machen, mit denen sich zu jedem Zeitpunkt nachweisen lässt, welcher Benutzer einen bestimmten Vorgang im Netzwerk durchgeführt hat. Es könnte auch nötig sein, detaillierte Berichte über die Aktivitäten eines bestimmten Benutzers oder ausgewählter Server nach Bedarf zu erstellen – und das zum Teil über viele Monate, manchmal sogar Jahre zurück.

Verwenden Sie das Sicherheits- und Überwachungspack, um Überwachungsdaten Ihrer gesamten IT-Umgebung zu erfassen, gleich, ob von lokalen Computern oder aus der Cloud. Alle Überwachungsdaten werden gespeichert und indiziert. Beim Operational Insights Premium-Abonnement werden alle Daten unbegrenzt gespeichert. Sie können die Überwachungsdaten ansehen, Suchvorgänge ausführen und zwischen verschiedenen Datentypen und Computern korrelieren, um umfassende Ergebnisse für jedes Zeitintervall seit Beginn der Datensammlung zu erhalten.

**Verwenden von Gruppenrichtlinien zur Erfassung von Überwachungsdaten**

Alle Überwachungsdaten, die Sie erfassen und an Operational Insights senden möchten, werden vollständig über die Gruppenrichtlinien verwaltet. Sie verwenden sie, um Sicherheitskonfigurationen als Teil eines Gruppenrichtlinienobjekts (GPO) zu definieren, die zu Active Directory-Containern wie Sites, Domänen und Organisationseinheiten verlinkt sind. Sie helfen bei der Verwaltung der Sicherheitseinstellungen. Richtliniendaten werden protokolliert und später an den Operational Insights-Dienst gesendet.

**Verwenden von AppLocker zur Erfassung von Überwachungsdaten**

Wenn Sie zusätzlich zu den lokalen Richtlinieneinstellungen AppLocker zur Erfassung der Überwachungsdaten verwenden, wird Operational Insights diese Daten erfassen und dann können Sie sie ansehen.

<!--HONumber=54--> 