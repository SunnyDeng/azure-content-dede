<properties
	pageTitle="Einrichten von Schutz zwischen lokalen VMware-Standorten"
	description="Verwenden Sie diesen Artikel, um Schutz zwischen zwei VMware-Standorten mithilfe von Azure Site Recovery zu konfigurieren."
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
	ms.date="12/03/2015"
	ms.author="raynew"/>


# Einrichten von Schutz zwischen lokalen VMware-Standorten


## Übersicht

InMage Scout in Azure Site Recovery ermöglicht eine Echtzeitreplikation zwischen lokalen VMware-Standorten. InMage Scout ist Teil der Abonnements des Azure Site Recovery-Diensts.


## Voraussetzungen

- **Azure-Konto**: Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung.


## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com) an.
2. Klicken Sie auf **Data Services** > **Recovery Services** > **Site Recovery-Tresor**.
3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.
4. Geben Sie unter **Name** einen aussagekräftigen Anzeigenamen für den Tresor ein.
5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](pricing/details/site-recovery/) unter „Geografische Verfügbarkeit“.

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.

## Schritt 2: Konfigurieren des Tresors und Herunterladen von InMage Scout-Komponenten

1. Klicken Sie auf **Tresor erstellen**.
2. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen.
3. Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen VMware-Standorten** aus.
4. Laden Sie InMage Scout herunter. Die Setupdateien für alle erforderlichen Komponenten befinden sich in der heruntergeladenen ZIP-Datei.


## Schritt 3: Installieren von Komponentenupdates

0Erfahren Sie mehr über die neuesten[Updates](#updates). Installieren Sie die Updatedateien in der folgenden Reihenfolge:

1. RX-Server, falls vorhanden
2. Konfigurationsserver
3. Prozessserver
3. Masterzielserver.
4. vContinuum-Server.

Installieren Sie wie folgt:

1. Laden Sie die ZIP-Datei für das [Update](http://aka.ms/scoutupdates) herunter. Diese ZIP-Datei enthält die folgenden Dateien:

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. Extrahieren Sie die ZIP-Dateien.
2. **RX server**: Kopieren Sie **RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz** auf den RX-Server, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install** aus.
2. **Konfigurationsserver/Prozessserver**: Kopieren Sie **CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe** auf den Konfigurationsserver und Prozessserver. Führen Sie sie durch Doppelklicken aus.
3. **Windows-Masterzielserver**: Um die Kopie des vereinheitlichten Agents zu aktualisieren, kopieren Sie **UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe** auf den Masterzielserver. Führen Sie sie durch Doppelklicken aus. Beachten Sie, dass der vereinheitliche Agent für Windows nicht auf den Quellserver anwendbar ist. Er sollte nur auf dem Windows-Masterzielserver installiert werden.
4. **Linux-Masterzielserver**: Um die Kopie des vereinheitlichten Agents zu aktualisieren, kopieren Sie **UA\_RHEL6 64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz** auf den Masterzielserver, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install** aus.
5. **vContinuum-Server**: Kopieren Sie **vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe** auf den vContinuum-Server. Stellen Sie sicher, dass Sie den vContinuum Assistenten geschlossen haben. Doppelklicken Sie auf die Datei, um Sie auszuführen.

## Schritt 4: Einrichten der Replikation
5. Richten Sie die Replikation zwischen den Quell- und Zielstandorten von VMware ein.
6. Anleitungen finden Sie in der InMage Scout-Dokumentation, die zusammen mit dem Produkt heruntergeladen wird. Alternativ können Sie wie folgt auf die Dokumentation zugreifen:

	- [Versionshinweise](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [Kompatibilitätsmatrix](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [Benutzerhandbuch](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [RX-Benutzerhandbuch](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [Schnellinstallationsanleitung](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## Aktualisierungen

### ASR Scout 8.0.1, Update vom 03.12.15

Das Update vom 03.12.15 umfasst Folgendes:

- **Konfigurationsserver**: Behebt ein Problem, das ein ordnungsgemäßes Funktionieren der 31 Tage kostenlosen Messfunktion verhinderte, wenn der Konfigurationsserver in Site Recovery registriert wurde.
- **Vereinheitlichter Agent**: Behebt ein Problem in Update 1 für das Masterziel, das bewirkte, dass das Update nicht auf dem Masterzielserver installiert wurde, wenn ein Upgrade von Version 8.0 auf 8.0.1 erfolgte.

>[AZURE.NOTE]
>
>-	Alle ASR-Updates sind kumulativ.
>-	Die Updates vom Typ CS und RX können nicht rückgängig gemacht werden, sobald sie auf das System angewendet wurden.


### ASR Scout 8.0.1 Update 1

Dieses neueste Update enthält Fehlerbehebungen und neue Funktionen:

- 31 Tage lang kostenloser Schutz pro Serverinstanz. Dies ermöglicht es Ihnen, die Funktionalität zu testen oder eine Machbarkeitsstudie einzurichten.
	- Alle Vorgänge auf dem Server, einschließlich Failover und Failback sind für die ersten 31 Tage kostenlos, ab dem Zeitpunkt, zu dem ein Server erstmalig mit ASR Scout geschützt wird.
	- Ab dem 32. Tag wird für jeden geschützten Server die Standardinstanzgebühr für Azure Site Recovery-Schutz für eine Website in Rechnung gestellt, die im Besitz eines Kunden ist.
	- Die Anzahl der geschützten Server, die zurzeit in Rechnung gestellt werden, kann jederzeit auf der Seite "Dashboard" des Azure Site Recovery-Tresors angezeigt werden.
- Unterstützung für vCLI 5.5 Update 2 hinzugefügt.
- Unterstützung für Linux-Betriebssysteme auf dem Quellserver hinzugefügt:
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Fehlerbehebungen für folgende Probleme:
	- Fehler der Tresorregistrierung für den Konfigurationsserver oder RX-Server.
	- Clustervolumes werden nicht wie erwartet angezeigt, wenn gruppierte virtuelle Ccomputer während der Wiederaufnahme erneut geschützt werden.
	- Failbackfehler, wenn der Masterzielserver auf einem anderen ESXi-Server als den lokalen virtuellen Produktionscomputern gehostet wird.
	- Die Konfigurationsdateiberechtigungen ändern sich, wenn ein Upgrade auf 8.0.1 durchgeführt wird, was sich auf den Schutz und Vorgänge auswirkt.
	- Der Neusynchronisierungs-Schwellenwert wird nicht wie erwartet erzwungen, was zu inkonsistentem Replikationsverhalten führt.
	- RPO-Einstellungen werden nicht ordnungsgemäß in der Konfigurationsserverschnittstelle angezeigt. Der unkomprimierte Datenwert zeigt fälschlicherweise einen komprimierten Wert an.
	-  Der Vorgang "Entfernen" löscht nicht wie erwartet im vContinuum-Assistenten, und die Replikation wird nicht in der Konfigurationsserverschnittstelle gelöscht.
	-  Im vContinuum-Assistenten wird der Datenträger automatisch deaktiviert, wenn Sie in der Datenträgeransicht während des Schutzes von virtuellen MSCS-Computern auf **Details** klicken.
	- Während des P2V-Szenarios erforderliche HP-Dienste wie CIMnotify und CqMgHost werden im wiederhergestellten virtuellen Computer nicht in "Manuell" verschoben, was zu einem verlängerten Startvorgang führt.
	- Fehler eines geschützten virtuellen Linux-Computers, wenn auf dem Masterzielserver mehr als 26 Datenträger vorhanden sind.

## Nächste Schritte

Etwaige Fragen können Sie im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.

<!---HONumber=AcomDC_1210_2015-->