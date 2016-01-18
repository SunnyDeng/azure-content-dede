<properties
	pageTitle="Anpassen von Attributzuordnungen | Microsoft Azure"
	description="Erfahren Sie, was Attributzuordnungen für SaaS-Apps in Azure Active Directory sind und wie Sie sie an Ihre geschäftlichen Anforderungen anpassen können."
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
	ms.date="01/05/2016"
	ms.author="markusvi"/>


# Anpassen von Attributzuordnungen


Microsoft Azure AD bietet Unterstützung für die Benutzerbereitstellung für SaaS-Anwendungen von Drittanbietern, z. B. Salesforce, Google Apps usw. Wenn Sie die Benutzerbereitstellung für eine SaaS-Anwendung eines Drittanbieters aktiviert haben, steuert das Azure-Verwaltungsportal deren Attributwerte mithilfe einer Konfiguration, die als "Attributzuordnung" bezeichnet wird.

Es ist eine vorkonfigurierte Sammlung von Attributzuordnungen zwischen Azure AD-Benutzerobjekten und den Benutzerobjekten der einzelnen SaaS-Apps vorhanden. Einige Apps verwalten andere Objekttypen, z. B. Gruppen oder Kontakte. <br> Sie können die Standardattributzuordnungen den Anforderungen Ihres Unternehmens entsprechend anpassen. Dies bedeutet, dass Sie vorhandene Attributzuordnungen ändern oder löschen und neue Attributzuordnungen erstellen können.

Im Azure AD-Portal können Sie auf dieses Feature zugreifen, indem Sie in der Symbolleiste einer SaaS-Anwendung auf "Attribute" klicken.

> [AZURE.NOTE]Der Link **Attribute** ist nur verfügbar, wenn die Benutzerbereitstellung für eine SaaS-Anwendung aktiviert ist.


![Salesforce][1]


Wenn Sie in der Symbolleiste auf Attribute klicken, wird die Liste der aktuellen Zuordnungen angezeigt, die für eine SaaS-Anwendung konfiguriert sind.

Der folgende Screenshot zeigt ein Beispiel für diesen Vorgang:



![Salesforce][2]


Im Beispiel oben können Sie sehen, dass das Attribut **firstName** eines verwalteten Objekts in Salesforce mit dem Wert **givenName** des verknüpften Azure AD-Objekts aufgefüllt wird.

Wenn Sie Ihre Attributzuordnungen anpassen oder die benutzerdefinierten Einstellungen auf die Standardkonfiguration zurücksetzen möchten, können Sie zu diesem Zweck unten in einer Anwendung in der Symbolleiste auf die entsprechende Schaltfläche klicken.


![Salesforce][3]


Es gibt Attributzuordnungen, die erforderlich sind, damit eine SaaS-Anwendung ordnungsgemäß funktioniert. In der Attributetabelle weisen die zugehörigen Attributzuordnungen **Ja** als Wert für das Attribut **Erforderlich** auf. Wenn eine Attributzuordnung erforderlich ist, kann sie nicht gelöscht werden. In diesem Fall ist das Feature **Löschen** nicht verfügbar.

Wenn Sie eine vorhandene Attributzuordnung ändern möchten, wählen Sie die Zuordnung aus, und klicken Sie dann auf **Bearbeiten**. Dadurch wird eine Seite mit einem Dialogfeld geöffnet, in dem Sie die ausgewählte Attributzuordnung ändern können.


![Bearbeiten der Attributzuordnung][4]



## Grundlegendes zu Attributzuordnungstypen


Mit Attributzuordnungen steuern Sie, wie die Attribute in einer SaaS-Anwendung eines Drittanbieters mit Daten aufgefüllt werden. Vier verschiedene Zuordnungstypen werden unterstützt:

- **Direkt**: Das Zielattribut wird mit dem Wert eines Attributs des verknüpften Objekts in Azure AD aufgefüllt.


- **Konstant**: Das Zielattribut wird mit einer bestimmten Zeichenfolge, die Sie angegeben haben, aufgefüllt.


- **Ausdruck**: Das Zielattribut wird abhängig vom Ergebnis eines skriptähnlichen Ausdrucks mit Daten aufgefüllt. Weitere Details finden Sie unter [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Kein**: Das Zielattribut bleibt unverändert. Wenn das Zielattribut allerdings leer ist, wird es mit dem von Ihnen angegebenen Standardwert aufgefüllt.



Zusätzlich zu diesen vier grundlegenden Attributzuordnungstypen unterstützen benutzerdefinierte Attributzuordnungen das Konzept der Zuordnung von **Standardwerten**. Die Standardwertzuordnung stellt sicher, dass ein Zielattribut mit einem Wert aufgefüllt wird, wenn weder in Azure AD noch für das Zielobjekt ein Wert vorhanden ist.

Microsoft Azure AD stellt eine äußerst effiziente Implementierung eines Synchronisierungsprozesses zur Verfügung. In einer initialisierten Umgebung werden nur Objekte, die eine Aktualisierung erfordern, während eines Synchronisierungszyklus verarbeitet. Das Aktualisieren von Attributzuordnungen besitzt Auswirkungen auf die Leistung eines Synchronisierungszyklus. Der Grund besteht darin, dass nach einer Aktualisierung der Attributzuordnungskonfiguration alle verwalteten Objekte erneut ausgewertet werden müssen. Aus diesem Grund ist es eine empfohlene bewährte Methode, die Anzahl der aufeinanderfolgenden Änderungen an Ihren Attributzuordnungen so gering wie möglich zu halten.



[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=AcomDC_0107_2016-->