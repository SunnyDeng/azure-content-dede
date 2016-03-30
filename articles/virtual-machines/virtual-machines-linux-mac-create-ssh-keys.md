<properties
	pageTitle="Erstellen von SSH-Schlüsseln unter Linux und Mac | Microsoft Azure"
	description="Generieren und Verwenden von SSH-Schlüsseln unter Linux und Mac für die Ressourcen-Manager- und klassischen Bereitstellungsmodelle in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/13/2016"
	ms.author="v-livech"/>

# Erstellen von SSH-Schlüsseln unter Linux und Mac für Linux-VMs in Azure

In diesem Thema:

1. Erstellen eines kennwortgeschützten öffentlichen und privaten Schlüsselpaars per `ssh-keygen`
2. Erstellen der Datei `~/.ssh/config` zum Beschleunigen der Anmeldung und Aktivieren wichtiger Standardeinstellungen für Sicherheit und Konfiguration
3. Anmelden an einer Linux- oder BSD-VM in Azure per SSH

## Voraussetzungen

Ein Azure-Konto([kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)) und ein Linux- oder Macintosh-Terminal mit installierten SSH-Toolkits. Versetzen Sie die Befehlszeilenschnittstelle in den Ressourcenmodus, indem Sie `azure config mode arm` eingeben.

## Einführung

Die Verwendung öffentlicher und privater SSH-Schlüssel ist der sicherste **und** einfachste Weg zum Anmelden an Ihren Linux-Servern. [Public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) (Public-Key-Kryptosystem) ist ein Weg mit deutlich mehr Sicherheit für die Anmeldung an Ihrer Linux- oder BSD-VM in Azure als Kennwörter, die viel leichter einem Brute-Force-Angriff unterzogen werden können. Sie können Ihren öffentlichen Schlüssel an beliebige Personen weitergeben, während Ihr privater Schlüssel ausschließlich für Ihren Besitz bestimmt ist (bzw. Ihre lokale Sicherheitsinfrastruktur).

In diesem Artikel werden Dateien mit Schlüsseln im *ssh-rsa*-Format erstellt, da diese für Bereitstellungen im Resource Manager empfohlen werden und im [Portal](https://portal.azure.com) sowohl für klassische Bereitstellungen als auch für Resource Manager-Bereitstellungen benötigt werden.

> [AZURE.NOTE] Wenn Sie sich an Ihren VMs über einen Windows-Computer anmelden, helfen Ihnen die Informationen unter [Verwenden von SSH-Schlüsseln unter Windows](virtual-machines-linux-ssh-from-windows.md) weiter.

## Kurze Befehlsliste

Ersetzen Sie in den folgenden Befehlsbeispielen die Werte zwischen &lt; und &gt; durch die Werte aus Ihrer eigenen Umgebung.

```bash
[username@fedora22 ~]$ ssh-keygen -t rsa -b 4096 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.

azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:

correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).

[username@fedora22 ~]$ eval "$(ssh-agent -s)"

[username@fedora22 ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.

[username@fedora22 ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.

[username@fedora22 ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[username@fedora22 ~]$

```

## Erstellen der SSH-Schlüssel

Für Azure sind öffentliche und private Schlüssel mit mindestens 2048 Bit im Format „ssh-rsa“ erforderlich. Verwenden Sie zum Erstellen des Schlüsselpaars `ssh-keygen`. Hiermit wird eine Reihe von Fragen gestellt, und anschließend werden ein privater Schlüssel und ein passender öffentlicher Schlüssel geschrieben. Beim Erstellen Ihrer Azure-VM übergeben Sie den Inhalt des öffentlichen Schlüssels, der dann auf die Linux-VM kopiert und mit Ihrem lokalen und sicher gespeicherten privaten Schlüssel verwendet wird, um Sie beim Anmelden zu authentifizieren.



### Verwenden von `ssh-keygen`

Mit diesem Befehl wird ein SSH-Schlüsselpaar per RSA mit 2048 Bit erstellt und kommentiert, um die einfache Identifizierung zu ermöglichen.

    username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"

##### Befehlsbeschreibung

`ssh-keygen` = zum Erstellen der Schlüssel verwendetes Programm

`-t rsa` = zu erstellender Schlüsseltyp, also [RSA-Format](https://en.wikipedia.org/wiki/RSA_(cryptosystem))

`-b 2048` = Bits des Schlüssels

`-C "username@fedoraVMAzure"` = Kommentar zum Schlüssel für die einfache Identifizierung. Der Kommentar wird am Ende der Datei mit dem öffentlichen Schlüssel angefügt. Ein häufig verwendeter Kommentar ist eine E-Mail-Adresse, aber für diesen Artikel aktivieren wir die Verwendung mehrerer SSH-Schlüssel, sodass ein generischer Kommentar vorgeschlagen wird.

#### Exemplarische Vorgehensweise für `ssh-keygen`

```bash
username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+

username@macbook$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` Der Schlüsselpaarname für diesen Artikel. Der Name **id\_rsa** für ein Schlüsselpaar ist die Standardeinstellung. Einige Tools erwarten für die Datei mit dem privaten Schlüssel ggf. den Namen **id\_rsa**, sodass es ratsam ist, wenn er vorhanden ist. (`~/.ssh/` ist der typische Standardspeicherort für alle SSH-Schlüsselpaare und die SSH-Konfigurationsdatei.)

`Enter passphrase (empty for no passphrase):` Es wird dringend empfohlen, Ihre Schlüsselpaare mit einem Kennwort zu versehen (unter `ssh-keygen` wird dies als „Passphrase“ bezeichnet). Wenn das Schlüsselpaar nicht mit einem Kennwort geschützt ist, kann es von Personen, die über eine Kopie der Datei mit dem privaten Schlüssel verfügen, zum Anmelden an allen Servern verwendet werden. Dies gilt für Server, die über den entsprechenden öffentlichen Schlüssel verfügen. Das Hinzufügen eines Kennworts sorgt also für deutlich mehr Schutz. Wenn sich eine Person Zugang zu Ihrer Datei mit dem privaten Schlüssel verschafft, haben Sie so mehr Zeit, die Schlüssel für die Authentifizierung zu ändern.

`username@macbook$ ls -al ~/.ssh` Hier werden Ihre neuen Schlüsselpaare und die dazugehörigen Berechtigungen angezeigt. `ssh-keygen` erstellt das Verzeichnis `~/.ssh`, falls es nicht vorhanden ist, und legt auch die richtige Eigentümerschaft und die Dateimodi fest.

## Erstellen und Konfigurieren einer SSH-Konfigurationsdatei

Auch wenn diese Maßnahme für die ersten Schritte mit einer Linux-VM nicht unbedingt erforderlich ist, gilt Folgendes: Es ist eine bewährte Methode, die Datei `~/.ssh/config` zu erstellen und zu konfigurieren. Damit soll verhindert werden, dass Sie versehentlich Kennwörter zum Anmelden an Ihren VMs verwenden. Außerdem können Sie die Automatisierung mit unterschiedlichen Schlüsselpaaren für unterschiedliche Azure-VMs durchführen und andere Programme konfigurieren, z.B. **git**, um mehrere Server zu erreichen.

Im folgenden Beispiel wird eine Standardkonfiguration veranschaulicht.

### Erstellen der Datei

```bash
username@macbook$ touch ~/.ssh/config
```

### Bearbeiten der Datei zum Hinzufügen der neuen SSH-Konfiguration

```bash
username@macbook$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

Bei dieser SSH-Konfiguration werden Abschnitte für jeden Dienst bereitgestellt, damit jeweils ein eigenes dediziertes Schlüsselpaar verwendet werden kann. Die Standardeinstellungen gelten für alle Hosts, an denen Sie angemeldet sind, die nicht mit einer der obigen Gruppen übereinstimmen. Außerdem können Sie bei der SSH-Konfiguration zwei separate [GitHub](https://github.com)-Anmeldungen verwenden, und zwar eine für die öffentliche Arbeit und eine zweite nur für private Repositorys, die Sie ggf. nutzen.


##### Beschreibung der Config-Datei

`Host` = Name des Hosts, der im Terminal aufgerufen wird. `ssh fedora22` weist `SSH` an, die Werte im Einstellungsblock mit der Bezeichnung `Host fedora22` zu verwenden. HINWEIS: Dies kann eine beliebige Bezeichnung sein, die für Ihre Nutzung logisch ist und nicht den tatsächlichen Hostnamen eines Servers darstellt.

`Hostname 102.160.203.241` = IP-Adresse oder DNS-Name für den Server, für den die Anmeldung besteht. Wird für die Weiterleitung an den Server verwendet und kann eine externe IP-Adresse sein, die einer internen IP-Adresse zugeordnet ist.

`User git` = Remotebenutzerkonto, das beim Anmelden an der Azure-VM verwendet wird.

`PubKeyAuthentication yes` = Weist SSH an, dass Sie einen SSH-Schlüssel für die Anmeldung verwenden möchten.

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` = Weist SSH an, welches Schlüsselpaar zur Authentifizierung bei der Anmeldung für den Server bereitgestellt werden soll.


## SSH für eine Linux-VM ohne Kennwort

Da Sie jetzt über ein SSH-Schlüsselpaar und eine konfigurierte SSH-Konfigurationsdatei verfügen, können Sie sich schnell und sicher an Ihrer Linux-VM anmelden. Beim ersten Anmelden an einem Server mit einem SSH-Schlüssel werden Sie vom Befehl zur Eingabe der Passphrase für die Schlüsseldatei aufgefordert.

`username@macbook$ ssh fedora22`

##### Befehlsbeschreibung

Wenn `username@macbook$ ssh fedora22` ausgeführt wird, ermittelt und lädt SSH zuerst die Einstellungen aus dem Block `Host fedora22` und lädt dann alle restlichen Einstellungen aus dem letzten Block `Host *`.

## Nächste Schritte

Jetzt können Sie Ihre Schlüsseldateien verwenden, um [eine geschützte Linux-VM in Azure mit einer Vorlage zu erstellen](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0323_2016-->