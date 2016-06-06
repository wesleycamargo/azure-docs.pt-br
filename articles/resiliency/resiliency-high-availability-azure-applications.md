<properties
   pageTitle="Alta disponibilidade para aplicativos do Azure | Microsoft Azure"
   description="Visões gerais técnicas e informações detalhadas sobre como projetar e criar aplicativos de alta disponibilidade no Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="hanuk;jroth;aglick"/>

#Alta disponibilidade para aplicativos baseados no Microsoft Azure
##Introdução à alta disponibilidade
Um aplicativo altamente disponível absorve flutuações em disponibilidade, carga e falhas temporárias em serviços dependentes e hardware. O aplicativo continua a operar com um usuário aceitável e nível de resposta sistêmico conforme definido por requisitos de negócios ou contratos de nível de serviço de aplicativo.

##Recursos de alta disponibilidade do Azure
O Azure tem muitos recursos internos da plataforma que oferecem suporte a aplicativos altamente disponíveis. Esta seção descreve alguns desses recursos principais. Para obter uma análise mais abrangente da plataforma, consulte [Orientações técnicas de resiliência do Azure](./resiliency-technical-guidance.md).

O Azure FC (Controlador de Malha) é responsável por provisionar e monitorar a condição das instâncias de computação do Azure. O Controlador de Malha verifica o status do hardware e software de instâncias do computador host e convidado. Quando ele detecta uma falha, força os SLAs automaticamente, realocando as instâncias da VM. O conceito de domínios de falha e atualização dá ainda mais suporte ao SLA de computação.

Quando várias instâncias de função são implantadas, o Azure implanta essas instâncias em diferentes domínios de falha. Um limite de domínio de falha é basicamente um rack de hardware diferentes na mesma região. Domínios de falha reduzem a probabilidade de que uma falha de hardware localizada interrompa o serviço de um aplicativo. Você não pode gerenciar o número de domínios de falha que são alocados às funções de trabalho ou da Web. O Controlador de Malha usa os recursos dedicados que são separados dos aplicativos hospedados do Azure. Ele tem 100% de tempo de atividade porque serve como núcleo do sistema do Azure. Ele monitora e gerencia instâncias da função em domínios de falha. O diagrama a seguir mostra os recursos compartilhados do Azure que são implantados e gerenciados pelo FC em diferentes domínios de falha.

###Isolamento de domínio de falha - exibição simplificada

![Isolamento de domínio de falha - Exibição simplificada](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png "Isolamento de domínio de falha - Exibição simplificada")

_Isolamento de domínio de falha - Exibição simplificada_

Domínios de atualização são semelhantes a domínios de falha na função, mas oferecem suporte a atualizações em vez de falhas. Um domínio de atualização é uma unidade lógica de separação da instância que determina quais instâncias em um serviço específico serão atualizadas em um ponto no tempo. Por padrão, para a implantação do serviço hospedado, são definidos cinco domínios de atualização. No entanto, você pode alterar esse valor no arquivo de definição de serviço. Por exemplo, você tem oito instâncias da função web. Haverá duas instâncias em três domínios de atualização e duas instâncias em um domínio de atualização. O Azure define a sequência de atualização, mas é baseado no número de domínios de atualização. Para obter mais informações sobre domínios de atualização, consulte [Atualizar um serviço do Azure](../cloud-services/cloud-services-update-azure-service.md).

Além desses recursos de plataforma que oferecem suporte a computação de alta disponibilidade, o Azure insere recursos de alta disponibilidade em outros serviços. Por exemplo, o Armazenamento do Azure mantém três réplicas de todos dados de blob, tabela e fila. Ele também permite a opção de replicação geográfica para armazenar backups de blobs e tabelas em uma região secundária. A CDN (Rede de Distribuição de Conteúdo) permite que os blobs sejam armazenados em cache em todo o mundo para redundância e escalabilidade. Banco de dados SQL do Azure mantém várias réplicas também. Além da série de artigos [Orientações técnicas de resiliência](https://aka.ms/bctechguide), consulte o documento [Práticas recomendadas para design de serviços em larga escala nos Serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/). Esses documentos fornecem uma discussão mais aprofundada dos recursos de disponibilidade da plataforma do Azure.

Embora o Azure ofereça vários recursos que dão suporte a alta disponibilidade, é importante compreender suas limitações. Para computação, o Azure garante que as funções estejam disponíveis e em execução, mas ele não sabe se seu aplicativo está em execução ou sobrecarregado. Para banco de dados SQL do Azure, os dados são replicados de forma síncrona dentro da região. Essas réplicas de banco de dados não são backups pontuais. Para armazenamento do Azure, tabela e blob de dados são replicados por padrão em uma região alternativa. No entanto, você não pode acessar as réplicas até que o Microsoft decida efetuar failover para o site alternativo. Um failover de região ocorre apenas no caso de uma interrupção prolongada de serviço de toda a região, e não há nenhum SLA para tempo de failover geográfico. Também é importante observar que qualquer dados corrompidos se espalha rapidamente nas réplicas. Por esses motivos, você deve complementar os recursos de disponibilidade da plataforma com recursos de disponibilidade específicos do aplicativo. Esses recursos de disponibilidade de aplicativos incluem o recurso de instantâneo de blob para criar backups pontuais de dados blob.

A maior parte desse documento se concentra em serviços de nuvem, que usam um modelo PaaS (Plataforma como Serviço). No entanto, também há recursos de disponibilidade específicos de máquinas virtuais do Azure, que usam um modelo IaaS (Infraestrutura como Serviço). Para alcançar alta disponibilidade com máquinas virtuais, você deve usar conjuntos de disponibilidade. Um conjunto de disponibilidade serve a uma função semelhante aos domínios de falha e atualização. Em um conjunto de disponibilidade, o Azure posiciona as máquinas virtuais de uma maneira que impede a falhas de hardware localizadas e atividades de manutenção, desativando todos os computadores nesse grupo. Conjuntos de disponibilidade são necessários para obter o SLA do Azure para disponibilidade de máquinas virtuais. O diagrama a seguir fornece uma representação de dois conjuntos de disponibilidade que agrupam máquinas virtuais da Web e do SQL Server, respectivamente.

###Conjuntos de disponibilidade para máquinas virtuais do Azure

![Conjuntos de disponibilidade para máquinas virtuais do Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png "Conjuntos de disponibilidade para máquinas virtuais do Azure")

_Conjuntos de disponibilidade para máquinas virtuais do Azure_

>[AZURE.NOTE]No diagrama anterior, o SQL Server está instalado e em execução em máquinas virtuais. Isso é diferente da discussão anterior do banco de dados SQL do Azure, que fornece o banco de dados como um serviço gerenciado.

##Estratégias de aplicativo para alta disponibilidade
A maioria das estratégias de aplicativo para alta disponibilidade envolve a redundância ou a remoção de dependências de hardware entre componentes de aplicativos. O design do aplicativo deve dar suporte a tolerância a falhas durante tempo de inatividade esporádico do Azure ou serviços de terceiros. As seções a seguir descrevem vários padrões de aplicativo para aumentar a disponibilidade dos serviços de nuvem.

##Comunicação assíncrona e filas duráveis
Considere a possibilidade de comunicação assíncrona entre serviços acoplados livremente para aumentar a disponibilidade de aplicativos do Azure. Nesse padrão, grave mensagens de filas de armazenamento ou filas do Barramento de Serviço para processamento posterior. Quando você escreve a mensagem na fila, o controle a retorna imediatamente para o remetente da mensagem. Outra camada do aplicativo manipula a processamento de mensagens, geralmente implementada como uma função de trabalho. Se a função de trabalho falhar, as mensagens serão acumuladas na fila até que o serviço de processamento seja restaurado. Como a fila está disponível, não há nenhuma dependência direta entre o remetente front-end e o processador de mensagens. Isso elimina a necessidade de chamadas de serviço síncronas que podem ser um gargalo de produtividade em aplicativos distribuídos.

Uma variação disso usa armazenamento do Azure (blobs, tabelas, filas) ou filas do Barramento de Serviço como um local de failover para chamadas de banco de dados com falha. Por exemplo, uma chamada síncrona de um aplicativo para outro serviço (como o banco de dados SQL do Azure) falha repetidamente. Você poderá serializar os dados no armazenamento durável. Em algum momento posterior quando o serviço ou o banco de dados estiver novamente online, o aplicativo poderá enviar novamente a solicitação de armazenamento. A diferença nesse modelo é que o local intermediário não é uma parte constante do fluxo de trabalho do aplicativo. Ele é usado apenas em cenários de falha.

Em ambos os cenários, comunicação assíncrona e o armazenamento intermediário impedem que um serviço de back-end inativo interrompa o aplicativo inteiro. As filas servem como um intermediário lógico. Para obter diretrizes sobre como escolher o serviço de enfileiramento de mensagens correto, consulte [Filas do Azure e filas de barramento de serviço do Azure - semelhanças e diferenças](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

##Lógica de detecção e repetição de falhas
Um ponto importante no design do aplicativo altamente disponível é utilizar a lógica de repetição no código para tratar normalmente um serviço temporariamente inativo. O [Bloco de aplicativos de tratamento de falhas transitórias](https://msdn.microsoft.com/library/hh680934.aspx), desenvolvido pela equipe de Padrões e práticas da Microsoft, ajuda os desenvolvedores de aplicativo nesse processo. A palavra "transitório" significa uma condição temporária que dura por um período relativamente curto. No contexto deste documento, o tratamento de falhas transitórias faz parte do desenvolvimento de um aplicativo altamente disponível. Exemplos de condições transitórias incluem erros intermitentes de rede e perda de conexões de banco de dados.

O Bloco de aplicativos de tratamento de falhas transitórias é uma maneira simplificada de tratar falhas dentro de seu código de forma amigável. Ele permite aumentar a disponibilidade dos aplicativos, adicionando lógica robusta de tratamento de falhas transitórias. Na maioria dos casos, a lógica de repetição trata as breves interrupções e reconecta o remetente e o destinatário depois de uma ou mais tentativas com falha. Uma tentativa de repetição bem-sucedida costuma passa despercebida aos usuários do aplicativo.

Há três opções para os desenvolvedores gerenciarem sua lógica de repetição: incremental, intervalo fixo e exponencial. A incremental espera mais tempo antes de cada repetição de forma linear crescente (por exemplo, 1, 2, 3 e 4 segundos). O Intervalo fixo, aguarda a mesma quantidade de tempo entre cada repetição (por exemplo, 2 segundos). Para uma opção mais aleatória, a retirada exponencial aguarda mais tempo entre as repetições. No entanto, ele usa um comportamento exponencial (por exemplo, 2, 4, 8 e 16 segundos).

A estratégia de alto nível dentro de seu código é:
1. Definir sua estratégia e política de repetição
1. Tentar a operação que pode resultar em uma falha transitória
1. Se ocorrer falha transitória, invoque a política de repetição
1. Se todas as novas tentativas falharem, capture uma exceção final 

Teste sua lógica de repetição em falhas simuladas para garantir que as repetições em operações sucessivas não resultem em um longo atraso inesperado. Faça isso antes de tomar a decisão de deixar a tarefa global.

##Padrão de dados de referência para alta disponibilidade
Dados de referência são os dados somente leitura de um aplicativo. Esses dados fornecem o contexto de negócios dentro do qual o aplicativo gera dados transacionais durante uma operação de negócios. Dados transacionais são uma função pontual dos dados de referência. Portanto, sua integridade depende do instantâneo dos dados de referência no momento da transação. Essa é uma definição ampla, mas deve ser suficiente para nosso objetivo aqui.

Dados de referência no contexto de um aplicativo são necessários para o funcionamento do aplicativo. Os respectivos aplicativos criam e mantêm dados de referência; sistemas de Gerenciamento de dados mestre frequentemente executam essa função. Esses sistemas são responsáveis pelo ciclo de vida dos dados de referência. Catálogo de produtos, mestre de funcionários, mestre de peças e mestre de equipamentos são exemplos de dados de referência. Os dados de referência também podem se originar de fora da organização, por exemplo, códigos postais ou taxas de impostos. Estratégias para aumentar a disponibilidade dos dados de referência normalmente são menos difíceis que para dados transacionais. Dados de referência têm a vantagem de ser, na maior parte, imutáveis.

Você pode criar funções de trabalho e na Web do Azure que consomem dados de referência de forma autônomas em tempo de execução ao implantar dados de referência com o aplicativo. Se o tamanho do armazenamento local permitir essa implantação, esse será um estado ideal. Bancos de dados inseridos (SQL, NOSQL) ou arquivos XML implantados no sistema de arquivos local ajudarão com a autonomia de unidades de escala de computação do Azure. No entanto, você deve ter um mecanismo para atualizar os dados em cada função sem a necessidade de reimplantação. Para fazer isso, coloque as atualizações dos dados de referência em um ponto de extremidade de armazenamento de nuvem (por exemplo, o armazenamento de blobs do Azure ou o banco de dados SQL). Adicione código a cada função que baixa as atualizações de dados em nós de computação na inicialização da função. Como alternativa, adicione o código que permite que um administrador execute um download forçado nas instâncias da função. Para aumentar a disponibilidade, as funções também devem conter um conjunto de dados de referência caso o armazenamento esteja inativo. Isso permite que as funções iniciem com um conjunto básico de dados de referência até que os recursos de armazenamento estejam disponíveis para as atualizações.

###Alta disponibilidade através de nós de computação autônomos

![Alta disponibilidade de aplicativo através de nós de computação autônomos](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png "Alta disponibilidade de aplicativo através de nós de computação autônomos")

_Alta disponibilidade de aplicativo através de nós de computação autônomos_

Uma consideração para esse padrão é a velocidade de implantação e inicialização de suas funções. Se você estiver implantando ou baixando grandes quantidades de dados de referência na inicialização, isso poderá aumentar a quantidade de tempo que leva para criar novas implantações ou instâncias de função. Isso pode ser uma compensação aceitável para a autonomia de ter dados de referência imediatamente disponíveis em cada função em vez de depender de serviços de armazenamento externo.

##Padrão de dados transacionais para alta disponibilidade
Dados transacionais são os dados gerados pelo aplicativo em um contexto de negócios. Dados transacionais são uma combinação do conjunto de processos de negócios que o aplicativo implementa e os dados de referência que dão suporte a esses processos. Exemplos de dados transacionais podem incluir pedidos, aviso de envio avançado, faturas e oportunidades de CRM. Os dados transacionais gerados serão alimentados em sistemas externos para manter um registro ou para processamento adicional.

Lembre-se de que os dados de referência podem ser alterados nos sistemas que são responsáveis por esses dados. Por esse motivo, os dados transacionais devem salvar o contexto de dados de referência pontual para que ele tenha as dependências externas mínimas para sua consistência semântica. Por exemplo, considere a remoção de um produto do catálogo alguns meses depois que um pedido foi atendido. A prática recomendada é inserir o máximo de contexto de dados de referência para a transação. Isso preservará a semântica associada à transação, mesmo se os dados de referência forem alterados após a captura da transação.

Conforme mencionado anteriormente, arquiteturas que usam acoplamento flexível e comunicação assíncrona se prestam para níveis mais altos de disponibilidade. Isso se aplica a dados transacionais, mas a implementação é mais complexa. Noções tradicionais transacionais normalmente contam com o banco de dados para garantir a transação. Quando você introduz camadas intermediárias, o código do aplicativo deve tratar corretamente os dados em várias camadas para garantir consistência e durabilidade suficientes.

A seguinte sequência descreve um fluxo de trabalho que separa a captura de dados transacionais de seu processamento:

1. Nó de computação na Web: apresente dados de referência.
1. Armazenamento externo: salve dados transacionais intermediários.
1. Nó de computação na Web: conclua a transação do usuário final.
1. Nó de computação na Web: envie os dados transacionais concluídos junto com o contexto de dados de referência para um armazenamento durável temporário com garantia de oferecer resposta previsível.
1. Nó de computação na Web: informe ao usuário final a conclusão da transação.
1. Nó de computação em segundo plano: extraia os dados transacionais, pós-processamento, se necessário, e envie-os para o local final de armazenamento no sistema atual.

O diagrama a seguir mostra uma possível implementação desse projeto em um serviço de nuvem hospedado no Azure.

###Alta disponibilidade através de acoplamento flexível

![Alta disponibilidade através de acoplamento flexível](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png "Alta disponibilidade através de acoplamento flexível")

_Alta disponibilidade de aplicativo através de acoplamento flexível_

As setas tracejadas no diagrama anterior indicam processamento assíncrono. A função web front-end não está ciente desse processamento assíncrono. Isso resulta no armazenamento da transação no seu destino final com referência ao sistema atual. Devido à latência apresentada neste modelo assíncrono, os dados transacionais não estão imediatamente disponíveis para consulta. Portanto, cada unidade de dados transacionais precisa ser salva em um cache ou sessão de usuário para atender às necessidades imediatas da interface do usuário.

Consequentemente, a função web é autônoma do restante da infraestrutura. O perfil de disponibilidade é uma combinação da função web e fila do Azure e não de toda a infraestrutura. Além de alta disponibilidade, essa abordagem permite que a função web seja dimensionada horizontalmente, independentemente do armazenamento de back-end. Esse modelo de alta disponibilidade pode ter um impacto sobre a economia das operações. Componentes adicionais como filas do Azure e funções de trabalho podem afetar os custos mensais com uso. Observe que o diagrama anterior mostra uma implementação dessa abordagem desacoplada para dados transacionais. Há muitas outras implementações possíveis. A lista a seguir fornece algumas variações alternativas.

 * Uma função de trabalho deve ser colocada entre a função web e a fila de armazenamento.
 * Uma fila do Barramento de Serviço pode ser usada em vez de uma fila de armazenamento do Azure.
 * O destino final pode ser o armazenamento do Azure ou um provedor de banco de dados diferente.
 * O Cache do Azure pode ser usado na camada Web para fornecer os requisitos imediatos de cache após a transação.

##Padrões de escalabilidade
Além dos padrões abordados nesta seção, é importante observar que a escalabilidade do serviço de nuvem afeta diretamente a disponibilidade. Se uma carga maior fizer com que o serviço não responda, a impressão de usuário será a de que o aplicativo está inativo. Siga as práticas recomendadas para escalabilidade com base em sua carga esperada de aplicativo e em futuras expectativas. A escala mais alta envolve várias considerações, como o uso de valor único versus várias contas de armazenamento, compartilhando entre vários bancos de dados e estratégias de cache. Para uma visão detalhada desses padrões, consulte [Práticas recomendadas para o design de serviços em larga escala nos serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##Próximas etapas
Este artigo faz parte de uma série de artigos com foco na [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). O próximo artigo desta série é [Recuperação de desastres para aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).

<!---HONumber=AcomDC_0525_2016-->