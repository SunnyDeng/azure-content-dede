<properties
   pageTitle="Optimieren der Umgebung mit Assessment-Lösungen"
   description="Verwenden von Assessment-Lösungen zum Bewerten der Risiken und der Integrität Ihrer Serverumgebungen in regelmäßigen Abständen"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2015"
   ms.author="banders" />

# Optimieren der Umgebung mit Assessment-Lösungen

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Mit den Assessment-Lösungen bewerten Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen. Sie bieten eine priorisierte Liste von Empfehlungen, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten. Empfehlungen werden in sechs Schwerpunktbereichen kategorisiert, wodurch Sie die Risiken und Integrität Ihrer Infrastruktur besser nachvollziehen und Maßnahmen einfacher ergreifen können, um Risiken zu verringern und die Integrität verbessern.

Derzeit sind die folgenden Assessment-Lösungen verfügbar:

- Active Directory

- SQL Server

Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jede Empfehlung enthält Informationen, warum ein Problem ggf. für Sie relevant ist, und wie Sie die vorgeschlagenen Änderungen umsetzen können.

Sie können Schwerpunktbereiche wählen, die für Ihre Organisation am wichtigsten sind, und Ihren Fortschritt in Richtung einer risiko- und fehlerfreien Umgebung nachverfolgen.

Sie installieren die Lösungen, um den Operations Manager-Agent und das Basiskonfigurationsmodul für Operational Insights zu aktualisieren, und fügen auf den überwachten Servern die Datei "AdvisorAssessment.exe" hinzu. Konfigurationsdaten werden gelesen und dann zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Sobald die Bewertungen abgeschlossen sind, werden zusammenfassende Informationen für Schwerpunktbereiche im Dashboard **Assessment** für die Infrastruktur in Ihrer Umgebung angezeigt. Mithilfe der Informationen auf dem Dashboard **Assessment** können Sie für Ihre Serverinfrastruktur empfohlene Maßnahmen anzeigen und anschließend ergreifen.

![Abbildung der Kachel "SQL Assessment"](./media/operational-insights-assessment/overview-sql-assess.png)

![Abbildung des Dashboards "SQL-Assessment"](./media/operational-insights-assessment/gallery-ad-01.png)


## Assessment-Lösungen – häufig gestellte Fragen

*Wie oft erfolgt eine Bewertung?*<br> Die Bewertung erfolgt alle sieben Tage.

*Gibt es eine Möglichkeit, die Häufigkeit der Bewertung zu konfigurieren?*<br> Derzeit leider nicht.

*Wird ein anderer Server, der erst nach dem Hinzufügen der Assessment-Lösung erkannt wird, auch bewertet?*<br> Ja. Ab dem Zeitpunkt der Erkennung wird er alle sieben Tage bewertet.

*Wann wird ein Server, der außer Betrieb genommen wird, aus der Bewertung entfernt?*<br> Ein Server, der drei Wochen keine Daten übertragen hat, wird entfernt.

*Wie lautet der Name des Prozesses, der die Daten sammelt?*<br> AdvisorAssessment.exe

*Wie lange dauert das Sammeln der Daten?*<br> Die eigentliche Datensammlung auf dem Server dauert etwa eine Stunde. Auf Servern mit vielen SQL-Instanzen oder -Datenbanken oder Active Directory-Servern kann der Vorgang auch mehr Zeit in Anspruch nehmen.

*Welche Art von Daten wird gesammelt?*<br> Die folgenden Datentypen werden gesammelt:

- WMI
- Registrierung
- Leistungsindikatoren
- Dynamische SQL-Verwaltungssichten

*Gibt es eine Möglichkeit, den Zeitpunkt der Datensammlung zu konfigurieren?*<br> Derzeit leider nicht.

*Warum muss ich ein ausführendes Konto konfigurieren?*<br> Für SQL Server werden nur wenige SQL-Abfragen ausgeführt. Damit diese ausgeführt werden können, muss ein ausführendes Konto mit der SQL-Berechtigung "Serverstatus anzeigen" verwendet werden. Zum Abfragen von WMI sind darüber hinaus lokale Administratorrechte erforderlich.

*Warum werden nur die ersten 10 Empfehlungen angezeigt?*<br> Anstatt Ihnen eine umfangreiche und erdrückende Aufgabenliste zu präsentieren, empfehlen wir, sich zuerst auf die Empfehlungen mit hoher Priorität zu konzentrieren. Nach deren Umsetzung werden weitere Empfehlungen verfügbar. Wenn Sie jedoch lieber die ganze Liste mit allen Einzelheiten anzeigen möchten, können Sie mithilfe der Protokollsuchfunktionen in Operational Insights alle Empfehlungen anzeigen.

*Gibt es eine Möglichkeit, eine Empfehlung zu ignorieren?*<br> Derzeit leider nicht.

## Grundlegendes zum Priorisieren von Empfehlungen

Jede vorgenommene Empfehlung erhält einen Gewichtungswert, der die relative Wichtigkeit der Empfehlung angibt. Es werden nur die zehn wichtigsten Empfehlungen angezeigt.

### Berechnen von Gewichtungen

Gewichtungen sind aggregierte Werte, die auf drei wesentlichen Faktoren basieren:

- Der *Wahrscheinlichkeit*, dass ein erkanntes Problem Schwierigkeiten verursacht. Eine höhere Wahrscheinlichkeit entspricht einer höheren Gesamtwertung für die Empfehlung.

- Der *Auswirkung* des Problems auf Ihre Organisation, wenn es tatsächlich Schwierigkeiten verursacht. Eine stärkere Auswirkung entspricht einer höheren Gesamtwertung für die Empfehlung.

- Dem erforderlichen *Aufwand* zur Umsetzung der Empfehlung. Ein höherer Aufwand entspricht einer niedrigeren Gesamtwertung für die Empfehlung.

Die Gewichtung für jede Empfehlung wird als Prozentsatz der Gesamtwertung ausgedrückt, die für jeden Schwerpunktbereich verfügbar ist. Wenn beispielsweise eine Empfehlung im Schwerpunktbereich "Sicherheit und Einhaltung" eine Wertung von 5 % hat, erhöht eine Umsetzung dieser Empfehlung die Gesamtwertung von "Sicherheit und Einhaltung" um 5 %.

### Schwerpunktbereiche

**Sicherheit und Compliance** - Schützen Sie den Ruf Ihrer Organisation, indem Sie Maßnahmen zur Verhinderung von Sicherheitsrisiken und -verstößen ergreifen, Unternehmensrichtlinien durchsetzen und technische, rechtliche sowie behördliche Vorschriften erfüllen.

**Verfügbarkeit und Geschäftskontinuität** - Halten Sie die Verfügbarkeit Ihrer Dienste und die Rentabilität Ihres Unternehmens aufrecht, indem Sie sicherstellen, dass Ihre Infrastruktur ausfallsicher und der Geschäftsbetrieb bei einem Notfall ausreichend geschützt ist.

**Leistung und Skalierbarkeit** - Fördern Sie das Wachstum und die Innovationsbereitschaft Ihrer Organisation, indem Sie dafür sorgen, dass die IT-Umgebung den aktuellen Leistungsanforderungen gerecht wird und schnell auf wechselnde Geschäftsanforderungen reagieren kann.

**Upgrade, Migration und Bereitstellung** - Machen Sie Ihre IT-Abteilung zum Hauptmotor für Wandel und Innovation, indem Sie das Potenzial neuer Technologien voll ausschöpfen und damit Mehrwert für Organisationseinheiten, Mitarbeiter und Kunden schaffen.

**Betrieb und Überwachung** - Senken Sie das Budget für die IT-Wartung, indem Sie Ihren IT-Betrieb rationalisieren und ein umfassendes Programm mit vorbeugenden Wartungsmaßnahmen umsetzen, um die Geschäftsleistung zu maximieren.

**Änderungs- und Konfigurationsverwaltung** - Schützen Sie den täglichen Betrieb Ihrer Organisation, und stellen Sie sicher, dass Änderungen sich nicht negativ auf die Geschäftsabläufe auswirken, indem Sie Verfahren zur Änderungssteuerung einrichten und Systemkonfigurationen nachverfolgen und überwachen.

### Müssen in jedem Schwerpunktbereich 100 % erzielt werden?

Nicht unbedingt. Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jedoch sind keine zwei Serverinfrastrukturen identisch, und spezifische Empfehlungen können mal mehr oder mal weniger relevant für Sie sein. Zum Beispiel sind einige Sicherheitsempfehlungen möglicherweise weniger wichtig, wenn Ihre virtuellen Computer nicht mit dem Internet verbunden sind. Verschiedene Verfügbarkeitsempfehlungen können weniger relevant für Dienste sein, die Ad-hoc-Datensammlung und -Berichterstattung mit niedriger Priorität bereitstellen. Probleme, die für ein gewachsenes Unternehmen Relevanz haben, sind für ein Start-up ggf. weniger wichtig. Es empfiehlt sich zu ermitteln, welche Schwerpunktbereiche zu Ihren Prioritäten zählen, und dann zu beobachten, wie sich Ihre Wertungen mit der Zeit verändern.

Zu jeder Empfehlung gehört eine Anleitung, in der erläutert wird, warum die Empfehlung wichtig sein könnte. Sie sollten anhand dieser Anleitung feststellen, ob die Umsetzung der Empfehlung bei Berücksichtigung der Art Ihrer IT-Dienste und der geschäftlichen Anforderungen Ihrer Organisation für Sie geeignet ist.

## Befolgen von Empfehlungen anhand der Bewertung des Schwerpunktbereichs

Bevor Sie eine Assessment-Lösung in Microsoft Azure Operational Insights verwenden können, müssen Sie die Lösung installiert haben. Weitere Informationen zum Installieren von Lösungen finden Sie unter [Verwenden von Solution Gallery zum Hinzufügen oder Entfernen von Lösungen](operational-insights-add-solution.md). Nach der Installation können Sie die Zusammenfassung der Empfehlungen anzeigen, indem Sie in Operational Insights auf der Seite "Overview" die Kachel "Assessment" verwenden.

Sie können Bewertungen der Einhaltung für Ihre Infrastruktur zusammenfassen und dann Details in den Empfehlungen untersuchen.

![!Abbildung der Empfehlungen zu "SQL Assessment"](./media/operational-insights-assessment/gallery-ad-03.png)



### So werden Empfehlungen für einen Schwerpunktbereich angezeigt und korrigierende Maßnahmen ergriffen

1. Klicken Sie auf der Seite **Overview** auf die Kachel **Assessment** für Ihre Serverinfrastruktur.

2. Überprüfen Sie auf der Seite **Assessment** die Zusammenfassungsinformationen in einem der Schwerpunktbereiche, und klicken Sie dann auf einen Schwerpunktbereich, um Empfehlungen für diesen Bereich anzuzeigen.

3. Auf jeder der Schwerpunktbereichsseiten können Sie mit Prioritäten versehene Empfehlungen für Ihre Umgebung anzeigen. Klicken Sie auf eine Empfehlung, um Details dazu anzuzeigen, warum die Empfehlung erfolgt. Die Informationen werden dann unter **Affected Objects** angezeigt.

4. Ergreifen Sie die unter **Vorgeschlagene Aktionen** vorgeschlagenen Korrekturmaßnahmen. Nachdem das Element behandelt wurde, geben spätere Bewertungen an, dass empfohlene Aktionen ausgeführt wurden, und Ihre Bewertung der Einhaltung erhöht sich. Korrigierte Elemente werden als **Passed Objects** angezeigt.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->