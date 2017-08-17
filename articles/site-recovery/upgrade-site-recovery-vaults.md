---
title: "Atualizar o cofre do Site Recovery para o cofre dos Serviços de Recuperação"
description: "Saiba como atualizar um cofre do Azure Site Recovery para o cofre dos Serviços de Recuperação"
ddocumentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: pt-br
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Atualizar cofres do Site Recovery para o Azure Resource Manager com base em cofres dos Serviços de Recuperação

Este artigo descreve como atualizar "Cofres do Site Recovery" para o Azure Resource Manager com base em "Cofres do Serviço de Recuperação" sem afetar a replicação em andamento. Leia mais sobre os recursos e benefícios do Azure Resource Manager [aqui](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introdução
O Cofre dos Serviços de Recuperação é um recurso do Azure Resource Manager para gerenciar suas necessidades de backup e recuperação de desastre de forma nativa na nuvem. É um cofre unificado que pode ser usado no novo Portal do Azure, uma substituição para os cofres clássicos de Backup e Site Recovery.

Os cofres dos Serviços de Recuperação proporcionam uma matriz de recursos, incluindo:

-   Suporte ao Azure Resource Manager: proteja e realize o failover de suas máquinas virtuais e máquinas físicas na pilha do Azure Resource Manager.

-   Excluir Disco – se você tiver arquivos temporários ou dados com uma variação alta com os quais você não queira gastar sua largura de banda, exclua os volumes da replicação. Esse recurso está habilitado no momento em "VMware para Azure" e "Hyper-V para Azure" e em breve será estendido para outros cenários também.

- Suporte para Armazenamento Premium e com redundância local (LRS): agora você pode proteger servidores em contas de armazenamento premium que permitem aos clientes proteger aplicativos com IOPs maiores. Esse recurso está habilitado no momento em "VMware para Azure".

-   Experiência de "Introdução" simplificada: a experiência aprimorada de Introdução foi personalizada para garantir a facilidade da configuração de recuperação de desastres.

- Gerenciar Backup e Site Recovery a partir do mesmo cofre: agora você pode proteger servidores para recuperação de desastres ou execução de backup, do cofre do mesmo, o que reduz consideravelmente a sobrecarga de gerenciamento.

Para obter mais detalhes sobre a os recursos experiência e recursos atualizados, confira este [blog](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Principais recursos

- **Sem impacto na replicação em andamento**: replicações contínuas continuam sem qualquer interrupção durante e após a atualização.

- **Sem custo adicional**: experimente todo um conjunto de recursos novos sem custo adicional

- **Sem perda de dados**: como isso é uma atualização e não uma migração, as informações de replicação existentes (pontos de recuperação, configurações de replicação etc.) permanecem intactas durante e após a atualização.


## <a name="what-happens-during-the-upgrade"></a>O que acontece durante a atualização?

Operações como registrar um novo servidor, habilitar a replicação para uma VM etc. não têm permissão durante a atualização. Qualquer operação que envolva apenas dados lidos ou gravados no cofre, como a replicação em andamento de itens protegidos no cofre, continua sem interrupções.

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>Alterações de automação e de ferramentas após a atualização do cofre
Como parte da atualização de seu tipo de cofre do modelo de implantação clássico para o modelo de implantação do Resource Manager, você deve atualizar sua automação ou ferramentas existentes para garantir que continuem funcionando após a atualização.

## <a name="preparing-your-environment-for-vault-upgrade"></a>Preparar seu ambiente para a atualização do cofre

1.  Instalar/Atualizar o PowerShell para a versão 5 ou posterior usando este [link](https://www.microsoft.com/download/details.aspx?id=50395)

2.  Instale o Azure PowerShell MSI mais recente clicando [aqui](https://github.com/Azure/azure-powershell/releases)

3.  [Baixe](https://aka.ms/vaultupgradescript) o script para a atualização do cofre

## <a name="prerequisites-for-upgrade"></a>Pré-requisitos para a atualização
Para atualizar seus cofres de cofres do Site Recovery para o Azure Resource Manager baseado em cofres do Serviço de Recuperação, os seguintes pré-requisitos devem ser atendidos.

- Versão mínima do agente: a atualização exige que a versão do Provedor do Azure Site Recovery instalada em seu servidor seja no mínimo 5.1.1700.0.

- Configuração com suporte: seu cofre não deve ser configurado com rede SAN, grupos de disponibilidade SQL Always. Há suporte para todas as outras configurações.

>[!NOTE]
> O mapeamento de armazenamento só pode ser gerenciado por meio do PowerShell após a atualização.

- Cenário de implantação com suporte: seu cofre não deve estar no modelo de implantação herdado "VMware para Azure".  Antes de continuar, você precisa mudar para o modelo de implantação avançada.

- Nenhum trabalho ativo iniciado pelo usuário que envolva operações do plano de gerenciamento: como o acesso ao plano de gerenciamento é restrito durante a atualização, você precisa concluir todas as ações do plano de gerenciamento e, depois, disparar a atualização. Isso não inclui a replicação em andamento.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

- Esta atualização afeta minha replicação em andamento?

  Nº A replicação em andamento continuará sem interrupção durante e após a atualização.

- O que acontece com as configurações de rede – VPN Site a Site, configurações de IP etc.?

  A atualização não afeta as configurações de rede, todas as conexões do Azure para o local permanecerão intactas.
- O que acontece com meus cofres se eu não planejar a atualização em breve?

  O suporte para o cofre do Site Recovery no Portal do Azure antigo será preterido a partir de setembro. Portanto, é altamente recomendável aos clientes usar o recurso de atualização para mudar para o novo Portal.

- O que este plano de migração significa para minhas ferramentas existentes?  

  Atualizar suas ferramentas para o modelo de implantação do Resource Manager em que os cofres dos Serviços de Recuperação são baseados é uma das alterações mais importantes que você precisa considerar em seus planos de atualização.

- O tempo de inatividade do plano de gerenciamento será de quanto tempo?

  A atualização demora cerca de 15 a 30 minutos. Pode demorar até no máximo uma hora.

- Posso reverter após a atualização?

  Nº Não há suporte para reversão após os recursos terem sido atualizados com êxito.

- Posso validar minha assinatura ou recursos para ver se eles podem ser atualizados?

  Sim. Na opção de atualização com suporte da plataforma, a primeira etapa é validar se os recursos podem fazer a atualizar. No caso de falha da validação dos pré-requisitos, você receberá erros ou avisos apropriados.

- Como fazer para relatar um problema com a atualização?

  Caso você enfrente falhas durante a atualização, tome nota do OperationId listado no erro. O Suporte da Microsoft estará trabalhando proativamente para resolver o problema. Também é possível entrar em contato com a equipe de suporte informando sua ID de Assinatura, nome do cofre e ID da operação. Trabalharemos para resolver o problema o mais rápido possível. Não repita a operação, a menos que seja explicitamente instruído a fazer isso pela Microsoft.

## <a name="how-to-run-the-script"></a>Como executar o script?

Execute o seguinte comando em um prompt do PowerShell:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionID: a ID da assinatura associada ao cofre que está sendo atualizado
- VaultName: nome do cofre que está sendo atualizado
- Local: local do cofre que está sendo atualizado
- ResourceType: HyperVRecoveryManagerVault para cofres do Site Recovery
- TargetResourceGroupName: grupo de recursos no qual você deseja que o cofre atualizado seja colocado. O TargetResourceGroupName pode ser um grupo de recursos existente no Azure Resource Manager ou um novo. Caso o TargetResourceGroupName fornecido não exista, ele será criado como parte da atualização no mesmo local que o cofre. Para ler mais sobre os Grupos de Recursos, clique [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups):

>[!NOTE]
>Nomes de Grupo de Recursos têm restrições. Respeite essas restrições, caso contrário a atualização do cofre falhará.

Exemplo:

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


Como alternativa, você pode executar o script conforme indicado abaixo, e receberá uma solicitação para fornecer entradas para todos os parâmetros necessários.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  O script do PowerShell solicita que você insira suas credenciais. Você precisa inserir suas credenciais duas vezes: uma vez para a conta do ASM e outra para a conta do Azure Resource Manager.

2.  Após a inserção das credenciais, o script executará uma verificação de pré-requisitos para determinar se a configuração da infraestrutura atende aos pré-requisitos mencionados anteriormente no documento.

3.  Após a verificação dos pré-requisitos, você receberá uma solicitação de confirmação para continuar com a atualização do cofre. Após a confirmação, o processo de atualização começa a atualizar seu cofre. Toda a atualização pode levar de 15 a 30 minutos para ser concluída.

4.  Após a conclusão bem-sucedida da atualização, você poderá acessar o cofre atualizado no novo Portal do Azure.

## <a name="management-experience-post-upgrade"></a>Experiência de gerenciamento pós-atualização

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Como replicar usando o Azure Site Recovery no cofre dos Serviços de Recuperação

- Agora você pode proteger suas VMs do Azure de uma região para outra. Para saber mais, veja [esta](site-recovery-azure-to-azure.md) documentação.

- Para saber mais sobre a replicação de VMs do VMware no Azure, veja [esta](vmware-walkthrough-overview.md) documentação.

- Para saber mais sobre a replicação de VMs do Hyper-V (sem VMM) no Azure, veja [esta](hyper-v-site-walkthrough-overview.md) documentação.

- Para saber mais sobre a replicação de VMs do Hyper-V (com VMM) no Azure, veja [esta](vmm-to-azure-walkthrough-overview.md) documentação.

- Para saber mais sobre a replicação de VMs do Hyper-V (com VMM) em um site secundário, veja [esta](site-recovery-vmm-to-vmm.md) documentação.

- Para saber mais sobre a replicação de VMs do VMware em um site secundário, veja [esta](site-recovery-vmware-to-vmware.md) documentação.

### <a name="how-to-view-your-replicated-items"></a>Como exibir os itens replicados

Veja abaixo a tela que mostra a página de painel do cofre dos Serviços de Recuperação que exibe as principais entidades para o cofre. Clique em **Site Recovery** -> **Itens replicados** para exibir a lista de entidades protegidas no cofre.


![Itens replicados](./media/upgrade-site-recovery-vaults/replicateditems.png)

A tela abaixo mostra o fluxo de trabalho para exibição de seus itens replicados e como iniciar um failover.

![Itens replicados](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>Como exibir suas configurações de replicação

No cofre do Site Recovery, cada grupo de proteção é definido com as configurações de replicação (Frequência de cópia, Retenção do ponto de recuperação, frequência dos instantâneos consistentes por aplicativo etc.). No cofre dos Serviços de Recuperação, essas configurações são definidas como uma política de replicação. O nome da política é o nome do grupo de proteção ou da "primarycloud_Policy".

Para saber mais sobre a política de replicação, clique [aqui](site-recovery-setup-replication-settings-vmware.md)

