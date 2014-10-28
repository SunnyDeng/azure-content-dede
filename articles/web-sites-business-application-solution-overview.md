<properties linkid="websites-business-application" urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Create a Line-of-Business Application on Azure Websites" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Erstellen einer Branchenanwendung auf Azure-Websites

Dieser Leitfaden bietet eine technische Übersicht über die Verwendung von Azure-Websites zur Erstellung von Branchenanwendungen. In diesem Dokument wird davon ausgegangen, dass es sich bei diesen Anwendungen um Intranetanwendungen handelt, die für interne Geschäftszwecke geschützt werden sollen. Es gibt zwei charakteristische Merkmale von Geschäftsanwendungen. Diese Anwendungen erfordern Authentifizierung, in der Regel mit einem Unternehmensverzeichnis. Und sie benötigen normalerweise Zugriff auf oder Integration in lokale Daten und Dienste. In diesem Leitfaden geht es primär um die Erstellung von Geschäftsanwendungen auf [Azure-Websites][Azure-Websites]. Es gibt jedoch Situationen, in denen [Azure-Clouddienste][Azure-Clouddienste] oder [virtuelle Azure-Computer][virtuelle Azure-Computer] die bessere Wahl für Ihre Anforderungen wären. Informieren Sie sich unbedingt über die Unterschiede dieser Optionen im Thema [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich][Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich].

In diesem Leitfaden werden die folgenden Themen behandelt:

-   [Die Vorteile][Die Vorteile]
-   [Die Wahl einer Authentifizierungsstrategie][Die Wahl einer Authentifizierungsstrategie]
-   [Erstellen einer Azure-Website zur Unterstützung von Authentifizierung][Erstellen einer Azure-Website zur Unterstützung von Authentifizierung]
-   [Verwenden von Service Bus zur Integration in lokale Ressourcen][Verwenden von Service Bus zur Integration in lokale Ressourcen]
-   [Überwachen der Anwendung][Überwachen der Anwendung]

<div class="dev-callout">
<strong>Hinweis</strong>
<p>In diesem Leitfaden werden einige der h&auml;ufigsten Themen und Aufgaben vorgestellt, die mit der &ouml;ffentlichen .COM-Websiteentwicklung abgestimmt werden. Allerdings gibt es weitere Funktionen von Azure-Websites, die Sie in Ihrer speziellen Implementierung verwenden k&ouml;nnen. Weitere Informationen zu diesen Funktionen finden Sie in den anderen Leitf&auml;den unter <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/">Globale Webpr&auml;senz</a> und <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview">Digitale Marketingkampagnen</a> (beides in englischer Sprache).</p>
</div>

## <a name="benefits"></a>Die Vorteile

Da Branchenanwendungen in der Regel auf Unternehmensanwender ausgerichtet sind, sollten Sie die Vorteile der Verwendung der Cloud anstelle von lokalen Unternehmensressourcen und lokaler Infrastruktur bedenken. Zunächst wären da die typischen Vorteile eines Wechsels zur Cloud, wie beispielsweise die flexible Skalierbarkeit mit dynamischen Workloads. Nehmen wir als Beispiel eine Anwendung für jährliche Mitarbeiterbeurteilungen. Die meiste Zeit des Jahres fällt für diese Art von Anwendung nur wenig Verkehr an. Aber während der Beurteilungsphase steigt der Verkehr in einem großen Unternehmen rasant an. Azure bietet Skalierungsoptionen, mit deren Hilfe das Unternehmen horizontal skalieren kann, um die Last während des verkehrsreichen Bewertungszeitraums zu verarbeiten. Gleichzeitig kann das Unternehmen Geld sparen, indem es die Kapazitäten dieser Anwendung für den Rest des Jahres wieder reduziert. Ein weiterer Vorteil der Cloud ist, dass sich Unternehmen verstärkt auf die Anwendungsentwicklung konzentrieren können, da sie sich weniger um die Anschaffung und das Management der Infrastruktur kümmern müssen.

Neben diesen gängigen Vorteilen profitieren auch Mitarbeiter und Partner vom Verschieben einer Geschäftsanwendung in die Cloud: Sie können von überall aus auf die Anwendung zugreifen. Benutzer müssen nicht mit dem Unternehmensnetzwerk verbunden sein, um die Anwendung zu nutzen, und die IT-Abteilung muss keine komplexen Reverseproxy-Lösungen einsetzen. Es gibt verschiedene Authentifizierungsoptionen, um den Zugriff auf Unternehmensanwendungen zu schützen. Diese Optionen werden in den folgenden Abschnitten dieses Leitfadens erläutert.

## <a name="authentication"></a>Die Wahl einer Authentifizierungsstrategie

Bei dem Szenario mit der Geschäftsanwendung ist Ihre Authentifizierungsstrategie eine der wichtigsten Entscheidungen. Verschiedene Optionen stehen zur Verfügung:

-   Verwenden Sie den [Azure Active Directory-Dienst][Azure Active Directory-Dienst]. Sie können diesen als eigenständiges Verzeichnis verwenden, oder Sie synchronisieren ihn mit einem lokalen Active Directory. Anwendungen interagieren dann mit dem Azure Active Directory, um Benutzer zu authentifizieren. Einen Überblick über diesen Ansatz finden Sie unter [Verwenden von Azure Active Directory][Verwenden von Azure Active Directory].
-   Verwenden Sie virtuelle Azure Computer und das virtuelle Netzwerk zur Installation des Active Directory. So haben Sie die Möglichkeit, eine lokale Active Directory-Installation in die Cloud zu erweitern. Sie können auch Active Directory-Verbunddienste (ADFS) verwenden, um einen Verbund von Identitätsanforderungen zu erstellen und an das lokale AD weiterzuleiten. Die Authentifizierung für die Azure-Anwendung wird dann über ADFS an das lokale Active Directory weitergeleitet. Weitere Informationen zu diesem Ansatz finden Sie unter [Ausführen von Windows Server Active Directory auf VMs][Ausführen von Windows Server Active Directory auf VMs] und [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure][Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure].
-   Verwenden Sie einen Zwischendienst wie den [Azure-Zugriffssteuerungsdienst][Azure-Zugriffssteuerungsdienst] (Access Control Service, ACS), um mehrere Identitätsdienste zur Authentifizierung von Benutzern zu einzusetzen. Daraus ergibt sich eine Abstraktion, um Benutzer entweder über Active Directory oder über einen anderen Identitätsanbieter zu authentifizieren. Weitere Informationen finden Sie unter [Verwenden von Azure Active Directory Access Control][Verwenden von Azure Active Directory Access Control].

Bei diesem Szenario mit einer Geschäftsanwendung bietet die erste Option mit der Verwendung von Azure Active Directory die schnellste Möglichkeit zur Implementierung einer Authentifizierungsstrategie für Ihre Anwendung. In den folgenden Teilen dieses Leitfadens geht es primär um Azure Active Directory. Je nach Ihren Geschäftsanforderungen eignet sich möglicherweise eine der beiden Lösungen besser für Sie. Wenn Sie beispielsweise keine Identitätsinformationen in der Cloud synchronisieren dürfen, ist die ADFS-Lösung für Sie möglicherweise die bessere Option. Dies gilt auch, wenn Sie andere Identitätsanbieter wie Facebook unterstützen müssen.

Bevor Sie ein neues Azure Active Directory einrichten, sollten Sie beachten, dass vorhandene Dienste wie Office 365 oder Windows Intune bereits Azure Active Directory verwenden. In diesem Fall sollten Sie Ihr vorhandenes Abonnement mit Ihrem Azure-Abonnement verknüpfen. Weitere Informationen erhalten Sie unter [Was ist ein Windows Azure AD-Mandant?][Was ist ein Windows Azure AD-Mandant?].

Falls Sie aktuell nicht über einen Dienst verfügen, der Azure Active Directory bereits verwendet, können Sie ein neues Verzeichnis im Verwaltungsportal erstellen. Verwenden Sie den Bereich **Active Directory** im Verwaltungsportal, um ein neues Verzeichnis zu erstellen.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

Sobald das Verzeichnis erstellt ist, können Sie Benutzer, integrierte Anwendungen und Domänen erstellen und verwalten.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Die vollständige Vorgehensweise finden Sie unter [Adding Sign-On to Your Web Application Using Azure AD][Adding Sign-On to Your Web Application Using Azure AD] (Anmeldung mithilfe von Azure AD zu Ihrer Webanwendung hinzufügen, in englischer Sprache). Wenn Sie dieses neue Verzeichnis als eigenständige Ressource verwenden, müssen Sie im nächsten Schritt Anwendungen entwickeln, die in das Verzeichnis integriert sind. Wenn Sie jedoch über lokale Active Directory-Identitäten verfügen, möchten Sie diese in der Regel mit dem neuen Azure Active Directory synchronisieren. Weitere Informationen zur Vorgehensweise finden Sie unter [Verzeichnisintegration][Verzeichnisintegration].

Sobald das Verzeichnis erstellt und gefüllt ist, müssen Sie Webanwendungen erstellen, die eine Authentifizierung erfordern, und diese in das Verzeichnis integrieren. Diese Schritte werden in den folgenden beiden Abschnitten behandelt.

## <a name="createintranetsite"></a>Erstellen einer Azure-Website zur Unterstützung von Authentifizierung

Im Szenario zur globalen Webpräsenz haben wir verschiedene Optionen zum Erstellen und Bereitstellen einer neuen Website erläutert. Falls Sie neu bei Azure-Websites sind, empfehlen wir Ihnen, sich [diese Informationen anzusehen][diese Informationen anzusehen]. Eine ASP.NET-Anwendung in Visual Studio ist eine gängige Intranet-Webanwendung, die Windows-Authentifizierung verwendet. Einer der Gründe hierfür sind die enge Integration und die Unterstützung dieses Szenarios durch ASP.NET und Visual Studio.

Wenn Sie beispielsweise ein ASP.NET MVC 4-Projekt in Visual Studio erstellen, können Sie im Dialog für die Projekterstellung eine **Intranetanwendung** erstellen.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

Dadurch werden Änderungen an den Projekteinstellungen vorgenommen, um Windows Authentifizierung zu unterstützen. Insbesondere wird in der Datei "web.config" für das Attribut **Modus** des Elements **Authentifizierung** **Windows** festgelegt. Wenn Sie ein anderes ASP.NET-Projekt erstellen, beispielsweise ein Web Forms-Projekt, oder wenn Sie an einem bestehenden Projekt arbeiten, müssen Sie diese Änderung manuell vornehmen.

Bei einem MVC-Projekt müssen Sie außerdem zwei Werte im Fenster mit den Projekteigenschaften ändern. Legen Sie für **Windows-Authentifizierung** **Aktiviert** fest und für **Anonyme Authentifizierung** **Deaktiviert**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Registrieren Sie diese Anwendung im Verzeichnis, und ändern Sie diese Anwendungskonfiguration für Verbindungen, um die Authentifizierung mit Azure Active Directory durchzuführen. Hierzu gibt es zwei Möglichkeiten in Visual Studio:

-   [Identitäts- und Zugriffstool für Visual Studio][Identitäts- und Zugriffstool für Visual Studio]
-   [Microsoft ASP.NET-Tools für Azure Active Directory][Microsoft ASP.NET-Tools für Azure Active Directory]

### <a name="identityandaccessforvs"></a>Identitäts- und Zugriffstool für Visual Studio:

Eine Möglichkeit ist die Verwendung des [Identity and Access Tool][Identity and Access Tool], das Sie herunterladen und installieren können. Dieses Tool integriert Visual Studio in das Kontextmenü des Projekts. Die folgenden Anweisungen und Screenshots basieren auf Visual Studio 2012. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Identity and Access**. Drei Einstellungen müssen konfiguriert werden. Geben Sie auf der Registerkarte **Eigenschaften** den **Pfad zum STS-Metadaten-Dokument** und den **URI der APP-ID** an. (Im Abschnitt [Registrieren von Anwendungen im Azure Active Directory][Registrieren von Anwendungen im Azure Active Directory] erfahren Sie, wie Sie diese Werte abrufen.

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

Die letzte Konfigurationsänderung muss auf der Registerkarte **Konfiguration** des Dialogfelds **Identity and Access** vorgenommen werden. Aktivieren Sie das Kontrollkästchen **Enable web farm cookies**. Die detaillierte Vorgehensweise finden Sie unter [Adding Sign-On to Your Web Application Using Azure AD][Adding Sign-On to Your Web Application Using Azure AD] (Anmeldung mithilfe von Azure AD zu Ihrer Webanwendung hinzufügen, in englischer Sprache).

#### <a name="registerwaadapp"></a>Registrieren von Anwendungen in Azure Active Directory:

Registrieren Sie Ihre Anwendung bei Azure Active Directory, um die Registerkarte **Anbieter** auszufüllen. Wählen Sie im Azure-Verwaltungsportal im Bereich **Active Directory** Ihr Verzeichnis aus, und rufen Sie dann die Registerkarte **Anwendungen** auf. Sie haben dann die Möglichkeit, Ihre Azure-Website per URL hinzuzufügen. Beachten Sie beim Durchführen dieser Schritte, dass Sie zunächst die URL zur localhost-Adresse festlegen, die für das lokale Debugging in Visual Studio angegeben wurde. Später bei der Bereitstellung können Sie diese dann durch die tatsächliche URL Ihrer Website ersetzen.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

Sobald Sie diese hinzugefügt haben, stellt das Portal den Pfad zum STS-Metadaten-Dokument (die **URL für das Verbundmetadatendokument**) und den **URI der APP-ID** bereit. Dieser Werte werden auf der Registerkarte **Anbieter** im Dialogfeld **Identity and Access** in Visual Studio verwendet.

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

### <a name="aspnettoolsforwaad"></a>Microsoft ASP.NET-Tools für Azure Active Directory:

Alternativ zu den zuvor beschriebenen Schritten können Sie auch die [Microsoft ASP.NET-Tools für Azure Active Directory][1] verwenden. Klicken Sie hierzu im Menü **Projekt** in Visual Studio auf das Element **Enable Azure Authentication**. Daraufhin wird ein einfaches Dialogfeld angezeigt, das zur Eingabe der Adresse Ihrer Azure Active Directory-Domäne auffordert (nicht die URL für Ihre Anwendung).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Wenn Sie der Administrator dieser Active Directory-Domäne sind, aktivieren Sie das Kontrollkästchen **Provision this application in the Azure AD**. Daraufhin wird die Anwendung bei Active Directory registriert. Falls Sie nicht der Administrator sind, deaktivieren Sie dieses Kontrollkästchen, und geben Sie die Informationen an, die einem Administrator angezeigt werden. Dieser Administrator kann das Verwaltungsportal verwenden, um eine integrierte Anwendung anhand der vorherigen Schritte mit dem Identitäts- und Zugriffstool zu erstellen. Die detaillierten Schritte zur Verwendung von ASP.NET-Tools für Azure Active Directory finden Sie unter [Windows Azure-Authentifizierung][Windows Azure-Authentifizierung].

Für die Verwaltung Ihrer Branchenanwendung können Sie ein beliebiges unterstütztes Quellcodeverwaltungssystem für die Bereitstellung verwenden. Aufgrund der sehr engen Integration in Visual Studio in diesem Szenario wählen Sie vermutlich Team Foundation Service (TFS) als Quellcodeverwaltungssystem. Beachten Sie in diesem Fall, dass Azure-Websites Integration mit TFS bietet. Navigieren Sie im Verwaltungsportal zur Registerkarte **Dashboard** Ihrer Website. Wählen Sie dann **Set up deployment from source control**. Befolgen Sie die Anweisungen zur Verwendung von TFS.

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

## <a name="servicebusrelay"></a>Verwenden von Service Bus zur Integration in lokale Ressourcen

Viele Branchenanwendungen müssen in lokale Daten und Dienste integriert werden. Es gibt verschiedene Gründe, warum bestimmte Datentypen nicht in die Cloud verschoben werden können. Hierbei kann es sich um praktische oder rechtliche Gründe handeln. Falls Sie sich in der Planungsphase befinden und festlegen, welche Daten in Azure gehostet und welche lokal gespeichert werden sollen, sollten Sie sich unbedingt die Ressourcen im [Azure-Vertrauensstellungscenter][Azure-Vertrauensstellungscenter] ansehen. Hybride Webanwendungen werden in Azure ausgeführt und greifen auf Ressourcen zu, die lokal gespeichert werden müssen.

Wenn Sie virtuelle Computer oder Clouddienste verwenden, können Sie Anwendungen in Azure über ein virtuelles Netzwerk mit einem Unternehmensnetzwerk verbinden. Websites unterstützt jedoch keine virtuellen Netzwerke. Für diese Art der Integration mit Websites empfiehlt sich die Verwendung des Diensts [Azure Service Bus Relay][Azure Service Bus Relay]. Mithilfe des Service Bus-Relay können Anwendungen in der Cloud sicher mit WCF-Diensten verbunden werden, die in einem Unternehmensnetzwerk ausgeführt werden. Dank Service Bus kann diese Kommunikation ohne Öffnen von Firewall-Ports erfolgen.

In dem Diagramm unten kommunizieren sowohl die Cloud-Anwendung als auch der lokale WCF-Dienst über einen zuvor erstellen Namespace mit Service Bus. Der lokale WCF-Dienst hat Zugriff auf interne Daten und Dienste, die nicht in die Cloud verschoben werden können. Der WCF registriert einen Endpunkt im Namespace. Die Website, die in Azure ausgeführt wird, verbindet sich in Service Bus ebenfalls mit diesem Endpunkt. Sie müssen lediglich in der Lage sein, ausgehende öffentliche HTTP-Anforderungen zu erstellen, um diesen Schritt abzuschließen.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

Service Bus stellt dann eine Verbindung zwischen der Cloud-Anwendung und dem lokalen WCF-Dienst her. Dadurch entsteht eine Basisarchitektur für die Erstellung hybrider Anwendungen, die sowohl Azure- als auch lokale Ressourcen verwenden. Weitere Informationen finden Sie unter [Verwenden des Service Bus-Relay-Diensts][Verwenden des Service Bus-Relay-Diensts] und im [Lernprogramm zu Service Bus-Relaymessaging][Lernprogramm zu Service Bus-Relaymessaging]. Ein Beispiel, bei dem diese Technik angewendet wird, finden Sie unter [Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus][Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus] (Enterprise Pizza – Verbinden von Websites mit lokalen Ressourcen mithilfe von Service Bus, in englischer Sprache).

## <a name="monitor"></a>Überwachen der Anwendung

Geschäftsanwendungen profitieren von den Website-Standardfunktionen wie Skalierung und Überwachung. Bei einer Geschäftsanwendung, die an bestimmten Tagen oder Uhrzeiten unterschiedliche Lasten verarbeiten muss, kann die Funktion für automatische Skalierung (Vorschau) bei der horizontalen Skalierung und der Reduzierung der Kapazitäten unterstützen, um die Ressourcen effizient zu nutzen. Die Überwachungsfunktionen umfassen Endpunktüberwachung und Überwachung des Kontingents. Alle diese Optionen wurden in den Szenarien [Globale Webpräsenz][diese Informationen anzusehen] und [Digitale Marketingkampagnen][2] detaillierter abgedeckt.

Die Überwachungsanforderungen können zwischen verschiedenen Branchenanwendungen variieren, die eine unterschiedlich hohe Bedeutung für das Unternehmen haben. Für die geschäftskritischeren Anwendungen sollten Sie die Investition in einer Drittanbieter-Überwachungslösung wie [New Relic][New Relic] in Erwägung ziehen.

Branchenanwendungen werden in der Regel von IT-Mitarbeitern verwaltet. Im Falle von unerwarteten Fehlern oder unerwartetem Verhalten können IT-Mitarbeiter eine detailliertere Protokollierung aktivieren und die daraus resultierenden Daten analysieren, um die Probleme zu ermitteln. Navigieren Sie im Verwaltungsportal zur Registerkarte **Configure**, und prüfen Sie die Optionen in den Bereichen **application diagnostics** und **site diagnostics**.

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Nutzen Sie die verschiedenen Anwendungs- und Websiteprotokolle, um Probleme mit der Website zu beheben. Beachten Sie, dass einige der Optionen **Dateisystem** angeben. Dadurch werden die Protokolldateien im Dateisystem ihrer Site gespeichert. Auf diese kann entweder über FTP, Azure PowerShell oder die Azure-Befehlszeilentools zugegriffen werden. Bei anderen Optionen ist **Speicher** angegeben. Dadurch werden die Informationen an das von Ihnen angegebene Azure-Speicherkonto gesendet. Bei der **Webserverprotokollierung** können Sie außerdem ein Festplattenkontingent für das Dateisystem festlegen oder eine Aufbewahrungsrichtlinie für die Speicheroption. So wird verhindert, dass eine unendlich große Menge an gespeicherten Protokolldaten aufbewahrt wird.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Weitere Informationen zu diesen Protokolleinstellungen finden Sie unter [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website][Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website].

Sie können die nicht aufbereiteten Protokolle über FTP oder Speicherdienstprogramme wie Azure-Speicher-Explorer öffnen, oder Sie können die Protokollinformationen in Visual Studio anzeigen. Detaillierte Informationen zur Verwendung dieser Protokolle bei der Fehlerbehebung finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio].

## <a name="summary"></a>Zusammenfassung

Mit Azure können Sie sichere Intranetanwendungen in der Cloud hosten. Azure Active Directory bietet Ihnen die Möglichkeit, Benutzer zu authentifizieren, damit nur Mitglieder Ihres Unternehmens auf die Anwendungen zugreifen können. Der Service Bus-Relay-Dienst ermöglicht Webanwendungen die Kommunikation mit lokalen Diensten und Daten. Dank dieses hybriden Anwendungsansatzes lässt sich eine Geschäftsanwendung einfacher in der Cloud veröffentlichen, die zugehörigen Daten und Dienste müssen nicht ebenfalls migriert werden. Nach ihrer Bereitstellung profitieren die Geschäftsanwendungen von den Standardfunktionen für Skalierung und Überwachung von Azure-Websites. Weitere Informationen finden Sie in den folgenden Fachartikeln:

| Bereich                         | Ressourcen                                                                                                                                |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| **Planen**                      | - [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich][3]                                                                    |
| **Erstellen und Bereitstellen** | - [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website][Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website]                                                                     
                                   - [Bereitstellen einer sicheren ASP.NET MVC für Azure][Bereitstellen einer sicheren ASP.NET MVC für Azure]                                                                                   |
| **Authentifizierung**           | - [Übersicht über die Azure-Identitätsoptionen][Übersicht über die Azure-Identitätsoptionen]                                                                                         
                                   - [Azure Active Directory-Dienst][4]                                                                                                       
                                   - [Was ist ein Azure AD-Mandant?][Was ist ein Windows Azure AD-Mandant?]                                                                   
                                   - [Hinzufügen von einmaligem Anmelden zu einer Webanwendung mithilfe von Azure AD][Adding Sign-On to Your Web Application Using Azure AD]  
                                   - [Lernprogramm zur Azure-Authentifizierung][Windows Azure-Authentifizierung]                                                              |
| **Service Bus Relay**           | - [Verwenden des Service Bus Relay-Diensts][Verwenden des Service Bus Relay-Diensts]                                                                                             
                                   - [Lernprogramm zu Service Bus Relay-Messaging][Lernprogramm zu Service Bus-Relaymessaging]                                                |
| **Überwachen**                  | - [Überwachen von Websites][Überwachen von Websites]                                                                                                             
                                   - [Gewusst wie: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure][Gewusst wie: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure]                                          
                                   - [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website][5]                                            
                                   - [Problembehandlung von Azure-Websites in Visual Studio][6]                                                                               |

  [Azure-Websites]: /de-de/documentation/services/web-sites/
  [Azure-Clouddienste]: /de-de/documentation/services/cloud-services/
  [virtuelle Azure-Computer]: /de-de/documentation/services/virtual-machines/
  [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich]: /de-de/manage/services/web-sites/choose-web-app-service
  [Die Vorteile]: #benefits
  [Die Wahl einer Authentifizierungsstrategie]: #authentication
  [Erstellen einer Azure-Website zur Unterstützung von Authentifizierung]: #createintranetsite
  [Verwenden von Service Bus zur Integration in lokale Ressourcen]: #servicebusrelay
  [Überwachen der Anwendung]: #monitor
  [Globale Webpräsenz]: http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/
  [Digitale Marketingkampagnen]: http://www.windowsazure.com/de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [Azure Active Directory-Dienst]: /de-de/documentation/services/active-directory/
  [Verwenden von Azure Active Directory]: /de-de/manage/windows/fundamentals/identity/#ad
  [Ausführen von Windows Server Active Directory auf VMs]: /de-de/manage/windows/fundamentals/identity/#adinvm
  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx
  [Azure-Zugriffssteuerungsdienst]: http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [Verwenden von Azure Active Directory Access Control]: /de-de/manage/windows/fundamentals/identity/#ac
  [Was ist ein Windows Azure AD-Mandant?]: http://technet.microsoft.com/de-de/library/jj573650.aspx
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [Adding Sign-On to Your Web Application Using Azure AD]: http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [Verzeichnisintegration]: http://technet.microsoft.com/de-de/library/jj573653.aspx
  [diese Informationen anzusehen]: /de-de/manage/services/web-sites/global-web-presence-solution-overview/
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [Identitäts- und Zugriffstool für Visual Studio]: #identityandaccessforvs
  [Microsoft ASP.NET-Tools für Azure Active Directory]: #aspnettoolsforwaad
  [Identity and Access Tool]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Registrieren von Anwendungen im Azure Active Directory]: #registerwaadapp
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [1]: http://go.microsoft.com/fwlink/?LinkID=282306
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [Windows Azure-Authentifizierung]: http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [Azure-Vertrauensstellungscenter]: /de-de/support/trust-center/
  [Azure Service Bus Relay]: http://msdn.microsoft.com/de-de/library/windowsazure/jj860549.aspx
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [Verwenden des Service Bus-Relay-Diensts]: /de-de/develop/net/how-to-guides/service-bus-relay/
  [Lernprogramm zu Service Bus-Relaymessaging]: http://msdn.microsoft.com/de-de/library/windowsazure/ee706736.aspx
  [Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus]: http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [2]: /de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [New Relic]: http://newrelic.com/azure
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website]: /de-de/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [Problembehandlung von Azure-Websites in Visual Studio]: /de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [3]: http://www.windowsazure.com/de-de/manage/services/web-sites/choose-web-app-service
  [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website]: http://www.windowsazure.com/de-de/develop/net/tutorials/get-started/
  [Bereitstellen einer sicheren ASP.NET MVC für Azure]: http://www.windowsazure.com/de-de/develop/net/tutorials/web-site-with-sql-database/
  [Übersicht über die Azure-Identitätsoptionen]: http://www.windowsazure.com/de-de/manage/windows/fundamentals/identity/
  [4]: http://www.windowsazure.com/de-de/documentation/services/active-directory/
  [Verwenden des Service Bus Relay-Diensts]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/service-bus-relay/
  [Überwachen von Websites]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/
  [Gewusst wie: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  [5]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [6]: http://www.windowsazure.com/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
