<properties
	pageTitle="Linux und Open-Source-Computing auf Azure | Microsoft Azure"
	description="Dieses Thema enthält eine Liste von Artikeln zu Linux- und Open Source-Computing auf Azure, u. a. die grundlegende Verwendung von Linux, einige grundlegende Konzepte zum Ausführen oder Hochladen von Linux-Images auf Azure und anderen Inhalten zu bestimmten Technologien und Optimierungen."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="09/02/2015"
	ms.author="rasquill"/>



# Linux und Open-Source-Computing auf Azure

Dieses Dokument versucht, an einem Ort alle Themen aufzulisten, die von Microsoft und seinen Partnern über die Ausführung von Linux-basierten virtuellen Maschinen und anderen Open-Source-Computing-Umgebungen und Anwendungen auf Microsoft Azure erstellt wurden. Da sowohl Azure als auch das Open-Source-Computing einer ständigen Weiterentwicklung unterliegen ist dieses Dokument vermutlich nicht mehr auf dem aktuellen Stand. *Trotz* dieser Tatsache geben wir unser Bestes, kontinuierlich neue Themen hinzuzufügen und veraltete Einträge zu entfernen. Wenn wir ein Thema vergessen haben, senden Sie uns einen Kommentar, oder übermitteln Sie eine Pull-Anforderung an das [Github-Repository](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In diesem Artikel werden Themen ohne Berücksichtigung des Bereitstellungsmodells aufgeführt.

## Allgemeine Hinweise
Die Abschnitte werden am rechten Rand dieser Seite aufgeschlüsselt. (Links können in mehr als einem Abschnitt auftreten, da Themen sich mit mehr als einem Konzept, mehr als einer Distribution oder mehr als einer Technologie beschäftigen können.) Darüber hinaus gibt es mehrere Themen, in denen verschiedene Linux-Optionen, Image-Repositorys, Fallstudien und Gewusst-wie-Themen zum Hochladen Ihrer eigenen benutzerdefinierten Images beschrieben werden:

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Veranstaltungen und Demonstrationen: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Gewusst wie: Hochladen Ihres eigenen Distro-Images](virtual-machines-linux-create-upload-vhd.md) (plus Anweisungen zum Verwenden einer [unterstützten Azure-Verteilung](virtual-machines-linux-endorsed-distributions.md))
- [Hinweise: Allgemeine Linux-Anforderungen für die Ausführung in Azure](virtual-machines-linux-create-upload-vhd-generic.md)
- [Hinweise: Allgemeine Einführung zu Linux auf Azure](virtual-machines-linux-introduction.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Distributionen

Es gibt Unmengen von Linux-Verteilungen, die in der Regel durch die Paket-Verwaltungssysteme unterteilt sind. Einige sind dpgk-basiert, wie Debian und Ubuntu, andere sind rpm-basiert, wie CentOS, SUSE und RedHat. Einige Unternehmen bieten Distributions-Images als formale Partner von Microsoft. Diese werden unterstützt. Andere werden von der Community bereitgestellt. Für die Distributionen in diesem Abschnitt gibt es formale Artikel, selbst wenn sie nur in Beispielen für andere Technologien verwendet wurden.

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu ist eine äußerst populäre und von Azure unterstützte Linux-Verteilung, die auf der dkpg- und apt-get-Paketverwaltung basiert.

1. [Gewusst wie: Hochladen Ihres eigenen Ubuntu-Images](virtual-machines-linux-create-upload-vhd-ubuntu.md)
2. [Gewusst wie: Ubuntu-LAMP-Stack](virtual-machines-linux-install-lamp-stack.md)
2. [Images: LAPP-Stack](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Gewusst wie: MySQL-Cluster](virtual-machines-linux-mysql-cluster.md)
4. [Gewusst wie: Node.js und Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)
5. [Gewusst wie: IPython Notebook](virtual-machines-python-ipython-notebook.md)
6. [Ausführen von ASP.NET 5 auf Linux mithilfe von Docker-Containern](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Images: Redis-Server](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Images: Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Images: Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Images: Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian ist eine wichtige Verteilung für Linux und Open-Source basierend auf der dpgk- und apt-get-Paketverwaltung. Das MSOpenTech VM Depot bietet verschiedene Images.

### CentOS

Die CentOS-Linux-Distribution ist eine stabile, vorhersehbare, verwaltbare und reproduzierbare Plattform, die aus den Quellen von Red Hat Enterprise Linux (RHEL) abgeleitet wurde.

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Image-Katalog](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Gewusst wie: Vorbereiten eines benutzerdefinierten CentOS-basierten virtuellen Computers für Azure](virtual-machines-linux-create-upload-vhd-centos.md)
4. [Blog: Bereitstellen eines CentOS-VM-Images aus OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Gewusst wie: Installieren von Apache Qpid Proton-C für AMQP und Service Bus](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Images: Apache 2.2.15 auf OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Images: Drupal 7.2, LAMP-Server auf OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Linux Enterprise Server und openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Gewusst wie: Installieren und Ausführen von MySQL](virtual-machines-linux-mysql-use-opensuse.md)
12. [Gewusst wie: Vorbereiten einer benutzerdefinierten SLES- oder openSUSE-VM](virtual-machines-linux-create-upload-vhd-suse.md)  
13. [[SUSE-Forum] How to: Move to a New Patch Server (Wechseln zu einem neuen Patchserver, in englischer Sprache)](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Images: SUSE Linux Enterprise Server für SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS ist eine kleine, optimierte Distribution für die reine Computingskalierung mit einem hohen Maß an Kontrolle hinsichtlich der Anpassung.

10. [Image-Katalog](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Gewusst wie: Verwenden von CoreOS in Azure](virtual-machines-linux-coreos-how-to.md)
12. [Gewusst wie: Erste Schritte mit Fleet und Docker unter CoreOS in Azure](virtual-machines-linux-coreos-fleet-get-started.md)
13. [Blog: TechEd Europe – Windows-Docker-Client und Linux-Container](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
14. [Blog: Azure wird größer, schneller und offener](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
15. [GitHub: Schnellstart-Anleitung für die Bereitstellung von CoreOS in Azure](https://github.com/timfpark/coreos-azure)
16. [GitHub: Bereitstellen der Java-Anwendung mit Spring Boot, MongoDB und CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Vorbereiten eines virtuellen Oracle Linux-Computers für Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Ausführen von FreeBSD in Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: Einfache Bereitstellung von FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Bereitstellen eines benutzerdefinierten FreeBSD-Images](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Gewusst wie: Installieren des Azure Linux-Agents](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace: Kaspersky AV für Linux-Dateiserver](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Grundlagen

1. [Grundlagen: Azure-Befehlszeilenschnittstelle (Azure-CLI)](../xplat-cli-install.md)
4. [Grundlagen: Zertifikatverwendung und -verwaltung](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Grundlagen: Auswählen von Linux-Benutzernamen](virtual-machines-linux-usernames.md)
6. [Grundlagen: Anmelden bei einem virtuellen Linux-Computer über das Azure-Portal](virtual-machines-linux-how-to-log-on.md)
7. [Grundlagen: SSH](virtual-machines-linux-use-ssh-key.md)
8. [Grundlagen: Zurücksetzen von Kennwörtern oder SSH-Eigenschaften für Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Grundlagen: Verwenden von Root](virtual-machines-linux-use-root-privileges.md)
10. [Grundlagen: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](virtual-machines-linux-how-to-attach-disk.md)
11. [Grundlagen: Trennen eines Datenträgers von einem virtuellen Linux-Computer](virtual-machines-linux-how-to-detach-disk.md)
12. [Blog zu den Grundlagen: Optimieren von Speicher, Festplatten und Leistung mit Linux und Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Grundlagen: RAID](virtual-machines-linux-configure-raid.md)
14. [Grundlagen: Erfassen eines virtuellen Linux-Computers zum Erstellen einer Vorlage](virtual-machines-linux-capture-image.md)
15. [Grundlagen: Der Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md)
16. [Grundlagen: Azure-VM-Erweiterungen und -Features](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Grundlagen: Einfügen benutzerdefinierter Daten in einen virtuellen Computer zur Verwendung mit "Cloud-init"](virtual-machines-how-to-inject-custom-data.md)
18. [Blog zu den Grundlagen: Erstellen von Linux auf Azure in zwölf Schritten für Hochverfügbarkeit](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Blog zu den Grundlagen: Automatisieren der Bereitstellung von Linux auf Azure mit der Azure-CLI, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [Erstellen einer Multi-VM-Bereitstellung über die Azure-Befehlszeilenschnittstelle](virtual-machines-create-multi-vm-deployment-../xplat-cli-install.md)
20. [Grundlagen: Die Docker-VM-Erweiterung von Azure](virtual-machines-docker-vm-extension.md)
23. [Azure-Dienstverwaltungs-REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) – Referenz
24. [GlusterFS auf Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Community-Images und Repositorys
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &ndash; für von der Community bereitgestellte VM-Images.
4. [GitHub](https://github.com/Azure/) &ndash; für die Azure-Befehlszeilenschnittstelle und viele weitere Tools und Projekte.
5. [Docker-Hub-Registrierung](https://registry.hub.docker.com/) &ndash; die Registrierung für Docker-Container-Images.

## Sprachen und Plattformen
### [Azure Java Dev Center](http://azure.microsoft.com/develop/java/)

1. [Images](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Gewusst wie: Verwenden von Service Bus aus Java mit AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Gewusst wie: Einrichten von Tomcat7 auf Linux mit dem Azure-Portal](virtual-machines-linux-setup-tomcat7-linux.md)
4. [Video: Azure-Java-SDK für die Dienstverwaltung](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog: Erste Schritte mit Azure-Verwaltungsbibliotheken für Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [GitHub-Repository: Azure Toolkit für Eclipse mit Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Referenz: Azure Toolkit für Eclipse mit Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [GitHub-Repository: MS Open Tech-Tools-Plug-In für IntelliJ IDEA und Android-Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog: MSOpenTech-Beiträge zu OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Images: WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Images: WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Images: JDK6 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Images: JDK7 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Images: JDK8 unter Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

### JVM-Sprachen

1. [Scala: Ausführen von Play Framework-Anwendungen in Azure-Clouddiensten](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### SDK-Typen, Installationen, Upgrades
4. [Azure-Dienstverwaltungs-SDK: Java](http://dl.windowsazure.com/javadoc/)
5. [Azure-Dienstverwaltungs-SDK: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure-Dienstverwaltungs-SDK: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Gewusst wie: Installieren von Ruby on Rails](virtual-machines-ruby-rails-web-app-linux.md)
6. [Azure-Dienstverwaltungs-SDK: Python](https://github.com/Azure/azure-sdk-for-python)
    - [Gewusst wie: Django-Webanwendung "Hello World" (Mac, Linux)](virtual-machines-python-django-web-app-linux.md)
7. [Azure-Dienstverwaltungs-SDK: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure-Dienstverwaltungs-SDK: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Gewusst wie: Installieren des LAMP-Stacks auf einem virtuellen Azure-Computer](virtual-machines-linux-install-lamp-stack.md)
    - [Video: Installieren eines LAMP-Stacks auf einem virtuellen Azure-Computer](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure-Dienstverwaltungs-SDK: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog: Mono, ASP.NET 5, Linux und Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Beispiele und Skripts

Es ist geplant, diesen Abschnitt in Kürze mit Inhalt zu füllen. Wenn Sie Vorschläge haben, senden Sie uns eine Pull-Anforderung, oder hinterlassen Sie nachstehend einen Kommentar.

1. [Erstellen einer Multi-VM-Bereitstellung über die Azure-Befehlszeilenschnittstelle](virtual-machines-create-multi-vm-deployment-../xplat-cli-install.md)
2. [Azure Linux-Github-Repository von Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Video: Verschieben von lokalen USB-Daten unter Linux auf Azure mit **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Video: Zugreifen auf Linux-basierten GUI auf Azure im Browser mit fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Video: Freigegebener Speicher unter Linux mithilfe der Vorschau der Azure-Dateien – Teil 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Video: Einbeziehen von Linux-Geräten auf Azure mithilfe von Service Bus und Websites](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Video: Verbinden einer nativen Linux-basierten zwischengespeicherten Anwendung mit Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Video: Lastenausgleich für hoch verfügbare Linux-Dienste auf Azure: OpenLDAP und MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Data

Dieser Abschnitt enthält Informationen zu mehreren verschiedenen Speicheransätzen und Technologien, einschließlich NoSQL, Relational und Big Data.

### NoSQL

1. [Blog: 8 NoSql-Datenbanken für Azure (Open-Source)](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): Erfahrungen mit CouchDb auf Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog:Ausführen von CouchDB-as-a-Service mit node.js, CORS und Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Gewusst wie: Erstellen einer "Node.js"-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](store-mongolab-web-sites-nodejs-store-data-mongodb.md)
4. Cassandra
    - [Gewusst wie: Ausführen von Cassandra mit Linux auf Azure und Zugreifen aus Node.js](virtual-machines-linux-nodejs-running-cassandra.md)
5. Redis
    - [Blog: Redis unter Windows im Azure Redis Cache-Dienst](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog: Ankündigung des ASP.NET-Sitzungsstatusanbieters für Redis-Vorschauversion](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog: RavenHQ jetzt in Azure Marketplace verfügbar](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
2. Hadoop/Cloudera  
	- [Blog: Installieren von Hadoop auf Azure Linux-VMs](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Gewusst wie: Erste Schritte mit Hadoop und Hive mit HDInsight](hdinsight-get-started.md)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) – ein vollständig verwalteter Hadoop-Dienst auf Azure.

### Relationale Datenbank
2. MySQL
    - [Gewusst wie: Installieren und Ausführen von MySQL](virtual-machines-linux-mysql-use-opensuse.md)
    - [Gewusst wie: Optimieren der Leistung von MySQL in Azure](virtual-machines-linux-optimize-mysql-perf.md)
    - [Gewusst wie: MySQL-Cluster](virtual-machines-linux-mysql-cluster.md)
    - [Gewusst wie: Erstellen einer MySQL-Datenbank mit Marketplace](store-php-create-mysql-database.md)
    - [Gewusst wie: Django und MySQL auf Azure-Websites mit Python und Visual Studio](web-sites-python-ptvs-django-mysql.md)
    - [Gewusst wie: PHP und MySQL auf Azure-Websites mit WebMatrix](web-sites-php-mysql-use-webmatrix.md)
    - [Hoch verfügbare MySQL-Architektur in Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Gewusst wie: Erstellen eines Clusters mit mehreren Mastern von MariaDbs](virtual-machines-mariadb-cluster.md)
8. [Installieren von Postgres mit Corosync Pg\_bouncer mit ILB](https://github.com/chgeuer/postgres-azure)


## Authentifizierung und Verschlüsselung

Authentifizierung und Verschlüsselung sind wichtige Themen in der Software-Entwicklung, und es gibt sehr viele Themen im Internet, die beschreiben, wie Sie angemessene Sicherheitsverfahren für beides erlernen und verwenden. Wir beschreiben einen Teil der grundlegenden Verwendung, um schnell Linux- und Open-Source-Workloads verwenden sowie auf Tools verweisen zu können, mit denen Remote-Sicherheitsfeatures zurückgesetzt oder entfernt werden können. Hierbei handelt es sich um grundlegende Verfahren, es werden in Kürze komplexere Szenarien folgen.

4. [Grundlagen: Zertifikatverwendung und -verwaltung](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Grundlagen: SSH](virtual-machines-linux-use-ssh-key.md)
8. [Grundlagen: Zurücksetzen von Kennwörtern oder SSH-Eigenschaften für Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Grundlagen: Verwenden von Root](virtual-machines-linux-use-root-privileges.md)

## Linux HPC (High Performance Computing)

Führen Sie HPC-Workloads auf Linux-VM-Clustern aus, die mit Open-Source-Tools oder mit dem Microsoft HPC Pack erstellt wurden.

1.	[Schnellstartvorlage: Einrichten eines SLURM-Clusters](http://azure.microsoft.com/documentation/templates/slurm/) und [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
2.	[Schnellstartvorlage: Einrichten eines Torque-Clusters](http://azure.microsoft.com/documentation/templates/torque-cluster/)
3.	[Schnellstartvorlage: Erstellen eines HPC-Clusters mit Linux-Computeknoten](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Tutorial: Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md)
5.	[Tutorial: Ausführen von NAMD mit dem Microsoft HPC Pack auf Linux-Computeknoten in Azure](virtual-machines-linux-cluster-hpcpack-namd.md)
6.	[Tutorial: Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md)


## DevOps, Verwaltung und Optimierung

Dieser Abschnitt beginnt mit einem Blogeintrag mit einer Reihe von Videos unter [Video: Virtuelle Azure-Computer: Verwenden von Chef, Puppet und Docker für die Verwaltung virtueller Linux-Computer.](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Der Bereich "DevOps, Verwaltung und Optimierung" ist jedoch ziemlich umfangreich und verändert sich sehr schnell. Sie sollten die Liste unten also als Ausgangspunkt betrachten.

1. Docker
	- [Docker-VM-Erweiterung für Linux auf Azure](virtual-machines-docker-vm-extension.md)
	- [Verwenden der Docker-VM-Erweiterung aus der Azure-Befehlszeilenschnittstelle (Azure-CLI)](virtual-machines-docker-with-../xplat-cli-install.md)
	- [Verwenden der Docker-VM-Erweiterung aus dem Azure-Vorschauportal](virtual-machines-docker-with-portal.md)
	- [Schneller Einstieg in Docker in Azure Marketplace](virtual-machines-docker-ubuntu-quickstart.md)
	- [Verwenden von "docker-machine" auf Azure](virtual-machines-docker-machine.md)
	- [Verwenden von Docker mit Swarm auf Azure](virtual-machines-docker-swarm.md)
	- [Erste Schritte mit Docker und Compose auf einem virtuellen Azure-Computer](virtual-machines-docker-compose-quickstart.md)

2. [Fleet mit CoreOS](virtual-machines-linux-coreos-how-to.md)
3. Deis
	- [GitHub-Repository: Installieren von Deis auf einem CoreOS-Cluster in Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Vollständige Anleitung für die automatisierte Kubernetes- Clusterbereitstellung mit CoreOS und Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins und Hudson
	- [Blog: Jenkins Slave-Plug-In für Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub-Repository: Jenkins Speicher-Plug-In für Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Drittanbieter: Hudson Slave-Plug-In für Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Drittanbieter: Hudson Speicher-Plug-In für Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef und virtuelle Computer](virtual-machines-windows-install-chef-client.md)
	- [Video: Was ist Chef, und wie funktioniert es??](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure-Automatisierung
	- [Video: Verwenden von Azure-Automatisierung mit virtuellen Linux-Computern](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC für Linux
    - [Blog: Powershell DSC für Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Docker Client DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Packer-Plug-In für Azure](https://github.com/msopentech/packer-azure)

## Support, Problembehandlung und weitergehende Problembehandlung

1. Microsoft-Support-Dokumentation
	- [Unterstützung: Unterstützung für Linux-Images in Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=Oct15_HO2-->