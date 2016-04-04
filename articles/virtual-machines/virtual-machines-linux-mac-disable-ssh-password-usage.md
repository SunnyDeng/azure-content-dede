<properties
	pageTitle="Deaktivieren von SSH-Kennwörtern auf Ihrer Linux-VM durch Konfigurieren von SSHD | Microsoft Azure"
	description="Sichern Sie Ihre Linux-VM unter Azure durch Deaktivieren von Kennwortanmeldungen für SSH."
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
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="v-vlivech"/>

# Deaktivieren von SSH-Kennwörtern auf Ihrer Linux-VM durch Konfigurieren von SSHD

Dieser Artikel konzentriert sich auf das Festlegen der Anmeldesicherheit Ihrer Linux-VM. Sobald der SSH-Port 22 geöffnet ist, beginnen Bots, Kennwörter zu erraten. In diesem Artikel lernen Sie, Kennwortanmeldungen über SSH zu deaktivieren. Indem wir die Möglichkeit, Kennwörter zu verwenden, vollständig unterbinden, schützen wir die Linux-VM vor dieser Form eines Brute-Force-Angriffs. Zusätzlicher Bonus: Wir konfigurieren Linux-SSHD so, dass nur Anmeldungen über öffentliche und private SSH-Schlüssel möglich sind – mit Abstand die sicherste Möglichkeit für die Anmeldung bei Linux. Aufgrund der Vielzahl möglicher Kombinationen erfordert das Erraten des privaten Schlüssels immensen Aufwand, und so werden Bots von vornherein entmutigt, auch nur zu versuchen, mit Brute-Force-Angriffen SSH-Schlüssel abzufragen.


## Ziele

- Konfigurieren von SSHD, um Folgendes zu unterbinden:
  - Anmeldungen mit Kennwort
  - Anmeldungen von Root-Benutzern
  - Abfrage/Rückmeldung-Authentifizierung
- Konfigurieren von SSHD, um Folgendes zu erlauben:
  - Nur SSH-Schlüsselanmeldungen
- Neustarten von SSHD, während Sie weiterhin angemeldet sind
- Testen der neuen SSHD-Konfiguration

## Einführung

[SSH definiert](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD ist der SSH-Server, der auf der Linux-VM ausgeführt wird. SSH ist ein Client, der von der Shell auf Ihrer MacBook- oder Linux-Arbeitsstation aus ausgeführt wird. SSH ist auch das Protokoll, das zum Sichern und Verschlüsseln der Kommunikation zwischen Ihrer Arbeitsstation und der Linux-VM verwendet wird.

Für diesen Artikel ist es sehr wichtig, dass Sie eine Anmeldung bei Ihrer Linux-VM für die gesamte Vorgehensweise offen halten. Aus diesem Grund werden wir zwei Terminals und eine SSH-Verbindung mit der Linux-VM für beide öffnen. Wir verwenden das erste Terminal, um die Konfigurationsdatei der SSHDs zu ändern und den SSHD-Dienst neu zu starten. Wir verwenden das zweite Terminal, um diese Änderungen zu testen, sobald der Dienst neu gestartet wurde. Da wir SSH-Kennwörter deaktivieren und uns strikt auf SSH-Schlüssel verlassen, wird die VM dauerhaft gesperrt, wenn Ihre SSH-Schlüssel nicht korrekt sind, und Sie die Verbindung mit der VM schließen, und niemand wird in der Lage sein, sich bei ihr anzumelden. Sie muss dann also gelöscht und neu erstellt werden.

## Voraussetzungen

- [Erstellen von SSH-Schlüsseln unter Linux und Mac für Linux-VMs in Azure](link)
- Azure-Konto
  - [Anmeldung für kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure-Portal](http://portal.azure.com)
- Linux-VM unter Azure
- Öffentliches und privates SSH-Schlüsselpaar in `~/.ssh/`
- Öffentlicher SSH-Schlüssel in `~/.ssh/authorized_keys` auf Linux-VM
- Sudo-Rechte auf der VM
- Port 22 offen

## Schnellbefehle

_Erfahrene Linux-Administratoren, die nur die TLDR-Version wünschen, beginnen hier. Alle anderen Benutzer, die ausführliche Erläuterungen und eine exemplarische Vorgehensweise wünschen, überspringen diesen Abschnitt._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## Ausführliche exemplarische Vorgehensweise

Melden Sie sich auf Terminal 1 (T1) bei der Linux-VM an. Melden Sie sich auf Terminal 2 (T2) bei der Linux-VM an.

Auf T2 werden wir die SSHD-Konfigurationsdatei bearbeiten.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Von hier aus bearbeiten wir nur die Einstellungen zum Deaktivieren von Kennwörtern und Aktivieren von SSH-Schlüsselanmeldungen. Es gibt viele Einstellungen in der Datei, die Sie untersuchen sollten, und ändern, um Linux und SSH so sicher wie nötig zu machen.

#### Deaktivieren von Anmeldungen mit Kennwort

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### Aktivieren der Authentifizierung mit dem öffentlichen Schlüssel

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### Deaktivieren der Root-Anmeldung

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### Deaktivieren der Abfrage/Rückmeldung-Authentifizierung

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### Neustarten von SSHD

Überprüfen Sie von der T1-Shell aus, ob Sie immer noch angemeldet sind. Dies ist wichtig, damit Sie nicht von Ihrer VM ausgesperrt werden, wenn Ihre SSH-Schlüssel nicht korrekt sind, da Kennwörter jetzt deaktiviert sind. Wenn eine Einstellung auf Ihrer Linux-VM falsch ist, können Sie „sshd\_config“ mit T1 korrigieren, da Sie immer noch angemeldet sind, und SSH die Verbindung aufrechterhält, während der SSHD-Dienst neu startet.

Von T2 ausführen:

##### Auf der Debian-Familie

```
username@macbook$ sudo service ssh restart
```

##### Auf der RedHat-Familie

```
username@macbook$ sudo service sshd restart
```

Kennwörter sind jetzt auf Ihrer VM deaktiviert, sodass sie vor Brute-Force-Kennwortanmeldeversuchen geschützt ist. Da nur SSH-Schlüssel zulässig sind, können Sie sich schneller und viel sicherer anmelden.

<!---HONumber=AcomDC_0323_2016-->