---
title: Principais recursos e conceitos na pilha do Azure | Microsoft Docs
description: Saiba mais sobre os principais recursos e conceitos do Azure Stack.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 6c02ec42874e4e3221c53e6d6e85378bbe2e414a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Principais recursos e conceitos do Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Se ainda não estiver familiarizado com o Microsoft Azure Stack, esses termos e descrições de recursos poderão ser úteis.

## <a name="personas"></a>Personas
Há dois tipos de usuários para a pilha do Microsoft Azure, o operador de nuvem (provedor) e o locatário (consumidor).

* Um **operador nuvem** pode configurar a pilha do Azure e gerenciar ofertas, planos, serviços, cotas e preços para fornecer recursos para seus locatários.  Operadores de nuvem também gerenciar a capacidade e respondem a alertas.  
* Um **locatário** (também conhecido como um usuário) usa os serviços que o administrador da nuvem oferece. Os locatários podem provisionar, monitorar e gerenciar os serviços assinados por eles, como Aplicativos Web, Armazenamento e Máquinas Virtuais.

## <a name="portal"></a>Portal
Os principais métodos de interagir com a pilha do Microsoft Azure são o portal do administrador, o portal do usuário e o PowerShell.

Os portais de pilha do Azure são feitos por instâncias separadas do Gerenciador de recursos do Azure.  Um operador de nuvem usa o portal do administrador para gerenciar a pilha do Azure e para executar ações como criar ofertas de locatário.  O portal do usuário (também conhecido como o portal de locatário) fornece uma experiência de autoatendimento para consumo de recursos de nuvem, como máquinas virtuais, contas de armazenamento e aplicativos Web. Para obter mais informações, consulte [usando os portais de administrador e usuário do Azure pilha](azure-stack-manage-portals.md).

## <a name="identity"></a>Identidade 
A pilha do Azure usa o Azure Active Directory (AAD) ou os serviços de Federação do Active Directory (AD FS) como um provedor de identidade.  

### <a name="azure-active-directory"></a>Azure Active Directory
Active Directory do Azure é o provedor de identidade multilocatário baseado em nuvem da Microsoft.  A maioria dos cenários de híbridos usam Active Directory do Azure como armazenamento de identidade.

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory (AD FS)
Você pode optar por usar serviços de Federação do Active Directory (AD FS) para implantações desconectadas da pilha do Azure.  A pilha do Azure, provedores de recursos e outros aplicativos funcionam da mesma forma com o AD FS que funcionam com o Active Directory do Azure. A pilha do Azure inclui sua própria instância do AD FS e o Active Directory e uma API de gráfico do Active Directory. Kit de desenvolvimento de pilha do Azure suporta os seguintes cenários de AD FS:

- Entrar para a implantação por meio do AD FS.
- Criar uma máquina virtual com segredos no cofre de chaves
- Crie um cofre para armazenar/acessar segredos
- Criar funções personalizadas de RBAC na assinatura
- Atribuir usuários a funções RBAC em recursos
- Criar funções RBAC todo o sistema por meio do PowerShell do Azure
- Entrar como um usuário por meio do PowerShell do Azure
- Criar serviço entidades usá-los para entrar no Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiões, serviços, planos, ofertas e assinaturas
Na pilha do Azure, os serviços são fornecidos para uso de regiões, assinaturas, ofertas e planos de locatários. Os locatários podem assinar várias ofertas. As ofertas podem ter um ou mais planos, e os planos podem ter um ou mais serviços.

![](media/azure-stack-key-features/image4.png)

Hierarquia de exemplo das assinaturas de ofertas por um locatário, cada um com diferentes planos e serviços.

### <a name="regions"></a>Regiões
Regiões de pilha do Azure são um elemento básico da escala e gerenciamento. Uma organização pode ter várias regiões com recursos disponíveis em cada região. Regiões também podem ter as ofertas de serviço diferentes disponíveis. No Kit de desenvolvimento de pilha do Azure, apenas uma única região é suportada e é automaticamente denominada *local*.

### <a name="services"></a>Serviços
A pilha do Microsoft Azure permite que os provedores fornecem uma ampla variedade de serviços e aplicativos, como máquinas virtuais, SQL Server bancos de dados, do SharePoint, Exchange e muito mais.

### <a name="plans"></a>Planos
Os planos são agrupamentos de um ou mais serviços. Como provedor, é possível criar planos para oferecer a seus locatários. Por sua vez, os locatários assinam suas ofertas para usar os planos e os serviços incluídos nelas.

Cada serviço adicionado a um plano pode ser configurado com configurações de cota para ajudá-lo a gerenciar sua capacidade de nuvem. As cotas podem incluir restrições como limites de VM, RAM e CPU e são aplicadas por assinatura do usuário. As cotas podem ser diferenciadas por local. Por exemplo, um plano que contém os serviços de computação da região um pode ter uma cota de duas máquinas virtuais, 4 GB de RAM e 10 núcleos de CPU.

Ao criar uma oferta, o administrador de serviço pode incluir um **plano base**. Esses planos base são incluídos por padrão quando um locatário assina essa oferta. Quando um usuário assina (e a assinatura é criada), o usuário tem acesso a todos os provedores de recursos especificado nesses planos de base (com as cotas correspondentes).

O administrador de serviços também pode incluir **planos de complemento** em uma oferta. Planos de complemento não são incluídos por padrão na assinatura. Planos de complemento são planos adicionais (cotas) disponíveis em uma oferta que pode adicionar um proprietário da assinatura para suas assinaturas.

### <a name="offers"></a>Ofertas
Ofertas são grupos de um ou mais planos que os provedores apresentam aos locatários para compra (assinatura). Por exemplo, oferecer Alpha pode conter um plano de contendo um conjunto de serviços de computação e o plano B que contém um conjunto de serviços de armazenamento e rede.

Uma oferta vem com um conjunto de planos base, e os administradores de serviços podem criar planos de complemento que os locatários podem adicionar à sua assinatura.

### <a name="subscriptions"></a>Assinaturas
Uma assinatura é a forma como os locatários compram suas ofertas. Uma assinatura é uma combinação de um locatário com uma oferta. Um locatário pode ter assinaturas de várias ofertas. Cada assinatura se aplica apenas a uma oferta. As assinaturas de um locatário determinam quais planos/serviços eles podem acessar.

As assinaturas ajudam os provedores de organizar e acessar serviços e recursos de nuvem.

Para o administrador, uma assinatura de provedor padrão é criada durante a implantação. Esta assinatura pode ser usada para gerenciar a pilha do Azure, implantar mais provedores de recursos e criar planos e ofertas para locatários. Ele não deve ser usado para executar aplicativos e cargas de trabalho do cliente. 

## <a name="azure-resource-manager"></a>Gerenciador de Recursos do Azure
Usando o Gerenciador de recursos do Azure, você pode trabalhar com seus recursos de infraestrutura em um modelo declarativo baseado em modelo.   Ele fornece uma interface única que você pode usar para implantar e gerenciar os componentes da solução. Para obter mais informações e diretrizes, consulte o [visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupos de recursos
Grupos de recursos são coleções de recursos, serviços e aplicativos – e cada recurso tem um tipo, como máquinas virtuais, redes virtuais, IPs públicos, contas de armazenamento e sites. Cada recurso deve estar em um grupo de recursos e para que grupos de recursos logicamente ajudam a organizar os recursos, como por carga de trabalho ou local.  No Microsoft Azure Stack, recursos como planos e ofertas também são gerenciados em grupos de recursos.
 
### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure
No Gerenciador de recursos do Azure, você pode criar um modelo (no formato JSON) que define a implantação e a configuração do seu aplicativo. Esse modelo é conhecido como um modelo do Gerenciador de Recursos do Azure e fornece uma maneira declarativa de definir a implantação. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos são implantados em um estado consistente.

## <a name="resource-providers-rps"></a>Provedores de recursos (RPs)
Os provedores de recursos são serviços Web que formam a base de todos os serviços de IaaS e PaaS baseados no Azure. Gerenciador de recursos do Azure depende de RPs diferente para fornecer acesso a serviços.

Há quatro RPs fundamentais:, rede, computação e armazenamento KeyVault. Cada um desses RPs o ajuda a configurar e controlar seus respectivos recursos. Os administradores de serviço também podem adicionar novos provedores de recursos personalizados.

### <a name="compute-rp"></a>RP de Computação
O CRP (Provedor de Recursos de Computação) permite que os locatários do Azure Stack criem suas próprias máquinas virtuais. O CRP inclui a capacidade de criar máquinas virtuais, bem como extensões de Máquina Virtual. O serviço de extensão de Máquina Virtual ajuda a fornecer recursos de IaaS para máquinas virtuais Windows e Linux.  Por exemplo, você pode usar o CRP para provisionar uma máquina virtual Linux e executar scripts de Bash durante a implantação para configurar a VM.

### <a name="network-rp"></a>RP de Rede
O NRP (Provedor de Recursos de Rede) oferece uma série de recursos de SDN (Rede Definida pelo Software) e NFV (Virtualização de Função de Rede) para a nuvem privada.  Você pode usar o NRP para criar recursos, como balanceadores, IPs públicos, software de carga de rede grupos de segurança, redes virtuais.

### <a name="storage-rp"></a>RP de Armazenamento
RP armazenamento oferece quatro serviços de armazenamento do Azure consistente: blob, tabela, fila e gerenciamento de contas. Ele também oferece um serviço de administração de nuvem de armazenamento para facilitar a administração pelo provedor de serviços dos serviços de Armazenamento consistente do Azure. O Armazenamento do Azure fornece a flexibilidade para armazenar e recuperar grandes quantidades de dados não estruturados, como documentos e arquivos de mídia, com os Blobs do Azure, e dados estruturados baseados em NoSQL com as Tabelas do Azure. Para obter mais informações sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Armazenamento de blob
O armazenamento de blobs armazena qualquer conjunto de dados. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo. O Armazenamento de Tabela armazena conjuntos de dados estruturados. O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados. O Armazenamento de Fila fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.

Cada blob é organizada em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de 500 TB de capacidade da conta de armazenamento. O armazenamento de blobs oferece três tipos de blobs: blob de blocos, blob de anexo e blob de páginas (discos). Os blobs de blocos são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc. Blobs de anexo são semelhantes aos blobs de blocos, mas são otimizados para operações de anexo. Um blob de anexo pode ser atualizado apenas com a adição de um novo bloco no final. Acrescentar blobs é uma boa opção para cenários como registro em log, em que novos dados precisam ser gravados apenas até o fim do blob. Blobs de página são otimizados para que representam os discos de IaaS e suporte aleatório grava e pode ter até 1 TB. Um disco de IaaS anexado a uma rede de máquinas virtuais do Azure é um VHD armazenado como um blob de página.

#### <a name="table-storage"></a>Armazenamento de tabela
O armazenamento de tabelas é um repositório de chave/atributo NoSQL da Microsoft, que tem um design sem esquemas, o que o torna diferente dos bancos de dados relacionais tradicionais. Como um repositório de dados não tem esquemas, é fácil adaptar seus dados conforme as mudanças nas necessidades de seu aplicativo. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente. O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada valor em uma tabela é armazenado com um nome de propriedade. Esse nome de propriedade pode ser usado para filtrar e especificar os critérios de seleção. Um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabelas não tem esquemas, duas entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes. Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

#### <a name="queue-storage"></a>Armazenamento de Filas
O armazenamento de filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O armazenamento de filas fornece mensagens assíncronas para a comunicação entre os componentes do aplicativo, estando eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O armazenamento de Fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

### <a name="keyvault"></a>KeyVault
O KeyVault RP fornece gerenciamento e a auditoria de segredos, como senhas e certificados. Por exemplo, um locatário pode usar o KeyVault RP para fornecer senhas de administrador ou chaves durante a implantação de VM.

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
É possível usar o RBAC para conceder acesso ao sistema para usuários, grupos e serviços autorizados, atribuindo-lhes funções no nível de uma assinatura, grupo de recursos ou recurso individual. Cada função define o nível de acesso que um usuário, grupo ou serviço tem em relação aos recursos do Microsoft Azure Stack.

RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso: leitor, Colaborador e proprietário. O proprietário tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. O colaborador pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas. O leitor pode apenas exibir os recursos existentes do Azure. As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite a criação e o gerenciamento de máquinas virtuais, mas não permite o gerenciamento de rede virtual ou da sub-rede à qual a máquina virtual está conectada.

## <a name="usage-data"></a>Dados de uso
Pilha do Microsoft Azure coleta e agrega dados de uso em todos os provedores de recursos e as transmite para o Azure para processamento pelo comércio do Azure. Os dados de uso coletados na pilha do Azure podem ser exibidos por meio de uma API REST. Há uma API de Locatário consistente do Azure, bem como APIs de Provedor e Provedor Delegado para obter dados de uso de todas as assinaturas de locatários. Esses dados podem ser usados para integrá-los a uma ferramenta externa ou um serviço de cobrança ou estorno. Depois de uso foi processado pelo comércio do Azure, ela pode ser exibida no portal de cobrança do Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>No desenvolvimento do Kit de desenvolvimento de pilha do Azure
No desenvolvimento de compilações permitem que usuários iniciais avaliar a versão mais recente do Kit de desenvolvimento de pilha do Azure. Eles são compilações incrementais com base na versão principal mais recente. Enquanto as versões principais continuará a ser liberado a cada poucos meses, as compilações no desenvolvimento liberará intermitentemente entre as versões principais.

Compilações de desenvolvimento fornece os seguintes benefícios:
- Correções de bug
- Novos recursos
- Outros aprimoramentos

## <a name="next-steps"></a>Próximas etapas
[Pré-requisitos de implantação de pilha do Azure](azure-stack-deploy.md)

