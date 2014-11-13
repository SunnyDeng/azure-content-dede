<properties urlDisplayName="Install MongoDB" pageTitle="Installieren von MongoDB auf einem virtuellen Computer, auf dem CentOS Linux in Azure ausgef&uuml;hrt wird" metaKeywords="Azure, MongoDB" description="Erfahren Sie, wie Sie MongoDB auf einen virtuellen Computer in Azure installieren k&ouml;nnen." metaCanonical="" services="" documentationCenter="" title="Installieren von MongoDB auf einem virtuellen Computer, auf dem CentOS Linux in Azure ausgef&uuml;hrt wird" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# Installieren von MongoDB auf einem virtuellen Computer, auf dem CentOS Linux in Azure ausgeführt wird

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Mithilfe des [Azure-Verwaltungsportals][Azure-Verwaltungsportals] können Sie einen virtuellen Computer erstellen, auf dem CentOS Linux aus der Image-Galerie ausgeführt wird. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

Sie erhalten Informationen zu folgenden Themen:

-   Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers, auf dem CentOS Linux aus der Galerie ausgeführt wird
-   Verbinden des virtuellen Computers über SSH oder PuTTY
-   Installieren von MongoDB auf dem virtuellen Computer

## Erstellen eines virtuellen Computers, auf dem CentOS Linux ausgeführt wird

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Linux-Computer erstellen und eine Remoteverbindung über SSH oder PuTTY herstellen. Sie haben auch erfahren, wie Sie MongoDB auf dem virtuellen Linux-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation][MongoDB-Dokumentation].

  [MongoDB]: http://www.mongodb.org/
  [Azure-Verwaltungsportals]: http://manage.windowsazure.com
  [MongoDB-Dokumentation]: http://www.mongodb.org/display/DOCS/Home
