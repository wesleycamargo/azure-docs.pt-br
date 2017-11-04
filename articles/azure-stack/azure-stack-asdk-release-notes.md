---
title: "Notas de versão do Kit de desenvolvimento de pilha do Microsoft Azure | Microsoft Docs"
description: "Problemas conhecidos do Kit de desenvolvimento de pilha do Azure, correções e aprimoramentos."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: twooley
ms.openlocfilehash: 95f63bc65491e56832b2c473d539cc702c38e584
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de versão do Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Essas notas de versão fornecem informações sobre problemas conhecidos no Kit de desenvolvimento de pilha do Azure, correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201710201"></a>Criar 20171020.1

### <a name="improvements-and-fixes"></a>Aperfeiçoamentos e correções

Para ver a lista de melhorias e correções no build 20171020.1, consulte o [melhorias e correções](azure-stack-update-1710.md#improvements-and-fixes) sistemas integrados de seção das notas de versão de 1710 pilha do Azure. Alguns dos itens listados na seção "melhorias de qualidade adicionais e correções" são relevantes apenas para sistemas integrados.

Além disso, as seguintes correções foram feitas:
- Corrigido um problema em que o provedor de recursos de computação exibido um estado desconhecido.
- Corrigido um problema em que as cotas não sejam exibidas no portal do administrador depois de criá-las e, em seguida, tente mais tarde exibir detalhes do plano.

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="powershell"></a>PowerShell
- A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações significativas. Para obter informações sobre a atualização do 1.2.10 versão, consulte o [guia de migração](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Implantação
- Você deve especificar um servidor de horário por endereço IP durante a implantação.

#### <a name="infrastructure-management"></a>Gerenciamento de infraestrutura
- Não habilite o backup da infra-estrutura no **backup da infra-estrutura** folha.
- O endereço IP do BMC gerenciamento controlador BMC e o modelo não são mostradas as informações essenciais de um nó de unidade de escala. Esse comportamento é esperado no Kit de desenvolvimento de pilha do Azure.

#### <a name="portal"></a>Portal
- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.
- Quando você exibe as propriedades de um grupo de recursos, o **mover** botão será desabilitado. Esse comportamento é esperado. Atualmente não há suporte para a movimentação de grupos de recursos entre assinaturas.
-  Qualquer fluxo de trabalho onde você pode selecionar uma assinatura, o grupo de recursos ou o local em uma lista suspensa, você pode enfrentar um ou mais dos seguintes problemas:

   - Você pode ver uma linha em branco na parte superior da lista. Você ainda deve ser capaz de selecionar um item conforme o esperado.
   - Se a lista de itens na lista suspensa é curta, você não poderá exibir os nomes de item.
   - Se você tiver várias assinaturas de usuário, a lista suspensa de grupo de recursos pode estar vazia. 

   Solução alternativa para os últimos dois problemas, você pode digitar o nome da assinatura ou grupo de recursos (se souber) ou você pode usar o PowerShell em vez disso.

- Você verá um **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.
- No **ativação necessária** detalhes do alerta de aviso, não clique no link para o **AzureBridge** componente. Se você fizer isso, o **visão geral** folha tentará carregar, e não o tempo limite.
- No portal do administrador, você poderá ver um **erro ao buscar locatários** erro no **notificações** área. Você pode ignorar com segurança esse erro.
- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.
- Não é possível exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, você pode verificar permissões usando o PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Quando você tentar adicionar itens para o marketplace de pilha do Azure usando o **adicionar do Azure** opção, nem todos os itens podem estar visíveis para download.
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- Os usuários recebem a opção de criar uma máquina virtual com armazenamento com redundância geográfica. Essa configuração faz com que a criação da máquina virtual falhar. 
- Você pode configurar uma máquina virtual conjunto de disponibilidade somente com um domínio de falha de um e um domínio de atualização de um.
- Não há nenhuma experiência marketplace para criar conjuntos de escala de máquina virtual. Você pode criar uma escala definida por meio de um modelo.
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

#### <a name="networking"></a>Rede
- Você não pode criar um balanceador de carga com um endereço IP público usando o portal. Como alternativa, você pode usar o PowerShell para criar o balanceador de carga.
- Quando você cria um balanceador de carga de rede, você deve criar uma regra NAT (conversão) do endereço de rede. Se você não fizer isso, você receberá um erro ao tentar adicionar uma regra NAT depois que o balanceador de carga é criado.
- Em **rede**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet para VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM), depois que a máquina virtual foi criada e associada com o endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original. Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo. No momento, você só deve usar os novos endereços IP públicos para criação de uma nova VM.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU. 
- Criação de itens diretamente no SQL e em servidores que não são executados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado não correspondente.

#### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.
 
#### <a name="usage-and-billing"></a>Uso e cobrança
- Medidor dados de uso de endereço IP públicos mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

## <a name="build-201709283"></a>Criar 20170928.3

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="powershell"></a>PowerShell
- A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações significativas. Para obter informações sobre a atualização do 1.2.10 versão, consulte o [guia de migração](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Implantação
- Você deve especificar um servidor de horário por endereço IP durante a implantação.

 #### <a name="infrastructure-management"></a>Gerenciamento de infraestrutura
- Não habilite o backup da infra-estrutura no **backup da infra-estrutura** folha.
- O provedor de recursos de computação exibe um estado desconhecido.
- O endereço IP do BMC gerenciamento controlador BMC e o modelo não são mostradas as informações essenciais de um nó de unidade de escala. Esse comportamento é esperado no Kit de desenvolvimento de pilha do Azure. 
   
#### <a name="portal"></a>Portal
- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.
- Quando você exibe as propriedades de um grupo de recursos, o **mover** botão será desabilitado. Esse comportamento é esperado. Atualmente não há suporte para a movimentação de grupos de recursos entre assinaturas.
- Você verá um **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.
- No **ativação necessária** detalhes do alerta de aviso, não clique no link para o **AzureBridge** componente. Se você fizer isso, o **visão geral** folha tentará carregar, e não o tempo limite.
- Cotas poderão não aparecer no portal do administrador depois de criá-los e, em seguida, tente posteriormente exibir os detalhes do plano. Como alternativa, em **serviços e cotas**, clique em **adicionar**e adicione uma nova entrada.
- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.
- Não é possível exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, você pode verificar permissões usando o Powershell.
  
#### <a name="marketplace"></a>Marketplace
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- Os usuários recebem a opção de criar uma máquina virtual com armazenamento com redundância geográfica. Essa configuração faz com que a criação da máquina virtual falhar.
- Você pode configurar uma máquina virtual conjunto de disponibilidade somente com um domínio de falha de um e um domínio de atualização de um.
- Não há nenhuma experiência marketplace para criar conjuntos de escala de máquina virtual. Você pode criar uma escala definida por meio de um modelo.

#### <a name="networking"></a>Rede
- Você não pode criar um balanceador de carga com um endereço IP público usando o portal. Como alternativa, você pode usar o PowerShell para criar o balanceador de carga.
- Quando você cria um balanceador de carga de rede, você deve criar uma regra NAT (conversão) do endereço de rede. Se você não fizer isso, você receberá um erro ao tentar adicionar uma regra NAT depois que o balanceador de carga é criado.
- Em **rede**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet para VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM), depois que a máquina virtual foi criada e associada com o endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original. Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (também conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo. No momento, você só deve usar os novos endereços IP públicos para criação de uma nova VM.


#### <a name="sqlmysql"></a>SQL/MySQL
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU. 
- Criação de itens diretamente no SQL e em servidores que não são executados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado não correspondente.

#### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.
