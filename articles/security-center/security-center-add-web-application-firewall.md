<properties
   pageTitle="Hinzufügen einer Web Application Firewall in Azure Security Center | Microsoft Azure"
   description="In diesem Dokument wird erläutert, wie Sie die Azure Security Center-Empfehlung **Web Application Firewall hinzufügen** implementieren."
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
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Hinzufügen einer Web Application Firewall in Azure Security Center

In Azure Security Center wird unter Umständen empfohlen, dass Sie zum Schutz Ihrer Webanwendungen eine Web Application Firewall (WAF) von einem Microsoft-Partner hinzufügen. In diesem Dokument sehen wir uns ein Beispiel zu dieser Vorgehensweise an.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Der Dienst wird anhand einer Beispielbereitstellung vorgestellt. Es ist keine schrittweise Anleitung.

## Implementieren der Empfehlung

1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Webanwendung mit Web Application Firewall sichern** aus. ![][1]

2. Wählen Sie auf dem Blatt **Webanwendungen mit Web Application Firewall sichern** eine Webanwendung aus. Das Blatt **Web Application Firewall hinzufügen** wird geöffnet. ![][2]
3. Sie können eine vorhandene Web Application Firewall verwenden oder eine neue erstellen. In diesem Beispiel sind keine vorhandenen WAFs verfügbar, daher erstellen wir eine neue WAF.

4. Wählen Sie zum Erstellen einer neuen WAF eine Lösung aus der Liste der angezeigten Partner aus. In diesem Beispiel wählen wir **Barracuda Web Application Firewall** aus.
5. Auf dem dann geöffneten Blatt **Barracuda Web Application Firewall** werden Informationen zu dieser Partnerlösung angezeigt. Klicken Sie auf dem Informationsblatt auf **Erstellen**. ![][3]

6. Das Blatt **New Web Application Firewall** wird geöffnet, auf dem Sie Schritte zur **VM-Konfiguration** ausführen und **WAF-Informationen** bereitstellen können. Wählen Sie **VM-Konfiguration** aus.

7. Auf dem Blatt **VM-Konfiguration** können Sie Informationen eingeben, die erforderlich sind, um den virtuellen Computer einzurichten, auf dem die WAF ausgeführt wird. ![][4]
8. Kehren Sie zum Blatt **New Web Application Firewall** zurück, und wählen Sie **WAF-Informationen**. Auf dem Blatt **WAF-Informationen** können Sie die WAF selbst konfigurieren. In Schritt 7 können Sie den virtuellen Computer konfigurieren, auf dem die WAF ausgeführt wird, und in Schritt 8 können Sie die WAF selbst bereitstellen.

9. Kehren Sie zum Blatt **Empfehlungen** zurück. Ein neuer Eintrag wurde generiert, nachdem Sie die WAF erstellt haben: **Web Application Firewall-Setup abschließen**. Aufgrund dieses Eintrags wissen Sie, dass Sie den Prozess der eigentlichen „Verdrahtung“ der WAF in Azure Virtual Network abschließen müssen, damit die Anwendung geschützt werden kann. ![][5]

10. Wählen Sie **Web Application Firewall-Setup abschließen** aus. Ein neues Blatt wird geöffnet. Sie sehen, dass eine Webanwendung vorhanden ist, für die der Datenverkehr umgeleitet muss.
11. Wählen Sie die Webanwendung aus. Es wird ein Blatt geöffnet, in dem Sie Schritte zum Abschließen des Web Application Firewall-Setups ausführen können. Führen Sie die Schritte aus, und wählen Sie dann **Datenverkehr einschränken** aus. Security Center führt die Verkabelung dann für Sie durch. ![][6]

> [AZURE.NOTE] Sie können mehrere Webanwendungen in Security Center schützen, indem Sie diese Anwendungen Ihren vorhandenen WAF-Bereitstellungen hinzufügen. WAF-Geräte (erstellt mit dem Ressourcen-Manager-Bereitstellungsmodell) müssen in einem separaten virtuellen Netzwerk bereitgestellt werden. WAF-Geräte (erstellt mit dem klassischen Bereitstellungsmodell) sind auf die Verwendung einer Netzwerksicherheitsgruppe beschränkt. Diese Unterstützung wird in Zukunft auf eine vollständig angepasste Bereitstellung eines WAF-Geräts (klassisch) erweitert. Erfahren Sie mehr über das [klassische und das Ressourcen-Manager-Bereitstellungsmodell](../azure-classic-rm.md) für Azure-Ressourcen.

Die Protokolle dieser WAF sind nun vollständig integriert. Security Center kann automatisch beginnen, die Protokolle zu sammeln und zu analysieren, damit Sie wichtige Sicherheitswarnungen erhalten können.

## Nächste Schritte

In diesem Dokument wurde gezeigt, wie Sie die Security Center-Empfehlung „Web Application Firewall hinzufügen“ implementieren. Weitere Informationen zum Konfigurieren einer Web Application Firewall finden Sie im folgenden Artikel:

- [Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog:](http://blogs.msdn.com/b/azuresecurity/) Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0309_2016-->