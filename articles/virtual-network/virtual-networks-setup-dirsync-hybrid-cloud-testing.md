<properties 
	pageTitle="Einrichten der Office 365-Verzeichnissynchronisierung (DirSync) in einer Hybridcloud zu Testzwecken" 
	description="Erfahren Sie, wie Sie einen Office 365-Verzeichnissynchronisierungsserver (DirSync) in einer Hybridcloud für IT Pro oder Entwicklung zu testen." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Office 365-Verzeichnissynchronisierung (DirSync) in einer Hybridcloud zu Testzwecken einrichten

Dieses Thema führt Sie durch die Erstellung einer Hybridcloudumgebung zum Testen der Office 365-Verzeichnissynchronisierung (DirSync) mit Kennwortsynchronisierung, die in Microsoft Azure gehostet wird. Hier sehen Sie die daraus resultierende Konfiguration.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Mit dieser Konfiguration wird ein DirSync-Server in der Azure-Produktionsumgebung an Ihrem Standort im Internet simuliert. Sie besteht aus folgenden Elementen:

- Einem vereinfachten lokalen Netzwerk (dem Corpnet-Subnetz).
- Einem standortübergreifenden virtuellen in Azure gehosteten Netzwerk (TestVNET).
- Einer Site-to-Site-VPN-Verbindung.
- Einem Office 365 Fast Track-Testabonnement.
- Einem Dirsync-Server und einem sekundären Domänencontroller im virtuellen Netzwerk "TestVNET".

Diese Konfiguration bietet eine Grundlage und einen allgemeinen Ausgangspunkt für Folgendes:

- Entwickeln und Testen von Anwendungen für Office 365, die eine Synchronisierung mit einer lokalen Active Directory-Domäne mithilfe der Kennwortsynchronisierung benötigen
- Durchführen von Tests dieser cloudbasierten IT-Workload

Die Einrichtung dieser Hybridcloud-Testumgebung umfasst drei Hauptphasen:

1.	Einrichten der Hybridcloudumgebung zu Testzwecken
2.	Konfigurieren der Testversion von Office 365 Fast Track
3.	Konfigurieren des DirSync-Servers (DS1)

Wenn Sie noch kein Azure-Abonnement besitzen, können Sie sich unter [Azure testen](http://azure.microsoft.com/pricing/free-trial/) für eine kostenlose Testversion registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie [Azure-Vorteile für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1: Einrichten der Hybridcloudumgebung

Folgen Sie den Anweisungen im Thema [Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md). Da der APP1-Server für diese Testumgebung im Subnetz des Unternehmensnetzwerks nicht benötigt wird, können Sie ihn vorübergehend herunterfahren.

Die aktuelle Konfiguration sieht folgendermaßen aus:

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]Für Phase 1 können Sie auch die simulierte Hybridcloud-Testumgebung einrichten. Im Thema [Einrichten einer simulierten Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) finden Sie entsprechende Anweisungen.

## Phase 2: Konfigurieren der Testversion von Office 365 Fast Track

Um mit dem Test von Office 365 Fast Track zu beginnen, benötigen Sie einen fiktiven Firmennamen und ein Microsoft-Konto. Es wird empfohlen, dass Sie eine Variante des Unternehmensnamens "Contoso" als Unternehmensnamen verwenden, einem fiktiven Unternehmen, das in Microsoft-Beispielinhalten verwendet wird, dies ist aber nicht unbedingt erforderlich.

Melden Sie sich als Nächstes für ein neues Microsoft-Konto an. Wechseln Sie zu **http://outlook.com**, und erstellen Sie ein Konto mit einer E-Mail-Adresse wie user123@outlook.com. Mit diesem Konto melden Sie sich für eine Testversion von Office 365 Fast Track an.

Melden Sie sich als Nächstes für eine neue Office 365 Fast Track-Testversion an.

1.	Melden Sie sich mit den Anmeldeinformationen des Kontos "CORP\User1" bei CLIENT1 an.
2.	Öffnen Sie Internet Explorer, und navigieren Sie zu **http://fasttrack.office.com**.3.	Klicken Sie auf **Erste Schritte mit Fast Track**.
4.	Klicken Sie auf der Seite für die ersten Schritte mit Fast Track unter **Melden Sie sich zunächst für eine Testversion von Office 365 an** auf **Registrierung für Unternehmen**.
5.	Füllen Sie auf der Seite für Schritt 1 alle Felder aus, und geben Sie unter **Geschäftliche E-Mail-Adresse** Ihr neues Microsoft-Konto an. Klicken Sie dann auf **Weiter**.
6.	Geben Sie auf der Seite für Schritt 2 den Namen des ersten Office 365-Kontos, den Namen des fiktiven Unternehmens und ein Kennwort ein. Notieren Sie die zurückgegebene E-Mail-Adresse (z. B. user123@contoso123.onmicrosoft.com), und bewahren Sie sie mit dem Kennwort sicher auf. Sie benötigen diese Informationen, um den Konfigurations-Assistenten für das Active Directory-Synchronisierungstool in Phase 3 auszuführen. Klicken Sie auf **Weiter**.
7.	Geben Sie auf der Seite für Schritt 3 die Telefonnummer eines Mobiltelefons oder Smartphones an, auf dem Sie SMS empfangen können. Klicken Sie dann auf **SMS senden**.
8.	Nachdem Sie die SMS auf Ihrem Mobiltelefon erhalten haben, geben Sie den Überprüfungscode ein, und klicken Sie dann auf **Mein Konto erstellen**. 
9.	Wenn Office 365 Ihr Konto erstellt hat, klicken Sie auf **Sie sind jetzt startklar**.
10.	Die Hauptseite des Office 365-Portals sollte jetzt angezeigt werden. Klicken Sie im oberen Menüband auf **Admin**, und klicken Sie dann auf **Office 365**. Die Seite des Office 365 Admin Center wird angezeigt. Lassen Sie diese Seite auf "client1" geöffnet.

Die aktuelle Konfiguration sieht folgendermaßen aus:

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Phase 3: Konfigurieren des DirSync-Servers (DS1)

Erstellen Sie zunächst einen virtuellen Azure-Computer für DS1, indem Sie die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer ausführen. Füllen Sie vor dem Ausführen dieser Befehle die Variablenwerte aus, und entfernen Sie die < and >-Zeichen.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Stellen Sie dann eine Verbindung mit dem virtuellen Computer "DS1" her.

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite für virtuelle Computer in der STATUS-Spalte für den virtuellen Computer "DS1" auf **Wird ausgeführt**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**. 
3.	Wenn Sie dazu aufgefordert werden, "DS1.rdp" zu öffnen, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie folgende Angaben:
	- Name: **CORP\User1**
	- Kennwort: [Kontokennwort für "User1"\]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Konfigurieren Sie anschließend eine Windows-Firewall-Regel, um Datenverkehr zum Testen der allgemeinen Konnektivität zuzulassen. Führen Sie in einer Windows PowerShell-Eingabeaufforderung auf Administratorebene auf "DS1" die folgenden Befehle aus.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte zu vier erfolgreichen Antworten von der IP-Adresse 10.0.0.1 führen.

Als Nächstes installieren Sie .NET 3.5 auf "DS1" mit dem folgenden Befehl an der Windows PowerShell-Eingabeaufforderung.

	Add-WindowsFeature NET-Framework-Core

Installieren Sie dann die Verzeichnissynchronisierung auf "DS1".

1.	Führen Sie Internet Explorer aus, geben Sie **http://go.microsoft.com/fwlink/?LinkID=278924** in der Adressleiste ein, und drücken Sie dann die EINGABETASTE. Wenn Sie aufgefordert werden, "dirsync.exe" auszuführen, klicken Sie auf den Pfeil neben **Speichern**, klicken Sie auf **Speichern unter**, und klicken Sie dann auf **Speichern**, um die Datei im Ordner "Downloads" zu speichern. Weitere Informationen zur Installation des Tools finden Sie unter [Installieren oder Aktualisieren des Verzeichnissynchronisierungstools](http://technet.microsoft.com/library/jj151800).2.	Öffnen Sie den Ordner **Downloads**, klicken Sie mit der rechten Maustaste auf die Datei **dirsync**, und klicken Sie dann auf **Als Administrator ausführen**.
3.	Klicken Sie auf der Willkommensseite des Assistenten für das Setup der Active Directory-Synchronisierung auf **Weiter**. 
4.	Klicken Sie auf der Seite "Lizenzbedingungen" auf **Ich stimme zu**, und klicken Sie dann auf **Weiter**.
5.	Klicken Sie auf der Seite "Installationsordner auswählen" auf **Weiter**. Es kann mehrere Minuten dauern, bis die Installation abgeschlossen ist.
6.	Deaktivieren Sie auf der Seite "Abgeschlossen" die Option **Konfigurations-Assistenten jetzt starten**, und klicken Sie dann auf **Fertig stellen**.
7.	Klicken Sie auf der Startseite auf **user1**, und klicken Sie dann auf **Abmelden**.

Aktivieren Sie als Nächstes die Verzeichnissynchronisierung für Ihre Office 365 Fast Track-Testversion.

1.	Klicken Sie auf CLIENT1 auf der Seite **Office 365 Admin Center** im linken Bereich auf **Benutzer**, und klicken Sie dann auf **Aktive Benutzer**.
2.	Klicken Sie unter **Active Directory-Synchronisierung** auf **Einrichten**.
3.	Klicken Sie auf der Seite "Active Directory-Synchronisierung einrichten und verwalten" unter Schritt 3 auf **Aktivieren**.
4.	Wenn **Möchten Sie die Active Directory-Synchronisierung aktivieren?** angezeigt wird, klicken Sie auf **Aktivieren**. Danach wird **Active Directory-Synchronisierung ist aktiviert** in Schritt 3 angezeigt.
5.	Lassen Sie die Seite **Active Directory-Synchronisierung einrichten und verwalten** auf CLIENT1 geöffnet.

Als Nächstes melden Sie sich mit dem Konto "CORP\User1" auf DC1 an, und öffnen Sie eine Windows PowerShell-Eingabeaufforderung auf Administratorebene. Führen Sie nacheinander die folgenden Befehle zum Erstellen einer neuen Organisationseinheit mit dem Namen "Contoso_users" aus, und fügen Sie zwei neue Benutzerkonten für Marci Kaufman und Lynda Meyer hinzu.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Wenn Sie die einzelnen Windows PowerShell-Befehl ausführen, werden Sie aufgefordert, das neue Benutzerkennwort einzugeben. Notieren Sie sich diese Kennwörter, und bewahren Sie sie an einem sicheren Ort auf. Sie benötigen sie später.

Konfigurieren Sie anschließend die Verzeichnissynchronisierung auf DS1.

1.	Melden Sie sich mit dem Konto "CORP\User1" bei DS1 an.
2.	Geben Sie auf der **Startseite** **Directory Sync** ein.
3.	Klicken Sie mit der rechten Maustaste auf **Verzeichnissynchronisierungskonfiguration**, und klicken Sie dann auf **Als Administrator ausführen**. Dadurch wird der Konfigurations-Assistent gestartet.
4.	Klicken Sie auf der Seite **Willkommen** auf Weiter.
5.	Geben Sie auf der Seite "Microsoft Azure Active Directory-Anmeldeinformationen" die E-Mail-Adresse und das Kennwort für das Konto ein, das Sie beim Einrichten der Testversion von Office 365 Fast Track in Phase 2 erstellt haben. Klicken Sie auf "Weiter". 
6.	Geben Sie auf der Seite für die Active Directory-Anmeldeinformationen **CORP\User1** unter **Benutzername** und das Kontokennwort für "User1" unter **Kennwort** ein. Klicken Sie auf **Weiter**.
7.	Wählen Sie auf der Seite "Hybride Bereitstellung" die Option **Hybride Bereitstellung aktivieren** aus, und klicken Sie dann auf **Weiter**.
8.	Wählen Sie auf der Seite "Kennwortsynchronisierung" die Option **Kennwortsynchronisierung aktivieren** aus, und klicken Sie dann auf **Weiter**.
9.	Die Seite "Konfiguration" wird angezeigt. Wenn die Konfiguration abgeschlossen ist, klicken Sie auf **Weiter**.
10.	Klicken Sie auf der Seite "Abgeschlossen" auf **Fertig stellen**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.

Als Nächstes stellen Sie sicher, dass die Benutzerkonten in der CORP-Domäne mit Office 365 synchronisiert werden. Beachten Sie, dass es einige Stunden dauern kann, bis die Synchronisierung erfolgt.

Klicken Sie auf CLIENT1 auf der Seite **Active Directory-Synchronisierung einrichten und verwalten** auf den Link **Benutzer** in Schritt 6 auf der Seite. Wenn die Verzeichnissynchronisierung erfolgreich war, sollte etwa Folgendes angezeigt werden:

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

Die Spalte **Status** gibt an, dass das Konto über die Synchronisierung mit einer Active Directory-Domäne abgerufen wurde.

Als Nächstes wird die Office 365-Kennwortsynchronisierung mit dem Active Directory-Konto von Lynda Myer gezeigt.

1.	Wählen Sie auf CLIENT1 auf der Seite **Aktive Benutzer** das Konto **Lynda Meyer** aus.
2.	Klicken Sie in den Eigenschaften des Kontos "Lynda Meyer" unter **Zugewiesene Lizenz** auf **Bearbeiten**.
3.	Wählen Sie auf der Registerkarte **Lizenz zuweisen** einen Standort unter **Benutzerstandort einstellen** aus (z. B. Vereinigte Staaten).
4.	Wählen Sie **Microsoft Office 365 (Plan E3)** aus, und klicken Sie dann auf **Speichern**.
5.	Schließen Sie Internet Explorer.
6.	Führen Sie Internet Explorer aus, und wechseln Sie zu **http://portal.microsoftonline.com**.7.	Melden Sie sich mit Lynda Meyers Office 365-Anmeldeinformationen an. Der Benutzername lautet dann "lyndam@<*Ihr fiktiver Name*>.onmicrosoft.com". Das Kennwort ist das Kennwort für das Lynda Meyers Active Directory-Benutzerkonto.
8.	Nach der erfolgreichen Anmeldung sehen Sie die Hauptseite des Office 365-Portals mit **Let's make a difference today**.

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Diese Umgebung können Sie nun zum Testen von Office 365-Anwendungen verwenden, die die Office 365 DirSync-Funktion nutzen, oder zum Testen der DirSync-Funktionalität und Leistung von DS1.

## Zusätzliche Ressourcen

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Lösungen mit Office-Server und der Cloud](http://technet.microsoft.com/library/dn262744.aspx)

[Einrichten einer Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Einrichten einer SharePoint-Intranetfarm in einer Hybridcloud zu Testzwecken](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Einrichten einer webbasierten Branchenanwendung in einer Hybridcloud zu Testzwecken](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Einrichten einer simulierten Hybridcloudumgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Testumgebungen für Azure-Hybridclouds](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO2-->