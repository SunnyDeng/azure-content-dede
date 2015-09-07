<properties
	pageTitle="Bewährte Methoden zum Ändern der Standardkonfiguration | Microsoft Azure"
	description="Stellt bewährte Methoden zum Ändern der Standardkonfiguration der Azure AD Connect-Synchronisierung vor."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
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

Auch wenn Änderungen an Ihrer Konfiguration der Azure AD Connect-Synchronisierung grundsätzlich unterstützt werden, sollten Sie dabei vorsichtig vorgehen, da die Azure AD Connect-Synchronisierung im Wesentlichen als Appliance vorgesehen ist.

Im Folgenden finden Sie eine Liste erwarteter Verhaltensweisen:

- Nach einem Upgrade von Azure AD Connect auf eine neuere Version werden die meisten Einstellungen auf die Standardwerte zurückgesetzt.
- Änderungen an integrierten Synchronisierungsregeln gehen verloren, nachdem ein Upgrade angewendet wurde.
- Integrierte Synchronisierungsregeln, die gelöscht wurden, werden bei einem Upgrade auf eine neuere Version neu erstellt.
- Von Ihnen erstellte benutzerdefinierte Synchronisierungsregeln bleiben unverändert, wenn ein Upgrade auf eine neuere Version angewendet wurde.



Wenn Sie die Standardkonfiguration ändern müssen, führen Sie folgende Schritte aus:

- Wenn Sie einen Attributfluss von einer integrierten Synchronisierungsregel anpassen müssen, verändern Sie diese nicht. Erstellen Sie stattdessen eine neue Synchronisierungsregel mit höherer Priorität (niedrigerem numerischem Werte), die den erforderlichen Attributfluss enthält.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.
- Wenn Sie den Bereich oder die Verknüpfungseinstellung einer "standardmäßigen" Synchronisierungsregel ändern müssen, dokumentieren Sie den Vorgang, und wenden Sie die Änderung nach dem Upgrade auf eine neuere Version von Azure AD Sync erneut an.



## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=August15_HO9-->