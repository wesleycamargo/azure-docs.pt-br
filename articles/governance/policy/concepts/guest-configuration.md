---
title: Entender como o conteúdo de uma máquina virtual de auditoria
description: Saiba como o Azure Policy usa a configuração do convidado para auditar as configurações dentro de uma máquina virtual do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 045339ec0a725359593797bda172a2e93f7c2890
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339016"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Além de auditoria e [corrigindo](../how-to/remediate-resources.md) recursos do Azure, a política do Azure podem auditar as configurações em uma máquina virtual. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida definições como a configuração do sistema operacional, a configuração ou a presença do aplicativo, as configurações do ambiente e muito mais.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as configurações dentro de uma máquina virtual, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) é habilitada. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Registre-se por meio do portal ou do PowerShell. O provedor de recursos é registrado automaticamente se a atribuição de uma política de configuração de convidado é feita por meio do portal.

#### <a name="registration---portal"></a>Registro – Portal

Para registrar o provedor de recursos da Configuração de Convidado por meio do portal do Azure, siga estas etapas:

1. Inicie o portal do Azure e clique em **Todos os serviços**. Pesquise e selecione **Assinaturas**.

1. Localize e clique na assinatura para a qual você deseja habilitar a Configuração de Convidado.

1. No menu à esquerda da página **Assinatura**, clique em **Provedores de recursos**.

1. Filtre ou role até localizar **Microsoft.GuestConfiguration** e, em seguida, clique em **Registrar** na mesma linha.

#### <a name="registration---powershell"></a>Registro – PowerShell

Para registrar o provedor de recursos da Configuração de Convidado por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Ferramentas de validação

Na máquina virtual, o cliente de Configuração de Convidado usa ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Anotações|
|-|-|-|
| Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| O Ruby e o Python são instalados pela extensão de Configuração de Convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração Convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado for recebida, as configurações serão verificadas em um intervalo de 15 minutos. Os resultados são enviados ao provedor de recursos de configuração do convidado assim que a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de Configuração do Convidado. Isso faz com que a Política do Azure avalie as propriedades do Azure Resource Manager. Uma avaliação de Política sob demanda recupera o valor mais recente do provedor de recursos de Configuração do Convidado. No entanto, ele não dispara uma nova auditoria da configuração dentro da máquina virtual.

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Editor|Nome|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, Datacenter 2012 R2, 2016 Datacenter, Datacenter de 2019|
|Microsoft|Windows Client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Configuração de convidado pode fazer a auditoria de nós que executam um sistema operacional com suporte.  Se você quiser auditar máquinas virtuais que usam uma imagem personalizada, você precisa duplicar a **DeployIfNotExists** definição e modificar as **se** seção para incluir as propriedades de imagem.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

Não há suporte para o Windows Server Nano Server em qualquer versão.

### <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede de extensão de configuração do convidado

Para se comunicar com o provedor de recursos de configuração de convidado no Azure, as máquinas virtuais exigem acesso aos datacenters do Azure na porta de saída **443**. Se você estiver usando uma rede virtual privada no Azure e não permitir o tráfego de saída, exceções devem ser configuradas usando [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) regras. Neste momento, uma marca de serviço não existe para configuração de convidado de política do Azure.

Para listas de endereços IP, você pode baixar [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Você só precisará permitir o acesso de saída para os IPs nas regiões em que suas VMs são implantadas.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
> Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Cada auditoria executada pela configuração de convidado requer duas definições de política, uma **DeployIfNotExists** definição e um **auditoria** definição. O **DeployIfNotExists** definição é usada para preparar a máquina virtual com o agente de configuração de convidado e outros componentes para dar suporte a [ferramentas de validação](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Valide se a máquina virtual recebeu uma configuração para avaliação. Se não houver nenhuma atribuição no momento, obtenha a atribuição e prepare a máquina virtual:
  - Autenticando na máquina virtual usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

Se o **DeployIfNotExists** atribuição é incompatível, um [as tarefas de correção](../how-to/remediate-resources.md#create-a-remediation-task) pode ser usado.

Uma vez a **DeployIfNotExists** atribuição está em conformidade, o **auditoria** atribuição de política usa as ferramentas de validação de local para determinar se a atribuição de configuração está em conformidade ou não compatível.
A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> Para cada definição da Configuração de Convidado, as duas definições de política, **DeployIfNotExists** e **Audit**, precisam existir.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. A iniciativa interna chamada *[Versão Prévia]: Auditar as configurações de segurança de Senha em máquinas virtuais do Linux e do Windows* contém 18 políticas. Há seis pares de **DeployIfNotExists** e **Audit** para o Windows e três para o Linux. Em cada caso, a lógica dentro da definição valida somente se o sistema operacional de destino é avaliado com base na definição da [regra de política](definition-structure.md#policy-rule).

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão de configuração de convidado grava arquivos de log para os seguintes locais:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Onde `<version>` refere-se ao número de versão atual.

## <a name="guest-configuration-samples"></a>Exemplos de configuração de convidado

Exemplos de convidado de configuração de política estão disponíveis nos seguintes locais:

- [Índice de exemplos - configuração do convidado](../samples/index.md#guest-configuration)
- [Repositório GitHub de exemplos de política do Azure](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos na [exemplos do Azure Policy](../samples/index.md).
- Revisar a [Estrutura de definição de política](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que um grupo de gerenciamento com [organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/index.md).
