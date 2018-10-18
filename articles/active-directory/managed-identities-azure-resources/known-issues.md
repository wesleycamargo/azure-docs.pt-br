---
title: Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure
description: Problemas conhecidos com identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 2a759aea4288af2e90335b47244408d6a537e24b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295574"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades gerenciadas dos recursos do Azure funcionam com os serviços de nuvem do Azure?

Não, não há planos para suportar identidades gerenciadas para recursos do Azure no Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>As identidades gerenciadas dos recursos do Azure funcionam com a ADAL (Biblioteca de Autenticação do Active Directory) ou com a MSAL (Microsoft Authentication Library)?

Não, as identidades gerenciadas dos recursos do Azure ainda não estão integradas com o ADAL ou o MSAL. Para obter detalhes sobre como adquirir um token para identidades gerenciadas para recursos do Azure usando o endpoint REST, consulte [Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>O que é o limite de segurança de identidades gerenciadas para recursos do Azure?

O limite de segurança da identidade é o recurso ao qual ele está anexado. Por exemplo, o limite de segurança de uma Máquina Virtual com identidades gerenciadas para recursos do Azure ativados é a Máquina Virtual. Qualquer código em execução nessa VM pode chamar as identidades gerenciadas do ponto de extremidade de recursos do Azure e solicitar tokens. É a experiência semelhante com outros recursos que suportam identidades gerenciadas para recursos do Azure.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>Devo usar as identidades gerenciadas para o ponto de extremidade do VM IMDS de recursos do Azure ou o ponto de extremidade de extensão de VM?

Ao usar identidades gerenciadas para recursos do Azure com VMs, incentivamos o uso das identidades gerenciadas do ponto de extremidade IMDS de recursos do Azure. O serviço de metadados de instância do Azure é um ponto de extremidade REST disponível para todas as VMs de IaaS criadas por meio do Azure Resource Manager. Alguns dos benefícios do uso de identidades gerenciadas para recursos do Azure sobre o IMDS são:

1. Todos os sistemas operacionais compatíveis com o Azure IaaS podem usar identidades gerenciadas para recursos do Azure por meio do IMDS. 
2. Não é mais necessário instalar uma extensão em sua VM para ativar identidades gerenciadas para recursos do Azure. 
3. Os certificados usados por identidades gerenciadas para recursos do Azure não estão mais presentes na VM. 
4. O ponto de extremidade IMDS é um endereço IP não roteável bastante conhecido, disponível somente de dentro da VM. 

As identidades gerenciadas da extensão de VM dos recursos do Azure ainda estão disponíveis para serem usadas hoje; no entanto, seguindo em frente, usaremos o endpoint do IMDS como padrão. As identidades gerenciadas da extensão de VM dos recursos do Azure serão preteridas em janeiro de 2019. 

Para obter mais informações sobre o Azure Instance Metadata Service, consulte [documentação do IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Quais são as distribuições Linux com suporte?

Todas as distribuições de Linux suportadas pelo IaaS do Azure podem ser usadas com identidades gerenciadas para recursos do Azure por meio do ponto de extremidade IMDS. 

Observação: As identidades gerenciadas para recursos do Azure extensão de VM (que deverão ser preteridas em janeiro de 2019) dá suporte apenas a seguintes distribuições Linux:
- CoreOS Estável
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Outras distribuições do Linux não possuem suporte atualmente e a extensão poderá falhar em distribuições sem suporte.

A extensão funciona em CentOS 6.9. No entanto, devido à falta de suporte do sistema em 6.9, a extensão não reinicializará automaticamente se falhar ou for interrompida. Ela é reiniciado quando a VM é reiniciada. Para reiniciar a extensão manualmente, consulte [Como você reinicia as identidades gerenciadas da extensão de recursos do Azure?](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Como você reinicia as identidades gerenciadas para a extensão de recursos do Azure?
No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome de extensão e o tipo para o Windows é: ManagedIdentityExtensionForWindows
- Nome de extensão e o tipo para o Linux é: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquemas para identidades gerenciadas para a extensão de recursos do Azure

Quando as identidades gerenciadas dos recursos do Azure são habilitadas em uma VM, o seguinte erro é exibido ao tentar usar o recurso "Script de automação" para a VM ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019) não dão suporte atualmente à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema se torna disponível para as identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019), ela será listada em [Exportando grupos de recursos que contêm extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>A folha Configuração não aparece no portal do Azure

Se a folha de configuração da VM não aparecer na sua VM, as identidades gerenciadas dos recursos do Azure ainda não foram ativadas no portal da sua região.  Verifique novamente mais tarde.  Você também pode habilitar identidades gerenciadas para recursos do Azure para sua VM usando [PowerShell](qs-configure-powershell-windows-vm.md) ou [CLI do Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Não é possível atribuir o acesso às máquinas virtuais na folha Controle de Acesso (IAM)

Se **Máquina virtual** não aparecer no portal do Azure como opção para **Atribuir acesso a** em **Controle de acesso (IAM)** > **Adicionar permissões**, as identidades gerenciadas para os recursos do Azure ainda não foram ativadas no portal em sua região. Verifique novamente mais tarde.  Ainda é possível selecionar a identidade da VM para a atribuição de função, pesquisando as identidades gerenciadas para o Principal de serviço dos recursos do Azure.  Insira o nome da VM no campo **Selecionar** e a Entidade de Serviço aparece no resultado da pesquisa.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A VM falha ao iniciar depois de ser movida do grupo de recursos ou assinatura

Se você mover uma máquina virtual no estado de execução, ela continua a ser executada durante a movimentação. No entanto, após a movimentação, se a VM for interrompida e reiniciada, ela falhará ao iniciar. Esse problema ocorre porque a VM não está atualizando a referência às identidades gerenciadas para a identidade de recursos do Azure e continua apontando para ela no grupo de recursos antigo.

**Solução alternativa** 
 
Acione uma atualização na VM para obter valores corretos para as identidades gerenciadas dos recursos do Azure. Você pode fazer uma alteração de propriedade de VM para atualizar a referência às identidades gerenciadas para a identidade de recursos do Azure. Por exemplo, você pode definir um novo valor de marca na VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova marca "fixVM" com um valor 1 na VM. 
 
Ao definir essa propriedade, a VM atualiza com as identidades gerenciadas corretas para o URI de recurso de recursos do Azure e, em seguida, você deve poder iniciar a VM. 
 
Depois que a VM for iniciada, a marca poderá ser removida usando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Problemas conhecidos com identidades atribuídas ao usuário

- atribuições de identidade atribuídas pelo usuário estão disponíveis apenas para VM e VMSS. IMPORTANTE: as atribuições de identidade atribuída pelo usuário serão alterado nos próximos meses.
- Identidades atribuídas pelo usuário duplicadas na mesma VM / VMSS farão com que o VM / VMSS falhe. Isso inclui as identidades que são adicionadas com maiúsculas de minúsculas. Por exemplo, MinhaIdentidadeAtribuídaPeloUsuário e minhaidentidadeatribuídapelousuário. 
- O provisionamento da extensão de VM (que deverá ser preterida em janeiro de 2019) para uma VM pode falhar devido a falhas de pesquisa de DNS. Reinicie a VM e tente novamente. 
- Adicionar uma identidade "inexistente" ao usuário fará com que a VM falhe. 
- Criar uma identidade atribuída pelo usuário com caracteres especiais (ou seja, sublinhado) no nome, não é suportado.
- os nomes de identidade atribuídos pelo usuário são restritos a 24 caracteres para o cenário de ponta a ponta. identidades atribuídas pelo usuário com nomes com mais de 24 caracteres não serão atribuídas.
- Se estiver usando a extensão da máquina virtual de identidade gerenciada (com planejamento de ser preterida em janeiro de 2019), o limite com suporte será 32 identidades gerenciadas atribuídas pelo usuário. Sem a extensão da máquina virtual de identidade gerenciada, o limite permitido é de 512.  
- Ao adicionar uma segunda identidade atribuída pelo usuário, o ID do cliente pode não estar disponível para tokens de solicitações para a extensão da VM. Como uma atenuação, reinicie as identidades gerenciadas da extensão VM dos recursos do Azure com os dois comandos bash a seguir:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Quando uma VM tem uma identidade atribuída pelo usuário, mas não uma identidade atribuída pelo sistema, a interface do usuário do portal mostrará identidades gerenciadas para recursos do Azure como desativadas. Para habilitar a identidade atribuída pelo sistema, use um modelo do Azure Resource Manager, uma CLI do Azure ou um SDK.
