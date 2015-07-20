
#### So installieren Sie Wartungsmodus-Hotfixes über Windows PowerShell für StorSimple

> [AZURE.IMPORTANT]Im Wartungsmodus müssen Sie den Hotfix zunächst auf einem und dann auf dem anderen Controller anwenden.

1. Versetzen Sie das Gerät in den Wartungsmodus. Informationen zum Wechseln in den Wartungsmodus finden Sie im Abschnitt [Wechseln in den Wartungsmodus](#enter-maintenance-mode).

2. Geben Sie zum Anwenden des Hotfixes Folgendes ein:

     `Start-HcsHotfix`

3. Geben Sie bei entsprechender Aufforderung den Pfad zur Netzwerkfreigabe mit den Hotfix-Dateien ein.

4. Sie werden aufgefordert, diesen Schritt zu bestätigen. Geben Sie **J** ein, um mit der Installation des Hotfixes fortzufahren.

5. Nachdem Sie den Hotfix auf einem Controller installiert haben, melden Sie sich bei dem anderen Controller an. Wenden Sie den Hotfix wie beim vorherigen Controller an.

6. Nachdem alle Hotfixes angewendet wurden, beenden Sie den Wartungsmodus. Anweisungen dazu finden Sie unter [Beenden des Wartungsmodus](#exit-maintenance-mode).

<!---HONumber=July15_HO2-->