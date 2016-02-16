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
   ms.topic="hero-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="yurid"/>

# Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center
In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
 Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Was sind Sicherheitswarnungen?
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, aus dem Netzwerk und von integrierter Antischadsoftware und von Firewalls, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen, z. B. Warnungen integrierter Partnerlösungen, wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen von Problemen benötigen. Außerdem sind Empfehlungen zum Reagieren auf einen Angriff vorhanden. Microsoft-Sicherheitsexperten analysieren ständig neue weltweite Bedrohungen, z. B. neue Angriffsmuster und Trends, die für die unternehmenseigenen Verbraucher- und Unternehmensprodukte und Onlinedienste auftreten. Für Security Center können so die Erkennungsalgorithmen aktualisiert werden, wenn neue Sicherheitsrisiken und Exploits erkannt werden, und Kunden können über die sich verändernde Bedrohungslage informiert werden. Beispiele für Arten von Bedrohungen, die mit Security Center erkannt werden können:

- **Brute-Force-Erkennung über Netzwerkdaten**: Hierbei werden Machine Learning-Modelle verwendet, die typische Muster von Netzwerkdatenverkehr für Ihre Anwendungen verstehen. Dies ermöglicht eine effektivere Erkennung von Zugriffsversuchen, die nicht von berechtigten Benutzern durchgeführt werden, sondern zu unlauteren Zwecken.
- **Brute-Force-Erkennung über Endpunktdaten**: Basiert auf der Analyse von Computerprotokollen. Dies ermöglicht eine Unterscheidung zwischen fehlerhaften und erfolgreichen Versuchen.
- **Virtuelle Maschinen, die mit schädlichen IPs kommunizieren**: Hierbei wird Netzwerkdatenverkehr mit der globalen Bedrohungsintelligenz von Microsoft verglichen. Es werden Computer erkannt, die gefährdet sind und mit Command-and-Control-Servern (C&C) kommunizieren (und umgekehrt).
- **Gefährdete VMs**: Basiert auf der Verhaltensanalyse von Computerprotokollen und der Korrelation mit anderen Signalen. Es werden anomale Ereignisse erkannt, die mit hoher Wahrscheinlichkeit das Ergebnis einer Computermanipulation und Ausnutzung von Sicherheitslücken sind.

## Verwalten von Sicherheitswarnungen 

Sie können die aktuellen Warnungen prüfen, indem Sie sich die Kachel **Sicherheitswarnungen** ansehen. Gehen Sie folgendermaßen vor, wenn Sie weitere Details zu jeder Warnung anzeigen möchten:

1. Auf dem Security Center-Dashboard sehen Sie die Kachel **Sicherheitswarnungen**.

    ![Die Kachel „Sicherheitswarnungen“ in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Klicken Sie auf die Kachel, um das Blatt **Sicherheitswarnungen** zu öffnen, das weitere Details zu den Warnungen enthält (siehe Abbildung unten).

    ![Das Blatt „Sicherheitswarnungen“ in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Im unteren Teil dieses Blatts sind die Details für die einzelnen Warnungen zu sehen. Zum Sortieren klicken Sie auf die Spalte, nach der Sie sortieren möchten. Die Spalten sind wie folgt definiert:

- **Warnung**: Eine kurze Erläuterung der Warnung.
- **Anzahl**: Eine Liste aller Warnungen dieses bestimmten Typs, die an einem bestimmten Tag erkannt wurden.
- **Erkannt von**: Der Dienst, der die Warnung ausgelöst hat.
- **Datum**: Das Datum, an dem das Ereignis aufgetreten ist.
- **Status**: Der aktuelle Status für diese Warnung. Es gibt zwei Statustypen:
    - **Aktiv**: Die Sicherheitswarnung wurde erkannt.
    - **Verworfen**: Die Sicherheitswarnung wurde vom Benutzer verworfen. Dieser Status wird üblicherweise für Warnungen verwendet, die untersucht, aber gemindert oder als harmlos eingestuft wurden.

- **Schweregrad**: Gibt den Schweregrad an („Hoch“, „Mittel“ oder „Niedrig“).

Sie können Warnungen nach Datum, Status und Schweregrad filtern. Das Filtern von Warnungen kann nützlich für Szenarien sein, in denen Sie den Bereich der angezeigten Warnungen einschränken müssen. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.

1. Klicken Sie auf dem Blatt **Sicherheitswarnungen** auf **Filter**. Das Blatt **Filter** wird geöffnet. Sie können Werte für Datum, Status und Schweregrad auswählen, die Sie anzeigen möchten.

	![Filtern von Warnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	Nach dem Untersuchen einer Sicherheitswarnung stellen Sie unter Umständen fest, dass es sich um falsch positive Warnung für Ihre Umgebung handelt oder ein erwartetes Verhalten für eine bestimmte Ressource darstellt. Falls Sie feststellen, dass eine Sicherheitswarnung nicht zutrifft, können Sie die Warnung immer verwerfen und dann aus der Ansicht herausfiltern. Es gibt zwei Möglichkeiten, eine Sicherheitswarnung zu verwerfen. Klicken Sie mit der rechten Maustaste auf eine Warnung, und wählen Sie **Verwerfen**, oder zeigen Sie auf ein Element, klicken Sie auf die drei Punkte, die rechts angezeigt werden, und wählen Sie **Verwerfen**. Sie können verworfene Sicherheitswarnungen anzeigen, indem Sie auf **Filter** klicken und **Verworfen** auswählen.

	![Filtern von Warnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Reagieren auf Sicherheitswarnungen
Wählen Sie eine Sicherheitswarnung aus, um weitere Informationen zu den Ereignissen zu erhalten, die die Warnung ausgelöst haben, sowie zu den Schritten, die Sie als Reaktion auf den Angriff ausführen müssen (falls zutreffend). Sicherheitswarnungen werden nach Typ und Datum gruppiert. Wenn Sie auf eine Sicherheitswarnung klicken, wird ein Blatt mit einer Liste der gruppierten Warnungen geöffnet.

![Reagieren auf Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

In diesem Fall bezieht sich die ausgelöste Warnung auf eine verdächtige RDP-Aktivität (Remote Desktop Protocol). Die erste Spalte zeigt, welche Ressourcen angegriffen wurden. In der zweiten Spalte wird die Uhrzeit aufgeführt, zu der dieser Angriff erkannt wurde. Die dritte Spalte zeigt den Status der Warnung, und die vierte Spalte den Schweregrad des Angriffs. Klicken Sie nach dem Überprüfen dieser Informationen auf die Ressource, die angegriffen wurde. Ein neues Blatt wird geöffnet und enthält Vorschläge zur weiteren Vorgehensweise, wie im folgenden Beispiel gezeigt.

![Vorschläge für die Bearbeitung von Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8.png)

> [AZURE.NOTE] Die von Security Center vorgeschlagene Wiederherstellung variiert je nach Sicherheitshinweis. In einigen Fällen müssen Sie möglicherweise weitere Azure-Funktionen verwenden, um die empfohlenen Lösungen zu implementieren. Beispielsweise kann die Abhilfe für diesen Angriff darin bestehen, die IP-Adresse, die den Angriff generiert, durch Verwenden einer [Netzwerk-ACL](virtual-networks-acl.md) oder einer Regel für die [Netzwerksicherheitsgruppe](virtual-networks-nsg.md) auf eine Negativliste zu setzen.


## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Security Center konfigurieren können. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0211_2016-->