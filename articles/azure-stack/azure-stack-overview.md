---
title: O que é o Azure Stack? | Microsoft Docs
description: Saiba como o Azure Stack permite que você execute serviços do Azure em seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631273"
---
# <a name="azure-stack-overview"></a>Visão geral do Azure Stack

O Azure Stack é uma extensão do Azure que fornece uma maneira de executar aplicativos em um ambiente local e entregar serviços do Azure em seu datacenter. Com uma plataforma de nuvem consistente, as empresas com confiança possam tomar decisões de tecnologia com base nos requisitos de negócios, em vez de decisões de negócios com base nas limitações de tecnologia.

## <a name="why-use-azure-stack"></a>Por que usar o Azure Stack?

O Azure fornece uma plataforma avançada para desenvolvedores criarem aplicativos modernos. No entanto, alguns aplicativos baseados em nuvem enfrentam obstáculos, como, normas, latência e conectividade intermitente. Azure e o Azure Stack desbloquear novos casos de uso de nuvem híbrida para voltados ao cliente e interno de linha de aplicativos de negócios:

- **Soluções desconectadas e de borda**. Atenda a latência e requisitos de conectividade processando dados localmente no Azure Stack e então agregando-lo no Azure para análise futura, a lógica de aplicativo comum entre ambos. Você ainda pode implantar o Azure Stack desconectado da internet sem conectividade para o Azure. Acho que de Chão de fábrica, o cruzeiro é fornecido e poços mina como exemplos.

- **Aplicativos de nuvem que atendem a regulamentos variados**. Desenvolver e implantar aplicativos no Azure, com total flexibilidade para implantar localmente com o Azure Stack para atender às normas ou requisitos de diretiva, sem alterações de código necessárias. Exemplos de aplicativos incluem auditoria global, relatórios financeiros, intercâmbio comerciais, jogos online e relatório de despesas.

- **Modelo de aplicativo de nuvem local**. Use os serviços do Azure, contêineres, sem servidor e arquiteturas de microsserviço para atualizar e estender aplicativos existentes ou criar novos. Os processos do DevOps consistente de uso em Azure na nuvem e o Azure Stack local para acelerar a modernização de aplicativos para aplicativos de missão crítica de núcleo.

O Azure Stack permite que esses cenários de uso, fornecendo:

- Uma experiência de entrega integrada para colocar em funcionamento rapidamente com o Azure Stack especialmente concebidos sistemas integrados do parceiros de hardware confiável. Após a entrega, pilha do Azure se integra facilmente ao data center com o monitoramento por meio da extensão do pacote de gerenciamento do System Center Operations Manager ou do Nagios.

- Gerenciamento de identidade flexíveis usando o Azure Active Directory (Azure AD) para os ambientes híbridos do Azure e o Azure Stack e aproveitando os serviços de Federação do Active Directory (AD FS) para desconectado implantações. 

- Abordagens de um ambiente de desenvolvimento de aplicativo consistente do Azure para maximizar a produtividade dos desenvolvedores e habilite o DevOps comuns entre ambientes híbridos.

- Entrega de serviços do Azure do local usando o poder de computação em nuvem híbrida. Adote práticas operacionais comuns no Azure e o Azure Stack para implantar e operar serviços do Azure IaaS e PaaS usando as mesmas experiências administrativas e as ferramentas do Azure. A Microsoft fornece a inovação contínua do Azure para o Azure Stack, incluindo novos serviços do Azure, as atualizações para os serviços existentes e aplicativos do Azure Marketplace e imagens adicionais.

## <a name="azure-stack-architecture"></a>Arquitetura de pilha do Azure
O Azure Stack, sistemas integrados são compostos em racks de servidores de 4 a 16 criados por parceiros de hardware confiáveis e entregue diretamente em seu datacenter. Após a entrega, um provedor de solução funcionará com você para implantar o sistema integrado e verifique se que a solução do Azure Stack atende aos seus requisitos de negócios. Você precisará preparar seu datacenter, garantindo a todos os itens necessários de energia e resfriamento, conectividade de borda e outro requisito de integração do datacenter necessários estão em vigor. 

> Para obter mais informações sobre a experiência de integração do datacenter do Azure Stack, consulte [integração de datacenter do Azure Stack](azure-stack-customer-journey.md).

O Azure Stack baseia-se no hardware padrão do setor e é gerenciado usando as mesmas ferramentas que você já usa para gerenciar assinaturas do Azure. Como resultado, você pode aplicar os processos de DevOps consistentes, se você estiver conectado ao Azure ou não. 

Arquitetura do Azure Stack permite que você forneça serviços do Azure na borda para locais remotos ou conectividade intermitente, desconectado da internet. Você pode criar soluções híbridas que processam dados localmente no Azure Stack e agregação-lo no Azure para análise e processamento adicional. Por fim, pois ele está instalado no local, você pode atender a requisitos específicos de normas ou de política com a flexibilidade de implantar o aplicativo de nuvem no local sem alterar nenhum código. 

## <a name="deployment-options"></a>Opções de implantação

### <a name="production-or-evaluation-environments"></a>Ambientes de produção ou de avaliação
O Azure Stack é oferecido em duas opções de implantação para atender às suas necessidades, sistemas integrados do Azure Stack para uso em produção e o Azure Stack desenvolvimento ASDK (Kit) para avaliar o Azure Stack:

- **Sistemas integrados do Azure Stack**. O Azure Stack, sistemas integrados são oferecidos por meio de uma parceria de parceiros da Microsoft e de hardware, criando uma solução que oferece inovação conduzida a nuvem e a simplicidade de gerenciamento de computação. Como o Azure Stack é oferecido como um sistema integrado de hardware e software, tem a flexibilidade e controle de que precisa, bem como a capacidade de inovar a partir da nuvem. Sistemas de pilha integrado do Azure variam de tamanho de 4 a 16 nós e têm suporte em conjunto pela Microsoft e parceiros de hardware. Use os sistemas integrados do Azure Stack para criar novos cenários e implantar novas soluções para suas cargas de trabalho de produção.

- **Kit de Desenvolvimento do Azure Stack**. O [Kit de desenvolvimento na pilha do Azure (ASDK)](./asdk/asdk-what-is.md) é uma implantação gratuita e de nó único do Azure Stack que você pode usar para avaliar e saber mais sobre o Azure Stack. Você também pode usar o ASDK como um ambiente de desenvolvedor para criar aplicativos usando as APIs e ferramentas que é consistente com o Azure. No entanto, o ASDK não se destina a ser usado como um ambiente de produção e tem as seguintes limitações em comparação com a implantação de produção completa de sistemas integrados:

    - O ASDK só pode ser associado com um único do Azure AD (Active Directory do Azure) ou o provedor de identidade do Active Directory Federation Services (AD FS).
    - Como os componentes do Azure Stack são implantados em um único computador host, recursos físicos limitados estão disponíveis para recursos de locatário. Essa configuração não se destina a escala ou para avaliação de desempenho.
    - Os cenários de redes estão limitados devido a requisitos de implantação NIC e de anfitrião único.

### <a name="connection-models"></a>Modelos de Conexão
Você pode optar por implantar Azure Stack **conectados** com a internet (e para o Azure) ou **desconectado** dele. Essa opção define quais opções estão disponíveis para seu armazenamento de identidade (Azure AD ou AD FS) e o modelo de cobrança (como você com base no uso de pagamento cobrança ou baseada em capacidade cobrança).

> Para obter mais informações, consulte as considerações para [conectados](azure-stack-connected-deployment.md) e [desconectado](azure-stack-disconnected-deployment.md) modelos de implantação. 

### <a name="identity-provider"></a>Provedor de identidade 
O Azure Stack usa Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) como um provedor de identidade para estabelecer identidades do Azure Stack. 

> [!IMPORTANT]
> Esse é um ponto de decisão fundamental! Escolhendo o Azure AD ou AD FS como seu provedor de identidade é uma decisão única que você precisa fazer no momento da implantação. Você não pode alterar isso posteriormente sem reimplantar todo o sistema.

Azure AD é o provedor de identidade multilocatário, baseados em nuvem da Microsoft. A maioria dos cenários híbridos com implantações conectado à internet usam o Azure AD como o repositório de identidades. No entanto, você pode optar por usar serviços de Federação do Active Directory (AD FS) para implantações desconectadas do Azure Stack. Provedores de recursos do Azure Stack e outros aplicativos, funcionam da mesma forma com o AD FS que funcionam com o Azure AD. O Azure Stack inclui sua própria instância do Active Directory e uma API do Graph do Active Directory. 

> Você pode aprender mais sobre as considerações de identidade do Azure Stack no [visão geral da identidade para o Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Como o Azure Stack é gerenciado?
Após a implantação do Azure Stack em uma implantação de sistemas integrados ou uma instalação ASDK, os principais métodos de interação com o Azure Stack são o portal de administração, o portal do usuário e o PowerShell. Cada um dos portais do Azure Stack são apoiados por instâncias separadas do Azure Resource Manager. Uma **operador do Azure Stack** usa o portal de administração para gerenciar o Azure Stack e para fazer coisas como criar ofertas de locatário e manter o status de integridade e o monitor do sistema integrado. O portal do usuário (também conhecido como portal de locatário) fornece uma experiência de autoatendimento para o consumo de recursos de nuvem, como máquinas virtuais, contas de armazenamento e aplicativos web. 

> Para obter mais informações sobre como gerenciar o Azure Stack usando o portal de administração, consulte o uso de [quickstart de portal de administração do Azure Stack](azure-stack-manage-portals.md).

Como um operador de pilha do Azure, você pode fornecer uma ampla variedade de aplicativos e serviços, tais como [máquinas virtuais](azure-stack-tutorial-tenant-vm.md), [aplicativos da web](azure-stack-app-service-overview.md)altamente disponível [SQL Server](azure-stack-tutorial-sql.md), e [MySQL Server](azure-stack-tutorial-mysql.md) bancos de dados. Você também pode usar [modelos de Azure Resource Manager de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) para implantar o SharePoint, Exchange e muito mais. 

Usando o portal de administração, você pode [configurar o Azure Stack para oferecer serviços](azure-stack-plan-offer-quota-overview.md) aos locatários usando cotas, planos, ofertas e assinaturas. Os usuários de locatários podem assinar várias ofertas. As ofertas podem ter um ou mais planos, e os planos podem ter um ou mais serviços. Operadores também gerenciar a capacidade e respondem aos alertas. 

Quando o Azure Stack é configurado, uma **usuário do Azure Stack** (também conhecido como um locatário) consome serviços que oferece o operador. Os usuários podem provisionar, monitorar e gerenciar os serviços assinados por eles, como aplicativos web, armazenamento e máquinas virtuais.

> Para saber mais sobre como gerenciar o Azure Stack, incluindo quais contas usar where, as responsabilidades de operador típicas, o que dizer a seus usuários e como obter ajuda, examine [Noções básicas de administração do Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Provedores de recursos 
Provedores de recursos são serviços web que formam a base para todos os Azure IaaS de pilha e os serviços de PaaS. O Azure Resource Manager se baseia em diferentes provedores de recursos para fornecer acesso a serviços. Cada provedor de recursos ajuda você a configurar e controlar seus respectivos recursos. Os administradores de serviço também podem adicionar novos provedores de recurso personalizado. 

### <a name="foundational-resource-providers"></a>Provedores de recursos fundamentais 
Há três provedores de recursos de IaaS fundamentais: Computação, rede e armazenamento:

- **Computação**. O provedor de recursos de computação permite que os locatários do Azure Stack criem suas próprias máquinas virtuais. O provedor de recursos de computação inclui a capacidade de criar máquinas virtuais, bem como extensões de máquina Virtual. O serviço de extensão de Máquina Virtual ajuda a fornecer recursos de IaaS para máquinas virtuais Windows e Linux.  Por exemplo, você pode usar o provedor de recursos de computação para provisionar uma máquina virtual Linux e executar scripts de Bash durante a implantação para configurar a VM.
- **Provedor de recursos de rede**. O provedor de recursos de rede oferece uma série de recursos de rede função NFV (virtualização) e de Software Defined Networking (SDN) para a nuvem privada. Você pode usar o provedor de recursos de rede para criar recursos como balanceadores de carga de software, IPs públicos, grupos de segurança de rede e redes virtuais.
- **Provedor de recursos de armazenamento**. O provedor de recursos de armazenamento oferece quatro serviços de armazenamento consistente do Azure: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [fila](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabela](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)e gerenciamento de conta do Cofre de chaves fornecendo gerenciamento e auditoria de segredos, como senhas e certificados. O provedor de recursos de armazenamento também oferece um serviço de administração de nuvem de armazenamento para facilitar a administração de provedor de serviço dos serviços de armazenamento consistente do Azure. O Armazenamento do Azure fornece a flexibilidade para armazenar e recuperar grandes quantidades de dados não estruturados, como documentos e arquivos de mídia, com os Blobs do Azure, e dados estruturados baseados em NoSQL com as Tabelas do Azure. 

### <a name="optional-resource-providers"></a>Provedores de recursos opcionais
Há três opcionais provedores de recursos de PaaS que você pode implantar e usar com o Azure Stack: Provedores de recursos do serviço de aplicativo, o SQL Server e o servidor MySQL:

- **O serviço de aplicativo**. [Serviço de aplicativo do Azure no Azure Stack](azure-stack-app-service-overview.md) é uma oferta de plataforma-como um serviço (PaaS) do Microsoft Azure disponíveis para o Azure Stack. O serviço permite aos clientes internos ou externos criar a web, API e o Azure Functions aplicativos para qualquer plataforma ou dispositivo. 
- **SQL Server**. Use o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider.md) para oferecer bancos de dados SQL como um serviço do Azure Stack. Depois de instalar o provedor de recursos e conectá-lo a uma ou mais instâncias do SQL Server, você e seus usuários podem criar bancos de dados para aplicativos nativos de nuvem, sites que usam o SQL e outras cargas de trabalho que usam SQL.
- **Servidor MySQL**. Use o [provedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) para expor bancos de dados MySQL como um serviço do Azure Stack. O provedor de recursos do MySQL é executado como um serviço em uma máquina virtual do Server Core do Windows Server 2016 (VM).

## <a name="providing-high-availability"></a>Fornecendo alta disponibilidade
Para obter alta disponibilidade de um sistema de produção de várias VMs no Azure, as VMs são colocadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que distribui-los em vários domínios de falha e domínios de atualização. Em menor escala do Azure Stack, um domínio de falha em um conjunto de disponibilidade é definido como um único nó na unidade de escala.  

Enquanto a infraestrutura do Azure Stack já é resistente a falhas, a tecnologia subjacente (clustering de failover) ainda incorrerá em algum tempo de inatividade para as VMs em um servidor físico afetado se houver uma falha de hardware. O Azure Stack oferece suporte a uma conjunto de disponibilidade com um máximo de três domínios de falha para ser consistente com o Azure.

- **Domínios de falha**. As VMs colocadas em um conjunto de disponibilidade será fisicamente isoladas uns dos outros, distribuindo-los de maneira mais uniforme possível em vários domínios de falha (nós do Azure Stack). Se houver uma falha de hardware, as VMs do domínio de falha com falha serão ser reiniciadas em outros domínios de falha, mas, se possível, mantidas em domínios de falha separados de outras VMs no mesmo conjunto de disponibilidade. Quando o hardware fica online novamente, as VMs serão redistribuídas para manter a alta disponibilidade. 
 
- **Domínios de atualização**. Domínios de atualização são outro conceito do Azure que fornece alta disponibilidade em conjuntos de disponibilidade. Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ao mesmo tempo. As VMs localizadas no mesmo domínio de atualização serão reiniciadas juntos durante a manutenção planejada. Conforme locatários cria VMs dentro de um conjunto de disponibilidade, a plataforma do Azure distribui automaticamente as VMs entre esses domínios de atualização. No Azure Stack, VMs estão ao vivo migrados entre os hosts online no cluster antes de seu host subjacente é atualizado. Como não há nenhum tempo de inatividade de locatário durante uma atualização do host, o recurso de domínio de atualização no Azure Stack existe somente para compatibilidade de modelo com o Azure. 

## <a name="role-based-access-control"></a>Controle de acesso baseado em função
Você pode usar o controle de acesso com base da função (RBAC) para conceder acesso de sistema para serviços, grupos e usuários autorizados, atribuindo-lhes funções em nível de recurso individual, grupo de recursos ou assinatura. Cada função define o nível de acesso que um usuário, grupo ou serviço tem em relação aos recursos do Microsoft Azure Stack.

O RBAC do Azure Stack tem três funções básicas que se aplicam a todos os tipos de recursos: Proprietário, Colaborador e leitor. O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas. Leitor pode exibir apenas os recursos existentes. O restante das funções RBAC permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não permite o gerenciamento de rede virtual ou da sub-rede à qual a máquina virtual está conectada.

> Ver [controle de acesso baseado em função](azure-stack-manage-permissions.md) para obter mais informações. 

## <a name="reporting-usage-data"></a>Relatar dados de uso
O Microsoft Azure Stack coleta e agrega dados de uso em todos os provedores de recursos e transmite para o Azure para o processamento de comércio do Azure. Os dados de uso coletados no Azure Stack podem ser exibidos por meio da API REST. Há uma API de Locatário consistente do Azure, bem como APIs de Provedor e Provedor Delegado para obter dados de uso de todas as assinaturas de locatários. Esses dados podem ser usados para integrá-los a uma ferramenta externa ou um serviço de cobrança ou estorno. Depois que o uso foi processado pelo comércio do Azure, ela pode ser exibida no portal de cobrança do Azure.

> Saiba mais sobre [reportando dados de uso do Azure Stack para Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Próximas etapas

[Comparar o Azure Stack e o Azure global](compare-azure-azure-stack.md)

[Noções básicas de administração](azure-stack-manage-basics.md)

[Início rápido: usar o portal de administração do Azure Stack](azure-stack-manage-portals.md)