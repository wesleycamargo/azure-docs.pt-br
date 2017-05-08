--- 
title: "Introdução à Automação do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral do serviço de automação do Azure, revisando os conceitos centrais e detalhes de implementação na preparação para integrar a oferta do Azure Marketplace."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 8a04fda8eaf6e14a278941e7bb55b23012f67850
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---

## <a name="getting-started-with-azure-automation"></a>Introdução à Automação do Azure

Este guia de Introdução apresenta os conceitos principais relacionados à implantação da Automação do Azure. Se você for novo na automação no Azure ou tem experiência com softwares de automação de fluxo de trabalho como o System Center Orchestrator, este guia ajuda você com uma introdução aos conceitos e detalhes de implantação. 

## <a name="key-concepts"></a>Principais conceitos

### <a name="automation-service"></a>Serviço de automação
A automação é um serviço do Azure que usa tecnologias do Azure e do Windows PowerShell para ajudá-lo a controlar o gerenciamento do Azure, da nuvem e de aplicativos e infraestrutura local.  A automação do Azure ajuda você a gerenciar o ciclo de vida completo de seus serviços e aplicativos com implantação automatizada usando a automação do processo, configuração do sistema operacional usando o PowerShell Desired State Configuration, atualização e monitoramento contínuos com o gerenciamento de atualizações e controle de alterações e solução de problemas e correções automatizados.

### <a name="automation-account"></a>Conta de automação
Uma conta de automação é um recurso do Azure que você cria.  Você pode gerenciar o Azure, a nuvem e os recursos locais com uma única conta de automação.  Uma conta de automação é um contêiner para os itens que você precisa fazer sua automação de trabalho: são runbooks, módulos, ativos como credenciais, agendamentos e configurações. Você pode usar várias contas de automação para separar os recursos em ambientes lógicos separados, como desenvolvimento, teste e produção ou em regiões geográficas.  

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Para executar runbooks em sistemas físicos ou virtuais em seu data center local, Azure ou outro provedor de nuvem, o recurso Hybrid Runbook Worker permite acessar e gerenciar esses recursos locais.     

### <a name="automation-desired-state-configuration"></a>Configuração de Estado Desejado de Automação
Automation Desired State Configuration (DSC), baseada no PowerShell DSC, configura, monitora e atualiza automaticamente o estado desejado de seus sistemas operacionais hospedados no Azure, no local, ou em outra nuvem.  

### <a name="management-solutions"></a>Soluções de gerenciamento
Soluções de gerenciamento, como gerenciamento de atualizações e controle de alterações, estendem a funcionalidade de automação do Azure e são usadas com o Log Analytics.  Eles podem incluir vários recursos que dão suporte a um cenário de gerenciamento específico, como runbooks de automação, alertas e consultas de pesquisa de Log Analytics predefinidas e visualizações.  

## <a name="architecture-overview"></a>Visão geral da arquitetura

A automação do Azure é um software como um aplicativo de serviço (SaaS) que fornece um ambiente de multilocatários escalonável e confiável para automação de processos com runbooks e gerenciar alterações de configuração nos sistemas Windows e Linux usando a Desired State Configuration (DSC) no Azure, em outros serviços de nuvem ou localmente. Entidades contidas dentro de sua conta de automação, como runbooks, ativos, contas executar como são isoladas de outras contas de automação em sua assinatura e outras assinaturas.  

Runbooks que são executados no Azure são executados em áreas restritas de automação, que são hospedadas na plataforma do Azure como uma máquina virtual de serviço (PaaS).  Áreas restritas de automação fornecem isolamento de locatários para todos os aspectos de execução de runbook – módulos, armazenamento, memória, comunicação de rede, fluxos de trabalho etc. Essa função é gerenciada pelo serviço e não é acessível de sua conta do Azure ou conta de automação do Azure para você controlar.         

Para automatizar a implantação e o gerenciamento de recursos em seu data center local ou outros serviços de nuvem, você pode designar um ou mais computadores para executar a função de Hybrid Runbook Worker (HRW).  Cada HRW requer que o agente de gerenciamento Microsoft (MMA) com uma conexão a um espaço de trabalho do Log Analytics e uma conta de automação.  O Log Analytics é usado para inicializar a instalação, manter o agente MMA e monitorar a funcionalidade do HRW.  A entrega de runbooks e as instruções para executá-los são desempenhadas pela Automação do Azure.

Você pode implantar vários HRW para fornecer alta disponibilidade para seus runbooks, trabalhos de runbook de balanceamento de carga e em alguns casos dedicá-los para ambientes e cargas de trabalho específicos.  O HRW se comunica com o serviço de automação pela porta de saída TCP 443.  Quando tiver runbook em execução em um HRW em seu data center, e você desejar que o runbook execute tarefas de gerenciamento em relação a outros computadores ou serviços dentro do datacenter, pode haver outras portas que o runbook precisa acessar.  Se suas políticas de segurança de TI não permitem que os computadores em sua rede se conectem com a Internet, confira o artigo [Gateway OMS](../log-analytics/log-analytics-oms-gateway.md), que atua como um proxy para o HRW coletar o status do trabalho e receber informações de configuração de sua conta de automação.

Runbooks em execução em um HRW são executados no contexto da conta do sistema local no computador, que é o contexto de segurança recomendado ao executar ações administrativas no computador Windows local. Se você deseja que o runbook execute tarefas em recursos fora do computador local, você precisa definir ativos de credencial segura na conta de automação que você pode acessar do runbook e usar para autenticar com o recurso externo. Você pode usar ativos de [Credencial](automation-credentials.md), [Certificado](automation-certificates.md) e [Conexão](automation-connections.md) no runbook com cmdlets que permitem a especificação das credenciais para que possa autenticá-las.

Configurações DSC armazenadas na Automação do Azure podem ser aplicadas diretamente às máquinas virtuais do Azure. Outras máquinas virtuais e físicas podem solicitar configurações do servidor de recepção de DSC de Automação do Azure.  Para gerenciar configurações de seus sistemas Windows e Linux virtuais ou físicos, você não precisa implantar qualquer infraestrutura para dar suporte a servidor de pull de DSC da automação, apenas acesso à Internet de saída em cada sistema a ser gerenciado pela DSC de automação, comunicação pela porta TCP 443 para o serviço OMS.   

![Visão geral da Automação do Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="automation-dsc"></a>DSC de automação
A DSC de automação do Azure pode ser usada para gerenciar várias máquinas:

* Máquinas virtuais do Azure (clássico) executando Windows ou Linux
* Máquinas virtuais do Azure executando Windows ou Linux
* Máquinas virtuais da Amazon Web Services (AWS) executando Windows ou Linux
* Máquinas locais físicas/virtuais do Windows, ou em uma nuvem diferente do Azure/AWS
* Máquinas locais físicas/virtuais do Linux, ou em uma nuvem diferente do Azure/AWS

A última versão do WMF 5 deve ser instalada para que o agente da DSC do PowerShell para Windows possa se comunicar com a Automação do Azure. A versão mais recente do [agente DSC do PowerShell para Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) deve ser instalada para que o Linux possa se comunicar com a Automação do Azure.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Ao designar um computador para executar trabalhos de Hybrid Runbook, esse computador deve ter o seguinte:

* Windows Server 2012 ou posterior
* Windows PowerShell 4.0 ou posterior.  Recomendamos instalar o Windows PowerShell 5.0 no computador para ter uma maior confiabilidade. Você pode baixar a nova versão no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=50395)
* Mínimo de dois núcleos
* Mínimo de 4 GB de RAM

## <a name="security"></a>Segurança
A Automação do Azure permite automatizar tarefas em relação a recursos no Azure, locais e com outros provedores de nuvem.  Para que um runbook execute as ações necessárias, ele deve ter permissões para acessar os recursos com segurança e apenas com os direitos mínimos necessários na assinatura.  

### <a name="automation-account"></a>Conta de Automação 
Todas as tarefas de automação que você executa contra recursos usando os cmdlets do Azure na automação do Azure se autenticam no Azure usando a autenticação baseada em credenciais de identidade organizacional do Azure Active Directory.  Uma conta de automação é separada da conta usada para entrar no portal para configurar e usar os recursos do Azure.  

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas as contas de Automação podem gerenciar todos os recursos em sua assinatura. Crie contas de Automação em regiões diferentes se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

> [!NOTE]
> As contas de Automação e os recursos que eles contêm, que são criados no portal do Azure, não podem ser acessados no portal clássico do Azure. Se quiser gerenciar essas contas ou os recursos com o Windows PowerShell, você deve usar os módulos do Azure Resource Manager.
> 

Ao criar uma conta de Automação no portal do Azure, você cria automaticamente duas entidades:

* Uma conta Executar como. Essa conta cria uma entidade de serviço no Azure AD (Azure Active Directory) e um certificado. Também atribui o RBAC (controle de acesso baseado em função) de Colaborador, que gerencia os recursos do Resource Manager por meio de runbooks.
* Uma conta Executar como clássica. Essa conta carrega um certificado de gerenciamento, que é usado para gerenciar o gerenciamento de recursos clássicos usando o runbooks.

O controle de acesso baseado em função está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de usuário do Azure AD e uma conta Executar como e autenticar essa entidade de serviço.  Leia o [artigo Controle de acesso baseado em função da Automação do Azure](automation-role-based-access-control.md) para obter mais informações que o ajudarão a desenvolver seu modelo para gerenciar permissões de Automação.  

#### <a name="authentication-methods"></a>Métodos de autenticação
A tabela a seguir resume os métodos de autenticação diferentes para cada ambiente com suporte da automação do Azure.

| Método | Ambiente 
| --- | --- | 
| Contas Executar como do Azure e Executar como clássica |Implantação clássica do Azure de Azure Resource Manager |  
| Conta de Usuário do Azure AD |Implantação clássica do Azure de Azure Resource Manager |  
| Autenticação do Windows |Data center local ou outro provedor de nuvem usando o Hybrid Runbook Worker |  
| Credenciais do AWS |Amazon Web Services |  

A seção **Como\Autenticação e segurança**, oferece suporte a artigos fornecendo etapas de visão geral e implementação para configurar a autenticação para esses ambientes, seja com uma conta existente ou nova dedicar para esse ambiente.  Para a conta Executar como do Azure e Executar como clássica, o tópico [Atualização da conta Executar como de Automação usando o PowerShell](automation-update-account-powershell.md) descreve como atualizar sua conta de automação existente com as contas executar como usando o PowerShell se ele não foi originalmente configurado com uma conta Executar como ou Executar como clássica.   
 
## <a name="network"></a>Rede
Para que o Hybrid Runbook Worker local se conecte e se registre no serviço OMS (Microsoft Operations Management Suite), ele deve ter acesso ao número da porta e às URLs descritas abaixo.  Isso é um adendo às [portas e URLs necessárias para que o Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) se conecte ao OMS. Se você usar um servidor proxy para comunicação entre o agente e o serviço do OMS, será necessário garantir que os recursos apropriados estejam acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso.

As informações a seguir listam a porta e as URLs que são necessárias para que o Hybrid Runbook Worker se comunique com a Automação.

* Porta: somente a TCP 443 é necessária para acesso de Internet de saída
* URL global: *.azure-automation.net

Se você tem uma conta de Automação definida para uma região específica e deseja restringir a comunicação com o data center regional, a tabela a seguir fornece o registro de DNS para cada região.

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

Para obter uma lista de endereços IP em vez de nomes, baixe e leia o arquivo xml [Endereço IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) no Centro de Download da Microsoft. 

> [!NOTE]
> Esse arquivo contém intervalos de endereços IP (inclusive intervalos de Computação, SQL e Armazenamento) usados em Datacenters do Microsoft Azure. Um arquivo atualizado é postado semanalmente que reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não serão usados em datacenters por pelo menos uma semana. Baixe o novo arquivo xml semanalmente e faça as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Express Route podem observar esse arquivo usado para atualizar o anúncio de BGP de espaço do Azure na primeira semana de cada mês. 
> 


## <a name="implementation"></a>Implementação

### <a name="creating-an-automation-account"></a>Criação de uma conta de automação

Há diferentes maneiras de criar uma conta de automação no portal do Azure.  A tabela a seguir apresenta cada tipo de experiência de implantação e as diferenças entre eles.  

|Método | Descrição |
|-------|-------------|
| Selecione Automação e controle no Marketplace | Uma oferta, que cria uma conta de automação e um espaço de trabalho do OMS vinculados uns aos outros no mesmo grupo de recursos e região.  Ele também implanta as soluções de gerenciamento de atualizações e controle de alterações, que são habilitadas por padrão. |
| Selecione a automação do Marketplace | Cria uma conta de automação em um grupo de recursos novos ou existentes que não está vinculada a um espaço de trabalho do OMS e não inclui todas as soluções disponíveis da oferta de Automação e controle. Isso é uma configuração básica que apresenta a automação e pode ajudá-lo a aprender a escrever runbooks, configurações da DSC e usar os recursos do serviço. |
| Soluções de gerenciamento selecionadas | Se você selecionar uma solução **[Gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md)**, **[iniciar/parar VMs durante as horas sem trabalho](automation-solution-vm-management.md)** ou **[Controle de alterações](../log-analytics/log-analytics-change-tracking.md)** ela solicitará que você selecione um espaço de trabalho de automação e OMS existentes ou oferecerá a você a opção para criar os dois conforme necessário para a solução a ser implantada na sua assinatura. |

Este tópico explica como criar um espaço de trabalho do OMS e uma conta de automação ao integrar a oferta de Automação e controle.  Para criar um conta autônoma de automação de teste ou visualização do serviço, revise o seguinte artigo [Criação de conta autônoma de automação](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-log-analytics"></a>Criar conta de automação integrada com a Log Analytics
O método recomendado para a automação integrada é selecionando a oferta de Automação e controle no Marketplace.  Isso cria uma conta de automação e estabelece a integração com um espaço de trabalho do OMS, incluindo a opção de instalar as soluções de gerenciamento que estão disponíveis com a oferta.  

>[!NOTE]
>Para criar uma conta de Automação, você deve ser membro da função de Administradores de Serviço ou coadministrador da assinatura que está concedendo acesso à assinatura. Você também deve ser adicionado como um usuário a instância da assinatura padrão do Active Directory. A conta não precisa ser atribuída a uma função com privilégios.
>
>Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionados à função de coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Nesse caso, você receberá um aviso "Você não tem permissões para criar..." na folha **Adicionar Conta de Automação**.
>
>Os usuários adicionados à função de coadministrador primeiro podem ser removidos das assinaturas da instância do Active Directory e adicionados novamente para torná-los Usuários completos no Active Directory. Para verificar essa situação no painel **Azure Active Directory** no portal do Azure, selecione **Usuários e grupos**, selecione **Todos os usuários** e, depois de selecionar o usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** sob o perfil de usuários não deve ser igual a **Convidado**.

1. Conecte-se no Portal do Azure com uma conta que seja membro da função Administradores da Assinatura e coadministradora da assinatura.

2. Clique em **Novo**.<br><br> ![Selecione a opção Novo no portal do Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Procure **Automação** e, em seguida, nos resultados da pesquisa selecione **Automação e controle***.<br><br> ![Pesquise e selecione Automação e controle no Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Depois de ler a descrição para a oferta, clique em **Criar**.  

5. Na folha Configurações **Automação e controle**, selecione **espaço de trabalho do OMS**.  Na folha **Espaços de Trabalho do OMS**, selecione um espaço de trabalho do OMS vinculado à mesma assinatura do Azure que a conta de automação está em ou crie um espaço de trabalho do OMS.  Se você não tiver um espaço de trabalho do OMS, selecione **Criar Novo Espaço de Trabalho** e, na folha **Espaço de Trabalho do OMS**, faça o seguinte: 
   - Especifique um nome para o novo **Espaço de Trabalho do OMS**.
   - Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
   - Em **Grupo de Recursos**, você pode selecionar um grupo de recursos existente ou criar um novo.  
   - Selecione um **Local**.  No momento, os únicos locais disponíveis **Sudeste da Austrália**, **Leste dos EUA**, **Sudeste Asiático**, **Centro Oeste dos EUA** e **Europa Ocidental**.
   - Selecione um **tipo de preço**.  A solução é oferecida em duas camadas: gratuito e camada por nó (OMS).  A camada gratuita tem um limite de quantidade de dados coletados diariamente, de período de retenção e de minutos de tempo de execução do trabalho de runbook.  A camada por nó do OMS não tem um limite de quantidade de dados coletados diariamente.  
   - Selecione **Conta de Automação**.  Se você estiver criando um novo espaço de trabalho do OMS, será necessário criar uma nova conta de Automação que será associada ao novo espaço de trabalho do OMS especificado anteriormente, incluindo a assinatura do Azure, o grupo de recursos e a região.  Selecione **Criar uma conta de Automação** e, na folha **Adicionar Conta de Automação**, forneça o seguinte: 
  - No campo **Nome**, digite o nome da conta de Automação.

    Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do OMS selecionado, e essas opções não podem ser modificadas.  Uma conta executar como do Azure é o método de autenticação padrão para a oferta.  Após clicar em **OK**, as opções de configuração são validadas e a conta de Automação é criada.  Você pode acompanhar o progresso em **Notificações** no menu. 

    Caso contrário, selecione uma conta Executar como de Automação existente.  A conta selecionada já não pode ser vinculada a outro espaço de trabalho do OMS, caso contrário, uma mensagem de notificação é apresentada na folha.  Se já estiver vinculada, você precisará selecionar uma conta Executar como de Automação diferente ou criar uma nova.

    Depois de concluir as informações necessárias, clique em **Criar**.  As informações serão verificadas e a conta de automação e as contas executar como e são criadas.  Você retorna para a folha do **espaço de trabalho do OMS** automaticamente.  

6. Depois de fornecer as informações necessárias na folha **Espaço de trabalho do OMS**, clique em **Criar**.  Enquanto as informações são verificadas e o espaço de trabalho é criado, você pode acompanhar seu progresso no menu **Notificações**.  Você retornará para a folha **Adicionar Solução**.  

7. Na folha configurações **Automação e controle**, confirme que você deseja instalar as soluções pré-selecionadas recomendadas. Se você anular a seleção, você pode instalá-los individualmente mais tarde.  

8. Clique em **Criar** para prosseguir com a automação de integração e um espaço de trabalho do OMS. Todas as configurações são validadas e, em seguida, ele tenta implantar a oferta em sua assinatura.  Esse processo pode levar vários segundos e você pode acompanhar o progresso no menu **Notificações**. 

Depois que a oferta está incorporada, você pode começar a criar runbooks, trabalhar com as soluções de gerenciamento que você habilitou ou começar a trabalhar com o [Log Analytics](https://docs.microsoft.com/azure/log-analytics) para coletar dados gerados pelos recursos em seus ambientes de nuvem ou no local.   

## <a name="next-steps"></a>Próximas etapas
* Você pode confirmar que a sua nova conta de automação pode autenticar em recursos do Azure em [Como testar a autenticação da conta Executar como de Automação do Azure](automation-verify-runas-authentication.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para saber mais sobre a autorização gráfica, confira [Criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).


