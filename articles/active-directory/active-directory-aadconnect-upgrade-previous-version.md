<properties
   pageTitle="Azure AD Connect: Aktualisieren von einer früheren Version | Microsoft Azure"
   description="Erläutert die verschiedenen Methoden für das Upgrade auf die aktuelle Version von Azure Active Directory Connect, einschließlich des direkten Upgrades und der Swing-Migration."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="02/29/2016"
   ms.author="andkjell"/>

# Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version
In diesem Thema werden die verschiedenen Methoden beschrieben, die Sie verwenden können, um Ihre Azure AD Connect-Installation auf die aktuelle Version zu aktualisieren. Wir empfehlen, dass Sie immer die aktuelle Version von Azure AD Connect nutzen.

Wenn Sie von DirSync aktualisieren möchten, lesen Sie [Aktualisieren des Windows Azure Active Directory-Synchronisierungstools (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Es gibt verschiedene Strategien für das Upgrade von Azure AD Connect.

| Methode | Beschreibung |
| --- | --- |
| [Automatisches Upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) | Für Kunden mit einer Expressinstallation ist dies die einfachste Methode. |
| [Direktes Upgrade](#in-place-upgrade) | Wenn Sie über einen einzelnen Server verfügen, aktualisieren Sie die Installation direkt auf dem Server. |
| [Swing-Migration](#swing-migration) | Wenn Sie über zwei Server verfügen, können einen der Server mit der neuen Version vorbereiten und den aktiven Server ändern, wenn Sie bereit sind.

Die erforderlichen Berechtigungen finden Sie unter den [erforderlichen Berechtigungen für das Upgrade](active-directory-aadconnect-accounts-permissions.md#upgrade).

## Direktes Upgrade
Ein direktes Upgrade funktioniert für das Aktualisieren von Azure AD Sync oder Azure AD Connect. Es funktioniert nicht für DirSync oder für eine Lösung mit FIM und Azure AD-Connector.

Dies ist die bevorzugte Methode, wenn Sie über einen einzelnen Server und weniger als etwa 100.000 Objekte verfügen. Nach dem Upgrade werden ein vollständiger Import und eine vollständige Synchronisierung ausgeführt. Dadurch wird sichergestellt, dass die neue Konfiguration auf alle vorhandenen Objekte im System angewendet wird. Dies kann einige Stunden. Die Dauer ist abhängig von der Anzahl der Objekte, die vom Synchronisierungsmodul berücksichtigt werden. Die normale geplante Deltasynchronisierung (standardmäßig alle 30 Minuten) wird angehalten, aber die Kennwortsynchronisierung wird fortgesetzt. Sie sollten das direkte Upgrade am Wochenende durchführen.

![Direktes Upgrade](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Wenn Sie standardmäßige Synchronisierungsregeln geändert haben, werden diese beim Upgrade wieder auf die Standardkonfiguration zurückgesetzt. Um sicherzustellen, dass Ihre Konfiguration zwischen Upgrades erhalten bleibt, achten Sie darauf, dass die Änderungen vorgenommen werden, wie in [Bewährte Methoden zum Ändern der Standardkonfiguration](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) beschrieben.

## Swing-Migration
Wenn Sie über eine komplexe Bereitstellung oder sehr viele Objekte verfügen, kann möglicherweise ein direktes Upgrade auf einem Live-System nicht ausgeführt werden. Dies könnte für einige Kunden mehrere Tage dauern, und während dieser Zeit werden keine Deltaänderungen verarbeitet.

Stattdessen ist die empfohlene Methode die Nutzung einer Swing-Migration. Für diese Methode benötigen Sie (mindestens) zwei Server: einen aktiven Server und einen Stagingserver. Der aktive Server (die durchgezogenen blauen Linien in der Abbildung unten) ist für die aktive Last verantwortlich. Der Stagingserver (die gestrichelten lilafarbenen Linien in der Abbildung unten) wird mit der neuen Version vorbereitet. Wenn er vollständig bereit ist, wird dieser Server zum aktiven Server gemacht. Der vorherige aktive Server, auf dem noch die alte Version installiert ist, wird der Stagingserver, und er wird aktualisiert.

![Stagingserver](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Hinweis: Es wurde festgestellt, dass einige Kunden für dieses Verfahren drei oder vier Server verwenden möchten. Da der Stagingserver aktualisiert wird, verfügen Sie während dieser Zeit nicht über einen Sicherungsserver für eine etwaige [Notfallwiederherstellung](active-directory-aadconnectsync-operations.md#disaster-recovery). Mit maximal vier Servern kann ein neuer Satz aus primären/Standbyservern mit der neuen Version vorbereitet werden. Dadurch ist sichergestellt, dass immer ein Stagingserver zur Verfügung steht.

Diese Schritte funktionieren auch bei einem Wechsel von Azure AD Sync oder einer Lösung mit FIM und Azure AD-Connector. Diese Schritte funktionieren nicht für DirSync, aber die entsprechende Methode einer Swing-Migration (wird auch als parallele Bereitstellung bezeichnet) mit den Schritten für DirSync finden Sie unter [Aktualisieren des Windows Azure Active Directory-Synchronisierungstools (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### Schritte bei der Swing-Migration

1. Stellen Sie sicher, dass Ihr aktiver Server und Ihr Stagingserver die gleiche Version verwenden.
2. Wenn Sie benutzerdefinierte Konfigurationen vorgenommen haben, die auf dem Stagingserver nicht vorhanden sind, führen Sie die Schritte unter [Verschieben der benutzerdefinierten Konfiguration vom aktiven Server auf den Stagingserver](#move-custom-configuration-from-active-to-staging-server) aus.
3. Aktualisieren Sie den Stagingserver auf die aktuelle Version.
4. Führen Sie mit dem Synchronisierungsmodul einen vollständigen Import und eine vollständige Synchronisierung aus.
5. Stellen Sie mithilfe der Schritte unter **Überprüfen** in [Überprüfen der Konfiguration eines Servers](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server) sicher, dass die neue Konfiguration nicht zu unerwarteten Änderungen führt. Wenn etwas nicht wie erwartet ist, korrigieren Sie es. Führen Sie dann den Import und die Synchronisierung erneut aus, und überprüfen Sie alles noch einmal, bis die Daten in Ordnung sind. Diese Schritte finden Sie im verknüpften Thema.
6. Machen Sie den Stagingserver zum aktiven Server. Dies ist der letzte Schritt **Wechseln des aktiven Servers** in [Überprüfen der Konfiguration eines Servers](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Aktualisieren Sie den Server jetzt im Stagingmodus auf die aktuelle Version. Führen Sie die gleichen Schritte wie zuvor aus, um die Daten und die Konfiguration zu aktualisieren.

### Verschieben der benutzerdefinierten Konfiguration vom aktiven Server auf den Stagingserver
Wenn Sie Änderungen an der Konfiguration des aktiven Servers vorgenommen haben, müssen Sie sicherstellen, dass die gleichen Änderungen auf dem Stagingserver angewendet werden.

Die benutzerdefinierten Synchronisierungsregeln, die Sie erstellt haben, können mit PowerShell verschoben werden. Andere Änderungen müssen die gleiche Weise auf beiden Systemen angewendet werden.

Folgendes muss auf beiden Servern auf die gleiche Weise konfiguriert sein:

- Verbindung mit den gleichen Gesamtstrukturen.
- Filter von Domänen und Organisationseinheiten.
- Die gleichen optionalen Funktionen wie Kennwortsynchronisierung und Kennwortrückschreiben.

**Verschieben von Synchronisierungsregeln** Gehen Sie folgendermaßen vor, um eine benutzerdefinierte Synchronisierungsregel zu verschieben:

1. Öffnen Sie den **Synchronisierungsregel-Editor** auf dem aktiven Server.
2. Wählen Sie die benutzerdefinierte Regel aus. Klicken Sie auf **Exportieren**. Hierdurch wird ein Editor-Fenster angezeigt. Speichern Sie die temporäre Datei mit der Erweiterung PS1. Dadurch wird sie zu einem PowerShell-Skript. Kopieren Sie die PS1-Datei auf den Stagingserver. ![Exportieren von Synchronisierungsregeln](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Die Connector-GUID ist auf dem Stagingserver anders. Um die GUID abzurufen, starten Sie den **Synchronisierungsregel-Editor**. Wählen Sie eine der standardmäßigen Regeln aus, die das gleiche verbundene System darstellt, und klicken Sie auf **Exportieren**. Ersetzen Sie die GUID in der PS1-Datei durch die GUID des Stagingservers.
4. Führen Sie die PS1-Datei an einer PowerShell-Eingabeaufforderung aus. Dadurch wird die benutzerdefinierte Synchronisierungsregel auf dem Stagingserver erstellt.
5. Wenn Sie über mehrere benutzerdefinierte Regeln verfügen, wiederholen Sie die Schritte für alle benutzerdefinierten Regeln.

## Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->