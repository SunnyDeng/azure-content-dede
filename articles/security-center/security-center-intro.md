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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Einführung in Azure Security Center

Sie erhalten Informationen zu Azure Security Center, zu dessen wichtigsten Funktionen und dazu, wie es funktioniert.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Dies ist eine Einführung in den Dienst anhand einer Beispielbereitstellung.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

##	Wichtige Funktionen
Azure Security Center stellt einfache und effektive, in Azure integrierte Funktionen zum Verhindern von, Erkennen von und Reagieren auf Bedrohungen bereit. Wichtige Funktionen sind:

| | |
|----- |-----|
| Verhindern | Überwacht den Sicherheitsstatus Ihrer Azure-Ressourcen |
| | Definiert Richtlinien für Ihre Azure-Abonnements anhand der Sicherheitsanforderungen Ihres Unternehmens und des Typs der Anwendungen oder der Vertraulichkeit Ihrer Daten |
| | Verwendet richtliniengesteuerte Sicherheitsempfehlungen, um Dienstbesitzer durch das Implementieren erforderlicher Sicherheitsmechanismen zu führen |
| | Schnell stellt Sicherheitsdienste und Appliances von Microsoft und Partnern bereit |
| Erkennen | Erfasst und analysiert automatisch Sicherheitsdaten aus Ihren Azure-Ressourcen, aus dem Netzwerk und aus Partnerlösungen wie Antischadsoftware und Firewalls |
| | Nutzt globale Bedrohungsintelligenz von Microsoft-Produkten und -Diensten, von Centern zur Bekämpfung digitaler Verbrechen und Reaktion auf Vorfälle sowie von externen Feeds |
| | Wendet erweiterte Analysen an, einschließlich Machine Learning und Verhaltensanalyse |
| Reagieren | Stellt priorisierte Sicherheitszwischenfälle/-warnungen bereit |
| | Bietet Einblicke in die Quelle des Angriffs und in betroffene Ressourcen |
| | Schlägt Möglichkeiten vor, den aktuellen Angriff zu beenden und zukünftige Angriffe zu verhindern |

## Einführende exemplarische Vorgehensweise
Der Zugriff auf Azure Security Center erfolgt über das [Azure-Portal](http://azure.microsoft.com/features/azure-portal/). Dazu [melden Sie sich beim Portal an](https://portal.azure.com), wählen Sie **Durchsuchen** aus, und scrollen Sie zur Option **Security Center**, oder wählen Sie die Kachel **Security Center** aus, die Sie zuvor auf dem Portal-Dashboard angeheftet haben.

![][1]

Aus Azure Security Center können Sie Sicherheitsrichtlinien festlegen, Sicherheitskonfigurationen überwachen und Sicherheitswarnungen anzeigen.

### Sicherheitsrichtlinien

Sie können Richtlinien für Ihre Azure-Abonnements definieren, die den Sicherheitsanforderungen Ihres Unternehmens entsprechen und auf den Typ der Anwendungen oder die Vertraulichkeit der Daten in den verschiedenen Abonnements abgestimmt sind. Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, möglicherweise Sicherheitsanforderungen, die sich von denen unterscheiden, die für Produktionsanwendungen verwendet werden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen, eine höhere Sicherheitsstufe erforderlich sein.

> [AZURE.NOTE]Damit Sie eine Sicherheitsrichtlinie bearbeiten können, müssen Sie ein Besitzer oder Mitwirkender des Abonnements sein.

Klicken Sie auf die Kachel **Sicherheitsrichtlinie**, um eine Liste Ihrer Abonnements zu erhalten, und wählen Sie ein Abonnement aus, um die Richtliniendetails anzuzeigen.

![][2]

**Datensammlung** (siehe oben) aktiviert die Datensammlung für eine Sicherheitsrichtlinie. Ein Aktivieren bietet: tägliche Überprüfung aller unterstützten virtuellen Computer hinsichtlich Sicherheitsüberwachung und -empfehlungen; Sammeln von Sicherheitsereignissen zur Analyse und Bedrohungserkennung.

**Empfehlungen anzeigen für:** (siehe oben) Ermöglicht es Ihnen, die Sicherheitsmechanismen zu wählen, die Sie entsprechend den Sicherheitsanforderungen der Ressourcen im Abonnement überwachen und empfehlen möchten.

### Sicherheitsempfehlungen

Azure Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anhand einer Liste mit Empfehlungen werden Sie durch den Prozess des Konfigurierens der erforderlichen Sicherheitsmechanismen geführt. Beispiele:

- Bereitstellung von Antischadsoftware, um bösartige Software zu erkennen und zu entfernen
- Konfigurieren von Netzwerksicherheitsgruppen und Regeln, um Datenverkehr zu virtuellen Computern zu steuern
- Bereitstellung einer Web Application Firewall als Schutz vor Angriffen, die auf Ihre Webanwendungen abzielen
- Bereitstellen von fehlenden Systemupdates
- Behandeln von Betriebssystemkonfigurationen, die nicht den empfohlenen Basisregeln entsprechen

Klicken Sie auf die Kachel **Empfehlungen**, um eine Liste mit Empfehlungen zu erhalten. Klicken Sie auf jede Empfehlung, um weitere Informationen anzuzeigen oder diese Empfehlung umzusetzen.

![][3]

### Ressourcenintegrität

Auf der Kachel **Ressourcenintegrität** wird der Gesamtsicherheitsstatus der Umgebung nach Ressourcentyp angezeigt: virtuelle Computer, Webanwendungen und weitere Ressourcen.

Wählen Sie auf der Kachel **Ressourcenintegrität** einen Ressourcentyp aus, um weitere Informationen (virtuelle Computer im folgenden Beispiel) anzuzeigen, einschließlich einer Liste aller potenziellen Sicherheitsrisiken, die erkannt wurden.

![][4]

### Sicherheitswarnungen

Azure Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls. Wurden Bedrohungen erkannt, wird eine Sicherheitswarnung erstellt. Beispiele für eine Erkennung sind:

- Gefährdete virtuelle Computer, die mit bekannten bösartigen IP-Adressen kommunizieren
- Erweiterte Schadsoftware, die mithilfe der Windows-Fehlerberichterstattung erkannt wurde
- Brute-Force-Angriffe gegen virtuelle Computer
- Sicherheitswarnungen von integrierter Antischadsoftware und integrierten Firewalls

Durch Klicken auf die Kachel **Sicherheitswarnungen** wird eine Liste priorisierter Warnungen angezeigt.

![][5]

Wenn Sie eine Warnung auswählen, werden weitere Informationen zu dem Angriff sowie Vorschläge angezeigt, wie auf ihn reagiert werden kann.

![][6]

## Erste Schritte
Zum Einstieg in Azure Security Center benötigen Sie ein Microsoft Azure-Abonnement. Azure Security Center wird mit Ihrem Azure-Abonnement aktiviert. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial) registrieren.

Der Zugriff auf Azure Security Center erfolgt über das [Azure-Portal](http://azure.microsoft.com/features/azure-portal/). Weitere Informationen finden Sie in der [Dokumentation zum Portal](https://azure.microsoft.com/documentation/services/azure-portal/).

[Erste Schritte mit Azure Security Center](security-center-get-started.md) führt Sie schnell durch die Sicherheitsüberwachungs- und Richtlinienverwaltungskomponenten von Azure Security Center.

## Nächste Schritte
In diesem Dokument wurden Azure Security Center, seine wichtigsten Funktionen sowie die ersten Schritte zu seiner Nutzung vorgestellt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – Erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
- [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md) – Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Häufig gestellte Fragen (FAQ) zu Security Center](security-center-faq.md) – Häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) (in englischer Sprache) – Neuigkeiten und Informationen zur Azure-Sicherheit

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_1210_2015-->