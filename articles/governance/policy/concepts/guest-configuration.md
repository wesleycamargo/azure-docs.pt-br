---
title: Entender como realizar auditorias em uma máquina virtual
description: Saiba como o Azure Policy usa a configuração do convidado para auditar as configurações dentro de uma máquina virtual do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 1ea87dc01048a2747a668db7a5b1f22b37ed9213
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310055"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Além de auditar e [corrigir](../how-to/remediate-resources.md) os recursos do Azure, o Azure Policy é capaz de auditar as configurações dentro de uma máquina virtual. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida definições como a configuração do sistema operacional, a configuração ou a presença do aplicativo, as configurações do ambiente e muito mais.

> [!IMPORTANT]
> No momento, há suporte apenas para políticas **internas** na Configuração de Convidado.

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as configurações dentro de uma máquina virtual, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) é habilitada. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Registre-se por meio do portal ou do PowerShell.

#### <a name="registration---portal"></a>Registro – Portal

Para registrar o provedor de recursos da Configuração de Convidado por meio do portal do Azure, siga estas etapas:

1. Inicie o portal do Azure e clique em **Todos os serviços**. Pesquise e selecione **Assinaturas**.

1. Localize e clique na assinatura para a qual você deseja habilitar a Configuração de Convidado.

1. No menu à esquerda da página **Assinatura**, clique em **Provedores de recursos**.

1. Filtre ou role até localizar **Microsoft.GuestConfiguration** e, em seguida, clique em **Registrar** na mesma linha.

#### <a name="registration---powershell"></a>Registro – PowerShell

Para registrar o provedor de recursos da Configuração de Convidado por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell
Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Ferramentas de validação

Na máquina virtual, o cliente de Configuração de Convidado usa ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Observações|
|-|-|-|
| Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| O Ruby e o Python são instalados pela extensão de Configuração de Convidado. |

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Publicador|NOME|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Não há suporte para a Configuração de Convidado em imagens personalizadas de máquina virtual.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operacional|Observações|
|-|-|
|Windows Client | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.
|Windows Server 2016 Nano Server | Sem suporte.|

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada auditoria executada pela Configuração de Convidado exige duas definições de política, uma **DeployIfNotExists** e uma **AuditIfNotExists**. **DeployIfNotExists** é usada para preparar a máquina virtual com o agente de Configuração de Convidado e outros componentes para dar suporte às [ferramentas de validação](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide se a máquina virtual recebeu uma configuração para avaliação. Se não houver nenhuma atribuição no momento, obtenha a atribuição e prepare a máquina virtual:
  - Autenticando na máquina virtual usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Quando **DeployIfNotExists** estiver em conformidade, a definição de política **AuditIfNotExists** usará as ferramentas de validação locais para determinar se a atribuição de configuração atribuída está em conformidade ou não está. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> Para cada definição da Configuração de Convidado, as duas definições de política, **DeployIfNotExists** e **AuditIfNotExists**, precisam existir.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa interna chamada *[Versão Prévia]: Auditar as configurações de segurança de Senha em máquinas virtuais do Linux e do Windows* contém 18 políticas. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. Em cada caso, a lógica dentro da definição valida somente se o sistema operacional de destino é avaliado com base na definição da [regra de política](definition-structure.md#policy-rule).

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revise a [estrutura de definição de política](definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](effects.md)
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/index.md)