
<properties
	pageTitle="Verwenden von Azure AD Connect Health mit Synchronisierung | Microsoft Azure"
	description="Auf dieser Seite zu Azure AD Connect Health wird erläutert, wie eine Überwachung mit Azure AD Connect für die Synchronisierung durchgeführt wird."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/08/2016"
	ms.author="billmath"/>

# Verwenden von Azure AD Connect Health für die Synchronisierung
Die folgende Dokumentation bezieht sich auf die Überwachung der Azure AD Connect-Synchronisierung mithilfe von Azure AD Connect Health. Informationen zum Überwachen von AD FS mit Azure AD Connect Health finden Sie unter [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md).

![Azure AD Connect Health für die Synchronisierung](./media/active-directory-aadconnect-health-sync/sync.png)

## Warnungen für Azure AD Connect Health für die Synchronisierung
Im Abschnitt mit Azure AD Connect Health-Warnungen für die Synchronisierung wird eine Liste der aktiven Warnungen angezeigt. Jede Warnung umfasst relevante Informationen, Lösungsschritte und Links zur verwandten Dokumentation. Durch Auswahl einer aktiven oder behobenen Warnung wird ein neues Blatt angezeigt. Dieses enthält zusätzliche Informationen, Lösungsschritte und Links zu weiterführender Dokumentation. Sie können außerdem Verlaufsdaten zu bereits behobenen Warnungen anzeigen.

Durch Auswahl einer Warnung werden zusätzliche Informationen, Lösungsschritte sowie Links zu weiterführender Dokumentation angezeigt.

![Azure AD Connect-Synchronisierungsfehler](./media/active-directory-aadconnect-health-sync/alert.png)

### Beschränkte Auswertung von Warnungen
Wenn Azure AD Connect die Standardkonfiguration NICHT verwendet (z. B. wenn die Attributfilterung von der Standardkonfiguration in eine benutzerdefinierte Konfiguration geändert wird), lädt der Azure AD Connect Health-Agent die Fehlerereignisse im Zusammenhang mit Azure AD Connect nicht hoch.

Dies schränkt die Auswertung der Warnungen vom Dienst ein. Ihnen wird im Azure-Portal unter Ihrem Dienst ein Banner angezeigt, das auf diese Bedingung hinweist.

![Azure AD Connect Health für die Synchronisierung](./media/active-directory-aadconnect-health-sync/banner.png)

Sie können dies ändern, indem Sie auf „Einstellungen“ klicken und das Hochladen aller Fehlerprotokolle durch den Azure AD Connect Health-Agent erlauben.

![Azure AD Connect Health für die Synchronisierung](./media/active-directory-aadconnect-health-sync/banner2.png)

## Einblick in die Synchronisierung
Mit der neuesten Version von Azure AD Connect Health für die Synchronisierung wurden folgende neue Funktionen eingeführt:

- Latenz von Synchronisierungsvorgängen
- Trends bei der Objektänderung

### Synchronisierungslatenz
Dieses Feature bietet eine grafische Darstellung des Trends bei Synchronisierungsvorgängen (Import, Export usw.) für Connectors. So können Sie zum einen schnell und einfach die Latenz bei Ihren Vorgängen überprüfen (sehr nützlich, wenn viele Änderungen durchgeführt werden). Zum anderen können Sie Anomalien bei der Latenz aufdecken, die möglicherweise untersucht werden müssen.

![Synchronisierungslatenz](./media/active-directory-aadconnect-health-sync/synclatency.png)

Standardmäßig wird nur die Latenz des Exportvorgangs für den Azure AD-Connector angezeigt. Wenn Sie weitere Vorgänge für den Connector oder Vorgänge von anderen Connectors anzeigen möchten, klicken Sie mit der rechten Maustaste auf das Diagramm und wählen den gewünschten Vorgang und Connector.

### Änderungen bei Synchronisierungsobjekten
Dieses Feature bietet eine grafische Darstellung des Trends bei der Anzahl von ausgewerteten und nach Azure AD exportierten Änderungen. Derzeit ist es schwierig, diese Informationen aus den Synchronisierungsprotokollen zu entnehmen. Mit diesem Diagramm erhalten Sie nicht nur eine einfachere Möglichkeit zum Überwachen der Anzahl der Änderungen in Ihrer Umgebung, sondern auch einen visuellen Überblick über die aufgetretenen Fehler.

![Synchronisierungslatenz](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installieren des Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0316_2016-->