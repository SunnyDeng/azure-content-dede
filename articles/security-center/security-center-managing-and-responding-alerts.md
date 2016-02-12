<properties
   pageTitle="Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center | Microsoft Azure"
   description="In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="yurid"/>

# Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center
In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
 Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Was sind Sicherheitswarnungen?
 Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftwareprogrammen und Firewalls, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste priorisierter Warnungen wird als Sicherheitswarnungen dargestellt.

Sie können die aktuellen Warnungen prüfen, indem Sie sich die Kachel **Sicherheitswarnungen** ansehen. Gehen Sie folgendermaßen vor, wenn Sie weitere Details zu jeder Warnung anzeigen möchten:

1. Auf dem Security Center-Dashboard sehen Sie die Kachel **Sicherheitswarnungen**.

    ![Die Kachel „Sicherheitswarnungen“ in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Klicken Sie auf das Symbol für Sicherheitswarnungen. Das Blatt **Sicherheitswarnungen** geöffnet und bietet weitere Details zu den Warnungen, wie unten dargestellt.

    ![Das Blatt „Sicherheitswarnungen“ in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Im unteren Teil dieses Blatts sind die Details für die einzelnen Warnungen zu sehen. Zum Sortieren klicken Sie auf die Spalte, nach der Sie sortieren möchten. Die Spalten sind wie folgt definiert:

- **Warnung**: eine kurze Erläuterung der Warnung.
- **Anzahl**: eine Liste aller Warnungen dieses bestimmten Typs, die an einem bestimmten Tag erkannt wurden.
- **Erkannt von**: der Dienst, der die Warnung ausgelöst hat.
- **Datum**: das Datum, an dem das Ereignis aufgetreten ist.
- **Status**: der aktuelle Status für diese Warnung. Es gibt zwei Statustypen:
    - **Aktiv**: Die Sicherheitswarnung wurde erkannt.
    - **Verworfen**: Die Sicherheitswarnung wurde vom Benutzer verworfen. Dieser Status wird üblicherweise für Warnungen verwendet, die untersucht, aber gemindert oder als harmlos eingestuft wurden.

- **Schweregrad**: der Schweregrad, der „Hoch“, „Mittel“ oder „Niedrig“ sein kann.

### Reagieren auf Sicherheitswarnungen
Viele Aktivitäten können auf einen möglichen Angriff auf Ihre Organisation hinweisen. Beispielsweise kann ein Netzwerkadministrator, der eine gesetzmäßige Netzwerkerfassung ausführt, so wahrgenommen werden wie eine Person, die eine Form von Angriff startet. In anderen Fällen könnte ein schlecht konfiguriertes System zu einigen falsch positiven Ergebnissen in einem Angriffserkennungssystem führen, wodurch echte Vorfälle schwerer zu entdecken sein können. Nachdem Sie die Sicherheitswarnungen mit Security Center überprüft haben, können Sie beginnen, Aktionen entsprechend dem Schweregrad einer Warnung auszuführen.

Um eine Aktion auszuführen, wählen Sie die Warnung aus, auf die Sie reagieren möchten. Auf der rechten Seite wird ein neues Blatt geöffnet, das weitere Details enthält (siehe folgende Abbildung).

![Reagieren auf Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

In diesem Fall bezieht sich die ausgelöste Warnung auf eine verdächtige RDP-Aktivität (Remote Desktop Protocol). Die erste Spalte zeigt, welche Ressourcen angegriffen wurden. In der zweiten Spalte wird die Uhrzeit aufgeführt, zu der dieser Angriff erkannt wurde. Die dritte Spalte zeigt den Status der Warnung, und die vierte Spalte den Schweregrad des Angriffs. Klicken Sie nach dem Überprüfen dieser Informationen auf die Ressource, die angegriffen wurde. Ein neues Blatt wird geöffnet und enthält Vorschläge zur weiteren Vorgehensweise, wie im folgenden Beispiel gezeigt.

![Vorschläge für die Bearbeitung von Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] Die von Security Center vorgeschlagene Wiederherstellung variiert je nach Sicherheitshinweis. In einigen Fällen müssen Sie möglicherweise weitere Azure-Funktionen verwenden, um die empfohlenen Lösungen zu implementieren. Beispielsweise besteht die Abhilfe für diesen Angriff darin, die IP-Adresse, die den Angriff generiert, durch Verwenden einer [Netzwerk-ACL](virtual-networks-acl.md) oder einer Regel für die [Netzwerksicherheitsgruppe](virtual-networks-nsg.md) auf die BlackList zu setzen.

### Verwalten von Sicherheitswarnungen
Sie können Warnungen nach Datum, Status und Schweregrad filtern. Klicken Sie auf dem Blatt **Sicherheitswarnungen** auf **Filter**, und aktivieren Sie die gewünschten Optionen (siehe folgende Abbildung).

![Filtern von Warnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Filtern von Warnungen kann nützlich für Szenarien sein, in denen Sie den Bereich der Warnungen einschränken müssen, die Sie im Dashboard anzeigen. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.

In den meisten Fällen sollten Sie die Empfehlungen in den Sicherheitshinweisen anwenden. Gelegentlich müssen Sie jedoch eine Warnung ignorieren, da es sich um einen falschen Alarm für Ihre Umgebung oder ein erwartetes Verhalten für eine bestimmte Ressource handelt. Unabhängig von den Umständen können Sie Empfehlungen für eine bestimmte Ressource mithilfe der Option **Verwerfen** ausblenden.

Klicken Sie mit der rechten Maustaste auf die Aufgabe, um sie zu verwerfen. Danach wird die Option **Verwerfen** ähnlich wie in der folgenden Abbildung angezeigt.

![Die Option „Verwerfen“ in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

In einer Auflistung von Warnungen gilt für jede Warnung eine sehr spezielle Beschreibung und Abhilfe. Die Warnungen, die in Security Center angezeigt werden, basieren auf dem Angriffsszenario. Warnungen zu den nachfolgenden Angriffsszenarien werden durch das Microsoft-Modul ausgelöst:

- **Brute-Force-Erkennung über Netzwerkdaten**: Basieren auf Machine Learning-Modellen, die aus Netzwerkdaten lernen.
- **Brute-Force-Erkennung über Endpunktdaten**: Basieren auf Security Center-Abfragen von Computerprotokollen. Dadurch lässt sich zwischen fehlerhaften und erfolgreichen Versuchen unterscheiden.
- **Virtuelle Computer, die mit böswilligen IPs kommunizieren**: Basieren darauf, dass Security Center Computer erkennt, die mit Bots kompromittiert sind und mit ihren Command-and-Control-Servern (C&C) kommunizieren.

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Security Center konfigurieren können. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0204_2016-->