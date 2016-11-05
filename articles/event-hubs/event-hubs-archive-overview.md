---
title: Arquivamento dos Hubs de Eventos do Azure | Microsoft Docs
description: Visão geral do recurso Arquivamento dos Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: djrosanova
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: darosa;sethm

---
# Arquivamento dos Hubs de Eventos do Azure
O Arquivamento dos Hubs de Eventos do Azure permite que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para a conta de armazenamento de blobs de sua escolha, com maior flexibilidade para especificar uma hora ou um período de tempo. A configuração do arquivamento é rápida, não há custos administrativos para executá-lo e ele é dimensionado automaticamente com as [unidades de produtividade](event-hubs-overview.md#capacity-and-security) dos Hubs de Eventos. O Arquivamento dos Hubs de Eventos é a maneira mais fácil de carregar dados de streaming no Azure e permite que você se concentre no processamento de dados em vez de se concentrar na captura de dados.

O Arquivamento dos Hubs de Eventos do Azure permite processar pipelines em tempo real e baseados em lote no mesmo fluxo. Isso permite que você crie soluções que podem crescer com suas necessidades ao longo do tempo. Se você estiver criando sistemas baseados em lote com o objetivo de processá-los em tempo real no futuro ou para adicionar um caminho frio eficiente para uma solução em tempo real, o Arquivamento dos Hubs de Eventos facilita o trabalho de transmissão de dados.

## Como funciona o Arquivamento dos Hubs de Eventos
Os Hubs de Eventos são um buffer de tempo de retenção durável para a entrada de telemetria, semelhante a um log distribuído. A chave para reduzir os Hubs de Eventos horizontalmente é o [modelo de consumidor particionado](event-hubs-overview.md#partition-key). Cada partição é um segmento independente de dados e é consumido de forma independente. Ao longo do tempo, esses dados expiram com base no período de retenção configurável. Assim, um Hub de Eventos específico nunca fica "muito cheio".

O Arquivamento dos Hubs de Eventos permite que você especifique sua própria conta de Armazenamento de Blobs do Azure e o contêiner que será usado para armazenar os dados arquivados. Essa conta pode ser na mesma região que o Hub de Eventos ou em outra região, o que dá flexibilidade ao recurso Arquivamento dos Hubs de Eventos.

Os dados arquivados são gravados no formato [Apache Avro][Apache Avro], um formato compacto, rápido e binário que fornece estruturas de dados avançados com esquema embutido. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory. Mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### Janelas de arquivamento
O Arquivamento dos Hubs de Eventos permite que você defina uma janela para controlar o arquivamento. Essa janela tem configuração mínima de tamanho e tempo com uma "política de ganha quem vem primeiro", que significa que o primeiro disparador encontrado causará uma operação de arquivamento. Se você tiver uma janela de arquivamento de quinze minutos/100 MB e envia 1 MB/s, a janela de tamanho disparará antes da janela de tempo. Cada partição arquiva independentemente e grava um blob de blocos completo no momento do arquivamento, nomeado com a hora em que o intervalo de arquivamento foi encontrado. A convenção de nomenclatura é a seguinte:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### Dimensionamento de unidades de produtividade
O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-overview.md#capacity-and-security). Uma única unidade de transferência permite o ingresso de 1 MB/s ou 1000 eventos por segundo e duas vezes essa quantidade de saída. Os Hubs de Eventos Standard podem ser configurados com 1 a 20 unidades de produtividade e outras podem ser adquiridas por meio de uma [solicitação de suporte][solicitação de suporte] para aumento de cota. O uso além das unidades de produtividade adquiridas é restringido. O Arquivamento dos Hubs de Eventos copia os dados diretamente do armazenamento interno dos Hubs de Eventos, ignorando as cotas de saída das unidades de produtividade e salvando a saída para outros leitores de processamento, como o Stream Analytics ou o Spark.

Uma vez configurado, o Arquivamento dos Hubs de Eventos é executado automaticamente assim que você envia seu primeiro evento. Ele continua sendo executado o tempo todo. Para que o seu processamento downstream saiba que processo está funcionando mais facilmente, os Hubs de Eventos gravam arquivos vazios quando não há nenhum dado. Isso fornece cadência e marcador previsíveis que podem alimentar os processadores em lotes.

## Configurando o Arquivamento dos Hubs de Eventos
O Arquivamento dos Hubs de Eventos podem ser configurados no momento da criação do Hub de Eventos por meio do portal ou do Azure Resource Manager. Você simplesmente habilitar o Arquivamento clicando no botão **Ativar**. Configure uma conta de armazenamento e o contêiner clicando na seção **Contêiner** da folha. Já que o Arquivamento dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de cinco minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## Adicionando Arquivamento a um Hub de Eventos existente
Os Arquivos podem ser configurados em Hubs de Eventos existentes que estão em um namespace de Hubs de Eventos. O recurso não está disponível em namespaces mistos ou de mensagens. Para habilitar o arquivamento em um Hub de Eventos existente ou para alterar as configurações de Arquivamento, clique no namespace para carregar a folha **Essentials** e clique no Hub de Eventos para o qual você deseja habilitar ou alterar a configuração de Arquivamento. Por fim, clique na seção **Propriedades** da folha aberta conforme mostrado na figura a seguir.

![][2]

Você também pode configurar o Arquivamento dos Hubs de Eventos por meio de modelos do Azure Resource Manager. Para saber mais, confira [este artigo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## Explorando o arquivamento e trabalhando com o Avro
Uma vez configurado, o Arquivamento dos Hubs de Eventos cria arquivos na conta de Armazenamento do Azure e no contêiner fornecido na janela de tempo configurada. Você pode exibir esses arquivos em qualquer ferramenta, como o [Gerenciador de Armazenamento do Azure][Gerenciador de Armazenamento do Azure]. Você pode baixar os arquivos localmente para trabalhar com eles.

Os arquivos produzidos pelo Arquivamento dos Hubs de Eventos têm o seguinte esquema Avro:

![][3]

Uma maneira fácil de explorar os arquivos do Avro é usando o jar [Ferramentas Avro][Ferramentas Avro] do Apache. Depois de baixar o jar, você pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```
java -jar avro-tools-1.8.1.jar getschema <name of archive file>
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

Para executar um processamento mais avançado, baixe e instale o Avro na plataforma escolhida. No momento da redação deste artigo, existem implementações disponíveis para C, C++, C#, Java, NodeJS, Perl, PHP, Python e Ruby.

O Apache Avro tem guias de Introdução completos para [Java][Java] e [Python][Python]. Você também pode ler o artigo [Introdução ao Arquivamento dos Hubs de Eventos](event-hubs-archive-python.md).

## Como o Arquivamento dos Hubs de Eventos é cobrado
O Arquivamento dos Hubs de Eventos é medido da mesma forma que as unidades de produtividade, com uma taxa por hora. A cobrança é diretamente proporcional ao número de unidades de produtividade adquiridas para o namespace. Conforme as unidades de produtividade são aumentadas ou diminuídas, o Arquivamento dos Hubs de Eventos aumenta e diminui para ter o desempenho correspondente. Os medidores acontecem em simultâneo. O custo do Arquivamento dos Hubs de Eventos é de US$ 0,10 por hora por unidade de produtividade, oferecido com desconto de 50% durante o período de visualização.

O Arquivamento dos Hubs de Eventos realmente é a maneira mais fácil de ter dados no Azure. Usando o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, você pode executar processamento em lotes e outras análises de sua escolha usando ferramentas familiares e plataformas em qualquer escala que precisar.

## Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* Um [aplicativo de exemplo completo que usa os Hubs de Evento][aplicativo de exemplo completo que usa os Hubs de Evento].
* O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][Escala horizontal do processamento de eventos com Hubs de Eventos].
* [Visão geral de Hubs de Evento][Visão geral de Hubs de Evento]

[Apache Avro]: http://avro.apache.org/
[solicitação de suporte]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Gerenciador de Armazenamento do Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Ferramentas Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->