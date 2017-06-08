---
title: Enviar eventos para Hubs de Eventos do Azure usando C | Microsoft Docs
description: Enviar eventos para Hubs de Eventos do Azure usando C
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 05/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: e1aeb2708e829480b0e4a520f6f9ee08894bfaf9
ms.contentlocale: pt-br
ms.lasthandoff: 05/04/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>Enviar eventos para Hubs de Eventos do Azure usando C

## <a name="introduction"></a>Introdução
Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte [Visão geral de Hubs de Eventos][Event Hubs overview].

Neste tutorial, você aprenderá a enviar eventos para um hub de eventos usando um aplicativo de console em C. Para receber eventos, clique na linguagem apropriada de recebimento no sumário à esquerda.

Para concluir este tutorial, você precisará do seguinte:

* Um ambiente de desenvolvimento C. Para este tutorial, vamos pressupor a pilha gcc em uma Máquina Virtual Linux do Azure com Ubuntu 14.04.
* Microsoft Visual Studio ou Visual Studio Community Edition
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Enviar mensagens ao Hub de Eventos
Nesta seção, escreveremos um aplicativo C para enviar eventos ao seu hub de eventos. Usaremos a biblioteca Proton AMQP do projeto [Apache Qpid](http://qpid.apache.org/). Isso é semelhante a usar Tópicos e Filas do Barramento de Serviço com AMQP por meio de C, como mostrado [aqui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [documentação Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Na [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), clique no link **Instalação do Qpid Proton** e siga as instruções dependendo do ambiente.
2. Para compilar a biblioteca Proton, instale os pacotes a seguir:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Baixe a [biblioteca Qpid Proton](http://qpid.apache.org/proton/index.html) e extraia-a, por exemplo:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Crie um diretório de compilação, compile e instale:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. No seu diretório de trabalho, crie um novo arquivo chamado **sender.c** com o seguinte conteúdo. Lembre-se de substituir o valor do nome do hub de eventos e do nome de namespace (o último é geralmente `{event hub name}-ns`). Você também deve substituir uma versão codificada em URL da chave para o **SendRule** criado anteriormente. Você pode codificar a URL [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
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
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
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

    > [!NOTE]
    > No código acima, usamos uma janela de saída de 1 para forçar a saída das mensagens assim que possível. Em geral, o aplicativo deve tentar enviar mensagens em lote para aumentar a taxa de transferência. Consulte a página [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para obter mais informações sobre como usar a biblioteca Qpid Proton neste e em outros ambientes e de plataformas para as quais associações são fornecidas (atualmente Perl, PHP, Python e Ruby).


## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

