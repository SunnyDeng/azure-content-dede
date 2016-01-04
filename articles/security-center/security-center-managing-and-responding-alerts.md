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
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center
In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Was sind Sicherheitswarnungen?
Azure Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste priorisierter Warnungen wird als „Sicherheitswarnungen“ dargestellt.

Sie können die aktuellen Warnungen prüfen, indem Sie sich die Kachel „Sicherheitswarnungen“ ansehen. Gehen Sie folgendermaßen vor, wenn Sie weitere Details zu jeder Warnung anzeigen möchten:

1. Auf dem **Azure Security Center**-Dashboard sehen Sie die Kachel **Sicherheitswarnungen**. 

    ![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Klicken Sie in der Grafik auf das Sicherheitswarnungsvorkommen. Daraufhin wird das Blatt **Sicherheitswarnungen** mit weiteren Details zu dieser Warnung geöffnet (siehe folgende Abbildung):
    
    ![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Im unteren Teil dieses Blatts sind die Details für jede Warnung zu sehen. Sie können die Liste der Warnungen nach jeder Spalte sortieren, indem Sie auf die gewünschte Spalte klicken. Die Spalten sind wie folgt definiert:

- **Warnung:** eine kurze Erläuterung der Warnung.
- **Anzahl:** die Anzahl aller Warnungen dieses bestimmten Typs, die an einem bestimmten Tag erkannt wurden.
- **Erkannt von:** der Dienst, der die Warnung ausgelöst hat.
- **Datum:** das Datum, an dem Ereignis aufgetreten ist.
- **Status:** Zeigt den aktuellen Status für diese Warnung. Es gibt zwei Statustypen:
    - **Aktiv:** Die Sicherheitswarnung wurde erkannt.
    - **Verworfen:** Die Sicherheitswarnung wurde vom Benutzer verworfen. Dieser Status wird üblicherweise für Warnungen verwendet, die untersucht wurden, für die aber festgestellt wurde, dass sie kein tatsächlicher Angriff sind, oder die untersucht und verworfen wurden.

- **Schweregrad:** Zeigt den Schweregrad an, der „Hoch“, „Mittel“ oder „Niedrig“ sein kann.
  

### Reagieren auf Sicherheitswarnungen
Viele Aktivitäten können auf einen möglichen Angriff auf Ihre Organisation hinweisen. Beispielsweise kann ein Netzwerkadministrator, der eine gesetzmäßige Netzwerkerfassung ausführt, so wahrgenommen werden wie eine Person, die eine Form von Angriff startet. In anderen Fällen könnte ein schlecht konfiguriertes System zu einigen falsch positiven Ergebnissen in einem Angriffserkennungssystem führen, wodurch es erschwert werden könnte, echte Vorfälle zu entdecken. Nachdem Sie die Sicherheitswarnungen mit Azure Security Center überprüft haben, können Sie beginnen, Aktionen entsprechend dem Schweregrad der jeweiligen Warnung auszuführen.

Um eine Aktion auszuführen, wählen Sie die Warnung aus, auf die Sie reagieren möchten. Auf der rechten Seite wird ein neues Blatt geöffnet, das mehr Details enthält (siehe folgende Abbildung):

![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

In diesem Fall bezieht sich die ausgelöste Warnung auf eine verdächtige RDP-Aktivität (Remote Desktop Protocol). Die erste Spalte zeigt, welche Ressourcen angegriffen wurden. In den weiteren Spalten werden die Uhrzeit, zu der dieser Angriff erkannt wurde, der Status der Warnung und der Schweregrad angezeigt. Nachdem Sie diese Informationen gelesen haben, klicken Sie auf die Ressource, die angegriffen wurde. Es wird ein neues Blatt mit weiteren Vorschlägen dazu geöffnet, wie Ihre nächsten Schritte aussehen sollten (siehe nächste Abbildung):

![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]Die von Azure Security Center vorgeschlagenen Abhilfemaßnahmen sind je nach Sicherheitswarnung unterschiedlich, und in einigen Fällen müssen Sie möglicherweise weitere Azure-Funktionen verwenden, um die empfohlenen Abhilfemaßnahmen zu implementieren. Beispielsweise besteht die Abhilfe für diesen Angriff darin, die IP-Adresse, die den Angriff generiert, durch Verwenden einer [Netzwerk-ACL](virtual-networks-acl.md) oder einer Regel für die [Netzwerksicherheitsgruppe](virtual-networks-nsg.md) auf die BlackList zu setzen.

### Verwalten von Sicherheitswarnungen
Sie können Warnungen nach Datum, Status und Schweregrad filtern. Klicken Sie unter dem Blatt „Sicherheitswarnungen“ auf „Filter“, und aktivieren Sie die gewünschten Optionen (siehe folgende Abbildung):

![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Filtern von Warnungen kann nützlich für Szenarien sein, in denen Sie den Bereich der Warnungen einschränken müssen, die Sie im Dashboard anzeigen. Es könnte beispielsweise sein, dass Sie, weil Sie eine mögliche Sicherheitsverletzung untersuchen, die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind. Während es für die meisten Sicherheitswarnungen Empfehlungen gibt, die angewendet werden sollten, müssen Sie in einigen Fällen möglicherweise eine Warnung ignorieren, da sie für Ihre Umgebung ein falsch positives Ergebnis oder ein erwartetes Verhalten für eine bestimmte Ressource ist. Unabhängig von den Umständen können Sie Empfehlungen für eine bestimmte Ressource mithilfe der Option **Verwerfen** ausblenden.

Um eine Aufgabe zu verwerfen, klicken Sie auf die Ressource, um weitere Informationen anzuzeigen oder die empfohlene Konfiguration anzuwenden. Klicken Sie mit der rechten Maustaste auf die Aufgabe, um sie zu verwerfen. Danach wird die Option **Verwerfen** ähnlich wie in der folgenden Abbildung angezeigt:

![Aktivieren von Datensammlung](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

In einer Auflistung von Warnungen hat jede Warnung eine sehr spezielle Beschreibung und Abhilfe. Die Warnungen, die Sie in Azure Security Center sehen, basieren auf dem Angriffsszenario. Die nachfolgenden Angriffsszenarien werden durch das Microsoft-Modul ausgelöst:

- **Brute-Force-Erkennung über Netzwerkdaten:** Diese Erkennungen basieren auf Machine Learning-Modellen, die aus Netzwerkdaten lernen. 
- **Brute-Force-Erkennung über Endpunktdaten:** Diese Erkennungen basieren auf Azure Security Center-Abfragen von Computerprotokollen. Dadurch lässt sich zwischen fehlgeschlagenen und erfolgreichen Versuchen unterscheiden. 
- **Virtuelle Computer, die mit böswilligen IPs kommunizieren:** Diese Erkennungen basieren darauf, dass Azure Security Center Computer erkennt, die mit Bots kompromittiert sind und mit ihren Command-and-Control-Servern (C&C) kommunizieren. 

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_1217_2015-->