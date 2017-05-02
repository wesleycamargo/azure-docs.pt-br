---
title: "Arquivar dados de telemetria com visão geral de Arquivamento dos Hubs de Eventos do Azure | Microsoft Docs"
description: "Visão geral do recurso Arquivamento dos Hubs de Eventos do Azure."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: f86336de4e1d5bda1eba12f0f95079b950963bde
ms.lasthandoff: 04/18/2017


---
# <a name="azure-event-hubs-archive"></a>Arquivamento dos Hubs de Eventos do Azure
O Arquivo Morto dos Hubs de Eventos do Azure permite que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para a conta de armazenamento de blobs de sua escolha, com maior flexibilidade para especificar uma hora ou um período de tempo desejado. A configuração da Arquivo Morto é rápida, não há custos administrativos para executá-lo e ele é escalado automaticamente com as [unidades de produtividade](event-hubs-what-is-event-hubs.md#capacity) dos Hubs de Eventos. O Arquivamento dos Hubs de Eventos é a maneira mais fácil de carregar dados de streaming no Azure e permite que você se concentre no processamento de dados em vez de se concentrar na captura de dados.

O Arquivo Morto dos Hubs de Eventos permite processar pipelines em tempo real e baseados em lote no mesmo fluxo. Isso permite que você crie soluções que podem crescer com suas necessidades ao longo do tempo. Se você estiver criando sistemas baseados em lote com o objetivo de processá-los em tempo real no futuro ou para adicionar um caminho frio eficiente para uma solução em tempo real, o Arquivamento dos Hubs de Eventos facilita o trabalho de transmissão de dados.

## <a name="how-event-hubs-archive-works"></a>Como funciona o Arquivamento dos Hubs de Eventos
Os Hubs de Eventos são um buffer de tempo de retenção durável para a entrada de telemetria, semelhante a um log distribuído. A chave para reduzir os Hubs de Eventos é o [modelo de consumidor particionado](event-hubs-what-is-event-hubs.md#partitions). Cada partição é um segmento independente de dados e é consumido de forma independente. Ao longo do tempo, esses dados expiram com base no período de retenção configurável. Assim, um Hub de Eventos específico nunca fica “cheio demais”.

O Arquivamento dos Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner que será usado para armazenar os dados arquivados. Essa conta pode estar na mesma região que o hub de eventos ou em outra região, concedendo flexibilidade ao recurso de Arquivo Morto dos Hubs de Eventos.

Os dados arquivados são gravados no formato [Apache Avro][Apache Avro]: um formato compacto, rápido e binário que fornece estruturas de dados avançados com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory. Mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="archive-windowing"></a>Janelas de arquivamento
O Arquivo Morto dos Hubs de Eventos permite que você defina uma “janela” para controlar o arquivamento. Essa janela tem configuração mínima de tamanho e tempo com uma “política de ganha quem vem primeiro”, o que significa que o primeiro gatilho encontrado causa uma operação de arquivar. Se você tiver uma janela de arquivo morto de quinze minutos/100 MB e enviar 1 MB por segundo, a janela de tamanho será disparada antes da janela de tempo. Cada partição arquiva de forma independente e grava um blob de blocos completo durante o arquivo morto, nomeado com a hora em que o intervalo de arquivo morto foi encontrado. A convenção de nomenclatura é a seguinte:

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento de unidades de produtividade
O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-what-is-event-hubs.md#capacity). Uma única unidade de produtividade permite o ingresso de 1 MB por segundo ou 1.000 eventos por segundo e duas vezes essa quantidade de saída. Os Hubs de Evento Standard podem ser configurados com 1 a 20 unidades de produtividade e outras podem ser adquiridas por meio de uma [solicitação de suporte][support request] para aumento de cota. O uso além das unidades de produtividade adquiridas é restringido. O Arquivo Morto dos Hubs de Eventos copia os dados diretamente do armazenamento interno dos Hubs de Eventos, ignorando as cotas de saída das unidades de produtividade e salvando a saída de outros leitores de processamento, como o Stream Analytics ou o Spark.

Uma vez configurado, o Arquivamento dos Hubs de Eventos é executado automaticamente assim que você envia seu primeiro evento. Ele continua sendo executado o tempo todo. Para que o seu processamento downstream saiba que processo está funcionando mais facilmente, os Hubs de Eventos gravam arquivos vazios quando não há nenhum dado. Isso fornece cadência e marcador previsíveis que podem alimentar os processadores em lotes.

## <a name="setting-up-event-hubs-archive"></a>Configurando o Arquivamento dos Hubs de Eventos
É possível configurar o Arquivo Morto no momento da criação do hub de eventos por meio do portal ou no Azure Resource Manager. Você simplesmente habilitar o Arquivamento clicando no botão **Ativar** . Configure uma conta de armazenamento e o contêiner clicando na seção **Contêiner** da folha. Já que o Arquivamento dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Adicionando o Arquivo Morto a um hub de eventos existente
O Arquivo Morto pode ser configurado nos hubs de eventos existentes que estão em um namespace dos Hubs de Eventos. O recurso não está disponível em namespaces de tipo **Misto** ou de **Mensagens**. Para habilitar o Arquivo Morto em um hub de eventos existente ou alterar as configurações do Arquivo Morto, clique no namespace para carregar a folha **Essentials** e clique no Hub de Eventos para o qual você deseja habilitar ou alterar a configuração de Arquivo Morto. Por fim, clique na seção **Propriedades** da folha aberta conforme mostrado na figura a seguir.

![][2]

Você também pode configurar o Arquivamento dos Hubs de Eventos por meio de modelos do Azure Resource Manager. Para obter mais informações, consulte [este artigo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Explorando o arquivamento e trabalhando com o Avro
Uma vez configurado, o Arquivamento dos Hubs de Eventos cria arquivos na conta de Armazenamento do Azure e no contêiner fornecido na janela de tempo configurada. Você pode exibir esses arquivos em qualquer ferramenta, como o [Gerenciador de Armazenamento do Azure][Azure Storage Explorer]. Você pode baixar os arquivos localmente para trabalhar com eles.

Os arquivos produzidos pelo Arquivamento dos Hubs de Eventos têm o seguinte esquema Avro:

![][3]

Uma maneira fácil de explorar arquivos do Avro é usando o jar [Ferramentas Avro][Avro Tools] do Apache. Depois de baixar o jar, você pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Esse comando retorna

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Você também pode usar as Ferramentas Avro para converter o arquivo em formato JSON e executar outros tipos de processamento.

Para executar um processamento mais avançado, baixe e instale o Avro na plataforma escolhida. No momento da redação deste artigo, há implementações disponíveis para C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

O Apache Avro tem guias de Introdução completos para [Java][Java] e [Python][Python]. Você também pode ler o artigo [Introdução ao Arquivamento dos Hubs de Eventos](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Como o Arquivamento dos Hubs de Eventos é cobrado
O Arquivamento dos Hubs de Eventos é medido da mesma forma que as unidades de produtividade, com uma taxa por hora. A cobrança é diretamente proporcional ao número de unidades de produtividade adquiridas para o namespace. Conforme as unidades de produtividade são aumentadas ou diminuídas, o Arquivamento dos Hubs de Eventos aumenta e diminui para ter o desempenho correspondente. Os medidores ocorrem em tandem. O custo do Arquivamento dos Hubs de Eventos é de US$ 0,10 por hora por unidade de produtividade, oferecido com desconto de 50% durante o período de visualização.

O Arquivamento dos Hubs de Eventos é a maneira mais fácil de inserir dados no Azure. Usando o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, você pode executar processamento em lotes e outras análises de sua escolha usando ferramentas familiares e plataformas em qualquer escala que precisar.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* Um [aplicativo de exemplo completo que usa os Hubs de Eventos][sample application that uses Event Hubs].
* O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][Scale out Event Processing with Event Hubs].
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

