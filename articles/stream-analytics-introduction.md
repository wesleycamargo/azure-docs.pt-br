<properties title="Introduction to Azure Stream Analytics" pageTitle="Introdução à análise de fluxo | Azure" description="Compreender a análise de fluxo do Azure" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Introdução à análise de fluxo do Azure

A Análise de fluxo do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem.

# Neste artigo

+ [Visão geral e motivação](#motivation) 
+ [Principais recursos](#capabilities)
+ [Motivações de negócios com a escolha de ﻿Análise de fluxo do Azure](#decision)
+ [Cenários e casos de uso](#scenarios)
+ [Próximas etapas](#neststeps)


##<a name="motivation"></a>Visão geral e motivação 

Grandes quantidades de dados estão fluindo em alta velocidade na rede hoje.  Organizações que podem processar e agir sobre esses dados em tempo real podem significativamente melhorar a eficiência e diferenciar-se no mercado.  Cenários de análise de fluxo em tempo real podem ser encontrados em todos os setores: análises e alertas personalizados sobre mercado de ações, em tempo real, oferecidos por empresas de serviços financeiros; detecção de fraudes em tempo real; serviços de proteção de identidade e de dados; ingestão e análise de dados confiáveis, gerados por sensores e atuadores incorporados em objetos físicos (IoT); análise de fluxo de cliques na Web; e aplicativos de CRM emitindo alertas quando a experiência do cliente em um período de tempo está degradada.  As empresas estão procurando a maneira mais flexível, confiável e econômica de fazer tal análise de dados de fluxo de eventos em tempo real para serem bem-sucedidas no mundo dos negócios modernos, altamente competitivo.  

Criação de sistemas de processamento de fluxo é uma tarefa complexa.  Operações de streaming como correlações e agregações não só precisam ser implementados com eficiência, mas também precisam ser escalonáveis e tolerantes a falhas.  Desafios operacionais adicionais são dispostos em camadas, incluindo implantação, depuração e monitoramento.  Os custos associados à criação e à manutenção de tal solução aumenta rapidamente.  Empresas maiores desistiram de pagar um alto custo com soluções personalizadas, enquanto pequenas empresas geralmente perdem a oportunidade devido à grande barreira de entrada e aos custos inviáveis associados a ele.  A Análise de fluxo do Azure soluciona esses desafios.

A Análise de fluxo do Azure é um serviço de computação de fluxo em tempo real totalmente gerenciado hospedado no Microsoft Azure, que oferece processamento de eventos complexos de fluxo de dados dimensionável, altamente resiliente e de baixa latência.  A Análise de fluxo do Azure permite que desenvolvedores combinem facilmente os fluxos de dados com registros históricos ou dados de referência para obter ideias de negócios de maneira rápida e fácil.  

Com alguns cliques no Portal do Azure, os clientes podem criar um trabalho de streaming usando uma linguagem semelhante ao SQL para especificar as transformações e monitorar a escala/velocidade do seu pipeline de fluxo geral. O serviço pode ser facilmente escalonado de poucos kilobytes a um gigabyte ou mais de eventos processados por segundo.  A maioria das outras soluções de transmissão disponíveis hoje exigem que os clientes escrevam um código personalizado complexo, mas com a Análise de fluxo do Azure os clientes podem escrever SQLs simples, declarativos e familiares.

A Análise de fluxo do Azure fornece uma variedade de operadores de filtros simples para correlações complexas.  A definição de operações em janelas, com base no tempo como agregações em janelas ou correlacionando fluxos múltiplos para detectar padrões como sequências, ou mesmo comparando condições atuais com valores e modelos históricos, pode ser feita em questão de minutos utilizando um conjunto simples de operadores de Linguagem de consulta de Análise de fluxo semelhantes ao SQL.  Especificar um pipeline de fluxo é tão simples quanto configurar suas entradas e saídas e fornecer uma consulta do tipo SQL descrevendo as transformações desejadas.  Apesar de isso ser suficiente para a maioria dos casos simples, os cenários de escala maiores e mais complexos se beneficiam de configurabilidade da Análise de fluxo.  Os usuários podem determinar quanto poder de computação dedicar a cada etapa do pipeline para atingir a taxa de transferência de pico desejada.

A Análise de fluxo do Azure no momento se conecta diretamente ao Hub de eventos do Azure para inclusão de fluxo e Blobs do Azure para dados históricos.  Usando o intervalo de interfaces de entrada e saída do Hub de eventos do Azure, é fácil integrar a Análise de fluxo do Azure com outras fontes de dados e mecanismos de processamento sem perder a natureza do streaming das computações.  Os dados podem ser gravados de Análise de fluxo para o Armazenamento do Azure onde ele pode posteriormente ser processado novamente como uma série de eventos, ou usado em outros formatos de análise de lote usando o HDInsight.  A Análise de fluxo do Azure utiliza anos de trabalho do Microsoft Research no desenvolvimento de mecanismos de streaming altamente ajustados para processamento de detecção de hora, bem como integrações de linguagem para tais especificações intuitivas.  A Análise de fluxo do Azure é criado aproveitando a comunidade de software livre do Hadoop, YARN e REEF para escalar horizontalmente o processamento.


##<a name="capabilities"></a>Principais recursos

###Fácil de usar
A Análise de fluxo dá suporte a um modelo de consulta simples e declarativo para descrever as transformações.  Para otimizar a facilidade de uso, selecionamos uma variante SQL, como o DSL, e podemos proteger clientes das complexidades técnicas significativas subjacentes ao nosso sistema de Streaming.  Usando a Linguagem de consulta de Análise de fluxo você pode implementar funções temporais rápida e facilmente, incluindo junções com base temporal, agregações em janela, filtros temporais, bem como outras operações comuns, como junções, agregações, projeções, filtros, etc.

Embora estendamos a semântica do SQL de algumas maneiras intuitivas para um usuário do SQL, permanecemos dentro dos limites sintáticos do SQL padrão para facilitar a implementação e o uso da ferramenta. Para obter informações adicionais sobre nossa linguagem de consulta, consulte a seção "Recursos da Análise de fluxo do Azure".

###Escalonável
A Análise de fluxo é capaz de lidar com altas taxas de transferência de evento, de até 1GB por segundo. A Integração com Hubs de eventos do Azure permite que a solução assimile milhões de eventos por segundo, provenientes de dispositivos conectados, clique em fluxos, arquivos de log, etc. para citar alguns. Para conseguir isso, aproveitamos a funcionalidade de particionamento dos Hubs de eventos que podem produzir 1 MB/s, por partição. A Análise de fluxo dá suporte ao particionamento enquanto processa esses eventos assimilados ao longo dos eixos horizontal e vertical.  Um usuário pode particionar o cálculo em uma série de etapas lógicas na definição de consulta, cada um com a capacidade de ser mais particionados em elementos de execução paralela aos dados.  Ao longo do tempo, a Análise de fluxo gerará automaticamente a escala com base na taxa de ingestão de eventos, complexidade de processamento e das latências esperadas para permitir que os usuários personalizem sua carga de trabalho adequadamente.  Para obter mais detalhes sobre a implementação de escalabilidade, consulte os links na seção de Recursos.  

###Recuperação confiável, repetível e rápida
A Análise de fluxo garante que não haja perda de dados e fornece continuidade de negócios na presença de falhas de nó por meio de seus recursos de ponto de verificação e de recuperação internos.  Isso é fornecido pela arquitetura do modelo de cliente-âncora da Análise do fluxo.  O serviço é desenvolvido para o estado de persistência a fim de otimizar a continuação de falha de nó, novas computações e saída de cache para levar em conta falhas de downstream com eficiência.  

Com a capacidade de manter o estado internamente, o serviço pode fornecer resultados reproduzíveis, onde é possível arquivar eventos e aplicar novamente o processamento no futuro, enquanto obtém os mesmos resultados.  Esse recurso permite que os clientes voltem no tempo e investiguem computações de cenários como análise de causas básicas e análises hipotéticas.  Esses recursos combinados fornecem recuperação rápida do processamento de falhas de nó, rápido reprocessamento de estado perdido voltando no tempo. 

###Baixa latência
A Análise de fluxo, no momento, é otimizado para latências observáveis de ponta-a-ponta no intervalo de menos de um segundo.  Isso permite que o sistema processe continuamente com uma alta taxa de transferência. O modelo de comunicação é um modelo de lote adaptável, baseado em pull que opera com base em tempos limites configurados e limites de tamanho.  Portanto, eventos e outros registros são divididas em lotes até que os limites sejam atingidos.  Portanto, mesmo com alta taxa de transferência, o sistema é capaz de fornecer menos latências.

###Dados de referência
A Análise de fluxo fornece aos usuários a capacidade de especificar e usar dados de referência.  Esses podem ser dados históricos ou que são alterados com menos frequência ao longo do tempo.  O sistema simplifica o uso dos dados de referência para que sejam tratados como qualquer outro fluxo eventos recebidos para se unir a outros fluxos de eventos incluídos em tempo real para executar transformações.  Para obter informações adicionais sobre a construção e o uso dos dados de referência, consulte a seção de Recursos.



##<a name="decision"></a> Motivações de negócios com a escolha de ﻿Análise de fluxo do Azure

###Custo
O serviço de Análise de fluxo é otimizado para fornecer baixíssimos custos aos usuários para que continuem mantendo soluções de análise em tempo real.  O serviço foi desenvolvido para que você pague com base no uso.  O uso é obtido com base no volume de eventos processados e na quantidade de capacidade de computação provisionada no cluster para lidar com os respectivos trabalhos de streaming.  

###Pronto para usar em minutos
Com a Análise de fluxo, você pode ter uma solução de processamento de eventos em tempo real altamente dimensionável, sem nenhum custo com hardware ou demais custos antecipados, sem instalação ou configuração demorada.  Azure cuida de tudo isso para você.  Você pode estar pronto para usar em minutos, simplesmente assinando o serviço por meio do portal do Azure.  A interface do usuário amigável do portal o conduz, passo a passo, por um guia de início rápido para configurar e testar sua fonte de entrada, seu repositório de saída e um editor de consulta fácil com preenchimento automático e recursos de recomendação.  

###Permite o desenvolvimento rápido
A Análise de fluxo ajuda a reduzir o atrito e a complexidade ao desenvolver funções de análise para escalar horizontalmente sistemas distribuídos.  Os desenvolvedores simplesmente descrevem sua transformação desejada em uma linguagem de consulta com base no SQL e o sistema distribuirá automaticamente a carga de trabalho para dimensionamento, desempenho e resiliência, eliminando a necessidade de programação de procedimento, necessária na maioria das soluções de processamento de fluxo.  Por meio de sua capacidade interna, integrada e o usuário de um portal amigável, a Análise de fluxo permite que um desenvolvedor de alto nível processe rápida e facilmente em tempo real sem ter que se preocupar sobre coisas como compras de infraestrutura, constante manutenção e dimensionamento. 

##<a name="scenarios"></a>Cenários e casos de uso

###Análise em tempo real para a Internet das coisas (IoT)
Como dispositivos tornam-se mais inteligentes e mais dispositivos são criados com recursos de comunicação, a expectativa quanto ao que pode ser feito com os dados gerados e coletados desses dispositivos continuam a evoluir tanto nos espaços comerciais quanto nos de consumo. Espera-se que com tantos dados disponíveis, possamos rapidamente combinar e processar os dados, ganhando mais visão do ambiente a nossa volta e dos dispositivos que usamos regularmente. Um cenário IoT canônico pode ser descrito usando o exemplo de Máquina de vendas. As Máquinas de vendas enviam regularmente dados como estoque, status, temperatura, etc. do produto ou para um gateway de campo (se o computador não for capaz de IP) ou para um gateway de nuvem (se capaz de IP) para inclusão no sistema. O fluxo de dados recebidos é processado e transformado, de modo que a saída computada possa ser imediatamente retroalimentada através de gateways ao dispositivo para executar uma ação correspondente.  Por exemplo: se o computador superaquecer, talvez o dispositivo precise se reinicializar ou executar automaticamente uma atualização de firmware sem qualquer intervenção humana.  A saída processada também pode disparar outros alertas e notificações para agendar automaticamente um técnico com base nos eventos. 

Conforme mais dados são coletados e processados, também pode ser usado o Aprendizado de Máquina para desenvolver e aprender de padrões vistos no sistema. Por exemplo, a possibilidade de prever quando máquinas talvez precisem de manutenção com base nos fluxos de eventos em tempo real alimentados na manutenção preventiva agendada ou de sistema na rota de um técnico quando as coisas estão para dar errado.

Esse padrão de dispositivo enviando informações para um sistema de processamento e, possivelmente, agir com base nos resultados processados em tempo real podem ser vistos comumente em casos de uso de IoT.  Outros desses cenários incluem carros conectados, análises de fluxo de cliques, gerenciamento de recursos, etc. Para otimizar esse ciclo de feedbacks para latências baixas e alta taxa de transferência, a Análise de fluxo pode ser usado para incluir dados do Hub de eventos do Azure, processando os dados e enviando os dados de volta para o Hub de eventos para o respectivo dispositivo assinado a fim de executar a ação apropriada.  


![Azure Stream Analytics real time analysis for the Internet of Things (IoT)][img.stream.analytics.scenario1]


###Análise de telemetria e de log por meio de painéis
Com crescimento dos volumes de dispositivos, aplicativos e máquinas, um caso comum de uso corporativo para realizar negócios é a necessidade de monitorar e responder às necessidades de mudança nos negócios criando análises ricas, quase em tempo real. O cenário canônico de Análise de log/Telemetria pode ser descrito utilizando o exemplo do Aplicativo ou Serviço online, no entanto, o padrão é geralmente visto em empresas que coletam e relatam por meio de aplicativos ou telemetria de dispositivo. O aplicativo ou serviço coletam regularmente dados de integridade.  São coletados dados representando o status atual do aplicativo ou da infraestrutura em um ponto no tempo, logs de solicitação de usuário e outros dados que representam ações ou atividades executadas dentro do aplicativo. Historicamente, os dados são salvos em um blob ou em outros tipos de armazenamento de dados para processamento adicional. Com a tendência recente de painéis em tempo real, além de salvar os dados em um blob ou outro tipo de armazenamento para análise histórica, os clientes desejam processar e transformar o fluxo de dados de entrada diretamente, de modo que possa ser fornecido imediatamente aos usuários finais na forma de Painéis e/ou Notificações quando a ação precisar ser tomada. Por exemplo, se um site do serviço ficar inativo, a equipe de operações pode ser notificada para começar a investigação e resolver o problema rapidamente. Em vários desses casos de uso, uma pessoa normalmente monitora um painel em tempo real, criado com base no conjunto de dados atualizados após o processamento dos dados assimilados por meio da Análise de fluxo. 
 
![Azure Stream Analytics telemetry and log analysis via dashboards][img.stream.analytics.scenario2]

###Evento de arquivamento para processamento futuro
As expectativas para execução rápida e ágil em empresas continuam a crescer. As empresas e os desenvolvedores têm optado por plataformas fáceis de usar, baseadas na nuvem para atender a demanda por mais agilidade e buscam plataformas que as habilitam a criar e processar um fluxo contínuo de dados gerados pelos seus sistemas em tempo próximo do real.  Hoje, esses clientes não conseguem utilizar um serviço que é otimizado para gravação de baixa latência para um armazenamento de dados e, portanto, acabam perdendo vários conjuntos cruciais de dados que podem revelar informações valiosas para seus negócios.  O cenário canônico de Evento arquivador pode ser descrito da seguinte maneira: Dados de vários dispositivos e plataformas que são distribuídos geograficamente em todo o mundo são enviados para um coletor de dados centralizado. Quando os dados estiverem no local central, algumas transformações sem monitoração de estado são executadas neles, como depuração de informações da PII, adicionando marcação geográfica, pesquisa IP, etc. Os dados transformados, em seguida, são arquivados no armazenamento de Blob de forma que possa ser facilmente consumido pelo HDInsight ou outras ferramentas de processamento offline.

![Azure Stream Analytics event archival for future processing][img.stream.analytics.scenario3]
 







<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##<a name="nextsteps"></a>Próximas etapas

- [Começar a usar a Análise de fluxo do Azure][stream.analytics.get.started]
- [guia do desenvolvedor da Análise de fluxo do Azure][stream.analytics.developer.guide]
- [Trabalhos da Análise de fluxo de escala do Azure][stream.analytics.scale.jobs]
- [Problemas conhecidos e limitações da Análise de fluxo do Azure][Stream.analytics.limitations]
- [Referência de linguagem de consulta da Análise de fluxo do Azure][Stream.analytics.query.language.reference]
- [Referência de API REST de gerenciamento da Análise de fluxo do Azure][stream.analytics.rest.api.reference] 



<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
