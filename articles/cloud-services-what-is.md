<properties 
	pageTitle="O que é um serviço de nuvem - gerenciamento de serviço do Azure" 
	description="Uma introdução ao serviço de nuvem no Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




#O que é um serviço de nuvem?
Quando você cria um aplicativo e o executa no Azure, o código e a configuração, juntos, são chamados de serviço de nuvem do Azure (conhecido como  *hosted service* em versões anteriores do Azure).

Criando um serviço de nuvem, você pode implantar um aplicativo web de multi-camadas no Windows Azure, definindo várias funções para distribuir o processamento e permitir dimensionamento flexível do aplicativo. Um serviço de nuvem consiste em uma ou mais funções Web e/ou funções de trabalho, cada uma com seus próprios arquivos e configuração de aplicativo. Os sites do Azure e máquinas virtuais também habilitam os aplicativos web no Azure.  A principal vantagem dos serviços de nuvem é a capacidade de suportar as mais complexas arquiteturas multi-camadas. Para obter uma comparação detalhada, consulte [Sites do Azure, Serviços de Nuvem e Máquinas Virtuais do Azure][Comparison].

Para um serviço de nuvem, o Azure mantém a infraestrutura para você, executando manutenção de rotina, aplicando patches aos sistemas operacionais e tentando se recuperar das falhas do serviço e do hardware. Se você definir pelo menos duas instâncias de cada função, a maioria da manutenção, além das próprias atualizações do serviço, pode ser executada sem interrupção do serviço. Um serviço de nuvem deve ter pelo menos duas instâncias de cada função para se qualificar para o Contrato de Nível de Serviço do Azure, que garante conectividade externa às suas funções voltadas para a internet em pelo menos 99,95% do tempo. 

Cada serviço de nuvem tem dois ambientes nos quais você pode implantar seu pacote e configuração de serviço. Você pode implantar um serviço de nuvem para p ambiente de preparo para testá-lo antes de promovê-lo para produção. Promover um serviço de nuvem do preparo para a produção é uma questão simples de permutar os endereços VIP (IP virtual) associados aos dois ambientes. 


## Conceitos ##


- **função de serviço de nuvem:** uma função de serviço de nuvem é composta por arquivos do aplicativo e uma configuração. Um serviço de nuvem pode ter dois tipos de função:
 
>- **função Web:**uma função Web fornece um servidor web de Serviços de Informações da Internet (IIS) dedicado, usado para hospedar aplicativos web front-end.

>- **função de trabalho:** os aplicativos hospedados em funções de trabalho podem executar tarefas assíncronas, de longa execução ou perpétuas, independentemente de interação do usuário ou de entrada.

- **instância de função:** uma instância de função é uma máquina virtual na qual o código do aplicativo e configuração da função são executados. Uma função pode ter várias instâncias definidas no arquivo de configuração do serviço.

- **sistema operacional convidado:** o sistema operacional convidado de um serviço de nuvem é o sistema operacional instalado nas instâncias de função (máquinas virtuais), nas quais o código do aplicativo é executado.

- **componentes do serviço de nuvem:** três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

>- **arquivo de definição do serviço**: o arquivo de definição do serviço de nuvem (.csdef) define o modelo do serviço, inclusive o número de funções.

>- **arquivo de configuração de serviço:** o arquivo de configuração do serviço de nuvem (.cscfg) fornece as configurações do serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

>- **pacote de serviço:** o pacote de serviço (.cspkg) contém o código do aplicativo e o arquivo de definição do serviço.

- **implantação do serviço de nuvem:** uma implantação de serviço de nuvem é uma instância de um serviço de nuvem implantada no ambiente de produção ou preparo do Azure. Você pode manter as implantações de preparo e produção.

- **ambientes de implantação:** o Azure oferece dois ambientes de implantação para os serviços de nuvem: um  *staging environment*ambiente de preparo no qual você pode testar a sua implantação antes de promovê-la para o  *production environment*ambiente de produção. Os dois ambientes são diferenciados apenas pelos endereços IP virtuais (VIP) pelos quais o serviço de nuvem é acessado. No ambiente de preparo, o GUID (identificador global exclusivo) do serviço de nuvem o identifica nas URLs (*GUID*.cloudapp.net). No ambiente de produção, a URL é baseada no prefixo de DNS amigável que atribuído ao serviço de nuvem (por exemplo,  *myservice*.cloudapp.net).

- **permutar implantações:** para promover uma implantação no ambiente de preparo do Azure para o ambiente de produção, você pode "permutar" as implantações trocando os VIPs pelos quais as duas implantações são acessadas. Após a implantação, o nome DNS para os pontos do serviço de nuvem aponta para a implantação no ambiente de preparo. 

- **monitoramento mínimo versus detalhado: ** *Minimal monitoring*, configurado por padrão para um serviço de nuvem, usa contadores de desempenho coletados dos sistemas operacionais host para as instâncias de função (máquinas virtuais). *Verbose monitoring*, coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para obter mais informações, consulte [Como monitorar serviços de nuvem][HTMonitorCloudServices].

- **Diagnóstico do Azure:** o Diagnóstico do Azure é a API que permite coletar dados de diagnóstico dos aplicativos em execução no Azure. O Diagnóstico do Azure deve ser habilitado para as funções do serviço de nuvem para que o monitoramento detalhado seja ativado. Para obter mais informações, consulte [Habilitando o diagnóstico no Azure][CloudServicesDiagnostics].

- **Vinculação de recurso:** Para mostrar as dependências do serviço de nuvem em outros recursos, é como uma instância de banco de dados SQL do Azure, você pode "vincular" o recurso a um serviço de nuvem. No Portal de Gerenciamento de Visualização, você pode exibir os recursos vinculados na página **Recursos Vinculados**, exibir o status dos recursos no painel e dimensionar uma instância vinculada do Banco de Dados SQL junto com as funções do serviço na página **Escala**. Vincular um recurso nesse sentido não o conecta ao aplicativo. Você deve configurar as conexões no código do aplicativo.

- **Dimensionar um serviço de nuvem:** um serviço de nuvem é dimensionado aumentando o número de instâncias de função (máquinas virtuais) implantadas para uma função. Um serviço de nuvem tem o dimensionamento diminuído com a diminuição de instâncias de função. No Portal de Gerenciamento de Visualização, você também pode dimensionar uma instância do Banco de Dados SQL vinculado alterando a edição do Banco de Dados SQL e o tamanho máximo do banco de dados ao dimensionar suas funções do serviço.

- **(SLA) Contrato de Nível de Serviço do Azure:** O SLA de computação do Azure garante que, quando você implanta duas ou mais instâncias de função para cada função, o acesso ao serviço de nuvem será mantido por pelo menos 99,95 por cento do tempo. Além disso, ações de detecção e corretivas serão iniciadas em 99,9 por cento do tempo em que o processo de uma instância de função não estiver em execução.Para obter mais informações, consulte [Contratos de Nível de Serviço][SLA].

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
