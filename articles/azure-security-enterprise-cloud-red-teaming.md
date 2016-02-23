<properties
   pageTitle="Microsoft Enterprise Cloud Red Teaming"
   description="In diesem Artikel werden die Microsoft-Strategie und -Vorgehensweise in Bezug auf Red Teaming und Penetrationstests für Live-Websites für von Microsoft verwaltete Cloudinfrastruktur, -dienste und -anwendungen beschrieben. Sie erfahren, wie Microsoft echte Sicherheitsverletzungen und Angriffe simuliert, eine fortlaufende Sicherheitsüberwachung durchführt und die Reaktion auf Sicherheitsvorfälle übt, um die Sicherheit von Microsoft Azure und Office 365 zu überprüfen und zu verbessern."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="yuridio"/>


#Microsoft Enterprise Cloud Red Teaming

Das so genannte „Red Teaming“ geht über Compliance-Akkreditierungen und andere Branchenanforderungen weit hinaus. Es bietet Microsoft und seinen Kunden die zusätzliche Sicherheit, dass für Microsoft-Clouddienste ständig Sicherheitsüberwachungen, Tests und Updates durchgeführt werden, um den sich ständig ändernden Bedrohungen für Kunden und Microsoft zu begegnen.

##Einführung

Unternehmen können sich besser auf die Auswirkungen heutiger und zukünftiger Bedrohungen vorbereiten, indem sie echte Angriffe simulieren und Taktik, Techniken und Verfahren (Tactics, Techniques and Procedures, TTPs) durchspielen, die von entschlossenen und hartnäckigen Angreifern eingesetzt werden. Anstatt nur zu versuchen, das Auftreten von Sicherheitsvorfällen zu verhindern, sollte unbedingt davon ausgegangen werden, dass ein Sicherheitsvorfall eintreten kann und auch wird. Die von Red Teaming und bei Penetrationstests für Live-Websites gewonnenen Informationen tragen zur signifikanten Verstärkung der Verteidigungsmaßnahmen, Verbesserung der Antwortstrategie, Schulung von Verteidigungspersonal und Erhöhung der Effektivität des gesamten Sicherheitsprogramms bei.

Unternehmen können Lücken bei der Erkennung und Reaktionsfähigkeit nicht umfassend ermitteln, indem sie sich allein auf Strategien zur Verhinderung von Sicherheitsverletzungen konzentrieren. Es ist fast noch wichtiger zu verstehen, wie Sicherheitsverletzungen bzw. Angriffe über den reinen Schutz hinaus erkannt und beantwortet werden können, als Maßnahmen zur Verhinderung des Auftretens einer Sicherheitsverletzung zu treffen. Indem Unternehmen für die schlimmsten Fälle planen (per „Wargames“ (Modellübungen und -eindringversuche) und „Red Teaming“ (echte Angriffe und Eindringversuche)), können sie die erforderlichen Fähigkeiten entwickeln, um Eindringversuche zu erkennen, und ihre Reaktionsmöglichkeiten in Bezug auf Sicherheitsverletzungen erheblich verbessern.

###Microsoft Enterprise Cloud Red Teaming 

In diesem Artikel wird beschrieben, wie Microsoft das Red Teaming – eine Form von Penetrationstests für Live-Websites – für von Microsoft verwaltete Infrastruktur, Dienste und Anwendungen einsetzt. Außerdem erfahren Sie, wie Microsoft echte Sicherheitsverletzungen simuliert, die Sicherheit ständig überwacht und die Reaktion auf Sicherheitsvorfälle übt, um die Sicherheit von Microsoft Azure und Office 365 (O365) zu testen und zu verbessern. Beachten Sie aber, dass beim Red Teaming und bei Penetrationstests für Live-Websites keine Endkundendaten vorsätzlich als Ziel dienen. Die Tests werden für Microsoft Azure- und O365-Infrastruktur und -Plattformen sowie für die eigenen Mandanten, Anwendungen und Daten von Microsoft durchgeführt. Mandanten, Anwendungen und Daten von Kunden, die unter Microsoft Azure oder O365 gehostet werden, dienen niemals als Ziel.

###Mehr als nur Vorbeugung 

Strategien und Technologie zur Verhinderung von Sicherheitsverletzungen kann keinen Schutz vor jedem Angriff garantieren. Bei der heutigen Bedrohungslage ist es von entscheidender Bedeutung, sich zu vergegenwärtigen, dass eine Verletzung entweder bereits erfolgt ist oder dies nur eine Frage der Zeit ist. Die Wahrscheinlichkeit ist ziemlich hoch, dass ein Unternehmen bereits angegriffen wurde und dies nur noch nicht erkannt wurde. Die Verinnerlichung dieser Annahme führt dazu, dass Erkennungs- und Antwortstrategien auf eine Art und Weise angepasst werden, die die Grenzen der Infrastruktur, Mitarbeiter, Prozesse und Technologien verschiebt.

![NSA-Zitat](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig1.png)

Laut Data Breach Investigation Report von Verizon aus dem Jahr 2014 wurde die Gefährdung in ca. 80 % der angegriffenen Unternehmen nicht erkannt (S. 12, <http://www.verizonenterprise.com/DBIR/2014/>[)](http://www.verizonenterprise.com/DBIR/2014/). Stattdessen wurden die Unternehmen von einer externen Stelle über die Sicherheitsverletzung informiert, z. B. Kunden, Strafverfolgungsbehörden, Drittanbietern oder externen Agenturen für Betrugsfälle. Und was noch dazu kommt: Diese Zahlen gelten nur für die Fälle, in denen der Angreifer schließlich erkannt wurde. Sie gelten nicht für Fälle, in denen der Angreifer bisher unerkannt bleiben konnte.

In den folgenden Abschnitten wird die Umstellung auf eine neue Sicherheitsstrategie beschrieben, die für Clouddienste von Microsoft verwendet wird, z. B. Microsoft Azure und Office 365. Diese Sicherheitsstrategie, die den Namen „Assume Breach“ (also etwa „Von einer Sicherheitsverletzung ausgehen“) hat, stellt sowohl eine Methodik als auch eine Veränderung der Kultur dar. Sie bewirkt eine Veränderung der grundlegenden Ausrichtung von Design, Engineering und Betrieb, indem davon ausgegangen wird, dass Angreifer bereits Schwachstellen ausgenutzt und sich Zugriff mit Rechten verschafft haben und in Live-Diensten des Produktionsbereichs aktiv sind.

##Herkömmliche Sicherheitsmethodik 

Bei der herkömmlichen Sicherheitsmethodik ging es vor allem um die Verhinderung. Die Verhinderung bzw. Vorbeugung ist eine Verteidigungsstrategie, die auf die Beseitigung von Schwachstellen abzielt und die Wahrscheinlichkeit von Sicherheitsverletzungen vor ihrem Auftreten verringert. Im Online Services-Portfolio von Microsoft (mit Microsoft Azure, Office 365, CRM Online usw.) umfasst dies ständige Verbesserungen der Sicherheitsprozesse durch unsere [*Security Development*](http://www.microsoft.com/security/sdl/default.aspx) [*Operational Security Assurance*](http://www.microsoft.com/download/details.aspx?id=40872)-Programme (OSA). Die Erstellung von Bedrohungsmodellen, die statische Codeanalyse und Sicherheitstests sind hilfreich beim Aufzählen, Reduzieren und Verwalten von Angriffsflächen – können aber nicht alle Sicherheitsrisiken ausräumen.

Ein Beispiel für eine Verhinderungsstrategie ist die Einschränkung des Bediener-/Administratorzugriffs bei Microsoft auf die Mitarbeiter, die den Zugriff wirklich benötigen und die Auswahlkriterien erfüllen (z. B. bestandene Hintergrundüberprüfung, Erfüllung aller Compliance- und Sicherheitsanforderungen, Zugriff aufgrund der Position erforderlich usw.). Außerdem erhalten Administratoren keine dauerhaften Berechtigungen, sondern Just-In-Time-Zugriff (JIT)[1] und „Just Enough Administration“ (JEA)[2]. Weitere Beispiele sind die Trennung der E-Mail-Umgebung der Mitarbeiter von der Produktionsumgebung und die Verwendung spezieller Arbeitsstationen mit starken Sicherheitsmaßnahmen zur Erledigung sensibler Aufgaben.

Wo immer es möglich ist, wird der Eingriff des Menschen durch automatisierte und stark überwachte toolbasierte Prozesse ersetzt. Beispiele für Routinefunktionen sind Bereitstellung, Debugging, Erfassung von Diagnosedaten und Dienstverwaltung. Für Microsoft Online Services wird weiter in die Systemsicherheit und die Automation des Betriebs investiert, um die Gefahr potenzieller Sicherheitsrisiken zu verringern.

Aus Sicht der Betriebstechnologie wird die Anfälligkeit für Angriffe durch Investitionen in Maßnahmen zur Verhinderung von Sicherheitsverletzungen reduziert, aber sie kann niemals vollständig beseitigt werden. Der Einsatz und die ständige Verbesserung der Sicherheitsprogramme (z. B. SDL und OSA) und Technologien von Microsoft sind somit zwar wichtige Mittel zur Vorbeugung gegen Sicherheitsverletzungen, aber es führt kein Weg daran vorbei, dass es für Unternehmen, Dienste und Benutzer zu Sicherheitsverletzungen kommt.

###Neue Bedrohungen 

Während der letzten fünf oder mehr Jahre wurde eine Bedrohungskategorie immer häufiger thematisiert. Mit dem Ausdruck „Advanced Persistent Threat (APT)“ – also etwa „erweiterte dauerhafte Bedrohung“ – wurde der von Nationen unterstützte Versuch bezeichnet, Militäranlagen, Anlagen der Rüstungsindustrie und staatliche Netzwerke mit dem vorrangigen Ziel der Gewinnung sensibler Daten zu infiltrieren. Heute wird die Bezeichnung „APT“ häufig in Medien und Sicherheitskreisen verwendet, um alle Angriffe zu beschreiben, die speziell auf eine einzelne Organisation abzielen oder eindeutig hochtechnischer Natur sind – unabhängig davon, ob der Angriff wirklich erweitert oder dauerhaft war. Allgemeine Merkmale von APT:

- Professionelle Planung

- Spezielles/sequenzielles Ziel

- Effektive Erkundung

- Professioneller Einsatz von Tools

- Social Engineering

Angreifer verfügen heutzutage über beträchtliche Ressourcen und Orchestrierungsmöglichkeiten. Unabhängig von der Professionalität eines Angreifers sind die Trends bei den Sicherheitsvorfällen im Zeitraum von 2009 bis heute ein eindeutiger Hinweis, dass die Wahrscheinlichkeit eines Vorfalls und die Risiken zunehmen. Die zunehmende Professionalität und Zielgerichtetheit von Sicherheitsbedrohungen in Verbindung mit der steigenden Häufigkeit deuten darauf hin, dass – früher oder später – alle Benutzer und Organisationen von Sicherheitsverletzungen betroffen sein werden.

In der aktuellen Bedrohungslage reicht es nicht aus, sich nur auf die Verhinderung zu konzentrieren, um gegen entschlossene und hartnäckige Angreifer zu bestehen. Außerdem ist es mit gängigen Sicherheitstools, z. B. Antivirenprogrammen und Angriffserkennungssystemen, schwierig, die gesamte Bandbreite heutiger Sicherheitsverletzungen abzudecken oder zu entschärfen. Mit Netzwerkkontrollmechanismen lassen sich vielleicht Amateure fernhalten, aber versierte und motivierte Angreifer finden immer einen Weg, um diese Barrieren zu überwinden. Dies führt dazu, dass Unternehmen häufig schlecht vorbereitet sind, wenn sie auf eine Sicherheitsverletzung umfassend reagieren müssen.

Aufgrund der Weiterentwicklung des IT-Bereichs und der vermehrten Nutzung der Cloud lassen sich die Grenzen eines Unternehmens nicht mehr durch ein Umkreisnetzwerk definieren, das physisch oder virtuell mit Firewalls verwaltet wird. Unternehmensdaten, darunter sensible Daten und Anwendungen, können sich an vielen verschiedenen Orten befinden: lokal, in privaten Rechenzentren, in der Cloud, bei Partnern und auf einer Vielzahl von Benutzergeräten. Hierfür ist eine fundamental andere Sicherheitsstrategie erforderlich, und die Sicherheitsmethodik der meisten Unternehmen muss neu ausgerichtet werden.

Die Reaktion auf Sicherheitsverletzungen war schon immer mit großen Herausforderungen verbunden, z. B. dem Identifizieren des Umfangs, schnellen Benachrichtigen von beteiligten Personen und Kunden, Untersuchen des Datenverlusts und Wiederherstellen betroffener Ressourcen. Aufgrund der Kombination, die sich aus den heutigen Angreifern und der Weiterentwicklung des IT-Bereichs ergibt, war die Reaktion auf Sicherheitsverletzungen noch nie so schwierig wie heute. Daher wird bei einer effektiven Sicherheitsstrategie im Gegensatz zum herkömmlichen Ansatz der Verhinderung von Sicherheitsverletzungen angenommen, dass entschlossene und hartnäckige Angreifer alle Verteidigungsmaßnahmen erfolgreich aushebeln können.

##Assume Breach-Methodik 

Die aktuelle Bedrohungslage macht es erforderlich, dass im Rahmen effektiver Sicherheitsstrategien nicht nur in Maßnahmen zur Verhinderung investiert wird, sondern auch in die Erkennung und in Reaktionsfähigkeit. Basierend auf einer ausführlichen Analyse von Sicherheitstrends hat Microsoft begonnen, auf die Wichtigkeit zusätzlicher Investitionen in reaktive Sicherheitsprozesse und Technologien aufmerksam zu machen, bei denen der Schwerpunkt auf der Erkennung und Reaktion auf neue Bedrohungen liegt, nicht nur auf ihrer Verhinderung. Aufgrund der Veränderungen der Bedrohungslage und einer eingehenden Analyse hat sich Microsoft außerdem dazu entschieden, seine Sicherheitsstrategie zu optimieren. Es geht nicht mehr nur um die Verhinderung von Sicherheitsverletzungen, sondern um die bessere Vorbereitung auf den Umgang mit Sicherheitsverletzungen nach ihrem Auftreten. Bei dieser Strategie wird nicht gefragt, „ob“ es zu größeren Sicherheitsvorfällen kommt, sondern „wann“.

Die Assume Breach-Vorgehensweisen von Microsoft gibt es schon seit vielen Jahren, aber die meisten Kunden sind sich dieser im Hintergrund ausgeführten Arbeiten zum Absichern der Microsoft Cloud nicht bewusst. Assume Breach ist ein Ansatz, der als Grundlage für Sicherheitsinvestitionen, Entwurfsentscheidungen und Sicherheitsvorgehensweisen für den Betrieb dient. Bei Assume Breach ist das Vertrauen in Anwendungen, Dienste, Identitäten und Netzwerke eingeschränkt, indem alle Komponenten – sowohl intern als auch extern – als nicht sicher und bereits gefährdet angesehen werden. Die Assume Breach-Strategie wurde zwar nicht aufgrund der Verletzung von Microsoft-Unternehmens- oder -Clouddiensten entwickelt, aber sie war eine Reaktion darauf, dass es bei vielen Unternehmen der gesamten Branche trotz aller Verhinderungsversuche zu Sicherheitsverletzungen gekommen ist.

Die Verhinderung von Sicherheitsverletzungen ist ein wichtiger Bestandteil des Betriebs jedes Unternehmens, aber diese Verfahren müssen ständig getestet und verbessert werden, um modernen Angriffen, z. B. APTs, begegnen zu können. Unternehmen müssen als Vorbereitung auf eine Sicherheitsverletzung zuerst robuste, wiederholbare und gründlich getestete Sicherheitsverfahren zur Reaktion auf Angriffe erstellen und anwenden.

„Prevent Breach“-Sicherheitsprozesse, z. B. die Bedrohungsmodellierung, Codeüberprüfungen und Sicherheitstests werden für sichere Entwicklungslebenszyklen zwar häufig verwendet, aber der Ansatz „Assume Breach“ hat viele Vorteile. Sie ermöglichen ein Gesamtpaket für die Sicherheit, indem reaktive Maßnahmen zur Begegnung von Sicherheitsverletzungen geprobt und gemessen werden. Bei Microsoft möchten wir dies durch fortlaufende „Wargame“-Übungen und Penetrationstests für Live-Websites im Rahmen unserer Reaktionspläne erreichen, um die Erkennung und Reaktionsfähigkeit zu verbessern.

![Prevent Breach und Assume Breach](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig2.png)

*Abbildung 1: Modell „Prevent Breach“ und Modell „Assume Breach“*

Bei Assume Breach verschiebt sich der Sicherheitsschwerpunkt auf die Identifizierung und Schließung von Lücken in folgenden Bereichen:

- Erkennung von Angriffen und erfolgreichen Eindringversuchen

- Reaktion auf Angriffe und erfolgreiche Eindringversuche

- Wiederherstellung nach Datenverlust, Manipulationen oder Gefährdung

- Verhinderung zukünftiger Angriffe und erfolgreicher Eindringversuche

Bei Assume Breach wird überprüft, ob die Schutz-, Erkennungs- und Antwortmechanismen richtig implementiert wurden – sogar mit Blick auf die Reduzierung potenzieller Bedrohungen durch „mit Wissen ausgestattete Angreifer“ (Einsatz legitimer Ressourcen, z. B. übernommene Konten und Computer).

Der Aufbau von Cloud- und Hybrid-Computermodellen erhöht die Bedeutung, die Penetrationstests beim Aufrechterhalten einer sicheren Betriebsumgebung haben, z. B. in Bezug auf die Vorbereitung auf den Umgang mit Sicherheitsverletzungen. Da viele Aspekte der Microsoft Cloud nicht der direkten Kontrolle eines Kunden unterliegen, kümmert sich Microsoft nicht nur um den Schutz vor Angriffen auf Infrastruktur, Plattform und Dienste, sondern auch um die Erkennung und entsprechende Reaktionen.

Aus diesem Grund führt Microsoft so genannte „Wargame“-Übungen und regelmäßige Red Teaming-Aktionen durch, um die Bereitschaft von Microsoft für den Assume Breach-Ansatz auszuwerten und zu verbessern. Durch die Modellierung von echten Angriffen und Penetrationstests kann Microsoft die Fähigkeit testen, Angriffen zu begegnen, Lücken bei Erkennung und Reaktion zu identifizieren und die Ressourcen auf die Schließung dieser Lücken zu richten. Indem Microsoft die Reaktion auf Sicherheitsvorfälle übt und ständig Überwachungs-, Forensik- und Wiederherstellungsmaßnahmen durchführt, sollen kritische Fähigkeiten für den Umgang mit Sicherheitsverletzungen entwickelt werden.

###Ausführung von „Assume Breach“ 

Assume Breach wurde in Microsoft-Clouddiensten ursprünglich in „Wargames“ und anschließend in richtigen Übungen zu Sicherheitsverletzungen (so genannten „Red Team-Sicherheitsverletzungen“) angewendet, mit denen echte Angriffe simuliert werden sollten. Mit Red Team-Sicherheitsverletzungen wird getestet, ob Microsoft auf zielgerichtete und dauerhafte Angriffe reagieren kann. Das Ziel hierbei ist, die durchschnittliche Dauer bis zur Erkennung (Mean-Time to Detect (MTTD))[3] und durchschnittliche Dauer bis zur Wiederherstellung (Mean-Time to Recovery (MTTR))[4] deutlich zu reduzieren.


![Assume Breach-Ausführungszyklus](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig3.png)

*Abbildung 2: Assume Breach-Ausführungszyklus*

„Wargames“ sind Modellübungen, bei denen der Ernstfall geübt wird. Bei Red Team-Übungen werden Sicherheitsverletzungen umfassend simuliert und Sicherheitsvorfälle möglichst realistisch dargestellt, um die zuständigen Personen auf echte Sicherheitsverletzungen vorzubereiten.

„Wargames“ und Red Team-Sicherheitsverletzungen sind eine Möglichkeit, die Reaktion auf Sicherheitsvorfälle vor einem wirklichen Vorfall zu üben. Je häufiger dies geübt wird, desto besser ist das Personal für den Ernstfall gerüstet.


###Wargames 

Bevor wir bei Microsoft Ressourcen für richtige Red Team-Sicherheitsverletzungen bereitgestellt haben, haben wir mit Modellübungen begonnen, die als „Wargames“ bezeichnet werden. Wargame-Übungen ähneln der [SDL-Bedrohungsmodellierung](http://msdn.microsoft.com/magazine/dd148644.aspx)[,](http://msdn.microsoft.com/magazine/dd148644.aspx)[ ](http://msdn.microsoft.com/magazine/dd148644.aspx)sind aber eher auf die Reaktion auf Sicherheitsverletzungen und das Personal eines Unternehmens oder Diensts ausgerichtet, das sich um einen Angriff kümmert. Ziel dieser Wargames ist die Verbesserung der Verfahren für die Reaktion auf Sicherheitsvorfälle, indem Personal aus verschiedenen Gruppen bei Microsoft einbezogen wird – vom Bereich „Security“ über „Engineering“ bis hin zu „Operations“. Als wir Wargames eingehender initiiert und durchgeführt haben, wurde deutlich, welche Gruppen oder Personen fehlten und einbezogen werden mussten.

Alle Einzelpersonen und Gruppen, die bei einer echten Sicherheitsverletzung voraussichtlich benötigt wurden und eingebunden sein mussten, wurden als Beteiligte in unsere Wargame-Übungen einbezogen. Die Liste der Personen wurde nicht nur um die Bereiche Security, Engineering und Operations erweitert, sondern auch um Vertreter nahezu aller Gruppen des Unternehmens, z. B. Rechtsabteilung, Personalverwaltung, Marketing und sogar die Führungsebene. Dank der Einbeziehung vieler Gruppen und Einzelpersonen konnten die erforderlichen Beziehungen über Bereiche und Geschäftseinheiten geknüpft werden, die für die Entwicklung umfassender Pläne zur Reaktion auf Sicherheitsverletzungen unerlässlich waren. In vielen Fällen haben diese Übungen auch dazu geführt, im gesamten Unternehmen den Wissensstand zu verbessern und das Bewusstsein in Bezug auf Risiken und Anforderungen zu wecken, die mit der Reaktion auf Sicherheitsverletzungen verbunden sind.

Das Hauptziel von Wargame-Übungen ist die Sicherstellung der Vollständigkeit von Standardverfahren (Standard Operating Procedures, SOPs) und die Verbesserung von Erkennung und Reaktionsfähigkeit. Für Wargame-Übungen gelten drei (3) Hauptphasen:

-   Identifizierung des Angriffsszenarios

-   Prozess zur Simulation des Angriffs und entsprechende Reaktion

-   Nachbereitung

Bei der Identifizierung des Angriffsszenarios wurde zuerst eine Liste mit potenziellen Angriffen erstellt, die modelliert werden können. Diese Angriffsszenarien umfassten viele unterschiedliche Formen und häufig eine Kombination aus unterschiedlichen Angriffsszenarien, z. B.:

-   Denial of Service

-   Auftreten von Schadsoftware

-   Remoteausführung von Code

-   Angriff auf Kundendaten

-   Unternehmensinterner Angreifer

-   Angriff auf Dienste

Szenarien wurden häufig danach ausgewählt bzw. bevorzugt ausgewählt, wie häufig sie auftreten oder indem bereits gemeldete Sicherheitsvorfälle ausgewertet wurden (von denen Microsoft oder seine Wettbewerber betroffen waren oder die in der Branche aufgetreten sind).

Nach der Auswahl eines Angriffsszenarios übernahmen ein oder mehrere Mitglieder der Gruppe – meist Mitglieder des Sicherheitsteams – die Rolle der Angreifer und erklärten die Ausführung des Angriffsszenarios. Dies ist der Anfang des Prozesses zur Simulation des Angriffs und der entsprechenden Reaktion.

Die anderen Teilnehmer der Wargame-Übung übernahmen die Rolle der Verteidiger und erläuterten, wie sie das Angriffsszenario erkennen und darauf reagieren können – unter Verweis auf die bereits vorhandenen Standardverfahren (bzw. per Identifizierung der Verfahren, die fehlten oder unvollständig waren). Nachdem die Verteidiger mit Schritten zur Erkennung, Untersuchung, Eindämmung und Wiederherstellung nach dem simulierten Angriff reagiert hatten, antworteten die Angreifer mit Gegenangriffen, bei denen häufig Verteidigungsansätze ausgehebelt wurden und die Verteidiger dazu veranlasst wurden, sich eingehender mit der Erkennung und Reaktionsmöglichkeiten zu beschäftigen.

Falls die Verteidiger beispielsweise vorschlugen, die beim Angriff verwendete IP-Adresse zu identifizieren und zu blockieren, wiesen die Angreifer auf Folgendes hin: IP-Quelladressen können leicht geändert, und es kann eine frei erhältliche Angriffsinfrastruktur verwendet werden, z. B. Botnets, um den Angriff über viele verschiedene IP-Adressen zu starten. Dieser ständige Abtausch während der Modellübungen führt zu einer erheblichen Zunahme der Standardverfahren und zur Identifizierung einer großen Zahl von Investitionsanforderungen sowie vielen wertvollen Diskussionen.

Wargames sind zwar sehr nützlich, aber sie unterliegen auch Einschränkungen in Bezug auf den Umfang der Messungen – vor allem, weil es um den Umgang mit ständigen zielgerichteten Angriffen und hartnäckigen Angreifern geht. Daher hat es sich Microsoft zum Ziel gemacht, die Bereitschaft zur Bekämpfung professioneller Angriffe durch entschlossene und hartnäckige Angreifer eingehend auszuwerten und zu messen. Mit Ausnahme der Behandlung echter Sicherheitsverletzungen gibt es hierfür keine bessere Vorgehensweise: die Schaffung echter Sicherheitsverletzungen per „Red Teaming“-Konzept. Red Teaming bezieht sich auf den Einsatz echter Angriffs- und Eindringtaktiken. Red Teaming lässt den theoretischen Aspekt von Wargames Wirklichkeit werden.

### Red Teaming 

Die Assume Breach-Sicherheitsstrategie wird von zwei (2) Kerngruppen ausgeführt: dem „Red Team“ (Angreifer) und dem „Blue Team“ (Verteidiger)[5]. Der Ansatz wird als „Red Teaming“ bezeichnet und besteht im Testen von Microsoft Azure- und Office 365-Systemen und -Vorgängen, indem die gleichen Taktiken, Techniken und Verfahren (Tactics, Techniques and Procedures , TTPs) wie bei echten Angreifern gegen die Live-Produktionsinfrastruktur eingesetzt werden – ohne die Vorkenntnisse zur Infrastruktur und Plattform der Engineering- oder Operations-Teams.

Auf diese Weise werden die Fähigkeiten zur Erkennung und Reaktion auf Sicherheitsverletzungen getestet, und Schwachstellen in der Produktion, Konfigurationsfehler, fehlerhafte Annahmen oder andere Sicherheitsprobleme können in kontrollierter Form identifiziert werden. Auf jede Red Team-Sicherheitsverletzung folgt eine vollständige Offenlegung durch das Red Team und das Blue Team, um Lücken zu ermitteln, Erkenntnisse auszuwerten und die Reaktionsfähigkeit deutlich zu verbessern.

#### Red Team 

Das Red Team besteht aus einer Gruppe von internen Vollzeitmitarbeitern von Microsoft, die sich mit Sicherheitsverletzungen der Infrastruktur und Plattform von Microsoft und der Mandanten und Anwendungen von Microsoft beschäftigen. Bei den Personen handelt es sich um die dedizierten Angreifer (eine Gruppe „ethischer“ Hacker), die zielgerichtete und hartnäckige Angriffe auf Online Services durchführen (Microsoft-Infrastruktur, -Plattformen und -Anwendungen, aber keine Anwendungen und Daten von Endkunden).

Aufgabe des Red Team ist das Angreifen und Eindringen in Umgebungen mit den gleichen Schritten[6], die auch von echten Angreifern ausgeführt werden. Dies ist in Abbildung 3 dargestellt:

![Phasen von Sicherheitsverletzungen](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig4.png)

*Abbildung 3: Phasen von Sicherheitsverletzungen*

Aus diesem Grund ist die Erforschung und das Verständnis von Branchenvorfällen und Bedrohungstrends, um über die neuesten Angriffsmethoden und von Angreifen eingesetzten Tools informiert zu sein, wichtiger Bestandteil jedes Red Team-Ansatzes. Als eine der weltweit am häufigsten angegriffenen Interneteinrichtungen liefert Microsoft Online Services viele Daten zu neuen Bedrohungen. Das Red Team nutzt diese Forschungsergebnisse und Erkenntnisse nicht nur zum Modellieren von echten Taktiken, die von Angreifern eingesetzt werden, sondern auch, um diese Taktiken auszuführen.

Zusätzlich zur Erforschung und Modellierung bekannter Angreifer betreibt das Red Team die Entwicklung und Ableitung eigener neuer Verfahren zum Angreifen von Microsoft Azure und Office 365, indem selbst entwickelte Eindringtools und Angriffsmethoden verwendet werden. Wie entschlossene Angreifer auch, setzt das Red Team neue und gemischte Bedrohungsverfahren für Angriffe ein und ändert seine Taktik, wenn es auf neue Hürden oder Verteidigungsmaßnahmen trifft.

Da die talentierten und motivierten Angreifer Verteidigungsmaßnahmen aushebeln, muss das Red Team dies ebenfalls tun. Mit Kontrollmaßnahmen an Netzwerkgrenzen können vielleicht Amateure abgehalten werden, aber hartnäckige Angreifer finden immer einen Weg. Nach dem Eindringen beschaffen sich Mitglieder des Red Team häufig die Rechte von Mitarbeitern, um eine weitere Ebene zu schaffen und noch tiefer in die Infrastruktur einzudringen. Außerdem errichtet das Red Team – wie alle versierten Angreifer – einen Brückenkopf, um einen dauerhaften Ausgangspunkt zu haben und die Vorgehensweise ständig ändern zu können und so der Entdeckung zu entgehen.

Beispielsweise kann das Red Team benutzerdefinierte Tools (Bots, Fernsteuerung usw.) installieren, um den ständigen Zugriff auf eine betroffene Ressource und das Abrufen von Informationen zu jeder Zeit zu ermöglichen. Dank der Vorgehensweise bei einem Angriff dieser Art kann das Red Team nicht nur sensible Daten abfischen, sondern diese Daten auch einsetzen (wiederum nur Daten von Microsoft und keine Daten von Kunden).

Aufgrund ihrem Umgang mit sensiblen und kritischen Informationen gelten für die Mitarbeiter, die bei Microsoft in Red Teams arbeiten, sehr hohe Standards in Bezug auf Sicherheit und Compliance. Sie müssen zusätzliche Checks, Überprüfungen ihres Hintergrunds und Schulungen durchlaufen, bevor sie sich an Angriffsszenarien beteiligen dürfen. Das Red Team nimmt zwar absichtlich keine Daten von Kunden ins Visier, aber die Mitglieder unterliegen den gleichen Anforderungen in Bezug auf den Zugriff auf Kundendaten (Access To Customer Data,

ATCD) wie normale Mitarbeiter, die Microsoft Azure und Office 365 bereitstellen und verwalten. Darüber hinaus werden von den Red Teams nur die verwaltete Infrastruktur und Plattformen von Microsoft angegriffen. Das Red Team greift keine Daten oder Anwendungen von Endbenutzern an, sondern Mandanten, Anwendungen und Daten in der Cloud, die sich im Besitz von Microsoft befindet und von Microsoft betrieben wird.

Trotzdem gilt für Red Teams eine strenger Verhaltenskodex. Der Verhaltenskodex für Red Teams enthält fünf (5) Hauptrichtlinien:

1.  Verursachen Sie absichtlich keine negativen Auswirkungen oder Ausfälle (bezogen auf Vereinbarung zum Servicelevel (SLA)).

2.  Unterlassen Sie es, absichtlich auf Daten von Kunden zuzugreifen oder diese zu ändern.

3.  Unterlassen Sie die absichtliche Durchführung schädlicher Aktionen.

4.  Unterlassen Sie es, vorhandene Sicherheitsmaßnahmen zu schwächen.

5.  Schützen Sie Informationen zu Schwachstellen und andere kritische Informationen, über die das Red Team verfügt, und geben Sie sie nur an Personen weiter, die diese für ihre Arbeit benötigen.

Darüber hinaus müssen sich die Red Teams für Azure und O365 an dokumentierte Einsatzregeln (Rules of Engagement, ROE) halten, mit denen sichergestellt werden soll, dass der Verhaltenskodex befolgt wird. Diese Einsatzregeln werden von der Führungsebene von Microsoft abgesegnet.

Es ist sehr wichtig, einige Sicherheitskennzahlen nachzuverfolgen, wenn ein Red Team die Sicherheitsverletzungen durchführt:

-   Durchschnittliche Dauer bis zur Gefährdung (Mean Time to Compromise, MTTC)

-   Durchschnittliche Dauer bis zur Erhöhung der Rechte (Mean Time to Privilege Escalation („Pwnage“), MTTP)

Bei der durchschnittlichen Dauer bis zur Gefährdung wird die Zeit vom Beginn der Übungen bis zu dem Zeitpunkt gemessen, an dem das Red Team eine Ressource erfolgreich übernommen hat. Das Red Team richtet dann normalerweise seine Ausgangsbasis in der Zielumgebung ein. Bei der durchschnittlichen Dauer bis zur Erhöhung der Rechte – von Angreifern meist „Pwnage“ genannt – wird die Zeit vom Beginn der Übung bis zur vollständigen Übernahme gemessen. In Active Directory-basierten Umgebungen ist dies beispielsweise der Zeitpunkt, zu dem sich das Red Team Domänenadministratorberechtigungen beschafft bzw. den Domänencontroller infiltriert.

Der MTTP-Zeitpunkt ist meist mit dem Zustand „Game over“ gleichzusetzen, da die meisten Unternehmen auf diese Art von Angriff nicht gut vorbereitet sind. Es sollte aber auch darauf hingewiesen werden, dass Game-over-Szenarien nicht die einzigen Ziele oder Szenarien sind, die von Red Teams demonstriert werden. Zu den Zielen von Red Team-Sicherheitsverletzungen können u. a. Datendiebstahl, Übernahmen auf Mandantenebene und Denial-of-Service-Angriffe sein. Außerdem können MTTC- und MTTP-Kennzahlen pro Übung berechnet oder pro Zielressource oder Ressourcentyp nachverfolgt und berechnet werden, um die Präzision zu erhöhen.

Die Rolle der Red Teams von Microsoft besteht darin, Lücken in den Sicherheitsmaßnahmen des Ziels zu ermitteln. Dank der Nachverfolgung von MTTC und MTTP kann Microsoft eine „Baseline“ bestimmen, die die Grundlage für ständige Verbesserungen darstellt. Indem per Red Teaming Schwachstellen ausgenutzt und Lücken bei der Überwachung und Wiederherstellung aufgedeckt werden, kann eine Verbesserung von Reaktionstools und des Prozesses erreicht werden (mehr hierzu weiter unten). Am wichtigsten ist Folgendes: Anhand von Angriffen sorgt Red Teaming für anschauliche Auswirkungen, mit denen die Wichtigkeit des Ansatzes „Assume Breach“ verdeutlicht wird. Red Teaming führt dazu, dass geschäftliche Risiken ermittelt werden, und kann zur Rechtfertigung von Verteidigungsressourcen, Prioritäten und Investitionsanforderungen dienen.

#### Blue Team 

Das Blue Team besteht entweder aus dediziertem Sicherheitspersonal, dessen Aufgabe die Reaktion auf Sicherheitsverletzungen ist, oder aus Mitgliedern von Sicherheitsteams oder der Bereiche Engineering und Operations. Unabhängig von ihrer Zusammenstellung sind sie unabhängig und agieren getrennt vom Red Team. Das Blue Team hält sich an etablierte Sicherheitsprozesse und setzt die neuesten Tools und Technologie ein, um auf Angriffe und Eindringversuche zu reagieren.

Genau wie bei echten Angriffen weiß das Blue Team nicht, wann oder wie die Angriffe des Red Team erfolgen oder welche Methoden verwendet werden. Seine Aufgabe besteht darin, alle Sicherheitsvorfälle zu erkennen und darauf zu reagieren – unabhängig davon, ob es sich um einen Red Team-Angriff oder einen echten Angriff handelt. Aus diesem Grund ist das Blue Team rund um die Uhr und an 365 Tagen im Jahr im Einsatz und muss auf Red Team-Sicherheitsverletzungen genauso wie auf die Aktionen anderer Angreifer reagieren.

Wenn ein Angreifer, z. B. das Red Team, eine Umgebung angreift, hat das Blue Team folgende Aufgaben:

-   Sammeln der vom Angreifer hinterlassenen Beweise

-   Erkennen der Beweise als Anzeichen für einen Angriff

-   Warnen der jeweiligen Engineering- und Operations-Teams

-   Selektieren der Warnungen, um zu ermitteln, ob eine weitere Untersuchung erforderlich ist

-   Ermitteln des Kontexts aus der Umgebung, um den Umfang des Vorfalls zu bestimmen

-   Aufstellen eines Aktionsplans zur Eindämmung oder Beseitigung des Angreifers

-   Ausführen des Aktionsplans und Durchführen der Wiederherstellung

Diese Schritte bilden die Schrittfolge als Reaktion auf den Sicherheitsvorfall, die parallel zur Schrittfolge des Angreifers ausgeführt wird. Dies ist in Abbildung 4 dargestellt:

![Reaktionsphasen bei einer Sicherheitsverletzung](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig5.png)

*Abbildung 4. Reaktionsphasen bei einer Sicherheitsverletzung*

Kurz gesagt: Dank Red Team-Sicherheitsverletzungen kann das Blue Team die umfassende Erkennung und Reaktion auf echte Angriffe üben. Am wichtigsten ist, dass so die Einübung von Reaktionen auf Sicherheitsverletzungen vor einem echten Angriff (durch einen deutlich weniger kooperativen Gegner) möglich ist. Außerdem kann das Blue Team aufgrund von Red Team-Sicherheitsverletzungen sein Bewusstsein für Situationen verbessern. Dies kann für den Umgang mit zukünftigen Angriffen nützlich sein (durch das Red Team oder einen anderen Angreifer).

Während des gesamten Erkennungs- und Reaktionsprozesses produziert das Blue Team verwertbare Daten und erhält Einblick in die tatsächlichen Bedingungen der zu verteidigenden Umgebungen. Dies wird häufig durch Datenanalysen und forensische Maßnahmen erreicht, die vom Blue Team durchgeführt werden, wenn auf Red Team-Angriffe reagiert wird, und durch die Einrichtung von Bedrohungsindikatoren, z. B. Gefährdungsindikatoren.

Nahezu genauso, wie das Red Team Lücken in den Sicherheitsmaßnahmen ermittelt, suchen Blue Teams nach Schwachstellen bei der Erkennung und Reaktionsfähigkeit. Da vom Red Team echte Angriffe nachgestellt werden, kann für das Blue Team präzise bewertet werden, wie gut (oder wie schlecht) entschlossenen und hartnäckigen Angreifern begegnet werden kann.

Schließlich werden bei Red Team-Sicherheitsverletzungen sowohl die Bereitschaft als auch die Auswirkungen der Reaktionsfähigkeit von Unternehmen gemessen.

Beispiele für Kennzahlen, die vom Blue Team häufig ausgewertet werden:

- Geschätzte Zeit bis zur Erkennung (Estimated Time to Detection, ETTD)

- Geschätzte Zeit bis zur Wiederherstellung (Estimated Time to Recovery, ETTR)

Der Grund dafür, warum dies „geschätzte Zeiten“ sind, ist: Während eines Angriffs kann sich das Blue Team – unabhängig vom Zeitpunkt der Erkennung – nicht vollkommen sicher sein, wann das Red Team einen erfolgreichen Angriff durchgeführt hat. Das Blue Team kann sich auch nicht vollkommen sicher sein, ob die Beseitigung des Red Team-Teams erfolgreich war und die Wiederherstellung nach einer Sicherheitsverletzung vollständig durchgeführt werden konnte. Der fehlende Beweis für die Anwesenheit des Red Team ist noch kein Beweis für die Beseitigung des Angreifers und die erfolgreiche Wiederherstellung. Dies ist der Grund, warum die Reaktion auf Sicherheitsvorfälle so aufwändig ist. Der Vorgang ähnelt dem Zusammensetzen eines Puzzles – Puzzleteil für Puzzleteil –, ohne zu wissen, wie das letzte Puzzleteil aussieht und welche Teile noch zum fertigen Bild fehlen.

Wenn das Blue Team über ein stärkeres Bewusstsein für die gesamte Bedrohungslage und einen besseren Einblick in die tatsächlichen Bedingungen in der jeweiligen Umgebung verfügt, kann es die Maßnahmen und Verfahren zur Reaktion auf Sicherheitsvorfälle verbessern, um auf den Umgang mit einem Angriff besser vorbereitet zu sein.


#### Red gegen Blue 

Im Gegensatz zu herkömmlichen Penetrationstests geht es bei Red Team-Sicherheitsverletzungen vorrangig darum, echte Bedrohungen zu simulieren, einschließlich des dazugehörigen „Gefechtsnebels“[7]. Dies bedeutet, dass das Blue Team nicht weiß, was das Red Team tut (und umgekehrt). Wenn das Red Team eine Ressource angreift, kann dadurch eine Sicherheitswarnung oder ein Vorfall ausgelöst werden, ohne dass sich das Team dessen bewusst ist.

Umgekehrt kann das Red Team Warnungen auch absichtlich auslösen, um Überwachungsmaßnahmen zu überprüfen bzw. zu validieren oder um sie im Rahmen einer Ablenkungstaktik zu nutzen. Während einer Übung kann das Red Team Reaktionsaktivitäten beobachten, z. B. die Zurücksetzung von Kennwörtern, die Wiederherstellung betroffener Ressourcen, die Entfernung von Beständigkeitsmechanismen oder sogar die vollständige Entfernung aus der Umgebung (obwohl der letzte Fall selten eintritt).

Auf diese Weise können bei Red Team-Sicherheitsverletzungen die Auswirkungen der Erkennung und Wiederherstellung beobachtet werden. Die Erkennung und Wiederherstellung können vom Red Team oder Blue Team allein aber nicht exakt bewertet werden. Sowohl die durchschnittliche Dauer bis zur Erkennung (MTTD) als auch die durchschnittliche Dauer bis zur Wiederherstellung (MTTR) kann nur am Ende der Übung berechnet werden, wenn beide Teams, das Red Team und das Blue Team, alles offenlegen. Ein Red Team muss wie folgt vorgehen, damit alle Auswirkungen auf Erkennung und Reaktionsfähigkeit gegeben sind:

- Emulieren eines großen TTP-Umfangs basierend auf Daten zu Angreifern einer Organisation

- Verwenden von TTPs über die gesamte Schrittfolge, um die Erkennung und Reaktionsfähigkeit zu üben und zu überprüfen

- Nutzen technischer und operativer professioneller Methoden

- Sicherstellen anhand von wiederholten Vorgängen, dass die Verbesserungen der Erkennung und Reaktionsfähigkeit ausreichen, um gegen entschlossene und hartnäckige Angreifer zu bestehen

Um sicherzustellen, dass mit der Übung die Initiierung und Einbeziehung des Blue Team verbunden ist, kann die Erkennung und Reaktion vom Red Team absichtlich ausgelöst werden. Dies geschieht normalerweise, nachdem das Red Team alle vorrangigen Ziele erreicht hat und keine Aktivitäten des Blue Team erkennen kann.

Ein Beispiel für das Auslösen der Erkennung und Reaktion ist das absichtliche Verursachen eines Antischadsoftware-Scans für eine Ressource, z. B. einen Domänencontroller (DC), um eine Warnung in Verbindung mit einer EICAR-Testdatei[8] auszulösen. Das Auftreten einer unerwarteten Sicherheitswarnung, besonders für eine kritische Ressource wie den DC, sollte zu Reaktionsmaßnahmen durch das Blue Team führen. Mit dem Erstellen eines Sicherheitsvorfalls wird dafür gesorgt, dass ein umfassender Angriff geprobt werden kann – von der Gefährdung bis zur Reaktion. Wenn das Blue Team nicht beteiligt ist, kann die Reaktion auf den Angriff nicht richtig geübt werden.

Im Idealfall weiß das Blue Team (am Anfang) nicht, ob ein Angriff durch einen echten Angreifer oder vom internen Red Team ausgelöst wurde. Nach Möglichkeit sollten Sicherheitsvorfälle vom Blue Team immer wie echte Vorfälle behandelt werden, und zwar mindestens bis zu dem Zeitpunkt, zu dem sie dem Red Team eindeutig zugeordnet werden können. Dieser Ansatz der Simulation echter Angriffe ist das beste Verfahren zur Einrichtung von Verteidigungsmaßnahmen, die für echte Angriffe geeignet sind.


#### Nachbereitung der Red Team-Sicherheitsverletzung 

Am Ende jeder Red Team-Sicherheitsverletzung kommen das Red Team und das Blue Team zu einer Nachbereitung zusammen, bei der die Sicherheitsverletzung und die Reaktion darauf ausgewertet werden. In dieser Phase tauschen die Teams Taktiken und Erfahrungen aus und legen alle Informationen offen. Das Red Team liefert alle Details dazu, wann und wie die Umgebung angegriffen wurde, welche Ressourcen gefährdet bzw. manipuliert und vom Red Team kontrolliert wurden, ob das Blue Team beim Aussperren des Red Team teilweise oder ganz erfolgreich war und ob die Wiederherstellung nach der Sicherheitsverletzung abgeschlossen werden konnte.

Außerdem liefert das Blue Team alle Details dazu, wie und wann das Red Team erkannt wurde (falls zutreffend), welche Ressourcen als gefährdet identifiziert wurden, welche Red Team-Beständigkeitsmechanismen erkannt wurden und welche Reaktionsschritte bei dem Versuch ausgeführt wurden, das Red Team auszusperren und die Wiederherstellung nach dem Angriff durchzuführen. Dies ist eines der speziellsten und kritischsten Elemente von Red Teaming: Das Red Team ist quasi der einzige Angreifer, der Details zur Sicherheitsverletzung sowie Feedback zum Erfolg (falls zutreffend) der Erkennungs- und Reaktionsmaßnahmen bereitstellen kann und dies auch tut. Die Möglichkeit des Austauschs von Notizen zwischen dem Red Team und dem Blue Team ist wichtig, um sicherzustellen, dass alle Lücken bei der Erkennung und Reaktionsfähigkeit erkannt werden.

Nur im Rahmen der Nachbereitung während der vollständigen Offenlegung können die echten Kennzahlen für die durchschnittliche Dauer bis zur Erkennung (MTTD) und durchschnittliche Dauer bis zur Wiederherstellung (MTTR) berechnet werden – z. B. indem die Differenz zwischen dem MTTC-Wert des Red Team und dem ETTD-Wert des Blue Team verglichen wird, um den tatsächlichen MTTD-Wert zu berechnen. Diese Kennzahlen für die Erkennung und Reaktionsfähigkeit – sowohl während des Vorfalls als auch nach dem Vorfall – sind hilfreich, um geschäftliche Risiken in Bezug auf Lücken bei der Erkennung und Wiederherstellung aufzulisten, die im Rahmen der Übung identifiziert werden.

Der größte restliche Teil der Nachbereitung wird dafür aufgewendet, dass das Red Team und das Blue Team gemeinsam die Schwachstellen und die Investitionsanforderungen ermitteln, die zu einer Verbesserung des Schutzes, der Erkennung und der Reaktionsfähigkeit führen. Bei jeder Red Team-Sicherheitsverletzung werden sowohl vom Red Team als auch vom Blue Team die erforderlichen Investitionen identifiziert, um besser vor zukünftigen Angriffen geschützt zu sein, Angreifer auszubremsen und die Erkennung und Reaktionsfähigkeit zu beschleunigen. Anders ausgedrückt: eine Erhöhung des MTTC-Werts und eine Reduzierung der Werte für MTTD und MTTR.

Im letzten Schritt der Nachbereitung werden vom Red Team und vom Blue Team ausführliche schriftliche Berichte erstellt. Diese Berichte enthalten Angriffszeitleisten für das Red Team und das Blue Team, eine Zusammenfassung der geschäftlichen Auswirkungen der Übung und eine ausführliche Liste mit Schwachstellen, Erkenntnissen und Investitionen, die zur Verbesserung der Erkennung und Reaktionsfähigkeit in Bezug auf Sicherheitsverletzungen getätigt werden müssen.

### Sicherheitsprinzipien 

Ein wichtiger Vorteil von Red Teaming ist die Zusammenführung von Sicherheitsexperten und „ethischen“ Hackern. Wie in jedem anderen Beruf auch sind Techniker und Bediener auf bestimmte Bereich spezialisiert, und es ist gut möglich, dass dadurch nicht die Fähigkeiten zum Abwehren von Angriffen und Eindringversuchen abgedeckt werden. Bei Microsoft kennen sich Techniker und Bediener in den Bereichen SDL/OSA und Common Engineering Criteria (CEC) gut aus, aber das Red Team verlagert die erforderlichen Sicherheitstests an die äußeren Grenzen einer Produktionsumgebung, deren Durchführung ansonsten viel zu aufwändig wäre.

Durch die Investition in Red Teaming mit dem alleinigen Schwerpunkt auf dem Verständnis, der Kommunikation und der Nutzung der neuesten Bedrohungen bei Angriffen auf Live-Websites zielt Microsoft darauf ab, seine Reaktionsfähigkeit ständig zu testen und zu verbessern und wichtige Sicherheitsprinzipien einzuhalten:

- Verhinderung der Ausrichtung einer Sicherheitsstrategie auf **statische Angriffsszenarien** oder der Annahme, dass Angriffe nur von **einer festen Position** ausgehen

- Einsatz von **Schichten sich gegenseitig ergänzender Sicherheitskontrollen** (mit dem Ergebnis, dass kumulativ vorgegangen wird und die Verteidigungsmaßnahmen verbessert werden)

- **Anzahl und Verteilung** von Sicherheitskontrollen ist wichtiger als die individuelle Effizienz

- Konzentration auf **Verzögerung und Reaktion** anstatt auf die **Verhinderung** eines Angriffs

## Zusammenfassung 

Unternehmen in der gesamten Branche werden mit der Tatsache konfrontiert, dass ihre Daten bereits angegriffen wurden bzw. ständig gefährdet sind. Verschlimmert wird diese Situation dadurch, dass viele Unternehmen sich gar nicht darüber im Klaren sind, dass auch sie angegriffen wurden und werden. Bei der heutigen Bedrohungslage ist es unerlässlich, die Anfälligkeit für Angriffe zu verringern, einschließlich der Bedrohungen durch Insider bzw. Personen im Unternehmen. Die wichtigste Anforderung besteht für Unternehmen darin, die durchschnittliche Dauer bis zur Erkennung und Wiederherstellung nach einem Angriff deutlich zu verringern.

In diesem Whitepaper wird beschrieben, warum Unternehmen beim Umgang mit Sicherheitslücken flexibler werden müssen. Mit einer „Assume Breach“-Sicherheitsstrategie setzt Microsoft die niemals endende Suche nach umfassenderen und eingehenderen Investitionen in die Sicherheit fort. Alle Unternehmen können davon profitieren, dass sie ähnliche Sicherheitsstrategien zur Bekämpfung aktueller und neuer Bedrohungen einführen.

Microsoft entwickelt Ansätze zur Verwaltung von Sicherheitsvorfällen, indem Gefährdungen im Voraus geplant und Angriffe und Eindringversuche anhand von „Wargames“ und einem aktiven Red Team simuliert werden. Beim „Red Teaming“ werden theoretische Szenarien Wirklichkeit, indem Sicherheitslücken offengelegt, Live-Schwachstellen ausgenutzt und handfeste Beweise dafür geliefert werden, dass der „Assume Breach“-Ansatz erforderlich ist.

Die anspruchsvolle Aufgabe des Blue Team besteht darin zu zeigen, dass Angriffe richtig erkannt werden und richtig darauf reagiert wird. Indem die Vorgehensweise bei Sicherheitsverletzungen mit einem Red Team und einem Blue Team geübt wird, können sich Sicherheitsabteilungen auf wichtige Angriffsvektoren konzentrieren, Gegenmaßnahmen entwickeln und ihre kritischen Reaktionsverfahren verbessern, die zur Vorbereitung auf entschlossene und hartnäckige Angreifer erforderlich sind.

Red Teaming ist zu einem der wichtigsten Bestandteile bei der Entwicklung und beim Schutz der Infrastruktur, Plattform und Dienste von Microsoft geworden. Die Red Teams für die Bereiche Microsoft Azure und Office 365 geben sich als professionelle Angreifer aus und ermöglichen es Microsoft, die Sicherheit zu überprüfen und zu verbessern, die Verteidigungsmaßnahmen zu verstärken und die Effektivität der Cloudsicherheitsprogramme für Unternehmen zu steigern.

Anhand von regelmäßigen Angriffen auf Live-Websites und Eindringversuche stellen Red Team-Sicherheitsverletzungen das kritische Mittel dar, um die Reaktion auf Sicherheitsvorfälle zu üben und die Verteidigungsbereitschaft und die Auswirkungen von echten Angriffen präzise zu messen. Kunden können darauf vertrauen, dass Microsoft ständig an der Verbesserung des Schutzes, der Erkennung und der Reaktionsfähigkeit arbeitet, um noch besser geschützte Clouddienste bereitstellen zu können.

## Referenzen und weitere nützliche Informationen 

Die folgenden Ressourcen enthalten allgemeinere Informationen zu Microsoft Azure- und Office 365-Diensten sowie zu speziellen Punkten, die im Haupttext erwähnt werden:

- [Microsoft Azure-Startseite](http://azure.microsoft.com) – Allgemeine Informationen und Links zu Microsoft Azure

- [Microsoft Office-Startseite](http://products.office.com) – Allgemeine Informationen und Links zu Microsoft Office

- [Microsoft Azure Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure)

- [Microsoft Office 365 Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Office-365)

- [Microsoft Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)

- Microsoft Azure oder Office 365 – Meldungen:] o [**http://www.microsoft.com/security/msrc/default.aspx*](http://www.microsoft.com/security/msrc/default.aspx)[](http://www.microsoft.com/security/msrc/default.aspx) oder per E-Mail an **secure@microsoft.com*

- [Interview with Mark Russinovich on Microsoft Azure & Security – Update on Penetration Testing](http://blogs.technet.com/b/matthewms/archive/2013/07/02/an-interview-with-mark-russinovich-on-windows-azure-amp-security-update-on-penetration-testing.aspx) (Interview mit Mark Russinovich zu Microsoft Azure & Security – Update zu Penetrationstests)

- [Red vs. Blue – Internal security penetration testing of Microsoft Azure and O365](http://azure.microsoft.com/documentation/videos/red-vs-blue-internal-security-penetration-testing-of-microsoft-azure/) (Rot gegen Blau – Interne Penetrationssicherheitstests für Microsoft Azure und O365)



## ANHANG 

### Unterschied zwischen Red Teaming und Penetrationstests 

Bei der standardmäßigen Vorgehensweise für Penetrationstests werden gängige Tools verwendet, um nach Möglichkeit Schwachstellen auszunutzen. Das gesamte Arsenal eines professionellen Angreifers wird aber nur selten eingesetzt.

Herkömmliche Penetrationstests stellen nur einen bestimmten Zeitpunkt dar, und danach ist der Test vorbei, ohne dass die Umgebung und das Reaktionsteam weiteren Angriffen ausgesetzt ist, wie es bei einem echten Angreifer der Fall ist. Beim Red Teaming gehen die Angriffe auch nach Abschluss eines Tests weiter. Red Teaming ist geeignet, um Dinge aufzudecken, die bei anderen Ansätzen von Penetrationstests häufig nicht erkannt werden (Zusammenfassung unten in Tabelle 1):

**HERKÖMMLICHE PENETRATIONSTESTS RED TEAMING**

| | Herkömmlicher Penetrationstest | Red Teaming |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Persistenz** | Wird beendet, wenn der Angriff auf die Umgebung erfolgreich war | Einrichtung eines Persistenzmechanismus, um dauerhaft Zugriff zu haben und die Vollständigkeit der Wiederherstellung nach einer Sicherheitsverletzung auszuwerten |
| **Tools** | Verwendung der gerade verfügbaren Tools, danach Warten auf den nächsten Testzyklus | Neue Exploits und Schwachstellen werden ständig untersucht, Angriffe mit neuen Tools werden sofort nach deren Entdeckung implementiert |
| **Aktivitäten nach der Sicherheitsverletzung** | Es wird nur versucht, in die Umgebung einzudringen, danach meist keine weiteren Schritte | Die Sicherheitsverletzung wird genutzt, um kritische Daten abzuschöpfen und für weitere Angriffe zu nutzen |
| **Entkommen und Verschleiern** | Bei statischen Tests wird meist nicht versucht, der Erkennung zu entkommen, und danach wird der Vorgang beendet | Mit dynamischen Tests wird versucht, der Erkennung zu entkommen |
| **Sachkundige Angreifer** | Es werden meist die Sicherheitssysteme angegriffen, deren Vorhandensein bekannt ist (aufgrund genauer Kenntnis der IT-Systeme), es wird getestet, ob sie richtig bereitgestellt wurden, oder bekannte Exploits werden genutzt | Es wird ermittelt, was bereitgestellt wurde *und* was nicht bereitgestellt wurde, um Schwachstellen zu ermitteln; Insiderwissen und benutzerdefinierte Tools werden eingesetzt, um Systeme anzugreifen, z. B. durch die Entwicklung benutzerdefinierter und bisher unbekannter Exploits |
| **Live-Website** | Fast nie Angriffe auf vollständige Produktionssysteme, sondern wird meistens zum Testen von Systemen in der Phase vor der Bereitstellung verwendet | Wird für Angriffe auf Produktionssysteme verwendet, alle Schichten des Stapels werden angegriffen |
| **Motivation** | Es werden Annahmen zur Umgebung getroffen, es werden keine Bereiche getestet, die vorher ausgeschlossen wurden | Keinerlei Annahmen werden getroffen, und es wird versucht, alles anzugreifen. Bei Bedarf werden Vorgehensweise und Taktik geändert |
| **Ergebnisse** | Es wird angezeigt, welche Tests durchgeführt wurden und welche Ergebnisse erzielt wurden (Erfolg/Misserfolg) | Werte für MTTC, MTTP, MTTD und MTTR werden gemessen und genau bewertet. Es wird ausführlich aufgezeigt, wann und wie die Umgebung angegriffen wurde, welche Ressourcen übernommen wurden und ob die Erkennung und die Reaktionsmaßnahmen erfolgreich waren. |


**Tabelle 1: Zusammenfassung der Unterschiede zwischen herkömmlichen Penetrationstests und Red Teaming**


##Warum sind simulierte Angriffe auf die Produktion sinnvoll? 

Test- und Präproduktionsumgebungen sind mit Produktionsumgebungen nie identisch. Während der Bereitstellung vorgenommene Änderungen, fortlaufende Wartungsmaßnahmen und Unterschiede bei der Vorgehensweise für Kunden in der Produktion – all dies trägt zu einer Veränderung des Zustands bei. Diese Abweichung hat einen guten Grund, da in der Produktion andere Anforderungen in Bezug auf die erwartete Auslastung und akzeptable Ausfallzeiten sowie in den Bereichen Sicherheit, Compliance und gesetzliche und vertragliche Vorgaben gelten.

Beim Red Teaming werden echte Systeme mit Penetrationstests für Live-Websites angegriffen, anstatt nur eine Entwicklungs- oder Testumgebung in einem Testlabor mit hypothetischen Workloads oder auch echte Komponenten mit simulierten Daten anzugreifen. Dies ist einer der wichtigsten Punkte, in denen sich das Red Teaming von herkömmlichen Sicherheitsstrategien unterscheidet: echte Server, echte Mandanten und echte Daten. Penetrationstests für Live-Websites in der Produktion ermöglichen eine deutlich vertrauenswürdigere und präzisere Messung der Auswirkungen für Kunden und das Unternehmen, die mit einer Sicherheitsverletzung verbunden sind.

Das Eindringen in Live-Websites in der Produktion bietet noch weitere Vorteile. Die Verteidigungsmaßnahmen in Bezug auf die Erkennung und die Reaktion werden einem echten Test unterzogen. Bei echten Angriffen wird die Stärke (oder Schwäche) von Schutzmaßnahmen, Erkennungsstrategien und der Reaktionsfähigkeit getestet, die in der Produktion eingesetzt werden. Dabei muss für diese Investitionen die Tatsache akzeptiert werden, dass die Produktionsumgebung angegriffen wird und sich derzeit unter Umständen in einem manipulierten Zustand befindet. Es wird deutlich, dass das für die Reaktionsmaßnahmen verantwortliche Team regelmäßig die Abläufe zur Bekämpfung zielgerichteter Angriffe und hartnäckiger Angreifer üben muss.

##Einsetzen eines Red Team 

Für die Nutzung des Assume Breach-Ansatzes ist nicht unbedingt eine Investition in Ihr eigenes Red Team-Personal erforderlich. Viele Vorteile lassen sich erzielen, indem Sie einfach Ihren Ansatz in Bezug auf die Sicherheitstests und die Bereitstellung Ihrer Infrastruktur und Anwendungen ändern. Wenn Sie sich beispielsweise darüber im Klaren sind, dass eine Sicherheitsverletzung noch lange nach dem eigentlichen Angriff weiterbestehen kann, ist dies für den Entwurf von Schutzmaßnahmen hilfreich, mit denen Daten und kritische Ressourcen im Falle eines Angriffs geschützt werden.

Red Teaming ist aber eine effektive Möglichkeit, um die Richtigkeit Ihrer Sicherheitsmaßnahmen und -prozesse zu testen, ein Bewusstsein für die aktuelle Lage zu wecken und sicherzustellen, dass Techniker, Personal, Reaktionsteams und das Management über neue Bedrohungen informiert sind. Berücksichtigen Sie die folgenden Punkte, um zu bestimmen, welche Prioritäten Sie bei der Investition setzen sollten:

-   **Prevent Breach und Assume Breach:** Diese Ansätze sind nicht gegenseitig austauschbar. Prevent Breach ist die kritische Implementierung von Sicherheitsfunktionen zur Verteidigung gegen Angriffe. Assume Breach geht weit über diese Schutzmaßnahmen hinaus, um vor, während und nach einer Sicherheitsverletzung die Erkennung und Reaktionsfähigkeit sicherzustellen.

-   **Identifizieren der „Kronjuwelen“:** Legen Sie Prioritäten für die Investitionen fest, indem Sie den Ansatz „Assume Breach“ verwenden. Was sind die kritischsten Daten für das Unternehmen? Wo werden diese Daten gespeichert? Welche Ressourcen sind für den Betrieb des Unternehmens am wichtigsten?

-   **Identifizieren relevanter Übungen:** Legen Sie Prioritäten für Sicherheitsübungen basierend auf Trends bei den Sicherheitsverletzungen und den „TTPs“ der Angreifer fest, die für Ihre Umgebung gelten.

-   **Einholen der Genehmigung:** Dies gilt besonders für die Genehmigung der oberen Führungsebene sowie für die Genehmigung durch die Rechtsabteilung. Außerdem sollten die unterschiedlichen Arbeitsgruppen/Bereiche Ihres Unternehmens über Ihre Arbeit informiert sein und ihre Zustimmung gegeben haben. Wenn Angriffe gegen Bereiche gestartet werden, die noch nie zuvor angegriffen wurden, kann dies zu ungewollten Dienstunterbrechungen und Unzufriedenheit bei Kunden führen.


Nachdem Sie einen Plan für die Durchführung von Red Teaming erstellt haben, müssen Sie Ihre Betriebsabläufe schützen:

-   **Mitarbeiter:** Achten Sie darauf, dass bei Assume Breach auf beiden Seiten (Red Team und Blue Team) geeignete Experten mitarbeiten. Das Blue Team sollte alle Sicherheitsvorfälle, einschließlich der Red Team-Sicherheitsverletzungen, wie echte Vorfälle behandeln. Wägen Sie die Vor- und Nachteile von Vollzeitmitarbeitern und Outsourcing ab, vor allem auch die Sicherheit des geistigen Eigentums, das beim Red Teaming produziert und angegriffen wird. Falls Sie einen Drittanbieter nutzen, sollten Sie seinen *Leistungsumfang*, die Haftungsregelung und seine Reputation prüfen.

-   **Überprüfung:** Die Teammitglieder müssen nicht nur über die erforderlichen Fähigkeiten verfügen, sondern auch vertrauenswürdig sein. Überprüfen Sie potenzielle Mitarbeiter eingehend, einschließlich ihres Hintergrunds.

-   **Schulung:** Wenn Sie Vollzeitmitarbeiter einstellen, sollten sie Schulungen zu standardmäßigen ethischen, sicherheitsbezogenen, datenschutzrechtlichen und Compliance-bezogenen Vorgehensweisen erhalten. Außerdem sollten sie in den jeweiligen besonderen Anforderungen Ihres Unternehmens bzw. Ihrer Branche geschult werden (externe Agenturen sollten zertifiziert sein, um regelmäßige Schulungsaktivitäten zu gewährleisten).

-   **Auditing:** Es ist sehr wichtig, dass die Aktivitäten des Red Team – vor und während der Übungen – angemessen überwacht, protokolliert und per Auditing begleitet werden. Diese Maßnahmen dienen quasi als die Karte „Sie kommen aus dem Gefängnis frei“, falls das Red Team für Schäden verantwortlich gemacht werden sollte, die keine Folge der Übung sind. Hiermit kann sichergestellt werden, dass das Red Team sich an einen strengen Verhaltenskodex hält und die Einsatzregeln einhält. Außerdem sind diese Protokolle und Auditing-Aufzeichnungen häufig wichtig, wenn es um die Berechnung der Werte für MTTC, MTTD und MTTR geht, und sie ermöglichen eine ausführliche Nachbereitung.

-   **Betriebssicherheit:** Es ist von entscheidender Bedeutung, dass Red Team-Mitglieder stets die Betriebssicherheit im Blick behalten, was die TTPs, Tools, Sicherheitserkenntnisse, Berichte, Exploits, Backdoors und anderen Ressourcen betrifft. Falls ein Angreifer Zugriff auf die Ressourcen des Red Team erlangen sollte, kann er Angriffe gegen bekannte Schwachstellen schnell nachbilden und starten – mit gravierenden Auswirkungen.

##Zusätzliche Informationen

Für Penetrationstests auf Unternehmensebene ist die Einbeziehung mehrerer Parteien innerhalb eines Unternehmens erforderlich. Da es in diesem Dokument auch um eine Beschreibung der Vorgehensweise geht, ist es für Personen bestimmt, die für die Auswertung von Umgebungen, Systemen und Prozessen für Sicherheitsrisiken verantwortlich sind. Beachten Sie hierbei Folgendes: Die Details in diesem Dokument stellen eine Übersicht darüber dar, wie Sicherheitsteams für Microsoft Azure und Office 365 das Red Teaming und Penetrationstests für Live-Websites durchführen. Es handelt sich nicht eine Anleitung dafür, wie Kunden die Cloudinfrastruktur, -plattformen oder -dienste von Microsoft angreifen sollen.

Microsoft führt regelmäßig Penetrationstests durch, um die Sicherheitskontrollen und -prozesse für die Cloud zu verbessern. Uns ist aber auch bewusst, dass Sicherheitsbewertungen eine wichtige Rolle bei den Aktivitäten zur Anwendungsentwicklung und -bereitstellung von Kunden spielen. Daher haben wir eine Richtlinie aufgestellt, damit Kunden autorisierte Penetrationstests für ihre Anwendungen durchführen können, z. B. in Microsoft Azure gehostete Anwendungen.

Da derartige Tests nicht von echten Angriffen unterschieden werden können, ist es wichtig, dass Kunden Penetrationstests nur durchführen, wenn Sie im Vorhinein eine Genehmigung vom Microsoft Azure-Kundendienst erhalten. Penetrationstests von Kunden müssen unter Einhaltung der Nutzungsbedingungen von Microsoft durchgeführt werden. Anfragen zu Penetrationstests müssen mindestens 7 Tage im Voraus eingereicht werden. Weitere Informationen finden Sie im [Microsoft Trust Center]https://www.microsoft.com/TrustCenter/CloudServices/Azure).

## Referenzen

[1] Just-in-Time-Zugriff (JIT) und -Rechteerhöhung: Die Erhöhung der Rechte wird nur gewährt, wenn diese benötigt werden, sowie nur für den jeweils erforderlichen Vorgang. Häufig wird dies so implementiert, dass eine oder mehrere Genehmigungen eingeholt werden müssen, bevor der Zugriff gewährt wird.

[2] Just Enough Administration (JEA): Diese Lösung wurde als Unterstützung beim Schützen von Systemen konzipiert. Bestimmte Benutzer können administrative Aufgaben durchführen, ohne dass sie Administratorrechte erhalten, und alle Aktionen dieser Benutzer werden per Audit überprüft. Weitere Informationen finden Sie im [Artikel zu „Just Enough Administration“ im TechNet-Katalog](https://gallery.technet.microsoft.com/Just-Enough-Administration-6b5ad370).

[3] Im Kontext einer Sicherheitsverletzung bezieht sich MTTD auf den Zeitraum, der für die Erkennung der Sicherheitsverletzung benötigt wird.

[4] MTTR bezieht sich darauf, wie lange die Wiederherstellung nach der Erkennung einer Sicherheitsverletzung dauert.

[5] Microsoft Azure- und Office 365-Personal, separate Red Teams und Blue Teams mit Vollzeitmitarbeitern.

[6] Weitere Informationen finden Sie unter [„Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains“](http://www.lockheedmartin.com/content/dam/lockheed/data/corporate/documents/LM-White-Paper-Intel-Driven-Defense.pdf) (Intelligence-gesteuerte Computernetzwerk-Verteidigung per Analyse von Angriffsaktionen und Vorgehensweisen bei Eindringversuchen) von Lockheed Martin.

[7] Gefechtsnebel (Englisch: Fog of war): Dient zum Beschreiben der Unsicherheit beim Situationsbewusstsein, die bei Teilnehmern in Bezug auf die eigenen Fähigkeiten sowie die Fähigkeiten und Absichten der Angreifer besteht.

[8] EICAR-Testdatei: Wird auch als „EICAR Standard Anti-Virus Test File“ bezeichnet. Es handelt sich um eine Computerdatei, die vom European Institute for Computer Antivirus Research (EICAR) und der Computer Antivirus Research Organization (CARO) entwickelt wurde, um die Reaktionsfähigkeit von Antischadsoftware zu testen. Anstatt echte Schadsoftware zu verwenden, die wirklichen Schaden anrichten könnte, kann Antischadsoftware mit dieser Testdatei ohne echten Computervirus getestet werden.


*(c) 2014 Microsoft Corporation. Alle Rechte vorbehalten. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen, die in diesem Dokument enthalten sind, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Das Nutzungsrisiko liegt bei Ihnen. Einige Beispiele dienen nur zu Illustrationszwecken und sind fiktiver Art. Ein realer Bezug ist weder beabsichtigt noch erwünscht. Dieses Dokument gibt keine Rechte an geistigem Eigentum an irgendeinem Microsoft-Produkt. Sie können dieses Dokument für interne Zwecke verwenden.*

<!---HONumber=AcomDC_0218_2016-->