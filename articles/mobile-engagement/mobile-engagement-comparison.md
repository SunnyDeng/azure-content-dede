<properties
	pageTitle="Vergleich von Azure Mobile Engagement mit anderen ähnlichen Azure-Diensten"
	description="Vergleich von Azure Mobile Engagement mit anderen ähnlichen Azure-Diensten – HockeyApp, AppInsights, Notification Hubs"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="piyushjo" />

# Vergleich von Azure Mobile Engagement mit anderen ähnlichen Azure-Diensten

Die Liste der Dienste, die von Microsoft Azure angeboten werden, wächst ständig, und vielleicht fragen Sie sich manchmal, was Azure Mobile Engagement von einem anderen Dienst unterscheidet, von dem Sie gerade gelesen oder gehört haben. Dieser Artikel soll der Verwirrung ein Ende setzen, damit Sie gezielt Azure Mobile Engagement auswählen können, wenn dieser Dienst für Ihre Zwecke am besten geeignet ist.
 
Azure Mobile Engagement ist ein Dienst, der sich speziell an **digitale Vermarkter/CMOs** wendet, jedoch von jedem **Besitzer oder Herausgeber einer mobilen App** verwendet werden kann, der die Nutzung, Beibehaltung und Monetarisierung seiner mobilen Apps steigern möchte.

*Azure Mobile Engagement ist eine als Software-as-a-Service (SaaS) bereitgestellte Benutzerengagementplattform, die Folgendes ermöglicht: datenbezogene Einblicke in die App-Nutzung, Benutzersegmentierung in Echtzeit und kontextbezogene Pushbenachrichtigungen sowie In-App-Messaging.*

Die Aufschlüsselung dieser Definition ergibt die folgenden Hauptmerkmale, die auch sein einzigartiges Wertangebot hervorheben:

1.	**Pushbenachrichtigungen und In-App-Messaging in Abhängigkeit vom Kontext**
		
	Dies ist der Schwerpunkt des Produkts – Ausführen gezielter und personalisierter Pushbenachrichtigungen. Und um dies zu ermöglichen, werden umfassende Verhaltensanalysedaten erfasst.

2.	**Datengesteuerte Einblicke in die App-Nutzung**

	Wir bieten plattformübergreifende SDKs, um die Verhaltensanalysen der App-Benutzer zu erfassen. Beachten Sie den Begriff „Verhaltensanalyse“ (im Vergleich zu „Leistungsanalyse“), da wir uns darauf konzentrieren, „wie“ die App-Benutzer die App verwenden. Wir erfassen grundlegende Leistungsanalysedaten zu Fehlern, Abstürzen usw., aber das ist nicht der Schwerpunkt des Produkts.

3.	**Benutzersegmentierung in Echtzeit**

	Nachdem Sie die Verhaltensanalysedaten der App-Benutzer erfasst haben, können Sie Ihre Zielgruppe anhand verschiedener Parameter und gesammelter Daten segmentieren, damit Sie gezielte Pushkampagnen ausführen können.

4.	**Software-as-a-service (SaaS):**

	Wir verfügen über ein vom Azure-Verwaltungsportal getrenntes Portal, das zum Interagieren und zum Anzeigen umfassender Verhaltensanalysen über die App-Benutzer und zum Ausführen von Marketingpushkampagnen optimiert ist. Das Produkt soll Ihnen in kürzester Zeit den Einstieg ermöglichen!
 
Diese Differenzierung ist hier die Basis des Vergleichs mit anderen scheinbar ähnlichen Azure-Angeboten – beachten Sie, dass der Artikel keinen Vergleich auf Featureebene bietet, sondern anhand eines eher szenarienbasierten Ansatzes definiert, welches Produkt seinen Zweck am besten erfüllt:
 
1.	[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) ist die mobile DevOps-Lösung von Microsoft. Mit dieser Lösung können Sie Builds an Betatester verteilen, Daten sammeln und Benutzerfeedback erhalten. Sie ist auch in Visual Studio Team Services integrierbar und ermöglicht einfache Bereitstellungen von Builds und Integration von Arbeitselementen. 
	
	Der Schwerpunkt liegt hier auf DevOps und dem Erfassen von Leistungsanalysedaten über die mobilen Apps. Möglicherweise integrieren Sie schließlich HockeyApps und Mobile Engagement in Ihre App – und dies wäre nicht ungewöhnlich, denn trotz einiger Überlappungen der gesammelten Daten haben die Produkte unterschiedliche Schwerpunkte und unterstützen Sie dabei, verschiedene Ziele zu erreichen.

2.	[Application Insights](../application-insights/app-insights-overview.md) Wenn Ihre mobile App eine Serverseite hat, verwenden Sie Application Insights, um die Webserverseite Ihrer App zu überwachen – für clientseitige mobile Apps sollten Sie jedoch HockeyApp verwenden.

3.	[Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) bietet einen einfachen Dienst in dem Sinne, dass Sie kein SDK in die mobile Anwendung integrieren müssen und Sie die vollständige Kontrolle über Ihre mobile App haben. Sie können Pushbenachrichtigungen mit grundlegenden Markierungsfunktionen senden. Dies eignet sich hervorragend für alle App-Besitzer, die sich weniger für die Zielgruppenadressierung, sondern mehr dafür interessieren, Informationen sofort an die App-Benutzer zu senden (Übertragung an einen großen Adressatenkreis).

	Beachten Sie, dass der Schwerpunkt hier auf dem Senden blitzschneller Benachrichtigungen mit grundlegender Segmentierungsfunktion liegt.

Wir betrachten nun einige Szenarien:

1.	Tim gehört zum Team für digitales Marketing im Kaufhaus Adventure Works, das mobile Apps für Benutzer veröffentlicht. Tim hat das Ziel, sicherzustellen, dass die Benutzer, die ihre mobilen Apps herunterladen, sie ständig verwenden – und zwar häufig. Dies steigert nicht nur die Markenbindung der App-Benutzer, sondern auch die Monetarisierung, wenn der App-Benutzer Käufe mithilfe der mobilen Anwendung tätigt. Dafür muss Tim gezielte Benachrichtigungen an die App-Benutzer senden, die sie als hilfreich empfinden, um sie aufzufordern, die App aufzurufen und oft zu nutzen. Hierfür setzt Tim Mobile Engagement ein. 

2.	Joann gehört zum Entwicklungsteam für mobile Apps bei Adventure Works und sucht ein Produkt, mit dem sie Details zu Abstürzen und Ausnahmen protokollieren und Leistungstelemetriedaten von einer App erhalten kann. Hierfür setzt Joann HockeyApp ein. Joann könnte sowohl HockeyApp für die Szenarien, auf die sich ihr Interesse als Entwicklerin richtet, als auch Azure Mobile Engagement für Tim in die gleiche App integrieren, um vom Besten von beiden zu profitieren.

3.	Robin gehört zum Entwicklungsteam für mobile Apps der Sendergruppe Contoso News, und sie möchte nur Eilmeldungsalarme ohne großartige Segmentierung an alle Benutzer senden, sobald der Meldungsalarm ausgelöst wird. Hierfür setzt Robin Notification Hubs ein. In diesem Szenario ist es jedoch möglich, dass mit fortschreitender Entwicklung der mobilen App die Anforderung nach umfangreicherer Segmentierung und Abrufen von Details zum Verhalten der App-Benutzer aufkommt. Wenn dieser Zeitpunkt gekommen ist, muss Robin Azure Mobile Engagement auf seine diesbezügliche Eignung beurteilen.
 
Zusammengefasst: Mobile Engagement dient nicht nur zum Sammeln von Analysen – es ist nicht „einfach nur ein weiteres Analyseprodukt von Microsoft“. Es geht um zielgerichtetes Senden von Pushbenachrichtigungen, und für diese Zielgruppenadressierung erfassen wir Verhaltensanalysedaten, aber der Schwerpunkt bleibt das Senden von Pushbenachrichtigungen, die für die App-Benutzer interessant sind und nicht als Spam verkannt werden.

Eine Zusammenfassung weiterer Details bietet Ihnen dieses [kurze Video](mobile-engagement-overview.md) zu Mobile Engagement.

<!---HONumber=AcomDC_0309_2016-->