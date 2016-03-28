## Senden von Nachrichten an Ereignis-Hubs

In diesem Abschnitt schreiben wir eine C-App, um Ereignisse an den Event Hub zu senden. Wir verwenden die Proton AMQP-Bibliothek aus dem [Apache Qpid-Projekt](http://qpid.apache.org/). Dies entspricht der Verwendung von Service Bus-Warteschlangen und -Themen mit AMQP aus C, wie [hier](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504) beschrieben. Weitere Informationen finden Sie in der [Qpid Proton-Dokumentation](http://qpid.apache.org/proton/index.html).

1. Klicken Sie auf der Seite [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) (in englischer Sprache) auf den Link **Installing Qpid Proton**, und befolgen Sie die Anweisungen für Ihre Umgebung. Wir setzen eine Linux-Umgebung voraus, z. B. eine [Azure-Linux-VM](../articles/virtual-machines/virtual-machines-linux-tutorial.md) mit Ubuntu 14.04.

2. Um die Proton-Bibliothek zu kompilieren, installieren Sie die folgenden Pakete:

	```
	sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
	```

3. Laden Sie die [Qpid Proton-Bibliothek](http://qpid.apache.org/proton/index.html) herunter, und extrahieren Sie sie, z. B.:

	```
	wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
	tar xvfz qpid-proton-0.7.tar.gz
	```

4. Erstellen Sie einen Build-Verzeichnis, kompilieren und installieren Sie:

	```
	cd qpid-proton-0.7
	mkdir build
	cd build
	cmake -DCMAKE_INSTALL_PREFIX=/usr ..
	sudo make install
	```

5. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Datei namens **sender.c** mit folgendem Inhalt. Vergessen Sie nicht, den Wert für den Namen des Event Hubs und den Namespacenamen (letzterer lautet i. d. R. `{event hub name}-ns`) zu ersetzen. Sie müssen auch eine URL-codierte Version des Schlüssels für die zuvor erstellte **SendRule** eingeben. Die URL-Codierung können Sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp) vornehmen.

	```
	#include "proton/message.h"
	#include "proton/messenger.h"
	
	#include <getopt.h>
	#include <proton/util.h>
	#include <sys/time.h>
	#include <stddef.h>
	#include <stdio.h>
	#include <string.h>
	#include <unistd.h>
	#include <stdlib.h>
	
	#define check(messenger)                                                     \
	  {                                                                          \
	    if(pn_messenger_errno(messenger))                                        \
	    {                                                                        \
	      printf("check\n");													 \
	      die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
	    }                                                                        \
	  }  
	
	pn_timestamp_t time_now(void)
	{
	  struct timeval now;
	  if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
	  return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
	}  
	
	void die(const char *file, int line, const char *message)
	{
	  printf("Dead\n");
	  fprintf(stderr, "%s:%i: %s\n", file, line, message);
	  exit(1);
	}
	
	int sendMessage(pn_messenger_t * messenger) {
		char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
		char * msgtext = (char *) "Hello from C!";
	
		pn_message_t * message;
		pn_data_t * body;
		message = pn_message();
	
		pn_message_set_address(message, address);
		pn_message_set_content_type(message, (char*) "application/octect-stream");
		pn_message_set_inferred(message, true);
	
		body = pn_message_body(message);
		pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
	
		pn_messenger_put(messenger, message);
		check(messenger);
		pn_messenger_send(messenger, 1);
		check(messenger);
	
		pn_message_free(message);
	}
	
	int main(int argc, char** argv) {
		printf("Press Ctrl-C to stop the sender process\n");
	
		pn_messenger_t *messenger = pn_messenger(NULL);
		pn_messenger_set_outgoing_window(messenger, 1);
		pn_messenger_start(messenger);
	
		while(true) {
			sendMessage(messenger);
			printf("Sent message\n");
			sleep(1);
		}
	
		// release messenger resources
		pn_messenger_stop(messenger);
		pn_messenger_free(messenger);
	
		return 0;
	}
	```

6. Kompilieren Sie die Datei, wobei **gcc** vorausgesetzt wird:

	```
	gcc sender.c -o sender -lqpid-proton
	```

> [AZURE.NOTE] In diesem Code verwenden wir ein Ausgabefenster von 1, um eine sofortige Ausgabe der Meldungen zu erzwingen. Im Allgemeinen sollten Anwendungen Nachrichten stapelweise ausgeben, um den Durchsatz zu erhöhen. Weitere Informationen zur Verwendung der Qpid Proton-Bibliothek in dieser und anderen Umgebungen und auf Plattformen, für die Bindungen bereitgestellt werden (derzeit Perl, PHP, Python und Ruby), finden Sie auf der Seite [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) (in englischer Sprache).

<!---HONumber=AcomDC_0316_2016-->