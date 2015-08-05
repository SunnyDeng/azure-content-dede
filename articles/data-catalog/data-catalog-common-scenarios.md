<properties
   pageTitle="Häufige Szenarios mit Azure Data Catalog"
   description="Häufige Szenarios mit Azure Data Catalog: Registrieren, Erweitern, Untersuchen, Verstehen und Nutzen von Datenquellen und Entfernen der Metadaten von Datenquellen."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/> <tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>


# Häufige Szenarios mit Azure Data Catalog

In diesem Artikel werden häufige Szenarios vorgestellt, bei denen Azure Data Catalog Organisationen dabei unterstützt, mehr Nutzen aus den vorhandenen Datenquellen zu ziehen.

## Szenario 1: Registrierung der zentralen Datenquellen

Organisationen verfügen häufig über Datenquellen mit hohem Wert. Diese Datenquellen umfassen OLTP-Branchensysteme, Data Warehouses und Business Intelligence-/Analysedatenbanken. In vielen Fällen nehmen die Anzahl der Systeme und die Überlappung zwischen Systemen im Laufe der Zeit zu, wenn sich die Anforderungen des Unternehmens ändern und sich das Unternehmen selbst aufgrund von Übernahmen und Akquisitionen weiterentwickelt.

Häufig ist es für Benutzer schwierig zu erkennen, wo die Daten innerhalb dieser Datenquellen zu finden sind. Folgende Fragen sind keine Seltenheit:

- Welches der drei Personalverwaltungssysteme, die im Unternehmen verwendet werden, soll ich zum Erstellen dieser Art von Bericht einsetzen?
- Wo finde ich die zertifizierten Verkaufszahlen für das gerade abgelaufene Geschäftsjahr?
- Wen soll ich fragen oder welchen Prozess soll ich verwenden, um Zugriff auf das Data Warehouse zu erhalten?
- Ich weiß nicht, ob diese Zahlen korrekt sind. Von wem erhalte ich Informationen dazu, wie diese Daten verwendet werden sollen, bevor ich dieses Dashboard für mein Team freigebe?

In diesen Fällen ist Azure Data Catalog hilfreich. Die zentralen, wertvollen und von IT-Experten verwalteten Datenquellen, die in der Organisation verwendet werden, sind häufig der logische Ausgangspunkt zum Auffüllen des Katalogs. Jeder Benutzer kann eine Datenquelle registrieren. Aber wenn der Katalog in Verbindung mit den Datenquellen eingeführt wird, die für die überwiegende Anzahl von Benutzern einen hohen Nutzen darstellen, fördert dies die Akzeptanz und Verwendung des Systems. Für Kunden, die Azure Data Catalog neu einführen, kann das Identifizieren und Registrieren der wichtigsten Datenquellen, die von vielen unterschiedlichen Teams mit Datennutzern verwendet werden, der erste Schritt zu einer erfolgreichen Nutzung sein.

## Szenario 2: Self-Service-Business Intelligence

Auch wenn herkömmliche Business Intelligence-Lösungen weiterhin wertvoller Bestandteil vieler Datenlandschaften von Unternehmen sind, hat die immer stärkere Beschleunigung der Geschäftstätigkeit dazu geführt, dass Self-Service-Business Intelligence immer wichtiger wird. Mit Self-Service-BI können Information-Worker und Analysten eigene Berichte, Arbeitsmappen und Dashboards erstellen, ohne dafür ein zentrales IT-Team in Anspruch nehmen zu müssen – bzw. durch die Zeitplanung und Verfügbarkeit des IT-Teams ausgebremst zu werden.

In Self-Service-BI-Szenarios kommt es häufig vor, dass Benutzer Daten aus mehreren Quellen kombinieren, von denen viele vorher noch nicht für BI und zur Analyse verwendet wurden. Obwohl einige dieser Datenquellen unter Umständen bereits bekannt sind, muss häufig erst ermittelt werden, welche Schritte ausgeführt werden müssen, um potenzielle Datenquellen für eine bestimmte Aufgabe zu finden und auszuwerten.

Dieser Ermittlungsprozess wird häufig noch manuell durchgeführt: Analysten nutzen Peer-Netzwerkverbindungen, um andere Personen zu identifizieren, die mit den gewünschten Daten arbeiten. Sobald eine Datenquelle gefunden wurde, kann sie verwendet werden. Aber der Prozess wiederholt sich dann für jeden weiteren Self-Service-BI-Vorgang, und mehrere Benutzer führen den gleichen redundanten manuellen Ermittlungsprozess durch.

Mit Azure Data Catalog können Benutzer diesen redundanten Kreislauf durchbrechen. Nachdem eine Datenquelle auf herkömmliche Weise ermittelt wurde, kann ein Analyst die Datenquelle registrieren, damit sie in Zukunft leichter gefunden werden kann. Der Benutzer kann den Nutzen weiter erhöhen, indem er die registrierten Datenressourcen mit Anmerkungen versieht. Dies muss aber nicht zum Zeitpunkt der Registrierung passieren. Benutzer können je nach Arbeitsaufwand im Laufe der Zeit ihren Beitrag leisten und den Nutzen der im Katalog registrierten Datenquellen nach und nach weiter steigern.

Dieses Anwachsen des Kataloginhalts stellt die natürliche Erweiterung nach der anfänglichen Registrierung der zentralen Datenquellen dar. Das Vorabauffüllen des Katalogs mit Daten, die von vielen Benutzern benötigt werden, kann im Hinblick auf die erste Nutzung und Ermittlungstätigkeit motivierend wirken. Wenn Benutzer weitere Quellen registrieren und mit Anmerkungen versehen können, werden sie und ihre Kollegen bei der Stange gehalten.

Beachten Sie, dass auch für umfangreiche BI-Projekte von Unternehmen die gleichen Muster und Anforderungen gelten, obwohl es bei diesem Szenario speziell um Self-Service-BI geht. Alle Vorgänge, die einen manuellen Prozess zur Ermittlung von Datenquellen umfassen, sind Vorgänge, für die mit Azure Data Catalog der Nutzen für die Organisation erhöht werden kann.

## Szenario 3: Erfassen von „Stammeswissen“

Woher wissen Sie, welche Daten Sie für Ihre Arbeit benötigen und wo Sie diese Daten finden?

Wenn Sie Ihren Job schon etwas länger machen, ist Ihnen dies wahrscheinlich bekannt. Sie haben einen allmählichen Lernprozess durchlaufen und gelernt, welche Datenquellen für Ihre tägliche Arbeit wichtig sind.

Wenn Ihr Team um eine neue Mitarbeiterin oder einen neuen Mitarbeiter erweitert wird, stellt sich die Frage, woher diese Person weiß, welche Daten sie benötigt und wo sie zu finden sind.

Wahrscheinlich fragt sie Sie einfach.

Diese ständige Weitergabe des „Stammeswissens“ ist Teil des Datenquellen-Ermittlungsprozesses in großen und kleinen Unternehmen. Länger angestellte und erfahrenere Teammitglieder haben sich ihr Wissen im Laufe der Jahre aufgebaut, und neuere Teammitglieder haben gelernt, dass sie sich mit Fragen an diese Kollegen wenden können. Die wichtigsten Informationen sind häufig nur in den Köpfen weniger zentraler Personen vorhanden, und wenn diese Personen im Urlaub sind oder das Team verlassen, wirkt sich dies für das Unternehmen negativ aus.

Es kommt vor, dass diese Datenexperten ihr Wissen dokumentieren und per E-Mail oder in Word-Dokumenten auf einer SharePoint-Website des Teams bereitstellen. Dies ist zwar hilfreich, aber damit werden gleichzeitig neue Ermittlungsprobleme geschaffen: Woher wissen die Mitarbeiter, welche Dokumentation vorhanden ist und wo sie sich befindet?

Azure Data Catalog ist ein Speicherort zum Bereitstellen dieses Stammeswissens, an dem es leicht auffindbar ist. Datenexperten können die Datenressourcen direkt mit Anmerkungen versehen und Links zur vorhandenen Dokumentation einfügen. Hierdurch wird nicht nur das Wissen selbst erfasst, sondern es wird auch in derselben Umgebung angeordnet, die für die Ermittlung von Datenquellen verwendet wird. Wenn der Katalog zum Ermitteln einer Datenquelle verwendet wird, finden Benutzer nicht nur die eigentliche Quelle, sondern auch das Expertenwissen, dass sich vorher nur im Kopf des Experten befunden hat.

<!---HONumber=July15_HO4-->