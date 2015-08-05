<properties 
	pageTitle="Anleitungen für die Zertifikaterneuerung für Office 365- und Azure AD-Benutzer." 
	description="In diesem Artikel wird für Office 365-Benutzer erläutert, wie Probleme mit E-Mails behoben werden, die sie zum Erneuern eines Zertifikats auffordern." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="billmath"/>


# Erneuern von Verbundzertifikaten für Office 365 und Azure AD

Wenn Sie eine E-Mail oder eine Portalbenachrichtigung erhalten haben, in der Sie aufgefordert werden, Ihr Zertifikat für Office 365 zu erneuern, lesen Sie diesen Artikel, um das Problem zu beheben und zu verhindern, dass es erneut auftritt. Dieser Artikel setzt voraus, dass Sie AD FS als Verbundserver verwenden.

## Überprüfen, ob Schritte erforderlich sind

Wenn Sie AD FS 2.0 oder höher verwenden, aktualisieren Office 365 und Azure AD das Zertifikat automatisch, bevor es abläuft. Sie müssen keine manuellen Schritte ausführen bzw. kein Skript als geplante Aufgabe ausführen. Damit dies funktioniert, müssen die beiden folgenden AD FS-Konfigurationseinstellungen wirksam sein:

- Die AD FS-Eigenschaft "AutoCertificateRollover" muss auf "True" festgelegt sein. Damit wird angegeben, dass AD FS automatisch neue Tokensignatur- und Tokenverschlüsselungszertifikate generiert, bevor die alten ablaufen.
	- Wenn der Wert "False" ist, verwenden Sie benutzerdefinierte Zertifikateinstellungen. [Hier](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) finden Sie umfassende Anleitungen.
- Die Verbundmetadaten müssen für das öffentliche Internet verfügbar sein.
	
	So wird dies überprüft:

	- Überprüfen Sie durch Ausführen des folgenden Befehls in einem PowerShell-Befehlsfenster auf dem primären Verbundserver, ob die AD FS-Installation den automatischen Zertifikat-Rollover verwendet:

	`PS C:\> Get-ADFSProperties`

(Beachten Sie, dass Sie bei Verwendung von AD FS 2.0 zuerst "Add-Pssnapin Microsoft.Adfs.Powershell" ausführen müssen.)

Überprüfen Sie, ob die Verbundmetadaten öffentlich zugänglich sind, indem Sie von einem Computer im öffentlichen Internet (außerhalb des Unternehmensnetzwerks) zur folgenden URL navigieren:

https://<your_FS_name>/federationmetadata/2007-06/federationmetadata.xml

Dabei wird `<your_FS_name> ` durch den Hostnamen des Verbunddiensts Ihrer Organisation ersetzt, z. B. "fs.contoso.com". Wenn Sie diese beiden Einstellungen erfolgreich überprüfen können, müssen Sie nichts weiter tun.

## Wenn die AutoCertificateRollover-Eigenschaft auf "False" festgelegt ist

Wenn die AutoCertificateRollover-Eigenschaft auf "False" festgelegt ist, verwenden Sie keine standardmäßigen AD FS-Zertifikateinstellungen. Die häufigste Ursache hierfür ist, dass Ihre Organisation AD FS-Zertifikate verwaltet, die von einer Organisationszertifizierungsstelle registriert werden. In diesem Fall müssen Sie Ihre Zertifikate selbst erneuern und aktualisieren. Verwenden Sie die Anleitungen [hier](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

## Wenn Ihre Metadaten nicht öffentlich zugänglich sind
Wenn die AutocertificateRollover-Einstellung "True" ist, aber die Verbundmetadaten nicht öffentlich verfügbar sind, verwenden Sie das folgende Verfahren, um sicherzustellen, dass Ihre Zertifikate sowohl lokal als auch in der Cloud aktualisiert werden:

### Stellen Sie sicher, dass Ihr AD FS-System ein neues Zertifikat generiert hat. 

- Stellen Sie sicher, dass Sie am primären AD FS-Server angemeldet sind.
- Überprüfen Sie die aktuellen Signaturzertifikate in AD FS, indem Sie ein PowerShell-Befehlsfenster öffnen und den folgenden Befehl ausführen: 

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(Beachten Sie, dass Sie bei Verwendung von AD FS 2.0 zuerst "Add-Pssnapin Microsoft.Adfs.Powershell" ausführen müssen.)


- Sehen Sie sich in der Ausgabe des Befehls die aufgeführten Zertifikate an. Wenn AD FS ein neues Zertifikat generiert hat, sollten Sie zwei Zertifikate in der Ausgabe sehen: Bei einem ist der IsPrimary-Wert "True" und das NotAfter-Datum ist innerhalb von 5 Tagen, und bei einem ist der IsPrimary-Wert "False" und das NotAfter-Datum ist etwa ein Jahr in der Zukunft.
	
- Wenn nur ein Zertifikat angezeigt wird und das NotAfter-Datum innerhalb von 5 Tagen ist, müssen Sie ein neues Zertifikat generieren, indem Sie die folgenden Schritte ausführen.

- Um ein neues Zertifikat zu generieren, führen Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung aus:`PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- Überprüfen Sie das Update, indem Sie den folgenden Befehl erneut ausführen: PS C:\>Get-ADFSCertificate –CertificateType token-signing
- Um die Eigenschaften der Office 365-Verbundvertrauensstellung manuell zu aktualisieren, gehen Sie folgendermaßen vor.

Nun sollten zwei Zertifikate aufgeführt werden, bei denen eines ein NotAfter-Datum in etwa einem Jahr in der Zukunft hat und bei dem der IsPrimary-Wert "False" ist.


### Um die Eigenschaften der Office 365-Verbundvertrauensstellung manuell zu aktualisieren, gehen Sie folgendermaßen vor.

1.	Öffnen Sie das Microsoft Azure Active Directory-Modul für Windows PowerShell.
2.	Führen Sie $cred=Get-Credential aus. Wenn Sie dieses Cmdlet zur Eingabe von Anmeldeinformationen auffordert, geben Sie die Anmeldeinformationen Ihres Clouddienstadministrators ein.
3.	Führen Sie Connect-MsolService –Credential $cred aus. Dieses Cmdlet verbindet Sie mit dem Clouddienst. Sie müssen einen Kontext erstellen, der Sie mit dem Clouddienst verbindet, bevor Sie andere Cmdlets ausführen, die vom Tool installiert werden.
4.	Wenn Sie diese Befehle auf einem Computer ausführen, der nicht der primäre AD FS-Verbundserver ist, führen Sie Set-MSOLAdfscontext -Computer <AD FS primary server> aus, wobei <AD FS primary server> der interne FQDN des primären AD FS-Servers ist. Dieses Cmdlet erstellt einen Kontext, der Sie mit AD FS verbindet. 
5.	Führen Sie Update-MSOLFederatedDomain –DomainName <domain> aus. Dieses Cmdlet aktualisiert die Einstellungen von AD FS im Clouddienst und konfiguriert die Vertrauensstellung zwischen beiden.

>[AZURE.NOTE]Wenn Sie mehrere Domänen der obersten Ebene unterstützen müssen, z. B. "contoso.com" und "fabrikam.com", müssen Sie den SupportMultipleDomain-Switch mit den Cmdlets verwenden. Weitere Informationen finden Sie unter "Unterstützen mehrerer Domänen auf oberster Ebene". Stellen Sie abschließend sicher, dass alle Webanwendungsproxy-Server mit dem Rollup [von Mai 2014 für Windows Server](http://support.microsoft.com/kb/2955164) aktualisiert wurden. Andernfalls können sich die Proxys möglicherweise nicht mit dem neuen Zertifikat aktualisieren, was zu einem Ausfall führt.

<!---HONumber=July15_HO4-->