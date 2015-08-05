Azure bestimmt die für die virtuelle Umgebung zu verwendende Version von Python mit der folgenden Priorität:

1. in runtime.txt im Stammordner angegebene Version
1. von der Python-Einstellung in der Web-App-Konfiguration angegebene Version (Blatt **Einstellungen** > **Anwendungseinstellungen** für Ihre Web-App im Azure-Portal)
1. Python 2.7 ist die Standardeinstellung, wenn keine der oben genannten angegeben sind

Gültige Werte für den Inhalt

    \runtime.txt

sind:

- python-2.7
- python-3.4

Wenn die Micro-Version (dritte Ziffer) angegeben wird, wird sie ignoriert.

<!---HONumber=July15_HO4-->