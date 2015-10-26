<properties
   pageTitle="Diretrizes de monitoramento e diagnóstico | Microsoft Azure"
   description="Práticas recomendadas para monitoramento de aplicativos distribuídos na nuvem."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Diretrizes de monitoramento e diagnóstico

![](media/best-practices-monitoring/pnp-logo.png)

## Visão geral
Serviços e aplicativos distribuídos em execução na nuvem são, por sua natureza, partes complexas de software que incluem muitas partes móveis. Em um ambiente de produção, é importante ser capaz de controlar a maneira como os usuários utilizam o seu sistema, monitorar a utilização de recursos e, de maneira geral, monitorar a integridade e o desempenho do sistema. Essas informações podem ser usadas como um auxílio de diagnóstico para detectar e corrigir problemas e para ajudar a identificar possíveis problemas e impedir que eles ocorram.

## Cenários de monitoramento e diagnóstico
O monitoramento permite que você obtenha uma visão geral de como um sistema está funcionando e é parte essencial da manutenção das metas de qualidade de serviço. Alguns cenários comuns para coleta de dados de monitoramento incluem:

- Garantir que o sistema permaneça íntegro.
- Monitorar a disponibilidade do sistema e dos elementos que o compõem.
- Manter o desempenho para garantir que a taxa de transferência do sistema não seja reduzida inesperadamente conforme o volume de trabalho aumentar.
- Garantir que o sistema cumpra com quaisquer SLAs que tenham sido acordados com os clientes.
- Proteger a privacidade e a segurança do sistema, dos usuários e de seus dados.
- Controlar as operações executadas para fins normativos ou de auditoria.
- Monitorar o uso diário do sistema e ajudar a identificar tendências que possam resultar em problemas se não forem resolvidas.
- Rastrear problemas que ocorrem, desde o relatório inicial por meio da análise de possíveis causas à retificação, atualizações de software resultantes e implantações.
- Controlar as operações e depurar versões de software.

> [AZURE.NOTE]Esta lista não se destina a ser completamente abrangente. Este documento se concentra nesses cenários como as situações mais comuns para realizar o monitoramento, mas pode haver outras menos comuns ou que sejam específicas para seu ambiente.

As seções a seguir descrevem esses cenários mais detalhadamente. As informações sobre cada cenário são abordadas no seguinte formato:

- Uma breve visão geral do cenário.
- Os requisitos típicos desse cenário.
- Os dados brutos de instrumentação necessários para dar suporte ao cenário e possíveis fontes de informações.
- Como esses dados brutos podem ser analisados e combinados para gerar informações de diagnóstico significativas.

## Monitoramento da integridade
Um sistema é íntegro se ele estiver em execução e for capaz de processar solicitações. A finalidade do monitoramento da integridade é gerar um instantâneo da integridade atual do sistema para que você possa verificar se todos os componentes do sistema estão funcionando conforme o esperado.

### Requisitos para monitoramento da integridade
Se qualquer parte do sistema for considerada como problemática, um operador deverá ser alertado rapidamente (em questão de segundos). O operador deve ser capaz de determinar quais partes do sistema estão funcionando normalmente e quais partes estão enfrentando problemas. A integridade do sistema pode ser realçada usando um sistema de semáforo; vermelho para problemático (o sistema parou), amarelo para parcialmente íntegro (o sistema está sendo executado com funcionalidade reduzida) e verde para completamente íntegro.

Um sistema de monitoramento de integridade abrangente permite que o operador faça uma busca detalhada no sistema para exibir o status de integridade dos componentes e subsistemas. Por exemplo, se o sistema geral for representado como parcialmente íntegro, o operador deve ser capaz de ter uma visão mais detalhada para determinar qual funcionalidade está indisponível no momento.

### Requisitos para coleta de dados, fontes de dados e instrumentação
Os dados brutos necessários para dar suporte ao monitoramento da integridade podem ser gerados como resultado de:

- Rastreando a execução de solicitações do usuário. Essas informações podem ser usadas para determinar quais solicitações tiveram êxito, quais falharam e quanto tempo cada solicitação leva.
- Monitoramento de usuário sintético. Este processo simula as etapas executadas pelo usuário e segue uma série predefinida de etapas. Os resultados de cada etapa devem ser capturados.
- Logs de exceções, falhas e avisos. Essas informações podem ser capturadas como resultado de instruções de rastreamento inseridas no código do aplicativo, bem como da recuperação de informações dos logs de eventos de qualquer serviço referenciado pelo sistema.
- Monitorando a integridade de quaisquer serviços de terceiros usados pelo sistema. Isso pode exigir a recuperação e análise de dados de integridade fornecidos por esses serviços, e essas informações podem ter diversos formatos.
- Monitoramento do ponto de extremidade. Esse mecanismo é descrito com mais detalhes na seção de Monitoramento da disponibilidade.
- Coletando informações de desempenho do ambiente, como utilização da CPU em segundo plano ou atividades de E/S (inclusive de rede).

### Analisando dados de integridade
O foco principal do monitoramento de integridade é indicar rapidamente se o sistema está em execução. A análise ativa dos dados imediatos pode acionar um alerta se for detectado que um componente crítico é problemático (ele não responde a uma série de pings consecutivos, por exemplo). O operador pode, em seguida, executar a ação corretiva apropriada.

Um sistema mais avançado pode incluir um elemento de previsão que realiza uma análise fria das cargas de trabalho recentes e atuais para identificar tendências e determinar se é provável que o sistema permaneça íntegro ou se recursos adicionais serão necessários. Este elemento de previsão deve ser baseado em métricas de desempenho críticas, como a taxa de solicitações dirigidas a cada serviço ou subsistema, os tempos de resposta dessas solicitações e o volume de dados que entram e saem de cada serviço. Se o valor de qualquer métrica ultrapassar um limite definido, o sistema poderá gerar um alerta para habilitar um operador ou o dimensionamento automático (se disponível) a executar as ações preventivas necessárias para manter a integridade do sistema. Essas ações podem envolver acrescentar recursos, reiniciar um ou mais serviços que estão falhando ou aplicar limitações a solicitações de prioridade mais baixa.

## Monitoramento da disponibilidade
Um sistema realmente íntegro requer que os componentes e os subsistemas que compõem o sistema estejam disponíveis. O monitoramento da disponibilidade está intimamente relacionado ao monitoramento da integridade, mas enquanto o monitoramento da integridade fornece uma exibição imediata da integridade atual do sistema, o monitoramento da disponibilidade se ocupa de monitorar a disponibilidade do sistema e de seus componentes para gerar estatísticas relacionadas ao tempo de atividade do sistema.

Em muitos sistemas, alguns componentes (como um banco de dados) são configurados com redundância interna para permitir um failover rápido caso ocorram falhas graves ou perda de conectividade. Idealmente, os usuários não devem estar cientes da ocorrência da falha, mas, da perspectiva de monitoramento da disponibilidade, é necessário reunir o máximo possível de informações sobre essas falhas para tentar determinar a causa e executar ações corretivas para impedir sua recorrência.

Os dados necessários para acompanhar a disponibilidade podem ser dependentes de diversos fatores de nível inferior, muitos dos quais podem ser específicos ao aplicativo, sistema e ambiente. Um sistema de monitoramento eficaz captura os dados de disponibilidade que correspondem a esses fatores de nível inferior e os agrega para fornecer uma visão geral do sistema. Por exemplo, em um sistema de comércio eletrônico, a funcionalidade de negócios que permite que um cliente faça pedidos pode depender do repositório no qual detalhes do pedido são armazenados e do sistema de pagamento que processa as transações monetárias para pagar por esses pedidos. A disponibilidade da parte de inserção de pedidos do sistema é, portanto, uma função da disponibilidade do repositório e do subsistema de pagamento.

### Requisitos para monitoramento da disponibilidade
Um operador também deve ser capaz de visualizar o histórico de disponibilidade de cada sistema e subsistema, bem como usar essas informações para identificar tendências que possam estar causando falhas periódicas em um ou mais subsistemas (serviços começam a falhar em uma determinada hora do dia que corresponde ao pico de processamento?)

Além de fornecer uma visão imediata e do histórico da disponibilidade ou de cada subsistema, uma solução de monitoramento também deve ser capaz de alertar rapidamente o operador quando um ou mais serviços falharem ou quando os usuários não conseguirem se conectar aos serviços. Não se trata apenas de monitorar cada serviço, mas também de examinar as ações que estão sendo executadas por cada usuário se essas ações falharem ao tentarem se comunicar com um serviço. Até certo ponto, um certo nível de falha de conectividade é normal e pode ocorrer devido a erros temporários, mas pode ser útil permitir que o sistema emita um alerta de número de falhas de conectividade em um subsistema especificado que ocorrerem durante um período específico de tempo.

### Requisitos para coleta de dados, fontes de dados e instrumentação
Assim como com o monitoramento de integridade, os dados brutos necessários para dar suporte ao monitoramento da disponibilidade podem ser gerados como resultado do monitoramento do usuário sintético e do log de quaisquer exceções, falhas e avisos que ocorrerem. Além disso, dados de disponibilidade podem ser obtidos realizando o monitoramento do ponto de extremidade. O aplicativo pode expor um ou mais pontos de extremidade de integridade, cada um deles testando o acesso a uma área funcional do sistema. O sistema de monitoramento pode executar um ping de cada ponto de extremidade seguindo uma cronograma definido e coletar os resultados (sucesso ou falha).

Todas as falhas de conectividade de rede e tempos limite, bem como novas tentativas de conexão, devem ser registrados. Todos os dados devem ter carimbo de data/hora.

<a name="analyzing-availablity-data"></a>
### Analisando dados de disponibilidade
Os dados de instrumentação devem ser agregados e correlacionados para dar suporte aos seguintes tipos de análise:

- A disponibilidade imediata do sistema e dos subsistemas.
- As taxas de falha de disponibilidade do sistema e dos subsistemas. O ideal é que um operador seja capaz de correlacionar falhas a atividades específicas; o que estava acontecendo quando o sistema falhou?
- Uma exibição do histórico de taxas de falha do sistema ou de quaisquer subsistemas em qualquer período de tempo especificado, assim como o carregamento do sistema (número de solicitações de usuário,s por exemplo) quando uma falha ocorreu.
- Os motivos para a indisponibilidade do sistema ou de quaisquer subsistemas. Por exemplo, o serviço não é executado, perda de conectividade, há conectividade mas é atingido o tempo limite e há conectividade mas são retornados erros.

Você pode calcular o percentual de disponibilidade de um serviço em um período de tempo usando a fórmula:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Isso é útil para fins de SLA (o [Monitoramento de SLA](#SLA-monitoring) é descrito em mais detalhes posteriormente neste guia). A definição de _Tempo de inatividade_ depende do serviço. Por exemplo, o Visual Studio Online define tempo de inatividade como o período durante o qual as tentativas do cliente de se conectar ao serviço levam mais de 120 segundos e todas as operações básicas de gravação e leitura falham após a conexão ser estabelecida dentro desse período.

## Monitoramento de desempenho
Conforme o sistema é colocado cada vez mais sob pressão enquanto o volume de usuários aumenta e o tamanho dos conjuntos de dados que esses usuários acessam cresce, a possível falha de um ou mais componentes se torna provável. Frequentemente, a falha de um componente é precedida por uma queda no desempenho. Se conseguir detectar uma redução desse tipo, você pode tomar medidas proativas para corrigir a situação.

O desempenho do sistema depende de vários fatores. Cada fator normalmente é medido usando KPIs (indicadores chave de desempenho), como o número de transações de banco de dados por segundo ou o volume de solicitações de rede que são atendidas com êxito em um determinado período de tempo. Alguns desses KPIs podem estar disponíveis como medidas de desempenho específicas, enquanto outros podem ser derivados de uma combinação de métricas.

> [AZURE.NOTE]Para determinar se o desempenho é bom ou insatisfatório, você precisa compreender o nível de desempenho em que o sistema deveria ser capaz de ser executado. Para isso, é necessário observar o sistema enquanto ele está funcionando com uma carga típica e capturar os dados de cada KPI ao longo de um período de tempo. Isso pode envolver executar o sistema com uma carga simulada em um ambiente de teste e coletar os dados apropriados antes de implantar o sistema em um ambiente de produção.

> Você também deve garantir que o monitoramento para fins de desempenho não se torne uma carga desnecessária para o sistema. Talvez você possa ajustar dinamicamente o nível de detalhes dos dados que o processo de monitoramento de desempenho coleta.

### Requisitos para monitoramento do desempenho
Para examinar o desempenho do sistema, normalmente é necessário que o operador consulte informações que incluem:

- As taxas de resposta para as solicitações de usuários.
- O número de solicitações de usuários simultâneas.
- O volume de tráfego de rede.
- As taxas com as quais as transações de negócios estão sendo concluídas.
- O tempo médio de processamento das solicitações.

Também pode ser útil fornecer ferramentas que permitem ao operador auxiliar na detecção de correlações, como:

- O número de usuários simultâneos versus os tempos de latência de solicitação (quanto tempo leva para iniciar o processamento de uma solicitação após o usuário tê-la enviado).
- O número de usuários simultâneos versus o tempo médio de resposta (quanto tempo leva para concluir uma solicitação após ela começar a ser processada).
- O volume de solicitações versus o número de erros de processamento.

Além dessas informações funcionais mais amplas, o operador também deve ser capaz de ter uma visão detalhada do desempenho de cada componente do sistema. Esses dados normalmente são fornecidos usando contadores de desempenho de nível mais detalhado, rastreando informações como:

- Utilização da memória,
- Número de threads,
- Tempo de processamento da CPU,
- Tamanho da fila de solicitações,
- Taxas e erros de E/S de rede ou de disco,
- Número de bytes gravados ou lidos,
- Indicadores de middleware, como tamanho da fila.

Todas as visualizações devem permitir que um operador especifique um período de tempo. Os dados exibidos podem ser um instantâneo da situação atual e/ou um histórico do desempenho.

O operador deve ser capaz de gerar um alerta com base em qualquer medida de desempenho para qualquer valor determinado durante qualquer intervalo de tempo especificado.

### Requisitos para coleta de dados, fontes de dados e instrumentação
Os dados de desempenho de alto nível (taxa de transferência, número de usuários simultâneos, número de transações de negócios, taxas de erro e assim por diante) podem ser obtidos ao monitorar o progresso das solicitações de usuários conforme elas chegam e passam pelo sistema. Isso envolve a incorporação de instruções de rastreamento nos pontos-chave do código do aplicativo, em conjunto com informações de tempo. Todas as falhas, exceções e avisos devem ser capturados com dados suficientes para que possam ser correlacionados às solicitações que os causaram. O log de IIS é outra fonte útil.

Se possível, você também deve capturar dados de desempenho de quaisquer sistemas externos que o aplicativo utilizar. Esses sistemas externos podem fornecer seus próprios contadores de desempenho ou outros recursos para solicitação de dados de desempenho. Se isso não for possível, você deve registrar informações como a hora de início e de término de cada solicitação feita para um sistema externo, junto com o status (êxito, falha ou aviso) da operação. Por exemplo, você pode usar uma abordagem de cronômetro para solicitações de tempo; inicie um cronômetro quando a solicitação for iniciada e pare o cronômetro quando ela for concluída.

Dados de desempenho mais detalhados para componentes individuais de um sistema podem estar disponíveis por meio de recursos como contadores de desempenho do Windows e o diagnóstico do Azure.

### Analisando dados de desempenho
Muito do trabalho de análise consiste em agregar dados de desempenho por tipo de solicitação de usuário (como adicionar um item a um carrinho de compras ou realizar o processo de check-out em um sistema de comércio eletrônico) e/ou pelo subsistema ou serviço ao qual cada solicitação é enviada.

Outro requisito comum é resumir os dados de desempenho em percentis selecionados. Por exemplo, determinar os tempos de resposta para 99% das solicitações de 95% das solicitações e 70% das solicitações. Pode haver metas de SLA ou outras metas definidas para cada percentil. Os resultados em andamento devem ser relatados quase em tempo real para ajudar a detectar problemas imediatos, bem como ser agregados ao longo do tempo para fins estatísticos.

No caso de problemas de latência com impacto sobre o desempenho, o operador deve conseguir identificar rapidamente a causa do afunilamento examinando a latência de cada etapa executada por cada solicitação. Os dados de desempenho devem, portanto, fornecer um meio de correlacionar as medidas de desempenho de cada etapa associá-las a uma solicitação específica.

Dependendo dos requisitos de visualização, pode ser útil gerar e armazenar um cubo de dados contendo exibições dos dados brutos para permitir análises e consultas ad hoc complexas das informações de desempenho.

## Monitoramento de segurança
Todos os sistemas comerciais que contêm dados confidenciais devem implementar uma estrutura de segurança. A complexidade do mecanismo de segurança geralmente é uma função da confidencialidade dos dados. Em um sistema que requer que os usuários sejam autenticados, você deve registrar todas as tentativas de logon, quer elas falhem ou tenham êxito. Além disso, todas as operações executadas e os detalhes de todos os recursos acessados por um usuário autenticado devem ser registrados em log. Quando o usuário encerra a sessão e faz logoff, essas informações também devem ser registradas.

O monitoramento pode ajudar a detectar ataques ao sistema. Por exemplo, um grande número de tentativas de logon com falha pode indicar um ataque de força bruta, ou um aumento inesperado das solicitações pode ser o resultado de um ataque de DDoS. Você deve estar preparado para monitorar todas as solicitações para todos os recursos, independentemente da origem dessas solicitações. Um sistema com uma vulnerabilidade de logon pode expor acidentalmente recursos para o mundo exterior sem exigir que um usuário de fato faça logon.

### Requisitos para monitoramento de segurança
Os aspectos mais importantes do monitoramento de segurança devem possibilitar que o operador faça, rapidamente, o seguinte:

- Detectar tentativas de invasão por uma entidade não autenticada,
- Identificar tentativas de entidades de executar operações em dados cujo acesso não lhes foi concedido,
- Determinar se o sistema, ou alguma parte do sistema, está sob ataque externo ou interno (por exemplo, um usuário autenticado mal-intencionado pode estar tentando derrubar o sistema).

Para dar suporte a esses requisitos, o operador deve ser notificado:

- De quaisquer tentativas repetidas de logon com falha feitas pela mesma conta dentro de um período de tempo especificado.
- Se a mesma conta autenticada tentar repetidamente acessar um recurso proibido durante um período de tempo especificado.
- Se ocorrer um grande número de solicitações não autenticadas ou não autorizadas durante um período de tempo especificado.

As informações fornecidas ao operador devem incluir o endereço do host de origem de cada solicitação. Se as violações de segurança forem provenientes, regularmente, de um determinado intervalo de endereços, esses hosts podem ser bloqueados.

Um aspecto fundamental da manutenção da segurança de um sistema é a capacidade de detectar rapidamente ações que desviam do padrão normal. Informações como o número de solicitações de logon com falha e/ou com êxito podem ser exibidas visualmente para ajudar a detectar se há um pico de atividade em um horário fora do comum (como usuários que fazem logon às 3h e executam um grande número de operações quando seu dia de trabalho começa às 9h). Essas informações também podem ser usadas para ajudar a configurar o dimensionamento automático baseado em tempo. Por exemplo, se o operador observar que um grande número de usuários faz logon regularmente em um determinado horário do dia, ele pode configurar a inicialização de serviços de autenticação adicionais para lidar com o volume de trabalho e depois desativar esses serviços quando o pico terminar.

### Requisitos para coleta de dados, fontes de dados e instrumentação
A segurança é um aspecto integral da maioria dos sistemas distribuídos, e é provável que os dados pertinentes sejam gerados em diversos pontos de um sistema. Você deve considerar a adoção de uma abordagem de SIEM (Gerenciamento de informações e eventos de segurança) para coletar informações relacionadas à segurança resultantes de eventos gerados pelo aplicativo, equipamento de rede, servidores, firewalls, software antivírus e outros elementos de prevenção de intrusões.

O monitoramento de segurança pode incorporar dados de ferramentas que não fazem parte do seu aplicativo, como utilitários que identificam atividades de varredura de porta por órgãos externos ou filtros de rede que detectam tentativas de acesso não autenticado ao seu aplicativo e seus dados.

Em todo caso, os dados coletados devem permitir que o administrador determine a natureza de qualquer ataque e tome as medidas apropriadas.

### Analisando dados de segurança
Um recurso do monitoramento de segurança é a variedade de fontes das quais os dados surgem. Os diferentes formatos e níveis de detalhe geralmente exigem uma análise complexa dos dados capturados, para uni-lis em um thread de informações coerente. Exceto pelos casos mais simples (como a detecção de um grande número de tentativas de logon com falha ou tentativas repetidas de acesso não autorizado a recursos críticos), talvez não seja possível executar nenhum processamento complexo automatizado dos dados de segurança, e talvez seja preferível gravar esses dados, com carimbo de data/hora mas em sua forma original, em um repositório seguro para permitir sua análise manual por especialistas.

<a name="SLA-monitoring"></a>

## Monitoramento de SLA
Muitos sistemas comerciais que dão suporte a clientes pagantes fazem garantias quanto ao desempenho do sistema na forma de SLAs. Essencialmente, os SLAs declaram que o sistema é capaz de lidar com um volume definido de trabalho dentro de um período de tempo acordado e sem perder informações críticas. O monitoramento de SLA envolve garantir que o sistema possa atender a SLAs mensuráveis.

> [AZURE.NOTE]O monitoramento de SLA está intimamente relacionado ao monitoramento de desempenho, mas enquanto o monitoramento de desempenho tem a finalidade de garantir que o sistema tenha um funcionamento _ideal_, o monitoramento de SLA é governado por uma obrigação contratual que define o que _ideal_ realmente significa.

Frequentemente, os SLAs são definidos em termos de:

- Disponibilidade geral do sistema. Por exemplo, uma organização pode garantir que o sistema estará disponível para 99,9% do tempo. Isso equivale a não mais que 9 horas de inatividade por ano ou aproximadamente 10 minutos por semana.
- Taxa de transferência operacional. Esse aspecto frequentemente é expresso como um ou mais limites máximos, como garantir que o sistema será capaz de dar suporte a até 100.000 solicitações de usuários simultâneas ou de manipular 10.000 transações de negócios simultâneas.
- Tempo de resposta operacional. O sistema também pode fazer garantias quanto à taxa na qual as solicitações serão processadas. Por exemplo, 99% de todas as transações de negócios serão concluídas em 2 segundos, e nenhuma transação levará mais de 10 segundos.

> [AZURE.NOTE]Alguns contratos para sistemas comerciais também podem incluir SLAs referentes ao suporte ao cliente. Por exemplo, todas as solicitações de suporte técnico obterão uma resposta dentro de 5 minutos e 99% de todos os problemas deverão ser totalmente resolvidos dentro de 1 dia útil. Um [acompanhamento de questões](#issue-tracking) (descrito posteriormente nesta seção) eficaz é fundamental para atender a SLAs como esses.

### Requisitos para monitoramento de SLA
No nível mais amplo, um operador deve ser capaz de determinar rapidamente se o sistema está cumprindo com os SLAs ou não. E se não, fazer uma busca detalhada e examinar os fatores subjacentes para determinar os motivos para o desempenho abaixo do padrão.

Indicadores amplos comuns que podem ser representados visualmente incluem:

- O percentual de tempo de atividade do serviço.
- A taxa de transferência do aplicativo (medida em termos de transações e/ou operações bem-sucedidas por segundo).
- O número de solicitações do aplicativo com êxito/falha.
- O número de falhas, exceções e avisos de aplicativo e sistema.

Todos esses indicadores devem ser capazes de ser filtrados por um período de tempo especificado.

Um aplicativo em nuvem provavelmente incluirá um número de subsistemas e componentes. O operador deve ser capaz de selecionar um indicador amplo e ver como ele é composto por meio da integridade dos elementos subjacentes. Por exemplo, se o tempo de atividade do sistema geral ficar abaixo de um valor aceitável, o operador deve ser capaz de ter uma visão detalhadas para determinar quais elementos estão contribuindo para essa falha.

> [AZURE.NOTE]O tempo de atividade do sistema precisa ser definido com cuidado. Em um sistema que usa a redundância para garantir a disponibilidade máxima, instâncias individuais de elementos podem falhar, mas o sistema pode permanecer funcional. O tempo de atividade do sistema apresentado pelo monitoramento da integridade deve indicar o tempo de atividade agregado de cada elemento, e não necessariamente se o sistema foi de fato interrompido. Além disso, falhas podem ocorrer de modo isolado. Portanto, mesmo se um sistema específico não estiver disponível, o restante do sistema pode permanecer disponível, mas com funcionalidade reduzida (em um sistema de comércio eletrônico, uma falha no sistema pode impedir que o cliente faça pedidos, mas o cliente ainda poderá navegar pelo catálogo de produtos).

Para fins de alerta, o sistema deve ser capaz de acionar um evento se qualquer um dos indicadores amplos ultrapassar um limite especificado. Os detalhes dos diversos fatores que compõem o indicador mais amplo devem estar disponíveis como dados contextuais para o sistema de alertas.

### Requisitos para coleta de dados, fontes de dados e instrumentação
Os dados brutos necessários para dar suporte ao monitoramento de SLA são semelhantes aos necessários para monitorar o desempenho, em conjunto com alguns aspectos do monitoramento da integridade e da disponibilidade (consulte as seções pertinentes para obter mais detalhes). Você pode capturar dados:

- Monitorando pontos de extremidade.
- Logs de exceções, falhas e avisos.
- Rastreando a execução de solicitações do usuário.
- Monitorando a disponibilidade de quaisquer serviços de terceiros usados pelo sistema.
- Usando contadores e métricas de desempenho.

Todos os dados devem ter informações de temporização e carimbo de data/hora.

### Analisando dados de SLA
Os dados de instrumentação devem ser agregados para gerar um panorama do desempenho geral do sistema e para dar suporte à busca detalhada, de modo a possibilitar a análise do desempenho dos subsistemas subjacentes. Por exemplo, você deve ser capaz de:

- Calcular o número total de solicitações de usuários durante um determinado período e determinar a taxa de êxito e falha dessas solicitações.
- Combinar os tempos de resposta de solicitações de usuários para gerar uma exibição geral dos tempos de resposta do sistema.
- Analisar o progresso das solicitações de usuários; decompor o tempo de resposta geral de uma determinada solicitação nos tempos de resposta dos itens de trabalho individuais da solicitação.  
- Determinar a disponibilidade geral do sistema como um percentual de tempo de atividade para qualquer período específico.
- Analisar a disponibilidade de tempo percentual de cada um dos componentes e serviços individuais do sistema. Isso pode envolver a análise de logs gerados por serviços de terceiros.

Muitos sistemas comerciais precisam relatar os números do desempenho real em comparação com os SLAs acordados por um período específico, que normalmente é de um mês. Essas informações podem ser usadas para calcular créditos ou outras formas de reembolso para os clientes se os SLAs não forem cumpridos durante o período. Você pode calcular a disponibilidade de um serviço usando a técnica descrita na seção [Analisando dados de disponibilidade](#analyzing-availability-data).

Para fins internos, uma organização também pode controlar o número e a natureza dos incidentes que causaram a falha dos serviços. Aprender a resolver esses problemas rapidamente ou eliminá-los completamente ajudará a reduzir o tempo de inatividade e a cumprir aos SLAs.

## Auditoria
Dependendo da natureza do aplicativo, pode haver exigências estatutárias ou legais que especificam os requisitos para auditoria das operações executadas pelos usuários e registro de todo o acesso aos dados. A auditoria pode comprovar a vinculação de clientes a solicitações específicas. O não repúdio é um fator importante em muitos sistemas de comércio eletrônico para ajudar a manter a relação de confiança entre um cliente e a organização responsável pelo aplicativo ou serviço.

### Requisitos para auditoria
Um analista deve ser capaz de rastrear a sequência das operações de negócios executadas pelos usuários, para que você possa reconstruir as ações dos usuários. Isso pode ser necessário simplesmente para fins de registro ou como parte de uma investigação forense.

Informações de auditoria são altamente confidenciais, pois provavelmente incluirão dados que identificam os usuários do sistema, bem como as tarefas que estão realizando. Por esse motivo, é mais provável que as informações de auditoria sejam visualizadas como relatórios disponibilizados somente para analistas confiáveis, em vez de usar um sistema interativo que dê suporte a operações gráficas de busca detalhada. O analista deve ser capaz de gerar uma variedade de relatórios, como uma lista das atividades de todos os usuários que ocorrerem durante um período especificado, detalhando a cronologia das atividades de um único usuário, ou uma lista da sequência das operações executadas em um ou mais recursos.

### Requisitos para coleta de dados, fontes de dados e instrumentação
As principais fontes de informações para auditoria podem incluir:

- O sistema de segurança que gerencia a autenticação do usuário.
- Logs de rastreamento que registram a atividade de usuários.
- Logs de segurança que rastreiam todas as solicitações de rede identificáveis e não identificáveis.

O formato dos dados de auditoria e a maneira como eles são armazenados pode ser motivado por requisitos regulatórios. Por exemplo, talvez não seja possível limpar os dados de forma alguma (eles devem ser registrados em seu formato original), e o acesso ao repositório em que eles são mantidos deve ser protegido para evitar violações.

### Analisando dados de auditoria
Um analista deve ser capaz de acessar os dados brutos em sua totalidade e em seu formato original. Além de requisito de gerar relatórios de auditoria comuns, as ferramentas usadas para analisar esses dados provavelmente deverão especializadas e mantidas externamente ao sistema.

## Monitoramento de uso
O monitoramento de uso controla como os recursos e componentes de um aplicativo são usados. Os dados coletados podem ser utilizados para:

- Determinar quais recursos são usados intensamente e identificar quaisquer possíveis pontos problemáticos no sistema. Elementos de alto tráfego podem se beneficiar do particionamento funcional ou até mesmo da replicação para distribuir a carga de maneira mais uniforme. Essas informações também podem ser usadas para determinar quais recursos são usados com pouca frequência e são possíveis candidatos à desativação ou substituição em uma versão futura do sistema.
- Obter informações sobre os eventos operacionais do sistema em situação de uso normal. Por exemplo, em um site de comércio eletrônico, você pode registrar as informações estatísticas sobre o número de transações e o volume de clientes que são responsáveis por elas. Essas informações podem ser usada para o planejamento de capacidade à medida que o número de clientes crescer.
- Detectar (possivelmente de forma indireta) a satisfação do usuário com o desempenho ou a funcionalidade do sistema. Por exemplo, se um grande número de clientes em um sistema de comércio eletrônico abandonar regularmente seus carrinhos de compras, isso pode ocorrer devido a um problema com a funcionalidade de check-out.
- Gerar informações de cobrança. Um aplicativo comercial ou um serviço multilocatário pode cobrar os clientes pelos recursos que eles usam.
- Aplicar cotas. Se um usuário de um sistema multilocatário ultrapassar sua cota paga de tempo de processamento ou uso de recursos durante um período especificado, seu acesso pode ser limitado ou o processamento pode ser reduzido.

### Requisitos para monitoramento de uso
Para examinar o uso do sistema, normalmente é necessário que o operador consulte informações que incluem:

- O número de solicitações processadas por cada subsistema e direcionadas a cada recurso.
- O trabalho executado por cada usuário.
- O volume de armazenamento de dados ocupado por cada usuário.
- Os recursos que estão sendo acessados por cada usuário.

O operador também deve ser capaz de gerar gráficos, exibindo por exemplo os usuários que mais consomem recursos ou os recursos acessados com maior frequência.

### Requisitos para coleta de dados, fontes de dados e instrumentação
O rastreamento do uso pode ser executado em um nível relativamente amplo, observando a hora de início e término de cada solicitação e a natureza da solicitação (ler, gravar e assim por diante, dependendo do recurso em questão). Você pode obter essas informações:

- Rastreando as atividades do usuário.
- Capturando contadores de desempenho que medem a utilização de cada recurso.
- Monitorando a utilização de CPU e E/S das operações executadas por cada usuário.

Para fins de medição, também é necessário ser capaz de identificar quais usuários são responsáveis por executar quais operações e os recursos que essas operações utilizam. As informações coletadas devem ser detalhadas o suficiente para permitir uma cobrança precisa.

<a name="issue-tracking"></a>
## Acompanhamento de questões
Os clientes e outros usuários podem relatar problemas se eventos ou comportamentos inesperados ocorrerem no sistema. O acompanhamento de questões é voltado gerenciar esses problemas, associá-los a esforços para resolver problemas subjacentes no sistema e informar os clientes de possíveis resoluções.

### Requisitos para o acompanhamento de questões
Frequentemente, o acompanhamento de questões é realizado usando um sistema separado que permite que os operadores registrem e relatem os detalhes dos problemas relatados pelos usuários. Esses detalhes podem incluir informações como as tarefas que o usuário estava tentando executar, sintomas do problema, a sequência dos eventos e quaisquer mensagens de aviso ou erro emitidas.

### Requisitos para coleta de dados, fontes de dados e instrumentação
A fonte inicial dos dados para o acompanhamento de questões é o usuário que relatou o problema em primeiro lugar. O usuário pode ser capaz de fornecer dados adicionais, como um despejo de memória (se o aplicativo incluir um componente que é executado na área de trabalho do usuário), um instantâneo da tela e a data e hora em que o erro ocorreu, em conjunto com qualquer outra informação ambiental, como sua localização.

Essas informações podem ser usadas para alimentar o esforço de depuração e ajudar a construir uma lista de pendências para futuras versões do software.

### Analisando dados de acompanhamento de questões
Diferentes usuários podem relatar o mesmo problema e o sistema de acompanhamento deve associar os relatórios comuns.

O progresso do esforço de depuração deve ser registrado em cada relatório de problema, e quando o problema é resolvido o cliente poderá ser informado sobre a solução.

Se um usuário relatar um problema conhecido, que tem uma solução conhecida no sistema de acompanhamento de questões, o operador deverá ser capaz de informar imediatamente a solução ao usuário.

## Rastreando operações e depurando versões de software.
Quando relata um problema, o usuário normalmente está ciente apenas do impacto imediato que ele tem sobre suas operações e pode somente relatar os resultados de sua própria experiência para um operador responsável por manter o sistema. Essas experiências geralmente são apenas um sintoma visível de um ou mais problemas fundamentais. Em muitos casos, um analista precisará estudar a cronologia das operações subjacentes para estabelecer a causa raiz do problema (este processo é conhecido como _Análise de causa raiz_).

> [AZURE.NOTE]A Análise de causa raiz pode revelar ineficiências no design de um aplicativo. Nessas situações, talvez seja possível refazer os elementos afetados e implantá-los como parte de uma versão subsequente. Esse processo requer um controle cuidadoso, e os componentes atualizados devem ser monitorados de perto.

### Requisitos para rastreamento e depuração
Para rastrear eventos inesperados e outros problemas, é vital que os dados de monitoramento forneçam informações suficientes não apenas sobre os problemas que ocorrem no nível mais amplo, mas também incluam detalhes suficientes para permitir que o analista rastreie as origens desses problemas e reconstrua a sequência dos eventos ocorridos. Essas informações devem ser suficientes para permitir que o analista diagnostique a causa raiz de quaisquer problemas, de modo que o desenvolvedor possa fazer as modificações necessárias para impedir que eles ocorram novamente.

### Requisitos para coleta de dados, fontes de dados e instrumentação
A solução de problemas pode envolver o rastreamento de todos os métodos (e seus parâmetros) chamados como parte de uma operação para construir uma árvore que descreva o fluxo lógico pelo sistema quando um cliente faz uma solicitação específica. Exceções e avisos gerados pelo sistema como resultado deste fluxo precisam ser capturados e registrados.

Para dar suporte à depuração, o sistema pode fornecer ganchos que permitam que o operador capture informações de estado em pontos importantes no sistema ou fornecer informações detalhadas passo a passo conforme a operação selecionada prossegue. Capturar de dados nesse nível de detalhe pode impor uma carga adicional ao sistema e deve ser um processo temporário, usado principalmente quando ocorre uma série muito incomum de eventos que é difícil de replicar, ou quando uma nova versão de um ou mais elementos de um sistema exige monitoramento cuidadoso para garantir que ela funcione conforme esperado.

## O pipeline de monitoramento e diagnóstico
Monitorar um sistema distribuído de larga escala representa um desafio significativo, e cada um dos cenários descritos na seção anterior não deve ser considerada, necessariamente, de forma isolada. É provável que haja uma sobreposição significativa dos dados de monitoramento e diagnóstico necessários para cada situação, embora talvez seja necessário processar e apresentar esses dados de diferentes maneiras. Por esses motivos, você deve adotar uma visão holística do monitoramento e do diagnóstico.

Você pode enxergar o processo de diagnóstico e monitoramento inteiro como um pipeline que compreende os estágios mostrados na Figura 1.

![](media/best-practices-monitoring/Pipeline.png)

_Figura 1. Os estágios do pipeline de monitoramento e diagnóstico_

A Figura 1 destaca como os dados para monitoramento e diagnóstico podem vir de uma variedade de fontes. O estágio de Instrumentação/coleta é voltado à instrumentação; determinar quais dados capturar, como capturá-los e como formatar esses dados para que eles possam ser examinados facilmente. A fase de Análise/diagnóstico usa os dados brutos para gerar informações significativas que podem ser usadas para determinar o estado do sistema. Essas informações podem ser usadas para tomar decisões sobre possíveis ações, e os resultados podem ser usados como entrada de volta na fase de Instrumentação/coleta. A fase de Visualização/alerta do estágio apresenta uma exibição de consumo do estado do sistema. Ela pode exibir informações quase em tempo real por meio de uma série de painéis e pode gerar relatórios, gráficos e tabelas para fornecer uma visão histórica dos dados que pode ajudar a identificar tendências de longo prazo. Se as informações indicarem a probabilidade de que um KPI ultrapasse os limites aceitáveis, esse estágio também pode acionar um alerta para um operador. Em alguns casos, um alerta também pode ser usado para acionar um processo automatizado que tenta realizar ações corretivas, como o dimensionamento automático.

Observe que estas etapas constituem um processo de fluxo contínuo em que os estágios acontecem em paralelo. Idealmente, todas as fases devem ser configuráveis dinamicamente. Em alguns pontos, especialmente quando um sistema tiver sido implantado recentemente ou estiver com problemas, pode ser necessário reunir dados estendidos com maior frequência. Em outras ocasiões, deve ser possível voltar à captura de um nível básico de informações essenciais para verificar se o sistema está funcionando corretamente.

Além disso, todo o processo de monitoramento deve ser considerado uma solução viva, em andamento, que está sujeita a melhorias e ajustes como resultado de comentários. Por exemplo, você pode começar pela medição de muitos fatores para determinar a integridade do sistema, mas a análise ao longo do tempo pode levar a um refinamento conforme você descartar medidas que não são relevantes, permitindo que você se concentre mais precisamente nos dados de que precisa, minimizando quaisquer ruídos de fundo.

## Fontes de dados de monitoramento e diagnóstico
As informações usadas pelo processo de monitoramento podem vir de várias fontes, como ilustrado na Figura 1. No nível do aplicativo, as informações vêm dos logs de rastreamento incorporados no código do sistema. Os desenvolvedores devem seguir uma abordagem padrão para controlar o fluxo de controle por meio de seu código (por exemplo, na entrada para um método, emitir uma mensagem de rastreamento que especifica o nome do método, a hora atual, o valor de cada parâmetro e outras informações pertinentes. Registrar as horas de entrada e saída também pode ser útil). Você deve registrar todas as exceções e avisos e certifique-se de manter um rastreamento completo de todos os avisos e exceções aninhadas. Idealmente, você também deve capturar informações que identifiquem o usuário que executa o código junto com informações de correlação da atividade (para controlar as solicitações à medida que elas passam pelo sistema) e registrar em log as tentativas de acessar todos os recursos, como bancos de dados, filas de mensagens, arquivos e outros serviços dependentes. Essas informações podem ser usadas para fins de auditoria e de medição.

Muitos aplicativos fazem uso de bibliotecas e estruturas para executar tarefas comuns como acessar um armazenamento de dados ou se comunicar em uma rede. Essas estruturas podem ser configuráveis para gerar suas próprias mensagens de rastreamento e informações de diagnóstico brutas, como taxas de transação, falhas e êxitos de transmissão de dados e assim por diante.

> [AZURE.NOTE]Muitas estruturas modernas publicam o desempenho e rastreiam eventos automaticamente, e capturar essas informações é simplesmente uma questão de fornecer um meio de recuperar e armazená-las onde elas puderem ser processadas e analisadas.

O sistema operacional que está executando o aplicativo pode ser uma fonte de informações detalhadas de todo o sistema, como contadores de desempenho que indicam o uso da CPU, a utilização da memória e as taxas de E/S. Também podem ser relatados erros do sistema operacional (por exemplo, uma falha ao abrir um arquivo corretamente).

Você também deve considerar a infraestrutura e os componentes subjacentes em que seu sistema é executado. Máquinas virtuais, redes virtuais e serviços de armazenamento podem ser fontes de importantes contadores de desempenho no nível da infraestrutura e outros dados de diagnóstico.

Se o seu aplicativo usar outros serviços externos, como um servidor Web ou sistema de gerenciamento de banco de dados, esses serviços podem publicar suas próprias informações de rastreamento, logs e contadores de desempenho. Exemplos incluem Exibições de gerenciamento dinâmico do SQL Server para rastrear as operações executadas em um banco de dados do SQL Server e logs de rastreamento do Internet Information Server para registrar as solicitações feitas a um servidor Web.

Conforme os componentes de um sistema são modificados e novas versões são implantadas, é importante ser capaz de atribuir problemas, eventos e métricas a cada versão. Essas informações devem ser vinculadas ao pipeline de versões para que os problemas de uma versão específica de um componente possam ser rastreados rapidamente e corrigidos.

Problemas de segurança podem ocorrer em qualquer ponto do sistema. Por exemplo, um usuário pode tentar fazer logon com uma ID de usuário ou senha inválida; um usuário autenticado pode tentar obter acesso não autorizado a um recurso ou um usuário pode fornecer uma chave inválida ou desatualizada para acessar informações criptografadas. Informações de segurança sobre solicitações bem-sucedidas e falhas sempre devem ser registradas.

A seção [Instrumentar um aplicativo](#instrumenting-an-application) contém mais orientações sobre as informações que você deve capturar, mas há várias estratégias que você pode usar para coletar essas informações em primeiro lugar:

- **Monitoramento de aplicativo/sistema**. Essa estratégia usa fontes internas ao aplicativo, às estruturas do aplicativo, ao sistema operacional e à infra-estrutura. O código do aplicativo pode gerar seus próprios dados de monitoramento em pontos importantes durante o ciclo de vida de uma solicitação de cliente. O aplicativo pode incluir instruções de rastreamento que podem ser habilitadas ou desabilitadas seletivamente conforme as circunstâncias ditarem. Também é possível inserir diagnóstico dinamicamente usando uma estrutura de diagnóstico. Normalmente, essas estruturas fornecem plug-ins que podem se conectar a vários pontos de instrumentação em seu código e capturar dados de rastreamento nesses pontos.

Além disso, seu código e/ou a infra-estrutura subjacente pode gerar eventos em pontos críticos. Agentes de monitoramento configurados para ouvir esses eventos podem registrar as informações do evento.

- **Monitoramento do usuário real**. Essa abordagem registra as interações entre um usuário e o aplicativo e observa o fluxo de cada solicitação e resposta. Essas informações podem ter um propósito duplo: podem ser usadas para medir o uso por cada usuário e para determinar se os usuários estão recebendo uma qualidade de serviço adequada (por exemplo, tempos de resposta rápidos, baixa latência e pequenos erros que ocorrem). Os dados capturados podem ser usados para identificar áreas de preocupação em que falhas ocorrem com mais frequência e elementos em que o sistema fica mais lento, possivelmente devido a pontos problemáticos no aplicativo ou alguma outra forma de afunilamento. Se essa abordagem for implementada com cuidado, é possível reconstruir os fluxos dos usuários pelo aplicativo para fins de teste e depuração.

	> [AZURE.IMPORTANT]Os dados capturados pelo monitoramento de usuários reais devem ser considerados altamente confidenciais, uma vez que podem incluir material confidencial. Se forem salvos, os dados capturados devem ser armazenados com segurança. Se os dados estiverem sendo usados para fins de monitoramento de desempenho ou depuração, todas as informações de identificação pessoal devem ser excluídas primeiro.

- **Monitoramento de usuário sintético**. Nessa abordagem, você deve escrever seu próprio cliente de teste, que simula um usuário e executa uma série de operações configuráveis típicas. Você pode monitorar o desempenho do cliente de teste para ajudar a determinar o estado do sistema. Você também pode usar várias instâncias do cliente de teste como parte de uma operação de teste de carga para estabelecer qual é a resposta do sistema sob cargas excessivas e que tipo de saída de monitoramento é gerada sob essas condições.

	> [AZURE.NOTE]Você pode implementar o monitoramento de usuários reais e sintéticos incluindo um código que rastreie e temporize a execução de chamadas de método e outras partes essenciais de um aplicativo.

- **Perfis**. Essa abordagem se destina principalmente a monitorar e melhorar o desempenho do aplicativo. Em vez de operar no nível funcional empregado pelo monitoramento de usuários reais e sintéticos, captura informações mais detalhadas conforme o aplicativo é executado. A criação de perfis pode ser implementada por meio de uma amostragem periódica do estado de execução de um aplicativo (determinando qual parte do código o aplicativo está executando em um determinado ponto no tempo) ou do uso de uma instrumentação que insere testes no código em momentos importantes (como no início e no fim de uma chamada de método) e registra quais métodos foram chamados, em que momento, e quanto tempo cada chamada levou. Esses dados podem, depois, ser analisados para determinar quais partes do aplicativo podem causar problemas de desempenho.

- **Monitoramento do ponto de extremidade**. Essa técnica usa um ou mais pontos de extremidade de diagnóstico expostos pelo aplicativo especificamente para habilitar o monitoramento. Um ponto de extremidade fornece um caminho para o código do aplicativo e pode retornar informações sobre a integridade do sistema. Pontos de extremidade diferentes podem se concentrar em diversos aspectos da funcionalidade. Você pode escrever seu próprio cliente de diagnóstico que envia solicitações periódicas para esses pontos de extremidade e assimilar as respostas. Essa abordagem é descrita mais detalhadamente em [Padrão de monitoramento de ponto de extremidade de integridade](https://msdn.microsoft.com/library/dn589789.aspx) no site da Microsoft.

Para ter a cobertura máxima, você deve usar essas técnicas em combinação umas com as outras.

<a name="instrumenting-an-application"></a>
## Instrumentar um aplicativo
A instrumentação é uma parte essencial do processo de monitoramento. Você só pode tomar decisões significativas sobre o desempenho e a integridade de um sistema se capturar dados que lhe permitirem tomar essas decisões. As informações reunidas usando a instrumentação devem ser suficientes para que você possa avaliar o desempenho, diagnosticar problemas e tomar decisões sem a necessidade de fazer logon um servidor de produção remoto para executar o rastreamento (e a depuração) manualmente.

Dados de instrumentação normalmente são compostos por informações gravadas em logs de rastreamento e métricas:

- O conteúdo de um log de rastreamento pode ser o resultado de dados textuais gravados pelo aplicativo, dados binários criados como resultado de um evento de rastreamento (se o aplicativo estiver usando o Rastreamento de Eventos para Windows – ETW) ou pode ser gerado por meio de logs de sistema que registram eventos resultantes de partes da infraestrutura, como um servidor Web. Mensagens de log textual geralmente são criadas para serem legíveis, mas também devem ser gravadas em um formato que permita que sejam analisadas facilmente por um sistema automatizado. Você também deve classificar os logs. Não grave todos os dados de rastreamento em um único log, mas use logs separados para gravar a saída do rastreamento por meio de diferentes aspectos operacionais do sistema. Isso permite que você filtre rapidamente as mensagens de log lendo o log adequado em vez de precisar processar um único arquivo longo. Nunca grave informações com requisitos de segurança diferentes (como informações de auditoria e dados de depuração) no mesmo log.

	> [AZURE.NOTE]Um log pode ser implementado como um arquivo no sistema de arquivos ou pode ser mantido em algum outro formato, como um blob no armazenamento de blobs. Informações de log também podem ser mantidas em um armazenamento mais estruturado, como linhas em uma tabela.

- As métricas geralmente são apenas uma medida ou contagem de algum aspecto ou recurso do sistema em um momento específico, com uma ou mais marcas ou dimensões associadas (às vezes chamado de _amostra_). Uma única instância de uma métrica não costuma ser útil quando isolada, portanto as métricas precisam ser capturadas ao longo do tempo. A principal questão a considerar é quais métricas você deve registrar e com que frequência. Frequentemente, gerar dados de métricas pode impor uma significativa carga adicional ao sistema, enquanto capturar métricas com pouca frequência pode causar a perda das circunstâncias que levam a um evento significativo. As considerações variam de métrica para métrica. Por exemplo, a utilização da CPU em um servidor pode variar significativamente segundo a segundo, mas a alta utilização se torna um problema apenas se for longa, durante vários minutos.

<a name="information-for-correlating-data"></a>
### Informações de correlação de dados
Você pode monitorar facilmente contadores de desempenho individuais no nível do sistema, capturar métricas de recursos e obter informações de rastreamento do aplicativo por meio de vários arquivos de log, mas algumas formas de monitoramento requerem que o estágio de análise e diagnóstico no pipeline de monitoramento correlacione os dados recuperados de várias fontes. Esses dados podem assumir diversas formas como dados brutos, e o processo de análise deve ter dados de instrumentação suficientes para ser capaz de mapear essas diferentes formas. Por exemplo, no nível da estrutura do aplicativo, uma tarefa pode ser identificada por uma ID de thread, mas dentro de um aplicativo o mesmo trabalho pode ser associado à ID do usuário que executa a tarefa. Além disso, é improvável que haja um mapeamento individual dos threads e solicitações de usuário, uma vez que operações assíncronas podem reutilizar os mesmos threads para executar operações em nome de mais de um usuário. Para complicar ainda mais, uma única solicitação pode ser manipulada por mais de um thread ao longo de sua execução no sistema. Se possível, associe cada solicitação a uma ID de atividade exclusiva que é propagada pelo sistema como parte do contexto da solicitação (a técnica para gerar e incluir IDs de atividade em informações de rastreamento depende da tecnologia usada para capturar os dados de rastreamento).

Todos os dados de monitoramento devem receber o carimbo de data/hora da mesma maneira. Para ter consistência, registre todas as datas e horas usando o Tempo Universal Coordenado. Isso ajudará a rastrear com mais facilidade as sequências de eventos.

> [AZURE.NOTE]Computadores que operam em redes e fusos horários diferentes podem não estar sincronizados, de modo que você não deve depender apenas do uso de carimbos de data/hora para correlacionar dados de instrumentação que abrangem várias máquinas.

### Quais informações os dados de instrumentação devem incluir?
Considere os seguintes pontos ao decidir quais dados de instrumentação você precisa coletar:

- Informações capturadas por eventos de rastreamento devem ser legíveis por pessoas e máquinas. Você deve adotar esquemas bem definidos para essas informações para facilitar o processamento automatizado de dados de log entre sistemas e dar consistência às operações e à equipe de engenharia que lê os logs. Inclua informações de ambiente, como o ambiente de implantação, a máquina na qual o processo está sendo executado, os detalhes do processo e a pilha de chamadas.  
- A criação de perfis pode impor uma sobrecarga significativa ao sistema e deve ser habilitada apenas quando necessário. A criação de perfis usando instrumentação registra um evento (como uma chamada de método) toda vez que ele ocorre, enquanto a amostragem registra apenas eventos selecionados. A seleção pode ser baseada em tempo – uma vez a cada N segundos, ou em frequência – uma vez a cada N solicitações. Se os eventos ocorrerem com muita frequência, a criação de perfis por instrumentação pode causar uma carga muito grande e ela mesma acabar tento um impacto sobre o desempenho geral. Nesse caso, a abordagem de amostragem pode ser preferível. No entanto, se a frequência de eventos for baixa, a amostragem pode perdê-los e, nesse caso, a instrumentação pode ser uma melhor abordagem.
- Forneça contexto suficiente para permitir que o desenvolvedor ou administrador determine a origem de cada solicitação. Isso pode incluir alguma forma de ID de atividade que identifica uma instância específica de uma solicitação e informações que podem ser usadas para correlacionar essa atividade com o trabalho computacional executado e os recursos usados. Observe que esse trabalho pode atravessar limites de processos e máquinas. Para a medição, o contexto também deve incluir (direta ou indiretamente por meio de outras informações correlacionadas) uma referência ao cliente que originou a solicitação. Esse contexto fornece informações valiosas sobre o estado do aplicativo no momento em que os dados de monitoramento foram capturados.
- Registre todas as solicitações e os locais ou regiões de onde essas solicitações foram feitas. Essas informações podem ajudar a determinar se há quaisquer pontos problemáticos específicos a um local e fornecer dados que podem ser úteis para determinar se deve ser feita uma nova partição de um aplicativo ou dos dados que ele usa.
- Registre e capture com cuidado os detalhes de exceções. Frequentemente, informações críticas sobre a depuração são perdidas como resultado de uma manipulação de exceções deficiente. Capture todos os detalhes das exceções geradas pelo aplicativo, incluindo quaisquer exceções internas e outras informações de contexto, incluindo a pilha de chamadas se possível.
- Seja consistente quanto aos dados de que os diferentes elementos do seu aplicativo capturam, pois isso pode auxiliar a analisar os eventos e correlacioná-los a solicitações de usuários. Considere o uso de um pacote de registro em log abrangente e configurável para reunir informações em vez de depender de desenvolvedores que implementam partes diferentes do sistema, todos adotando a mesma abordagem. Colete dados dos contadores de desempenho principais, como o volume de E/S executados, a utilização da rede, o número de solicitações, o uso da memória e a utilização da CPU. Alguns serviços de infraestrutura podem fornecer seus próprios contadores de desempenho específicos, como o número de conexões a um banco de dados, a taxa na qual as transações estão sendo executadas e o número de transações com êxito ou falha. Aplicativos também podem definir seus próprios contadores de desempenho.
- Registre todas as chamadas feitas para serviços externos, como sistemas de banco de dados, serviços Web ou outros serviços de nível de sistema fornecidos como parte da infraestrutura (por exemplo, o armazenamento em cache do Azure). Registre informações sobre o tempo necessário para executar cada chamada e o êxito ou falha da chamada. Se possível, capture informações sobre todas as tentativas de repetição e as falhas para quaisquer erros transitórios que ocorrem.

### Garantindo a compatibilidade com sistemas de telemetria
Em muitos casos, as informações produzidas pela instrumentação são geradas como uma série de eventos e passadas para um sistema de telemetria separado para processamento e análise. Um sistema de telemetria geralmente é independente de qualquer tecnologia ou aplicativo específico, mas espera que as informações sigam um formato específico, geralmente definido por um esquema. O esquema especifica de maneira eficaz um contrato que define os campos e os tipos de dados que o sistema de telemetria pode receber. O esquema deve ser generalizado para permitir que dados sejam recebidos de uma variedade de plataformas e dispositivos.

Um esquema comum deve incluir campos que são comuns a todos os eventos de instrumentação, como o nome do evento, a hora do evento, o endereço IP do remetente e os detalhes necessários para correlacionar com outros eventos (como uma ID de usuário, uma ID de dispositivo e uma ID de aplicativo). Lembre-se de que os eventos podem ser gerados por diversos dispositivos. Portanto, o esquema não deve ser dependente do tipo de dispositivo. Além disso, eventos do mesmo aplicativo podem ser gerados por diversos dispositivos. O aplicativo pode dar suporte a roaming ou a alguma outra forma de distribuição entre dispositivos. Idealmente, a maioria desses campos deve mapear para a saída da biblioteca de log usada para capturar os eventos.

O esquema também pode incluir campos de domínio que são relevantes para um determinado cenário comum entre diferentes aplicativos. Poderiam ser informações sobre exceções, eventos de início e término do aplicativo e sucesso e/ou falha de chamadas à API de serviços Web. Todos os aplicativos que usam o mesmo conjunto de campos de domínio devem emitir o mesmo conjunto de eventos, permitindo que um conjunto de relatórios e análises comuns seja compilado.

Por fim, um esquema pode conter campos personalizados para capturar os detalhes de eventos específicos ao aplicativo.

### Práticas recomendadas para a instrumentação de aplicativos
A lista a seguir resume as práticas recomendadas para instrumentar um aplicativo distribuído em execução na nuvem.

- Logs facilitam a leitura e a análise. Use logs estruturados sempre que possível. Seja conciso e descritivo nas mensagens de log.
- Em todos os logs, identifique a origem e forneça informações de contexto e de tempo conforme cada registro de log é gravado.
- Use o mesmo fuso horário e formato para todos os carimbos de data/hora. Isso ajudará a correlacionar eventos para operações que se estendem para hardware e serviços em execução em diferentes regiões geográficas.
- Categorize os logs e grave mensagens no arquivo de log apropriado.
- Não divulgue informações confidenciais sobre o sistema ou informações pessoais dos usuários. Limpe essas informações antes do registro, mas certifique-se de que os detalhes relevantes sejam mantidos. Por exemplo, remover a ID e senha de qualquer cadeia de caracteres de conexão de banco de dados, mas grave as informações restantes no log para que um analista possa determinar que o sistema está acessando o banco de dados correto. Registre todas as exceções críticas, mas permita que o administrador ative e desative o registro em log para níveis mais detalhados de exceções e avisos. Além disso, capture e registre todas as informações de lógica de repetição. Esses dados podem ser úteis para monitorar a integridade temporária do sistema.
- Rastreie chamadas fora do processo, como solicitações para serviços Web ou banco de dados externos.
- Não misture mensagens de log com requisitos de segurança diferentes no mesmo arquivo de log. Por exemplo, não grave informações de depuração e auditoria no mesmo log.
- Com exceção dos eventos de auditoria, todas as chamadas de log devem ser operações do tipo "disparar e esquecer", que não devem atrapalhar o progresso das operações de negócios. Eventos de auditoria são excepcionais porque são essenciais para os negócios e podem ser classificados como uma parte fundamental das operações de negócios.
- O registro em log deve ser extensível e não deve ter dependências diretas com relação a nenhum destino concreto. Por exemplo, em vez de gravar informações usando _System.Diagnostics.Trace_, defina uma interface abstrata (como _ILogger_) que exponha métodos de registro e que possa ser implementada usando qualquer meio apropriado.
- Todos os logs devem ser à prova de falhas e nunca devem acionar erros em cascata. O registro em log não deve lançar exceções.
- Trate a instrumentação como um processo iterativo contínuo e analise os logs regularmente, não apenas quando houver um problema.

## Coletando e armazenando dados
A fase de coleta do processo de monitoramento é voltada a recuperar informações geradas pela instrumentação, formatar esses dados para facilitar seu consumo pela fase de análise/computação e salvar os dados transformados em um armazenamento confiável e acessível. Os dados de instrumentação que você coletar de diferentes partes de um sistema distribuído poderão ser mantidos em diversos locais e com vários formatos. Por exemplo, o código do aplicativo pode gerar arquivos de log de rastreamento e dados de log de eventos do aplicativo, enquanto os contadores de desempenho que monitoram os principais aspectos da infraestrutura que seu aplicativo usa podem ser capturados usando outras tecnologias. Quaisquer componentes e serviços de terceiros que o aplicativo usar poderão fornecer informações de instrumentação em formatos diferentes, usando arquivos de rastreamento separados, armazenamento de blob ou até mesmo um armazenamento de dados personalizado.

A coleta de dados geralmente é executada implementando um serviço de coleta que pode ser executado de forma autônoma por meio do aplicativo que gera os dados de instrumentação. A Figura 2 ilustra um exemplo dessa arquitetura, destacando o subsistema de coleta de dados de instrumentação.

![](media/best-practices-monitoring/TelemetryService.png)

_Figura 2. Coletando dados de instrumentação_

Observe que essa é uma exibição simplificada. O serviço de coleta não é necessariamente um único processo, e;e pode ser composto por várias partes constituintes em execução em máquinas diferentes, conforme descrito nas seções a seguir. Além disso, se a análise de alguns dados de telemetria precisar ser executada rapidamente (análise hot, conforme descrito na seção [Suporte a Análise hot, warm e cold](#supporting-hot-warm-and-cold-analysis) mais adiante neste documento), componentes locais em operação fora do serviço de coleta podem executar as tarefas de análise imediatamente. A Figura 2 ilustra essa situação para eventos selecionados. Após o processamento analítico, os resultados podem ser enviados diretamente para o subsistema de visualização e alerta. Dados sujeitados a análise warm ou cold são mantidos no armazenamento enquanto esperam o processamento.

Para serviços e aplicativos do Azure, o WAD (Diagnóstico do Azure) fornece uma possível solução para a captura de dados. O WAD reúne dados das seguintes fontes para cada nó de computação, agrega-os e os carrega no armazenamento do Azure:

- Logs do Azure
- Logs do IIS
- Logs de solicitação de falha do IIS
- Logs de Eventos do Windows
- Contadores de desempenho
- Despejos de memória
- Logs de infraestrutura do Diagnóstico do Azure  
- Logs de erros personalizados

Para saber mais, consulte o artigo [Azure: Noções básicas de telemetria e solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) no site da Microsoft.

### Estratégias para coletar dados de instrumentação
Devido à natureza elástica da nuvem e para evitar a necessidade de recuperar dados de telemetria manualmente de cada nó no sistema, você deve fazer com que os dados sejam transferidos para um local central e consolidados. Em um sistema que se estende por vários datacenters, pode ser útil primeiro coletar, consolidar e armazenar dados região por região e depois agregar os dados regionais em um único sistema central.

Para otimizar o uso da largura de banda, você pode optar por transferir dados menos urgentes em partes, como lotes. No entanto, os dados não devem ser adiados por tempo indeterminado, especialmente se contiverem informações em que o tempo faz diferença.

#### _Receber e enviar dados de instrumentação_
O subsistema de coleta de dados de instrumentação pode recuperar ativamente dados de instrumentação dos logs e outras fontes para cada instância do aplicativo (o _modelo de pull_), ou pode atuar como um receptor passivo aguardando que os dados sejam enviados dos componentes que constituem cada instância do aplicativo (o _modelo de push_).

Uma abordagem para implementar o modelo de pull é usar agentes de monitoramento em execução localmente com cada instância do aplicativo. Um agente de monitoramento é um processo separado que recupera periodicamente (recebe por pull) dados de telemetria coletados no nó local e grava essas informações diretamente no armazenamento centralizado que é compartilhado por todas as instâncias do aplicativo. Esse é o mecanismo implementado pelo WAD. Cada instância de uma função de trabalho ou web do Azure pode ser configurada para capturar informações de diagnóstico e outras informações de rastreamento que são armazenadas localmente. O agente de monitoramento que é executado em conjunto copia os dados especificados para o armazenamento do Azure. A página [Configurando o diagnóstico para os Serviços de Nuvem do Azure e máquinas virtuais](https://msdn.microsoft.com/library/azure/dn186185.aspx) no site da Microsoft fornece mais detalhes sobre esse processo. Alguns elementos, como logs de IIS, despejos de memória e logs de erros personalizados são gravados no armazenamento de blob, enquanto dados do log de Eventos do Windows, eventos do ETW e contadores de desempenho são registrados no armazenamento de tabela. A Figura 3 ilustra esse mecanismo:

![](media/best-practices-monitoring/PullModel.png)

_Figura 3. Usando um agente de monitoramento para obter informações e gravar em um armazenamento compartilhado_

> [AZURE.NOTE]Usar um agente de monitoramento é ideal para capturar dados de instrumentação obtidos naturalmente de uma fonte de dados, como informações de Exibições de Gerenciamento do SQL Server ou o comprimento de uma Fila do barramento de serviço do Azure.

Para obter informações sobre como configurar e usar o Diagnóstico do Azure, visite a página [Coletando dados de log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) no site da Microsoft.

Dados de telemetria de um aplicativo de pequena escala em execução em um número limitado podem ser armazenados em um único local usando a abordagem descrita. No entanto, um aplicativo em nuvem global complexo, altamente dimensionável, pode facilmente gerar volumes enormes de dados de centenas de funções da Web e de trabalho, de fragmentos de bancos de dados e outros serviços. Esse fluxo de dados poderia facilmente sobrecarregar a largura de banda de E/S disponível com um único local central. Portanto, sua solução de telemetria deve ser dimensionável para impedir que ele atue como um ponto de estrangulamento conforme o sistema expandir, e idealmente deve incorporar um grau de redundância para reduzir os riscos de perder informações de monitoramento importantes (como dados de cobrança ou de auditoria) se parte do sistema falhar.

Para resolver esses problemas, você pode implementar o enfileiramento. A Figura 4 mostra essa estrutura. Nessa arquitetura, o agente de monitoramento local (se puder ser configurado adequadamente) ou o serviço de coleta de dados personalizado (se não puder) posta os dados em uma fila e um processo separado, em execução assíncrona (o Serviço de gravação em armazenamento na Figura 4), pega os dados nessa fila e os grava no armazenamento compartilhado. Uma fila de mensagens é adequada para este cenário, pois fornece pelo menos uma semântica que garante que, uma vez postados, os dados na fila não serão perdidos. O Serviço de gravação em armazenamento pode ser implementado usando uma função de trabalho separada.

![](media/best-practices-monitoring/BufferedQueue.png)

_Figura 4. Usando uma fila para armazenar dados de instrumentação em buffer_

O serviço de coleta de dados local pode adicionar dados a uma fila imediatamente após seu recebimento. A fila atua como um buffer e o serviço de gravação em armazenamento pode recuperar e gravar os dados em seu próprio ritmo. Por padrão, uma fila opera em uma base "primeiro a entrar, primeiro a sair", mas você pode priorizar mensagens para passá-las adiante na fila se elas contiverem dados que precisam ser tratados mais rapidamente. Para saber mais, consulte o padrão [Fila de prioridade](https://msdn.microsoft.com/library/dn589794.aspx). Como alternativa, você pode usar diferentes canais (como Tópicos do barramento de serviço) para direcionar dados para destinos diferentes dependendo do formato do processamento analítico necessário.

Para ter dimensionalidade, você pode executar várias instâncias do serviço de gravação em armazenamento. Se houver um grande volume de eventos, você pode usar um hub de eventos para enviar os dados para diferentes recursos de computação para processamento e armazenamento.

<a name="consolidating-instrumentation-data"></a>
#### _Consolidando dados de instrumentação_
Os dados de instrumentação recuperados pelo serviço de coleta de dados de uma única instância de um aplicativo fornece uma exibição localizada da integridade e do desempenho dessa instância. Para avaliar a integridade geral do sistema, é necessário consolidar alguns aspectos dos dados dos modos de exibição locais. Isso pode ser executado após os dados serem armazenados, mas em alguns casos também é possível fazê-lo conforme os dados são coletados. Em vez de serem gravados diretamente no armazenamento compartilhado, os dados de instrumentação podem passar por um serviço de consolidação de dados separado que combine os dados e atue como um processo de filtro e limpeza. Por exemplo, dados de instrumentação que incluem as mesmas informações de correlação, como uma ID de atividade, podem ser mesclados (é possível que um usuário comece a executar uma operação de negócios em um nó e depois seja transferido para outro nó em caso de falha de nó ou dependendo de como o balanceamento de carga estiver configurado). Esse processo também pode detectar e remover quaisquer dados duplicados (sempre é uma possibilidade de se o serviço de telemetria usar filas de mensagens para enviar dados de instrumentação para o armazenamento). A Figura 5 ilustra um exemplo dessa estrutura.

![](media/best-practices-monitoring/Consolidation.png)

_Figura 5. Usando um serviço separado para consolidar e limpar dados de instrumentação_

### Armazenando dados de instrumentação
As discussões anteriores deram uma visão bastante simplista da forma como dados de instrumentação são armazenados. Na verdade, pode fazer sentido armazenar diferentes tipos de informações usando tecnologias mais apropriadas para a maneira como cada tipo de informação provavelmente será usada. Por exemplo, o armazenamento em tabelas e em blobs do Azure têm algumas semelhanças na forma como são acessados, mas têm limitações com relação às operações que podem ser executadas utilizando-os e a granularidade dos dados que eles contêm é bastante diferente. Se você precisar executar operações mais analíticas ou precisar de recursos de pesquisa de texto completo dos dados, pode ser mais adequado usar o armazenamento de dados que fornece recursos otimizados para tipos específicos de consultas e acesso de dados. Por exemplo, dados do contador de desempenho podem ser armazenados em um banco de dados SQL para possibilitar a análise ad hoc, logs de rastreamento podem ser melhor armazenados no Banco de Dados de Documentos do Azure, informações de segurança podem ser gravadas no HDFS, informações que exigem pesquisa de texto completo podem ser armazenadas usando a Pesquisa Elástica (o que também pode agilizar pesquisas usando a indexação avançada.) Você pode implementar um serviço adicional que recupere periodicamente dados do armazenamento compartilhado, particione e filtre os dados de acordo com sua finalidade e os grave em um conjunto apropriado de armazenamentos de dados conforme mostrado na Figura 6. Uma abordagem alternativa é incluir essa funcionalidade no processo de consolidação e a limpeza e gravar os dados diretamente nesses armazenamentos conforme eles são recuperados, em vez de salvá-los em uma área de armazenamento compartilhado intermediária. Cada abordagem tem suas vantagens e desvantagens. Implementar um serviço separado de particionamento diminui a carga sobre o serviço de limpeza e a consolidação e permite que pelo menos alguns dos dados particionados sejam regenerados se necessário (dependendo da quantidade de dados mantidos no armazenamento compartilhado). No entanto, ele consome recursos adicionais e pode haver um atraso entre os o recebimento dos dados de instrumentação de cada instância de aplicativo e a conversão desses dados em informações acionáveis.

![](media/best-practices-monitoring/DataStorage.png)

_Figura 6. Particionamento de dados de acordo com requisitos de análise e armazenamento_

Os mesmos dados de instrumentação podem ser necessários para mais de um propósito. Por exemplo, contadores de desempenho podem ser usados para fornecer uma visão do histórico de desempenho do sistema ao longo do tempo, mas essas informações também podem ser combinadas com outros dados de uso para gerar informações de cobrança do cliente. Nessas situações, os mesmos dados podem ser enviados para mais de um destino, como um banco de dados de documento que pode atuar como um armazenamento de longo prazo para manter informações de cobrança e um armazenamento multidimensional para lidar com análises de desempenho complexas.

Você também deve a urgência com que os dados são necessários. Dados que fornecem informações para a geração de alertas precisam ser acessados rapidamente e, por isso, devem ser mantidos em um armazenamento de dados rápido e indexados ou estruturados de modo a otimizar as consultas que sistema de alertas executa. Em alguns casos, pode ser necessário que o serviço de telemetria que reúne os dados em cada nó formate e salve os dados localmente para que uma instância local do sistema de alerta possa notificar rapidamente quaisquer problemas. Os mesmos dados podem ser enviados para o serviço de gravação em armazenamento mostrado nos diagramas anteriores e armazenados centralmente se também forem necessários para outras finalidades.

Informações que são usadas para análises mais consideradas, para gerar relatórios e para identificar tendências históricas são menos urgentes e podem ser armazenadas de uma forma que dê suporte à mineração de dados e a consultas ad hoc. Para obter mais informações, consulte a seção [Suporte a análises hot, warm e cold](#supporting-hot-warm-and-cold-analysis) mais adiante neste documento.

#### _Rotação de logs e retenção de dados_
A instrumentação pode gerar volumes consideráveis de dados. Esses dados podem ser mantidos em diversos locais, começando pelos arquivos de log brutos, arquivos de rastreamento e outras informações capturadas em cada nó para a exibição consolidada, limpa e particionada desses dados mantidos em um armazenamento compartilhado. Em alguns casos, quando os dados são processados e transferidos, os dados brutos de origem podem ser removidos de cada nó. Em outros casos, pode ser necessário ou simplesmente útil salvar as informações brutas. Por exemplo, pode ser melhor deixar dados gerados para fins de depuração disponíveis em sua forma bruta, mas eles poderão ser descartados bem rapidamente após os bugs serem corrigidos. Dados de desempenho geralmente têm uma vida útil mais longa para possibilitar que eles sejam usados para identificar tendências de desempenho e para planejamento de capacidade. A exibição consolidada dos dados geralmente é mantida online por um período limitado para habilitar o acesso rápido, após o qual ela pode ser arquivada ou descartada. Dados coletados para medição e de cobrança clientes talvez precisem ser salvos indefinidamente. Além disso, requisitos regulatórios podem exigir que as informações coletadas para fins de auditoria e segurança também sejam arquivados e salvos. Esses dados também são confidenciais e talvez precisem ser criptografados ou protegidos para evitar violações. Você nunca deve registrar informações como senhas de usuários ou outras informações que possam ser usadas para cometer fraudes de identidade. Esses detalhes devem ser limpos dos dados antes que eles sejam armazenados.

#### _Redução da resolução_
Com frequência, é útil armazenar dados históricos para poder identificar tendências de longo prazo. Em vez de salvar dados antigos em sua totalidade, talvez seja possível reduzir a amostra de dados para reduzir sua resolução e economizar custos com armazenamento. Como exemplo, em vez de salvar indicadores de desempenho minuto a minuto, dados de mais de um mês podem ser consolidados para formar um modo de exibição de hora em hora.

### Práticas recomendadas para coletar e armazenar informações de log
A lista a seguir resume as práticas recomendadas para capturar e armazenar informações de log.

- O agente de monitoramento ou o serviço de coleta de dados deve ser executado como um serviço fora do processo e deve ser simples de implantar.
- Toda a saída do agente de monitoramento ou do serviço de coleta de dados deve ter um formato independente da máquina, do sistema operacional ou do protocolo de rede. Por exemplo, emita informações em um formato autodescritivo como JSON, MessagePack ou Protobuf em vez de ETL/ETW. Usar um formato padrão permite que o sistema construa pipelines de processamento; componentes que leem, transformam e geram dados no formato combinado podem ser integrados facilmente.
- O processo de coleta de dados e monitoramento deve ser à prova de falhas e não deve acionar quaisquer condições de erro em cascata.
- Em caso de falha temporária enquanto envia informações para um coletor de dados, o serviço de coleta de dados ou agente de monitoramento deve estar preparado para reorganizar os dados de telemetria para que as informações mais recentes sejam enviadas primeiro (o serviço de coleta de dados/agente de monitoramento pode optar por descartar os dados mais antigos ou por salvá-los localmente e transmiti-los posteriormente para acompanhamento, a seu próprio critério).

## Analisando dados e diagnosticando problemas
Uma parte importante do processo de monitoramento e diagnóstico é analisar os dados coletados para obter uma visão do bem-estar geral do sistema. Você deve definir seus próprios KPIs e métricas de desempenho, e é importante entender como você pode estruturar os dados que foram coletados para atender às suas necessidades de análise. Também é importante entender como os dados capturados em diferentes métricas e arquivos de log estão correlacionados, pois essas informações podem ser fundamentais para rastrear uma sequência de eventos e ajudar a diagnosticar problemas que surgirem.

Conforme descrito na seção [Consolidando dados de instrumentação](#consolidating-instrumentation-data), os dados de cada parte do sistema geralmente são capturados localmente, mas normalmente precisam ser combinados com dados gerados em outros locais que participam do sistema. Essas informações requerem uma correlação cuidadosa para garantir que os dados sejam combinados com precisão. Por exemplo, os dados de uso de uma operação podem incluir um nó que hospeda um site ao qual um usuário se conecta, um nó que executa um serviço separado acessado como parte dessa operação e o armazenamento de dados mantido em um nó adicional. Essas informações precisam ser vinculadas para fornecer uma visão geral do uso de recursos e processamento da operação. O pré-processamento e filtragem de dados pode ocorrer no nó em que os dados são capturados, enquanto a agregação e a formatação têm maior probabilidade de ocorrer em um nó central.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Suporte à análise hot, warm e cold
Analisar e reformatar dados para visualização, geração de relatórios e alertas pode ser um processo complexo que consome seu próprio conjunto de recursos. Algumas formas de monitoramento precisam ser pontuais e requerem análise imediata dos dados para serem eficazes. Elas são conhecidas como _análises hot_. Exemplos incluem as análises necessárias para gerar alertas e alguns aspectos do monitoramento de segurança (como detectar um ataque ao sistema). Os dados necessários para esses fins devem ser disponibilizados e estruturados rapidamente para que o processamento seja eficiente. Em alguns casos pode ser necessário mover o processamento da análise para os nós individuais nos quais os dados são mantidos.

Outras formas de análise são menos críticas e podem exigir um pouco de computação e agregação após os dados brutos serem recebidos. Elas são conhecidas como _análises warm_. A análise de desempenho normalmente se encaixa nesta categoria. Nesse caso, é improvável que um evento de desempenho único e isolado seja estatisticamente significativo (ele pode ser causado por um aumento repentino ou uma falha), enquanto os dados de uma série de eventos devem fornecer uma imagem mais confiável do desempenho do sistema. A análise warm também pode ser usada para ajudar a diagnosticar problemas de integridade. Um evento de integridade normalmente é processado pela realização de análises hot e pode gerar um alerta imediatamente. O operador deve ser capaz de detectar os motivos para o evento de integridade examinando os dados do caminho warm. Esses dados devem conter informações sobre os eventos que levaram ao problema que causou o evento de integridade.

Alguns tipos de monitoramento de geram dados que são mais de longo prazo e a análise pode ser executada posteriormente, possivelmente de acordo com um cronograma predefinido. Em alguns casos, talvez a análise precise executar a filtragem complexa de grandes volumes de dados capturados em um período de tempo. Elas são conhecidas como _análises cold_. O principal requisito é que os dados sejam armazenados em segurança após serem capturados. Por exemplo, a auditoria e o monitoramento de uso exigem uma imagem precisa do estado do sistema em momentos regulares, mas essas informações de estado não precisam estar disponíveis para processamento imediatamente após serem coletadas. A análise cold também pode ser usada para fornecer dados para análise de previsão de integridade. As informações de histórico obtidas durante um período de tempo especificado podem ser usadas em conjunto com os dados atuais de integridade (recuperados do afunilamento) para identificar tendências que em breve poderão causar problemas de integridade. Nesses casos, pode ser necessário emitir um alerta para possibilitar que a ação corretiva seja realizada.

### Correlacionando dados
Os dados capturados pela instrumentação podem fornecer um instantâneo do estado do sistema, mas o objetivo da análise é tornar esses dados acionáveis. Por exemplo, o que causou uma intensa carga de E/S no nível do sistema em um momento específico? Trata-se do resultado de um grande número de operações de banco de dados? Isso é refletido nos tempos de resposta do banco de dados, no número de transações por segundo e nos tempos de resposta do aplicativo ao mesmo tempo? Se sim, uma ação corretiva que pode reduzir a carga pode ser fragmentar os dados em mais servidores. Além disso, exceções podem surgir como resultado de uma falha em qualquer nível do sistema, e uma exceção em um nível normalmente causa outra falha no nível acima. Por esses motivos, você precisa ser capaz de correlacionar os diferentes tipos de dados de monitoramento em cada nível para produzir uma visão geral do estado do sistema e dos aplicativos que são executados nele. Você pode usar essas informações para tomar decisões sobre se o sistema está funcionando de forma aceitável ou não e determinar o que pode ser feito para melhorar a qualidade do sistema.

Conforme descrito na seção [Informações para correlação de dados](#information-for-correlating-data), você deve garantir que os dados brutos de instrumentação incluam informações suficientes de contexto e ID de atividades para dar suporte às agregações necessárias para a correlação de eventos. Além disso, esses dados podem ser mantidos em formatos diferentes e pode ser necessário analisar essas informações para convertê-los em um formato padronizado para fins de análise.

### Solução de problemas e diagnósticos
Para fazer um diagnóstico, é necessário ser capaz de determinar a causa das falhas ou comportamentos inesperados, incluindo a realização de análise da causa raiz. As informações necessárias geralmente incluem:

- Informações detalhadas de logs de eventos e rastreamentos para todo o sistema ou para um subsistema especificado durante um período especificado.
- Rastreamentos de pilha completos, resultantes de exceções e falhas de qualquer nível especificado que ocorrerem tanto dentro do sistema ou em um subsistema especificado durante um período especificado.
- Despejos de memória para todos os processos com falha em qualquer parte do sistema ou de um subsistema especificado durante um período especificado.
- Logs de atividades que gravaram as operações executadas por todos os usuários ou por usuários selecionados durante um período especificado.

A análise de dados para fins de solução de problemas normalmente requer um conhecimento técnico profundo da arquitetura do sistema e dos vários componentes que compõem a solução. Como resultado, frequentemente é necessário um alto grau de intervenção manual para interpretar os dados, estabelecer a causa dos problemas e recomendar uma estratégia apropriada para corrigi-los. Pode ser apropriado simplesmente armazenar uma cópia dessas informações em seu formato original e torná-la disponível para análise cold por um especialista.

## Visualizando dados e gerando alertas
Um aspecto importante de qualquer sistema de monitoramento é a capacidade de apresentar os dados de forma que um operador possa identificar rapidamente quaisquer problemas ou tendências e informar rapidamente um operador caso ocorra um evento significativo que possa exigir atenção.

A apresentação de dados pode ter diversos formatos, incluindo a visualização usando painéis, alertas e relatórios.

### Visualização com painéis
A maneira mais comum para visualizar dados é usar painéis que podem exibir informações como uma série de tabelas, gráficos ou de alguma outra maneira ilustrada. Esses itens podem ser parametrizados e um analista deve ser capaz de selecionar os parâmetros importantes (como o período de tempo) para qualquer situação específica. Os painéis podem ser organizados hierarquicamente, com os painéis de nível superior fornecendo uma visão geral de cada aspecto do sistema, mas com a facilidade de possibilitar que o operador tenha acesso aos detalhes. Por exemplo, um painel que mostra a E/S de disco geral para o sistema deve permitir que um analista examine os dados e veja as taxas de E/S para cada disco individual para determinar se um ou mais dispositivos específicos são responsáveis por um volume desproporcional de tráfego. O ideal é que o painel também exiba informações relacionadas, como a origem de cada solicitação (o usuário ou atividade) que está gerando a E/S. Essas informações podem, então, ser usadas para determinar se (e como) é necessário distribuir a carga de forma mais uniforme entre os dispositivos e se o sistema teria um desempenho melhor se mais dispositivos fossem adicionados. Um painel também pode usar codificação por cores ou outras indicações visuais para indicar valores que parecem anormais ou que estão fora de um intervalo esperado. Usando o exemplo anterior, um disco com uma taxa de E/S que está se aproximando de sua capacidade máxima por um longo período (um disco hot) poderia ser destacado em vermelho, um disco com uma taxa de E/S que é executada periodicamente no seu limite máximo por curtos períodos (um disco warm) poderia ser destacado em amarelo e um disco apresentando o uso normal pode ser exibido em verde.

Observe que, para que um sistema de painéis funcione de maneira eficaz, ele deve ter dados brutos com os quais trabalhar. Se estiver criando seu próprio sistema de painéis ou usando um painel desenvolvido por outra empresa, você deve compreender quais dados de instrumentação precisa coletar, em quais níveis de granularidade e como eles deve ser formatados para consumo pelo painel.

Um bom painel não exibe apenas informações, ele fornece um meio de permitir que um analista faça perguntas ad hoc sobre essas informações. Alguns sistemas oferecem ferramentas de gerenciamento que o operador pode usar para executar essas tarefas e explorar os dados subjacentes. Como alternativa, dependendo do repositório usado para manter essas informações, talvez seja possível consultar os dados diretamente ou importá-los para ferramentas como o Microsoft Excel para análises e relatórios posteriores.

> [AZURE.NOTE]Você deve restringir o acesso aos painéis a pessoal autorizado. Essas informações podem ser confidenciais comercialmente. Você também deve proteger os dados subjacentes apresentados pelo painel de controle para impedir que os usuários os alterem.

### Gerando alertas
Gerar alertas é o processo de analisar os dados de instrumentação e monitoramento e gerar uma notificação se for detectado um evento significativo.

A geração de alertas é usada para garantir que o sistema permaneça íntegro, responsivo e seguro. Trata-se de uma parte importante de qualquer sistema que faz garantias de desempenho, disponibilidade e privacidade para os usuários e cujos dados precisem ser acionados imediatamente. Talvez seja necessário notificar o operador do evento que disparou o alerta. Os alertas também podem ser usados para invocar funções do sistema, como o dimensionamento automático.

A geração de alertas normalmente depende dos seguintes dados de instrumentação:

- Eventos de segurança. Se os logs de eventos indicarem que falhas repetidas autenticação e/ou autorização estão ocorrendo, o sistema pode estar sob ataque e um operador deve ser informado.
- Métricas de desempenho. O sistema deve responder rapidamente se uma métrica de desempenho específica ultrapassar um limite especificado.
- Informações de disponibilidade. Se uma falha for detectada, talvez seja necessário reiniciar rapidamente um ou mais subsistemas ou fazer um failover para um recurso de backup. Falhas repetidas em um subsistema podem indicar problemas mais graves.

Operadores podem receber informações de alerta por meio de vários canais de distribuição, como emails, mensagens de texto SMS ou um dispositivo de pager. Um alerta também pode incluir uma indicação de quão crítica é a situação que surgiu. Muitos sistemas de alertas dão suporte a grupos de assinantes e todos os operadores que são membros do mesmo grupo podem receber o mesmo conjunto de alertas.

Um sistema de alertas deve ser personalizável e os valores apropriados dos dados de instrumentação subjacentes podem ser fornecidos como parâmetros. Essa abordagem permite que o operador filtre dados e se concentra nos limites ou combinações de valores que são de seu interesse. Observe que, em alguns casos, dados brutos de instrumentação podem ser fornecidos para o sistema de alertas, enquanto em outras situações pode ser mais apropriado fornecer dados agregados (por exemplo, um alerta pode ser acionado se a utilização da CPU de um nó ultrapassar 90% durante 10 minutos). Os detalhes fornecidos ao sistema de alertas também devem incluir quaisquer informações de contexto e de resumo apropriadas. Esses dados podem ajudar a reduzir a possibilidade de eventos falsos positivos acionarem um alerta.

### Relatórios
Os relatórios são usados para gerar uma visão geral do sistema e podem incorporar dados históricos, bem como informações atuais. Os requisitos para a geração de relatórios se enquadram em duas categorias amplas: relatórios operacionais e relatórios de segurança.

Relatórios operacionais geralmente incluem os seguintes aspectos:

- Agregar estatísticas que permitem que você compreenda a utilização de recursos do sistema geral ou de subsistemas especificados durante um período especificado.
- Identificar tendências na utilização de recursos do sistema geral ou de subsistemas específicos durante um período de tempo determinado.
- Monitorar as exceções que ocorreram em todo o sistema ou em subsistemas especificados durante um determinado período de tempo.
- Determinar a eficiência do aplicativo em termos dos recursos implantados e compreender se o volume de recursos (e seu custo associado) pode ser reduzido sem afetar o desempenho desnecessariamente.

Os relatórios de segurança têm a preocupação de monitorar o uso do sistema por cliente, e podem incluir:

- Fazer auditoria das operações do usuário. Isso requer a gravação de solicitações individuais executadas por cada usuário com datas e horas. Os dados devem ser estruturados para permitir que um administrador reconstrua rapidamente a sequência de operações executadas por um usuário específico em um período de tempo especificado.
- Controlar o uso de recursos pelo usuário. Isso requer a gravação de como cada solicitação de um usuário acessa os vários recursos que compõem o sistema e por quanto tempo. Um administrador deve ser capaz de usar esses dados para gerar um relatório de utilização por usuário por um período de tempo especificado, possivelmente para fins de cobrança.

Em muitos casos, relatórios podem ser gerados por processos em lotes de acordo com um cronograma definido (a latência normalmente não é um problema), mas eles também devem estar disponíveis para a geração de maneira ad hoc se necessário. Por exemplo, se estiver armazenando dados em um banco de dados relacional, como o Banco de Dados SQL Azure, você pode usar uma ferramenta como o SQL Server Reporting Services para extrair e formatar dados e apresentá-lo como um conjunto de relatórios.

## Diretrizes e padrões relacionados
- As Diretrizes de dimensionamento automático descrevem como diminuir a sobrecarga de gerenciamento ao reduzir a necessidade de um operador monitorar o desempenho de um sistema continuamente e tomar decisões sobre como adicionar ou remover recursos.
- O [Padrão de monitoramento de ponto de extremidade de integridade](https://msdn.microsoft.com/library/dn589789.aspx) descreve como implementar verificações funcionais dentro de um aplicativo que ferramentas externas podem acessar por meio de pontos de extremidade expostos em intervalos regulares.
- O padrão de [Fila de prioridade](https://msdn.microsoft.com/library/dn589794.aspx) mostra como priorizar mensagens em fila para que solicitações urgentes sejam recebidas e possam ser processadas antes de mensagens menos urgentes.

## Mais informações
- O artigo [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md) no site da Microsoft.
- O artigo [Azure: Noções básicas de telemetria e solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) no site da Microsoft.
- A página [Coletando dados de log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) no site da Microsoft.
- A página [Configurando o diagnóstico para os Serviços de Nuvem do Azure e máquinas virtuais](https://msdn.microsoft.com/library/azure/dn186185.aspx) no site da Microsoft.
- As páginas do [Cache Redis do Azure](http://azure.microsoft.com/services/cache/), do [Banco de Dados de Documentos do Azure](http://azure.microsoft.com/services/documentdb/) e do [HDInsight](http://azure.microsoft.com/services/hdinsight/) no site da Microsoft.
- A página [Como usar Filas do barramento de serviço](http://azure.microsoft.com/) no site da Microsoft.
- O artigo [Microsoft SQL Server Business Intelligence em Máquinas Virtuais do Azure](./virtual-machines/virtual-machines-sql-server-business-intelligence.md) no site da Microsoft.
- A página [Noções básicas sobre alertas e notificações de monitoramento no Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx) no site da Microsoft.
- A página do [Application Insights](app-insights-get-started/) no site da Microsoft.

<!---HONumber=Oct15_HO3-->