---
title: "Introdução à Automação do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral do serviço de Automação do Azure. Ele examina os detalhes de design e implementação no preparo para a integração da oferta do Microsoft Azure Marketplace."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: d6ee5c35ce9866f6106c7b5dbc51599b666c3eb1
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="get-started-with-azure-automation"></a>Introdução à Automação do Azure

Este artigo apresenta os conceitos principais relacionados à implantação da Automação do Azure. Se você for novo na Automação no Azure ou tem experiência com softwares de automação de fluxo de trabalho como o System Center Orchestrator, você aprende a preparar e carregar a Automação. Depois de ler este artigo, você estará pronto para começar a desenvolver runbooks para dar suporte às suas necessidades de automação do processo. 


## <a name="automation-architecture-overview"></a>Visão geral de arquitetura de automação

![Visão geral da Automação do Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Automação do Azure é um aplicativo de software como serviço (SaaS) que fornece um ambiente de multilocatário escalonável e confiável onde você pode usar runbooks para automatizar processos. Você pode usar a configuração de estado desejado (DSC) do Azure, outros serviços em nuvem, ou em um ambiente local para gerenciar mudanças de configurações nos sistemas Windows e Linux. As entidades em sua conta de Automação do Azure, incluindo runbooks, ativos e contas Executar como, são isoladas de outras contas de automação em sua assinatura e outras assinaturas.  

Runbooks que são executados no Azure são executados em áreas restritas de automação. As áreas restritas são hospedadas na plataforma do Azure como máquinas virtuais de serviço (PaaS). 

As áreas restritas de Automação do Azure fornecem isolamento de locatários para todos os aspectos de execução de runbook, incluindo módulos, armazenamento, memória, comunicação de rede e fluxos de trabalho. Essa função é gerenciada pelo serviço. Você não pode acessar ou gerenciar a função a partir de sua conta de automação ou do Azure.         

Para automatizar a implantação e o gerenciamento dos recursos no seu centro de dados local ou em outros serviços de nuvem, depois de criar uma conta de Automação do Azure, você pode designar uma ou mais VMs para executar a função do [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Cada Hybrid Runbook Worker exige que o Agente de Gerenciamento da Microsoft seja instalado e uma conta de Automação do Azure. O agente deve ter uma conexão com um espaço de trabalho de Log Analytics do Azure. O Log Analytics é usado para inicializar a instalação, manter o Agente de Gerenciamento da Microsoft e monitorar a funcionalidade do Hybrid Runbook Worker. Automação do Azure executa o fornecimento de runbooks e as instruções para executá-los.

Você pode implantar vários Hybrid Runbook Workers. Use o Hybrid Runbook Workers para fornecer alta disponibilidade para seus runbooks e trabalhos de runbook de balanceamento de carga. Em alguns casos, você pode dedicar os trabalhos de runbook a ambientes ou cargas de trabalho específicas. O Microsoft Monitoring Agent no Hybrid Runbook Worker inicia a comunicação com o serviço de Automação do Azure pela porta TCP 443. O Hybrid Runbook Workers não exige os requisitos de firewall de entrada.  

Talvez você queira um runbook que esteja em execução em um Hybrid Runbook Worker para executar tarefas de gerenciamento em relação a outros serviços ou computadores em seu ambiente. Nesse cenário, o runbook também precisará acessar outras portas. Se suas políticas de segurança de TI não permitirem que os computadores em sua rede se conectem à internet, examine [Gateway de OMS](../log-analytics/log-analytics-oms-gateway.md). O Gateway do Conjunto de Gestão de Operações (OMS) atua como um proxy para Hybrid Runbook Worker. Ele Coleta o status de trabalho e recebe informações de configuração da sua conta de Automação do Azure.

Runbooks que são executados em um Hybrid Runbook Worker são executados no contexto da conta do sistema local no computador. É recomendável um contexto de segurança quando você executar ações administrativas no computador local do Windows. Se você quiser que o runbook execute tarefas com base nos recursos que estão fora do computador local, você precisará definir os ativos de credencial de segurança na conta de Automação do Azure. Você pode acessar os ativos de credencial de segurança do runbook e usá-los para autenticar com o recurso externo. Você pode usar os ativos [Credencial](automation-credentials.md), [Certificado](automation-certificates.md) e [Conexão](automation-connections.md) no seu runbook. Use os ativos com os cmdlets que você pode usar para especificar credenciais para autenticá-los.

Você pode aplicar configurações de DSC que são armazenadas na Automação do Azure para máquinas virtuais. Outras máquinas virtuais e físicas podem solicitar configurações do servidor de pull de DSC de Automação. Você não precisa implantar nenhuma infraestrutura para dar suporte ao servidor de pull de DSC de Automação para gerenciar as configurações dos seus sistemas virtuais ou físicos do Windows ou do Linux. Você só precisa de acesso de internet de saída a partir de cada sistema que você deseja gerenciar usando o DSC de Automação. A comunicação ocorre sobre a porta TCP 443 para o serviço OMS.   

## <a name="prerequisites"></a>pré-requisitos

### <a name="automation-dsc"></a>DSC de automação
Você pode usar o DSC de Automação para gerenciar esses computadores:

* Máquinas virtuais do Azure (clássico) executando Windows ou Linux.
* Máquinas virtuais do Azure executando Windows ou Linux.
* Máquinas virtuais da Amazon Web Services (AWS) executando Windows ou Linux.
* Computadores com Windows físicos e virtuais locais ou em nuvem diferente do Azure ou AWS.
* Computadores com Linux físicos e virtuais locais ou em nuvem diferente do Azure ou AWS.

Para computadores do Windows, a versão mais recente do Windows Management Framework (WMF) 5 deve ser instalada. Para computadores do Linux, a versão mais recente do [Agente do DSC do PowerShell para Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) deve ser instalada. O agente do DSC do PowerShell usa WMF 5 para se comunicar com a Automação do Azure. 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Quando você designa um computador para executar trabalhos de runbook híbridos, o computador deve atender aos seguintes pré-requisitos:

* Windows Server 2012 ou posterior.
* Windows PowerShell 4.0 ou posterior. Para aumentar a confiabilidade, recomendamos o PowerShell 5.0 do Windows. Você pode [fazer o download da nova versão](https://www.microsoft.com/download/details.aspx?id=50395) no Centro de Downloads da Microsoft.
* .NET Framework 4.6.2 ou posterior.
* Um mínimo de dois núcleos.
* Um mínimo de 4 GB de RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação
Para criar ou atualizar uma conta de Automação e concluir as tarefas descritas neste artigo, é necessário ter os seguintes privilégios e permissões:   
 
* Para criar uma conta de automação, sua conta de usuário do Azure Active Directory (Microsoft Azure AD) deve ser adicionada a uma função com permissões equivalentes à função de proprietário para recursos do **Microsoft.Automation**. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).  
* No portal do Azure, em **Azure Active Directory** > **GERENCIAR** > **Registros do aplicativo**, se **Registros do aplicativo**  estiver definido como **Sim**, os usuários não administradores no locatário do Azure AD poderão [registrar aplicativos do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Se **Registros do aplicativo** estiver definido como **Não**, o usuário que executar esta ação precisará ser um administrador global no Microsoft Azure AD. 

Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionado à função de administrador global/coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Neste cenário, você recebe esta mensagem na página **Adicionar Conta de Automação**: “Você não tem permissões para criar”. 

Se um usuário for adicionado à função de administrador global/coadministrator pela primeira vez, você poderá removê-los da instância do Active Directory da assinatura e, em seguida, adicioná-los novamente à função de Usuário completa no Active Directory.

Para verificar as funções de usuário:
1. No portal do Azure, acesse o painel **Azure Active Directory**.
2. Selecione **Usuários e grupos**.
3. Selecione **Todos os usuários**. 
4. Depois de selecionar um usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** no perfil do usuário não deve ser igual a **Convidado**.

## <a name="authentication-planning"></a>Plano para autenticação
Na Automação do Azure, você pode automatizar tarefas com base nos recursos que estão no Azure, no local e em outros serviços em nuvem. Para que um runbook execute as ações necessárias, ele deve ter permissões para acessar os recursos com segurança. Ele deve ter os direitos mínimos necessários dentro da assinatura.  

### <a name="what-is-an-automation-account"></a>O que é uma Conta de Automação 
Todas as tarefas de automação que você pode executar com base nos recursos usando os cmdlets na Automação do Azure autenticam o Azure usando a autenticação com base em credenciais de identidade organizacional do Microsoft Azure AD.  Uma conta de automação é separada da conta usada para entrar no portal para configurar e usar os recursos do Azure. 

Os recursos a seguir são incluídos com uma conta de Automação do Azure:

* **Certificados**. Contém um certificado usado para autenticação a partir de um runbook ou a configuração DSC. Você também pode adicionar certificados.
* **Conexões**. Contém informações de autenticação e configuração necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou configuração DSC.
* **Credenciais**. Contém um objeto **PSCredential**, que tem as credenciais de segurança, como um nome de usuário e senha. As credenciais são necessárias para autenticar a partir de um runbook ou configuração DSC.
* **Módulos de integração**. Os módulos do PowerShell que estão incluídos com uma conta de Automação do Azure. Use os módulos do PowerShell para executar os cmdlets em runbooks e as configurações DSC.
* **Agendas**. Contém agendamentos que iniciam ou interrompem um runbook em um horário especificado, incluindo frequências recorrentes.
* **Variáveis**. Contêm valores que estão disponíveis de um runbook ou configuração DSC.
* **Configurações DSC**. Scripts do PowerShell que descreve como configurar um recurso do sistema operacional ou a configuração ou como instalar um aplicativo em um computador Windows ou Linux.  
* **Runbooks**. Um conjunto de tarefas que executa um processo automatizado na Automação do Azure baseado no Windows PowerShell.    

Recursos de automação para cada conta de automação estão associados uma única região do Azure. No entanto, você pode usar contas de Automação do Azure para gerenciar todos os recursos em sua assinatura. Crie contas de Automação em regiões diferentes se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

Quando você cria uma conta de automação no portal do Azure, as duas entidades de autenticação são criadas automaticamente:

* **Conta Executar como**. Essa conta executa as seguintes tarefas:
  - Cria uma entidade de serviço no Microsoft Azure AD.
  - Cria um certificado.
  - Atribui o RBAC (Controle de Acesso Baseado em Função) de Colaborador, que gerencia os recursos do Azure Resource Manager por meio de runbooks.
* **Conta Executar como clássica**. Essa conta carrega um certificado de gerenciamento. O certificado é usado para gerenciar os recursos clássicos usando runbooks.

RBAC está disponível com o Gerenciador de Recursos para conceder as ações permitidas para uma conta de usuário do Microsoft Azure AD e uma conta Executar como. Você também pode usar o RBAC para autenticar essa entidade de serviço. Para obter mais informações e para obter ajuda para o desenvolvimento de um modelo para gerenciar permissões de Automação do Azure, consulte [Controle de acesso baseado em função no artigo de automação do Azure](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Métodos de autenticação
A tabela a seguir resume os métodos de autenticação que você pode usar para cada ambiente para o qual a Automação do Azure oferece suporte.

| Método | Ambiente 
| --- | --- | 
| Contas Executar como do Azure e Executar como clássica |Implantação clássica do Azure de Azure Resource Manager |  
| Conta de Usuário do Azure AD |Implantação clássica do Azure de Azure Resource Manager |  
| Autenticação do Windows |Centro de dados local ou outro provedor de serviços de nuvem usando a função do Hybrid Runbook Worker |  
| Credenciais do Amazon Web Services |Amazon Web Services |  

Os artigos a seguir fornecem a visão geral e as etapas de implementação para configurar a autenticação para esses ambientes. Os artigos descrevem o uso de uma conta existente e uma nova conta dedicada por você para esse ambiente. 
* [Criar conta autônoma de Automação do Azure](automation-create-standalone-account.md)
* [Autenticar Runbooks com implantação clássica do Azure e do Resource Manager](automation-create-aduser-account.md)
* [Autenticar runbooks com o Amazon Web Services](automation-config-aws-account.md)
* [Atualizar a conta Executar como de Automação](automation-create-runas-account.md)

Para a conta Executar como do Azure e Executar como clássica, [Atualização da Conta de automação Executar como](automation-create-runas-account.md) descreve como atualizar sua conta de automação existente com as contas Executar como a partir do portal. O tópico também descreve como usar o PowerShell se a conta de Automação do Azure não tiver sido originalmente configurada com uma conta Executar como ou conta Executar como clássica. Você pode criar uma conta Executar como e uma conta Executar como clássica usando um certificado emitido por sua autoridade de certificação corporativa (CA). Examine [Atualizar a Automação do Azure da conta Executar como](automation-create-runas-account.md) para aprender a criar as contas usando essa configuração.     
 
## <a name="network-planning"></a>Planeje a sua rede
Para que o Hybrid Runbook Worker se conecte e se registre com o OMS, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. Isso é um adendo às [portas e URLs necessárias para que o Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) se conecte ao OMS. 

Se você usar um servidor proxy para comunicação entre o agente e o serviço do OMS, certifique-se de que os recursos apropriados estejam acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso.

A porta e URLs a seguir são necessárias para a função do Hybrid Runbook Worker se comunicar com a Automação do Azure:

* Porta: Somente a TCP 443 é necessária para acesso de Internet de saída.
* URL global: *.azure-automation.net.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região.

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft. 

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. Os intervalos de computação, SQL e de armazenamento são incluídos no arquivo. 
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana. 
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Usuários do ExpressRoute do Azure devem observar que esse arquivo é usado para atualizar o anúncio do Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês. 
> 

## <a name="creating-an-automation-account"></a>Criar uma conta de Automação

A tabela a seguir apresenta os métodos para criar uma conta de Automação no portal do Azure. A tabela apresenta cada tipo de experiência de implantação e as diferenças entre elas.  

|Método | DESCRIÇÃO |
|-------|-------------|
| Selecione **Automação e Controle** no Azure Marketplace | Uma oferta do Marketplace do Azure cria uma conta de automação e espaço de trabalho do OMS que são vinculados no mesmo grupo de recursos e região. A integração com o OMS também inclui o benefício do uso do Log Analytics para monitorar e analisar os fluxos de trabalho e o status do trabalho de runbook ao longo do tempo. Você também pode usar os recursos avançados no Log Analytics para escalar ou investigar problemas. A oferta também implanta as soluções de **Controle de Alterações** e **Gerenciamento de Atualizações**, que são habilitadas por padrão. |
| Selecione **Automação** no Marketplace | Esse método cria uma conta de Automação do Azure em um grupo de recursos novo ou existente que não esteja vinculado a um espaço de trabalho do OMS. Ele não inclui nenhuma solução disponível da oferta de **Automação e Controle**. Esse método é uma configuração básica que apresenta a Automação do Azure a você. Ele pode ajudá-lo a aprender a gravar runbooks e configurações DSC, e aprender a usar os recursos do serviço. |
| Selecione soluções de **Gestão** | Se você selecionar uma solução **Gerenciamento**, incluindo [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md), [Iniciar/parar VMs durante horários inativos](automation-solution-vm-management.md) ou [Controle de Alterações](../log-analytics/log-analytics-change-tracking.md), a solução solicitará que você selecione uma conta de Automação do Azure existente e o espaço de trabalho do OMS. A solução oferece a opção de criar uma conta de Automação do Azure e espaço de trabalho do OMS conforme necessário para a solução ser implantada na sua assinatura. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Criar uma conta de Automação do Azure integrada com o OMS
A automação integrada é recomendável para que você selecione a oferta de **Automação e Controle** no Marketplace. Usae esse método cria uma conta de Automação do Azure e estabelece a integração com um espaço de trabalho do OMS. Quando você usa esse método, você também tem a opção de instalar as soluções de gerenciamento que estão disponíveis com a oferta.  

[Criar uma conta de automação independente](automation-create-standalone-account.md) orienta você pelo processo de criação de uma conta de automação e espaço de trabalho do OMS, integrando a oferta de **Automação e Controle**. Você pode aprender a criar uma conta de Automação do Azure independente para testar ou visualizar o serviço.  

Para criar uma conta de automação e espaço de trabalho do OMS usando o Marketplace de **Automação e Controle**, oferecendo:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores da assinatura e um coadministrador da assinatura.
2. Selecione **Novo**.<br><br> ![Selecione Novo no portal do Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Pesquise **Automação**. Nos resultados da pesquisa, selecione **Automação e Controle**.<br><br> ![Pesquisar e selecionar Automação e Controle no Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Examine a descrição para a oferta e, em seguida, selecione **Criar**.  
5. Em **Automação e controle**, selecione **Espaço de trabalho do OMS**. Em **Espaços de trabalho do OMS**, selecione um espaço de trabalho do OMS vinculado à assinatura do Azure onde a conta de automação esteja. Se você não tiver um espaço de trabalho do OMS, selecione **Criar novo espaço de trabalho**. Em **Espaço de trabalho do OMS**: 
  1. Para o **Espaço de trabalho do OMS**, insira um nome para o novo espaço de trabalho.
  2. Para **Assinatura**, selecione uma assinatura para vincular. Se a seleção padrão não for a assinatura que você deseja usar, selecione a assinatura na lista suspensa.
  3. Em **Grupo de Recursos**, você pode criar um grupo de recursos ou selecionar um grupo de recursos existente.  
  4. Para **Localização**, selecione uma região. Para saber mais, confira [em quais regiões a Automação do Azure está disponível](https://azure.microsoft.com/regions/services/). As soluções são oferecidas em duas camadas: gratuita e por nó (OMS). A camada gratuita tem um limite de quantidade de dados coletados diariamente, de período de retenção e de minutos de tempo de execução do trabalho de runbook. A camada por nó (OMS) não tem um limite de quantidade de dados coletados diariamente.  
  5. Selecione **Conta de Automação**.  Se você criar um novo espaço de trabalho do OMS, você também deve criar uma conta de Automação do Azure que esteja associada ao novo espaço de trabalho do OMS. Inclua sua assinatura do Azure, grupo de recursos e região. 
    1. Selecione **Criar uma conta de Automação do Azure**.
    2. Em **Conta de automação**, no campo **Nome**, digite o nome da conta de Automação.
    Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do OMS selecionado. Você não pode modificar essas opções. 
    3. Uma conta executar como do Azure é o método de autenticação padrão para a oferta. Depois de selecionar **OK**, as opções de configuração serão validadas e a conta de Automação será criada. Para acompanhar seu progresso, no menu, selecione **Notificações**. 
    4. Caso contrário, selecione uma conta Executar como de Automação existente. A conta selecionada não poderá já estar vinculada a outro espaço de trabalho do OMS. Caso esteja, será exibida uma mensagem de notificação. Se a conta já estiver vinculada a um espaço de trabalho do OMS, selecione uma conta de Automação do Azure Executar como diferente ou crie uma.
    5. Depois que você inserir ou selecionar as informações necessárias, selecione **Criar**. As informações serão verificadas e a conta de Automação do Azure e as contas Executar como são criadas. Você retorna automaticamente para o painel do **Espaço de trabalho do OMS**.  
6. Depois que você inserir ou selecionar as informações necessárias no painel **Espaço de trabalho do OMS**, selecione **Criar**.  As informações são verificadas e o espaço de trabalho é criado. Para acompanhar seu progresso, no menu, selecione **Notificações**. Você retornará para o painel **Adicionar Solução**.  
7. Em configurações de **Automação e Controle**, confirme que você deseja instalar as soluções recomendadas pré-selecionadas. Se você alterar qualquer uma das opções padrão, você pode instalar as soluções individualmente mais tarde.  
8. Para continuar com a Automação do Azure de integração e um espaço de trabalho do OMS, selecione **Criar**. Todas as configurações são validadas e, em seguida, o Azure tenta implantar a oferta em sua assinatura. Esse processo pode levar alguns segundos. Para acompanhar seu progresso, no menu, selecione **Notificações**. 

Depois de a oferta ser incorporada, você pode realizar as seguintes tarefas:
* Iniciar a criação de runbooks.
* Trabalhar com as soluções de gerenciamento que você habilitou.
* Implantar uma função do [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Começar a trabalhar com [Log Analytics](https://docs.microsoft.com/azure/log-analytics) para coletar os dados gerados pelos recursos em seu ambiente de nuvem ou local.   

## <a name="next-steps"></a>Próximas etapas
* Verificar se a sua nova conta de automação pode se autenticar com base nos recursos do Azure. Para saber mais, consulte [Testar a autenticação da conta Executar como de Automação do Azure](automation-verify-runas-authentication.md).
* Saiba como começar com a criação de runbooks e considerações relacionadas antes de você começar a criar. Para saber mais, veja [Tipos de runbook de automação](automation-runbook-types.md).


