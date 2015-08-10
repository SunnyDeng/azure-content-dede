<properties
	pageTitle="Bewährte Methoden zum Ändern der Standardkonfiguration"
	description="Bewährte Methoden zum Ändern der Standardkonfiguration der Azure AD Connect-Synchronisierung"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Bewährte Methoden zum Ändern der Standardkonfiguration

Die von Azure AD Connect erstellte Konfiguration funktioniert in der vorliegenden Form für die meisten Umgebungen, die ein lokales Active Directory-Verzeichnis mit Azure AD synchronisieren.<br> In einigen Fällen müssen jedoch einige Änderungen an einer Konfiguration vorgenommen werden, um bestimmte Anforderungen zu erfüllen.

Auch wenn Änderungen an Ihrer Azure AD-Konfiguration grundsätzlich unterstützt werden, sollten Sie dabei vorsichtig vorgehen, da Azure AD im Wesentlichen als Appliance vorgesehen ist.

Im Folgenden finden Sie eine Liste erwarteter Verhaltensweisen:

- Nach einem Upgrade von Azure AD Connect auf eine neuere Version werden die meisten Einstellungen auf die Standardwerte zurückgesetzt.
- Änderungen an integrierten Synchronisierungsregeln gehen verloren, nachdem ein Upgrade angewendet wurde.
- Integrierte Synchronisierungsregeln, die gelöscht wurden, werden bei einem Upgrade auf eine neuere Version neu erstellt.
- Von Ihnen erstellte benutzerdefinierte Synchronisierungsregeln bleiben unverändert, wenn ein Upgrade auf eine neuere Version angewendet wurde.



Wenn Sie die Standardkonfiguration ändern müssen, führen Sie folgende Schritte aus:

- Wenn Sie einen Attributfluss von einer integrierten Synchronisierungsregel anpassen müssen, verändern Sie diese nicht. Erstellen Sie stattdessen eine neue Synchronisierungsregel mit höherer Priorität \(niedrigerem numerischem Werte\), die den erforderlichen Attributfluss enthält.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.
- Wenn Sie den Bereich oder die Verknüpfungseinstellung einer "standardmäßigen" Synchronisierungsregel ändern müssen, dokumentieren Sie den Vorgang, und wenden Sie die Änderung nach dem Upgrade auf eine neuere Version der Azure AD-Synchronisierung erneut an.



**Weitere wichtige Hinweise:**

- Wenn Sie die attributbasierte Filterung und die Kennwortsynchronisierung konfiguriert haben, stellen Sie sicher, dass sich nur Objekte, die mit Azure AD synchronisiert sind, im Bereich der Kennwortsynchronisierung befinden. 





## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->