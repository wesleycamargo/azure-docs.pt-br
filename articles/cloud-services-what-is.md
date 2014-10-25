<properties linkid="manage-services-what-is-a-cloud-service" urlDisplayName="What is a Cloud Service" pageTitle="What is a cloud service - Azure service management" metaKeywords="Azure cloud services intro, cloud services overview, cloud services basics" description="An introduction to the cloud service in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="What is a cloud service?" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# O que é um serviço de nuvem?

Quando você cria um aplicativo e o executa no Azure, o código e a configuração, juntos, são chamados de serviço de nuvem do Azure (conhecido como *serviço hospedado* em versões anteriores do Azure).

Criando um serviço de nuvem, você pode implantar um aplicativo web de multi-camadas no Windows Azure, definindo várias funções para distribuir o processamento e permitir dimensionamento flexível do aplicativo. Um serviço de nuvem consiste em uma ou mais funções Web e/ou funções de trabalho, cada uma com seus próprios arquivos e configuração de aplicativo. Os sites do Azure e máquinas virtuais também habilitam os aplicativos web no Azure. A principal vantagem dos serviços de nuvem é a capacidade de suportar as mais complexas arquiteturas multi-camadas. Para obter uma comparação detalhada, consulte [Sites do Azure, Serviços de Nuvem e Máquinas Virtuais do Azure][Sites do Azure, Serviços de Nuvem e Máquinas Virtuais do Azure].

Para um serviço de nuvem, o Azure mantém a infraestrutura para você, executando manutenção de rotina, aplicando patches aos sistemas operacionais e tentando se recuperar das falhas do serviço e do hardware. Se você definir pelo menos duas instâncias de cada função, a maioria da manutenção, além das próprias atualizações do serviço, pode ser executada sem interrupção do serviço. Um serviço de nuvem deve ter pelo menos duas instâncias de cada função para se qualificar para o Contrato de Nível de Serviço do Azure, que garante conectividade externa às suas funções voltadas para a internet em pelo menos 99,95% do tempo.

Cada serviço de nuvem tem dois ambientes nos quais você pode implantar seu pacote e configuração de serviço. Você pode implantar um serviço de nuvem para p ambiente de preparo para testá-lo antes de promovê-lo para produção. Promover um serviço de nuvem do preparo para a produção é uma questão simples de permutar os endereços VIP (IP virtual) associados aos dois ambientes.

## Conceitos

-   **função de serviço de nuvem:** Uma função de serviço de nuvem é composta por arquivos de aplicativo e uma configuração. Um serviço de nuvem pode ter dois tipos de função:

> -   **função web:** uma função web fornece um servidor web de Serviços de Informações da Internet (IIS) dedicado, usado para hospedar aplicativos web de front-end.

> -   **função de trabalho:** Os aplicativos hospedados em funções de trabalho podem executar tarefas assíncronas, de execução longa ou perpétuas, independente da interação do usuário ou da entrada.

-   **instância de função:** uma instância de função é uma máquina virtual na qual o código do aplicativo e configuração da função são executados. Uma função pode ter várias instâncias definidas no arquivo de configuração do serviço.

-   **sistema operacional convidado:** O sistema operacional convidado para um serviço de nuvem é o sistema operacional instalado nas instâncias de função (as máquinas virtuais), no qual seu código do aplicativo é executado.

-   **componentes do serviço de nuvem:** Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

> -   **arquivo de definição de serviço:** O arquivo de definição do serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.

> -   **arquivo de configuração do serviço:** O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

> -   **pacote de serviço:** O pacote de serviço (arquivo .cspkg) contém o código do aplicativo e o arquivo de definição do serviço.

-   **implantação do serviço de nuvem:** Uma implantação do serviço de nuvem é uma instância de um serviço de nuvem implantada em ambiente de preparação ou de produção de Azure. Você pode manter as implantações no preparo e na produção.

-   **ambientes de implantação:** O Azure oferece dois ambientes da implantação para os serviços de nuvem: um *ambiente de preparo*, onde você pode testar sua implantação antes da promovê-la para o *ambiente de produção*. Os dois ambientes são diferenciados apenas pelos endereços IP virtuais (VIP) pelos quais o serviço de nuvem é acessado. No ambiente de preparo, o GUID (identificador global exclusivo) do serviço de nuvem o identifica nas URLs (*GUID*.cloudapp.net). No ambiente de produção, a URL é baseada no prefixo DNS mais amigável atribuído ao serviço de nuvem (por exemplo, *myservice*.cloudapp.net).

-   **permuta de implantações:** Para promover uma implantação no ambiente de preparo do Azure para o ambiente de produção, você pode “permutar” as implantações trocando os VIPs pelos quais as duas implantações são acessadas. Após a implantação, o nome DNS dos pontos do serviço de nuvem apontam para a implantação que estava no ambiente de preparo.

-   **monitoramento mínimo versus detalhado:** *Monitoramento mínimo*, configurado por padrão para um serviço de nuvem, usa contadores de desempenho coletados dos sistemas operacionais host para as instâncias de função (máquinas virtuais). O *Monitoramento detalhado* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para obter mais informações, consulte [Como monitorar serviços de nuvem][Como monitorar serviços de nuvem].

-   **Diagnóstico do Azure:** O Diagnóstico do Azure é a API que permite que você colete dados de diagnóstico dos aplicativos m execução no Azure. O Diagnóstico do Azure deve ser habilitado para as funções do serviço de nuvem para que o monitoramento detalhado seja ativado. Para obter mais informações, consulte [Habilitando o diagnóstico no Azure][Habilitando o diagnóstico no Azure] (a página pode estar em inglês).

-   **vinculação de recurso:** Para mostrar as dependências do serviço de nuvem em outros recursos, é como uma instância de banco de dados SQL do Azure, você pode "vincular" o recurso a um serviço de nuvem. No Portal de Gerenciamento de Visualização, você pode exibir os recursos vinculados na página **Recursos Vinculados**, exibir o status dos recursos no painel e dimensionar uma instância vinculada do Banco de Dados SQL junto com as funções do serviço na página **Escala**. Vincular um recurso nesse sentido não o conecta ao aplicativo. Você deve configurar as conexões no código do aplicativo.

-   **dimensionamento de um serviço de nuvem:** um serviço de nuvem é tem o dimensionamento aumentado com o aumento do número de instâncias de função (máquinas virtuais) implantadas para uma função. Um serviço de nuvem tem o dimensionamento diminuído com a diminuição de instâncias de função. No Portal de Gerenciamento de Visualização, você também pode dimensionar uma instância do Banco de Dados SQL vinculado alterando a edição do Banco de Dados SQL e o tamanho máximo do banco de dados ao dimensionar suas funções do serviço.

-   **Contrato de Nível de Serviço (SLA) do Azure:** O SLA de computação do Azure garante que, quando você implanta duas ou mais instâncias de função para cada função, o acesso ao serviço de nuvem será mantido por pelo menos 99,95 por cento do tempo. Além disso, ações de detecção e corretivas serão iniciadas em 99,9 por cento do tempo em que o processo de uma instância de função não estiver em execução. Para obter mais informações, consulte [Contratos de Nível de Serviço][Contratos de Nível de Serviço].

  [Sites do Azure, Serviços de Nuvem e Máquinas Virtuais do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/choose-web-site-cloud-service-vm/
  [Como monitorar serviços de nuvem]: http://azure.microsoft.com/pt-br/manage/services/cloud-services/how-to-monitor-a-cloud-service/
  [Habilitando o diagnóstico no Azure]: http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-dotnet-diagnostics/
  [Contratos de Nível de Serviço]: http://azure.microsoft.com/pt-br/support/legal/sla/
