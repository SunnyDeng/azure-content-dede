# Azure-Identität

Die Identitätsverwaltung ist in der öffentlichen Cloud genauso wichtig wie in lokalen Systemen. Azure unterstützt eine Reihe unterschiedlicher Identitätstechnologien für die Cloud, um diese Aufgabe zu erleichtern. Dazu gehören:

- Sie können Windows Server Active Directory (meist nur "AD" genannt) mithilfe von virtuellen Computern in der Cloud ausführen, die mit Azure Virtual Machines erstellt wurden. Dieser Ansatz ist sinnvoll, wenn Sie ein lokales Datencenter mit Azure in die Cloud erweitern.

- Mit Azure Active Directory können Sie Ihren Benutzern die Möglichkeit einer einmaligen Anmeldung für SaaS-Anwendungen (Software as a Service) eröffnen. So setzt beispielsweise Office 365 von Microsoft diese Technologie ein, und auch Anwendungen, die auf Azure oder anderen Cloud-Plattformen ausgeführt werden, können sie nutzen.

- Mithilfe von Azure Active Directory Access Control können in der Cloud oder lokal ausgeführte Anwendungen ihren Benutzern die Anmeldung mit Identitäten aus Facebook, Google, Microsoft und von anderen Identitätsanbietern ermöglichen.


Dieser Artikel enthält eine Beschreibung aller drei Optionen.

## Inhaltsverzeichnis

- [Ausführen von Windows Server Active Directory auf VMs](#adinvm)

- [Verwenden von Azure Active Directory](#ad)

- [Verwenden von Azure Active Directory Access Control](#ac)


## <a name="adinvm"></a>Ausführen von Windows Server Active Directory auf VMs

Die Ausführung von Windows Server AD auf Azure-VMs gleicht einer lokalen Ausführung. [Abbildung 1](#fig1) zeigt ein typisches Beispiel für diesen Fall.

![Azure Active Directory in Virtual Machine](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Abbildung 1: Windows Server Active Directory kann auf Azure-VMs ausgeführt werden, die über ein virtuelles Azure-Netzwerk mit dem lokalen Datencenter eines Unternehmens verbunden sind.

Im hier gezeigten Beispiel wird Windows Server AD auf VMs ausgeführt, die mittels Azure Virtual Machines, der IaaS-Technologie der Plattform, erstellt wurden. Diese VMs und einige wenige andere werden zu einem virtuellen Netzwerk (VNET) gruppiert, das über ein virtuelles Azure-Netzwerk mit dem lokalen Datencenter eines Unternehmens verbunden ist. Das VNET legt eine Gruppe von Cloud-VMs fest, die über eine Verbindung mit einem virtuellen privaten Netzwerk (VPN) mit dem lokalen Netzwerk interagieren. Auf diese Weise gleichen die Azure-VMs jedem anderen Subnetz des lokalen Datencenters. Wie die Abbildung zeigt, führen zwei der VMs Windows Server AD-Domänencontroller aus. Die anderen VMs im VNET können Anwendungen wie beispielsweise SharePoint ausführen oder eine andere Verwendung haben und etwa zu Entwicklungs- oder Testzwecken genutzt werden. Das lokale Datencenter führt ebenfalls zwei Windows Server AD-Domänencontroller aus.

Es gibt mehrere Optionen für die Verbindung der in der Cloud und der lokal ausgeführten Domänencontroller. Dazu gehören:

- Zuordnung aller Controller zu einer einzigen Active Directory-Domäne.

- Erstellen separater AD-Domänen lokal und in der Cloud, die Teil derselben Gesamtstruktur sind.

- Erstellen separater AD-Gesamtstrukturen lokal und in der Cloud und anschließende Verbindung dieser Gesamtstrukturen mittels gesamtstrukturübergreifender Vertrauensstellungen oder Windows Server Active Directory-Verbunddiensten (AD FS), die auch auf VMs in Azure ausgeführt werden können.

Unabhängig von der gewählten Option sollte ein Administrator sicherstellen, dass Authentifizierungsanfragen lokaler Benutzer nur im Bedarfsfall an cloudbasierte Domänencontroller geleitet werden, da die Verbindung in die Cloud meist langsamer ist als in lokalen Netzwerken. Ein weiterer, bei der Verbindung von cloudbasierten und lokalen Domänencontrollern zu berücksichtigender Faktor ist der durch Replikation generierte Datenverkehr. Domänencontroller in der Cloud befinden sich in der Regel an ihrem eigenen AD-Standort, wodurch Administratoren die Häufigkeit der Replikation planen können. Azure stellt Datenverkehr, der aus einem Azure-Datencenter gesendet wird, in Rechnung, nicht jedoch empfangene Bytes, was Einfluss auf die vom Administrator gewählte Replikationsweise haben könnte. Ebenso sollte darauf hingewiesen werden, dass Azure zwar eigene DNS-Unterstützung (Domain Name System) bietet, dieser Dienst aber nicht über alle für Active Directory erforderlichen Funktionen verfügt (wie etwa eine Unterstützung von Dynamic DNS und SRV-Einträge). Daher müssen Sie für die Ausführung von Windows Server AD auf Azure ihre eigenen DNS-Server in der Cloud einrichten.

Die Ausführung von Windows Server AD auf Azure-VMs kann in verschiedenen Szenarien sinnvoll sein. Hier einige Beispiele:

- Wenn Sie Azure Virtual Machines zur Erweiterung Ihres eigenen Datencenters nutzen, führen Sie möglicherweise Anwendungen in der Cloud aus, die lokale Domänencontroller zur Bearbeitung von Aufgaben wie integrierten Windows-Authentifizierungsanforderungen oder LDAP-Abfragen benötigen. SharePoint interagiert zum Beispiel häufig mit Active Directory; auch wenn es möglich ist, eine SharePoint-Farm auf Azure mit einem lokalen Verzeichnis auszuführen, steigern cloudbasierte Domänencontroller doch deutlich die Leistung. (Es sollte jedoch festgehalten werden, dass dies nicht unbedingt erforderlich ist: Es gibt viele Anwendungen, die unter ausschließlicher Verwendung von lokalen Domänencontrollern erfolgreich in der Cloud ausgeführt werden können.)

- Stellen Sie sich eine weit entfernte Niederlassung vor, die nicht über genügend Ressourcen verfügt, um eigene Domänencontroller auszuführen. Aktuell müssen ihre Benutzer sich bei Domänencontrollern am anderen Ende der Welt authentifizieren - die Anmeldung dauert entsprechend lange. Wenn Active Directory auf Azure in einem näher gelegenen Microsoft-Rechenzentrum ausgeführt wird, kann dies den Vorgang beschleunigen, ohne dass zusätzliche Server in der Niederlassung erforderlich wären.

- Ein Unternehmen, das Azure zur Notfallwiederherstellung nutzt, könnte einige wenige aktive VMs in der Cloud pflegen, darunter einen Domänencontroller. Dieser kann dann so eingerichtet werden, dass er den Standort nach Bedarf erweitert, wenn Ausfälle an anderen Orten aufzufangen sind.

Es gibt weitere Möglichkeiten. So ist es beispielsweise nicht erforderlich, Windows Server AD in der Cloud mit einem lokalen Datencenter zu verbinden. Wenn Sie eine SharePoint-Farm für einen bestimmten Kreis von Benutzern ausführen möchten, die sich zum Beispiel alle nur mit cloudbasierten Identitäten anmelden, könnten Sie eine eigenständige Gesamtstruktur auf Azure erstellen. Wie Sie diese Technologie verwenden, hängt ganz von Ihrer Zielsetzung ab. (Eine ausführlichere Anleitung zur Verwendung von Windows Server AD mit Azure finden Sie [hier](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Verwenden von Azure Active Directory

Mit zunehmender Verbreitung von SaaS-Anwendungen stellt sich eine offenkundige Frage: Welche Art Verzeichnisdienst sollten diese cloudbasierten Anwendungen nutzen? Die Antwort von Microsoft auf diese Frage ist Azure Active Directory.

Für die Verwendung des Verzeichnisdienstes in der Cloud gibt es zwei Hauptoptionen:

- Einzelpersonen und Organisationen, die nur mit SaaS-Anwendungen arbeiten, können Azure Active Directory als ihren einzigen Verzeichnisdienst nutzen.

- Organisationen, die Windows Server Active Directory ausführen, können ihr lokales Verzeichnis mit Azure Active Directory verbinden und dann für ihre Benutzer die Möglichkeit einer einmaligen Anmeldung für SaaS-Anwendungen konfigurieren.


[Abbildung 2](#fig2) zeigt die erste dieser beiden Optionen, bei der nur Azure Active Directory erforderlich ist.

![Azure Active Directory in Virtual Machine](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Abbildung 2: Azure Active Directory ermöglicht Benutzern einer Organisation die einmalige Anmeldung für SaaS-Anwendungen einschließlich Office 365.

Wie die Abbildung zeigt, ist Azure AD ein mehrinstanzenfähiger Dienst. Dies bedeutet, dass es zahlreiche unterschiedliche Organisationen gleichzeitig unterstützen und dabei Verzeichnisinformationen über die Benutzer jeder einzelnen speichern kann. In diesem Beispiel versucht ein Benutzer von Organisation A, auf eine SaaS-Anwendung zuzugreifen. Dabei kann es sich um eine Komponente von Office 365 handeln, zum Beispiel SharePoint Online, oder um etwas anderes - auch Anwendungen, die nicht von Microsoft stammen, können diese Technologie nutzen. Da Azure AD das SAML 2.0-Protokoll unterstützt, ist die einzige Voraussetzung für eine Anwendung, auf Grundlage dieses Industriestandards interagieren zu können. (Tatsächlich können Anwendungen, die mit Azure AD arbeiten, in jedem beliebigen Rechenzentrum ausgeführt werden, nicht nur in Azure-Rechenzentren.)

Der Vorgang beginnt, wenn der Benutzer auf eine SaaS-Anwendung zugreift (Schritt 1). Hierfür muss der Benutzer ein von Azure AD ausgegebenes Token vorweisen.

Dieses Token enthält Informationen zur Identifizierung des Benutzers und ist von Azure AD digital signiert. Der Benutzer erhält dieses Token, wenn er sich mit Benutzername und Kennwort bei Azure AD anmeldet (Schritt 2). Azure AD gibt dann das erforderliche Token aus (Schritt 3).

Das Token wird dann an die SaaS-Anwendung gesendet (Schritt 4), die die Signatur des Tokens prüft und seinen Inhalt verwendet (Schritt 5). In der Regel nutzt die Anwendung die im Token enthaltenen Informationen, um zu entscheiden, auf welche Informationen der Benutzer zugreifen darf; auch andere Verwendungen sind denkbar.

Wenn die Anwendung mehr Informationen über den Benutzer benötigt, als im Token enthalten sind, kann sie diese mithilfe der Azure AD Graph-API direkt von Azure AD anfordern (Schritt 6). In der ersten Version von Azure AD ist das Verzeichnis-Schema relativ einfach: Es enthält nur Benutzer und Gruppen und die Beziehungen unter ihnen. Anwendungen können mit diesen Informationen Erkenntnisse über die Verbindungen zwischen den Benutzern gewinnen. So könnte es für eine Anwendung erforderlich sein zu wissen, wer Vorgesetzter eines Benutzers ist, um über den Zugriff auf bestimmte Daten zu entscheiden. Diese Informationen kann sie über eine Azure AD-Abfrage durch die Graph-API erhalten.

Die Graph-API nutzt ein gewöhnliches RESTful-Protokoll, wodurch sie von den meisten Clients einschließlich Mobilgeräten problemlos genutzt werden kann. Die API unterstützt auch die von OData definierten Erweiterungen, die Aspekte wie etwa eine Abfragesprache hinzufügen, mit der Clients auf nützlichere Weise auf Daten zugreifen können. (Weitere Informationen zu OData finden Sie unter [Einführung in OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf).) Da die Graph-API Informationen über Beziehungen zwischen Benutzern liefern kann, können Anwendungen mit ihrer Hilfe das in das Azure AD-Schema eingebettete soziale Diagramm einer bestimmten Organisation verstehen (weshalb sie auch Graph-API - "Diagramm-API" genannt wird). Für ihre eigene Authentifizierung gegenüber Azure AD für Graph-API-Anforderungen verwendet eine Anwendung OAuth 2.0.

Falls eine Organisation Windows Server Active Directory nicht verwendet - sie nicht über lokale Server oder Domänen verfügt - und nur mit Cloud-Anwendungen arbeitet, die Azure AD nutzen, können die Benutzer des Unternehmens mithilfe dieses Cloud-Directory mit einer Anmeldung auf alle Anwendungen zugreifen. Aber auch wenn dieses Szenario täglich häufiger wird, arbeiten die meisten Organisationen nach wie vor mit lokalen, mit Windows Server Active Directory erstellten Domänen. Azure AD kann auch hier eine nützliche Rolle spielen, wie [Abbildung 3](#fig3) zeigt.

![Azure Active Directory in Virtual Machine](./media/identity/identity_03_AD.png)
<a id="fig3"></a>Abbildung 3: Eine Organisation kann einen Verbund aus Windows Server Active Directory und Azure Active Directory herstellen, damit ihre Benutzer per einmaliger Anmeldung auf SaaS-Anwendungen zugreifen können.

In diesem Szenario möchte ein Benutzer von Organisation B auf eine SaaS-Anwendung zugreifen. Dies ist erst möglich, wenn die Directory-Administratoren der Organisation eine Verbundbeziehung mit Azure AD unter Verwendung von AD FS hergestellt haben, wie die Abbildung zeigt. Daneben müssen die Administratoren auch die Datensynchronisierung zwischen lokalem Windows Server AD und Azure AD konfigurieren. Dadurch werden Benutzer- und Gruppeninformationen automatisch vom lokalen Verzeichnis nach Azure AD kopiert. Dies eröffnet die folgenden Möglichkeiten: Im Endeffekt erweitert die Organisation ihr lokales Verzeichnis in die Cloud. Durch eine derartige Kombination von Windows Server AD und Azure AD erhält die Organisation einen Verzeichnisdienst, der als eine Entität verwaltet werden kann und dennoch sowohl lokal als auch in der Cloud präsent ist.

Zur Verwendung von Azure AD melden sich die Benutzer zunächst wie gewohnt bei ihrer lokalen Active Directory-Domäne an (Schritt 1). Wenn sie versuchen, auf eine SaaS-Anwendung zuzugreifen (Schritt 2), gibt Azure AD aufgrund des Verbundverfahrens ein Token für diese Anwendung aus (Schritt 3). (Weitere Informationen zur Funktionsweise eines Verbunds finden Sie unter [Claims-Based Identity for Windows: Technologien und Szenarien](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Wie auch zuvor enthält dieses Token Informationen zur Identifizierung des Benutzers und ist von Azure AD digital signiert. Das Token wird dann an die SaaS-Anwendung gesendet (Schritt 4), die die Signatur des Tokens prüft und seinen Inhalt verwendet (Schritt 5). Und wie im vorigen Szenario kann die SaaS-Anwendung mithilfe der Graph-API bei Bedarf mehr über den Benutzer erfahren (Schritt 6).

Aktuell ist Azure AD kein vollständiger Ersatz für ein lokales Windows Server AD. Wie bereits erwähnt, ist das Schema des cloudbasierten Verzeichnisses viel einfacher; dazu fehlen Aspekte wie Gruppenrichtlinien, die Fähigkeit, Informationen über Computer zu speichern, und Unterstützung für LDAP. (Tatsächlich kann ein Windows-Computer nicht für eine Benutzeranmeldung unter ausschließlicher Verwendung von Azure AD konfiguriert werden - dieses Szenario wird nicht unterstützt.) Stattdessen bestehen die ursprünglichen Ziele von Azure AD darin, Unternehmensbenutzern den Zugriff auf Anwendungen in der Cloud ohne jeweils separate Anmeldung zu ermöglichen und lokalen Verzeichnisadministratoren das manuelle Synchronisieren des lokalen Verzeichnisses mit jeder von der Organisation genutzten SaaS-Anwendung abzunehmen. Es ist jedoch davon auszugehen, dass dieser Verzeichnisdienst für die Cloud im Laufe der Zeit breitere Anwendungsmöglichkeiten erhalten wird.

## <a name="ac"></a>Verwenden von Azure Active Directory-Zugriffssteuerung

Cloudbasierte Identitätstechnologien können eine ganze Reihe von Problemen lösen. Azure Active Directory kann beispielsweise Benutzern einer Organisation die einmalige Anmeldung für SaaS-Anwendungen ermöglichen. Aber Identitätstechnologien in der Cloud können noch auf vielfältige andere Weise eingesetzt werden.

Stellen Sie sich etwa vor, dass die Benutzeranmeldung für eine Anwendung mithilfe von Token geschehen soll, die von mehreren Identitätsanbietern (IdPs) *identity providers (IdPs)* ausgegeben werden. Aktuell gibt es die verschiedensten Identitätsanbieter, darunter Facebook, Google, Microsoft und andere; Anwendungen gestatten häufig die Benutzeranmeldung mit einer dieser Identitäten. Weshalb sollte eine Anwendung sich die Mühe machen, ihre eigene Benutzer- und Kennwortliste zu pflegen, wenn sie stattdessen auf bereits existierende Identitäten zurückgreifen kann? Das Akzeptieren vorhandener Identitäten erleichtert den Benutzern das Leben, die sich eine Kombination aus Benutzername und Kennwort weniger merken müssen, und genauso den Anwendungsentwicklern, die keine eigenen Listen von Benutzernamen und Kennwörtern mehr pflegen müssen.

Aber auch wenn jeder Identitätsanbieter ein Token irgendeiner Art ausgibt, gibt es keinen verbindlichen Standard - jeder IdP nutzt ein eigenes Format. Zudem sind auch die Informationen in diesen Token nicht durch einen Standard geregelt. Wenn eine Anwendung von beispielsweise Facebook, Google und Microsoft ausgegebene Token akzeptieren soll, muss für jedes dieser drei Formate ein eigener Code geschrieben werden.

Weshalb aber so vorgehen? Weshalb nicht stattdessen eine Vermittlungsinstanz erstellen, die ein einziges Tokenformat mit einer vereinheitlichten Darstellung von Identitätsinformationen erzeugen kann? Dieser Ansatz würde für Anwendungsentwickler die Dinge sehr vereinfachen, da sie auf diese Weise nur eine Art Token berücksichtigen müssten. Dies ist genau die Rolle der Zugriffssteuerung mit Azure Active Directory Access Control: als Vermittler in der Cloud zu agieren, um unterschiedlichste Token nutzen zu können. [Abbildung 4](#fig4) zeigt, wie dies funktioniert.

![Azure Active Directory in Virtual Machine](./media/identity/identity_04_IdentityProviders.png) 
<a id="fig4"></a>Abbildung 4: Azure Active Directory Access Control erleichtert Anwendungen die Annahme von Token, die von unterschiedlichen Identitätsanbietern ausgegeben wurden.

Der Vorgang beginnt, wenn ein Benutzer versucht, von einem Browser aus auf die Anwendung zuzugreifen. Die Anwendung leitet ihn an einen IdP seiner Wahl (dem auch die Anwendung vertraut) weiter. Er authentifiziert sich bei diesem IdP zum Beispiel durch Eingabe von Benutzername und Kennwort (Schritt 1), der IdP gibt dann ein Token mit den Benutzerinformationen aus (Schritt 2).

Wie die Abbildung zeigt, unterstützt die Zugriffssteuerung zahlreiche unterschiedliche, cloudbasierte IdPs, darunter bei Google, Yahoo, Facebook, Microsoft (vormals Windows Live ID) erstellte Konten und alle OpenID-Anbieter. Sie unterstützt auch Identitäten, die mit Azure Active Directory und, über den Verbund mit AD FS, mit Windows Server Active Directory erstellt wurden. Ziel ist es, die aktuell am häufigsten genutzten Identitäten abzudecken, ob sie nun von cloudbasierten oder lokalen IdPs ausgegeben werden.

Sobald der Browser des Benutzers über ein Token des gewählten IdPs verfügt, wird dieses an die Zugriffssteuerung gesendet (Schritt 3). Diese validiert das Token und vergewissert sich so, dass es tatsächlich vom angegebenen IdP stammt, und erstellt dann entsprechend der Regeln, die für die jeweilige Anwendung definiert wurden, ein neues Token. Wie Azure Active Directory ist auch die Zugriffssteuerung ein Dienst mit mehreren Mandanten, wobei die Mandanten hier Anwendungen und nicht Kundenorganisationen sind. Wie die Abbildung zeigt, kann jede Anwendung ihren eigenen Namespace erhalten und verschiedenste Regeln zu Authentifizierung und anderen Aspekten definieren.

Mit diesen Regeln können die Administratoren jeder Anwendung definieren, wie die Token unterschiedlicher IdPs in ein Access Control-Token transformiert werden sollen. Wenn beispielsweise unterschiedliche IdPs die Benutzernamen auf unterschiedliche Weise darstellen, können diese Namen mit den Regeln von Access Control in ein einheitliches Format konvertiert werden. Access Control sendet dieses neue Token dann zurück an den Browser (Schritt 4), der es bei der Anwendung einreicht (Schritt 5). Sobald die Anwendung das Access Control-Token erhält, überprüft sie, ob es wirklich von Access Control ausgegeben wurde, und nutzt dann die enthaltenen Informationen (Schritt 6).

Auch wenn dieses Verfahren etwas umständlich erscheinen mag, erleichtert es den Entwicklern einer Anwendung ihre Aufgabe beträchtlich. Die Anwendung kann von vielen Anbietern ausgegebene Identitäten akzeptieren, ohne unterschiedlichste Token mit unterschiedlichsten Informationen verarbeiten zu müssen, sondern erhält nur einen Tokentyp mit vertrauten Informationen. Zudem entfällt die Konfiguration von Vertrauensverhältnissen zwischen Anwendung und IdPs, da Vertrauensverhältnisse nun von Access Control verwaltet werden - und die Anwendung nur noch diesem Vermittler vertrauen muss.

Abschließend sei noch darauf hingewiesen, dass Access Control in keiner Weise an Windows gebunden ist und genauso von einer Linux-Anwendung genutzt werden könnte, die nur Google- und Facebook-Identitäten akzeptiert. Und auch wenn Access Control zur Azure Active Directory-Familie gehört, können Sie es sich aus der Beschreibung im vorangegangenen Abschnitt als voll abgegrenzten Dienst vorstellen. Obwohl beide Technologien mit Identität arbeiten, lösen sie recht unterschiedliche Probleme (wenngleich Microsoft anklingen lässt, die beiden vermutlich irgendwann integrieren zu wollen).

Das Arbeiten mit Identitäten ist für beinahe jede Anwendung wichtig. Das Ziel von Access Control besteht darin, Entwicklern das Erstellen von Anwendungen zu erleichtern, die Identitäten unterschiedlicher Anbieter akzeptieren. Microsoft hat diesen Dienst in die Cloud gestellt und so für alle Anwendungen auf allen Plattformen verfügbar gemacht.

##Zum Autor

David Chappell ist Principal der Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) in San Francisco, Kalifornien. Mit seinen Vorträgen, Veröffentlichungen und Beratungsleistungen hilft David Chappell Menschen weltweit, neue Technologien zu verstehen, sie anzuwenden und besser über sie zu entscheiden.
<!--HONumber=42-->
