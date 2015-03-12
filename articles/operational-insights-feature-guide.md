<properties 
	pageTitle="Operational Insights-Funktionsleitfaden" 
	description="Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Operational Insights-Funktionsleitfaden</h1>

In diesem Leitfaden wird erläutert, welche Probleme Sie mithilfe von Operational Insights lösen können, aus welchen Elementen die Operational Insights-Umgebung besteht und wie Sie ein Operational Insights-Konto erstellen und sich beim Dienst anmelden.

<h2 id="whatisaad">Was ist Operational Insights?</h2>

Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden.

Mit Operational Insights können Sie Computerdaten in betriebliche Intelligenz umwandeln. Sie haben folgende Möglichkeiten:

- Verwalten der Kapazität Ihrer Serverinfrastruktur
- Aktualisieren von Servern durch Systemupdates
- Verstehen der Beziehungen zwischen Daten in Serverprotokolldateien
- Schützen von Servern mit Antischadsoftware
- Verwalten von Sicherheitsrisiken mit Sicherheitsgarantie
- Nachverfolgen von Änderungen an der Konfiguration Ihrer Server
- Optimieren von SQL Server
- Durchsuchen von Ereignis- und IIS-Protokollen
- Installieren von Agents auf virtuellen Maschinen von Microsoft Azure (IaaS)
- Verwenden von Operational Insights ohne Operations Manager  

<h2 id="">Operational Insights-Umgebung</h2>

Die Operational Insights-Umgebung besteht aus folgenden Komponenten:

- Arbeitsbereiche, die von Microsoft Azure gehostet werden und Container für Azure-Konten sind
- Webdienst von Operational Insights, der in der Cloud gehostet wird
- Separate Agents, die eine direkte Verbindung zum Webdienst herstellen,
- oder ein Dienst, der an System Center Operations Manager angefügt ist (nicht unbedingt erforderlich)




Falls Sie die vorherige Version von Operationel Insights, genannt System Center Advisor, verwenden, ist möglicherweise Advisor-Software in Ihrer lokalen Umgebung installiert. Advisor-Software wird jedoch mit Operational Insights nicht unterstützt.

Wenn Sie Operational Insights-Software als Operations Manager-Dienst verwenden, enthält die Umgebung mindestens eine Verwaltungsgruppe und mindestens einen Agent pro Verwaltungsgruppe. Die Operations Manager-Agents sammeln Daten von Ihren Servern und analysieren diese mithilfe von Intelligence Packs (vergleichbar den Management Packs in System Center Operations Manager). Die analysierten Daten werden regelmäßig von Operations Manager an den Operational Insights-Webdienst gesendet. Dazu wird ein Proxyserver verwendet, sodass die anderen Server umgangen und nicht zusätzlich belastet werden.

Auf ähnliche Weise können die auf den einzelnen Computern installierten Agents eine direkte Verbindung zum Webdienst herstellen, um die gesammelten Daten zur Verarbeitung zu senden.

Die Daten in den einzelnen Intelligence Packs werden analysiert und im Operational Insights-Portal dargestellt. Sie können alle Warnungen und die entsprechenden Abhilfemaßnahmen, Konfigurationsbewertungen, Kapazitätsprobleme der Infrastruktur, Status der Systemupdates, Antischadsoftware-Warnungen und Protokolldaten anzeigen. Sie können auch detaillierte Suchvorgänge ausführen.

<h2 id="">Erstellen eines Operational Insights-Kontos und Anmelden</h2>

Richten Sie anhand der folgenden Informationen ein Konto ein, das mit Microsoft Azure Operational Insights verwendet werden soll, und melden Sie sich bei Operational Insights an.



<h3>Abrufen eines Organisations- oder Microsoft-Kontos</h3>

Wenn Sie ein Organisationskonto erstellen möchten, melden Sie sich bei <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">"Microsoft-Konto für Organisationen" an</a>. Anschließend müssen Sie Ihrem Organisationskonto Zugriff auf Operational Insights in <a href="http://aka.ms/Mr1dtz" target="_blank">Windows Azure Active Directory gewähren</a>.


Wenn Sie ein Microsoft-Konto (früher "Windows Live ID" genannt) erhalten möchten, melden Sie sich bei <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">"Registrieren - Microsoft-Konto" an</a>. Mit einem Microsoft-Konto wird Operational Insights automatisch Zugriff gewährt.


<h3>Anmelden bei Operational Insights</h3>

1. Wechseln Sie zu <a href="preview.opinsights.azure.com" target="_blank">Microsoft Azure Operational Insights,</a>  und wählen Sie "Anmeldung" am oberen Rand der Seite aus.
2. Wählen Sie **Microsoft-Konto** oder **Organisationskonto** aus, und **melden Sie sich mit Ihren Anmeldeinformationen an**.
3. Erstellen Sie, wenn Sie dazu aufgefordert werden, ein Operational Insights-Konto, mit dem Ihre Anmeldeinformationen verknüpft werden.
4. Wählen Sie, wenn Sie dazu aufgefordert werden, **Vorschau testen** aus, um die zu verwendende Version von Operational Insights auszuwählen.
5. Geben Sie, wenn Sie dazu aufgefordert werden, Ihren Einladungscode ein, und klicken Sie auf **Übernehmen**, um die Vorschau zu verknüpfen. Wenn Sie keinen Einladungscode haben, geben Sie am unteren Bildschirmrand Ihre Daten ein, um einen Code abrufen.

<!--HONumber=45--> 
