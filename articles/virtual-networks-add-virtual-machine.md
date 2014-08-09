<properties linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk
==================================================================

In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines Azure-Speicherkontos und eines virtuellen Azure-Computers (VM) geführt, den Sie zu einem [virtuellen Netzwerk](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx) hinzufügen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben.

**Wichtig**

Wenn Sie planen, eine VM zu erstellen, um eine neue Active Directory-Gesamtstruktur zu installieren, befolgen Sie die Anweisungen unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](../active-directory-forest/).

Ziele
-----

In diesem Lernprogramm lernen Sie Folgendes:

-   [Erstellen eines Speicherkontos](#CreateStorageAcct)

-   [Erstellen eines virtuellen Computers und Bereitstellen für ein virtuelles Netzwerk](#CreateVM)

Voraussetzungen
---------------

-   Führen Sie eines der folgenden Lernprogramme aus:

    -   [Erstellen eines virtuellen Netzwerks in Azure](/de-de/manage/services/networking/create-a-virtual-network/)

        ODER

    -   [Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen](/de-de/manage/services/networking/cross-premises-connectivity/)
-   Windows Live-Konto mit mindestens einem gültigen aktiven Abonnement

-   Die Namen der folgenden Komponenten aus [Erstellen eines virtuellen Netzwerks in Azure](/de-de/manage/services/networking/create-a-virtual-network/) oder [Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen](/de-de/manage/services/networking/cross-premises-connectivity/):

    -   Affinitätsgruppe, die Sie dem virtuellen Netzwerk zugewiesen haben

    -   Name des virtuellen Netzwerks

    -   Namen der Subnetze

Erstellen des Speicherkontos
----------------------------

1.  Klicken Sie nach der Erstellung des virtuellen Netzwerks im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) unten links auf dem Bildschirm auf **Neu**.

    ![Neues Speicherkonto](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  Klicken Sie im Navigationsbereich auf **DATA SERVICES** \> **SPEICHER** und dann auf **SCHNELLERFASSUNG**.

    ![Schnellerfassung](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  Geben Sie die folgenden Informationen ein, und klicken Sie dann auf das Häkchen rechts auf dem Bildschirm.

-   **URL:** Geben Sie *yourstorage* ein.

-   **REGION/AFFINITY GROUP:** Wählen Sie in der Dropdownliste **YourAffinityGroup** aus.

-   **ENABLE GEO-REPLICATION:** Lassen Sie dieses Kontrollkästchen aktiviert.

    ![Neues Konto erstellen](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.  Auf der Seite **Speicher** wird in der Spalte **STATUS** die Option **Online** angezeigt, wenn der Vorgang abgeschlossen ist.

    ![Neues Speicherkonto erstellt](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)

Erstellen eines virtuellen Computers und Bereitstellen für ein virtuelles Netzwerk
----------------------------------------------------------------------------------
**So erstellen Sie einen virtuellen Computer und stellen ihn für ein virtuelles Netzwerk bereit:**

1.  Klicken Sie nach dem Erstellen des Speicherkontos unten links auf dem Bildschirm auf **Neu**.

    ![Neue VM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  Klicken Sie im Navigationsbereich auf **COMPUTE** \> **VIRTUELLER COMPUTER** und dann auf **FROM GALLERY**.

    ![Aus dem Katalog](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  Wählen Sie auf dem Bildschirm **VM OS Selection** die Option **Windows Server 2008 R2 SP1, October 2012** (oder die aktuelle Version), und klicken Sie dann auf den nächsten Pfeil.

    ![VM-Betriebssystem](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)


4.  Geben Sie auf dem Bildschirm **Konfiguration des virtuellen Computers** die folgenden Informationen ein, und klicken Sie dann auf den nächsten Pfeil.

    **Tipp:** Notieren Sie den Benutzernamen und das Kennwort, da Sie sich mit diesen Anmeldeinformationen beim neuen virtuellen Computer anmelden.

-   **NAME DES VIRTUELLEN COMPUTERS:** Geben Sie *YourVMachine* ein.

-   **NEUER BENUTZERNAME:** Schreibgeschützt.

-   **NEUES KENNWORT:** Geben Sie ein sicheres Kennwort ein.

-   **KENNWORT BESTÄTIGEN:** Geben Sie das Kennwort erneut ein.

-   **GRÖSSE:** Wählen Sie **Klein** aus.

    ![VM-Konfiguration](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.  Geben Sie auf dem Bildschirm **Virtual machine mode** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil.

-   **Standalone Virtual Machine:** Lassen Sie diese Option aktiviert.

-   **DNS-NAME:** Geben Sie *yourcloudapplication* ein.

-   **SPEICHERKONTO:** Wählen Sie **yourstorage** aus.

-   **REGION/AFFINITY GROUP/VIRTUAL NETWORK:** Wählen Sie in der Dropdownliste **YourVirtualNetwork** aus.

    ![VM-Modus](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.  Geben Sie auf dem Bildschirm **Virtual machine options** die folgenden Informationen ein, und klicken Sie dann auf das Häkchen. Der virtuelle Computer wird jetzt erstellt. Es kann bis zu 10 Minuten dauern, bis der neue Computer erstellt ist.

-   **VERFÜGBARKEITSGRUPPE:** Wählen Sie **Keine** aus.

-   **VIRTUAL NETWORK SUBNETS:** Wählen Sie **FrontEndSubnet** aus.

    **HINWEIS**

    Sie müssen mindestens ein Subnetz auswählen, jedoch NICHT das Gatewaysubnetz.

    ![VM-Optionen](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

7.  Nach der Erstellung des virtuellen Computers wird auf dem Bildschirm des virtuellen Computers für **STATUS** **Wird ausgeführt** angegeben.

    ![VM-Instanzen](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)

8.  Klicken Sie im Navigationsbereich auf **ALL ITEMS**. Alle Objekte werden mit dem aktuellen Status angezeigt.

    ![Registerkarte "All items"](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

Nächste Schritte
----------------

Informationen zum Installieren eines weiteren Domänencontrollers für Ihre lokale Active Directory-Domäne auf der gerade erstellten VM finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Network](/de-de/manage/services/networking/replica-domain-controller/).

Weitere Informationen
---------------------

-   [Virtuelles Azure-Netzwerk](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx)

-   [Konfigurieren eines virtuellen Netzwerks mit Netzwerkkonfigurationsdateien](http://msdn.microsoft.com/de-de/library/windowsazure/jj156097.aspx)


<!-- LINKS -->

[wa_com]: http://manage.windowsazure.com/
[Tut2_VN]: ..Tutorial2_CreateVNetCrossPrem 
[Tut1_VN]: ..Tutorial1_CreateVirtualNetwork