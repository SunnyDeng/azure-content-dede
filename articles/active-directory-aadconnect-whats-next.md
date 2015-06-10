<properties 
	pageTitle="Verwalten von Azure AD Connect" 
	description="Erfahren Sie, wie die Standardkonfiguration und Betriebsaufgaben für Azure AD Connect erweitert werden." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Verwalten von Azure AD Connect 


<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/active-directory-aadconnect/" title="Worum handelt es sich?" class="current">Worum handelt es sich?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-how-it-works/" title="Funktionsweise">Funktionsweise</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-get-started/" title="Erste Schritte">Erste Schritte</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-whats-next/" title="Wie geht es weiter?">Wie geht es weiter?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-learn-more/" title="Weitere Informationen">Weitere Informationen</a>
</div>

Es handelt sich um fortgeschrittene operative Themen, die es Ihnen ermöglichen, Azure Active Directory Connect so anzupassen, dass die Bedürfnisse und Anforderungen Ihrer Organisation erfüllt werden.

## Ändern der Standardkonfiguration
Die Standardkonfiguration von Azure AD Connect ist in den meisten Fällen ausreichend, um Ihre lokalen Verzeichnisse auf die Cloud zu erweitern. Es gibt jedoch bestimmte Fälle, in denen Sie die Standardeinstellungen ändern und der Geschäftslogik Ihrer Organisation anpassen müssen. In diesen Fällen können Sie die Standardkonfiguration ändern, wobei einige Dinge beachtet werden müssen, bevor Sie dies tun.

Wenn Sie von Azure AD Sync oder DirSync aktualisieren oder verschieben, beachten Sie die folgenden Punkte:

- Nachdem Sie Azure AD Sync auf eine neuere Version aktualisiert haben, werden die meisten Einstellungen auf Standardwerte zurückgesetzt.
- Änderungen an integrierten Synchronisierungsregeln gehen verloren, nachdem ein Upgrade angewendet wurde.
- Integrierte Synchronisierungsregeln, die gelöscht wurden, werden bei einem Upgrade auf eine neuere Version neu erstellt.
- Von Ihnen erstellte benutzerdefinierte Synchronisierungsregeln bleiben unverändert, wenn ein Upgrade auf eine neuere Version angewendet wurde.

Wenn Sie die Standardkonfiguration ändern müssen, führen Sie folgende Schritte aus:

- Wenn Sie einen Attributfluss von einer integrierten Synchronisierungsregel anpassen müssen, verändern Sie diese nicht. Erstellen Sie stattdessen eine neue Synchronisierungsregel mit höherer Priorität (niedrigerem numerischem Werte), die den erforderlichen Attributfluss enthält.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.
- Wenn Sie den Umfang oder die Join-Einstellung einer integrierten Synchronisierungsregel ändern müssen, so dokumentieren Sie dies und wenden Sie die Änderung nach dem Upgrade auf eine neuere Version von Azure AD Connect erneut an.






 

## Verwenden des Synchronisierungsregel-Editors

In Azure AD Connect können Sie den Objekt- und Attributfluss zwischen Azure AD und Ihren lokalen Verzeichnissen konfigurieren und optimieren, indem Sie Synchronisierungsregeln konfigurieren. Synchronisierungsregeln können mithilfe des Synchronisierungsregel-Editors konfiguriert werden. Der Synchronisierungsregel-Editor wird bei der Installation von Azure AD Connect mit installiert. Um den Editor verwenden zu können, müssen Sie Mitglied der ADSyncAdmins-Gruppe oder der Administratorgruppe sein, die Sie bei der Installation von Azure AD Connect angegeben haben.

Im Screenshot unten sehen Sie alle für Ihre Konfiguration erstellten Synchronisierungsregeln, wenn Sie Azure AD Connect mit der Expressinstallation einrichten. Jede Zeile in der Tabelle stellt eine Synchronisierungsregel dar. Auf der linken Seite werden unter Regeltypen die beiden verschiedenen Arten aufgeführt: eingehend und ausgehend. Eingehend und ausgehend bedeutet aus Sicht vom Metaverse. Das heißt, Informationen aus den Verzeichnissen werden in den Metaverse übertragen. Ausgehend bezieht sich auf Regeln, mit denen Informationen und Attribute an Verzeichnisse wie unser lokales Active Directory oder Azure AD gesendet werden.

<center>![Synch Rules Editor](./media/active-directory-aadconnect-whats-next/Synch_Rule.png) </center>

Um eine neue Regel zu erstellen, wählen Sie „Neue Regel hinzufügen“ und konfigurieren Sie die Regel. Nehmen wir beispielsweise an, dass wir eine Join-Regel erstellen möchten, bei der jeder Benutzer in unserem lokalen Verzeichnis mit dem Metaverse-Objekt verknüpft wird, das über dieselbe Telefonnummer verfügt. Hierzu erstellen Sie die neue Regel und geben das verbundene System an, in unserem Fall "contoso.com", den Objekttyp des verbundenen Systems, die Benutzer, den Metaverse-Objekttyp, die Person und den Join-Verknüpfungstyp.

<center>![Create Sync rule](./media/active-directory-aadconnect-whats-next/synch2.png) </center>


Geben Sie dann auf dem Bildschirm „Join-Regeln“ unter Quellattribut und unter Zielattribut „TelephoneNumber“ an. Und das ist schon alles. Sie haben nun erfolgreich eine Join-Regel erstellt.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next/synch3.png) </center>

Sie können den Synchronisierungsregel-Editor dazu verwenden, weitere Geschäftslogiken außerhalb der Standardkonfiguration anzuwenden und sie an die Bedürfnisse Ihrer Organisation anzupassen. Weitere Informationen zum Synchronisierungsregel-Editor finden Sie unter [Grundlagen der Standardkonfiguration](https://msdn.microsoft.com/library/azure/dn800963.aspx).


## Verwenden einer deklarativen Bereitstellung 
Die deklarative Bereitstellung funktioniert „ohne Code“ und kann mit dem Synchronisierungsregel-Editor eingerichtet und konfiguriert werden. Der Editor kann für die Einrichtung und Erstellung eigener Bereitstellungsregeln verwendet werden.

Ein wesentlicher Bestandteil der deklarativen Bereitstellung ist die in den Attributflüssen verwendete Ausdruckssprache. Die verwendete Sprache ist eine Teilmenge von Microsoft ® Visual Basic ® for Applications (VBA). Diese Sprache wird in Microsoft Office verwendet, und Benutzer mit Erfahrungen mit VBScript werden sie wiedererkennen. Die Ausdruckssprache  für die deklarative Bereitstellung verwendet nur Funktionen und ist keine strukturierte Sprache; es gibt keine Methoden oder Anweisungen. Funktionen werden stattdessen verschachtelt, um den Programmablauf auszudrücken.

Weitere Informationen zur Ausdruckssprache finden Sie unter [Grundlagen zu den Ausdrücken für eine deklarative Bereitstellung](https://msdn.microsoft.com/library/azure/dn801048.aspx)

## Zusätzliche Dokumentation
Ein Teil der Dokumentation, die für Azure AD Sync erstellte wurde, ist noch immer relevant und gilt für Azure AD Connect. Obwohl jede Anstrengung unternommen wird, diese Dokumentation an Azure.com zu übermitteln, befindet sich ein Teil noch immer in der MSDN-Bibliothek. Zusätzliche Dokumentation finden Sie unter [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) und [Azure AD Sync auf MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).

**Zusätzliche Ressourcen**

* [Verwenden Sie Ihre lokale Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md)
* [Funktionsweise von Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Erste Schritte mit Azure AD Connect](active-directory-aadconnect-get-started.md)
* [Wie geht es weiter mit Azure AD Connect?](active-directory-aadconnect-whats-next.md)
* [Weitere Informationen](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->