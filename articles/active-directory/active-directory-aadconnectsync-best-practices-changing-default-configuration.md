<properties
	pageTitle="Azure AD Connect-Synchronisierung: Bewährte Methoden zum Ändern der Standardkonfiguration | Microsoft Azure"
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
	ms.date="02/16/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Bewährte Methoden zum Ändern der Standardkonfiguration
Dieses Thema dient zur Beschreibung der unterstützten und nicht unterstützten Änderungen der Azure AD Connect-Synchronisierung.

Die von Azure AD Connect erstellte Konfiguration funktioniert in der vorliegenden Form für die meisten Umgebungen, die ein lokales Active Directory-Verzeichnis mit Azure AD synchronisieren. In einigen Fällen müssen jedoch einige Änderungen an einer Konfiguration vorgenommen werden, um bestimmte Anforderungen zu erfüllen.

## Änderungen des Dienstkontos
Die Azure AD Connect-Synchronisierung läuft unter einem Dienstkonto, das vom Installations-Assistenten erstellt wurde. Dieses Dienstkonto enthält die Verschlüsselungsschlüssel für die von der Synchronisierung verwendete Datenbank. Es ist mit einem 127 Zeichen langen Kennwort erstellt, das nicht abläuft.

- Das Ändern oder Zurücksetzen des Kennworts des Dienstkontos wird **nicht unterstützt**. Dadurch würden die Verschlüsselungsschlüssel gelöscht, und der Dienst wäre nicht in der Lage, auf die Datenbank zuzugreifen und zu starten.

## Änderungen am Scheduler
Ab den Versionen von Build 1.1 (Februar 2016) können Sie den [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) so konfigurieren, dass ein anderer Synchronisierungszyklus als der Standardwert von 30 Minuten verwendet wird.

## Änderungen an Synchronisierungsregeln
Der Installations-Assistent verfügt über eine Konfiguration, die für die meisten gängigen Szenarien funktioniert. Falls Sie Änderungen an der Konfiguration vornehmen müssen, müssen Sie diese Regeln befolgen, um weiterhin über eine unterstützte Konfiguration zu verfügen.

- Sie können [Attributflüsse ändern](#change-attribute-flows), wenn die standardmäßig festgelegten direkten Attributflüsse für Ihre Organisation nicht geeignet sind.
- Wenn Sie das [„Fließen“ eines Attributs verhindern](#do-not-flow-an-attribute) und alle vorhandenen Attributwerte in Azure AD entfernen möchten, müssen Sie dafür eine Regel erstellen.
- [Deaktivieren Sie eine unerwünschte Synchronisierungsregel](#disable-an-unwanted-sync-rule), statt sie zu löschen. Eine gelöschte Regel wird bei einem Upgrade neu erstellt.
- Zum [Ändern einer vordefinierten Regel](#change-an-out-of-box-rule) sollten Sie eine Kopie der Originalregel erstellen und die vordefinierte Regel deaktivieren. Der Synchronisierungsregel-Editor wird angezeigt und dient Ihnen als Unterstützung.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.

>[AZURE.WARNING] Die standardmäßigen Synchronisierungsregeln verfügen über einen Fingerabdruck. Wenn Sie eine Änderung an diesen Regeln vornehmen, stimmt der Fingerabdruck überein. Daher kann es später zu Problemen kommen, wenn Sie versuchen, eine neue Version von Azure AD Connect anzuwenden. Führen Sie Änderungen nur wie in diesem Artikel beschrieben durch.

### Ändern von Attributflüssen
In einigen Fällen funktionieren die standardmäßigen Attributflüsse für eine Organisation nicht.

Sie sollten die folgenden Regeln einhalten:

- Erstellen Sie eine neue Synchronisierungsregel mit Ihren Attributflüssen. Durch Angabe einer höheren Rangfolge (niedrigere numerischer Wert) überschreiben Ihre Regeln alle Standardflüsse.
- Fügen Sie einer Standardregel keine zusätzlichen Flüsse hinzu. Diese Änderungen gehen bei einem Upgrade verloren.

Bei Fabrikam wird in einer Gesamtstruktur das lokale Alphabet für Vorname, Nachname und Anzeigename verwendet. Die lateinische Zeichenfolgendarstellung dieser Attribute ist in den Erweiterungsattribute gespeichert. Beim Erstellen der globalen Adressliste in Azure AD und Office 365 möchte das Unternehmen stattdessen diese verwenden.

Mit einer Standardkonfiguration sieht ein Objekt aus der lokalen Gesamtstruktur folgendermaßen aus:

![Attributfluss 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

Führen Sie folgende Schritte aus, um eine Regel mit anderen Attributflüssen zu erstellen:

- Öffnen Sie im Menü „Start“ den **Synchronisierungsregel-Editor**.
- Während auf der linken Seite noch **Eingehend** ausgewählt ist, klicken Sie auf die Schaltfläche **Neue Regel hinzufügen**.
- Benennen Sie die Regel und fügen Sie eine Beschreibung hinzu. Wählen Sie das lokale Active Directory und die entsprechenden Objekttypen aus. Wählen Sie für **Verknüpfungstyp** die Option **Join**. Wählen Sie als Rangfolge eine Zahl, die nicht von einer anderen Regel verwendet wird. Die vordefinierten Regeln beginnen mit 100; in diesem Beispiel kann also der Wert 50 verwendet werden. ![Attributfluss 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- Lassen Sie den Bereich leer (d. h. die Regel sollte für alle Benutzerobjekte in der Gesamtstruktur gelten).
- Lassen Sie die Verknüpfungsregeln leer (d. h. alle Verknüpfungen sollen von der standardmäßigen Regel behandelt werden).
- Erstellen Sie unter „Transformationen“ die folgenden Abläufe. ![Attributfluss 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- Klicken Sie auf **Hinzufügen**, um die Regel zu speichern.
- Wechseln Sie zu **Synchronization Service Manager**. Wählen Sie unter **Connectors** den Connector, für den wir die Regel hinzugefügt haben. Wählen Sie **Ausführen** und **Vollständige Synchronisierung**. Bei einer vollständigen Synchronisierung werden alle Objekte neu berechnet, die die aktuellen Regeln verwenden.

Dies ist das Endergebnis für dasselbe Objekt mit dieser benutzerdefinierten Regel:

![Attributfluss 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### Verhindern des „Fließens“ eines Attributs
Es gibt zwei Möglichkeiten, wie Sie dafür sorgen, dass ein Attribut nicht „fließt“ (also nicht übertragen wird). Die erste Option befindet sich im Installations-Assistenten und ermöglicht es Ihnen, [ausgewählte Attribute zu entfernen](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Diese Option funktioniert, wenn Sie das Attribut vorher noch nie synchronisiert haben. Falls Sie aber bereits mit der Synchronisierung dieses Attributs begonnen haben und es danach mit diesem Feature entfernen, beendet das Synchronisierungsmodul die Verwaltung des Attributs, und die vorhandenen Werte verbleiben in Azure AD.

Wenn Sie den Wert eines Attributs entfernen und sicherstellen möchten, dass es in Zukunft nicht „fließt“, müssen Sie stattdessen eine benutzerdefinierte Regel erstellen.

Bei Fabrikam haben wir erkannt, dass einige der Attribute, die wir mit der Cloud synchronisiert haben, nicht vorhanden sein sollten. Wir möchten sicherstellen, dass diese Attribute aus Azure AD entfernt werden.

![Erweiterungsattribute](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Erstellen einer neuen Regel für eingehende Synchronisierung und Füllen der Beschreibung ![Beschreibungen](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Erstellen Sie Attributflüsse vom Typ **Ausdruck** und mit der Quelle **AuthoritativeNull**. Das Literal **AuthoritativeNull** gibt an, dass der Wert in MV auch dann leer sein sollte, wenn eine Synchronisierungsregel mit geringerer Vorrangigkeit versucht, den Wert einzufügen. ![Erweiterungsattribute](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Speichern Sie die Synchronisierungsregel. Starten Sie **Synchronisierungsdienst**, suchen Sie nach dem Connector, und wählen Sie **Ausführen** und **Vollständige Synchronisierung**. Alle Attributflüsse werden neu berechnet.
- Stellen Sie sicher, dass die beabsichtigten Änderungen exportiert werden sollen, indem Sie den Connectorbereich durchsuchen. ![Gestaffeltes Löschen](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### Deaktivieren einer unerwünschten Synchronisierungsregel
Löschen Sie eine standardmäßige Synchronisierungsregel nicht. Sie wird beim nächsten Upgrade wiederhergestellt.

In einigen Fällen erstellt der Installations-Assistent eine Konfiguration, die für Ihre Topologie nicht funktioniert. Falls Sie beispielsweise über eine Topologie mit Kontoressourcengesamtstruktur verfügen, das Schema in der Kontogesamtstruktur aber um das Exchange-Schema erweitert haben, werden sowohl für die Kontogesamtstruktur als auch für die Ressourcengesamtstruktur Regeln für Exchange erstellt. In diesem Fall müssen wir die Synchronisierungsregel für Exchange deaktivieren.

![Deaktivierte Synchronisierungsregel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

In der Abbildung oben wurde mit dem Installations-Assistenten ein altes Exchange 2003-Schema in der Kontogesamtstruktur gefunden. Es wurde hinzugefügt, bevor die Ressourcengesamtstruktur in die Umgebung von Fabrikam eingeführt wurde. Um sicherzustellen, dass keine Attribute aus der alten Exchange-Implementierung synchronisiert werden, sollte die Synchronisierungsregel wie gezeigt deaktiviert werden.

### Ändern einer standardmäßigen Regel
Wenn Sie Änderungen an einer standardmäßigen Regel vornehmen müssen, können Sie eine Kopie der standardmäßigen Regel erstellen und die ursprüngliche Regel deaktivieren. Nehmen Sie an der geklonten Regel dann die gewünschten Änderungen vor. Der Synchronisierungsregel-Editor unterstützt Sie dabei. Wenn Sie eine standardmäßige Regel öffnen, wird dieses Dialogfeld angezeigt:

![Warnung für standardmäßige Regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wählen Sie **Ja**, um eine Kopie der Regel zu erstellen. Die geklonte Regel wird geöffnet.

![Geklonte Regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Nehmen Sie an dieser geklonten Regel die erforderlichen Änderungen für Bereich, Verknüpfung und Transformationen vor.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->