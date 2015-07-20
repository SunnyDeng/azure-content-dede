<properties 
	pageTitle="Bereitstellen zwischen lokalen VMware-Standorten" 
	description="InMage Scout in Azure Site Recovery verarbeitet die Replikation, das Failover und die Wiederherstellung zwischen lokalen VMware-Standorten." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# Bereitstellen zwischen lokalen VMware-Standorten


##Übersicht

InMage Scout in Azure Site Recovery ermöglicht eine Echtzeitreplikation zwischen lokalen VMware-Standorten. InMage Scout ist Teil der Abonnements des Azure Site Recovery-Diensts.


## Voraussetzungen

- **Azure-Konto**: Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung.


##Schritt 1: Erstellen eines Tresors und Herunterladen von InMage Scout

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com) an.
2. Klicken Sie auf **Data Services** > **Recovery Services** > **Site Recovery-Tresor**.
3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.
4. Geben Sie unter **Name** einen aussagekräftigen Anzeigenamen für den Tresor ein.
5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](pricing/details/site-recovery/) unter „Geografische Verfügbarkeit“.

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>Aktiv</b> angegeben.</P>

##Schritt 2: Konfigurieren des Tresors

1. Klicken Sie auf **Tresor erstellen**.
2. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen.
3. Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen VMware-Standorten** aus.
4. Laden Sie InMage Scout herunter.
5. Richten Sie die Replikation zwischen zwei VMware-Standorten gemäß der InMage Scout-Dokumentation ein, die zusammen mit dem Produkt heruntergeladen wird.


##Nächste Schritte

Etwaige Fragen können Sie im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.<

<!---HONumber=July15_HO2-->