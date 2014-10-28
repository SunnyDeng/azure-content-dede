<properties linkid="develop-net-architecture-multi-tenant-web-application" urlDisplayName="Multi-Tenant Web Application Pattern" pageTitle="Multi-Tenant Web Application Pattern - Azure Architecture" metaKeywords="" description="Find architectural overviews and design patterns that describe how to implement a multi-tenant web application on Azure." metaCanonical="" services="" documentationCenter=".NET" title="Multitenant Applications in Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

<div>
<div class="left-nav">
<div class="static-nav">
<ul>
<li class="menu-nodejs-compute"><a href="/de-de/develop/net/compute/">Compute</a></li>
<li class="menu-nodejs-data"><a href="/de-de/develop/net/data/">Datendienste</a></li>
<li class="menu-nodejs-appservices"><a href="/de-de/develop/net/app-services/">App-Dienste</a></li>
<li><a href="/de-de/develop/net/reference/">Verweis</a></li>
<li><a href="/de-de/develop/net/guidance/">Anleitungen</a></li>
<li><a href="/de-de/develop/net/architecture/">Architektur</a></li>
<li><a href="/de-de/develop/net/samples/">Beispiele</a></li>
<li><a href="/de-de/develop/net/end-to-end-Apps/">Szenario-basierte Lernprogramme</a></li>
</ul>
<ul class="links">
<li class="forum"><a href="/de-de/support/forums/">Foren</a></li>
</ul>
</div>

<div class="floating-nav jump-to"><br />
<ul>
<li>In diesem Abschnitt (gehe zu):</li>
<li><a href="/de-de/develop/net/architecture/#overviews">&Uuml;bersichten der Anwendungsarchitektur</a></li>
<li><strong>Anwendungsmuster: Mehrinstanzenf&auml;hige Anwendungen</strong></li>
<li><a href="/de-de/develop/net/architecture/load-testing-pattern/">Anwendungsmuster: Auslastungstests</a></li>
<li><a href="/de-de/develop/net/architecture/#designpatterns">Entwurfsmuster</a></li>
</ul>
</div>

</div>

</div>

# Mehrinstanzenfähige Anwendungen in Azure

Eine mehrinstanzenfähige Anwendung ist eine gemeinsame Ressource, mit der unterschiedliche Benutzer, oder "Mandanten", die Anwendung so anzeigen können, als handele es sich um ihre eigene. Ein typisches Szenario für eine mehrinstanzenfähige Anwendung ist, wenn alle Benutzer das Benutzererlebnis individualisieren möchten, aber ansonsten die gleichen grundlegenden Geschäftsanforderungen haben. Beispiele für große mehrinstanzenfähige Anwendungen sind Office 365, Outlook.com und visualstudio.com.

Aus Sicht des Anbieters der Anwendung liegen die Vorteile vor allem in der Effizienz bei Betrieb und Kosten. Eine Version Ihrer Anwendung kann die Anforderungen vieler Mandanten/Kunden erfüllen. Dies ermöglicht eine Konsolidierung von Systemadministrationsaufgaben wie Überwachung, Leistungssteigerung, Softwarewartung und Datensicherung.

Im Folgenden finden Sie eine Liste der wichtigsten Anforderungen aus Sicht des Anbieters.

-   **Bereitstellung**: Sie müssen in der Lage sein, neue Mandanten für die Anwendung bereitzustellen. Bei mehrinstanzenfähigen Anwendungen mit einer großen Zahl an Mandanten ist es normalerweise notwendig, diesen Prozess mittels Self-Service-Bereitstellung zu automatisieren.
-   **Wartbarkeit**: Sie müssen in der Lage sein, Upgrades der Anwendung und weitere Wartungsaufgaben durchzuführen, während diese von mehreren Mandanten verwendet wird.
-   **Überwachung**: Sie müssen die Anwendung stets überwachen, um Probleme zu erkennen und zu beheben. Hier muss auch überwacht werden, wie jeder einzelne Mandat die Anwendung nutzt.

Eine korrekt implementierte mehrinstanzenfähige Anwendung bietet Benutzern die folgenden Vorteile.

-   **Isolation**: Die Aktivitäten einzelner Mandanten beeinflussen nicht die Nutzung der Anwendung durch andere Mandanten. Mandanten können untereinander nicht auf ihre Daten zugreifen. Dem Mandanten erscheint es so, als ob er die Anwendung exklusiv nutzt.
-   **Verfügbarkeit**: Für einzelne Mandanten muss die Anwendung konstant verfügbar sein, unter Umständen mit Garantien, die in einem SLA festgelegt sind. Erneut, die Aktivitäten anderer Mandanten sollten die Verfügbarkeit der Anwendung nicht beeinträchtigen.
-   **Skalierbarkeit**: Die Anwendung kann über Skalierung die Anforderungen einzelner Mandanten erfüllen. Die Präsenz und Aktivitäten anderer Mandanten sollten die Leistung der Anwendung nicht beeinträchtigen.
-   **Kosten**: Die Kosten sind niedriger als bei der Ausführung einer dedizierten Einzelinstanzanwendung, da Mehrinstanzenfähigkeit die gemeinsame Nutzung von Ressourcen ermöglicht.
-   **Individualisierbarkeit**. Die Möglichkeit, die Anwendung für einen einzelnen Mandanten auf verschiedene Weisen zu individualisieren, beispielsweise durch das Hinzufügen oder Entfernen von Funktionen, Ändern von Farben und Logos oder sogar durch das Hinzufügen eigener Codes oder Skripte.

Kurz, obwohl es einige Überlegungen gibt, die Sie bei der Bereitstellung eines hochskalierbaren Dienstes berücksichtigen müssen, gibt es auch einige Ziele und Anforderungen, die bei vielen mehrinstanzenfähigen Anwendungen üblich sind. Einige sind möglicherweise für bestimmte Szenarien nicht relevant, und die Wichtigkeit einzelner Ziele und Anforderungen unterscheidet sich je nach Szenario. Als Anbieter einer mehrinstanzenfähigen Anwendung gibt es für Sie ebenfalls Ziele und Anforderungen wie das Erfüllen der Ziele und Anforderungen des Mandanten, Rentabilität, mehrere Serviceebenen, Bereitstellung, Wartbarkeit, Überwachung und Automatisierung.

Weitere Informationen zu zusätzlichen Überlegungen bei der Entwicklung einer mehrinstanzenfähigen Anwendung finden Sie unter [Hosting a Multi-Tenant Application on Azure][Hosting a Multi-Tenant Application on Azure] (Hosten einer mehrinstanzenfähigen Anwendung in Azure, in englischer Sprache).

Azure bietet viele Funktionen, mit denen Sie die entscheidenden Probleme lösen können, die bei der Entwicklung eines mehrinstanzenfähigen Systems auftreten.

**Isolation**

-   Segmentieren Sie Websitemandanten nach Hostheadern mit oder ohne SSL-Kommunikation.
-   Segmentieren Sie Websitemandanten nach Abfrageparametern.
-   Webdienste in Workerrollen

    -   Workerrollen, die typischerweise Daten im Hintergrund einer Anwendung verarbeiten
    -   Workerrollen, die typischerweise als Frontend für Anwendungen agieren

**Storage**

Datenverwaltung wie eine SQL Azure-Datenbank oder Azure-Speicherdienste wie der Tabellenspeicherdienst, der Speicherdienste für große Mengen unstrukturierter Daten bietet, außerdem der Blob-Dienst, der Speicherdienste für große Mengen unstrukturierter Daten oder binäre Daten wie Videos, Audiodateien und Bilder bietet

-   Sichern mehrinstanzenfähiger Daten in einer SQL-Datenbank mit geeigneten SQL-Server-Anmeldungen pro Mandant
-   Wenn Sie Azure Tables für Anwendungsressourcen verwenden können Sie über das Festlegen einer Zugriffssteuerung auf Containerebene Berechtigungen anpassen, ohne neue URLs für die Ressourcen ausgeben zu müssen, die mit gemeinsamen Zugriffssignaturen geschützt sind.
-   Azure-Warteschlangen für Anwendungsressourcen: Azure-Warteschlangen werden meist verwendet, um die Verarbeitung für die Mandanten zu beschleunigen, können jedoch auch eingesetzt werden, um erforderliche Aufgaben für Bereitstellung oder Verwaltung zu verteilen.
-   Service Bus-Warteschlangen für Anwendungsressourcen lenken Aufgaben zu einem gemeinsamen Dienst. Sie können eine einzelne Warteschlange verwenden, bei der jeder sendende Mandant nur über Berechtigungen dazu verfügt (die sich von den ausgegebenen Ansprüchen aus ACS ableiten), Aufgaben in diese Warteschlange zu leiten, wobei nur die Empfänger des Dienstes die Berechtigung besitzen, aus dieser Warteschlange die Daten zu beziehen, die von mehreren Mandanten stammen.

**Verbindungs- und Sicherheitsdienste**

-   Azure Service Bus dient zur besseren Skalierung und bietet mehr Resilienz. Es handelt sich hierbei um eine Nachrichteninfrastruktur, die sich zwischen Anwendungen befindet und es diesen ermöglicht, Nachrichten über eine lose Verbindung auszutauschen.

**Netzwerkdienste**

Azure bietet mehrere Netzwerkdienste, die Authentifizierung unterstützen und die Verwaltbarkeit gehosteter Anwendungen verbessern. Diese Dienste umfassen Folgendes:

-   Mit Azure – Virtuelles Netzwerk können Sie in Azure virtuelle private Netzwerke (VPN) bereitstellen und verwalten und diese sicher mit Ihrer IT-Infrastruktur vor Ort verbinden.
-   Der Traffic Manager für das virtuelle Netzwerk ermöglicht Ihnen einen Lastenausgleich für eingehenden Datenverkehr über mehrere gehostete Azure-Dienste, gleich, ob sie im selben Datencenter ausgeführt werden oder über verschiedene Datencenter weltweit.
-   Azure Active Directory (Azure AD) ist ein moderner, REST-basierter Dienst, der Identitätsverwaltung und Funktionen zur Zugriffssteuerung für Ihre Cloudanwendungen bietet. Die Verwendung von Azure AD für Anwendungsressourcen bietet einen einfachen Weg der Authentifizierung und Autorisierung von Benutzern für den Zugang zu Ihren Webanwendungen und Webdiensten. Gleichzeitig werden die Funktionen zur Authentifizierung und Autorisierung aus Ihrem Code herausgenommen.
-   Azure Service Bus bietet eine sichere Nachrichten- und Datenflussfunktion für verteilte und hybride Anwendungen, beispielsweise Kommunikation zwischen Anwendungen, die von Azure gehostet werden, und lokalen Anwendungen/Diensten, ganz ohne komplexe Firewall- und Sicherheitsinfrastruktur. Verwendung von Service Bus Relay für Anwendungsressourcen: Die Dienste, die als Endpunkte erreichbar sind, können dem Mandanten gehören (beispielsweise außerhalb des Systems gehostete, lokale Dienste) oder speziell für den Mandanten bereitgestellt werden (da sensible, mandantenspezifische Daten bewegt werden).

**Bereitstellen von Ressourcen**

Azure bietet viele verschiedene Möglichkeiten zur Bereitstellung neuer Mandanten. Bei mehrinstanzenfähigen Anwendungen mit einer großen Zahl an Mandanten ist es normalerweise notwendig, diesen Prozess mittels Self-Service-Bereitstellung zu automatisieren.

-   Mit Workerrollen können Sie für einzelne Mandanten Ressourcen bereitstellen oder entfernen (wenn sich beispielsweise ein neuer Mandant anmeldet oder ein bereits vorhandener kündigt), Metriken zu Messzwecken sammeln und die Skalierung verwalten, indem einem bestimmten Zeitplan gefolgt oder das Überschreiten bestimmter Schwellen von wesentlichen Leistungsindikatoren als Auslöser genommen wird. Diese Rolle können Sie außerdem verwenden, um Aktualisierungen und Upgrades für die Lösung bereitzustellen.
-   Sie können Azure Blobs verwenden, um Rechen- oder vorinitialisierte Speicherressourcen für neue Mandanten bereitzustellen und gleichzeitig Zugriffsrichtlinien auf Containerebene zu bieten, um die Rechendienstpakete, VHD-Images und andere Ressourcen zu schützen.
-   Optionen für die Bereitstellung von SQL-Datenbank-Ressourcen für einen Mandanten umfassen:

    -   DDL in Skripten oder eingebettet als Ressourcen in Assemblys
    -   Programmatisches Bereitstellen von DAC-Paketen für SQL Server 2008 R2
    -   Kopieren aus einer Masterreferenzdatenbank
    -   Verwenden von Datenbankimport und -export zur Bereitstellung einer neuen Datenbank aus einer Datei



<!--links-->

  [Compute]: /de-de/develop/net/compute/
  [Datendienste]: /de-de/develop/net/data/
  [App-Dienste]: /de-de/develop/net/app-services/
  [Verweis]: /de-de/develop/net/reference/
  [Anleitungen]: /de-de/develop/net/guidance/
  [Architektur]: /de-de/develop/net/architecture/
  [Beispiele]: /de-de/develop/net/samples/
  [Szenario-basierte Lernprogramme]: /de-de/develop/net/end-to-end-Apps/
  [Foren]: /de-de/support/forums/
  [Übersichten der Anwendungsarchitektur]: /de-de/develop/net/architecture/#overviews
  [Anwendungsmuster: Auslastungstests]: /de-de/develop/net/architecture/load-testing-pattern/
  [Entwurfsmuster]: /de-de/develop/net/architecture/#designpatterns
  [Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/de-de/library/hh534480.aspx
