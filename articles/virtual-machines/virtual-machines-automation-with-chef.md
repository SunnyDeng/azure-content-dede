<properties 
   pageTitle="Automatisieren der Bereitstellung von Azure Virtual Machine mit Chef" 
   description="Lernen Sie die Automatisierung von Azure Virtual Machine mit Chef kennen" 
   services="virtual-machines" 
   documentationCenter="" 
   authors="diegoviso" 
   manager="timlt" 
   editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" 
ms.tgt_pltfrm="vm-multiple" 
ms.devlang="na" 
ms.topic="article" 
ms.date="05/19/2015" 
ms.author="diviso"/>

# Automatisieren der Bereitstellung von Azure Virtual Machine mit Chef

Chef ist ein hervorragendes Tool zur Automatisierung und für Konfigurationen mit gewünschten Zuständen.

Mit unserer aktuellen Veröffentlichung der Cloud-API bietet Chef eine problemlose Integration in Azure und ermöglicht es Ihnen, bietet Ihnen die Möglichkeit, Konfigurationszustände über einen einzelnen Befehl bereitzustellen und zu implementieren.

In diesem Artikel wird veranschaulicht, wie die Chef-Umgebung für die Bereitstellung von Azure Virtual Machines eingerichtet wird. Zudem werden Sie schrittweise durch die Erstellung einer Richtlinie und die anschließende Bereitstellung dieser Richtlinie auf einem virtuellen Azure-Computer geführt.

Lassen Sie uns beginnen!

## Grundlagen zu Chef

Bevor Sie beginnen, wird empfohlen, dass Sie sich mit den grundlegenden Konzepten von Chef vertraut machen. <a href="http://www.chef.io/chef" target="_blank">Hier</a> steht sehr gutes Material zur Verfügung, und ich empfehle Ihnen, es schnell durchzulesen, bevor Sie mit dieser exemplarischen Vorgehensweise beginnen. Die Grundlagen werden vor dem Start dennoch kurz zusammengefasst.

Das folgende Diagramm zeigt die allgemeine Chef-Architektur.

![][2]

Chef weist drei Hauptkomponenten auf. **Chef-Server, Chef-Client (Knoten)** und **Chef-Arbeitsstation.**

Der **Chef-Server** ist unser Verwaltungspunkt, für den es zwei Optionen gibt: eine gehostete oder eine lokale Lösung. Wir werden eine gehostete Lösung verwenden.

Der **Chef-Client (Knoten)** ist der Agent, der sich auf den Servern befindet, die Sie verwalten.

Die **Chef-Arbeitsstation** ist unsere Verwaltungsarbeitsstation, auf der die Richtlinien erstellt und Verwaltungsbefehle ausgeführt werden. Wir führen den Befehl **“knife”** auf der Chef-Arbeitsstation aus, um unsere Infrastruktur zu verwalten.

Es gibt zudem das Konzept von so genannten "Rezeptbüchern" (Cookbooks) und "Rezepten". Hierbei handelt es sich um Richtlinien, die wir definieren und auf unsere Server anwenden.

## Vorbereiten der Arbeitsstation

Zunächst bereiten wird die Arbeitsstation vor. Ich verwende eine Windows-Standardarbeitsstation. Wir müssen ein Verzeichnis zum Speichern unserer Konfigurationsdateien und Rezeptbücher erstellen.

Erstellen Sie zunächst ein Verzeichnis namens **C:\\chef**.

Erstellen Sie dann ein zweites Verzeichnis **c:\\chef\\cookbooks**.

Wir müssen jetzt unsere Azure-Einstellungsdatei herunterladen, damit Chef mit unserem Azure-Abonnement kommunizieren kann.

Laden Sie die Veröffentlichungseinstellungen herunter unter: <a href="https://manage.windowsazure.com/publishsettings/" target="_blank">https://manage.windowsazure.com/publishsettings/</a>

Speichern Sie die Datei mit Veröffentlichungseinstellungen in **C:\\chef**.

##Erstellen eines verwalteten Chef-Kontos

Registrieren Sie sich für ein gehosteten Chef-Konto: <a href="https://manage.chef.io/signup" target="_blank">https://manage.chef.io/signup</a>

Bei der Anmeldung werden Sie aufgefordert, eine neue Organisation zu erstellen.

![][3]

Sobald Ihre Organisation erstellt wurde, laden Sie das Starterkit herunter.

![][4]

**Hinweis**: Wenn Sie eine Meldung mit der Warnung erhalten, dass Ihre Schlüssel zurückgesetzt werden, ist es in Ordnung, wenn Sie fortfahren, da wir bisher keine vorhandene Infrastruktur konfiguriert haben.

Die ZIP-Datei dieses Starterkits enthält die Konfigurationsdateien und Schlüssel für Ihre Organisation.

##Konfigurieren der Chef-Arbeitsstation

Extrahieren Sie den Inhalt der Datei "chef-starter.zip" im Ordner **C:\\chef**.

Kopieren Sie alle Dateien unter **chef-starter\\chef-repo.chef** in den Ordner **c:\\chef**.

Ihr Verzeichnis sollte in etwa wie folgt aussehen:

![][5]

Im Stammverzeichnis von "c:\\chef" sollten sich jetzt 4 Dateien, einschließlich der Azure-Veröffentlichungsdatei befinden.

Die PEM-Dateien enthalten die privaten Schlüssel für die Organisation und Administration für die Kommunikation, während die Datei **knife.rb** die "knife"-Konfiguration enthält. Wir müssen die Datei **knife.rb** bearbeiten.

Öffnen Sie die Datei im Editor Ihrer Wahl, und ändern Sie "cookbook\_path", indem Sie "/../" aus dem Pfad entfernen, damit dieser wie folgt aussieht:

	cookbook_path  ["#{current_dir}/cookbooks"]

Fügen Sie zudem die folgende Zeile hinzu, die den Namen Ihrer Azure-Datei für die Veröffentlichungseinstellungen widerspiegelt.

	knife[:azure_publish_settings_file] = "yourfilename.publishsettings" 

Die Datei "knife.rb" sollte jetzt etwa wie folgt aussehen:

![][6]

Diese Zeilen stellen sicher, dass Knife auf unser "cookbooks"-Verzeichnis unter "c:\\chef\\cookbooks" verweist und auch unsere Azure-Datei für die Veröffentlichungseinstellungen im Rahmen der Azure-Vorgänge verwendet.

## Installieren des Chef Development Kit

Laden Sie als Nächstes das ChefDK (Chef Development Kit) zum Einrichten der Chef-Arbeitsstation herunter, und installieren Sie es anschließend.

<a href="http://downloads.getchef.com/chef-dk/windows" target="_blank">http://downloads.getchef.com/chef-dk/windows</a>

![][7]

Das ist ganz einfach. Lassen Sie es in seinem Standardverzeichnis "c:\\opscode" installieren. Die Installation dauert ca. 10 Minuten.

Vergewissern Sie sich, dass die PATH-Variable Einträge für "C:\\opscode\\chefdk\\bin;C:\\opscode\\chefdk\\embedded\\bin;c:\\users\\yourusername.chefdk\\gem\\ruby\\2.0.0\\bin" enthält.

Wenn sie nicht vorhanden sind, stellen Sie sicher, dass Sie diese Pfade hinzufügen!

**BEACHTEN SIE, DASS DIE REIHENFOLGE DER PFADE WICHTIG IST!** Wenn die "opscode"-Pfade nicht in der richtigen Reihenfolge angegeben sind, können Probleme auftreten.

Starten Sie die Arbeitsstation neu, bevor Sie fortfahren.

Als Nächstes wird die Azure-Erweiterung von Knife installiert. Dadurch erhält Knife das Azure-Plug-In.

Führen Sie den folgenden Befehl aus:

	chef gem install knife-azure ––pre

**Hinweis**: Das Argument "-pre" stellt sicher, dass Sie die neueste RC-Version des Azure-Plug-Ins für knife erhalten, das Zugriff auf den neuesten API-Satz bietet.

Voraussichtlich werden gleichzeitig auch eine Reihe von Abhängigkeiten installiert.

![][8]


Um sicherzustellen, dass alles richtig konfiguriert ist, führen Sie Folgendes aus:

	knife azure image list

Wenn alles richtig konfiguriert ist, wird eine Liste der verfügbaren Azure-Images angezeigt.

Herzlichen Glückwunsch. Die Arbeitsstation wurde eingerichtet.

##Erstellen eines Cookbooks

Ein Cookbook wird von Chef dazu verwendet, um eine Reihe von Befehlen zu definieren, die auf dem verwalteten Client ausgeführt werden sollen. Die Cookbook-Erstellung ist unkompliziert, und wir verwenden den Befehl "chef generate cookbook", um unsere Cookbook-Vorlage zu erstellen. Ich nenne mein Cookbook "Webserver", da ich eine Richtlinie verwenden möchte, die IIS automatisch bereitstellt.

Führen Sie unter dem Verzeichnis "C:\\Chef" den folgenden Befehl aus:

	chef generate cookbook webserver

Dadurch wird eine Reihe von Dateien im Verzeichnis **C:\\Chef\\cookbooks\\webserver** generiert. Wir müssen jetzt den Satz von Befehlen definieren, die unser Chef-Client auf dem verwalteten virtuellen Computer ausführen soll.

Die Befehle werden in der Datei **default.rb.** gespeichert. In dieser Datei werden eine Reihe von Befehlen definiert, die IIS installieren, starten und eine Vorlagendatei in den Ordner "wwwroot" kopieren.

Ändern Sie die Datei **C:\\chef\\cookbooks\\webserver\\recipes\\default.rb**, und fügen Sie die folgenden Zeilen hinzu:

	powershell_script 'Install IIS' do
 		action :run
 		code 'add-windowsfeature Web-Server'
	end

	service 'w3svc' do
 		action [ :enable, :start ]
	end

	template 'c:\inetpub\wwwroot\Default.htm' do
 		source 'Default.htm.erb'
 		rights :read, 'Everyone'
	end

Speichern Sie die Datei, sobald Sie fertig sind.

## Erstellen einer Vorlage

Wie bereits zuvor erwähnt, müssen wir eine Vorlagendatei generieren, die als unsere "default.html"-Standardseite verwendet wird.

Führen Sie den folgenden Befehl aus, um die Vorlage zu erstellen:

	chef generate template webserver Default.htm

Wechseln Sie nun zur Datei **C:\\chef\\cookbooks\\webserver\\templates\\default\\Default.htm.erb**, und bearbeiten Sie die Datei.

Fügen Sie etwas einfachen HTML-Code ("Hello World") hinzu, und speichern Sie die Datei.

## Hochladen des Cookbooks auf den Chef-Server

In diesem Schritt nehmen wir eine Kopie des Cookbooks, das wir auf unserem lokalen Computer erstellt haben, und laden es auf den von Chef gehosteten Server hoch. Sobald es hochgeladen ist, wird das Cookbook auf der Registerkarte für die Richtlinie angezeigt.

	knife cookbook upload webserver

![][9]

## Bereitstellen eines virtuellen Computers mit Knife Azure

Wir stellen jetzt einen virtuellen Azure-Computer bereit und wenden das Cookbook "Webserver" an, das unseren IIS-Webdienst und die Standardwebseite installiert.

Verwenden Sie hierzu den Befehl**knife azure server create**.

Hier folgt ein Beispiel für den Befehl:

	knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Die Parameter sind selbsterklärend. Ersetzen Sie Ihre bestimmten Variablen, und führen Sie den Befehl dann aus.

**Hinweis**: Über die Befehlszeile werden auch die Filterregeln für mein Endpunktnetzwerk mithilfe des Parameters "–tcp-endpoints" automatisiert. Ich habe die Ports 80 und 3389 geöffnet, um den Zugriff auf meine Webseite und die RDP-Sitzung zu gestatten.

Nachdem Sie den Befehl ausgeführt haben, wechseln Sie zum Azure-Verwaltungsportal, wo Sie die Bereitstellung des virtuellen Computers verfolgen können.

![][13]

Wechseln Sie dann wieder zur Eingabeaufforderung:

![][10]

Nachdem die Bereitstellung abgeschlossen ist, sollten wir in der Lage sein, über den Port 80 eine Verbindung zum Webdienst herstellen zu können, das wir den Port beim Bereitstellen des virtuellen Computers mit dem Befehl "knife azure" geöffnet haben. Da dieser virtuelle Computer der einzige in meinem Cloud-Dienst ist, wird er mit der URL des Cloud-Diensts verbunden.

![][11]

Wie Sie sehen können, bin ich beim HTML-Code recht kreativ gewesen :)

Vergessen Sie nicht, dass wir die Verbindung auch über eine RDP-Sitzung des Azure-Verwaltungsportals über den Port 3389 herstellen können.

Ich hoffe, diese Informationen waren hilfreich. Starten Sie noch heute mit Azure, um Ihre Infrastruktur als Code umzusetzen!


<!--Image references-->
[2]: ./media/virtual-machines-automation-with-chef/2.png
[3]: ./media/virtual-machines-automation-with-chef/3.png
[4]: ./media/virtual-machines-automation-with-chef/4.png
[5]: ./media/virtual-machines-automation-with-chef/5.png
[6]: ./media/virtual-machines-automation-with-chef/6.png
[7]: ./media/virtual-machines-automation-with-chef/7.png
[8]: ./media/virtual-machines-automation-with-chef/8.png
[9]: ./media/virtual-machines-automation-with-chef/9.png
[10]: ./media/virtual-machines-automation-with-chef/10.png
[11]: ./media/virtual-machines-automation-with-chef/11.png
[13]: ./media/virtual-machines-automation-with-chef/13.png


<!--Link references-->

<!---HONumber=August15_HO6-->