---
title: "Atualização da pilha do Azure 1711 | Microsoft Docs"
description: "Saiba mais sobre o que é a atualização 1711 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: 4d98556f17fa834c497c2d1cd1854c9e6b02e021
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-stack-1711-update"></a>Atualização de pilha 1711 do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções neste pacote de atualização, problemas para esta versão e onde baixar a atualização. Conhecido problemas são divididos em problemas conhecidos relacionados ao processo de atualização diretamente e problemas conhecidos com a compilação (após a instalação).

> [!IMPORTANT]
> Este pacote de atualização é aplicável somente para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure pilha 1711 **20171122.1**.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Você deve primeiro instalar a pilha do Azure [1710 atualizar](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) antes de aplicar essa atualização.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Esta atualização inclui os seguintes aprimoramentos e correções para a pilha do Azure.

#### <a name="new-features"></a>Novos recursos

- Suporte para distribuir os modelos de solução
- Atualizações no log de gráfico de pilha do Azure e tratamento de erros
- Capacidade de ativar e desativar o hosts
- Os usuários agora podem ativar máquinas virtuais do Windows automaticamente
- Ponto de extremidade privilegiado adicionado cmdlet do PowerShell para recuperar as chaves de recuperação do BitLocker para fins de retenção
- Suporte para atualizar as imagens offline durante a atualização de infraestrutura

#### <a name="fixes"></a>Correções

- Condição de corrida fixa no DNS durante unidade substituível de campo (FRU) e log do cluster também atualizado
- Correção para a capacidade de desabilitar-host no campo FRU (unidade renovável)
- Vários outros desempenho, segurança e correções de estabilidade

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correções e novos recursos do Windows Server 2016

- [14 de novembro de 2017 — KB4048953 (Build de SO 14393.1884)](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

Esta seção contém os problemas conhecidos que podem ocorrer durante a instalação da atualização 1711.


1. **Sintoma:** operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização: *"nome: instalação de atualização.", "Descrição": "Instalar a atualização em Hosts e VMs de infra-estrutura.", "errorMessage": "digite LiveUpdate da função ' VirtualMachines' gerada uma exceção: \n\nThere não há espaço suficiente no disk.\n\nat <ScriptBlock>, <No file>: line22 ","status":"Erro","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","etapas": []"*
    2. **Causa:** esse problema é causado pela falta de espaço livre em disco em uma ou mais máquinas virtuais que fazem parte da infraestrutura de pilha do Azure
    3. **Resolução:** entre em contato com Microsoft atendimento e suporte (CSS) para obter assistência.
<br><br>
2. **Sintoma:** operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização:*chamada "ExtractToFile" com "3" argumento (s) de exceção: "o processo não pode acessar o arquivo ' <\\<machineName>-ERCS01\C$ \ Programa Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >'*
    1. **Causa:** esse problema é causado quando reiniciando a atualização do portal que anteriormente era retomado usando um ponto de extremidade privilegiado (PEP).
    2. **Resolução:** entre em contato com Microsoft atendimento e suporte (CSS) para obter assistência.
<br><br>
3. **Sintoma:**operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização:*"tipo 'CheckHealth' da função 'VirtualMachines' gerado uma verificação de integridade de máquina de exceção: \n\nVirtual para <machineName>-ACS01 produzida o erros. \nThere a seguir foi um erro ao obter informações de VM de hosts. Detalhes da exceção: \nGet-VM: A operação no computador 'Node03' falhou: serviço o WS-Management não pode processar a solicitação. \Nservice o WMI ou o provedor WMI retornou um erro desconhecido: HRESULT 0x8004106c ".*
    1. **Causa:** esse problema é causado por um problema do Windows Server que se destina a ser resolvido em atualizações subsequentes do servidor de janela.
    2. **Resolução:** entre em contato com Microsoft atendimento e suporte (CSS) para obter assistência.
<br><br>
4. **Sintoma:**operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização:*"tipo 'DefenderUpdate' da função 'URP' gerou uma exceção: Falha ao obter a versão do \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{nome do arquivo} .exe após 60 tentativas de copiar-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: linha 262"*
    1. **Causa:** esse problema é causado por um download do plano de fundo com falha ou incompleta de atualizações de definições do Windows Defender.
    2. **Resolução:** para continuar a atualização depois de até 8 horas, tente passaram desde que a primeira atualização tente.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Esta seção contém os problemas conhecidos de pós-instalação com compilação **20171122.1**.

#### <a name="portal"></a>Portal

- Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. Isso indica que ocorreu um erro durante a instalação da atualização e que a atualização foi informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o CSS da Microsoft para obter assistência.
- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.
- Quando você exibe as propriedades de um grupo de recursos, o **mover** botão será desabilitado. Esse comportamento é esperado. Atualmente não há suporte para a movimentação de grupos de recursos entre assinaturas.
- Qualquer fluxo de trabalho onde você pode selecionar uma assinatura, o grupo de recursos ou o local em uma lista suspensa, você pode enfrentar um ou mais dos seguintes problemas:

   - Você pode ver uma linha em branco na parte superior da lista. Você ainda deve ser capaz de selecionar um item conforme o esperado.
   - Se a lista de itens na lista suspensa é curta, você não poderá exibir os nomes de item.
   - Se você tiver várias assinaturas de usuário, a lista suspensa de grupo de recursos pode estar vazia. 

        > [!NOTE]
        > Solução alternativa para os últimos dois problemas, você pode digitar o nome da assinatura ou grupo de recursos (se souber) ou você pode usar o PowerShell em vez disso.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.
- Não é possível exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, você pode verificar permissões usando o PowerShell.

#### <a name="health-and-monitoring"></a>Monitoramento e integridade

- Se você reinicializar uma instância de função de infraestrutura, você receberá uma mensagem indicando que a reinicialização falhou. No entanto, a reinicialização teve êxito realmente.

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
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM), depois que a máquina virtual foi criada e associada com o endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original. Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo. No momento, você só deve usar os novos endereços IP públicos para criação de uma nova VM.
- Operadores de pilha do Azure podem ser impossível implantar, excluir, modificar VNETs ou grupos de segurança de rede. Esse problema é visto principalmente nas tentativas de atualização subsequentes do mesmo pacote. Isso é causado por um problema de empacotamento com uma atualização que está sendo investigado.
 
#### <a name="sqlmysql"></a>SQL/MySQL
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU. 
- Criação de itens diretamente no SQL e em servidores que não são executados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado não correspondente.
 
#### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

#### <a name="identity"></a>Identidade

No Azure Active Directory Federation Services (ADFS) implantado a ambientes, o **azurestack\azurestackadmin** conta não é o proprietário da assinatura do provedor padrão. Em vez de fazer logon na **portal de administração / ponto de extremidade adminmanagement** com o **azurestack\azurestackadmin**, você pode usar o **azurestack\cloudadmin** conta, isso Você pode gerenciar e usar a assinatura de provedor padrão.

> [!IMPORTANT]
> Embora o **azurestack\cloudadmin** conta é o proprietário da assinatura do provedor padrão em ambientes do AD FS implantado, ele não tem permissões para RDP para o host. Continuar a usar o **azurestack\azurestackadmin** conta ou a conta de administrador local para fazer logon, acessar e gerenciar o host, conforme necessário.

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização do Azure pilha 1711 [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Mais informações

A Microsoft fornece uma maneira de monitorar e retomar atualizações usando o ponto de extremidade privilegiado (PEP) instalado com 1711 de atualização.

- Consulte o [monitorar atualizações na pilha do Azure usando a documentação do ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Consulte também

- Consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md) para obter uma visão geral do gerenciamento de atualizações na pilha do Azure.
- Consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md) para obter mais informações sobre como aplicar atualizações com pilha do Azure.
