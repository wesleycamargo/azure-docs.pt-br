---
title: Principais recursos e conceitos no Azure Stack | Microsoft Docs
description: Saiba mais sobre os principais recursos e conceitos do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 16c908dabd313cd9d64ce5be9b7d0299423a7675
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344678"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Principais recursos e conceitos do Azure Stack
Se ainda não estiver familiarizado com o Microsoft Azure Stack, esses termos e descrições de recursos poderão ser úteis.

## <a name="personas"></a>Personas
Há dois tipos de usuários do Microsoft Azure Stack, o operador de nuvem (provedor) e o locatário (consumidor).

* Um **operador de nuvem** pode configurar o Azure Stack e gerenciar ofertas, planos, serviços, cotas e preços para fornecer recursos para seus locatários.  Operadores de nuvem também gerenciar a capacidade e respondem aos alertas.  
* Um **locatário** (também conhecido como um usuário) consome serviços que oferece o administrador da nuvem. Os locatários podem provisionar, monitorar e gerenciar os serviços assinados por eles, como Aplicativos Web, Armazenamento e Máquinas Virtuais.

## <a name="portal"></a>Portal
Os principais métodos de interação com o Microsoft Azure Stack são o portal do administrador, o portal do usuário e o PowerShell.

Cada um dos portais do Azure Stack são apoiados por instâncias separadas do Azure Resource Manager.  Um operador de nuvem usa o portal do administrador para gerenciar o Azure Stack e fazer coisas como criar ofertas de locatário.  O portal do usuário (também conhecido como portal de locatário) fornece uma experiência de autoatendimento para o consumo de recursos de nuvem, como máquinas virtuais, contas de armazenamento e aplicativos Web. Para obter mais informações, consulte [usando os portais de administrador e usuário do Azure Stack](azure-stack-manage-portals.md).

## <a name="identity"></a>Identidade 
Pilha do Azure usa o Azure Active Directory (AAD) ou os serviços de Federação do Active Directory (AD FS) como um provedor de identidade.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory é um provedor de identidade multilocatário, baseados em nuvem da Microsoft.  A maioria dos cenários híbridos usar Azure Active Directory como o repositório de identidades.

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory (AD FS)
Você pode optar por usar serviços de Federação do Active Directory (AD FS) para implantações desconectadas do Azure Stack.  O Azure Stack, provedores de recursos e outros aplicativos funcionam da mesma forma com o AD FS que funcionam com o Azure Active Directory. O Azure Stack inclui sua própria instância do AD FS e o Active Directory e uma API do Graph do Active Directory. Kit de desenvolvimento do Azure Stack dá suporte aos seguintes cenários do AD FS:

- Entrar para a implantação por meio do AD FS.
- Criar uma máquina virtual com os segredos no cofre de chaves
- Criar um cofre para armazenar/acessar segredos
- Criar funções personalizadas RBAC na assinatura
- Atribuir usuários às funções RBAC em recursos
- Criar funções RBAC de todo o sistema por meio do PowerShell do Azure
- Entrar como um usuário por meio do PowerShell do Azure
- Criar serviço de entidades de usá-las para entrar no Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiões, serviços, planos, ofertas e assinaturas
No Azure Stack, os serviços são entregues aos locatários usando assinaturas, regiões, ofertas e planos. Os locatários podem assinar várias ofertas. As ofertas podem ter um ou mais planos, e os planos podem ter um ou mais serviços.

![](media/azure-stack-key-features/image4.png)

Hierarquia de exemplo das assinaturas de ofertas por um locatário, cada um com diferentes planos e serviços.

### <a name="regions"></a>Regiões
Regiões de pilha do Azure são um elemento básico de escala e gerenciamento. Uma organização pode ter várias regiões com os recursos disponíveis em cada região. Regiões também podem ter de ofertas de serviço diferentes disponíveis. No Kit de desenvolvimento do Azure Stack, apenas uma única região é suportada e é automaticamente denominada *local*.

### <a name="services"></a>Serviços
O Microsoft Azure Stack permite que os provedores forneçam uma ampla variedade de serviços e aplicativos, como máquinas virtuais do SQL Server bancos de dados, SharePoint, Exchange e muito mais.

### <a name="plans"></a>Planos
Os planos são agrupamentos de um ou mais serviços. Como provedor, é possível criar planos para oferecer a seus locatários. Por sua vez, os locatários assinam suas ofertas para usar os planos e os serviços incluídos nelas.

Cada serviço adicionado a um plano pode ser configurado com configurações de cota para ajudá-lo a gerenciar sua capacidade de nuvem. As cotas podem incluir restrições como limites de VM, RAM e CPU e são aplicadas por assinatura do usuário. As cotas podem ser diferenciadas por local. Por exemplo, um plano que contém os serviços de computação da região A poderia ter uma cota de duas máquinas virtuais, 4 GB de RAM e 10 núcleos da CPU.

Ao criar uma oferta, o administrador de serviços pode incluir um **plano base**. Esses planos base são incluídos por padrão quando um locatário assina essa oferta. Quando um usuário assina (e a assinatura é criada), o usuário tem acesso a todos os provedores de recursos especificados nesses planos base (com as cotas correspondentes).

O administrador de serviços também pode incluir **planos de complemento** em uma oferta. Planos de complemento não são incluídos por padrão na assinatura. Planos de complemento são planos adicionais (cotas) disponíveis em uma oferta que um proprietário de assinatura pode adicionar a suas assinaturas.

### <a name="offers"></a>Ofertas
Ofertas são grupos de um ou mais planos que os provedores apresentam aos locatários para compra (assinatura). Por exemplo, a oferta Alpha pode conter um plano de contendo um conjunto de serviços de computação e o plano B que contém um conjunto de serviços de armazenamento e rede.

Uma oferta vem com um conjunto de planos base, e os administradores de serviços podem criar planos de complemento que os locatários podem adicionar à sua assinatura.

### <a name="subscriptions"></a>Assinaturas
Uma assinatura é a forma como os locatários compram suas ofertas. Uma assinatura é uma combinação de um locatário com uma oferta. Um locatário pode ter assinaturas de várias ofertas. Cada assinatura se aplica apenas a uma oferta. As assinaturas de um locatário determinam quais planos/serviços eles podem acessar.

As assinaturas ajudam os provedores de organizar e acessar serviços e recursos de nuvem.

Para o administrador, uma assinatura de provedor padrão é criada durante a implantação. Essa assinatura pode ser usada para gerenciar o Azure Stack, implantar mais provedores de recursos e criar planos e ofertas para locatários. Ele não deve ser usado para executar aplicativos e cargas de trabalho do cliente. Começando com a versão 1804, duas assinaturas adicionais complementam a assinatura do provedor padrão; uma assinatura de medição e uma assinatura de consumo. Essas adições facilitam a separação do gerenciamento de infraestrutura principal, provedores de recursos adicionais e cargas de trabalho.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Usando o Azure Resource Manager, você pode trabalhar com os recursos de infraestrutura em um modelo declarativo com base no modelo.   Ele fornece uma interface única que você pode usar para implantar e gerenciar componentes de sua solução. Para obter mais informações e diretrizes, confira a [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupos de recursos
Grupos de recursos são coleções de recursos, serviços e aplicativos – e cada recurso tem um tipo, como máquinas virtuais, redes virtuais, IPs públicos, contas de armazenamento e sites. Cada recurso deve estar em um grupo de recursos e para grupos de recursos logicamente ajudar a organizar os recursos, como com carga de trabalho ou a localização.  No Microsoft Azure Stack, recursos como planos e ofertas também são gerenciados em grupos de recursos.

Diferentemente [Azure](../azure-resource-manager/resource-group-move-resources.md), não é possível mover recursos entre grupos de recursos. Quando você exibir as propriedades de um recurso ou grupo de recursos no portal de administração do Azure Stack, o *mover* botão está esmaecida e indisponível. 
 
### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Com o Azure Resource Manager, você pode criar um modelo (no formato JSON) que define a implantação e configuração do seu aplicativo. Esse modelo é conhecido como um modelo do Gerenciador de Recursos do Azure e fornece uma maneira declarativa de definir a implantação. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos são implantados em um estado consistente.

## <a name="resource-providers-rps"></a>Provedores de recursos (RPs)
Os provedores de recursos são serviços Web que formam a base de todos os serviços de IaaS e PaaS baseados no Azure. O Azure Resource Manager conta com RPs diferentes para fornecer acesso a serviços.

Há quatro RPs fundamentais: Cofre de chaves, armazenamento, computação e rede. Cada um desses RPs o ajuda a configurar e controlar seus respectivos recursos. Os administradores de serviço também podem adicionar novos provedores de recurso personalizado.

### <a name="compute-rp"></a>RP de Computação
O CRP (Provedor de Recursos de Computação) permite que os locatários do Azure Stack criem suas próprias máquinas virtuais. O CRP inclui a capacidade de criar máquinas virtuais, bem como extensões de Máquina Virtual. O serviço de extensão de Máquina Virtual ajuda a fornecer recursos de IaaS para máquinas virtuais Windows e Linux.  Por exemplo, você pode usar o CRP para provisionar uma máquina virtual Linux e executar scripts de Bash durante a implantação para configurar a VM.

### <a name="network-rp"></a>RP de Rede
O NRP (Provedor de Recursos de Rede) oferece uma série de recursos de SDN (Rede Definida pelo Software) e NFV (Virtualização de Função de Rede) para a nuvem privada.  Você pode usar o NRP para criar recursos como software de carga balanceadores, IPs públicos, grupos de segurança, redes virtuais.

### <a name="storage-rp"></a>RP de Armazenamento
O RP de armazenamento oferece quatro serviços de armazenamento consistente do Azure: blob, tabela, fila e gerenciamento de conta. Ele também oferece um serviço de administração de nuvem de armazenamento para facilitar a administração pelo provedor de serviços dos serviços de Armazenamento consistente do Azure. O Armazenamento do Azure fornece a flexibilidade para armazenar e recuperar grandes quantidades de dados não estruturados, como documentos e arquivos de mídia, com os Blobs do Azure, e dados estruturados baseados em NoSQL com as Tabelas do Azure. Para obter mais informações sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Armazenamento de blob
O armazenamento de blobs armazena qualquer conjunto de dados. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo. O Armazenamento de Tabela armazena conjuntos de dados estruturados. O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados. O Armazenamento de Fila fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.

Cada blob é organizado em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de 500 TB de capacidade da conta de armazenamento. O armazenamento de blobs oferece três tipos de blobs: blob de blocos, blob de anexo e blob de páginas (discos). Os blobs de blocos são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc. Blobs de anexo são semelhantes aos blobs de blocos, mas são otimizados para operações de anexo. Um blob de anexo pode ser atualizado apenas com a adição de um novo bloco no final. Acrescentar blobs é uma boa opção para cenários como registro em log, em que novos dados precisam ser gravados apenas até o fim do blob. Blobs de página são otimizados para representar discos de IaaS e aleatórias de suporte gravam e podem ter até 1 TB. Um disco de IaaS anexado a uma rede de máquinas virtuais do Azure é um VHD armazenado como um blob de página.

#### <a name="table-storage"></a>Armazenamento de tabela
O armazenamento de tabelas é um repositório de chave/atributo NoSQL da Microsoft, que tem um design sem esquemas, o que o torna diferente dos bancos de dados relacionais tradicionais. Como um repositório de dados não tem esquemas, é fácil adaptar seus dados conforme as mudanças nas necessidades de seu aplicativo. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente. O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada valor em uma tabela é armazenado com um nome de propriedade. Esse nome de propriedade pode ser usado para filtrar e especificar os critérios de seleção. Um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabelas não tem esquemas, duas entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes. Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

#### <a name="queue-storage"></a>Armazenamento de Filas
O armazenamento de filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O armazenamento de filas fornece mensagens assíncronas para a comunicação entre os componentes do aplicativo, estando eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O armazenamento de Fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

### <a name="keyvault"></a>KeyVault
O RP KeyVault fornece gerenciamento e a auditoria de segredos, como senhas e certificados. Por exemplo, um locatário pode usar o RP KeyVault para fornecer senhas de administrador ou chaves durante a implantação de VM.

## <a name="high-availability-for-azure-stack"></a>Alta disponibilidade para o Azure Stack
*Aplica-se a: Azure 1802 de pilha ou versões posteriores*

Para obter alta disponibilidade de um sistema de produção de várias VMs no Azure, as VMs são colocadas em um conjunto de disponibilidade que abrange vários domínios de falha e domínios de atualização. Dessa forma, [VMs implantadas em conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) são fisicamente isolados uns dos outros em racks de servidor separado para permitir a resiliência de falha conforme mostrado no diagrama a seguir:

  ![Alta disponibilidade de pilha do Azure](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Conjuntos de disponibilidade no Azure Stack
Enquanto a infraestrutura do Azure Stack já é resistente a falhas, a tecnologia subjacente (clustering de failover) ainda incorrerá em algum tempo de inatividade para as VMs em um servidor físico afetado se houver uma falha de hardware. O Azure Stack oferece suporte a uma conjunto de disponibilidade com um máximo de três domínios de falha para ser consistente com o Azure.

- **Domínios de falha**. As VMs colocadas em um conjunto de disponibilidade será fisicamente isoladas uns dos outros, distribuindo-los de maneira mais uniforme possível em vários domínios de falha (nós do Azure Stack). Se houver uma falha de hardware, as VMs do domínio de falha com falha serão ser reiniciadas em outros domínios de falha, mas, se possível, mantidas em domínios de falha separados de outras VMs no mesmo conjunto de disponibilidade. Quando o hardware fica online novamente, as VMs serão redistribuídas para manter a alta disponibilidade. 
 
- **Domínios de atualização**. Domínios de atualização são outro conceito do Azure que fornece alta disponibilidade em conjuntos de disponibilidade. Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ao mesmo tempo. As VMs localizadas no mesmo domínio de atualização serão reiniciadas juntos durante a manutenção planejada. Conforme locatários cria VMs dentro de um conjunto de disponibilidade, a plataforma do Azure distribui automaticamente as VMs entre esses domínios de atualização. No Azure Stack, VMs estão ao vivo migrados entre os hosts online no cluster antes de seu host subjacente é atualizado. Como não há nenhum tempo de inatividade de locatário durante uma atualização do host, o recurso de domínio de atualização no Azure Stack existe somente para compatibilidade de modelo com o Azure. 

### <a name="upgrade-scenarios"></a>Cenários de atualização 
As VMs em conjuntos de disponibilidade que foram criados antes da versão 1802 do Azure Stack recebem um número padrão de domínios de falha e atualização (1 e 1, respectivamente). Para obter alta disponibilidade para VMs nesses conjuntos de disponibilidade já existente, você deve primeiro excluir as VMs existentes e, em seguida, reimplantá-los em uma novo conjunto de disponibilidade com as contagens corretas de domínio de falha e atualização conforme descrito em [alteração a conjunto de disponibilidade para uma VM Windows](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Para conjuntos de dimensionamento de máquina virtual, um conjunto de disponibilidade é criado internamente com uma falha atualização e domínio do domínio contagem padrão (3 e 5, respectivamente). O domínio de falha e atualização do padrão de quaisquer conjuntos de dimensionamento de máquina virtual criados antes da atualização 1802 será colocada em uma conjunto de disponibilidade com contagens (1 e 1, respectivamente). Para atualizar essas instâncias de conjunto de dimensionamento de máquina virtual para obter a visualização mais recente, escalar horizontalmente os conjuntos de dimensionamento de máquina virtual pelo número de instâncias que existiam antes da atualização 1802 e, em seguida, exclua as instâncias mais antigas dos conjuntos de escala de máquina virtual. 

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
É possível usar o RBAC para conceder acesso ao sistema para usuários, grupos e serviços autorizados, atribuindo-lhes funções no nível de uma assinatura, grupo de recursos ou recurso individual. Cada função define o nível de acesso que um usuário, grupo ou serviço tem em relação aos recursos do Microsoft Azure Stack.

O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso: proprietário, Colaborador e leitor. O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas. O leitor pode apenas exibir os recursos existentes do Azure. As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não permite o gerenciamento de rede virtual ou da sub-rede à qual a máquina virtual está conectada.

## <a name="usage-data"></a>Dados de uso
O Microsoft Azure Stack coleta e agrega dados de uso em todos os provedores de recursos e transmite para o Azure para o processamento de comércio do Azure. Os dados de uso coletados no Azure Stack podem ser exibidos por meio da API REST. Há uma API de Locatário consistente do Azure, bem como APIs de Provedor e Provedor Delegado para obter dados de uso de todas as assinaturas de locatários. Esses dados podem ser usados para integrá-los a uma ferramenta externa ou um serviço de cobrança ou estorno. Depois que o uso foi processado pelo comércio do Azure, ela pode ser exibida no portal de cobrança do Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>No desenvolvimento de Kit de desenvolvimento do Azure Stack
Compilações em desenvolvimento permitem que os pioneiros avaliar a versão mais recente do Kit de desenvolvimento do Azure Stack. Eles são compilações incrementais com base na versão principal mais recente. Embora as versões principais continuará a ser liberado a cada poucos meses, as compilações em desenvolvimento liberará intermitentemente entre as versões principais.

Compilações em desenvolvimento irá fornecer os seguintes benefícios:
- Correções de bug
- Novos recursos
- Outras melhorias

## <a name="next-steps"></a>Próximas etapas
[Noções básicas de administração](azure-stack-manage-basics.md)

