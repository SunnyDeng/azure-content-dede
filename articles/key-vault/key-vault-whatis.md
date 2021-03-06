<properties
	pageTitle="Was ist der Azure-Schlüsseltresor? | Microsoft Azure"
	description="Der Azure-Schlüsseltresor unterstützt Sie dabei, kryptografische Schlüssel und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Durch Verwenden des Schlüsseltresors können Kunden Schlüssel und geheime Schlüssel (beispielsweise Authentifizierungsschlüssel, Schlüssel für Speicherkonten, Datenverschlüsselungsschlüssel, PFX-Dateien und Kennwörter) verschlüsseln, indem sie durch Hardwaresicherheitsmodule (HSMs) geschützte Schlüssel verwenden."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/12/2015"
	ms.author="cabailey"/>



# Was ist der Azure-Schlüsseltresor?

Azure-Tresorschlüssel ist in den meisten Regionen verfügbar. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](../../../../pricing/details/key-vault/).

## Einführung

Der Azure-Schlüsseltresor unterstützt Sie dabei, kryptografische Schlüssel und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Durch Verwenden des Schlüsseltresors können Sie Schlüssel und geheime Schlüssel (beispielsweise Authentifizierungsschlüssel, Schlüssel für Speicherkonten, Datenverschlüsselungsschlüssel, PFX-Dateien und Kennwörter) verschlüsseln, indem Sie Schlüssel verwenden, die durch Hardwaresicherheitsmodule (HSMs) geschützt werden. Zur Steigerung der Sicherheit können Sie Schlüssel in HSMs importieren oder in diesen generieren (Schlüssel verbleiben immer innerhalb der HSM-Grenzen). Die HSMs sind FIPS 140-2 Ebene 2-zertifiziert.

Der Schlüsseltresor optimiert die Schlüsselverwaltung und ermöglicht es Ihnen, die Kontrolle über Schlüssel zu behalten, die für den Datenzugriff und die Verschlüsselung Ihrer Daten verwendet werden. Entwickler können Schlüssel für Tests und Entwicklung innerhalb von Minuten erstellen und diese später nahtlos in Schlüssel für die Produktion migrieren. Sicherheitsadministratoren können nach Bedarf Berechtigungen für Schlüssel erteilen (und widerrufen).

Die folgende Tabelle enthält Informationen dazu, wie Sie mithilfe des Schlüsseltresors die Anforderungen von Entwicklern und Sicherheitsadministratoren erfüllen können.





| Rolle | Problembeschreibung | Lösung durch den Azure-Schlüsseltresor |
| ------------- |-------------|-----|
| Entwickler einer Azure-Anwendung | "Ich möchte eine Anwendung für Azure schreiben, die Schlüssel für Anmeldung und Verschlüsselung verwendet, aber ich möchte diese Schlüssel außerhalb der Anwendung verwalten, damit die Lösung auch für eine geografisch verteilte Anwendung geeignet ist. <br/><br/>Ich möchte Schlüssel und geheime Schlüssel schützen, ohne den Code dafür selbst zu schreiben. Außerdem sollen die Schlüssel auf einfache Weise bei optimaler Leistung aus meiner Anwendung verwendet werden können." | √ Schlüssel werden in einem Tresor gespeichert und über einen URI aufgerufen, wenn sie benötigt werden.<br/><br/> √ Schlüssel werden mithilfe von Algorithmen nach Industriestandard, Schlüssellängen und Hardwaresicherheitsmodulen (HSMs) von Azure geschützt.<br/><br/> √ Schlüssel werden in HSMs verarbeitet, die sich in denselben Azure-Datencentern befinden wie die Anwendungen. Dadurch wird eine höhere Zuverlässigkeit und eine geringere Latenz geboten als bei Aufbewahrung der Schlüssel an einem getrennten Speicherort, wie lokale Standorte.|
| Entwickler von SaaS-Lösungen (Software-as-a-Service) |"Ich möchte weder die Verantwortung noch die Haftung für Schlüssel und geheime Schlüssel für Kundenmandanten übernehmen. <br/><br/>Ich möchte, dass der Kunde sowohl die Verantwortung als auch die Verwaltung für seine Schlüssel übernimmt, sodass ich mich auf meine eigentlichen Aufgaben konzentrieren kann – die Bereitstellung der Softwarefunktionen." | √ Kunden können eigene Schlüssel in Azure importieren und diese verwalten. Wenn eine SaaS-Anwendung kryptografische Vorgänge unter Verwendung der Kundenschlüssel ausführen muss, führt der Schlüsseltresor diese Vorgänge im Namen der Anwendung aus. Die Anwendung hat keine Informationen über die Kundenschlüssel.|
| Sicherheitsbeauftragter (Chief Security Officer, CSO) | "Ich möchte sicherstellen, dass unsere Anwendungen den Anforderungen für FIPS 140-2 Level 2 HSMs für die sichere Schlüsselverwaltung entsprechen. <br/><br/>Ich möchte sicherstellen, dass meine Organisation die Kontrolle über den gesamten Lebenszyklus der Schlüssel behält und die Schlüsselverwendung überwachen kann. <br/><br/>Auch wenn wir verschiedene Azure-Dienste und -Ressourcen nutzen, möchte ich die Schlüssel über einen einzigen Speicherort in Azure verwalten." |√ HSMs sind FIPS 140-2 Level 2-zertifiziert.<br/><br/>√ Der Schlüsseltresor ist so konzipiert, dass Microsoft Ihre Schlüssel weder extrahiert noch sieht.<br/><br/>√ Die Schlüsselverwendung wird nahezu in Echtzeit protokolliert (aktuell in der [Vorschau](http://blogs.technet.com/b/kv/archive/2015/11/10/keyvault_2d00_logging_2d00_preview.aspx)).<br/><br/>√ Der Tresor stellt eine einzige Benutzeroberfläche bereit – unabhängig davon, über wie viele Tresore Sie in Azure verfügen, welche Regionen diese unterstützen und von welchen Anwendungen die Tresore verwendet werden. |


Jeder Benutzer mit einem Azure-Abonnement kann Schlüsseltresore erstellen und verwenden. Wenngleich der Schlüsseltresor insbesondere Entwicklern und Sicherheitsadministratoren Vorteile bietet, kann er durch einen Organisationsadministrator implementiert und verwaltet werden, der andere Azure-Dienste für eine Organisation verwaltet. Beispielsweise kann sich dieser Administrator mit einem Azure-Abonnement anmelden, einen Tresor für die Schlüsselspeicherung der Organisation erstellen und dann operative Vorgänge wie die folgenden übernehmen:

+ Erstellen oder Importieren eines Schlüssels oder geheimer Informationen
+ Widerrufen oder Löschen eines Schlüssels oder geheimer Informationen
+ Autorisieren von Benutzern oder Anwendungen zur Verwaltung oder Verwendung von Schlüsseln und geheimen Schlüsseln
+ Konfigurieren der Schlüsselverwendung (z. B. Anmelden oder Verschlüsseln)
+ Übersachen der Schlüsselverwendung

Dieser Administrator kann anschließend den Entwicklern die URIs für den Aufruf der Schlüssel aus ihren Anwendungen und den Sicherheitsadministratoren Protokollinformationen zur Schlüsselverwendung bereitstellen. Beachten Sie, dass sich die Protokollierung der Verwendung derzeit in der [Vorschau](http://blogs.technet.com/b/kv/archive/2015/11/10/keyvault_2d00_logging_2d00_preview.aspx) befindet.

   ![Übersicht über den Azure-Schlüsseltresor][1]

Entwickler können die Schlüssel außerdem durch Verwendung von APIs direkt verwalten. Weitere Informationen finden Sie im [Entwicklerhandbuch für den Schlüsseltresor](key-vault-developers-guide.md).

## Nächste Schritte

Ein Lernprogramm für den Einstieg für Administratoren finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](key-vault-get-started.md).

Weitere Informationen zur Verwendung von Schlüsseln und geheimen Schlüsseln mit dem Azure-Schlüsseltresor finden Sie unter [Informationen zu Schlüsseln und geheimen Schlüsseln](https://msdn.microsoft.com/library/azure/dn903623.aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png

<!---HONumber=Nov15_HO3-->