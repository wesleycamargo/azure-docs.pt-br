---
title: "Melhores práticas de segurança de máquinas virtuais do Azure | Microsoft Docs"
description: "Este artigo fornece uma coleção de melhores práticas de segurança a serem usadas em máquinas virtuais localizadas no Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Melhores práticas de segurança de Máquinas Virtuais do Azure

Na maioria dos cenários de IaaS (Infraestrutura como Serviço), as [Máquinas Virtuais](https://docs.microsoft.com/en-us/azure/virtual-machines/) são a principal carga de trabalho para as organizações que usam a computação em nuvem. Isso é predominante em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), nos quais as organizações desejam migrar cargas de trabalho para a nuvem de forma lenta. Siga o cenário [considerações gerais de segurança para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) e verifique se você tem as melhores práticas de cenário aplicadas a todas as suas VMs localizadas no Azure.

Neste artigo, abordaremos uma coleção de melhores práticas de segurança das VMs do Azure. Essas melhores práticas derivam de nossa experiência com as VMs do Azure e da experiência de clientes como você. 

Para cada prática recomendada, vamos explicar:

- O que é a prática recomendada
- Por que é ideal habilitar essa prática recomendada
- O que poderá acontecer se você não habilitar a prática recomendada
- Possíveis alternativas à prática recomendada
- Como você pode aprender a habilitar a prática recomendada

Este artigo Melhores práticas de segurança de máquinas virtuais do Azure baseia-se em um consenso, bem como nas funcionalidades da plataforma Azure e em conjuntos de recursos existentes no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As melhores práticas de segurança de VMs do Azure abordadas neste artigo incluem:

- Autenticação de máquina virtual e controle de acesso
- Disponibilidade de máquina virtual e acesso de rede
- Proteger dados em repouso em VMs do Azure por meio da imposição de criptografia
- Gerenciando atualizações de máquina virtual
- Gerenciar a situação de segurança da máquina virtual
- Monitorando o desempenho da máquina virtual

## <a name="virtual-machine-authentication-and-access-control"></a>Autenticação de máquina virtual e controle de acesso

A primeira etapa para proteger sua Máquina Virtual é garantir que somente usuários autorizados podem provisionar novas VMs. É possível usar [políticas do Resource Manager](../azure-resource-manager/resource-manager-policy.md) para estabelecer convenções para recursos na organização, criar políticas personalizadas e aplicá-las a recursos, como [grupo de recursos](../azure-resource-manager/resource-group-overview.md). As VMs que pertencem a esse grupo de recursos herdarão essas políticas. Embora essa seja a abordagem recomendada para gerenciar recursos (incluindo VMs) que têm necessidades diferentes e que estão localizados em grupos de recursos diferentes, você também pode controlar o acesso individual às VMs usando [o RBAC (controle de acesso baseado em função)](../active-directory/role-based-access-control-configure.md).

Ao habilitar políticas do Azure Resource Manager e o RBAC para controlar o acesso à VM, você melhora a segurança geral da VM. É recomendável colocar VMs acopladas rigidamente que compartilham o mesmo ciclo de vida no mesmo grupo de recursos. Os grupos de recursos permitem implantar e monitorar recursos como um grupo, além de acumular custos de cobrança por grupo de recursos. Use a abordagem de [privilégio mínimo](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) para permitir o acesso aos usuários para provisionar VMs e planeje usar as seguintes funções internas no Azure ao atribuir privilégios aos usuários:

- [Colaborador de Máquina Virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): pode gerenciar máquinas virtuais, mas não a rede virtual nem a conta de armazenamento à qual ele está conectado.
- [Colaborador de Máquina Virtual Clássica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): pode gerenciar máquinas virtuais clássicas, mas não a rede virtual nem a conta de armazenamento à qual ele está conectado.
- [Gerente de Segurança](../active-directory/role-based-access-built-in-roles.md#security-manager): pode gerenciar componentes de segurança, políticas de segurança e máquinas virtuais.
- [Usuário do DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): pode exibir tudo e se conectar a máquinas virtuais, iniciá-las, reiniciá-las e desligá-las

Não compartilhe contas e senhas entre administradores nem reutilize senhas em várias contas de usuário ou serviços, particularmente, aqueles destinados à mídia social ou outras atividades não administrativas. O ideal é que você use modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para proteger o provisionamento das VMs. Ao usar essa abordagem, é possível proteger as opções de implantação e impor configurações de segurança em toda a implantação.

As organizações que não impõem o controle de acesso a dados utilizando funcionalidades como o RBAC podem estar concedendo mais privilégios do que o necessário para seus usuários. Isso pode levar diretamente ao comprometimento dos dados, por ter acesso a determinado nível de dados que, inicialmente, você não deveria ter.
 

## <a name="virtual-machine-availability-and-network-access"></a>Disponibilidade de máquina virtual e acesso de rede

Se a VM executar aplicativos críticos que precisam ter alta disponibilidade, será altamente recomendável usar várias VMs.  Para uma melhor disponibilidade, crie, no mínimo, duas VMs no [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md). O Azure [Load Balancer](../load-balancer/load-balancer-overview.md) também exige que as VMs com balanceamento de carga pertençam ao mesmo conjunto de disponibilidade. Se essas VMs precisarem ser acessadas pela Internet, você precisará configurar um [balanceador de carga para a Internet](../load-balancer/load-balancer-internet-overview.md).

Quando as VMs são expostas à Internet, é importante garantir que você [controla o fluxo de tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).  Uma vez que os NSGs podem ser aplicados às sub-redes, você pode minimizar o número de NSGs agrupando recursos por sub-rede e aplicando NSGs às sub-redes. O objetivo é criar uma camada de isolamento de rede, que pode ser feita por meio da configuração correta das funcionalidades de [segurança de rede](../best-practices-network-security.md) no Azure.  

Você também pode usar o recurso de acesso à VM Just-In-Time na Central de Segurança do Azure para controlar quem e por quanto tempo alguém pode ter acesso remoto a uma VM específica. Assista ao vídeo abaixo para obter mais informações sobre como usar essa funcionalidade:


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As organizações que não impõem a restrição de acesso de rede para VMs para a Internet estão expostas a riscos de segurança, como um ataque de Força Bruta de RDP. 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>Proteger dados em repouso em VMs do Azure por meio da imposição de criptografia

Hoje em dia, a [criptografia de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória para garantir a privacidade de dados, conformidade e soberania. O [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permite que os administradores de TI criptografem discos de VM (Máquina Virtual) IaaS Windows e Linux. A Azure Disk Encryption aproveita o recurso padrão da indústria BitLocker do Windows e o recurso DM-Crypt do Linux para fornecer criptografia de volume para o SO e os discos de dados.

Você pode aproveitar a Azure Disk Encryption para ajudar a proteger seus dados e atender a requisitos de conformidade e segurança da organização. As organizações devem cogitar também usar a criptografia para ajudar a reduzir riscos relativos ao acesso a dados não autorizado. Também é recomendável que você criptografe as unidades antes de gravar dados confidenciais nelas. 

Lembre-se de criptografar os volumes de dados da VM para protegê-los em repouso em sua conta de armazenamento do Azure. Proteja as chaves de criptografia e o segredo usando o [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/). 

As organizações que não impõem a criptografia de dados ficam mais expostas a problemas de integridade de dados, como usuários não autorizados ou mal-intencionados que roubam dados e contas comprometidas tendo acesso não autorizado aos dados em formato não criptografado. Além desses riscos, as empresas que precisam estar em conformidade com a regulamentação do setor devem provar que estão atentas e usando os controles de segurança corretos para melhorar a segurança dos dados.

Você pode aprender mais sobre a Azure Disk Encryption lendo o artigo [Criptografia de Disco do Azure para VMs IaaS Windows e Linux](azure-security-disk-encryption.md).


## <a name="manage-virtual-machine-updates"></a>Gerenciar atualizações de máquina virtual

O Azure não envia Atualizações do Windows por push para Máquinas Virtuais do Microsoft Azure, pois elas se destinam a serem gerenciadas pelo usuário. É exatamente como qualquer computador local. No entanto, é recomendável que os clientes deixem a configuração automática do Windows Update habilitada. Outra opção é implantar um servidor [WSUS (Windows Server Update Services)](https://technet.microsoft.com/windowsserver/bb332157.aspx) ou outro produto de gerenciamento de atualizações adequado em outra Máquina Virtual do Azure ou local. O WSUS e o Windows Update mantêm as VMs atualizadas. Também é recomendável fazer uso de um produto de exame para verificar se todas as Máquinas Virtuais IaaS estão atualizadas.

Imagens de estoque fornecidas pelo Azure são atualizadas regularmente para incluir a série mais recente das Atualizações do Windows. No entanto, não há nenhuma garantia de que as imagens serão atuais no momento da implantação. É possível que haja um pequeno retardo (não superior a algumas semanas) nas versões públicas. Verificar e instalar todas as Atualizações do Windows deve ser a primeira etapa de cada implantação. Isso é especialmente importante de ter em mente ao implantar imagens fornecidas ou imagens de sua própria biblioteca. As imagens fornecidas como parte da galeria do Microsoft Azure sempre têm as Atualizações do Windows automáticas habilitadas por padrão.

As organizações que não impõem políticas de atualização de software estão mais expostas a ameaças que exploram vulnerabilidades conhecidas que já foram corrigidas. Além desses riscos, as empresas que precisam estar em conformidade com a regulamentação do setor devem provar que estão atentas e usando os controles de segurança corretos para melhorar a segurança de suas cargas de trabalho localizadas na nuvem.
É importante enfatizar que há várias semelhanças entre as melhores práticas de atualização de software em um datacenter tradicional versus no IaaS do Azure. Portanto, é recomendável avaliar as políticas atuais de atualização de software para incluir as VMs do Azure.

## <a name="manage-virtual-machine-secure-posture"></a>Gerenciar a situação de segurança da máquina virtual

As ameaças cibernéticas estão evoluindo e a proteção das VMs exige uma funcionalidade de monitoramento mais sofisticada que pode detectar ameaças rapidamente, disparar alertas e reduzir os falsos positivos. A situação de segurança para esse tipo de carga de trabalho abrange todos os aspectos de segurança da VM, desde o gerenciamento de atualizações até o acesso de rede segura, ao mesmo tempo que monitora de forma ativa as ameaças que tentam obter acesso não autorizado aos recursos.

É possível usar a [Central de Segurança do Azure](../security-center/security-center-intro.md) para monitorar a situação de segurança das [VMs Windows](../security-center/security-center-virtual-machine.md) e [Linux](../security-center/security-center-linux-virtual-machine.md). É possível aproveitar as seguintes funcionalidades da Central de Segurança do Azure para monitorar as VMs:

- Configurações de segurança do SO (Sistema Operacional) com as regras de configuração recomendadas
- Segurança do sistema e atualizações críticas que estão ausentes
- Recomendações de proteção de Endpoint Protection (antimalware)
- Validação de criptografia de disco
- Avaliação de vulnerabilidade e remediação
- Detecção de ameaças

A Central de Segurança pode monitorar as ameaças de forma ativa e essas ameaças serão expostas em Alertas de Segurança. As ameaças que estão correlacionadas serão agregadas em uma única exibição chamada *Incidente de Segurança*. É possível assistir ao vídeo abaixo para entender como a Central de Segurança pode ajudá-lo a identificar possíveis ameaças em suas VMs localizadas no Azure.

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As organizações que não impõem uma situação de segurança para suas VMs não estão cientes das possíveis tentativas de contornar os controles de segurança em vigor.

## <a name="monitoring-virtual-machine-performance"></a>Monitorando o desempenho da máquina virtual

O abuso de recursos também pode ser um problema quando você tem processos em uma VM que estão consumindo mais recursos do que deveriam. Uma máquina virtual com um problema de desempenho pode levar à interrupção do serviço, que vai contra um dos princípios de segurança: disponibilidade. Por esse motivo, é fundamental monitorar o acesso à VM não apenas de forma reativa (durante um problema), mas também ter uma linha de base durante o horário normal de operação.

Os [Logs de Diagnóstico do Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) podem ajudá-lo a monitorar os recursos da máquina virtual e identificar possíveis problemas que comprometem o desempenho e a disponibilidade. A Extensão Diagnóstico do Azure fornece funcionalidades de monitoramento e diagnóstico em uma máquina virtual do Azure baseada no Windows. É possível habilitar essas funcionalidades na máquina virtual incluindo a extensão como parte do [modelo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md) do Azure Resource Manager. Também é possível usar o [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) para obter visibilidade da integridade do recurso.

As organizações que não monitoram o desempenho da máquina virtual não poderão determinar se alterações específicas nos padrões de desempenho fazem parte de sua utilização normal ou se há uma operação anormal que está consumindo mais recursos do que o normal. A anomalia poderá indicar um possível ataque proveniente de um recurso externo ou de um processo comprometido em execução na máquina virtual. 
