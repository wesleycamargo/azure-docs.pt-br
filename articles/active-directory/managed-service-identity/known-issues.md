---
title: Problemas conhecidos e perguntas frequentes com Identidade do Serviço Gerenciado (MSI) para o Azure Active Directory
description: Problemas conhecidos com a Identidade de Serviço Gerenciado do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 552f9e7cae4d7f46ea1548cfe7d9482bff79e5bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930979"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Problemas conhecidos e perguntas frequentes com Identidade do Serviço Gerenciado (MSI) para o Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

### <a name="does-msi-work-with-azure-cloud-services"></a>O MSI funciona com os Serviços de Nuvem do Azure?

Não, nem há previsão de compatibilidade para o MSI com os Serviços de Nuvem do Azure no futuro.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>A MSI funciona com ADAL (Biblioteca de Autenticação do Active Directory) ou com a MSAL (Biblioteca de Autenticação da Microsoft)?

Não, a MSI ainda não está integrada à ADAL ou à MSAL. Para obter detalhes sobre a aquisição de um token MSI usando o ponto de extremidade REST do MSI, consulte [Como usar uma MSI (Identidade de Serviço Gerenciado) da VM do Azure para aquisição de token](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>O que é o limite de segurança de uma Identidade de Serviço Gerenciado?

O limite de segurança da identidade é o recurso ao qual ele está anexado. Por exemplo, o limite de segurança para a MSI de uma Máquina Virtual, é a Máquina Virtual. Qualquer código em execução na VM, é capaz de chamar o ponto de extremidade do MSI e solicitar tokens. É a experiência semelhante com outros recursos que oferecem suporte a MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Devo usar o ponto de extremidade IMDS de VM do MSI ou o ponto de extremidade de extensão da VM do MSI?

Ao usar o MSI com VMs, recomendamos que você usando o ponto de extremidade MSI do IMDS. O serviço de metadados de instância do Azure é um ponto de extremidade REST disponível para todas as VMs de IaaS criadas por meio do Azure Resource Manager. Estes são alguns dos benefícios de usar o MSI em IMDS:

1. Todos os sistemas operacionais de IaaS do Azure com suporte podem usar MSI em IMDS. 
2. Não é mais necessário instalar uma extensão na sua VM para habilitar o MSI. 
3. Os certificados usados pelo MSI não estão mais presentes na VM. 
4. O ponto de extremidade IMDS é um endereço IP não roteável bastante conhecido, disponível somente de dentro da VM. 

A extensão de VM do MSI ainda está disponível para ser usada hoje. No entanto, mais adiante, usaremos como o padrão o ponto de extremidade IMDS. Em breve, a extensão de VM do MSI será iniciada em um plano de reprovação. 

Para obter mais informações sobre o Serviço de Metadados de Instância do Azure, confira [Documentação do IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Quais são as distribuições Linux com suporte?

Todas as distribuições do Linux com suporte do Azure IaaS podem ser usadas com MSI por meio do ponto de extremidade do IMDS. 

Observação: a extensão de VM do MSI somente oferece suporte às seguintes distribuições do Linux:
- CoreOS Estável
- CentOS 7.1
- Redhat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Outras distribuições do Linux não possuem suporte atualmente e a extensão poderá falhar em distribuições sem suporte.

A extensão funciona em CentOS 6.9. No entanto, devido à falta de suporte do sistema em 6.9, a extensão não reinicializará automaticamente se falhar ou for interrompida. Ela é reiniciado quando a VM é reiniciada. Para reiniciar manualmente a extensão, consulte [como você reinicia a extensão do MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Como você reinicia a extensão do MSI?
No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome de extensão e o tipo para o Windows é: ManagedIdentityExtensionForWindows
- Nome de extensão e o tipo para o Linux é: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Script de automação" falha durante a tentativa de exportação de esquema para a extensão do MSI

Quando a Identidade de Serviço Gerenciado é habilitada em uma VM, o erro a seguir é mostrado ao tentar usar o recurso "Script de automação" para a VM ou seu grupo de recursos:

![Erro de exportação de script de automação de MSI](../media/msi-known-issues/automation-script-export-error.png)

A extensão de VM de Identidade de Serviço Gerenciado atualmente não dá suporte à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar a Identidade de Serviço Gerenciado no recurso. Estas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar uma Identidade de Serviço Gerenciado da VM usando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema estiver disponível para a extensão da VM de MSI, ela será listada em [Exportar Grupos de Recursos que contenham extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>A folha Configuração não aparece no portal do Azure

Se a folha Configuração de VM não aparecer na sua VM, então a MSI ainda não foi habilitada no portal na sua região.  Verifique novamente mais tarde.  Você também pode habilitar a MSI para sua VM usando o [PowerShell](qs-configure-powershell-windows-vm.md) ou a [CLI do Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Não é possível atribuir o acesso às máquinas virtuais na folha Controle de Acesso (IAM)

Se **Máquina Virtual** não aparece no portal do Azure como uma opção para **Atribuir acesso a** em **Controle de Acesso (IAM)** > **Adicionar permissões**, então a Identidade de Serviço Gerenciado não foi habilitada no portal na sua região ainda. Verifique novamente mais tarde.  Você ainda pode selecionar a identidade de serviço gerenciado para a atribuição de função por meio de pesquisa de Entidade de Serviço da MSI.  Insira o nome da VM no campo **Selecionar** e a Entidade de Serviço aparece no resultado da pesquisa.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A VM falha ao iniciar depois de ser movida do grupo de recursos ou assinatura

Se você mover uma máquina virtual no estado de execução, ela continua a ser executada durante a movimentação. No entanto, após a movimentação, se a VM for interrompida e reiniciada, ela falhará ao iniciar. Esse problema ocorre porque a VM não está atualizando a referência para a identidade da MSI e continua a apontar para ela no grupo de recursos antigo.

**Solução alternativa** 
 
Dispare uma atualização na VM para que ela possa obter os valores corretos para a MSI. Você pode fazer uma alteração de propriedade de VM para atualizar a referência para a identidade da MSI. Por exemplo, você pode definir um novo valor de marca na VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova marca "fixVM" com um valor 1 na VM. 
 
Definindo essa propriedade, a VM é atualizada com o URI correto do recurso da MSI e então você poderá iniciar a máquina virtual. 
 
Depois que a VM for iniciada, a marca poderá ser removida usando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Problemas conhecidos com Identidades de Atribuídas pelo Usuário

- Atribuições de Identidade Atribuída pelo Usuário estão disponíveis apenas para VM e VMSS. IMPORTANTE: atribuições de Identidade Atribuída pelo Usuário serão alteradas nos próximos meses.
- Identidades Atribuídas pelo Usuário duplicadas na mesma VM/VMSS farão com que a VM/VMSS falhe. Isso inclui as identidades que são adicionadas com maiúsculas de minúsculas. Por exemplo, MinhaIdentidadeAtribuídaPeloUsuário e minhaidentidadeatribuídapelousuário. 
- O provisionamento da extensão de VM para uma VM pode falhar devido a falhas de pesquisa de DNS. Reinicie a VM e tente novamente. 
- Adicionar uma identidade atribuída pelo usuário 'não existente' fará com que a VM falhe. 
- Não há suporte para a criação de uma identidade atribuída pelo usuário com caracteres especiais (isto é, sublinhado) no nome.
- Nomes de identidades atribuídas pelo usuário são restritos a 24 caracteres para o cenário de ponta a ponta. As identidades atribuídas pelo usuário com nomes com mais de 24 caracteres falharão em serem atribuídas.  
- Ao adicionar uma segunda identidade atribuída pelo usuário, o clientID pode não estar disponível para solicitar tokens para a extensão de VM. Como mitigação, reinicie a extensão de VM da MSI com os dois seguintes comandos bash:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Quando uma VM tem uma identidade atribuída pelo usuário, mas nenhuma identidade atribuída pelo sistema, a interface do usuário do portal exibirá a MSI como desabilitada. Para habilitar a identidade atribuída pelo sistema, use um modelo do Azure Resource Manager, uma CLI do Azure ou um SDK.
