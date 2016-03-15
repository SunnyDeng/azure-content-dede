<properties
   pageTitle="Einführung in Azure Security Center | Microsoft Azure"
   description="Sie erhalten Informationen zu Azure Security Center, zu dessen wichtigsten Funktionen und dazu, wie es funktioniert."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Einführung in Azure Security Center

Sie erhalten Informationen zu Azure Security Center, zu dessen wichtigsten Funktionen und dazu, wie es funktioniert.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Der Dienst wird anhand einer Beispielbereitstellung vorgestellt.

## Was ist Azure Security Center?
 Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

##	Wichtige Funktionen
 Security Center stellt benutzerfreundliche und effektive Funktionen bereit, die in Azure integriert sind und verwendet werden können, um Bedrohungen zu verhindern, zu erkennen und darauf zu reagieren. Wichtige Funktionen sind:

| | |
|----- |-----|
| Verhindern | Überwacht den Sicherheitsstatus Ihrer Azure-Ressourcen |
| | Definiert Richtlinien für Ihre Azure-Abonnements und -Ressourcengruppen anhand der Sicherheitsanforderungen Ihres Unternehmens, des Typs der genutzten Anwendungen und der Vertraulichkeit Ihrer Daten |
| | Verwendet richtliniengesteuerte Sicherheitsempfehlungen, um Dienstbesitzer durch das Implementieren erforderlicher Sicherheitsmechanismen zu führen |
| | Stellt schnell Sicherheitsdienste und Appliances von Microsoft und Partnern bereit |
| Erkennen |Erfasst und analysiert automatisch Sicherheitsdaten aus Ihren Azure-Ressourcen, aus dem Netzwerk und aus Partnerlösungen wie Antischadsoftware und Firewalls |
| | Nutzt die globale Bedrohungsintelligenz von Microsoft-Produkten und -Diensten, von der Microsoft Digital Crimes Unit (DCU), vom Microsoft Security Response Center (MSRC) sowie von externen Feeds |
| | Wendet erweiterte Analysen an, einschließlich Machine Learning und Verhaltensanalyse |
| Reagieren | Stellt priorisierte Sicherheitszwischenfälle/-warnungen bereit |
| | Bietet Einblicke in die Quelle des Angriffs und in betroffene Ressourcen |
| | Schlägt Möglichkeiten vor, den aktuellen Angriff zu beenden und zukünftige Angriffe zu verhindern |

## Einführende exemplarische Vorgehensweise
 Sie können über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) auf Security Center zugreifen. [Melden Sie sich am Portal an](https://portal.azure.com), wählen Sie **Durchsuchen** aus, und scrollen Sie zur Option **Security Center**. Oder wählen Sie die Kachel **Security Center** aus, die Sie zuvor auf dem Portal-Dashboard angeheftet haben.

![Kachel „Sicherheit“ im Azure-Portal][1]

In Security Center können Sie Sicherheitsrichtlinien festlegen, Sicherheitskonfigurationen überwachen und Sicherheitswarnungen anzeigen.

### Sicherheitsrichtlinien

Sie können Richtlinien für Ihre Azure-Abonnements und Ressourcengruppen gemäß den Sicherheitsanforderungen Ihres Unternehmens definieren. Außerdem können Sie sie genau an die Arten der genutzten Anwendungen und an die Empfindlichkeit der Daten jedes Abonnements anpassen. Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, unter Umständen Sicherheitsanforderungen, die sich von denen für Produktionsanwendungen unterscheiden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen, eine höhere Sicherheitsstufe erforderlich sein.

> [AZURE.NOTE] Um eine Sicherheitsrichtlinie auf Abonnementebene oder Ressourcengruppenebene zu ändern, müssen Sie der Besitzer des Abonnements oder ein Mitwirkender sein.

Klicken Sie auf dem Blatt **Security Center** auf die Kachel **Sicherheitsrichtlinie**, um eine Liste Ihrer Abonnements und Ressourcengruppen anzuzeigen.

![Blatt „Security Center“][2]

Wählen Sie auf dem Blatt **Sicherheitsrichtlinie** ein Abonnement aus, um Einzelheiten zur Richtlinie anzuzeigen.

![Blatt „Security Center“, Abonnement][3]

**Datensammlung** (siehe oben) aktiviert die Datensammlung für eine Sicherheitsrichtlinie. Die Aktivierung bietet Folgendes:

- Die tägliche Überprüfung aller unterstützten virtuellen Computer für die Sicherheitsüberwachung und Empfehlungen
- Das Sammeln von Sicherheitsereignissen für die Analyse und Bedrohungserkennung

Durch **Ein Speicherkonto pro Region auswählen** (siehe oben) können Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto auswählen, in dem Daten dieser virtuellen Computer gespeichert werden. Wenn Sie kein Speicherkonto für die einzelnen Regionen auswählen, wird ein Speicherkonto für Sie erstellt. Die gesammelten Daten werden aus Sicherheitsgründen logisch von anderen Kundendaten getrennt.

> [AZURE.NOTE] Die Datensammlung und die Auswahl eines Speicherkontos pro Region werden auf Abonnementebene konfiguriert.

**Empfehlungen anzeigen für** (siehe oben) ermöglicht es Ihnen, die Sicherheitsmechanismen auszuwählen, die Sie entsprechend den Sicherheitsanforderungen der Ressourcen im Abonnement überwachen und empfehlen möchten.

Wählen Sie anschließend eine Ressourcengruppe aus, um Einzelheiten zur Richtlinie anzuzeigen.

![Blatt „Security Center“, Ressourcengruppe][4]

Mit **Vererbung** (siehe oben) können Sie die Ressourcengruppe wie folgt definieren:

- „Geerbt“ (Standardeinstellung) bedeutet, dass alle Sicherheitsrichtlinien für diese Ressourcengruppe von der Abonnementebene geerbt werden.
- „Eindeutig“ bedeutet, dass die Ressourcengruppe eine benutzerdefinierte Sicherheitsrichtlinie hat. Sie müssen eventuelle Änderungen unter **Empfehlungen anzeigen für** vornehmen.

> [AZURE.NOTE] Bei einem Konflikt zwischen der Richtlinie auf Abonnementebene und der Richtlinie auf Ressourcengruppenebene hat die Richtlinie auf Ressourcengruppenebene Vorrang.

### Sicherheitsempfehlungen

 Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anhand einer Liste mit Empfehlungen werden Sie durch den Prozess des Konfigurierens der erforderlichen Sicherheitsmechanismen geführt. Beispiele:

- Bereitstellung von Antischadsoftware, um Schadsoftware zu erkennen und zu entfernen
- Konfigurieren von Netzwerksicherheitsgruppen und Regeln, um Datenverkehr zu virtuellen Maschinen zu steuern
- Bereitstellung von Web Application Firewalls als Schutz vor Angriffen, die auf Ihre Webanwendungen abzielen
- Bereitstellen von fehlenden Systemupdates
- Behandeln von Betriebssystemkonfigurationen, die nicht den empfohlenen Basisregeln entsprechen

Klicken Sie auf die Kachel **Empfehlungen**, um eine Liste mit Empfehlungen zu erhalten. Klicken Sie auf eine Empfehlung, um weitere Informationen anzuzeigen oder Schritte zur Behebung des Problems auszuführen.

![Sicherheitsempfehlungen in Azure Security Center][5]

### Ressourcenintegrität

Auf der Kachel **Ressourcenintegrität** wird der Gesamtsicherheitsstatus der Umgebung nach Ressourcentyp angezeigt, z. B. virtuelle Computer, Webanwendungen und weitere Ressourcen.

Wählen Sie auf der Kachel **Ressourcenintegrität** einen Ressourcentyp aus, um weitere Informationen anzuzeigen, einschließlich einer Liste aller potenziellen Sicherheitsrisiken, die erkannt wurden. (Unten im Beispiel ist **Virtuelle Computer** ausgewählt.)

![Kachel „Ressourcenintegrität“][6]

### Sicherheitswarnungen

 Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls. Wurden Bedrohungen erkannt, wird eine Sicherheitswarnung erstellt. Beispiele für eine Erkennung sind:

- Gefährdete virtuelle Computer, die mit bekannten bösartigen IP-Adressen kommunizieren
- Erweiterte Schadsoftware, die mithilfe der Windows-Fehlerberichterstattung erkannt wurde
- Brute-Force-Angriffe gegen virtuelle Computer
- Sicherheitswarnungen von integrierter Antischadsoftware und integrierten Firewalls

Durch Klicken auf die Kachel **Sicherheitswarnungen** wird eine Liste priorisierter Warnungen angezeigt.

![Sicherheitswarnungen][7]

Wenn Sie eine Warnung auswählen, werden weitere Informationen zum Angriff sowie Vorschläge angezeigt, wie darauf reagiert werden kann.

![Details der Sicherheitswarnung][8]

## Erste Schritte
Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Security Center wird mit Ihrem Azure-Abonnement aktiviert. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.

 Sie können über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) auf Security Center zugreifen. Weitere Informationen finden Sie in der [Dokumentation zum Portal](https://azure.microsoft.com/documentation/services/azure-portal/).

Unter [Erste Schritte mit Azure Security Center](security-center-get-started.md) werden Sie schnell durch die Komponenten zur Sicherheitsüberwachung und Richtlinienverwaltung von Security Center geführt.

## Nächste Schritte
In diesem Dokument wurden Security Center, seine wichtigsten Funktionen sowie die ersten Schritte zu seiner Nutzung vorgestellt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](security-center-policies.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog:](http://blogs.msdn.com/b/azuresecurity/) enthält Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0309_2016-->