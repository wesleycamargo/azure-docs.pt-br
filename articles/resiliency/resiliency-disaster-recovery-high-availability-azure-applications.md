---
title: Recuperação de desastre e alta disponibilidade para aplicativos do Azure | Microsoft Docs
description: Visões gerais técnicas e informações detalhadas sobre como projetar e criar aplicativos para alta disponibilidade e recuperação de desastres no Microsoft Azure.
services: ''
documentationcenter: na
author: adamglick
manager: saladki
editor: ''

ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick

---
# Recuperação de desastres e alta disponibilidade para aplicativos baseados no Microsoft Azure
## Introdução
Este artigo se concentra na alta disponibilidade para aplicativos em execução no Azure. Uma estratégia geral para alta disponibilidade também inclui o aspecto de recuperação de desastre. Planejamento para falhas e desastres na nuvem exige que você reconheça as falhas rapidamente. Em seguida, implemente uma estratégia que corresponda a tolerância de tempo de inatividade do aplicativo. Além disso, você deve considerar a extensão da perda de dados que o aplicativo pode tolerar sem causar consequências adversas para os negócios quando forem restaurados.

A maioria das empresas afirma que está preparada para falhas temporárias e em larga escala. No entanto, antes de responder essa pergunta por você mesmo, sua empresa ensaia essas falhas? Você testa a recuperação de bancos de dados para garantir que os processos corretos estejam em vigor? Provavelmente, não. Isso ocorre porque a recuperação de desastre começa com muito planejamento e arquitetura para implementar esses processos. Assim como muitos outros requisitos não funcionais, como segurança, a recuperação de desastres raramente obtém análise antecipada e a alocação de tempo necessária. Além disso, a maioria das empresas não tem o orçamento para regiões geograficamente distribuídas com capacidade redundante. Consequentemente, mesmo os aplicativos essenciais são excluídos com frequência do planejamento apropriado de recuperação de desastre.

Plataformas de nuvem, como o Azure, fornecem regiões geograficamente dispersas em todo o mundo. Essas plataformas também fornecem recursos que são compatíveis com a disponibilidade e uma variedade de cenários de recuperação de desastre. Agora, cada aplicativo de nuvem crítico pode receber devida atenção para a verificação de desastres do sistema. O Azure tem resiliência e recuperação de desastre internas para muitos de seus serviços. Você deve estudar os recursos dessa plataforma cuidadosamente e complementar com estratégias de aplicativo.

Este artigo descreve as etapas de arquitetura necessárias que você deve seguir para verificação de desastres em uma implantação do Azure. Em seguida, você pode implementar o processo maior de continuidade de negócios. Um plano de continuidade de negócios é um roteiro para continuar as operações sob condições adversas. Isso pode ser uma falha com a tecnologia, como um serviço inativo ou um desastre natural, como uma interrupção de energia ou um temporal. Resiliência de aplicativo para desastres é apenas um subconjunto do processo maior de recuperação de desastre, conforme descrito neste documento do NIST: [Contingency Planning Guide for Information Technology Systems](https://www.fismacenter.com/sp800-34.pdf) (Guia de planejamento de contingência para sistemas de tecnologia da informação).

As seções a seguir definem níveis diferentes de falhas, técnicas para lidar com elas e arquiteturas que dão suporte a essas técnicas. Essas informações fornecem a entrada para seus processos e procedimentos de recuperação de desastre, a fim de garantir que a estratégia de recuperação de desastre funcione de maneira correta e eficiente.

## Características de aplicativos de nuvem resilientes
Um aplicativo bem projetado pode suportar falhas de capacidade em um nível tático e também pode tolerar falhas estratégicas de todo o sistema no nível de região. As seções a seguir definem a terminologia referenciada em todo o documento para descrever os vários aspectos dos serviços de nuvem resilientes.

### Alta disponibilidade
Um aplicativo em nuvem altamente disponível implementa estratégias para absorver a interrupção das dependências, como os serviços gerenciados oferecidos pela plataforma de nuvem. Apesar de possíveis falhas de capacidades da plataforma de nuvem, essa abordagem permite que o aplicativo continue a exibir características sistêmicas funcionais e não funcionais esperadas. Isso é abordado detalhadamente na série de vídeos do Channel 9 [Failsafe: Guidance for Resilient Cloud Architectures](https://channel9.msdn.com/Series/FailSafe) (À prova de falhas: orientação para arquiteturas de nuvem resilientes).

Quando você implementa o aplicativo, deve considerar a probabilidade de uma interrupção de capacidade. Além disso, considere o impacto que uma interrupção terá sobre o aplicativo da perspectiva dos negócios antes de se aprofundar nas estratégias de implementação. Sem a devida atenção ao impacto nos negócios e a probabilidade de atingir a condição de risco, a implementação pode ser caro e potencialmente desnecessária.

Considere uma analogia automotiva para alta disponibilidade. Até mesmo peças de qualidade e engenharia avançada não impedem falhas ocasionais. Por exemplo, quando seu carro tem um pneu furado, o carro ainda funciona, mas está operando com funcionalidade degradada. Se você estiver preparado para essa ocorrência potencial, poderá usar um dos pneus sobressalentes de aro fino até chegar a uma oficina. Embora o pneu sobressalente não permita rodar em alta velocidade, você ainda pode operar o veículo até você substituir o pneu. Da mesma forma, um serviço de nuvem preparado para potencial perda de capacidade pode impedir que um problema relativamente pequeno interrompa o aplicativo inteiro. Isso será verdadeiro mesmo se o serviço de nuvem for executado com funcionalidade degradada.

Há algumas características principais de serviços de nuvem altamente disponíveis: disponibilidade, escalabilidade e tolerância a falhas. Embora essas características estejam correlacionadas, é importante entender cada uma e como elas contribuem para a disponibilidade geral da solução.

### Disponibilidade
Um aplicativo disponível considera a disponibilidade da infraestrutura subjacente e serviços dependentes. Os aplicativos disponíveis removem pontos únicos de falha por meio de redundância e design resiliente. Quando você amplia o escopo para considerar a disponibilidade no Azure, é importante entender o conceito da disponibilidade efetiva da plataforma. A disponibilidade efetiva considera os SLA (Contratos de Nível de Serviço) de cada serviço dependente e seu efeito cumulativo na disponibilidade total do sistema.

Disponibilidade do sistema é a medida do percentual de uma janela de tempo em que o sistema será capaz de operar. Por exemplo, o SLA de disponibilidade de pelo menos duas instâncias de uma função de trabalho ou web no Azure é de 99,95% (em 100%). Ela não mede o desempenho nem a funcionalidade dos serviços executados nessas funções. No entanto, a disponibilidade efetiva do serviço de nuvem também é afetada pelos diversos SLAs dos outros serviços dependentes. Quanto mais partes móveis no sistema, mais cuidado você deve tomar para garantir que o aplicativo atenda, com resiliência, os requisitos de disponibilidade dos usuários finais.

Considere os seguintes SLAs para um serviço do Azure que usa os serviços do Azure: computação, banco de dados SQL e armazenamento do Azure.

| Serviço do Azure | Contrato de Nível de Serviço | Possíveis minutos de tempo de inatividade/mês (30 dias) |
|:--- |:--- |:---:|
| Computação |99,95% |21,6 minutos |
| Banco de Dados SQL |99,99% |4,3 minutos |
| Armazenamento |99,90% |43,2 minutos |

Você deve planejar para inatividade de todos os serviços em momentos diferentes. Neste exemplo simplificado, o número total de minutos por mês em que o aplicativo pode estar inativo é 108 minutos. Um mês de 30 dias tem um total de 43.200 minutos. 108 minutos é 0,25% do número total de minutos em um mês de 30 dias (43.200 minutos). Isso fornece uma disponibilidade efetiva de 99,75% para o serviço de nuvem.

No entanto, o uso de técnicas de disponibilidade descritas neste documento pode aumentar esse valor. Por exemplo, se você projetar seu aplicativo para continuar a execução quando o banco de dados SQL não estiver disponível, poderá remover isso da equação. Isso pode significar que o aplicativo é executado com capacidades reduzidas, portanto, também há requisitos de negócios a considerar. Para obter uma lista completa de SLAs do Azure, confira [Contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

### Escalabilidade
A escalabilidade afeta a disponibilidade diretamente. Um aplicativo que falha com uma carga maior não está mais disponível. Aplicativos escalonáveis podem atender à demanda maior com resultados consistentes, em janelas de tempo aceitáveis. Quando um sistema é escalonável, ele é dimensionado horizontal ou verticalmente para gerenciar o aumento de carga, mantendo o desempenho consistente. Em termos básicos, o escalonamento horizontal adiciona mais computadores do mesmo tamanho (processador, memória e largura de banda) e o escalonamento vertical aumenta o tamanho dos computadores existentes. Para o Azure, você tem opções de escala vertical para selecionar vários tamanhos de máquina para computação. No entanto, alterar o tamanho da máquina exige uma reimplantação. Portanto, as soluções mais flexíveis são projetadas para escala horizontal. Isso é especialmente verdadeiro para a computação, pois você pode facilmente aumentar o número de instâncias em execução de qualquer função web ou de trabalho. Essas instâncias adicionais tratam o aumento do tráfego por meio do portal da Web do Azure, scripts do PowerShell ou código. Baseie essa decisão nos aumentos em métricas monitoradas específicas. Nesse cenário, o desempenho do usuário ou as métricas não sofrem uma queda perceptível sob carga. Normalmente, as funções da Web e de trabalho armazenam qualquer estado externamente. Isso permite o balanceamento de carga flexível e manipulação normal de qualquer alteração em contagens da instância. Escala horizontal também funciona bem com serviços, como o armazenamento do Azure, que não fornece opções em camadas para a escala vertical.

Implantações de nuvem devem ser vistas como uma coleção de unidades de escala. Isso permite que o aplicativo seja elástico em atender às necessidades de produtividade dos usuários finais. As unidades de escala são mais fáceis de visualizar no nível do servidor Web e de aplicativos. Isso ocorre porque o Azure já fornece nós de computação sem estado por meio de funções Web e de trabalho. A adição de unidades de escala de computação à implantação não causará nenhum efeito colateral de gerenciamento de estado de aplicativo porque as unidades de escala de computação são sem estado. Uma unidade de escala de armazenamento é responsável por gerenciar uma partição de dados (estruturados ou não). Partição de tabela, contêiner de Blobs e Banco de Dados SQL do Azure são exemplos de unidades de escala de armazenamento. Até mesmo o uso de várias contas de armazenamento do Azure tem um impacto direto na escalabilidade do aplicativo. Você deve criar um serviço de nuvem altamente escalonável para incorporar várias unidades de escala de armazenamento. Por exemplo, se um aplicativo usar dados relacionais, particione os dados entre vários bancos de dados SQL. Isso permite que o armazenamento acompanhe o modelo de unidade de escala de computação elástica. Da mesma forma, o armazenamento do Azure permite esquemas de particionamento de dados que exigem designs deliberados para atender às necessidades de produtividade da camada de computação. Para obter uma lista de práticas recomendadas para a criação de serviços de nuvem escalonáveis, confira [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Práticas recomendadas para design de serviços em larga escala nos Serviços de Nuvem do Azure).

### Tolerância a falhas
Os aplicativos devem considerar que cada capacidade de nuvem dependente poderá e ficará inativa em algum momento. Um aplicativo tolerante a falhas detecta e corrige elementos com falha para prosseguir e retornar os resultados corretos em um período específico. Para condições de erro transitórias, um sistema tolerante a falhas empregará uma política de repetição. Para falhas mais graves, o aplicativo pode detectar problemas e fazer failover para hardware alternativo ou planos de contingência até corrigir a falha. Um aplicativo confiável pode gerenciar adequadamente a falha de uma ou mais partes e continuar operando corretamente. Aplicativos com tolerância a falhas podem usar uma ou mais estratégias de design, como redundância, replicação ou funcionalidade degradada.

## Recuperação de desastre
Uma implantação de nuvem pode parar de funcionar devido a uma interrupção sistêmica dos serviços dependentes ou d infraestrutura subjacente. Nessas circunstâncias, um plano de continuidade de negócios dispara o processo de recuperação de desastre. Esse processo normalmente envolve a equipe de operações e procedimentos automatizados para reativar o aplicativo em uma região disponível. Isso exige a transferência de usuários, dados e serviços do aplicativo para a nova região. Também envolve o uso de mídia de backup ou replicação contínua.

Considere a analogia anterior que comparou a alta disponibilidade com a capacidade de recuperação de um pneu furado usando um sobressalente. Por outro lado, a recuperação de desastre envolve as etapas executadas após um acidente de carro, em que o carro não está mais funcionando. Nesse caso, a melhor solução é encontrar uma forma eficiente de trocar de carro, chamando um serviço de viagem ou um amigo. Nesse cenário, provavelmente haverá um atraso maior em voltar à estrada. Existe também maior complexidade no reparo e retorno ao veículo original. Da mesma forma, a recuperação de desastres em outra região é uma tarefa complexa que geralmente envolve algum tempo de inatividade e perda de dados. Para melhor compreender e avaliar as estratégias de recuperação de desastres, é importante definir dois termos: RTO (Objetivo do Tempo de Recuperação) e RPO (Objetivo do Ponto de Recuperação).

### Objetivo de tempo de recuperação
O RTO é a quantidade máxima de tempo alocada para restaurar a funcionalidade do aplicativo. Isso tem base nas necessidades dos negócios e está relacionado à importância do aplicativo. Aplicativos de negócios críticos exigem um baixo RTO.

### Objetivo de ponto de recuperação
O RPO é a janela de tempo aceitável de perda de dados devido ao processo de recuperação. Por exemplo, se o RPO for de uma hora, você deverá fazer backup completo ou replicar os dados pelo menos a cada hora. Depois de colocar o aplicativo em uma região alternativa, poderá haver até uma hora de dados de backup. Como o RTO, os aplicativos críticos visam um RPO bem menor.

## Lista de verificação
Vamos resumir os principais pontos que foram abordados neste artigo (e seus artigos relacionados sobre aplicativos de [alta disponibilidade](resiliency-high-availability-azure-applications.md) e [recuperação de desastre](resiliency-disaster-recovery-azure-applications.md) do Azure). Este resumo atuará como uma lista de verificação de itens que você deve considerar para sua própria disponibilidade e planejamento de recuperação de desastres. Essas são as práticas recomendadas úteis para clientes que buscam seriedade na implementação de uma solução bem-sucedida.

1. Faça uma avaliação de risco para cada aplicativo, pois cada um pode ter requisitos diferentes. Alguns aplicativos são mais críticos e justificariam o custo extra de planejá-los para recuperação de desastres.
2. Use essas informações para definir o RTO e RPO de cada aplicativo.
3. Projete para falhas, começando com a arquitetura do aplicativo.
4. Implemente as práticas recomendadas para alta disponibilidade, equilibrando o custo, a complexidade e o risco.
5. Implemente processos e planos de recuperação de desastres.
   * Considere falhas que abranjam o módulo até uma interrupção total da nuvem.
   * Estabeleça estratégias de backup para todos os dados de referência e transacionais.
   * Escolha uma arquitetura de recuperação de desastres em vários locais.
6. Defina um proprietário específico para processos de recuperação de desastres, automação e testes. O proprietário deve gerenciar e possuir todo o processo.
7. Documente os processos para que sejam repetidos com facilidade. Embora haja um único proprietário, várias pessoas devem ser capazes de entender e seguir os processos em uma emergência.
8. Treine a equipe para implementar o processo.
9. Use regularmente simulações de desastres para treinamento e validação do processo.

## Resumo
Quando o hardware ou aplicativos falham dentro do Azure, as técnicas e estratégias para gerenciá-los são diferentes de quando ocorre falha em sistemas locais. O principal motivo para isso é que as soluções de nuvem geralmente têm mais dependências na infraestrutura que são distribuídas entre uma região do Azure e gerenciada como serviços separados. Você deve lidar com falhas parciais usando técnicas de alta disponibilidade. Para gerenciar falhas mais graves, possivelmente devido a um evento de desastre, use estratégias de recuperação de desastres.

O Azure detecta e trata muitas falhas, mas há muitos tipos de falhas que exigem estratégias específicas do aplicativo. Ativamente, você deve preparar e gerenciar as falhas de aplicativos, serviços e dados.

Ao criar seu plano de recuperação de desastres e disponibilidade do aplicativo, considere as consequências da falha do aplicativo nos negócios. A definição de processos, políticas e procedimentos para restaurar sistemas críticos após um evento catastrófico consome tempo, planejamento e compromisso. E depois de estabelecer os planos, você não pode parar por aqui. Você deve analisar regularmente, testar e melhorar continuamente os planos com base em seu portfólio de aplicativos, necessidades de negócios e tecnologias disponíveis para você. O Azure fornece novos recursos e lança novos desafios para criar aplicativos avançados resistentes a falhas.

## Recursos adicionais
[Alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Recuperação de desastre para aplicativos criados no Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Orientações técnicas de resiliência do Azure](resiliency-technical-guidance.md)

[Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md)

[Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Failsafe: Diretrizes para arquiteturas de nuvem resilientes](https://channel9.msdn.com/Series/FailSafe)

[Práticas recomendadas para serviços em grande escala nos Serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

## Próximas etapas
Este artigo faz parte de uma série de artigos com foco na recuperação de desastres e alta disponibilidade para aplicativos do Azure. O próximo artigo desta série é [Alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0824_2016-->