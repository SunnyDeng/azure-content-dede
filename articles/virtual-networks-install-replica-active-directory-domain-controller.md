<properties linkid="manage-services-networking-replica-domain-controller" urlDisplayName="Replica domain controller" pageTitle="Install a replica domain controller in Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="" solutions="" manager="" editor="" />

Installieren eines Active Directory-Replikatdomänencontrollers in Azure Virtual Networks
========================================================================================

In diesem Lernprogramm werden Sie durch die Schritte zum Installieren eines zusätzlichen Domänencontrollers aus Ihrer Active Directory-Unternehmensgesamtstruktur auf einem virtuellen Computer (VM) in [Azure Virtual Network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx) geführt. Im Lernprogramm wird das virtuelle Netzwerk für die VM mit dem Netzwerk in Ihrem Unternehmen verbunden. Konzeptuelle Hilfe zum Installieren von Active Directory-Domänendiensten (AD DS) auf Azure Virtual Network finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx).

Inhaltsverzeichnis
------------------

-   [Voraussetzungen](#Prerequisites)
-   [Schritt 1: Überprüfen der statischen IP-Adresse für "YourPrimaryDC"](#verifystaticip)
-   [Schritt 2: Installieren der Unternehmensgesamtstruktur](#installforest)
-   [Schritt 3: Erstellen von Subnetzen und Standorten](#subnets)
-   [Schritt 4: Installieren eines weiteren Domänencontrollers in "CloudSite"](#cloudsite)
-   [Schritt 5: Überprüfen der Installation](#validate)
-   [Schritt 6: Bereitstellen eines virtuellen Computers, der beim Start in die Domäne eingebunden ist](#provisionvm)
-   [Schritt 7: Sichern des Domänencontrollers](#backup)
-   [Schritt 8: Testen der Authentifizierung und Autorisierung](#test)


Voraussetzungen
---------------

-   [Erstellen Sie ein virtuelles Netzwerk für standortübergreifende Verbindungen](http://www.windowsazure.com/de-de/manage/services/networking/cross-premises-connectivity/), das zwischen dem virtuellen Azure-Netzwerk und dem Unternehmensnetzwerk konfiguriert wird.
-   Erstellen Sie einen Clouddienst im virtuellen Netzwerk.
-   Stellen Sie zwei VMs im Clouddienst bereit, die Teil des virtuellen Netzwerks sind (geben Sie das Subnetz an, in dem sich die VM befinden soll). Weitere Informationen finden Sie unter [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](http://www.windowsazure.com/de-de/manage/services/networking/add-a-vm-to-a-virtual-network/). Eine VM muss mindestens die Größe L haben, damit zwei Datenträger daran angeschlossen werden können. Die Datenträger werden zum Speichern der folgenden Daten benötigt:
    -   Active Directory-Datenbank und -protokolle
    -   Sicherungen des Systemstatus
-   Ein Unternehmensnetzwerk mit zwei VMs (**YourPrimaryDC** und **FileServer**).
-   DNS (Domain Name System)-Infrastruktur, die bereitgestellt wird, wenn externe Benutzer Namen für Konten in Active Directory auflösen müssen. In diesem Fall sollten Sie eine DNS-Zonendelegierung erstellen, bevor Sie den DNS-Server auf dem Domänencontroller installieren, oder zulassen, dass die Delegierung vom Assistenten zum Installieren von Active Directory-Domänendiensten erstellt wird. Weitere Informationen zum Erstellen einer DNS-Zonendelegierung finden Sie unter [Erstellen einer Zonendelegierung](http://technet.microsoft.com/en-us/library/cc753500.aspx).
-   Konfigurieren Sie auf dem DC, den Sie auf einer Azure-VM installieren, die folgenden Konfliktlösereinstellungen für den DNS-Client:
    -   Bevorzugter DNS-Server: Lokaler DNS-Server
    -   Alternativer DNS-Server: Loopbackadresse oder, falls möglich, ein anderer DNS-Server, der auf einem DC im selben virtuellen Netzwerk ausgeführt wird

**Hinweis**

Sie müssen Ihre eigene DNS-Infrastruktur angeben, damit AD DS in Azure Virtual Network unterstützt wird. Die von Azure für diese Version bereitgestellte DNS-Infrastruktur unterstützt einige von AD DS benötigten Features nicht, z. B. die dynamische Registrierung von SRV-Ressourceneinträgen.

**Hinweis**

Wenn Sie die Schritte unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](/de-de/manage/services/networking/active-directory-forest/) bereits ausgeführt haben, müssen Sie möglicherweise AD DS aus dem Domänencontroller im virtuellen Azure-Netzwerk entfernen, bevor Sie mit diesem Lernprogramm beginnen. Weitere Informationen zum Entfernen von AD DS finden Sie unter [Entfernen eines Domänencontrollers aus einer Domäne](http://technet.microsoft.com/en-us/library/cc771844(v=WS.10).aspx).

Schritt 1: Überprüfen der statischen IP-Adresse für "YourPrimaryDC"
-------------------------------------------------------------------

1.  Melden Sie sich im Unternehmensnetzwerk bei **YourPrimaryDC** an.

2.  Klicken Sie im Server-Manager auf **Netzwerkverbindungen anzeigen**.

3.  Klicken Sie mit der rechten Maustaste auf die LAN-Verbindung, und klicken Sie dann auf **Eigenschaften**.

4.  Klicken Sie auf **Internetprotokoll Version 4 (TCP/IPv4)** und dann auf **Eigenschaften**.

5.  Stellen Sie sicher, dass dem Server eine statische IP-Adresse zugewiesen ist.

    ![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


Schritt 2: Installieren der Unternehmensgesamtstruktur
------------------------------------------------------

1.  Klicken Sie in der RDP-Sitzung für die VM auf **Start**, geben Sie **dcpromo** ein, und drücken Sie die EINGABETASTE.

    ![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2.  Klicken Sie auf der Seite **Willkommen** auf **Weiter**.

    ![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3.  Klicken Sie auf der Seite **Betriebssystemkompatibilität** auf **Weiter**.

    ![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4.  Klicken Sie auf der Seite **Bereitstellungskonfiguration auswählen** auf **Neue Domäne in neuer Gesamtstruktur erstellen** und dann auf **Weiter**.

    ![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5.  Geben Sie auf der Seite **Name der Gesamtstruktur-Stammdomäne** als vollqualifizierten Domänennamen (FQDN) der Gesamtstruktur-Stammdomäne **corp.contoso.com** ein, und klicken Sie auf **Weiter**.

    ![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6.  Klicken Sie auf der Seite **Funktionsebene der Gesamtstruktur festlegen** auf **Windows Server 2008 R2** und dann auf **Weiter**.

    ![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7.  Klicken Sie auf der Seite **Weitere Domänencontrolleroptionen** auf **DNS-Server** und dann auf **Weiter**.

    ![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8.  Klicken Sie auf **Ja**, wenn die folgende Warnung zur DNS-Delegierung angezeigt wird.

    ![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9.  Geben Sie auf der Seite **Location for Active Directory database, log files and SYSVOL** den Speicherort der Dateien ein oder wählen Sie ihn aus, und klicken Sie dann auf **Weiter**.

    ![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. Geben Sie auf der Seite **Directory Services Restore Administrator** das DSRM-Kennwort ein und bestätigen Sie es. Klicken Sie dann auf **Weiter**.

    ![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. Bestätigen Sie auf der Seite **Zusammenfassung** Ihre Auswahl, und klicken Sie auf **Weiter**.

    ![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Klicken Sie nach Beendigung des Assistenten zum Installieren von Active Directory-Domänendiensten auf **Fertig stellen** und dann auf **Neu starten**, um die Installation abzuschließen.

    ![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



Schritt 3: Erstellen von Subnetzen und Standorten
-------------------------------------------------

1.  Klicken Sie auf **YourPrimaryDC** auf **Start** \> **Verwaltung** und dann auf **Active Directory-Standorte und -Dienste**.
2.  Klicken Sie auf **Standorte**, klicken Sie mit der rechten Maustaste auf **Subnetze**, und klicken Sie dann auf **Neues Subnetz**.

    ![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3.  Geben Sie unter **Präfix::** die Zahlenfolge **10.1.0.0/24** ein, wählen Sie das Standortobjekt **Default-First-Site-Name** aus, und klicken Sie dann auf **OK**.

    ![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4.  Klicken Sie mit der rechten Maustaste auf **Standorte**, und klicken Sie dann auf **Neuer Standort**.

    ![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5.  Geben Sie unter **Name** den Namen **CloudSite** ein, wählen Sie **DEFAULTIPSITELINK** aus, und klicken Sie auf **OK**.

    ![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6.  Klicken Sie auf **OK**, um zu bestätigen, dass der Standort erstellt wurde.

    ![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7.  Klicken Sie mit der rechten Maustaste auf **Subnetze**, und klicken Sie dann auf **Neues Subnetz**.

    ![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8.  Geben Sie unter **Präfix::** die Zahlenfolge **10.4.2.0/24** ein, wählen Sie das Standortobjekt **CloudSite** aus, und klicken Sie dann auf **OK**.

    ![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


Schritt 4: Installieren eines weiteren Domänencontrollers in "CloudSite"
------------------------------------------------------------------------

1.  Melden Sie sich bei **YourVMachine** an, klicken Sie auf **Start**, geben Sie **dcpromo** ein, und drücken Sie die EINGABETASTE.

    ![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2.  Klicken Sie auf der Seite **Willkommen** auf **Weiter**.

    ![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)

3.  Klicken Sie auf der Seite **Betriebssystemkompatibilität** auf **Weiter**.

    ![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4.  Klicken Sie auf der Seite **Bereitstellungskonfiguration auswählen** auf **Vorhandene Gesamtstruktur** \> **Domänencontroller vorhandener Domäne hinzufügen** und dann auf **Weiter**.

    ![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5.  Stellen Sie auf der Seite **Sicherheitsinformationen für das Netzwerk** sicher, dass Sie den Domänencontroller in der Domäne **corp.contoso.com** installieren, und geben Sie die Sicherheitsinformationen eines Mitglieds der Gruppe **Domänen-Admins** ein (oder verwenden Sie die Sicherheitsinformationen von **corp\\administrator**).

    ![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)

6.  Klicken Sie auf der Seite **Domäne auswählen** auf **Weiter**.

    ![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7.  Stellen Sie auf der Seite **Standort auswählen** sicher, dass **CloudSite** ausgewählt ist, und klicken Sie auf **Weiter**.

    ![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8.  Klicken Sie auf der Seite **Weitere Domänencontrolleroptionen** auf **Weiter**.

    ![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9.  Klicken Sie in der Warnmeldung **Zuweisung statischer IP** auf **Ja, der Computer verwendet eine automatisch von einem DHCP-Server zugewiesene IP-Adresse (nicht empfohlen)**.

    **Wichtig**

    Die IP-Adresse in Azure Virtual Network ist zwar dynamisch, die Leasedauer entspricht jedoch der Dauer der VM. Deshalb müssen Sie keine statische IP-Adresse für den Domänencontroller festlegen, den Sie im virtuellen Netzwerk installieren. Wenn eine statische IP-Adresse in der VM festgelegt wird, kommt es zu Kommunikationsfehlern.

    ![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. Klicken Sie auf **Ja**, wenn Sie eine Nachfrage zur DNS-Delegierungswarnung erhalten.

    ![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. Klicken Sie auf der Seite **Location for Active Directory database, log files and SYSVOL** auf **Durchsuchen**, geben Sie einen Speicherort für die Active Directory-Dateien auf dem Datenträger ein oder wählen Sie ihn aus, und klicken Sie auf **Weiter**.

    ![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. Geben Sie auf der Seite **Directory Services Restore Administrator** das DSRM-Kennwort ein und bestätigen Sie es. Klicken Sie dann auf **Weiter**.

    ![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. Klicken Sie auf der Seite **Zusammenfassung** auf **Weiter**.

    ![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Klicken Sie nach Beendigung des Assistenten zum Installieren von Active Directory-Domänendiensten auf **Fertig stellen** und dann auf **Neu starten**, um die Installation abzuschließen.

    ![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


Schritt 5: Überprüfen der Installation
--------------------------------------

1.  Stellen Sie die Verbindung mit der VM wieder her.

2.  Klicken Sie auf **Start**, klicken Sie mit der rechten Maustaste auf **Eingabeaufforderung**, und klicken Sie dann auf **Als Administrator ausführen**.

3.  Geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE: 'Dcdiag /c /v'

4.  Überprüfen Sie, ob die Tests erfolgreich ausgeführt wurden.

Führen Sie nach der Konfiguration des Domänencontrollers das folgende Windows PowerShell-Cmdlet aus, um weitere virtuelle Maschinen bereitzustellen und automatisch bei der Bereitstellung zur Domäne hinzuzufügen. Die Konfliktlösereinstellungen des DNS-Clients für die VMs müssen konfiguriert sein, wenn die VMs bereitgestellt werden. Setzen Sie die richtigen Namen für Ihre Domäne, den VM-Namen usw. ein.

Weitere Informationen zum Verwenden von Windows PowerShell finden Sie unter [Erste Schritte mit Azure PowerShell](http://msdn.microsoft.com/de-de/library/windowsazure/jj156055.aspx) und [Azure-Cmdlets für die Verwaltung](http://msdn.microsoft.com/de-de/library/windowsazure/jj152841).


Schritt 6: Bereitstellen eines virtuellen Computers, der beim Start in die Domäne eingebunden ist
-------------------------------------------------------------------------------------------------

1.  Öffnen Sie Azure PowerShell ISE, fügen Sie das folgende Skript ein, ersetzen Sie die Platzhalter durch eigene Werte und führen Sie das Skript aus, um eine weitere virtuelle Maschine zu erstellen, die beim ersten Start in die Domäne eingebunden ist.

    Klicken Sie auf den Namen des virtuellen Netzwerks, in dem die Ausführung erfolgt, um die interne IP-Adresse des Domänencontrollers zu bestimmen.

    Im folgenden Beispiel lautet die interne IP-Adresse des Domänencontrollers 10.4.3.1. **Add-AzureProvisioningConfig** übernimmt auch einen **-MachineObjectOU**-Parameter, der die Festlegung von Gruppenrichtlinieneinstellungen auf allen virtuellen Computern in diesem Container ermöglicht, wenn er angegeben wird (erfordert den vollständigen Distinguished Name in Active Directory).

    Melden Sie sich nach der Bereitstellung der virtuellen Computer an, indem Sie ein Domänenkonto im Format eines Benutzerprinzipalnamen angeben, z. B. **administrator@corp.contoso.com**.

         #Deploy a new VM and join it to the domain
         #-------------------------------------------
         #Specify my DC's DNS IP (10.4.3.1)
         $myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
            
         # OS Image to Use
         $image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-en-us-30GB.vhd'
         $service = 'myazuresvcindomainM1'
         $AG = 'YourAffinityGroup'
         $vnet = 'YourVirtualNetwork'
         $pwd = 'p@$$w0rd'
         $size = 'Small'
            
         #VM Configuration
         $vmname = 'MyTestVM1'
         $MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
             Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
             Set-AzureSubnet -SubnetNames 'BackEnd'
            
         New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet


Schritt 7: Sichern des Domänencontrollers
-----------------------------------------


1.  Stellen Sie eine Verbindung mit **YourVMachine** her.

2.  Klicken Sie auf **Start** \> **Server-Manager** \> **Features hinzufügen**, und wählen Sie dann **Windows Server-Sicherungsfeatures** aus. Befolgen Sie die Anweisungen zum Installieren der Windows Server-Sicherung.

3.  Klicken Sie auf **Start** \> **Windows Server-Sicherung** \> **Einmalsicherung**.

4.  Klicken Sie auf **Unterschiedliche Optionen** und dann auf **Weiter**.

5.  Klicken Sie auf **Vollständiger Server** und dann auf **Weiter**.

6.  Klicken Sie auf **Lokale Datenträger** und dann auf **Weiter**.

7.  Wählen Sie das Ziellaufwerk aus, auf dem weder die Betriebssystemdateien noch die Active Directory-Datenbank gehostet werden, und klicken Sie auf **Weiter**.

    ![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8.  Bestätigen Sie die Sicherungseinstellungen, die Sie ausgewählt haben, und klicken Sie dann auf **Sicherung**.

Schritt 8: Testen der Authentifizierung und Autorisierung
---------------------------------------------------------

1.  Erstellen Sie ein Domänenbenutzerkonto in Active Directory, um die Authentifizierung und Autorisierung zu testen. Melden Sie sich bei der Client-VM an jedem Standort an, und erstellen Sie einen freigegebenen Ordner auf der VM.

2.  Testen Sie den Zugriff auf den freigegebenen Ordner mit verschiedenen Konten, Gruppen und Berechtigungen.

Weitere Informationen
---------------------

-   [Virtuelles Azure-Netzwerk](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx)

-   [Azure PowerShell](http://msdn.microsoft.com/de-de/library/windowsazure/jj156055.aspx)

-   [Azure-Cmdlets für die Verwaltung](http://msdn.microsoft.com/de-de/library/windowsazure/jj152841)


