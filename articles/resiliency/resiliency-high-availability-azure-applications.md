---
title: Alta disponibilidade para aplicativos do Azure | Microsoft Docs
description: Visão geral técnica e informações detalhadas sobre como projetar e criar aplicativos para alta disponibilidade no Microsoft Azure.
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
# <a name="high-availability-for-applications-built-on-microsoft-azure"></a>Alta disponibilidade para aplicativos baseados no Microsoft Azure
Um aplicativo altamente disponível absorve flutuações em disponibilidade, carga e falhas temporárias em serviços dependentes e hardware. O aplicativo continua funcionando em um nível de resposta sistêmico e de usuário aceitável, conforme definido por requisitos de negócios ou SLAs (contratos de nível de serviço) de aplicativo.

## <a name="azure-high-availability-features"></a>Recursos de alta disponibilidade do Azure
O Azure tem muitos recursos internos da plataforma que oferecem suporte a aplicativos altamente disponíveis. Esta seção descreve alguns desses recursos principais. Para obter uma análise mais abrangente da plataforma, confira [Orientações técnicas de resiliência do Azure](resiliency-technical-guidance.md).

### <a name="fabric-controller"></a>Controlador de malha
O controlador de malha do Azure provisiona e monitora a condição das instâncias de computação do Azure. O controlador de malha verifica o status do hardware e software de instâncias do computador host e convidado. Quando ele detecta uma falha, força os SLAs automaticamente, realocando as instâncias da VM. O conceito de domínios de falha e atualização dá ainda mais suporte ao SLA de computação.

Quando várias instâncias de função do Serviço de Nuvem são implantadas, o Azure implanta essas instâncias em diferentes domínios de falha. Um limite de domínio de falha é basicamente um rack de hardware diferentes na mesma região. Domínios de falha reduzem a probabilidade de que uma falha de hardware localizada interrompa o serviço de um aplicativo. Você não pode gerenciar o número de domínios de falha que são alocados às funções de trabalho ou da Web. O controlador de malha usa os recursos dedicados que são separados dos aplicativos hospedados pelo Azure. Ele tem 100% de tempo de atividade porque atua como núcleo do sistema do Azure. Ele monitora e gerencia instâncias da função em domínios de falha.

O diagrama a seguir mostra recursos compartilhados do Azure que o controlador de malha implanta e gerencia nos diferentes domínios de falha.

![Exibição simplificada de isolamento de domínio de falha](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Domínios de atualização são semelhantes a domínios de falha na função, mas oferecem suporte a atualizações em vez de falhas. Um domínio de atualização é uma unidade lógica de separação da instância que determina quais instâncias em um serviço específico serão atualizadas em um ponto no tempo. Por padrão, para a implantação do serviço hospedado, são definidos cinco domínios de atualização. No entanto, você pode alterar esse valor no arquivo de definição de serviço. Por exemplo, suponha que você tenha oito instâncias da sua função web. Haverá duas instâncias em três domínios de atualização e duas instâncias em um domínio de atualização. O Azure define a sequência de atualização, mas se baseia no número de domínio de atualização. Para saber mais sobre domínios de atualização, confira [Como atualizar um serviço de nuvem](../cloud-services/cloud-services-update-azure-service.md).

### <a name="features-in-other-services"></a>Recursos em outros serviços
Além dos recursos de plataforma que são compatíveis com a computação de alta disponibilidade, o Azure insere recursos de alta disponibilidade em outros serviços. Por exemplo, o Armazenamento do Azure mantém três réplicas de todos dados de blob, tabela e fila. Ele também permite a opção de replicação geográfica para armazenar backups de blobs e tabelas em uma região secundária. A Rede de Distribuição de Conteúdo do Azure permite que os blobs sejam armazenados em cache em todo o mundo para redundância e escalabilidade. Banco de dados SQL do Azure mantém várias réplicas também.

Além da série de artigos [Orientações técnicas de resiliência](https://aka.ms/bctechguide), veja o documento [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Práticas recomendadas para design de serviços em larga escala nos Serviços de Nuvem do Azure). Esses documentos fornecem uma discussão mais aprofundada dos recursos de disponibilidade da plataforma do Azure.

Embora o Azure ofereça vários recursos compatíveis com a alta disponibilidade, é importante compreender suas limitações:

* Para computação, o Azure garante que as funções estejam disponíveis e em execução, mas ele não sabe se seu aplicativo está em execução ou sobrecarregado.
* Para banco de dados SQL do Azure, os dados são replicados de forma síncrona dentro da região. Você pode optar pela replicação geográfica ativa, que permite até quatro cópias adicionais de banco de dados na mesma região (ou em regiões diferentes). Essas réplicas de banco de dados não são backups pontuais. Os bancos de dados SQL fornecem recursos de backup pontuais. Para saber mais sobre os recursos pontuais de Banco de Dados SQL, leia [Azure SQL Database Point in Time Restore (Recuperação pontual do Banco de Dados SQL)](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
* Para armazenamento do Azure, tabela e blob de dados são replicados por padrão em uma região alternativa. No entanto, você não pode acessar as réplicas até que o Microsoft decida efetuar failover para o site alternativo. Um failover de região ocorre apenas no caso de uma interrupção prolongada de serviço de toda a região, e não há nenhum SLA para tempo de failover geográfico. Também é importante observar que qualquer dado corrompido se espalha rapidamente nas réplicas.

Por esses motivos, você deve complementar os recursos de disponibilidade da plataforma com recursos de disponibilidade específicos do aplicativo. Os recursos de disponibilidade específicos de aplicativo incluem o instantâneo de blob para criação de backups pontuais dos dados do blob.

### <a name="availability-sets-for-azure-virtual-machines"></a>Conjuntos de disponibilidade para Máquinas Virtuais do Azure
A maior parte deste artigo se concentra em serviços de nuvem, que usam um modelo PaaS (plataforma como serviço). No entanto, também há recursos de disponibilidade específicos de Máquinas Virtuais do Azure, que usam um modelo IaaS (infraestrutura como serviço). Para alcançar alta disponibilidade com Máquinas Virtuais, você deve usar conjuntos de disponibilidade. Um conjunto de disponibilidade serve a uma função semelhante aos domínios de falha e atualização. Em um conjunto de disponibilidade, o Azure posiciona as máquinas virtuais de uma maneira que impede a falhas de hardware localizadas e atividades de manutenção, desativando todos os computadores nesse grupo. Conjuntos de disponibilidade são necessários para obter o SLA do Azure para disponibilidade de máquinas virtuais.

O diagrama a seguir fornece uma representação de dois conjuntos de disponibilidade que agrupam máquinas virtuais da Web e do SQL Server, respectivamente.

![Conjuntos de disponibilidade para Máquinas Virtuais do Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

> [!NOTE]
> No diagrama anterior, o SQL Server está instalado e em execução em máquinas virtuais. Isso é diferente da discussão anterior sobre o Banco de Dados SQL do Azure, que fornece um banco de dados como um serviço gerenciado.
> 
> 

## <a name="application-strategies-for-high-availability"></a>Estratégias de aplicativo para alta disponibilidade
A maioria das estratégias de aplicativo para alta disponibilidade envolve a redundância ou a remoção de dependências de hardware entre componentes de aplicativos. O design do aplicativo deve dar suporte a tolerância a falhas durante tempo de inatividade esporádico do Azure ou serviços de terceiros. As seções a seguir descrevem padrões de aplicativo para aumentar a disponibilidade dos serviços de nuvem.

### <a name="asynchronous-communication-and-durable-queues"></a>Comunicação assíncrona e filas duráveis
Considere a possibilidade de comunicação assíncrona entre serviços acoplados livremente para aumentar a disponibilidade de aplicativos do Azure. Nesse padrão, grave mensagens em filas de armazenamento ou em filas do Barramento de Serviço do Azure para processamento posterior. Quando você escreve a mensagem na fila, o controle a retorna imediatamente para o remetente da mensagem. Outra camada do aplicativo manipula a processamento de mensagens, geralmente implementada como uma função de trabalho. Se a função de trabalho falhar, as mensagens serão acumuladas na fila até que o serviço de processamento seja restaurado. Como a fila está disponível, não há nenhuma dependência direta entre o remetente front-end e o processador de mensagens. Isso elimina a necessidade de chamadas de serviço síncronas que podem ser um gargalo de produtividade em aplicativos distribuídos.

Uma variação disso usa armazenamento do Azure (blobs, tabelas, filas) ou filas do Barramento de Serviço como um local de failover para chamadas de banco de dados com falha. Por exemplo, uma chamada síncrona de um aplicativo para outro serviço (como o banco de dados SQL do Azure) falha repetidamente. Você pode serializar os dados no armazenamento durável. Em algum momento posterior, quando o serviço ou o banco de dados estiver novamente online, o aplicativo poderá enviar novamente a solicitação do armazenamento. A diferença nesse modelo é que o local intermediário não é uma parte constante do fluxo de trabalho do aplicativo. Ele é usado apenas em cenários de falha.

Em ambos os cenários, a comunicação assíncrona e o armazenamento intermediário impedem que um serviço de back-end inativo interrompa o aplicativo inteiro. As filas servem como um intermediário lógico. Para saber mais sobre como escolher o serviço de enfileiramento correto, confira [Filas do Azure e filas de Barramento de Serviço do Azure — comparações e contrastes](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

### <a name="fault-detection-and-retry-logic"></a>Lógica de detecção e repetição de falhas
Um ponto importante no design do aplicativo altamente disponível é usar a lógica de repetição no código para tratar normalmente um serviço temporariamente inativo. O [bloco de aplicativos de tratamento de falhas transitórias](https://msdn.microsoft.com/library/hh680934.aspx), desenvolvido pela equipe de Padrões e Práticas da Microsoft, ajuda os desenvolvedores de aplicativo nesse processo. A palavra "transitório" significa uma condição temporária que dura apenas por um período relativamente curto. No contexto deste artigo, o tratamento de falhas transitórias faz parte do desenvolvimento de um aplicativo altamente disponível. Exemplos de condições transitórias incluem erros intermitentes de rede e perda de conexões de banco de dados.

O Bloco de aplicativos de tratamento de falhas transitórias é uma maneira simplificada de tratar falhas dentro de seu código de forma amigável. Você pode usá-lo para aumentar a disponibilidade dos aplicativos, adicionando lógica robusta de tratamento de falhas transitórias. Na maioria dos casos, a lógica de repetição trata as breves interrupções e reconecta o remetente e o destinatário depois de uma ou mais tentativas com falha. Uma tentativa de repetição bem-sucedida costuma passa despercebida aos usuários do aplicativo.

Os desenvolvedores têm três opções para gerenciar a respectiva lógica de repetição: incremental, intervalo fixo e exponencial. A incremental espera mais tempo antes de cada repetição de forma linear crescente (por exemplo, 1, 2, 3 e 4 segundos). O Intervalo fixo, aguarda a mesma quantidade de tempo entre cada repetição (por exemplo, 2 segundos). Para uma opção mais aleatória, a retirada exponencial aguarda mais tempo entre as repetições. No entanto, ele usa um comportamento exponencial (por exemplo, 2, 4, 8 e 16 segundos).

A estratégia de alto nível dentro de seu código é:

1. Definir sua estratégia e política de repetição.
2. Tentar a operação que pode resultar em uma falha transitória.
3. Se ocorrer falha transitória, invocar a política de repetição.
4. Se todas as novas tentativas falharem, capturar uma exceção final.

Teste sua lógica de repetição em falhas simuladas para garantir que as repetições em operações sucessivas não resultem em um longo atraso inesperado. Faça isso antes de tomar a decisão de deixar a tarefa global.

### <a name="reference-data-pattern-for-high-availability"></a>Padrão de dados de referência para alta disponibilidade
Dados de referência são os dados somente leitura de um aplicativo. Esses dados fornecem o contexto de negócios dentro do qual o aplicativo gera dados transacionais durante uma operação de negócios. Dados transacionais são uma função pontual dos dados de referência. Portanto, sua integridade depende do instantâneo dos dados de referência no momento da transação. Essa é uma definição ampla, mas deve ser suficiente para nosso objetivo aqui.

Dados de referência no contexto de um aplicativo são necessários para o funcionamento do aplicativo. Os respectivos aplicativos criam e mantêm dados de referência; sistemas MDM (gerenciamento de dados mestre) frequentemente executam essa função. Esses sistemas são responsáveis pelo ciclo de vida dos dados de referência. Catálogo de produtos, mestre de funcionários, mestre de peças e mestre de equipamentos são exemplos de dados de referência. Os dados de referência também podem se originar fora da organização, por exemplo, CEPs ou alíquota de imposto. Estratégias para aumentar a disponibilidade dos dados de referência normalmente são menos difíceis que para dados transacionais. Dados de referência têm a vantagem de ser, na maior parte, imutáveis.

Você pode criar funções de trabalho e web do Azure que consomem dados de referência de forma autônoma em tempo de execução ao implantar dados de referência com o aplicativo. Se o tamanho do armazenamento local permitir essa implantação, esse será um estado ideal. Bancos de dados inseridos (SQL, NoSQL) ou arquivos XML implantados em um sistema de arquivos local ajudarão com a autonomia de unidades de escala de computação do Azure. No entanto, você deve ter um mecanismo para atualizar os dados em cada função sem a necessidade de reimplantação. Para fazer isso, coloque as atualizações dos dados de referência em um ponto de extremidade de armazenamento de nuvem (por exemplo, o armazenamento de blobs do Azure ou o banco de dados SQL). Adicione código a cada função que baixa as atualizações de dados em nós de computação na inicialização da função. Como alternativa, adicione o código que permite que um administrador execute um download forçado nas instâncias da função.

Para aumentar a disponibilidade, as funções também devem conter um conjunto de dados de referência caso o armazenamento esteja inativo. Isso permite que as funções iniciem com um conjunto básico de dados de referência até que os recursos de armazenamento estejam disponíveis para as atualizações.

![Alta disponibilidade de aplicativo através de nós de computação autônomos](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Uma consideração para esse padrão é a velocidade de implantação e inicialização de suas funções. Se você estiver implantando ou baixando grandes quantidades de dados de referência na inicialização, isso poderá aumentar a quantidade de tempo que leva para criar novas implantações ou instâncias de função. Isso pode ser uma compensação aceitável para a autonomia de ter dados de referência imediatamente disponíveis em cada função em vez de depender de serviços de armazenamento externo.

### <a name="transactional-data-pattern-for-high-availability"></a>Padrão de dados transacionais para alta disponibilidade
Dados transacionais são os dados que o aplicativo gera em um contexto de negócios. Dados transacionais são uma combinação do conjunto de processos de negócios que o aplicativo implementa e dos dados de referência que permitem esses processos. Exemplos de dados transacionais podem incluir pedidos, aviso de envio avançado, faturas e oportunidades de CRM (gerenciamento de relacionamento com o cliente). Os dados transacionais gerados serão alimentados em sistemas externos para manter um registro ou para processamento adicional.

Lembre-se de que os dados de referência podem ser alterados nos sistemas que são responsáveis por esses dados. Por esse motivo, os dados transacionais devem salvar o contexto de dados de referência pontual para que ele tenha as dependências externas mínimas para sua consistência semântica. Por exemplo, considere a remoção de um produto do catálogo alguns meses depois que um pedido é atendido. A prática recomendada é inserir o máximo de contexto de dados de referência para a transação. Isso preservará a semântica associada à transação, mesmo se os dados de referência forem alterados após a captura da transação.

Conforme mencionado anteriormente, arquiteturas que usam acoplamento flexível e comunicação assíncrona se prestam para níveis mais altos de disponibilidade. Isso se aplica a dados transacionais, mas a implementação é mais complexa. Noções tradicionais transacionais normalmente contam com o banco de dados para garantir a transação. Quando você introduz camadas intermediárias, o código do aplicativo deve tratar corretamente os dados em várias camadas para garantir consistência e durabilidade suficientes.

A seguinte sequência descreve um fluxo de trabalho que separa a captura de dados transacionais de seu processamento:

1. Nó de computação na Web: apresente dados de referência.
2. Armazenamento externo: salve dados transacionais intermediários.
3. Nó de computação na Web: conclua a transação do usuário final.
4. Nó de computação na Web: envie os dados transacionais concluídos com o contexto de dados de referência para um armazenamento durável temporário com garantia de oferecer uma resposta previsível.
5. Nó de computação na Web: informe ao usuário final a conclusão da transação.
6. Nó de computação em segundo plano: extraia os dados transacionais, pós-processamento, se necessário, e envie-os para o local final de armazenamento no sistema atual.

O diagrama a seguir mostra uma possível implementação desse projeto em um serviço de nuvem hospedado pelo Azure.

![Alta disponibilidade através de acoplamento flexível](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

As setas tracejadas no diagrama acima indicam processamento assíncrono. A função web front-end não está ciente desse processamento assíncrono. Isso resulta no armazenamento da transação no seu destino final com referência ao sistema atual. Devido à latência apresentada neste modelo assíncrono, os dados transacionais não estão imediatamente disponíveis para consulta. Portanto, cada unidade de dados transacionais precisa ser salva em um cache ou uma sessão de usuário para atender às necessidades imediatas da interface do usuário.

A função web independe do restante da infraestrutura. O perfil de disponibilidade é uma combinação da função web e fila do Azure e não de toda a infraestrutura. Além de alta disponibilidade, essa abordagem permite que a função web seja escala horizontalmente, independentemente do armazenamento de back-end. Esse modelo de alta disponibilidade pode ter um impacto sobre a economia das operações. Componentes adicionais, como filas do Azure e funções de trabalho, podem afetar os custos mensais com uso.

Observe que o diagrama anterior mostra uma implementação dessa abordagem desacoplada para dados transacionais. Há muitas outras implementações possíveis. A lista a seguir fornece algumas alternativas:

* Uma função de trabalho deve ser colocada entre a função web e a fila de armazenamento.
* Uma fila do Barramento de Serviço pode ser usada em vez de uma fila de armazenamento do Azure.
* O destino final pode ser o armazenamento do Azure ou um provedor de banco de dados diferente.
* O Cache do Azure pode ser usado na camada Web para fornecer os requisitos imediatos de cache após a transação.

### <a name="scalability-patterns"></a>Padrões de escalabilidade
É importante observar que a escalabilidade do serviço de nuvem afeta diretamente a disponibilidade. Se uma carga maior fizer com que o serviço não responda, a impressão de usuário será a de que o aplicativo está inativo. Siga as práticas recomendadas para escalabilidade com base em sua carga esperada de aplicativo e em futuras expectativas. A escala mais alta envolve várias considerações, como o uso de uma única conta ou de várias contas de armazenamento, compartilhando entre vários bancos de dados, e estratégias de cache. Para uma visão detalhada desses padrões, confira [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)(Práticas recomendadas para design de serviços em larga escala nos Serviços de Nuvem do Azure).

## <a name="next-steps"></a>Próximas etapas
Este artigo faz parte de uma série de artigos com foco na [recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md). O próximo artigo desta série é [Recuperação de desastre para aplicativos criados no Microsoft Azure](resiliency-disaster-recovery-azure-applications.md).

<!--HONumber=Oct16_HO2-->


