<properties
   pageTitle="Updateinfrastruktur für Red Hat Enterprise Linux-Images| Microsoft Azure"
   description="Einführung in den yum-Updatedienst für eine bedarfsgesteuerte Red Hat Enterprise Linux-Instanz in Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# Updateinfrastruktur für Red Hat Enterprise Linux-Images

Durch Verwendung von Red Hat Update Infrastructure (RHUI) in Azure können Sie yum-Repositoryinhalte für Azure Red Hat Enterprise Linux (RHEL)-Images verwalten. Die bedarfsgesteuerten RHEL-Instanzen, die Sie über den Azure Marketplace erstellt haben, haben dann Zugriff auf das regionale yum-Repository. So können RHEL-Instanzen inkrementell aktualisiert werden.

Die von RHUI verwaltete yum-Repositoryliste wird während der Bereitstellung in der RHEL-Instanz konfiguriert. Daher müssen Sie keine zusätzliche Konfiguration vornehmen. Führen Sie einfach `yum update` aus, wenn die RHEL-Instanz ausgeführt wird.

## RHUI – Übersicht
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) bietet eine hoch skalierbare Lösung zum Verwalten von yum-Repositoryinhalten für Red Hat Enterprise Linux-Cloudinstanzen, die von für Red Hat zertifizierten Cloudanbietern gehostet werden. RHUI basiert auf dem Pulp-Upstreamprojekt und ermöglicht Cloudanbietern, die in Red Hat gehosteten Repositoryinhalte lokal zu spiegeln, benutzerdefinierte Repositorys mit eigenen Inhalten zu erstellen und diese Repositorys über ein Inhaltsübermittlungssystem mit Lastenausgleich einer großen Gruppe von Endbenutzern zur Verfügung zu stellen.

## Regionen, in denen RHUI bereitgestellt wird
RHUI wird in allen öffentlichen Regionen bereitgestellt, die im [Dashboard „Azure-Status“](https://azure.microsoft.com/status/) aufgeführt sind. Das bedeutet, dass Sie den yum-Updatedienst in diesen Regionen ohne zusätzliche Gebühren erhalten können. Diese Informationen werden künftig aktualisiert.

## Abrufen von Updates aus einem lokalen Updaterepository (z. B. Red Hat Network Satellite)

Um Updates aus einem lokalen Updaterepository zu erhalten, benötigen Sie eine Lizenz für Red Hat Cloud Access und ein Red Hat-Abonnement für Azure.

Dann müssen Sie die Registrierung von RHUI aufheben und anschließend RHUI für Ihre lokale Updateinfrastruktur neu registrieren. Es folgen die entsprechenden Schritte im Detail.

1.	Bearbeiten Sie „/etc/yum.repos.d/rh-cloud.repo“, und ändern Sie alle Vorkommen von `enabled=1` in `enabled=0`. Beispiel:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Bearbeiten Sie „/etc/yum/pluginconf.d/rhnplugin.conf“, und ändern Sie `enabled=0` in `enabled=1`.
3.	Führen Sie dann die Registrierung für Red Hat Network (RHN) durch.

        rhn_register

    oder

        rhnreg_ks


> [AZURE.NOTE] Für ausgehende Datenübertragungen fallen Gebühren an (siehe [Preisdetails](http://azure.microsoft.com/pricing/details/data-transfers/)). Wir empfehlen die Verwendung der RHUI-Standardversion, sodass Sie inkrementelle Updates erhalten, ohne dass zusätzliche Gebühren anfallen.

## Nächste Schritte
Nachdem Sie sich mit der Verwendung von RHUI in Azure vertraut gemacht haben, können Sie nun ein RHEL-Image über den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) erstellen und `yum update` in Ihrer RHEL-Instanz verwenden.

<!---HONumber=AcomDC_0224_2016-->