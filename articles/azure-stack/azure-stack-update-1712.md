---
title: "Atualização da pilha do Azure 1712 | Microsoft Docs"
description: "Saiba mais sobre o que é a atualização 1712 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: 0456a202990d383370051d99112f829533b1b101
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="azure-stack-1712-update"></a>Atualização de 1712 de pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções neste pacote de atualização, problemas para esta versão e onde baixar a atualização. Conhecido problemas são divididos em problemas conhecidos relacionados ao processo de atualização diretamente e problemas conhecidos com a compilação (após a instalação).

> [!IMPORTANT]
> Este pacote de atualização é aplicável somente para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure pilha 1712 **180106.1**. Se um cliente tiver implantado **180103.2** anteriormente, você não precisa aplicar **180106.1**.

## <a name="before-you-begin"></a>Antes de começar

> [!IMPORTANT]
> Não tente criar máquinas virtuais durante o processo de instalação de atualização de 1712. Consulte [gerenciar atualizações na visão geral do Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) para obter mais detalhes.

### <a name="prerequisites"></a>Pré-requisitos

Você deve primeiro instalar a pilha do Azure [1711 atualizar](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) antes de aplicar essa atualização.

### <a name="post-update-steps"></a>Etapas de pós-atualização

Essa atualização também requer que você instale as atualizações de firmware do parceiro OEM depois de concluir a instalação da atualização 1712 pilha do Azure.

> [!NOTE]
> Consulte o site de parceiros do OEM para baixar as atualizações.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Esta atualização inclui os seguintes aprimoramentos e correções para a pilha do Azure.

#### <a name="new-features"></a>Novos recursos

- AzureStack de teste para validar a nuvem de pilha do Azure disponível por meio do ponto de extremidade com privilégios
- Capacidade de registrar uma implantação desconectada da pilha do Azure
- Alertas de monitoramento para expiração da conta de usuário e de certificado
- Adicionado cmdlet Set-BmcPassword PEP para rotação de senha do BMC
- Atualizações de registro em log de rede para dar suporte a log sob demanda
- Suporte para a operação de recriação de imagem para conjuntos de escalas da máquina Virtual (VMSS)
- Habilitar o modo de quiosque em ERCS VM para logon CloudAdmin
- Os locatários podem ativar máquinas virtuais do Windows automaticamente

#### <a name="fixes"></a>Correções

- Correção para mostrar o Status operacional do nó em manutenção durante a execução do reparo
- Corrigir correto carimbo de data/hora registros de IP público de uso
- Vários outros desempenho, estabilidade e correções de segurança
- FonteDeTempo e Defender privilegiadas correções de bug do módulo de ponto de extremidade

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correções e novos recursos do Windows Server 2016

- [3 de janeiro - 2018 — KB4056890 (Build de SO 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Esta atualização inclui as correções de software para o problema de segurança do setor descrito por [MSRC segurança comunicado ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

Esta seção contém os problemas conhecidos que podem ocorrer durante a instalação da atualização 1712.

1. **Sintoma:** operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização: *"tipo 'CheckHealth' da função 'VirtualMachines' gerada uma exceção: \n\nVirtual verificação de integridade da máquina para - ACS01 produzidos os erros a seguir. \nThere foi um erro ao obter informações de VM de hosts. Detalhes da exceção: \nGet-VM: A operação no computador 'Node03' falhou: serviço o WS-Management não pode processar a solicitação. \Nservice o WMI ou o provedor WMI retornou um erro desconhecido: HRESULT 0x8004106c. "*
    1. **Causa:** esse problema é causado por um problema do Windows Server que se destina a ser resolvido em atualizações subsequentes do servidor de janela.
    2. **Resolução:** entre em contato com Microsoft atendimento e suporte (CSS) para obter assistência.
<br><br>
2. **Sintoma:** operadores de pilha do Azure poderão ver o seguinte erro durante o processo de atualização:*"Habilitando o anel de semente VM falha no nó Host Node03 com erro: [Node03 Host] conectando-se ao servidor remoto Node03 de Host falhou com o mensagem de erro a seguir: O cliente WinRM recebeu um status de erro de servidor HTTP (500), mas o serviço remoto não incluiu quaisquer outras informações sobre a causa da falha. "*
    1. **Causa:** esse problema é causado por um problema do Windows Server que se destina a ser resolvido em atualizações subsequentes do servidor de janela. 
    2. **Resolução:** entre em contato com Microsoft atendimento e suporte (CSS) para obter assistência.
<br><br>

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Esta seção contém os problemas conhecidos de pós-instalação com compilação **180106.1**.

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
- Alguns itens do marketplace estão sendo removidos nesta versão devido a questões de compatibilidade. Esses serão habilitados novamente após a validação adicional.
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
- O balanceamento de carga interno (ILB) incorretamente lida com endereços MAC para VMs de back-end que interrompe a instâncias do Linux.

#### <a name="sqlmysql"></a>SQL/MySQL
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU.
- Criação de itens diretamente no SQL e em servidores que não são executados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado não correspondente.

    > [!NOTE]
    > Você não deve ter um impacto para os usuários de provedor de recursos SQL ou MySQL existentes ao atualizar seus sistemas integrados de pilha do Azure para a versão 1712. Você pode continuar a usar seus builds de provedor de recursos SQL ou MySQL atuais até que uma nova atualização de pilha do Azure está disponível.

#### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

#### <a name="identity"></a>Identidade

No Azure Active Directory Federation Services (ADFS) implantado a ambientes, o **azurestack\azurestackadmin** conta não é o proprietário da assinatura do provedor padrão. Em vez de fazer logon na **portal de administração / ponto de extremidade adminmanagement** com o **azurestack\azurestackadmin**, você pode usar o **azurestack\cloudadmin** conta, isso Você pode gerenciar e usar a assinatura de provedor padrão.

> [!IMPORTANT]
> Embora o **azurestack\cloudadmin** conta é o proprietário da assinatura do provedor padrão em ambientes do AD FS implantado, ele não tem permissões para RDP para o host. Continuar a usar o **azurestack\azurestackadmin** conta ou a conta de administrador local para fazer logon, acessar e gerenciar o host, conforme necessário.

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização do Azure pilha 1712 [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Mais informações

A Microsoft fornece uma maneira de monitorar e retomar atualizações usando o ponto de extremidade privilegiado (PEP) instalado com 1712 de atualização.

- Consulte o [monitorar atualizações na pilha do Azure usando a documentação do ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Consulte também

- Consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md) para obter uma visão geral do gerenciamento de atualizações na pilha do Azure.
- Consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md) para obter mais informações sobre como aplicar atualizações com pilha do Azure.
