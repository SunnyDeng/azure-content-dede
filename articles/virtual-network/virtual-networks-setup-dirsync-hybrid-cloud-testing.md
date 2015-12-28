<properties 
	pageTitle="Testumgebung für Office 365 DirSync | Microsoft Azure" 
	description="Erfahren Sie, wie für ein Office 365-Verzeichnissynchronisierung (DirSync)-Serverkonfiguration in einer hybridcloud für IT Pro oder Entwicklung zu testen." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2015" 
	ms.author="josephd"/>

# Office 365-Verzeichnissynchronisierung (DirSync) in einer hybridcloud zu Testzwecken einrichten

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 

Dieses Thema führt Sie durch die Erstellung einer hybriden Cloud-Umgebung zum Testen von Office 365-Verzeichnissynchronisierung (DirSync) mit Kennwortsynchronisierung in Microsoft Azure gehostet. Hier sehen Sie die daraus resultierende Konfiguration.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Diese Konfiguration simuliert einen Dirsync-Server in Azure-Produktionsumgebung von Ihrem Speicherort im Internet. Sie besteht aus:

- einem vereinfachten lokalen Netzwerk (dem Corpnet-Subnetz).
- Einem standortübergreifenden virtuellen in Azure gehosteten Netzwerk (TestVNET)
- einer Site-to-Site-VPN-Verbindung.
- Ein Office 365 Fast Track-Testabonnement.
- Ein DirSync-Server, der das Azure AD Connect-Tool ausführt, und ein sekundärer Domänencontroller im virtuellen Netzwerk TestVNET.

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Ausgangspunkt für Folgendes:

- Entwickeln und Testen von Anwendungen für Office 365, die von der Synchronisierung mit einer lokalen Active Directory-Domäne mithilfe der Kennwortsynchronisierung abhängen.
- Testen dieses cloudbasierten IT-Workloads

Die Einrichtung dieser Hybrid Cloud-Testumgebung besteht aus drei Hauptphasen:

1.	Einrichten der Hybrid Cloud-Umgebung zu Testzwecken
2.	Konfigurieren Sie die Testversion von Office 365 Fast Track.
3.	Konfigurieren Sie den DirSync-Server (DS1).

Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure ausprobieren](http://azure.microsoft.com/pricing/free-trial/) für eine kostenlose Testversion registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie [Azure-Vorteil für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1: Einrichten der Hybridcloudumgebung

Folgen Sie den Anweisungen im Thema [Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md). Da das Vorhandensein des APP1-Servers im Subnetz des Unternehmensnetzwerks in dieser Testumgebung nicht erforderlich ist, können Sie jetzt herunterfahren.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]Für Phase 1 können Sie auch die simulierte Hybridcloud-Testumgebung einrichten. Im Thema [Einrichten einer simulierten Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) finden Sie entsprechende Anweisungen.

## Phase 2: Konfigurieren der Testversion von Office 365 Fast Track

Um Ihre Testversion von Office 365 Fast Track zu starten, benötigen Sie einen fiktiven Firmennamen und ein Microsoft-Konto. Wir empfehlen Ihnen, dass Sie eine Variante des Unternehmensnamens Contoso für den Namen Ihres Unternehmens verwenden. Das ist ein fiktives Unternehmen, das in Microsoft-Beispielinhalten verwendet wird, wobei dies aber nicht unbedingt erforderlich ist.

Melden Sie sich als Nächstes ein neues Microsoft-Konto an. Wechseln Sie zu ****http://outlook.com**, und erstellen Sie ein Konto mit einer E-Mail-Adresse wie user123@outlook.com. Sie können für eine Testversion von Office 365 Fast Track mit diesem Konto anmelden.

Melden Sie sich als Nächstes für eine neue Office 365 Enterprise E3-Testversion an.

1.	Melden Sie sich mit den Anmeldeinformationen des CORP\\User1 CLIENT1 an.
2.	Öffnen Sie Internet Explorer, und wechseln Sie zu ****https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Melden Sie sich für die Testversion von Office 365 Enterprise E3 an.

Wenn Sie zur Eingabe Ihrer **geschäftlichen E-Mail-Adresse** aufgefordert werden, geben Sie Ihr neues Microsoft-Konto an.

Wenn Sie zum Erstellen einer ID aufgefordert werden, geben Sie den Namen eines anfänglichen Office 365-Kontos, den fiktiven Firmennamen und ein Kennwort ein. Notieren Sie die zurückgegebene E-Mail-Adresse (z. B. user123@contoso123.onmicrosoft.com), und bewahren Sie sie mit dem Kennwort sicher auf. Sie benötigen diese Informationen, um die Azure AD Connect-Konfiguration in Phase 3 abzuschließen.

Danach wird die Hauptseite des Office 365-Portals angezeigt. Klicken Sie im oberen Menüband auf **Admin**, und klicken Sie dann auf **Office 365**. Die Office 365 Admin Center-Seite angezeigt wird. Lassen Sie auf dieser Seite auf "client1" geöffnet.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Phase 3: Konfigurieren des DirSync-Servers (DS1)

Erstellen Sie zunächst einen virtuellen Azure-Computer für DS1 mit diesen Befehlen an der Eingabeaufforderung von Azure PowerShell auf dem lokalen Computer. Füllen Sie vor dem Ausführen dieser Befehle die Variablenwerte aus, und entfernen Sie die Zeichen < and >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Schließen Sie dann den virtuellen Computer DS1.

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite für virtuelle Computer in der STATUS-Spalte für den virtuellen Computer "DS1" auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie dazu aufgefordert werden, "DS1.rdp" zu öffnen, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
	- Name: **CORP\\User1**
	- Kennwort: [Kontokennwort für "User1"]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr zum Testen der allgemeinen Konnektivität zuzulassen. Ein auf Windows PowerShell-Eingabeaufforderung auf DS1 führen Sie diese Befehle.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte zu vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 führen.

Als Nächstes installieren Sie .NET 3.5 auf DS1 mit dem folgenden Befehl an der Windows PowerShell-Eingabeaufforderung.

	Add-WindowsFeature NET-Framework-Core

Aktivieren Sie als Nächstes Verzeichnissynchronisierung für Ihre Office 365 Fast Track-Testversion.

1.	Klicken Sie auf CLIENT1 auf der Seite **Office 365 Admin Center** im linken Bereich auf **Benutzer**, und klicken Sie dann auf **Aktive Benutzer**.
2.	Klicken Sie unter **Active Directory-Synchronisierung** auf **Einrichten**.
3.	Klicken Sie auf der Seite "Active Directory-Synchronisierung einrichten und verwalten" unter Schritt 3 auf **Aktivieren**.
4.	Wenn **Möchten Sie die Active Directory-Synchronisierung aktivieren?** angezeigt wird, klicken Sie auf **Aktivieren**. Danach wird **Active Directory-Synchronisierung ist aktiviert** in Schritt 3 angezeigt.
5.	Lassen Sie die Seite **Active Directory-Synchronisierung einrichten und verwalten** auf CLIENT1 geöffnet.

Als Nächstes melden Sie sich mit dem Konto CORP\\User1 auf DC1, und öffnen Sie eine auf Windows PowerShell-Eingabeaufforderung. Führen Sie diese Befehle nacheinander zum Erstellen einer neuen Organisationseinheit namens "Contoso\_users", und fügen Sie zwei neue Benutzerkonten für Marci Kaufman und Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Wenn Sie jeden Windows PowerShell-Befehl ausführen, werden Sie zur Eingabe des neuen Kennworts des Benutzers aufgefordert. Diese Kennwörter zu erfassen und an einem sicheren Ort aufbewahren. Diese werden später benötigt werden.

Installieren und konfigurieren Sie als Nächstes das Azure AD Connect-Tool auf DS1.

1.	Führen Sie Internet Explorer aus, geben Sie ****https://www.microsoft.com/download/details.aspx?id=47594** in der **Adressleiste** ein, und betätigen Sie dann die EINGABETASTE.
2.	Führen Sie das Microsoft Azure AD Connect-Setupprogramm aus.
3.	Doppelklicken Sie auf dem Desktop auf ** Azure AD Connect**.
4.	Wählen Sie auf der Seite **Willkommen** die Option **Ich stimme den Lizenzbedingungen und dem Datenschutzhinweis zu**, und klicken Sie dann auf **Weiter**.
5.	Klicken Sie auf der Seite **Expresseinstellungen** auf **Expresseinstellungen verwenden**.
6.	Geben Sie auf der Seite **Herstellen einer Verbindung mit Azure AD** die E-Mail-Adresse und das Kennwort des anfänglichen Kontos ein, das Sie beim Einrichten der Testversion von Office 365 Fast Track in Phase 2 erstellt haben. Klicken Sie auf **Weiter**.
7.	Geben Sie auf der Seite **Herstellen einer Verbindung mit AD DS** den Namen **CORP\\User1** unter **Benutzername** und das User1-Kontokennwort unter **Kennwort** ein. Klicken Sie auf Weiter.
8.	Überprüfen Sie auf der Seite **Bereit zum Konfigurieren** die Einstellungen, und klicken Sie dann auf **Installieren**.
9.	Klicken Sie auf der Seite **Konfiguration abgeschlossen** auf **Beenden**.

Als Nächstes stellen Sie sicher, dass die Benutzerkonten in der CORP-Domäne zu Office 365 synchronisiert werden. Beachten Sie, dass es möglicherweise ein paar Minuten dauert, bis die Synchronisierung erfolgt.

Klicken Sie auf CLIENT1 auf der Seite **Active Directory-Synchronisierung einrichten und verwalten** auf den Link **Benutzer** in Schritt 6 auf der Seite. Wenn die Verzeichnissynchronisierung erfolgreich war, sollte etwa wie folgt angezeigt werden.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

Die Spalte **Status** gibt an, dass das Konto über die Synchronisierung mit einer Active Directory-Domäne abgerufen wurde.

Führen Sie dann die Office 365-Kennwortsynchronisierung mit dem Active Directory-Konto „Lynda Myer“ aus.

1.	Wählen Sie auf CLIENT1 auf der Seite **Aktive Benutzer** das Konto **Lynda Meyer** aus.
2.	Klicken Sie in den Eigenschaften des Kontos "Lynda Meyer" unter **Zugewiesene Lizenz** auf **Bearbeiten**.
3.	Wählen Sie auf der Registerkarte **Lizenz zuweisen** einen Standort unter **Benutzerstandort einstellen** aus (z. B. Vereinigte Staaten).
4.	Wählen Sie **Microsoft Office 365 (Plan E3)** aus, und klicken Sie dann auf **Speichern**.
5.	Schließen Sie Internet Explorer.
6.	Führen Sie Internet Explorer aus, und wechseln Sie zu ****http://portal.microsoftonline.com**.
7.	Melden Sie sich mit den Office 365-Anmeldeinformationen von Lynda Meyer an. Der Benutzername lautet dann "lyndam@<*Ihr fiktiver Name*>.onmicrosoft.com". Das Kennwort ist das Kennwort für das Lynda Dressler Active Directory-Benutzerkonto.
8.	Nach der erfolgreichen Anmeldung sehen Sie die Hauptseite des Office 365-Portals mit **Heute machen wir einen Unterschied**.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Diese Umgebung ist nun bereit für Sie zum Testen des Office 365-Anwendungen, die auf Office 365 DirSync-Funktion basieren oder DirSync-Funktionalität und Leistung von DS1 testen.

## Zusätzliche Ressourcen

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Lösungen mit Office-Server und die Cloud](http://technet.microsoft.com/library/dn262744.aspx)

[Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Einrichten einer simulierten Hybrid Cloud-Umgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Testumgebungen für Azure-Hybridclouds](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=AcomDC_1217_2015-->