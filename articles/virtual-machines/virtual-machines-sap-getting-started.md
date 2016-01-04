<properties
   pageTitle="Verwenden von SAP auf Azure Virtual Machines (VMs) | Microsoft Azure"
   description="Verwenden von SAP auf Azure Virtual Machines (VMs)"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="sedusch"/>
   
# Verwenden von SAP auf Azure Virtual Machines (VMs)

Cloud Computing ist ein häufig verwendeter Begriff, der in der IT-Branche mehr und mehr an Bedeutung gewinnt – in kleinen Unternehmen ebenso wie in großen und multinationalen Konzernen. Microsoft Azure ist die Plattform für Clouddienste von Microsoft, die ein umfangreiches Spektrum an neuen Möglichkeiten bietet. Nun können Kunden Anwendungen schnell als Clouddienste bereitstellen bzw. die Bereitstellung aufheben und sind nicht mehr durch technische oder budgetbezogene Aspekte eingeschränkt. Anstatt Zeit und Geld in die Hardwareinfrastruktur zu investieren, können sich Unternehmen auf die Anwendung, Geschäftsprozesse und ihre Vorteile für Kunden und Benutzer konzentrieren.

Mit den Microsoft Azure Virtual Machines-Diensten bietet Microsoft eine umfassende IaaS-Plattform (Infrastructure-as-a-Service). Auf SAP NetWeaver basierende Anwendungen werden auf Azure Virtual Machines (IaaS) unterstützt. Die folgende Whitepaper beschreiben, wie auf SAP NetWeaver basierende Anwendungen in Microsoft Azure als Plattform der Wahl geplant und implementiert werden.

## Planung und Implementierung

Titel: SAP NetWeaver in Azure Virtual Machines – Planungs- und Implementierungshandbuch

Zusammenfassung: Mit diesem Whitepaper sollten Sie beginnen, wenn Sie planen, SAP NetWeaver in Azure Virtual Machines zu verwenden. Mithilfe dieses Planungs- und Implementierungshandbuchs können Sie bewerten, ob ein vorhandenes oder geplantes SAP NetWeaver-basiertes System in einer Azure Virtual Machines-Umgebung bereitgestellt werden kann. Es beschreibt mehrere SAP NetWeaver-Bereitstellungsszenarien und enthält SAP-Konfigurationen, die nur für Azure gelten. Im Whitepaper werden alle erforderlichen Konfigurationsinformationen aufgelistet und beschrieben, die Sie in SAP bzw. Azure benötigen, um eine hybride SAP-Umgebung auszuführen. Zudem werden Maßnahmen erläutert, die Sie ergreifen können, um eine hohe Verfügbarkeit von SAP NetWeaver-basierten Systemen auf IaaS sicherzustellen.

Aktualisierung: August 2015

[Dieses Handbuch jetzt herunterladen](http://go.microsoft.com/fwlink/?LinkId=397963)
## Bereitstellung
Titel: SAP NetWeaver auf Azure Virtual Machines – Bereitstellungshandbuch

Zusammenfassung: Dieses Dokument enthält Schrittanleitungen für die Bereitstellung von SAP NetWeaver-Software auf virtuellen Computern in Azure. Schwerpunkt dieses Dokuments sind drei Bereitstellungsszenarien. Das Hauptaugenmerk liegt dabei auf der Aktivierung der Azure-Überwachungserweiterungen für SAP, einschließlich der Empfehlungen zur Problembehandlung für die Azure-Überwachungserweiterungen für SAP. Dieses Dokument setzt voraus, dass Sie das Planungs- und Implementierungshandbuch gelesen haben.

Aktualisierung: September 2015

[Dieses Handbuch jetzt herunterladen](http://go.microsoft.com/fwlink/?LinkId=397964)

## SAP DBMS in Azure
Titel: SAP DBMS in Azure – Bereitstellungshandbuch

Zusammenfassung: Dieses Whitepaper behandelt Aspekte der Planung und Implementierung für die DBMS-Systeme, die in Verbindung mit SAP ausgeführt werden sollen. Im ersten Teil werden allgemeine Informationen aufgelistet und vorgestellt. Die weiteren Teile des Dokuments erläutern Bereitstellungen unterschiedlicher DBMS in Azure, die von SAP unterstützt werden. Als DBMS werden SQL Server, SAP ASE und Oracle vorgestellt. In diesen speziellen Teilen werden Überlegungen erläutert, die Sie berücksichtigen müssen, wenn Sie SAP-Systeme in Azure in Verbindung mit einem dieser DBMS verwenden. Themen wie Methoden für Sicherungen und hohe Verfügbarkeit, die von den unterschiedlichen DBMS in Azure unterstützt werden, werden für die Nutzung mit SAP-Anwendungen vorgestellt.

Aktualisierung: August 2015

[Dieses Handbuch jetzt herunterladen](http://go.microsoft.com/fwlink/?LinkId=397965)

## SAP NetWeaver in Azure

Titel: SAP NetWeaver – Erstellen einer auf Azure basierenden Lösung für die Notfallwiederherstellung

Zusammenfassung: Dieses Dokument enthält eine Schrittanleitung zum Erstellen einer auf Azure basierenden Lösung für die Notfallwiederherstellung für SAP NetWeaver. Für die beschriebene Lösung wird davon ausgegangen, dass die SAP-Umgebung virtualisiert und lokal basierend auf Hyper-V ausgeführt wird. Im ersten Teil des Dokuments werden die Komponenten von Azure Site Recovery-Diensten (ASR) vorgestellt. Im zweiten Teil des Dokuments geht es um die Besonderheiten SAP NetWeaver-basierter Umgebungen. Hier werden Einsatzmöglichkeiten für ASR mit SAP NetWeaver-Anwendungsinstanzen und SAP Central Services erläutert und beschrieben. Ein Schwerpunkt des zweiten Teils ist die Nutzung von ASR für SAP Central Services, die mit Windows Server-Failoverclusterkonfigurationen geschützt werden.

Aktualisierung: September 2015

[Dieses Handbuch jetzt herunterladen](http://go.microsoft.com/fwlink/?LinkID=521971)

## SAP NetWeaver in Azure – HA

Titel: SAP NetWeaver in Azure – Clustering von SAP ASCS/SCS-Instanzen mithilfe von Windows Server-Failoverclustern in Azure mit SIOS DataKeeper

Zusammenfassung: In diesem Dokument wird beschrieben, wie SIOS DataKeeper verwendet wird, um eine hoch verfügbare SAP ASCS/SCS-Konfiguration in Azure einzurichten. SAP schützt die Single Point of Failure-Komponenten wie SAP ASCS/SCS oder Enqueue Replication Services mit Windows Server-Failoverclusterkonfigurationen, die freigegebene Datenträger erfordern. Diese SAP-Komponenten sind für die Funktionalität eines SAP-Systems unverzichtbar. Aus diesem Grund müssen Funktionen für hohe Verfügbarkeit eingerichtet sein, um sicherzustellen, dass diese Komponenten einen Ausfall eines Servers oder eines virtuellen Computers tolerieren – z. B. mit Windows-Clusterkonfigurationen für Bare-Metal- und Hyper-V-Umgebungen. Seit August 2015 kann Azure selbst keine freigegebenen Datenträger bereitstellen, die für die Windows-basierten hoch verfügbaren Konfigurationen für diese kritischen SAP-Komponenten erforderlich wären. Jedoch können mit dem Produkt DataKeeper von SIOS auf der Azure IaaS-Plattform Windows Server-Failoverclusterkonfigurationen erstellt werden, wie sie für SAP ASCS/SCS erforderlich sind. Dieses Whitepaper beschreibt die einzelnen Schritte zur Installation einer Windows Server-Failoverclusterkonfiguration mit freigegebenen Datenträgern, die von SIOS Datakeeper in Azure bereitgestellt werden. Im Whitepaper werden Konfigurationsdetails in Azure, Windows und SAP erläutert, die für eine optimale Funktionsweise der hoch verfügbaren Konfiguration sorgen. Das Dokument ergänzt die SAP-Installationsdokumentation und die SAP-Hinweise, die die primäre Ressource für Installationen und Bereitstellungen von SAP-Software auf verschiedenen Plattformen darstellen.

Aktualisierung: August 2015

[Dieses Handbuch jetzt herunterladen](http://go.microsoft.com/fwlink/?LinkId=613056)

<!---HONumber=AcomDC_1125_2015-->