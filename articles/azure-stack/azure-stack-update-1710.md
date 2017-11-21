---
title: "Atualização da pilha do Azure 1710 (compilação 20171020.1) | Microsoft Docs"
description: "Saiba mais sobre o que é a atualização 1710 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: twooley
ms.openlocfilehash: acce605fe1f3157898b490c394d24037689a7cb6
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Atualização da pilha do Azure 1710 (compilação 20171020.1)

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções neste pacote de atualização, problemas para esta versão e onde baixar a atualização. Conhecido problemas são divididos em problemas conhecidos relacionados ao processo de atualização diretamente e problemas conhecidos com a compilação (após a instalação).

> [!IMPORTANT]
> Este pacote de atualização é apenas para sistemas de pilha do Azure integradas. Não são aplicáveis a este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="improvements-and-fixes"></a>Aperfeiçoamentos e correções

Esta atualização inclui as seguintes melhorias de qualidade e correções para a pilha do Azure.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Correções e melhorias do Windows Server 2016

- Atualizações do Windows Server 2016: 10 de outubro de 2017 — KB4041691 (Build 14393.1770 do sistema operacional. Consulte [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691) para obter mais informações.

### <a name="additional-quality-improvements-and-fixes"></a>Correções e melhorias de qualidade adicionais

- Adicionado o cmdlets do PowerShell com privilégios de ponto de extremidade para ajudar a solucionar problemas e atualizar o servidor de protocolo NTP (Network Time).
- Adicionado suporte para atualizar a lista branca de cmdlet e módulos de ponto de extremidade de administração JEA (Just Enough) do ponto de extremidade com privilégios. 
- Corrigir erros de idiomas locais no ponto de extremidade com privilégios.
- Adicionada a habilidade para girar credenciais de gateway.
- Remover a conta de administrador local CBLocalAdmin. 
- Corrigido o conteúdo do modelo de alerta de pulsação certificar-se de pulsação alerta trabalho corretamente após uma atualização.
- Corrigido o provedor de recursos de malha para lidar com tempos limite durante as operações de FRU. 
- Adicionada a habilidade para desenvolvedores de nuvem usar perfis de API do Gerenciador de recursos do Azure na pilha do Azure.
- Desabilitado o serviço Windows Update na máquina virtual implantação (DVM). 
- Removida o liga/desliga ações do nó da interface do usuário.
- Várias outras correções de desempenho e estabilidade. 
 
## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

Esta seção contém os problemas conhecidos que podem ocorrer durante a instalação da atualização 1710.

> [!IMPORTANT]
> Se a atualização falhar, quando posteriormente tentar reiniciar a atualização, você deve usar o `Resume-AzureStackUpdate` cmdlet do ponto de extremidade com privilégios. Não continue a atualização usando o portal do administrador. (Isso é um problema conhecido nesta versão.) Para obter mais informações, consulte [monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).

| Sintoma  | Causa  | Resolução |
|---------|---------|---------|
|Quando você executa uma atualização, um erro semelhante à seguinte<br>pode ocorrer durante a etapa de "Armazenamento Hosts reiniciar o nó de armazenamento"<br> do plano de ação de atualização.<br><br>**{"name": "Reiniciar o armazenamento de Hosts", "Descrição": "reiniciar<br> Hosts de armazenamento.","errorMessage": "reinicialização da função de tipo<br> 'BareMetal' gerada uma exceção: \n\n computador<br> 05 de nome de host é ignorada. Falha ao recuperar seu LastBootUpTime<br> por meio do serviço WMI com a seguinte mensagem de erro:<br> o servidor RPC não está disponível.<br> (Exceção de HRESULT: 0x800706BA). \nat Restart-Host** | Esse problema é causado por um driver defeituoso potencial presente em algumas configurações. | 1. Entrar para a interface da web do baseboard management controlador BMC e reinicie o host que é identificado na mensagem de erro.<br><br>2. Continue a atualização usando o ponto de extremidade com privilégios. |
| Quando você executa uma atualização, o processo de atualização é exibida para paralisações<br> e não tornar o progresso após a etapa "etapa: executando etapa 2.4 - instalação<br> Atualizar"do plano de ação de atualização.<br><br>Esta etapa é seguida por uma série de processos de cópia de nupkg<br> arquivos para os compartilhamentos de arquivos de infraestrutura interna. Por exemplo:<br><br>**Copiando arquivos 1 de content\PerfCollector\VirtualMachines para <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Ou, você verá a mensagem:<br><br>**WarningMessage:Task: Invocação da interface 'LiveUpdate'<br> da função 'Cloud\Fabric\VirtualMachines' falhou:<br> digite 'LiveUpdate' da função 'VirtualMachines' gerado um<br> exceção: não há espaço suficiente no disco .**  | O problema é causado por arquivos de log enchendo os discos em uma máquina virtual de infraestrutura e um problema no Windows Server expansão arquivo Server (SOFS) que será entregue em uma atualização subsequente. | Entre em contato com atendimento ao cliente Microsoft e suporte (CSS) para obter assistência. | 
| Quando você executa uma atualização, um erro semelhante à seguinte<br> pode ocorrer durante a etapa "etapa: executando etapa 2.13.2 - atualização<br> *VM_Name*"do plano de ação de atualização. (A máquina virtual<br> nome pode variar).<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: invocação de interface 'LiveUpdate' de<br> função 'Cloud\Fabric\WAS' falhou: 'LiveUpdate' de tipo de função<br> 'Era' gerou uma exceção: erro durante a armazenamento<br> inicialização: Ocorreu um erro ao tentar fazer uma API<br> chamada para o serviço Microsoft Storage: {"Mensagem": "um tempo limite<br> ocorreu durante a comunicação com o Service Fabric.<br> Tipo de exceção: TimeoutException.<br> Mensagem de exceção: tempo limite da operação. "}**  | O problema é causado por um tempo limite de e/s no Windows Server que será corrigido em uma atualização subsequente. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executa uma atualização, um erro semelhante à seguinte<br> pode ocorrer durante a etapa "etapa 21 VMs de reinicialização do SQL server".<br><br>**Digite 'LiveUpdateRestart' da função 'VirtualMachines' gerado um<br> exceção: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> consultando VM MachineName-Sql01. - 13/10/2017 5:11:50 PM VerboseMessage: [VirtualMachines: LiveUpdateRestart]<br> VM está marcada como HighlyAvailable. - 13/10/2017 5:11:50 PM<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] em<br>MS. Internal.ServerClusters.ExceptionHelp.Build em<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(forçar Boolean) em Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() em <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() em Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 13/10/2017 5:11:50 PM aviso<br>mensagem: tarefa: invocação de interface 'LiveUpdateRestart' de<br> função 'Cloud\Fabric\VirtualMachines' falhou:** | Esse problema pode ocorrer se a máquina virtual não pôde reiniciar. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executa uma atualização, pode ocorrer um erro semelhante à seguinte:<br><br>**2017-10-22T01:37:37.5369944Z 'Desligamento' de tipo de função 'SQL'<br> gerou uma exceção: erro ao pausar nó<br> .at 's45r1004 Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType &#95; App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 em<br> desligamento, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br> Aplicativos \EnterpriseCloudEngineApplicationType &#95; App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: 50 no &#60; de linha ScriptBlock &#62;<br> &#60; Nenhum arquivo >: 18 no &#60; de linha ScriptBlock &#62; &#60; Nenhum arquivo &#62;: 16 de linha** | Esse problema pode ocorrer se a máquina virtual não pode ser colocada em um estado suspenso para que as funções. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executar uma atualização, qualquer um dos seguintes erros podem ocorrer:<br><br>**Tipo 'Validar' da função 'ADFS' gerou uma exceção: validação<br> para a função do AD FS/gráfico falhou com erro: erro ao verificar o ADFS<br> investigação do ponto de extremidade *endpoint_URI*: chamada de exceção<br> " GetResponse"com"0"argumento (s):" o servidor remoto<br> retornou um erro: (503) do servidor não está disponível. "em Invoke -<br>ADFSGraphValidation**<br><br>**Tipo 'Validar' da função 'ADFS' gerou uma exceção: validação<br> para a função do AD FS/gráfico falhou com erro: erro ao buscar<br> propriedades ADFS: não foi possível conectar ao <br>net.tcp://localhost: 1500/política. A tentativa de conexão durou<br> para um período de tempo de 00:00:02.0498923. Código de erro TCP<br> 10061: nenhuma conexão pôde ser feita porque o destino<br> máquina recusou 127.0.0.1:1500.<br> em ADFSGraphValidation Invoke** | O plano de ação de atualização não é possível validar a integridade de serviços de Federação do Active Directory (AD FS). | Entre em contato com o CSS da Microsoft para obter assistência.

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Esta seção contém os problemas conhecidos com 20171020.1 de compilação após a instalação.

### <a name="portal"></a>Portal

- Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. Isso indica que ocorreu um erro durante a instalação da atualização e que a atualização foi informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o CSS da Microsoft para obter assistência.
- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.
- Quando você exibe as propriedades de um grupo de recursos, o **mover** botão será desabilitado. Esse comportamento é esperado. Atualmente não há suporte para a movimentação de grupos de recursos entre assinaturas.
- Qualquer fluxo de trabalho onde você pode selecionar uma assinatura, o grupo de recursos ou o local em uma lista suspensa, você pode enfrentar um ou mais dos seguintes problemas:

   - Você pode ver uma linha em branco na parte superior da lista. Você ainda deve ser capaz de selecionar um item conforme o esperado.
   - Se a lista de itens na lista suspensa é curta, você não poderá exibir os nomes de item.
   - Se você tiver várias assinaturas de usuário, a lista suspensa de grupo de recursos pode estar vazia. 

   Solução alternativa para os últimos dois problemas, você pode digitar o nome da assinatura ou grupo de recursos (se souber) ou você pode usar o PowerShell em vez disso.
- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.
- Não é possível exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, você pode verificar permissões usando o PowerShell.
  
### <a name="backup"></a>Backup

- Não habilite o backup da infra-estrutura no **backup da infra-estrutura** folha.

### <a name="health-and-monitoring"></a>Monitoramento e integridade

- Se você reinicializar uma instância de função de infraestrutura, você receberá uma mensagem indicando que a reinicialização falhou. No entanto, a reinicialização teve êxito realmente.

### <a name="marketplace"></a>Marketplace
- Quando você tentar adicionar itens para o marketplace de pilha do Azure usando o **adicionar do Azure** opção, nem todos os itens podem estar visíveis para download.
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.

### <a name="compute"></a>Computação
- Os usuários recebem a opção de criar uma máquina virtual com armazenamento com redundância geográfica. Essa configuração faz com que a criação da máquina virtual falhar.
- Você pode configurar uma máquina virtual conjunto de disponibilidade somente com um domínio de falha de um e um domínio de atualização de um.
- Não há nenhuma experiência marketplace para criar conjuntos de escala de máquina virtual. Você pode criar uma escala definida por meio de um modelo.
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.
 
### <a name="networking"></a>Rede
- Você não pode criar um balanceador de carga com um endereço IP público usando o portal. Como alternativa, você pode usar o PowerShell para criar o balanceador de carga.
- Quando você cria um balanceador de carga de rede, você deve criar uma regra NAT (conversão) do endereço de rede. Se você não fizer isso, você receberá um erro ao tentar adicionar uma regra NAT depois que o balanceador de carga é criado.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM), depois que a máquina virtual foi criada e associada com o endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original. Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo. No momento, você só deve usar os novos endereços IP públicos para criação de uma nova VM.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU. 
- Criação de itens diretamente no SQL e em servidores que não são executados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado não correspondente.
 
### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedimentos do campo FRU (unidade renovável)

- Durante a execução de atualização, imagens offline não sejam corrigidas. Se você precisar substituir um nó de unidade de escala, trabalhe com o fornecedor do hardware do OEM para certificar-se de que o nó substituído tem o nível de patch mais recente.

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização de 1710 [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter informações sobre como aplicar atualizações, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).