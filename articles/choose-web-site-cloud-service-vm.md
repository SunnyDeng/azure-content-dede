<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="When to Choose Azure Web Sites, Cloud Services, or Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />




Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure
=======================================================================

Azure bietet verschiedene Wege für das Hosten Ihrer Webanwendungen, beispielsweise [Azure-Websites](http://go.microsoft.com/fwlink/?LinkId=306051), [Clouddienste](http://go.microsoft.com/fwlink/?LinkId=306052) und [virtuelle Computer](http://go.microsoft.com/fwlink/?LinkID=306053). Jetzt stellt sich sicher die Frage, welche dieser Optionen die beste für Sie ist. Vielleicht sind auch Konzepte wie IaaS oder PaaS unklar. In diesem Artikel werden die Optionen genau erläutert. So können Sie die richtige Wahl für Ihr Webszenario treffen. Alle drei Optionen ermöglichen es Ihnen, hoch skalierbare Webanwendungen in Azure auszuführen. Es gibt jedoch Unterschiede, die Sie bei Ihrer Entscheidung unterstützen können.

In den meisten Fällen sind Azure-Websites die beste Lösung. Sie bieten einfache und flexible Optionen für Bereitstellung und Verwaltung. Außerdem können Websites mit hohem Volumen gehostet werden. Sie können schnell eine neue Website erstellen: mit einer häufig verwendeten Software wie WordPress, aus der Web App Gallery oder indem Sie eine bereits vorhandene Website auf Azure-Websites übertragen. Wenn Sie das [Azure WebJobs-SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) (Vorschau) verwenden, können Sie auch Auftragsverarbeitung im Hintergrund hinzufügen.

Sie verfügen außerdem über die Option, Webanwendungen über Azure-Clouddienste oder virtuelle Computer in Azure zu hosten. Diese Optionen sind die richtige Wahl, wenn Ihre Webebene eine zusätzliche Ebene zur Kontrolle und Anpassung benötigt, die diese Optionen bieten. Diese gesteigerte Kontrolle sorgt jedoch auch für mehr Komplexität bei der Erstellung, Verwaltung und Bereitstellung von Anwendungen. Das folgende Diagramm dient der Abwägung zwischen den drei Optionen.

![Auswahldiagramm](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Sie können Websites einfacher einrichten, verwalten und überwachen. Dafür verfügen Sie jedoch über geringere Konfigurationsmöglichkeiten. Die Kernaussage lautet: Wenn kein zwingender Grund vorhanden ist, aus dem Sie Ihr Web-Front-End in Clouddiensten oder auf virtuellen Computern platzieren müssen, verwenden Sie Azure-Websites. Die folgenden Teile dieses Dokuments liefern Ihnen die Informationen, die Sie für eine fundierte Entscheidung benötigen. Dies umfasst:

-   [Szenarien](#scenarios)
-   [Dienstzusammenfassungen](#services)
-   [Funktionsvergleiche](#features)

Szenarien
---------

### Ich bin Inhaber eines Kleinunternehmens und suche nach einem günstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.

Azure-Websites (WAWS) sind eine tolle Lösung für dieses Szenario, da Sie mit einer kostenlosen Nutzung beginnen und später bei Bedarf weitere Funktionen hinzufügen können. Beispielsweise erhalten Sie zu jeder kostenlosen Website eine Domäne von Azure (*Ihr\_Unternehmen*.azurewebsites.net). Wenn Sie Ihre eigene Domäne verwenden möchten, können Sie dies für einen geringen monatlichen Preis hinzufügen (ab Januar 2014). Es gibt viele weitere Dienste und Skalierungsoptionen dank derer Sie die Website mit steigenden Benutzeranforderungen weiterentwickeln können. **Azure-Websites** ermöglichen Ihnen Folgendes:

-   Beginnen mit einer kostenlosen Ebene und nach oben Skalieren dieser je nach Anforderungen
-   Verwenden der Web App Gallery, um schnell häufig genutzte Webanwendungen wie WordPress einzurichten
-   Hinzufügen zusätzlicher Dienste und Funktionen von Azure zu Ihrer Anwendung, je nach Bedarf
-   Sichern Ihrer Websites mit HTTPS über das Zertifikat, das Sie zusammen mit Ihrem Domänennamen *Ihr\_Unternehmen*.azurewebsites.net erhalten haben

### Ich bin ein Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.

Für Webentwickler bieten Azure-Websites alles, was diese zur Erstellung anspruchsvoller Webanwendungen benötigen. Azure-Websites ermöglichen nahtlose Integration mit Tools wie Visual Studio und SQL-Datenbank. **Websites** ermöglichen Entwicklern Folgendes:

-   Verwenden von Befehlszeilentools für [automatisierte Aufgaben](http://www.windowsazure.com/de-de/documentation/scripts/?services=web-sites)
-   Arbeiten mit gängigen Sprachen wie [.Net](http://www.windowsazure.com/de-de/develop/net/), [PHP](http://www.windowsazure.com/de-de/develop/php/), [Node.js](http://www.windowsazure.com/de-de/develop/nodejs/) und [Python](http://www.windowsazure.com/de-de/develop/python/)
-   Wählen drei verschiedener Skalierungsebenen für die Skalierung auf sehr hohe Kapazitäten
-   Integrieren in andere Azure-Dienste, wie [SQL-Datenbank](http://www.windowsazure.com/de-de/documentation/services/sql-database/), [Service Bus](http://www.windowsazure.com/de-de/documentation/services/service-bus/) und [Speicher](http://www.windowsazure.com/de-de/documentation/services/storage/), oder Partnerangeboten aus dem [Azure Store](http://www.windowsazure.com/de-de/gallery/store/), beispielsweise MySQL und MongoDB
-   Integrieren in Tools wie Visual Studio, Git, WebMatrix, WebDeploy, TFS und FTP

### Ich migriere meine Webanwendung mit mehreren Ebenen und einem Web-Front-End zur Cloud.

Wenn Sie eine Anwendung mit mehreren Ebenen ausführen möchten, zum Beispiel einen Webserver, der mit einer Datenbank kommuniziert, um Daten von Websites zu erhalten und zu speichern, gibt es dafür mehrere Optionen in Azure. Die architektonischen Optionen umfassen Websites, Clouddienste und virtuelle Computer. **Websites** sind eine gute Option für die Webebene Ihrer Lösung und können mit Azure-SQL-Datenbank verwendet werden, um eine Zwei-Ebenen-Architektur zu erstellen. Websites ermöglichen es Ihnen auch, mit dem Azure-WebJobs-SDK (Vorschau) im Hintergrund ablaufende oder lang andauernde Prozesse auszuführen. Wenn Sie eine komplexere Architektur oder flexiblere Skalierungsoptionen benötigen, sind Clouddienste oder virtuelle Computer die beste Wahl.

**Clouddienste** ermöglichen Ihnen Folgendes:

-   Hosten von Diensten für Web, mittlere Ebene und Back-End mit skalierbaren Web- und Workerrollen
-   Ausschließliches Hosten von Diensten für mittlere Ebene und Back-End mit Workerrollen, wobei das Front-End auf Azure-Websites verbleibt
-   Unabhängiges Skalieren von Front-End- und Back-End-Diensten

**Virtuelle Computer** ermöglichen Ihnen Folgendes:

-   Einfacheres Migrieren maßgeschneiderter Umgebungen als Image eines virtuellen Computers
-   Ausführen von Software oder Diensten, die nicht mit Websites oder Clouddiensten konfiguriert werden können

### Meine Anwendung basiert auf maßgeschneiderten Windows-/Linux-Umgebungen.

Wenn Ihre Anwendung komplexe Installation oder Konfiguration von Software und Betriebssystem erfordert, sind virtuelle Computer die beste Lösung. **Virtuelle Computer** ermöglichen Ihnen Folgendes:

-   Verwenden des Vitueller-Computer-Katalogs, um mit einem Betriebssystem wie Windows oder Linux zu beginnen und dieses später an Ihre Anwendungsanforderungen anzupassen
-   Erstellen und Hochladen eines maßgeschneiderten Image eines bereits vorhandenen lokalen Servers für die Ausführung auf einem virtuellen Computer in Azure

### Meine Website verwendet Open Source-Software und ich möchte sie in Azure hosten.

Sie können mit allen drei Optionen Open-Source-Sprachen und -Frameworks hosten. **Bei Clouddiensten** müssen Sie Startaufgaben verwenden, um erforderliche Open-Source-Software, die unter Windows ausgeführt wird, zu installieren und zu konfigurieren. Mit **virtuellen Computern** können Sie die Software auf dem Image eines virtuellen Computers installieren und konfigurieren. Dieser kann Windows- oder Linux-basiert sein. Wenn Ihr Open-Source-Framework auf Websites unterstützt wird, ist es viel einfacher, diese Anwendungstypen zu hosten. Dies liegt daran, dass Websites automatisch mit den Sprachen und Frameworks konfiguriert werden können, die Ihre Anwendung benötigt. **Websites** ermöglichen Ihnen Folgendes:

-   Verwenden vieler gängiger Open-Source-Sprachen wie [.NET](http://www.windowsazure.com/de-de/develop/net/), [PHP](http://www.windowsazure.com/de-de/develop/php/), [Node.js](http://www.windowsazure.com/de-de/develop/nodejs/) und [Python](http://www.windowsazure.com/de-de/develop/python/)
-   Einrichten von WordPress, Drupal, Umbraco, DNN und vielen weiteren Webanwendungen von Drittanbietern
-   Migrieren vorhandener Anwendungen oder Erstellen einer neuen mit der Web App Gallery

### Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.

Wenn Sie eine Branchenanwendung erstellen möchten, benötigt Ihre Website möglicherweise direkten Zugriff auf Dienste oder Daten in Ihrem Unternehmensnetzwerk. Dies ist mit**Websites**, **Clouddiensten** und **virtuellen Computern** umsetzbar. Es gibt Unterschiede bei den jeweiligen Ansätzen, einschließlich der Folgenden:

-   Eine sichere Verbindung zwischen Websites und lokalen Ressourcen ist über die Verwendung eines Servicebus-Relais möglich. So können Dienste im Unternehmensnetzwerk Aufgaben im Auftrag der Webanwendung ausführen, ohne dass sämtliche Elemente in die Cloud verschoben werden müssen oder ein virtuelles Netzwerk erstellt werden muss.
-   Clouddienste und virtuelle Computer bieten die Vorteile des Virtuellen Netzwerks. Im Endeffekt ermöglicht das Virtuelle Netzwerk Computern, die in Azure ausgeführt werden, eine Verbindung zu einem lokalen Netzwerk. Azure wird dann zu einer Erweiterung Ihres unternehmenseigenen Datencenters.

### Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.

HTTP-basierte Webdienste ermöglichen die Unterstützung vieler unterschiedlicher Clients, einschließlich mobiler Clients. Frameworks wie die ASP.NET-Web-API können in Visual Studio intergiert werden. Dies erleichtert die Erstellung und Verwendung von REST-Diensten. Diese Dienste sind von einem Webendpunkt aus exponiert, sodass es möglich ist, jede beliebige Webhostingtechnik in Azure anzuwenden, um dieses Szenario zu unterstützen. **Websites** sind jedoch auch eine gute Wahl für das Hosten von REST-APIs. Websites ermöglichen Ihnen Folgendes:

-   Schnelles Erstellen einer Website für das Hosten des HTTP-Webdiensts in einem der global verteilten Datencenter von Azure.
-   Migrieren vorhandener oder Erstellen neuer Dienste mit möglicher vorteilhafter Nutzung der ASP.NET-Web-API in Visual Studio.
-   Erreichen von SLA zur Verfügbarkeit mit einer einzelnen Instanz oder horizontale Skalierung auf mehrere dedizierte Computer
-   Verwenden der veröffentlichten Website, um für HTTP-Clients REST-APIs bereitzustellen, auch für mobile Clients

Dienstzusammenfassungen
-----------------------

[Azure-Websites](http://go.microsoft.com/fwlink/?LinkId=306051) ermöglicht Ihnen schnelles Erstellen hoch skalierbarer Webanwendungen in Azure. Sie können das Azure-Portal oder die Befehlszeilentools verwenden, um eine Website mit gängigen Sprachen wie .NET, PHP, Node.js und Python zu erstellen. Unterstützte Frameworks sind bereits bereitgestellt und es sind keine weiteren Installationsschritte erforderlich. Die Azure-Websites Galerie enthält viele Anwendungen von Drittanbietern, wie Drupal und WordPress, sowie Entwicklungsframeworks, wie Django und CakePHP. Wenn Sie eine Website erstellt haben, können Sie entweder eine vorhandene Website migrieren oder eine völlig neue erstellen. Mit Websites müssen Sie keine physische Hardware mehr verwalten und erhalten gleichzeitig mehrere Skalierungsoptionen. Sie können von einem gemeinsamen Modell mit mehreren Mandanten zu einem Standardmodus wechseln, bei dem dedizierte Computer eingehenden Datenverkehr bearbeiten. Websites ermöglichen auch die Integration in andere Azure-Dienste wie SQL-Datenbank, Service Bus und Speicher. Wenn Sie das [Azure WebJobs-SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) (Vorschau) verwenden, können Sie Auftragsverarbeitung im Hintergrund hinzufügen. Alles in allem erleichtern Ihnen Azure-Websites die Konzentration auf die Anwendungsentwicklung durch die Unterstützung einer großen Auswahl an Sprachen, Open-Source-Anwendungen und Bereitstellungsmethoden (FTP, Git, Web Deploy oder TFS). Wenn keine besonderen Anforderungen bestehen, die Clouddienste oder virtuelle Computer erfordern, sind Azure-Websites mit hoher Wahrscheinlichkeit die beste Wahl.

[Clouddienste](http://go.microsoft.com/fwlink/?LinkId=306052) ermöglichen Ihnen die Erstellung hochverfügbarer, skalierbarer Webanwendungen in einer funktionsreichen Platform-as-a-Service (PaaS)-Umgebung. Anders als bei Websites wird ein Clouddienst zuerst in einer Entwicklungsumgebung wie Visual Studio erstellt und daraufhin in Azure bereitgestellt. Frameworks wie PHP erfordern maßgeschneiderte Bereitstellungsschritte oder Aufgaben, die das Framework beim Rollenstart installieren. Der Hauptvorteil von Clouddiensten ist die Möglichkeit, komplexere Architekturen mit mehreren Ebenen zu unterstützen. Ein einzelner Clouddienst kann aus einer Front-End-Webrolle und einer oder mehreren Workerrollen bestehen. Jede Ebene kann unabhängig skaliert werden. Sie verfügen außerdem über mehr Kontrolle über die Infrastruktur Ihrer Webanwendung. Beispielsweise können Sie eine Remote-Desktop-Verbindung mit den Computern erstellen, auf denen Rolleninstanzen ausgeführt werden. Außerdem können Sie erweiterte Änderungen an IIS und der Computerkonfiguration vornehmen, die beim Rollenstart ausgeführt werden. Dies umfasst auch Aufgaben, die Administratorsteuerung erfordern.

[Virtuelle Computer](http://go.microsoft.com/fwlink/?LinkID=306053) ermöglichen Ihnen die Ausführung von Webanwendungen auf virtuellen Computern in Azure. Diese Funktion ist auch unter der Bezeichnung Infrastructure-as-a-Service (IaaS) bekannt. Erstellen Sie neue Windows Server- oder Linux-Computer über das Portal, oder laden Sie ein bereits vorhandenes Image eines virtuellen Computers hoch. Mit virtuellen Computern erhalten Sie den höchsten Grad an Kontrolle über Betriebssystem, Konfiguration sowie installierte Software und Dienste. Dies ist eine gute Option für die schnelle Migration komplexer lokaler Webanwendungen zur Cloud, da die Computer als Ganzes wechseln können. Mit dem Virtuellen Netzwerk können Sie diese Computer außerdem mit lokalen Unternehmensnetzwerken verbinden. Wie bei Clouddiensten verfügen Sie über Remote-Zugriff auf diese Computer und die Möglichkeit, Konfigurationen auf Administratorebene zu ändern. Im Gegensatz zu Websites und Clouddiensten ist es jedoch erforderlich, dass Sie die Images Ihrer virtuellen Computer vollständig auf der Infrastrukturebene verwalten. Ein einfaches Beispiel ist, dass Sie Ihre eigenen Patches auf das Betriebssystem anwenden müssen.

Funktionsvergleiche
-------------------

In der folgenden Tabelle werden die Funktionen von Websites, Clouddiensten und virtuellen Computern verglichen, um Sie bei Ihrer Wahl zu unterstützen. Kästchen, die ein Sternchen enthalten, werden in den Hinweisen unterhalb der Tabelle genauer erläutert.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Funktion</th>
   <th align="left" valign="middle">Websites</th>
   <th align="left" valign="middle">Clouddienste (Webrollen)</th>
   <th align="left" valign="middle">Virtuelle Computer</th>
</tr>
<tr>
   <td valign="middle"><p>Zugriff auf Dienste wie Service Bus, Speicher und SQL-Datenbank</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Hosten der Web- oder Webdienstebene einer mehrschichtigen Architektur</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Hosten der mittleren Ebene einer mehrschichtigen Architektur</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Integrierte Unterstützung von MySQL-as-a-Service</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X <sup>1</sup></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Unterstützung von ASP.NET, klassischem ASP, Node.js, PHP, Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Horizontale Skalierung auf mehrere Instanzen ohne erneute Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X <sup>2</sup></td>
</tr>
<tr>
   <td valign="middle"><p>SSL-Unterstützung</p></td>
   <td valign="middle">X <sup>3</sup></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Visual Studio-Integration</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Remote-Debuggen</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit Git, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Bereitstellung von Code mit Web Deploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle"><sup>4</sup></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Unterstützung von WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Zeitnahe Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Gemeinsame Inhalte/gemeinsame Konfiguration der Instanzen</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Skalierung auf leistungsstärkere Computer ohne erneute Bereitstellung</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Mehrere Bereitstellungsumgebungen (Produktion und Staging)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Netzwerkisolation mit Azure – Virtuelles Netzwerk</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Azure Traffic Manager-Unterstützung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Remote-Desktopzugriff auf Server</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Möglichkeit zum Definieren/Ausführen von Startaufgaben</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Automatische Aktualisierungsverwaltung für das Betriebssystem</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Integrierte Endpunktüberwachung</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Nahtloser Plattformwechsel (32-Bit/64-Bit)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>

<sup>1</sup>Web- oder Workerrollen können in MySQL-as-a-Service über Angebote von ClearDB integriert werden, jedoch nicht als Teil des Verwaltungsportal-Workflows.

<sup>2</sup>Obwohl eine horizontale Skalierung virtueller Computer auf mehrere Instanzen möglich ist, müssen die Dienste, die auf diesen Computern ausgeführt werden, so geschrieben werden, dass sie diese horizontale Skalierung auffangen. Die Konfiguration eines Lastenausgleichsmoduls ist erforderlich, um Anfragen zwischen den Computern zu leiten. Zuletzt sollte eine Affinitätsgruppe für alle Computer erstellt werden, die an derselben Rolle beteiligt sind. Dies dient zum Schutz vor gleichzeitigen Neustarts bei Wartungs- oder Hardwarefehlern.

<sup>3</sup>Bei Websites wird SSL für benutzerdefinierte Domänennamen nur im Standardmodus unterstützt. Weitere Informationen zur Verwendung von SSL mit Websites finden Sie unter [Configuring an SSL certificate for an Azure Web Site](http://www.windowsazure.com/de-de/develop/net/common-tasks/enable-ssl-web-site/) (Konfigurieren eines SSL-Zertifikats für eine Azure-Website, in englischer Sprache).

<sup>4</sup>Web Deploy wird für Clouddienste unterstützt, wenn die Bereitstellung über einzelne Instanzrollen erfolgt. Produktionsrollen erfordern jedoch mehrere Instanzen zur Erfüllung der Azure-SLA. Daher ist Web Deploy kein geeigneter Bereitstellungsmechanismus für Clouddienste in der Produktion.

