<properties 
	pageTitle="Introdução à análise de fluxo | Azure" 
	description="Compreender a análise de fluxo do Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/06/2015" 
	ms.author="jeffstok"/>


# Introdução à análise de fluxo do Azure

O Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por streaming de dados na nuvem.


## Introdução ao Stream Analytics do Azure (vídeo)

Neste [vídeo](http://azure.microsoft.com/documentation/videos/introduction-to-azure-stream-analytics-with-santosh-balasubramanian/), Santosh Balasubramanian e Scott Hanselman introduzem a Stream Analytics do Azure. (Duração: 11:22)

> [AZURE.VIDEO introduction-to-azure-stream-analytics-with-santosh-balasubramanian]

## Visão geral e motivação

Grandes quantidades de dados estão fluindo em alta velocidade na rede hoje. As organizações que podem processar e agir sobre esses dados em tempo real podem melhorar significativamente a eficiência e diferenciar-se no mercado. Os cenários de Stream Analytics em tempo real podem ser encontrados em todos os setores: análise de comercialização de ações personalizada, em tempo real e alertas oferecidos por empresas de serviços financeiros; detecção de fraudes em tempo real; serviços de proteção de identidade e de dados; ingestão confiável e análise de dados gerados por sensores e atuadores inseridos em objetos físicos (Internet das Coisas ou IoT); análise de sequência de cliques na Web; e aplicativos de CRM (gerenciamento de relacionamento com o cliente) emitem alertas quando a experiência do cliente em um período de tempo está degradada. As empresas estão procurando a maneira mais flexível, confiável e econômica de fazer tal análise de dados de fluxo de eventos em tempo real para serem bem-sucedidas no mundo dos negócios modernos, que é altamente competitivo.

Criação de sistemas de processamento de fluxo é uma tarefa complexa. As operações de streaming como correlações e agregações não só precisam ser implementadas com eficiência, mas também precisam ser escalonáveis e tolerantes a falhas. Os desafios operacionais adicionais são dispostos em camadas, incluindo implantação, depuração e monitoramento. Os custos associados à criação e à manutenção de tal solução aumenta rapidamente. As empresas maiores se resignaram a pagar um alto custo com soluções personalizadas, embora as empresas menores geralmente percam a oportunidade devido à alta barreira para a entrada e a despesa proibitiva. A Análise de fluxo do Azure soluciona esses desafios.

A Stream Analytics do Azure é um serviço de computação de fluxo em tempo real, totalmente gerenciado e hospedado no Microsoft Azure. Esse serviço fornece processamento escalonável de eventos complexos de fluxo de dados altamente resiliente e de baixa latência. Ele permite aos desenvolvedores combinar fluxos de dados com registros históricos ou dados de referência para derivar informações comerciais com facilidade e rapidez.

Com alguns cliques no Portal do Azure, os clientes podem criar um trabalho de streaming usando uma linguagem semelhante ao SQL para especificar as transformações e monitorar a escala/velocidade do seu pipeline de fluxo geral. O serviço pode ser facilmente escalonado de poucos kilobytes a um gigabyte ou mais de eventos processados por segundo. A maioria das outras soluções de transmissão disponíveis hoje exigem que os clientes escrevam um código personalizado complexo, mas com a Análise de fluxo do Azure os clientes podem escrever SQLs simples, declarativos e familiares.

A Stream Analytics do Azure fornece uma variedade de operadores desde filtros simples até correlações complexas. A definição de operações em janelas com base no tempo como agregações em janelas ou correlacionando fluxos múltiplos para detectar padrões como sequências, ou mesmo comparando condições atuais com valores e modelos históricos, pode ser feita em questão de minutos utilizando um conjunto simples de operadores de Linguagem de consulta de Stream Analytics semelhantes ao SQL. Especificar um pipeline de fluxo é tão simples quanto configurar suas entradas e saídas e fornecer uma consulta do tipo SQL descrevendo as transformações desejadas. Apesar de isso ser suficiente para a maioria dos casos simples, os cenários mais complexos e de maior escala se beneficiam da capacidade de configuração da Stream Analytics. Os usuários podem determinar quanto poder de computação dedicar a cada etapa do pipeline para atingir a taxa de transferência de pico desejada.

No momento, a Stream Analytics do Azure se conecta diretamente ao Hub de eventos do Azure para a inclusão de fluxo e o serviço Blob do Azure para dados históricos. Usando o intervalo de interfaces de entrada e saída do Hub de eventos do Azure, é fácil integrar a Stream Analytics do Azure a outras fontes de dados e mecanismos de processamento sem perder a natureza do streaming das computações. Os dados podem ser gravados da Stream Analytics para o Armazenamento do Azure, onde podem posteriormente ser processados novamente como uma série de eventos, ou usados em outros formatos de análise de lote usando o HDInsight do Azure. A Stream Analytics do Azure utiliza anos de trabalho do Microsoft Research no desenvolvimento de mecanismos de streaming altamente ajustados para processamento de detecção de hora, bem como integrações de linguagem para tais especificações intuitivas. A Stream Analytics do Azure é criada aproveitando a comunidade de software livre do Hadoop, YARN e REEF para escalar horizontalmente o processamento.


## Principais recursos

### Fácil de usar
A Análise de fluxo dá suporte a um modelo de consulta simples e declarativo para descrever as transformações. Para otimizar a facilidade de uso, selecionamos uma variante do SQL como linguagem específica do domínio (DSL), e podemos proteger os clientes das complexidades técnicas significativas subjacentes ao nosso sistema de transmissão. Usando a Linguagem de consulta de Stream Analytics, você pode implementar funções temporais rapida e facilmente, incluindo junções com base temporal, agregações em janela, filtros temporais, bem como outras operações comuns, como junções, agregações, projeções e filtros.

Embora estendamos a semântica do SQL de algumas maneiras intuitivas para um usuário do SQL, permanecemos dentro dos limites sintáticos do SQL padrão para facilitar a implementação e o uso da ferramenta. Para obter informações adicionais sobre a nossa linguagem de consulta, consulte a seção "Próximas etapas".

### Escalabilidade
A Análise de fluxo é capaz de lidar com altas taxas de transferência de evento, de até 1GB por segundo. A Integração com Hubs de eventos do Azure permite que a solução assimile milhões de eventos por segundo, provenientes de dispositivos conectados, clique em fluxos e arquivos de log, para citar alguns. Para conseguir isso, aproveitamos a funcionalidade de particionamento dos Hubs de eventos que podem produzir 1 MB/s por partição. A Stream Analytics dá suporte ao particionamento enquanto processa esses eventos assimilados ao longo dos eixos horizontal e vertical. Um usuário pode particionar o cálculo em uma série de etapas lógicas na definição de consulta, cada um com a capacidade de ser mais particionados em elementos de execução paralela aos dados. Ao longo do tempo, a Stream Analytics gerará automaticamente a escala com base na taxa de ingestão de eventos, complexidade de processamento e das latências esperadas para permitir que os usuários personalizem sua carga de trabalho adequadamente. Para obter mais detalhes sobre a implementação de escalabilidade, consulte os links na seção de “Próximas etapas”.

### Confiabilidade, capacidade de repetição e recuperação rápida
A Stream Analytics ajuda a evitar que haja perda de dados e fornece continuidade de negócios na presença de falhas de nó por meio de seus recursos de ponto de verificação e de recuperação integrados. Esses recursos são fornecidos pela arquitetura do modelo de cliente âncora na Stream Analytics. O serviço é desenvolvido para o estado de persistência a fim de otimizar a continuação de falha de nó, novas computações e saída de cache para levar em conta falhas de downstream com eficiência.

Com a capacidade de manter o estado internamente, o serviço pode fornecer resultados reproduzíveis, onde é possível arquivar eventos e aplicar novamente o processamento no futuro, enquanto obtém os mesmos resultados. Esse recurso permite que os clientes voltem no tempo e investiguem computações de cenários como análise de causas básicas e análises hipotéticas. Esses recursos combinados fornecem uma recuperação rápida do processamento de falhas de nó e rápido reprocessamento de estado perdido.

### Baixa latência
A Análise de fluxo, no momento, é otimizado para latências observáveis de ponta-a-ponta no intervalo de menos de um segundo. Isso permite que o sistema processe continuamente com uma alta taxa de transferência. O modelo de comunicação é um modelo de lote adaptável, baseado em pull que opera com base em tempos limites configurados e limites de tamanho. Portanto, eventos e outros registros são divididas em lotes até que os limites sejam atingidos. Mesmo com a alta taxa de transferência, o sistema é capaz de fornecer menos latências.

### Dados de referência
A Análise de fluxo fornece aos usuários a capacidade de especificar e usar dados de referência. Eles podem ser dados históricos ou que são alterados com menos frequência ao longo do tempo. O sistema simplifica o uso dos dados de referência para que sejam tratados como qualquer outro fluxo de eventos recebidos para se unir a outros fluxos de eventos incluídos em tempo real para executar transformações. Para obter informações adicionais sobre a construção e o uso dos dados de referência, consulte a seção “Próximas etapas”.



## Motivações de negócios para a escolha de Stream Analytics do Azure

### Baixo custo
O serviço de Análise de fluxo é otimizado para fornecer baixíssimos custos aos usuários para que continuem mantendo soluções de análise em tempo real. O serviço foi desenvolvido para que você pague com base no uso. O uso é obtido com base no volume de eventos processados e na quantidade de capacidade de computação provisionada no cluster para lidar com os respectivos trabalhos de streaming.

### Implementação rápida
Com a Análise de fluxo, você pode ter uma solução de processamento de eventos em tempo real altamente dimensionável, sem nenhum custo com hardware ou demais custos antecipados, sem instalação ou configuração demorada. Azure cuida de tudo isso para você. Você pode estar pronto para usar em minutos, simplesmente assinando o serviço por meio do portal do Azure. A interface do usuário amigável no portal percorre um guia de início rápido passo a passo para ter a fonte de entrada configurada e testada e o seu repositório de saída configurado e testado. Um editor de consulta fácil fornece preenchimento automático e recursos de recomendação.

### Rápido desenvolvimento
A Stream Analytics ajuda a reduzir o atrito e a complexidade ao desenvolver funções de análise para escalar horizontalmente sistemas distribuídos. Os desenvolvedores simplesmente descrevem sua transformação desejada em uma linguagem de consulta com base no SQL e o sistema distribuirá automaticamente a carga de trabalho para dimensionamento, desempenho e resiliência, eliminando a necessidade de programação de procedimento, necessária na maioria das soluções de processamento de fluxo. Por meio de suas capacidades integradas e do usuário de um portal amigável, a Stream Analytics permite que um desenvolvedor de alto nível processe rapida e facilmente em tempo real sem ter que se preocupar com coisas como compras de infraestrutura, manutenção e dimensionamento constantes.

## Cenários e casos de uso

### Análise em tempo real para a Internet das Coisas
Conforme os dispositivos se tornam mais inteligentes e mais dispositivos são criados com recursos de comunicação, a expectativa quanto ao que pode ser feito com os dados gerados e coletados desses dispositivos continuam a evoluir tanto nos espaços comerciais quanto nos de consumo. Espera-se que com tantos dados disponíveis, possamos rapidamente combinar e processar os dados, ganhando mais visão do ambiente a nossa volta e dos dispositivos que usamos regularmente. Um cenário IoT canônico pode ser descrito por meio de um exemplo de máquina de venda automática:

As Máquinas de vendas enviam regularmente dados como estoque, status, temperatura, etc. do produto para um gateway de campo (se o computador não tiver IP) ou para um gateway de nuvem (se tiver IP) para inclusão no sistema. O fluxo de dados recebidos é processado e transformado, de modo que a saída computada possa ser imediatamente retroalimentada através de gateways ao dispositivo para executar uma ação correspondente. Por exemplo: se o computador superaquecer, talvez o dispositivo precise se reinicializar ou executar automaticamente uma atualização de firmware sem qualquer intervenção humana. A saída processada também pode disparar outros alertas e notificações para agendar automaticamente um técnico com base nos eventos.

Conforme mais dados são coletados e processados, também pode ser usado o Aprendizado de Máquina para desenvolver e aprender de padrões vistos no sistema. Por exemplo, a possibilidade de prever quando as máquinas talvez precisem de manutenção com base nos fluxos de eventos em tempo real alimentados no sistema ou na manutenção preventiva agendada na rota de um técnico quando as coisas estão para dar errado.

Esse padrão de dispositivos enviando informações para um sistema de processamento e, possivelmente, agindo com base nos resultados processados em tempo real pode ser visto comumente em casos de uso de IoT. Outros cenários incluem carros conectados, análise de sequência de cliques e gerenciamento de recursos. Para otimizar esse ciclo de comentários para latências de baixa e alta taxa de transferência, a Stream Analytics pode ser usada para incluir dados de Hubs de eventos do Azure, processando e enviando os dados diretamente aos Hubs de eventos para o respectivo dispositivo assinado para executar a ação apropriada.


![Análise em tempo real da Stream Analytics do Azure para a Internet das Coisas (IoT)][img.stream.analytics.scenario1]


### Análise de telemetria e de log por meio de painéis
Com o crescimento dos volumes de dispositivos, aplicativos e máquinas, um caso comum de uso corporativo para realizar negócios é a necessidade de monitorar e responder às necessidades de mudança nos negócios criando análises ricas, quase em tempo real. Um cenário canônico de análise de telemetria/log pode ser descrito usando um exemplo de aplicativo ou serviço online, mas o padrão é geralmente visto em empresas que coletam e relatam telemetria de aplicativo ou dispositivo:

O aplicativo ou serviço coletam regularmente dados de integridade. São coletados dados representando o status atual do aplicativo ou da infraestrutura em um ponto no tempo, logs de solicitação de usuário e outros dados que representam ações ou atividades executadas dentro do aplicativo. Historicamente, os dados são salvos em um blob ou em outros tipos de armazenamento de dados para processamento adicional.

Com a tendência recente de painéis em tempo real, além de salvar os dados em um blob ou outro tipo de repositório para análise histórica, os clientes desejam processar e transformar o fluxo de dados de entrada diretamente, de modo que possa ser fornecido imediatamente aos usuários finais na forma de painéis e/ou notificações quando a ação precisar ser tomada. Por exemplo, se um site do serviço ficar inativo, a equipe de operações pode ser notificada para começar a investigação e resolver o problema rapidamente. Em vários desses casos de uso, uma pessoa normalmente monitora um painel em tempo real, criado com base no conjunto de dados atualizados após o processamento dos dados assimilados por meio da Análise de fluxo.
 
![A telemetria e a análise de log de Stream Analytics via painéis][img.stream.analytics.scenario2]

### Evento de arquivamento para processamento futuro
As expectativas para execução rápida e ágil em empresas continuam a crescer. As empresas e os desenvolvedores têm optado por plataformas fáceis de usar, baseadas em nuvem para atender à demanda por mais agilidade e buscam plataformas que as habilitam a criar e processar um fluxo contínuo de dados gerados pelos seus sistemas em tempo próximo ao real. Hoje, esses clientes não conseguem utilizar um serviço que é otimizado para gravação de baixa latência para um armazenamento de dados e, portanto, acabam perdendo vários conjuntos cruciais de dados que podem revelar informações valiosas para os seus negócios. Um cenário canônico de evento de arquivamento pode ser descrito conforme segue:

Dados de vários dispositivos e plataformas, que são distribuídos em todo o mundo, são enviados para um coletor de dados centralizado. Quando os dados estiverem no local central, alguma transformação sem monitoração de estado será executada neles, como depuração de informações de identificação pessoal, adicionando marcação geográfica e executando a pesquisa IP. Os dados transformados são, então, arquivados no armazenamento de Blob de forma que possam ser facilmente consumidos pelo HDInsight ou outras ferramentas para processamento offline.

![Evento de arquivamento da Stream Analytics do Azure para processamento futuro][img.stream.analytics.scenario3]
 
## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54-->