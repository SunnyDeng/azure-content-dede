<properties 
	pageTitle="Office 365-Verzeichnissynchronisierung (DirSync) in einer hybridcloud zu Testzwecken einrichten" 
	description="Erfahren Sie, wie für ein Office 365-Verzeichnissynchronisierung (DirSync)-Serverkonfiguration in einer hybridcloud für IT Pro oder Entwicklung zu testen." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

# Office 365-Verzeichnissynchronisierung (DirSync) in einer hybridcloud zu Testzwecken einrichten

Dieses Thema führt Sie durch die Erstellung einer hybriden Cloud-Umgebung zum Testen von Office 365-Verzeichnissynchronisierung (DirSync) mit Kennwortsynchronisierung in Microsoft Azure gehostet. Hier ist die resultierende Konfiguration.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_3.png)
 
Diese Konfiguration simuliert einen Dirsync-Server in Azure-Produktionsumgebung von Ihrem Speicherort im Internet. Es besteht aus:

- Eine vereinfachte im lokalen Netzwerk (Corpnet-Subnetz).
- Ein standortübergreifendes virtuelles Netzwerk in Azure (TestVNET) gehostet.
- Eine Standort-zu-Standort-VPN-Verbindung.
- Ein Office 365 Fast Track-Testabonnement.
- Ein Dirsync-Server und sekundärer Domänencontroller im virtuellen Netzwerk TestVNET.

Diese Konfiguration bietet eine Grundlage und allgemeinen Ausgangspunkt erarbeitet, die aus dem können Sie:

- Entwickeln und Testen von Anwendungen für Office 365, die Synchronisierung mit lokalen Active Directory-Domäne mithilfe der Kennwortsynchronisierung abhängen.
- Durchführen Sie diese Cloud-basierten IT-Arbeitsauslastung Tests.

Es gibt drei Hauptphasen diese Hybrid Cloud-Testumgebung einrichten:

1.	Richten Sie die Hybrid Cloud-Umgebung zum Testen.
2.	Konfigurieren Sie die Testversion von Office 365 Fast Track.
3.	Konfigurieren Sie den DirSync-Server (DS1).

Wenn Sie noch nicht über ein Azure-Abonnement verfügen, Sie können registrieren Sie sich für eine kostenlose Testversion an [versuchen Azure](http://azure.microsoft.com/pricing/free-trial/). Wenn Sie ein MSDN-Abonnement verfügen, finden Sie unter [Azure-Leistungen für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1: Einrichten der Hybrid Cloud-Umgebung

Verwenden Sie die Anweisungen im Thema [Einrichten einer hybriden Cloud-Umgebung zum Testen](virtual-networks-setup-hybrid-cloud-environment-testing.md) . Da das Vorhandensein des APP1-Servers im Subnetz des Unternehmensnetzwerks in dieser Testumgebung nicht erforderlich ist, können Sie jetzt herunterfahren.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_1.png)

## Phase 2: Konfigurieren Sie die Testversion von Office 365 Fast Track

Um Ihre Testversion von Office 365 Fast Track zu starten, benötigen Sie einen fiktiven Firmennamen und ein Microsoft-Konto. Es wird empfohlen, dass Sie eine Variante des Unternehmensnamens Contoso für den Namen Ihres Unternehmens, einem fiktiven Unternehmen, die in Microsoft-Beispielinhalt verwendet wird, aber nicht erforderlich unbedingt.

Melden Sie sich als Nächstes ein neues Microsoft-Konto an. Wechseln Sie zu **http://outlook.com** und erstellen Sie ein Konto mit einer e-Mail-Adresse wie user123@outlook.com. Sie können für eine Testversion von Office 365 Fast Track mit diesem Konto anmelden.

Melden Sie sich als Nächstes für eine neue Office 365 Fast Track-Testversion an.

1.	Melden Sie sich mit den Anmeldeinformationen des CORP\User1 CLIENT1 an.
2.	Öffnen Sie Internet Explorer, und wechseln Sie zu **http://fasttrack.office.com**.
3.	Klicken Sie auf **erste Schritte mit Fast Track**.
4.	Klicken Sie auf die erste Schritte mit Fast Track-Seite unter **zunächst registrieren Sie sich für eine Testversion von Office 365**, klicken Sie auf **für Unternehmen, registrieren Sie sich hier**.
5.	Geben Sie auf der Seite Schritt 1 der Seite in Ihrem neue Microsoft-Konto angeben **geschäftliche e-Mail-Adresse**, und klicken Sie dann auf **Weiter**.
6.	Geben Sie den Namen der ersten Office 365-Konto auf der Seite Schritt2 in das erste Feld der fiktiven Firmennamen und dann ein Kennwort. Notieren Sie die resultierende e-Mail-Adresse (z. B. user123@contoso123.onmicrosoft.com) und das Kennwort an einem sicheren Ort. Sie benötigen diese Informationen, um das Tool Active Directory-Synchronisierung in Phase 3 Konfigurations-Assistenten abgeschlossen haben. Klicken Sie auf **Weiter**.
7.	Geben Sie auf der Seite Schritt 3 die Telefonnummer Ihres Telefons der Message-fähiger Text-Endgeräte oder smart, und klicken Sie dann auf **SMS**.
8.	Nachdem Sie die SMS-Benachrichtigung auf Ihrem Mobiltelefon erhalten, geben Sie den Überprüfungscode, und klicken Sie dann auf **Mein Konto erstellen**. 
9.	Wenn Office 365 fertig ist Ihr Konto erstellt ist, klicken Sie auf **Sie startbereit sind**.
10.	Die wichtigste Office 365-Portalseite sollte jetzt angezeigt werden. Klicken Sie in der oberen Menüband **Admin**, und klicken Sie dann auf **Office 365**. Die Office 365 Admin Center-Seite angezeigt wird. Lassen Sie auf dieser Seite auf "client1" geöffnet.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_2.png)

## Phase 3: Konfigurieren Sie den DirSync-Server (DS1)

Erstellen Sie zunächst einen virtuellen Computer in Azure für DS1 mit diesen Befehlen an der Eingabeaufforderung von Azure PowerShell auf dem lokalen Computer. Vor dem Ausführen dieser Befehle füllen Sie die Variablenwerte und entfernen die Zeichen < und >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"	
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Schließen Sie dann den virtuellen Computer DS1.

1.	Klicken Sie auf der Seite virtuelle Computer von Azure-Verwaltungsportal auf **ausführen** in der Statusspalte für den virtuellen Computer DS1.
2.	Klicken Sie in der Taskleiste auf **Connect**. 
3.	Wenn Sie dazu aufgefordert werden, um DS1.rdp zu öffnen, klicken Sie auf **öffnen**.
4.	Wenn ein Meldungsfeld Remotedesktopverbindung angezeigt wird, klicken Sie auf **Connect**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie:
	- Name: **CORP\User1**
	- Kennwort: [Kennwort für User1]
6.	Wenn das Meldungsfeld Remotedesktopverbindung verweisen auf Zertifikate angezeigt wird, klicken Sie auf **Ja**.

Als Nächstes konfigurieren Sie eine Windows-Firewall-Regel zum Zulassen von Datenverkehr zum Testen der Konnektivität. Ein auf Windows PowerShell-Eingabeaufforderung auf DS1 führen Sie diese Befehle.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Der Ping-Befehl sollte in vier Antworten von IP-Adresse 10.0.0.1 führen.

Als Nächstes installieren Sie .NET 3.5 auf DS1 mit dem folgenden Befehl an der Windows PowerShell-Eingabeaufforderung.

	Add-WindowsFeature NET-Framework-Core

Installieren Sie dann die Verzeichnissynchronisierung auf DS1.

1.	Ausführen von Internet Explorer, geben **http://go.microsoft.com/fwlink/?LinkID=278924** in die Adressleiste ein, und drücken Sie dann die EINGABETASTE. Aufforderung zum Ausführen von dirsync.exe, klicken Sie auf den Pfeil neben **speichern**, klicken Sie auf **speichern unter**, und klicken Sie dann auf **speichern** zum Speichern der Datei im Ordner "Downloads". Weitere Informationen zur Installation des Tools finden Sie unter [installieren oder Aktualisieren des Verzeichnissynchronisierungstools](http://technet.microsoft.com/library/jj151800).
2.	Öffnen der **Downloads** Ordner mit der rechten Maustaste die **Dirsync** Datei, und klicken Sie dann auf **als Administrator ausführen**.
3.	Klicken Sie auf der Seite Willkommen des Assistenten für die Einrichtung der Synchronisierung von Active Directory auf **Weiter**. 
4.	Klicken Sie auf der Seite "Lizenzbedingungen" auf **ich stimme**, und klicken Sie dann auf **Weiter**.
5.	Klicken Sie auf der Seite Wählen Sie Ordner Installation **Weiter**. Zum Abschließen der Installation mehrere Minuten dauern.
6.	Deaktivieren Sie auf der Seite Fertig **Konfigurations-Assistenten jetzt starten**, und klicken Sie dann auf **Fertig stellen**.
7.	Klicken Sie auf der Startseite auf **user1**, und klicken Sie dann auf **Abmelden**.

Aktivieren Sie als Nächstes Verzeichnissynchronisierung für Ihre Office 365 Fast Track-Testversion.

1.	Auf CLIENT1 auf die **Office 365 Administrationscenter** Seite im linken Bereich klicken Sie auf **Benutzer**, und klicken Sie dann auf **aktive Benutzer**.
2.	Für **Active Directory-Synchronisierung**, klicken Sie auf **einrichten**.
3.	Auf dem Einrichten und Verwalten von Active Directory-Synchronisierung-Seite, in Schritt 3, klicken Sie auf **aktivieren**.
4.	Wenn **möchten Sie die Active Directory-Synchronisierung aktivieren?**, klicken Sie auf **aktivieren**. Nachdem Sie dies tun, **Active Directory-Synchronisierung aktiviert** in Schritt 3 wird angezeigt.
5.	Lassen Sie die **festlegen einrichten und Verwalten von Active Directory-Synchronisierung** Seite auf "client1" geöffnet.

Als Nächstes melden Sie sich mit dem Konto CORP\User1 auf DC1, und öffnen Sie eine auf Windows PowerShell-Eingabeaufforderung. Führen Sie diese Befehle nacheinander zum Erstellen einer neuen Organisationseinheit namens "Contoso_users", und fügen Sie zwei neue Benutzerkonten für Marci Kaufman und Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Wenn Sie jede Windows PowerShell-Befehl ausführen, werden Sie für das neue Kennwort des Benutzers aufgefordert. Diese Kennwörter zu erfassen und an einem sicheren Ort aufbewahren. Diese werden später benötigt werden.

Konfigurieren Sie anschließend die Verzeichnissynchronisierung auf DS1.

1.	Melden Sie sich mit dem Konto CORP\User1 in DS1 an.
2.	Auf der **Start** geben **Verzeichnissynchronisierung**.
3.	Mit der rechten Maustaste **Verzeichnissynchronisierungskonfiguration**, und klicken Sie dann auf **als Administrator ausführen**. Dadurch wird der Konfigurations-Assistent gestartet.
4.	Klicken Sie auf der Seite "Willkommen" auf **Weiter**.
5.	Geben Sie die e-Mail-Adresse und das Kennwort beim Einrichten der Testversion von Office 365 Fast Track in Phase 2 erstellten Konto auf der Seite Microsoft Azure Active Directory-Anmeldeinformationen. Klicken Sie auf Weiter. 
6.	Geben Sie auf der Seite Active Directory-Anmeldeinformationen **CORP\User1** in **Benutzername** und das Kennwort für das User1 in **Kennwort**. Klicken Sie auf **Weiter**.
7.	Wählen Sie auf der Seite Hybridbereitstellung **Hybridbereitstellung aktivieren**, und klicken Sie dann auf **Weiter**.
8.	Wählen Sie auf der Seite Kennwortsynchronisierung **Kennwortsynchronisierung aktivieren**, und klicken Sie dann auf **Weiter**.
9.	Die Seite Konfiguration angezeigt. Wenn die Konfiguration abgeschlossen ist, klicken Sie auf **Weiter**.
10.	Klicken Sie auf der Seite Fertig **Fertig stellen**. Klicken Sie bei der entsprechenden Aufforderung auf **OK**.

Als Nächstes stellen Sie sicher, dass die Benutzerkonten in der CORP-Domäne zu Office 365 synchronisiert werden. Beachten Sie, dass ein paar Stunden dauert, bevor die Synchronisierung erfolgt.

Auf CLIENT1 auf die **legen einrichten und Verwalten von Active Directory-Synchronisierung** auf der **Benutzer** Link in Schritt 6 dieser Seite. Wenn die Verzeichnissynchronisierung erfolgreich war, sollte etwa wie folgt angezeigt werden.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_4.png)

Die **Status** Spalte gibt an, dass das Konto über die Synchronisierung mit Active Directory-Domäne abgerufen wurde.

Führen Sie dann die Office 365-Kennwortsynchronisierung mit Lynda Myer Active Directory-Konto vor.  

1.	Auf CLIENT1 auf die **aktive Benutzer** Seite auf die **Lynda Meyer** Konto.
2.	In den Eigenschaften des Kontos Lynda Meyer unter **zugewiesene Lizenz**, klicken Sie auf **bearbeiten**.
3.	In der **Lizenz zuweisen** Registerkarte, wählen Sie einen Speicherort im **Benutzerstandort einstellen** (z. B. Vereinigte Staaten).
4.	Wählen Sie **Microsoft Office 365 planen E3**, und klicken Sie dann auf **speichern**.
5.	Schließen Sie Internet Explorer.
6.	Führen Sie Internet Explorer, und wechseln Sie zu **http://portal.microsoftonline.com**. 
7.	Melden Sie sich mit Lynda Meyers Office 365-Anmeldeinformationen an. Der Benutzernamen werden Lyndam @<*der fiktiven Namen*>. onmicrosoft.com. Das Kennwort ist das Kennwort für das Lynda Dressler Active Directory-Benutzerkonto.
8.	Nach der erfolgreichen Anmeldung sehen Sie die wichtigste Office 365-Portal-Seite mit **nehmen Sie heute noch einen Unterschied**.

Dies ist die aktuelle Konfiguration.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_3.png)
 
Diese Umgebung ist nun bereit für Sie zum Testen des Office 365-Anwendungen, die auf Office 365 DirSync-Funktion basieren oder DirSync-Funktionalität und Leistung von DS1 testen.

## Zusätzliche Ressourcen

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Lösungen mit Office-Server und die Cloud](http://technet.microsoft.com/library/dn262744.aspx)

[Einrichten einer Hybrid Cloud-Umgebung zu Testzwecken](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Einrichten einer simulierten Hybrid Cloud-Umgebung zu Testzwecken](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

<!--HONumber=47-->
