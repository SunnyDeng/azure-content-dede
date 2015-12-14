<!--v-sharos 10/13/2105 virtual device security-->

Beachten Sie bei der Verwendung des virtuellen StorSimple-Geräts die folgenden Sicherheitsaspekte:

- Das virtuelle Gerät wird über das Microsoft Azure-Abonnement geschützt. Wenn Sie daher das virtuelle Gerät verwenden und Ihr Azure-Abonnement beeinträchtigt ist, sind auch die auf dem virtuellen Gerät gespeicherten Daten anfällig.

- Der öffentliche Schlüssel des Zertifikats zum Verschlüsseln von in Azure StorSimple gespeicherten Daten wird im klassischen Azure-Portal sicher zur Verfügung gestellt, und der private Schlüssel wird auf dem StorSimple-Gerät aufbewahrt. Auf dem virtuellen StorSimple-Gerät werden die öffentlichen und privaten Schlüssel in Azure gespeichert.

- Das virtuelle Gerät wird im Microsoft Azure-Datencenter gehostet.

<!---HONumber=AcomDC_1203_2015-->