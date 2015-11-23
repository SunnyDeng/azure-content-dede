<properties
	pageTitle="Bewährte Methoden zum Ändern der Standardkonfiguration | Microsoft Azure"
	description="Stellt bewährte Methoden zum Ändern der Standardkonfiguration der Azure AD Connect-Synchronisierung vor."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Bewährte Methoden zum Ändern der Standardkonfiguration

Dieses Thema dient zur Beschreibung der unterstützten und nicht unterstützten Änderungen der Azure AD Connect-Synchronisierung.

Die von Azure AD Connect erstellte Konfiguration funktioniert in der vorliegenden Form für die meisten Umgebungen, die ein lokales Active Directory-Verzeichnis mit Azure AD synchronisieren. In einigen Fällen müssen jedoch einige Änderungen an einer Konfiguration vorgenommen werden, um bestimmte Anforderungen zu erfüllen.

## Änderungen des Dienstkontos
Die Azure AD Connect-Synchronisierung läuft unter einem Dienstkonto, das vom Installations-Assistenten erstellt wurde. Dieses Dienstkonto enthält die Verschlüsselungsschlüssel für die von der Synchronisierung verwendete Datenbank. Es ist mit einem 127 Zeichen langen Kennwort erstellt, das nicht abläuft.

- Das Ändern oder Zurücksetzen des Kennworts des Dienstkontos wird **nicht unterstützt**. Dadurch würden die Verschlüsselungsschlüssel gelöscht, und der Dienst wäre nicht in der Lage, auf die Datenbank zuzugreifen und zu starten.

## Änderungen am Scheduler
Die Azure AD Connect-Synchronisierung ist so eingestellt, dass die Identitätsdaten alle 3 Stunden synchronisiert werden. Während der Installation wird eine geplante Aufgabe erstellt, die mit den Berechtigungen für den Betrieb des Synchronisierungsservers unter einem Dienstkonto ausgeführt wird.

- Änderungen an der geplanten Aufgabe werden **nicht unterstützt**. Das Kennwort für das Dienstkonto ist nicht bekannt. Siehe [Änderungen des Dienstkontos](#changes-to-the-service-account).
- Eine häufigere Synchronisierung als standardmäßig 3 Stunden wird **nicht unterstützt**.

## Änderungen an Synchronisierungsregeln

Der Installations-Assistent verfügt über eine Konfiguration, die für die meisten gängigen Szenarien funktioniert. Falls Sie Änderungen an der Konfiguration vornehmen müssen, müssen Sie diese Regeln befolgen, um weiterhin über eine unterstützte Konfiguration zu verfügen.

- Die einzige unterstützte Änderung einer standardmäßigen Synchronisierungsregel ist ihre Deaktivierung. Alle anderen Änderungen können bei einem Upgrade verloren gehen.
- Falls Sie eine andere Änderung an einer standardmäßigen Regel vornehmen müssen, können Sie eine Kopie davon erstellen und die ursprüngliche Regel deaktivieren. Der Synchronisierungsregel-Editor wird angezeigt und dient Ihnen als Unterstützung.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.

>[AZURE.WARNING]Die standardmäßigen Synchronisierungsregeln verfügen über einen Fingerabdruck. Wenn Sie eine Änderung an diesen Regeln vornehmen, stimmt der Fingerabdruck überein. Daher kann es später zu Problemen kommen, wenn Sie versuchen, eine neue Version von Azure AD Connect anzuwenden. Führen Sie Änderungen nur wie in diesem Artikel beschrieben durch.

### Löschen einer unerwünschten Synchronisierungsregel
Löschen Sie eine standardmäßige Synchronisierungsregel nicht. Sie wird beim nächsten Upgrade wiederhergestellt.

In einigen Fällen erstellt der Installations-Assistent eine Konfiguration, die für Ihre Topologie nicht funktioniert. Falls Sie beispielsweise über eine Topologie mit Kontoressourcengesamtstruktur verfügen, das Schema in der Kontogesamtstruktur aber um das Exchange-Schema erweitert haben, werden sowohl für die Kontogesamtstruktur als auch für die Ressourcengesamtstruktur Regeln für Exchange erstellt. In diesem Fall müssen wir die Synchronisierungsregel für Exchange deaktivieren.

![Deaktivierte Synchronisierungsregel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

In der Abbildung oben wurde mit dem Installations-Assistenten ein altes Exchange 2003-Schema in der Kontogesamtstruktur gefunden. Es wurde hinzugefügt, bevor die Ressourcengesamtstruktur in die Umgebung von Fabrikam eingeführt wurde. Um sicherzustellen, dass keine Attribute aus der alten Exchange-Implementierung synchronisiert werden, sollte die Synchronisierungsregel wie gezeigt deaktiviert werden.

### Ändern der standardmäßigen Regeln
Wenn Sie Änderungen an einer standardmäßigen Regel vornehmen müssen, können Sie eine Kopie der standardmäßigen Regel erstellen und die ursprüngliche Regel deaktivieren. Nehmen Sie an der geklonten Regel dann die gewünschten Änderungen vor. Der Synchronisierungsregel-Editor unterstützt Sie dabei. Wenn Sie eine standardmäßige Regel öffnen, wird dieses Dialogfeld angezeigt:

![Warnung für standardmäßige Regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wählen Sie **Ja**, um eine Kopie der Regel zu erstellen. Die geklonte Regel wird geöffnet.

![Geklonte Regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Nehmen Sie an dieser geklonten Regel die erforderlichen Änderungen für Bereich, Verknüpfung und Transformationen vor.

### Verhindern des „Fließens“ eines Attributs
Es gibt zwei Möglichkeiten, wie Sie dafür sorgen, dass ein Attribut nicht „fließt“ (also nicht übertragen wird). Die erste Option befindet sich im Installations-Assistenten und ermöglicht es Ihnen, [ausgewählte Attribute zu entfernen](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Diese Option funktioniert, wenn Sie das Attribut vorher noch nie synchronisiert haben. Falls Sie aber bereits mit der Synchronisierung dieses Attributs begonnen haben und es danach mit diesem Feature entfernen, beendet das Synchronisierungsmodul die Verwaltung des Attributs, und die vorhandenen Werte verbleiben in Azure AD.

Wenn Sie den Wert eines Attributs entfernen und sicherstellen möchten, dass es in Zukunft nicht „fließt“, müssen Sie stattdessen eine benutzerdefinierte Regel erstellen.

Bei Fabrikam haben wir erkannt, dass einige der Attribute, die wir mit der Cloud synchronisiert haben, nicht vorhanden sein sollten. Wir möchten sicherstellen, dass diese Attribute aus Azure AD entfernt werden.

![Erweiterungsattribute](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Erstellen einer neuen Regel für eingehende Synchronisierung und Füllen der Beschreibung ![Beschreibungen](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Erstellen Sie Attributflüsse vom Typ **Ausdruck** und mit der Quelle **AuthoritativeNull**. Das Literal **AuthoritativeNull** gibt an, dass der Wert in MV auch dann leer sein sollte, wenn eine Synchronisierungsregel mit geringerer Vorrangigkeit versucht, den Wert einzufügen. ![Erweiterungsattribute](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Speichern Sie die Synchronisierungsregel. Starten Sie **Synchronisierungsdienst**, suchen Sie nach dem Connector, und wählen Sie **Ausführen** und **Vollständige Synchronisierung**. Alle Attributflüsse werden neu berechnet.
- Stellen Sie sicher, dass die beabsichtigten Änderungen exportiert werden sollen, indem Sie den Connectorbereich durchsuchen. ![Gestaffeltes Löschen](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO3-->