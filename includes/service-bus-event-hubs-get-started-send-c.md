## Enviar mensagens ao Hub de Eventos

Nesta seção, gravaremos um aplicativo C para enviar eventos para o seu hub de eventos. Usaremos a biblioteca Proton AMQP do projeto [Apache Qpid](http://qpid.apache.org/). Isso é semelhante a usar Tópicos e Filas do Barramento de Serviço com AMQP por meio de C, como mostrado [aqui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [documentação Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Na [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), clique no link **Instalação do Qpid Proton** e siga as instruções dependendo do ambiente. Vamos considerar um ambiente Linux, por exemplo, uma [VM do Linux do Azure](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04.

2. Para compilar a biblioteca Proton, instale os pacotes a seguir:

	```
	sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
	```

3. Baixe a [biblioteca Qpid Proton](http://qpid.apache.org/proton/index.html) e extraia-, por exemplo:

	```
	wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
	tar xvfz qpid-proton-0.7.tar.gz
	```

4. Crie um diretório de compilação, compile e instale:

	```
	cd qpid-proton-0.7
	mkdir build
	cd build
	cmake -DCMAKE_INSTALL_PREFIX=/usr ..
	sudo make install
	```

5. No seu diretório de trabalho, crie um novo arquivo chamado **sender.c** com o seguinte conteúdo. Lembre-se de substituir o valor para o nome do Hub de Evento e o nome de namespace (o último é geralmente `{event hub name}-ns`). Você também deve substituir uma versão codificada em URL da chave para o **SendRule** criado anteriormente. Você pode codificar a URL [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).

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

6. Compile o arquivo, supondo que **gcc**:

	```
	gcc sender.c -o sender -lqpid-proton
	```

> [AZURE.NOTE] No código acima, usamos uma janela de saída de 1 para forçar a saída das mensagens assim que possível. Em geral, o aplicativo deve tentar enviar mensagens em lote para aumentar a taxa de transferência. Consulte a página [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para obter mais informações sobre como usar a biblioteca Qpid Proton neste e em outros ambientes e de plataformas para as quais associações são fornecidas (atualmente Perl, PHP, Python e Ruby).

<!---HONumber=AcomDC_0413_2016-->