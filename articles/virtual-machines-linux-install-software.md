<properties linkid="manage-linux-commontasks-install-software" urlDisplayName="Install software on VM" pageTitle="Install software on a Linux virtual machine - Azure" metaKeywords="" description="Learn how to install software on your Linux virtual machine in Azure by using CentOS/Red Hat or Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install software on your Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Installieren von Software auf dem virtuellen Linux-Computer in Azure
====================================================================

Bei Linux-Distributionen wird Software in der Regel mithilfe von Softwarepaketen installiert. Diese Pakete werden normalerweise mit verschiedenen Befehlen wie `apt` oder `yum` verwaltet. Sie können Programme auch ohne Paket installieren, z. B. mit einem *tarball* des Quellcodes.

Im Folgenden wird die Verwendung der Paketmanager für einige verbreitete Linux-Distributionen erläutert. Die jeweiligen Schritte hängen von der verwendeten Linux-Distribution ab.

**Hinweis:** Je nach der Einrichtung Ihrer Umgebung werden zum Ausführen dieser Befehle eventuell Rootberechtigungen (über `sudo`) benötigt.

CentOS/Red Hat
--------------

CentOS verwendet `yum` für die Paketverwaltung. Mit diesem Tool können Sie Pakete installieren sowie installierte Pakete deinstallieren, aktualisieren, auflisten usw. Die Syntax dieser Befehle ist unten dargestellt.

### Installation

Mit dem folgenden Befehl werden ein Paket sowie alle Pakete, von denen es abhängig ist, installiert. Aufgrund von Abhängigkeiten wird u. U. mehr als ein Paket installiert.

    yum install [Paketname]

### Deinstallation

Mit dem folgenden Befehl wird ein Paket von Ihrem Computer deinstalliert. Denken Sie daran, dass keine Abhängigkeiten entfernt werden.

    yum remove [Paketname]

### Aktualisieren

Mit dem folgenden Paket wird die neueste Version installiert. Das Paket muss installiert sein, bevor Sie es aktualisieren können.

    yum update [Paketname]

### Auflisten installierter Pakete

Mit dem folgenden Befehl wird eine Liste aller installierten Pakete auf Ihrem Computer angezeigt.

    yum list installed

Ubuntu
------

In Ubuntu gibt es das Tool `apt` (Advanced Packaging Tool) für die Paketverwaltung. Mit diesem Tool können Sie Pakete installieren sowie installierte Pakete deinstallieren, aktualisieren, auflisten usw. Die Syntax dieser Befehle ist unten dargestellt.

### Installation

Mit dem folgenden Befehl werden ein Paket sowie alle Pakete, von denen es abhängig ist, installiert. Aufgrund von Abhängigkeiten wird u. U. mehr als ein Paket installiert.

    apt-get install [Paketname]

### Deinstallation

Mit dem folgenden Befehl wird ein Paket von Ihrem Computer deinstalliert. Denken Sie daran, dass keine Abhängigkeiten entfernt werden.

    apt-get remove [Paketname]

### Aktualisieren/Upgrade

Für ein Upgrade auf eine neue Version müssen Sie zwei Befehle eingeben: einen zur Aktualisierung des Paketindex und einen weiteren für das Upgrade der Pakete. Führen Sie den folgenden Befehl aus, um den Paketindex zu aktualisieren:

    apt-get update

Wenn der Paketindex aktualisiert ist, führen Sie den folgenden Befehl für ein Upgrade der Pakete aus:

    apt-get upgrade
