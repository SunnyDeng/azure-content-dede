<properties
  pageTitle="Linux- und Open-Source-Computing auf Azure"
  description="Dieses Thema enthält eine Liste von Linux- und Open Source-Computing auf Azure, darunter die grundlegende Verwendung von Linux, einige grundlegende Konzepte zum Ausführen oder Hochladen von Linux-Images auf Azure und anderen Inhalten zu bestimmten Technologien und Optimierungen."
  documentationCenter=""
  authors="squillace"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/17/2015"
  ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Linux- und Open-Source-Computing auf Azure

Dieses Dokument versucht, an einem Ort alle Themen aufzulisten, die von Microsoft und seinen Partnern über die Ausführung von Linux-basierten virtuellen Maschinen und anderen Open-Source-Computing-Umgebungen und Anwendungen auf Microsoft Azure erstellt wurden. Da sich sowohl Azure als auch Open-Source-Computing fortlaufend verändern, ist fast sicher, dass dieses Dokument bereits veraltet ist, *despite* bemühen wir uns, kontinuierlich neuere Themen hinzuzufügen und veraltete Themen zu entfernen. Wenn wir ein Thema übersehen haben, lassen Sie es uns in den Kommentaren wissen, oder senden Sie eine Pull-Anforderung an unser [Github-Repository](https://github.com/Azure/azure-content/).

Die Abschnitte sind wie folgt gegliedert. (Links können in mehr als einem Abschnitt auftreten, da Themen sich mit mehr als einem Konzept, mehr als einer Verteilung oder mehr als einer Technologie beschäftigen können.)

- [Verteilungen](#distros) &mdash; Themen, die eine spezielle Verteilung behandeln.
- [Die Grundlagen](#basics) &mdash; Viele der Grundlagen, die Sie entweder bereits kennen oder kennen müssen.
- [Community-Images und Repositorys](#images) &mdash; Andere Orte mit sehr nützlichen Informationen, Repositorys und Binärdateien.
- [Sprachen und Plattformen](#langsandplats)
- [Beispiele und Skripts](#samples)
- [Authentifizierung und Verschlüsselung](#security) &mdash; Wichtige sicherheitsrelevante Themen, die nicht notwendigerweise für Azure spezifisch sind.
- [DevOps, Verwaltung und Optimierung](#devops) &mdash; Eine wichtige Kategorie, die fortlaufend Änderungen unterworfen ist.
- [Support, Problembehandlung und weitergehende Problembehandlung](#supportdebug) 

Darüber hinaus gibt es mehrere Themen, in denen verschiedene Linux-Optionen, Image-Repositorys, Fallstudien und Gewusst-wie-Themen zum Hochladen Ihrer eigenen benutzerdefinierten Images beschrieben werden: 

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Veranstaltungen und Demonstrationen: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Gewusst wie: Hochladen Ihres eignen Verteilungs-Images](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd/) (und auch von Anweisungen mit einer [unterstützten Azure-Verteilung](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/))
- [Hinweise: Allgemeine Linux-Anforderungen zur Ausführung in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-generic/)
- [Hinweise: Allgemeine Einführung zu Linux in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-introduction/)


### <a id='distros'>Verteilungen</a>

Es gibt Unmengen von Linux-Verteilungen, die in der Regel durch die Paket-Verwaltungssysteme unterteilt sind: Einige sind dpgk-basiert, wie Debian und Ubuntu und andere sind rpm-basiert, wie CentOS, SUSE und RedHat. Einige Unternehmen bieten Verteilungs-Images als formale Partner von Microsoft. Diese werden unterstützt. Andere werden von der Community bereitgestellt. Für die Verteilungen in diesem Abschnitt gibt es formale Artikel, selbst wenn sie nur in Beispielen für andere Technologien verwendet wurden.


#### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu ist eine sehr populäre und von Azure unterstützte Linux-Verteilung, basierend auf der dkpg- und apt-get-Paketverwaltung.

1. [Gewusst wie: Hochladen Ihres eigenen Ubuntu-Images](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-ubuntu/)
2. [Gewusst wie: Ubuntu-LAMP-Stapel](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
2. [Images: LAMP-Stapel](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Gewusst wie: MySQL-Cluster](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
4. [Gewusst wie: Node.js und Cassandra](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. [Gewusst wie: IPython Notebook](http://azure.microsoft.com/documentation/articles/virtual-machines-python-ipython-notebook/)
6. [ Ausführen von ASP.NET 5 auf Linux mit Docker-Containern](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Images: Redis Server](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Images: Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Images: Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Images: Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)

#### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian ist eine wichtige Verteilung für Linux und Open-Source basierend auf der dpgk- und apt-get-Paketverwaltung. Das MSOpenTech VM Depot bietet verschiedene Images.

#### CentOS

Die CentOS-Linux-Verteilung ist eine stabile, vorhersehbare, verwaltbare und reproduzierbare Plattform, die aus den Quellen von Red Hat Enterprise Linux (RHEL) abgeleitet wurde.

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Image-Galerie](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Gewusst wie: Vorbereiten eines benutzerdefinierten CentOS-basierten virtuellen Computers für Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-centos/)
4. [Blog: Gewusst wie: Bereitstellen eines CentOS-VM-Images aus OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Gewusst wie: Installieren von Apache Qpid Proton-C für AMQP und Service Bus](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Images: Apache 2.2.15 auf OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Images: Drupal 7.2, LAMP Server auf OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

#### SUSE Enterprise Linux und OpenSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Gewusst wie: Installieren und Ausführen von MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
12. [Gewusst wie: Vorbereiten einer benutzerdefinierten SLES- oder openSUSE-VM](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/)  
13. [[SUSE-Forum] Gewusst wie: Wechseln zu einem neuen Patch-Server](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Images: SUSE Linux Enterprise Server for SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

#### CoreOS

CoreOS ist eine kleine, optimierte Verteilung für die reine Computingskalierung mit einem hohen Maß an Kontrolle hinsichtlich der Anpassung.

10. [Image-Galerie](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Gewusst wie: Verwenden von CoreOS auf Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
12. [Blog: TechEd Europa -- Windows-Docker-Client und Linux-Container](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
13. [Blog: Azure wird größer, schneller und offener](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
14. [Github: Schnellstart-Anleitung für die Bereitstellung von CoreOS in Azure](https://github.com/timfpark/coreos-azure)
15. [Github: Bereitstellen der Java-Anwendung mit Spring Boot, MongoDB und CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Vorbereiten eines virtuellen Oracle Linux-Computers für Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-oracle/)

#### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Ausführen von FreeBSD in Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: Einfache Bereitstellung von FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Bereitstellen eines benutzerdefinierten FreeBSD-Images](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Gewusst wie: Installieren des Azure Linux Agent](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
18. [Marketplace: Kaspersky AV für Linux-Dateiserver](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a id='basics'>Grundlagen</a>

1. [Grundlagen: Azure-Befehlszeilenschnitttstelle](http://azure.microsoft.com/documentation/articles/xplat-cli/)
4. [Grundlagen: Zertifikatverwendung und -verwaltung](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Grundlagen: Auswählen von Linux-Benutzernamen](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-usernames/)
6. [Grundlagen: Anmelden bei einer Linux-VM mit dem Azure-Portal](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-log-on/)
7. [Grundlagen: SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Grundlagen: Gewusst wie: Zurücksetzen eines Kennworts oder von SSH-Eigenschaften für Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Grundlagen: Verwenden von Root](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)
10. [Grundlagen: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)
11. [Grundlagen: Trennen eines Datenträgers von einem virtuellen Linux-Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-detach-disk/)
12. [Blog zu den Grundlagen: Optimieren von Speicher, Festplatten und Leistung mit Linux und Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Grundlagen: RAID](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/)
14. [Grundlagen: Erfassen eines virtuellen Linux-Computers zum Erstellen einer Vorlage](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/)
15. [Grundlagen: Azure Linux Agent](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
16. [Grundlagen: Azure-VM - Erweiterungen und Features](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Grundlagen: Einfügen benutzerdefinierter Daten in einen virtuellen Computer zur Verwendung mit Cloud-init](http://azure.microsoft.com/documentation/articles/virtual-machines-how-to-inject-custom-data/)
18. [Blog zu den Grundlagen: Erstellen von Linux auf Azure in 12 Schritten für Hochverfügbarkeit](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Blog zu den Grundlagen: Automatisieren der Bereitstellung von Linux auf Azure mit xplat, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [Grundlagen: Azure-Docker-VM-Erweiterung](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
23. Referenz zu [REST-API für Azure-Dienstverwaltung](https://msdn.microsoft.com/de-de/library/azure/ee460799.aspx)

### <a id='images'>Community-Images und -Repositorys</a>
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; für virtuelle Computerimages der Community.
4. [Github](https://github.com/Azure/) &mdash; für xplat-cli und viele andere Tools und Projekte.
5. [Docker-Hub-Registrierung](https://registry.hub.docker.com/) &mdash; (Registrierung für Docker-Container-Images).


### <a id='langsandplats'>Sprachen und Plattformen</a>
#### [Azure Java Dev Center](http://azure.microsoft.com/develop/java/)

1. [Images](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Gewusst wie: Verwenden von Service Bus aus Java mit AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Gewusst wie: Einrichten von Tomcat7 auf Linux mit dem Azure-Portal](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-setup-tomcat7-linux/)
4. [Video: Azure-Java-SDK für Dienstverwaltung](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog: Erste Schritte mit Azure-Verwaltungsbibliotheken für Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Github-Repository: Azure Toolkit for Eclipse with Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Referenz: Azure Toolkit for Eclipse with Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Github-Repository: MS Open Tech Tools plugin for IntelliJ IDEA and Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog: MSOpenTech-Beiträge zu OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Images: WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Images: WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Images: JDK6 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Images: JDK7 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Images: JDK8 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

#### JVM-Sprachen

1. [Scala: Ausführen von Play Framework-Anwendungen in Azure-Clouddiensten](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

#### SDK-Typen, Installationen, Upgrades
4. [Azure-Dienstverwaltungs-SDK: Java](http://dl.windowsazure.com/javadoc/)
5. [Azure-Dienstverwaltungs-SDK: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure-Dienstverwaltungs-SDK: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
- [Gewusst wie: Installieren von Ruby on Rails](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-rails-web-app-linux/)
- [Gewusst wie: Installieren von Ruby on Rails mit Capistrano, Nginx, Unicorn und PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
6. [Azure-Dienstverwaltungs-SDK: Python](https://github.com/Azure/azure-sdk-for-python)
- [Gewusst wie: Django Hello World Web Application (Mac-Linux)](http://azure.microsoft.com/documentation/articles/virtual-machines-python-django-web-app-linux/)
7. [Azure-Dienstverwaltungs-SDK: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure-Dienstverwaltungs-SDK: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
- [Gewusst wie: Installieren des LAMP-Stapels auf einem virtuellen Azure-Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
- [Video: Installieren eines LAMP-Stapels auf einem virtuellen Azure-Computer](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure-Dienstverwaltungs-SDK: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog: Mono, ASP.NET 5, Linux und Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### <a id='samples'>Beispiele und Skripts</a>

1. [Github-Repository (Azure/Linux) von Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Video: Gewusst wie: Verschieben von lokalen USB-Daten unter Linux auf Azure mit **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Video: Zugreifen auf Linux-basierten GUI auf Azure im Browser mit fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Video: Freigegebener Speicher unter Linux mithilfe der Vorschau der Azure-Dateien - Teil 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Video: Einbeziehen von Linux-Geräten auf Azure mithilfe von Service Bus und Websites](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Video: Verbinden einer nativen Linux-basierten zwischengespeicherten Anwendung mit Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Video: Lastenausgleich für hoch verfügbare Linux-Dienste auf Azure: OpenLDAP und MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


### <a id='data'>Daten</a>

Dieser Abschnitt enthält Informationen zu mehreren verschiedenen Speicheransätzen und Technologien, einschließlich NoSQL, Relational und Big Data.

#### Nosql

1. [Blog: 8 NoSql-Datenbanken für Azure (Open-Source)](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
- [Slideshare (MSOpenTech): Erfahrungen mit CouchDb auf Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Blog: Ausführen von CouchDB-as-a-Service mit node.js, CORS und Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
- [Gewusst wie: Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](http://azure.microsoft.com/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/)
4. Cassandra
- [Gewusst wie: Ausführen von Cassandra mit Linux auf Azure und Zugreifen aus Node.js](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. Redis
- [Blog: Redis unter Windows im Azure Redis Cache-Dienst](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Blog: Neu: ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
- [Blog: RavenHQ jetzt in Azure Marketplace verfügbar](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

#### Big Data
2. Hadoop/Cloudera  
	- [Blog: Installieren von Hadoop auf Azure Linux-VMs](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Gewusst wie: Erste Schritte mit Hadoop und Hive mit HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-get-started/)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) - ein vollständig verwalteter Hadoop-Dienst auf Azure.

#### Relationale Daten
2. MySQL
- [Gewusst wie: Installieren und Ausführen von MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
- [Gewusst wie: Optimieren der Leistung von MySQL in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/)
- [Gewusst wie: MySQL-Cluster](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
- [Gewusst wie: Erstellen einer MySQL-Datenbank mit Marketplace](http://azure.microsoft.com/documentation/articles/store-php-create-mysql-database/)
- [Gewusst wie: Django und MySQL auf Azure-Websites mit Python und Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-python-ptvs-django-mysql/)
- [Gewusst wie: PHP und MySQL auf Azure-Websites mit WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/)
7. MariaDB
- [Gewusst wie: Erstellen eines Clusters mit mehreren Mastern von MariaDbs](http://azure.microsoft.com/documentation/articles/virtual-machines-mariadb-cluster/)
7. PostgreSQL
- [Gewusst wie: Installieren von Ruby on Rails mit Capistrano, Nginx, Unicorn und PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)


### <a id='security'>Authentifizierung und Verschlüsselung</a>

Authentifizierung und Verschlüsselung sind wichtige Themen in der Software-Entwicklung, und es gibt sehr viele Themen im Internet, die beschreiben, wie Sie angemessene Sicherheitsverfahren für beides erlernen und verwenden. Wir beschreiben einen Teil der grundlegenden Verwendung, um schnell Linux- und Open-Source-Arbeitsauslastungen verwenden sowie auf Tools verweisen zu können, mit denen Remote-Sicherheitsfeatures zurückgesetzt oder entfernt werden können.

4. [Grundlagen: Zertifikatverwendung und -verwaltung](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Grundlagen: SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Grundlagen: Gewusst wie: Zurücksetzen eines Kennworts oder von SSH-Eigenschaften für Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Grundlagen: Verwenden von Root](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)

### <a id='devops'>DevOps, Verwaltung und Optimierung</a>

Dieser Abschnitt beginnt mit einem Blogeintrag mit einer Reihe von Videos unter [Video: Virtuelle Computer in Azure: Verwenden von Chef, Puppet und Docker für die Verwaltung virtueller Linux-Computer](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Der Bereich "DevOps, Verwaltung und Optimierung" ist jedoch ziemlich umfangreich und verändert sich sehr schnell. Sie sollten die Liste unten also als Ausgangspunkt betrachten.

1. Docker
	- [Docker-VM-Erweiterung für Linux in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
	- [Verwenden der Docker-VM-Erweiterung aus der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli)](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)
	- [Verwenden der Docker-VM-Erweiterung aus dem Azure-Vorschauportal](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)
	- [Schneller Einstieg in Docker in Azure Marketplace](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/)
2. [Fleet mit CoreOS](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
3. Deis
	- [GitHub-Repository:
Installieren von Deis auf einem CoreOS-Cluster in Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins und Hudson
	- [Blog: Jenkins Slave-Plug-in für Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub-Repository: Jenkins Speicher-Plug-in für Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Drittanbieter: Hudson Slave-Plug-in für Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Drittanbieter: Hudson Speicher-Plug-in für Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef und virtuelle Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-install-chef-client/)
	- [Video: Was ist Chef, und wie funktioniert es?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure-Automatisierung
	- [Video: Gewusst wie: Verwenden von Azure-Automatisierung mit virtuellen Linux-Computern](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC für Linux
    - [Blog: Gewusst wie: Powershell DSC für Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [Github: Docker Client DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)

### <a id='supportdebug'>Support, Problembehandlung und weitergehende Problembehandlung</a>

1. Microsoft-Support-Dokumentation
	- [Support: Support für Linux-Images in Microsoft Azure](http://support2.microsoft.com/kb/2941892)


<!--Anchors-->
[Verteilungen]: #distros
[Grundlagen]: #basics
[Community-Images und Repositorys]: #images
[Sprachen und Plattformen]: #langsandplats
[Beispiele und Skripts]: #samples
[Authentifizierung und Verschlüsselung]: #security
[DevOps, Verwaltung und Optimierung]: #devops
[Support, Problembehandlung und weitergehende Problembehandlung]: #supportdebug

<!--HONumber=45--> 
