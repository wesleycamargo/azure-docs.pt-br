<properties 
    pageTitle="Casos de uso comuns do Banco de Dados de Documentos | Azure" 
    description="Saiba mais sobre os principais cinco casos de uso para o Banco de Dados de Documentos: conteúdos gerados pelo usuário, registros de log de eventos, dados de catálogo, dados de preferências do usuário e IoT (Internet of Things, Internet das Coisas)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/10/2015" 
    ms.author="hawong"/>

# Casos de uso comuns do Banco de Dados de Documentos
Este artigo fornece uma visão geral dos vários casos de uso comuns do Banco de Dados de Documentos. As recomendações neste artigo servem como ponto de partida ao desenvolver seu aplicativo com o Banco de Dados de Documentos.

Após ler este artigo, você poderá responder as perguntas a seguir:
 
- Quais são os casos de uso comuns do Banco de Dados de Documentos?
- Quais são os benefícios de usar o Banco de Dados de Documentos como um repositório de conteúdo gerado pelo usuário?
- Quais são os benefícios de usar o Banco de Dados de Documentos como um armazenamento de dados de catálogo?
- Quais são os benefícios de usar o Banco de Dados de Documentos como um armazenamento de logs de evento?
- Quais são os benefícios de usar o Banco de Dados de Documentos como um armazenamento de dados de preferência do usuário?
- Quais são os benefícios de usar o Banco de Dados de Documentos como um armazenamento de dados para sistemas IoT (Internet of Things, Internet das Coisas)?

    
## Conteúdo gerado pelo usuário
Um caso de uso comum para o Banco de Dados de Documentos é armazenar e consultar UGC (User Generated Content, conteúdo gerado pelo usuário) para aplicativos Web e móveis, particularmente aplicativos de mídia social. Alguns exemplos de UGC são sessões de bate-papo, tweets, postagens em blogs, classificações e comentários. Muitas vezes, o UGC em aplicativos de mídia social é uma mistura de texto de forma livre, propriedades, marcas e relações que não se limitam a uma estrutura rígida.

Conteúdos como bate-papos, comentários e postagens podem ser armazenados no Banco de Dados de Documentos sem a necessidade de transformações ou objetos complexos em camadas de mapeamento relacional. Propriedades de dados podem ser adicionadas ou modificadas facilmente para atender aos requisitos conforme os desenvolvedores iteram sobre o código do aplicativo, portanto, promovendo um desenvolvimento rápido.

Aplicativos que se integram com várias redes sociais devem responder às mudanças nos esquemas destas redes. Como os dados são automaticamente indexados por padrão no Banco de Dados de Documentos, os dados estão prontos para serem consultados a qualquer momento. Portanto, esses aplicativos têm a flexibilidade para recuperar as projeções de acordo com suas respectivas necessidades.

Muitos dos aplicativos sociais são executados em escala global e podem apresentar os padrões de uso imprevisíveis. Flexibilidade para dimensionar o armazenamento de dados é essencial, uma vez que a camada do aplicativo é dimensionada de acordo com a demanda de uso. Você pode escalar horizontalmente incluindo partições de dados adicionais em uma conta do Banco de Dados de Documentos. Além disso, você também pode criar contas adicionais do Banco de Dados de Documentos em várias regiões. Para saber mais sobre a disponibilidade da região do serviço do Banco de Dados de Documentos, consulte [Regiões do Azure](http://azure.microsoft.com/regions/#services).

## Dados de catálogo
Os cenários de uso de dados de catálogo envolvem armazenar e consultar um conjunto de atributos para entidades, como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de usuário, catálogos de produtos, registros de dispositivo para IoT e sistemas de listas de materiais. Os atributos desses dados podem variar e podem mudar ao longo do tempo para atender aos requisitos do aplicativo.

Considere um exemplo de um catálogo de produto para um fornecedor de peças automotivas. Todas as partes podem ter seus próprios atributos além dos atributos comuns que todas as partes compartilham. Além disso, atributos de uma parte específica podem mudar no ano seguinte quando um novo modelo é liberado. Como um armazenamento de documentos JSON, o Banco de Dados de Documentos dá suporte a esquemas flexíveis e permite representar dados com propriedades aninhadas e, portanto, é adequado para armazenar dados de catálogo de produtos.

## Dados de log
O registro em log do aplicativo geralmente é emitido em grandes volumes e pode ter atributos que variam com base na versão do aplicativo implantada ou nos eventos de log de componentes. Dados de log não estão limitados por relações complexas ou estruturas rígidas. Cada vez mais, os dados de log são mantidos no formato JSON, uma vez que o JSON é leve e fácil para leitura humana.
   
Normalmente, há dois casos de uso importantes relacionados aos dados do log de eventos. O primeiro caso de uso é executar consultas ad hoc em um subconjunto dos dados para solução de problemas. Durante a solução de problemas, um subconjunto de dados é primeiramente recuperado do logs, normalmente por séries temporais. Em seguida, uma busca detalhada é executada filtrando o conjunto de dados com níveis de erro ou mensagens de erro. É aí que armazenar os logs de eventos no Banco de Dados de Documentos se torna uma vantagem. Os dados de log armazenados no Banco de Dados de Documentos são indexados automaticamente por padrão, assim, os dados estão prontos para serem consultados a qualquer momento. Além disso, os dados de log podem ser persistidos entre partições de dados como uma série temporal. Logs mais antigos podem ser colocados em armazenamento frio de acordo com sua política de retenção.

O segundo caso de uso envolve executar trabalhos de análise de dados offline em um grande volume de dados de log. Exemplos desse caso de uso incluem análise de disponibilidade de servidor, análise de erros de aplicativo e análise de dados de sequência de cliques. Normalmente, o Hadoop é usado para executar esses tipos de análises. Com o conector do Hadoop para o Banco de Dados de Documentos, bancos de dados do Bancos e Dados de Documentos funcionam como fontes de dados e coletores de trabalhos Pig, Hive e Map/Reduce. Para obter detalhes sobre o conector do Hadoop para o Banco de Dados de Documentos, consulte [Executar um trabalho do Hadoop com o Banco de Dados de Documentos e HDInsight](documentdb-run-hadoop-with-hdinsight/).

## Dados de preferências do usuário
Hoje em dia, aplicativos Web e móveis mais modernos vêm com modos de exibição e experiências mais complexos. Esses modos de exibição e experiências são geralmente dinâmicos, atendendo as preferências ou o humor do usuário e as necessidades da marca. Portanto, os aplicativos precisam conseguir recuperar configurações personalizadas com eficiência para renderizar experiências e elementos da interface do usuário rapidamente.

JSON é um formato eficiente para representar dados de layout de interface do usuário já que não é leve, mas pode ser facilmente interpretado pelo JavaScript. O Banco de Dados de Documentos oferece níveis de consistência ajustáveis que permitir leituras rápidas com gravações de baixa latência. Portanto, armazenar dados de layout da interface do usuário, incluindo configurações personalizadas, como documentos JSON no Banco de Dados de Documentos é um meio eficaz de obter esses dados.

## Dados de sensor do dispositivo
Casos de uso de IoT normalmente compartilham alguns padrões sobre como consomem, processam e armazenam dados. Em primeiro lugar, esses sistemas permitem a entrada de dados, podendo acomodar picos de dados de sensores de dispositivo de várias localidades. Em seguida, esses sistemas processam e analisar dados de streaming para obterem resultados em tempo real. E, por último, mas não menos importante, a maioria se não todos os dados acabarão aparecendo em um armazenamento de dados para consulta ad hoc e análise offline.

O Microsoft Azure oferece serviços avançados que podem ser aproveitados para casos de uso de IoT. Os serviços de IoT do Azure são um conjunto de serviços, incluindo Hubs de eventos do Azure, Banco de Dados de Documentos do Azure, análise de fluxo do Azure, Hub de notificação do Azure, aprendizado de máquina do Azure, Azure HDInsight e PowerBI.

Picos de dados podem ser processados por Hubs de eventos do Azure que oferecem inclusão de dados de alta taxa de transferência com baixa latência. Dados incluídos que precisam ser processados para obter informações em tempo real podem ser encaminhados para análise de fluxo do Azure para análise em tempo real. Os dados podem ser carregados no Banco de Dados de Documentos para consultas ad hoc. Depois que os dados são carregados no Banco de Dados de Documentos, os dados estão prontos para serem consultados. Os dados no Banco de Dados de Documentos podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser ainda mais refinados e processados conectando dados do Banco de Dados de Documentos no HDInsight para trabalhos Pig, Hive ou Map/Reduce. Dados refinados são carregados, em seguida, de volta ao Banco de Dados de Documentos para relatórios.

Para obter uma solução de IoT de exemplo usando o Banco de Dados de Documentos, EventHubs e Storm, consulte os [repositórios de exemplo do hdinsight-storm no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas do Azure para IoT, consulte [Criar a Internet das Coisas](http://www.microsoft.com/pt-br/server-cloud/internet-of-things.aspx).

## Próximas etapas
 
Para começar a usar o Banco de Dados de Documentos, você pode criar uma [conta](http://azure.microsoft.com/pricing/free-trial/) e seguir nosso [guia de aprendizado](documentdb-learning-map.md) para conhecer o Banco de Dados de Documentos e localizar as informações necessárias.

Ou, se você quiser ler mais sobre os clientes que usam o Banco de Dados de Documentos, as histórias dos clientes a seguir estão disponíveis:

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Integrador líder oferece a empresas multinacionais informações globais em minutos com as tecnologias de nuvem flexíveis.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionando inteligência às notícias para fornecer informações com a finalidade de engajar cidadãos. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obter cores consistentes em todo o mundo, grandes marcas se voltam para a SGS. E a SGS se volta para o Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). O líder global Telenor usa a nuvem para se mover com a velocidade de uma start-up. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). A loja do futuro utiliza pesquisa rápida e um fluxo de dados fácil.
 

<!---HONumber=August15_HO6-->