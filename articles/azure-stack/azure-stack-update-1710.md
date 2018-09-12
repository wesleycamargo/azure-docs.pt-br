---
title: Atualização da pilha do Azure 1710 (Build 20171020.1) | Microsoft Docs
description: Saiba mais sobre as novidades na atualização 1710 para o Azure Stack integrada sistemas, os problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377183"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Atualização da pilha do Azure 1710 (Build 20171020.1)

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve os aperfeiçoamentos e correções nesse pacote de atualização, problemas conhecidos de nesta versão e onde baixar a atualização. Problemas são divididos em problemas conhecidos de conhecidos diretamente relacionados ao processo de atualização e problemas conhecidos com a compilação (após a instalação).

> [!IMPORTANT]
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="improvements-and-fixes"></a>Aperfeiçoamentos e correções

Esta atualização inclui os seguintes aprimoramentos de qualidade e correções para o Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Correções e aprimoramentos do Windows Server 2016

- Atualizações do Windows Server 2016: 10 de outubro de 2017 — KB4041691 (Build 14393.1770 do sistema operacional. Ver [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) para obter mais informações.

### <a name="additional-quality-improvements-and-fixes"></a>Correções e melhorias de qualidade adicionais

- Adicionados os cmdlets do PowerShell com privilégios de ponto de extremidade para ajudar a solucionar problemas e atualizar o servidor de protocolo NTP (Network Time).
- Adicionado suporte para atualizar a lista de permissões de cmdlet e módulos de ponto de extremidade de JEA Just Enough Administration () com privilégios de ponto de extremidade. 
- Erros de idioma local fixo no ponto de extremidade com privilégios.
- Adicionada a capacidade de alternar as credenciais de gateway.
- Removida a conta de administrador local CBLocalAdmin. 
- Corrigido o conteúdo do modelo de alerta de pulsação tornar-se de pulsação alerta trabalho corretamente após uma atualização.
- Corrigido o provedor de recursos de malha para lidar com tempos limite durante as operações de FRU. 
- Adicionada a habilidade para desenvolvedores de nuvem usar perfis de API do Azure Resource Manager no Azure Stack.
- Desabilitado o serviço de atualização do Windows na máquina virtual implantação (DVM). 
- Removido o poder do nó ativar/desativar as ações da interface do usuário.
- Várias outras correções de estabilidade e desempenho. 
 
## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

Esta seção contém problemas conhecidos que podem ser encontrados durante a instalação da atualização 1710.

> [!IMPORTANT]
> Se a atualização falhar, quando posteriormente tentar retomar a atualização, você deve usar o `Resume-AzureStackUpdate` cmdlet do ponto de extremidade com privilégios. Não continue a atualização usando o portal do administrador. (Isso é um problema conhecido nesta versão.) Para obter mais informações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).

| Sintoma  | Causa  | Resolução |
|---------|---------|---------|
|Quando você executa uma atualização, um erro semelhante à seguinte<br>podem ocorrer durante a etapa de "Armazenamento Hosts reiniciar o nó de armazenamento"<br> do plano de ação de atualização.<br><br>**{"name": "Reiniciar o armazenamento de Hosts", "description": "reinicie<br> Hosts de armazenamento. errorMessage","": "digite 'Restart' da função<br> 'Bare-metal' gerada uma exceção: \n\n computador<br> 05 de nome de host é ignorada. Falha ao recuperar seu LastBootUpTime<br> por meio do serviço WMI com a seguinte mensagem de erro:<br> o servidor RPC está indisponível.<br> (Exceção de HRESULT: 0x800706BA). \nat Restart-Host** | Esse problema é causado por um driver defeituoso presente em algumas configurações possíveis. | 1. Entrar para a interface da web do baseboard management controller (BMC) e reinicie o host que é identificado na mensagem de erro.<br><br>2. Retome a atualização usando o ponto de extremidade com privilégios. |
| Quando você executa uma atualização, o processo de atualização é exibida prejudicando<br> e não prosseguir após a etapa "etapa: executando etapa 2.4 - Install<br> Atualizar"do plano de ação de atualização.<br><br>Esta etapa é seguida por uma série de processos de cópia de. nupkg<br> compartilhamentos de arquivos de arquivos para a infraestrutura interna. Por exemplo: <br><br>**Copiando arquivos de 1 de content\PerfCollector\VirtualMachines para <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Ou, você verá a mensagem:<br><br>**WarningMessage:Task: Invocação da interface 'LiveUpdate'<br> da função 'Cloud\Fabric\VirtualMachines' falhou:<br> digite 'LiveUpdate' da função 'VirtualMachines' gerado um<br> exceção: não há espaço suficiente no disco .**  | O problema é causado por arquivos de log enchendo os discos em uma máquina virtual de infraestrutura e de um problema no Windows Server expansão arquivo SOFS (servidor) que será entregue em uma próxima atualização. | Entre em contato com atendimento ao cliente Microsoft e suporte (CSS) para obter assistência. | 
| Quando você executa uma atualização, um erro semelhante à seguinte<br> podem ocorrer durante a etapa "etapa: executando etapa 2.13.2 - atualização<br> *VM_Name*"do plano de ação de atualização. (A máquina virtual<br> nome pode variar).<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: invocação de interface 'LiveUpdate' de<br> a função 'Cloud\Fabric\WAS' falhou: 'LiveUpdate' de tipo de função<br> 'WAS' gerou uma exceção: erro durante a armazenamento<br> inicialização: Ocorreu um erro ao tentar fazer uma API<br> chamada para o serviço Microsoft Storage: {"Message": "um tempo limite<br> ocorreu durante a comunicação com o Service Fabric.<br> Tipo de exceção: TimeoutException.<br> Mensagem de exceção: tempo limite da operação. "}**  | O problema é causado por um tempo limite de e/s no Windows Server que será corrigido em uma atualização subsequente. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executa uma atualização, um erro semelhante à seguinte<br> pode ocorrer durante a etapa "Step 21 VMs de reinicialização do SQL server".<br><br>**Digite 'LiveUpdateRestart' da função 'VirtualMachines' gerado de um<br> exceção: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> consultando MachineName VM-Sql01. - 13/10/2017 5: 11H50 VerboseMessage: [VirtualMachines: LiveUpdateRestart.]<br> VM está marcada como HighlyAvailable. - 13/10/2017 5: 11H50<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] em<br>MS. Internal.ServerClusters.ExceptionHelp.Build em<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(forçar Boolean) em failoverclusters.<br> StopClusterResourceCommand.BeginTimedOperation() na <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() em failoverclusters.<br> FCCmdlet.ProcessRecord() - 13/10/2017 5: 11H50 aviso<br>mensagem: tarefa: invocação de interface 'LiveUpdateRestart' de<br> função 'Cloud\Fabric\VirtualMachines' falhou:** | Esse problema pode ocorrer se a máquina virtual não pôde reiniciar. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executa uma atualização, pode ocorrer um erro semelhante ao seguinte:<br><br>**2017-10-22T01:37:37.5369944Z 'Desligar' de tipo de função 'SQL'<br> gerou uma exceção: erro ao nó pausando<br> .at de 's45r1004 Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications \ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 no<br> Desligamento, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>\EnterpriseCloudEngineApplicationType aplicativos&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: linha 50 no < ScriptBlock&#62;,<br> <No file>: linha 18 em < ScriptBlock&#62;, < nenhum arquivo&#62;: 16 de linha** | Esse problema pode ocorrer se a máquina virtual não pode ser colocada em um estado suspenso para esvaziar as funções. | Entre em contato com o CSS da Microsoft para obter assistência.
| Quando você executa uma atualização, qualquer um dos seguintes erros poderá ocorrer:<br><br>**"Validar' de tipo de função 'ADFS' gerou uma exceção: validação<br> para a função do AD FS/gráfico falhou com erro: erro ao verificar o ADFS<br> ponto de extremidade de investigação *endpoint_URI*: chamada de exceção<br> " GetResponse"com"0"argumento (s):" o servidor remoto<br> retornou um erro: (503) do servidor não disponível. "em Invoke -<br>ADFSGraphValidation**<br><br>**Tipo 'Validar' da função 'ADFS' gerou uma exceção: validação<br> para a função do AD FS/gráfico falhou com erro: erro ao buscar<br> propriedades do ADFS: não foi possível conectar ao <br>baseaddress="NET.TCP://localhost:6080/vmmhelperservice/: 1500/política. A tentativa de conexão durou<br> para um período de tempo de 00:00:02.0498923. Código de erro TCP<br> 10061: nenhuma conexão pôde ser estabelecida porque o destino<br> máquina recusou-a ativamente 127.0.0.1:1500.<br> em ADFSGraphValidation Invoke** | O plano de ação de atualização não é possível validar a integridade de serviços de Federação do Active Directory (AD FS). | Entre em contato com o CSS da Microsoft para obter assistência.

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Esta seção contém problemas conhecidos com a compilação 20171020.1 após a instalação.

### <a name="portal"></a>Portal

- Não é possível exibir os recursos de computação ou armazenamento no portal do administrador. Isso indica que ocorreu um erro durante a instalação da atualização e que a atualização incorretamente foi relatada como bem-sucedida. Se esse problema ocorrer, entre em contato com o CSS da Microsoft para obter assistência.
- Você pode ver um painel em branco no portal. Para recuperar o painel, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.
- Quando você exibir as propriedades de um grupo de recursos, o **mover** botão está desabilitado. O comportamento é esperado. No momento, não há suporte para a movimentação de grupos de recursos entre assinaturas.
- Qualquer fluxo de trabalho onde você pode selecionar uma assinatura, o grupo de recursos ou o local em uma lista suspensa, você pode ter um ou mais dos seguintes problemas:

   - Você pode ver uma linha em branco na parte superior da lista. Você ainda deve ser capaz de selecionar um item, conforme o esperado.
   - Se a lista de itens na lista suspensa for curta, você não poderá exibir os nomes de item.
   - Se você tiver várias assinaturas do usuário, a lista suspensa de grupo de recursos pode estar vazia. 

   Solução alternativa para os dois últimos problemas, você pode digitar o nome da assinatura ou grupo de recursos (se souber) ou você pode usar o PowerShell.
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.
- Não é possível exibir as permissões de sua assinatura usando os portais do Azure Stack. Como alternativa, você pode verificar permissões usando o PowerShell.
- O **integridade do serviço** folha Falha ao carregar. Quando você abre a folha de integridade do serviço no portal de administrador ou usuário do Azure Stack exibirá um erro e não carregar as informações. Este comportamento é esperado. Embora você possa selecionar e abrir o serviço de integridade, esse recurso ainda não está disponível, mas será implementado em uma versão futura do Azure Stack.
 

### <a name="backup"></a>Backup

- Não habilitar o backup de infraestrutura na **backup da infra-estrutura** folha.

### <a name="health-and-monitoring"></a>Monitoramento e integridade

- Se você reinicializar uma instância de função de infraestrutura, você pode receber uma mensagem indicando que a reinicialização falhou. No entanto, a reinicialização foi bem-sucedida.

### <a name="marketplace"></a>Marketplace
- Quando você tenta adicionar itens para o Azure Stack marketplace usando o **adicionar a partir do Azure** opção, nem todos os itens podem estar visíveis para download.
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos, como planos e ofertas. Esses itens não estão funcionando para os usuários.

### <a name="compute"></a>Computação
- Os usuários recebem a opção de criar uma máquina virtual com o armazenamento com redundância geográfica. Essa configuração faz com que a criação da máquina virtual falha.
- Você pode configurar uma máquina virtual conjunto de disponibilidade somente com um domínio de falha de um e um domínio de atualização de um.
- Não há nenhuma experiência de marketplace para criar conjuntos de dimensionamento de máquina virtual. Você pode criar um conjunto de dimensionamento, usando um modelo.
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.
 
### <a name="networking"></a>Rede
- É possível criar um balanceador de carga com um endereço IP público por meio do portal. Como alternativa, você pode usar o PowerShell para criar o balanceador de carga.
- Quando você cria um balanceador de carga de rede, você deve criar uma regra de NAT (conversão) de endereço de rede. Se você não fizer isso, você receberá um erro ao tentar adicionar uma regra de NAT, depois de criar o balanceador de carga.
- Não é possível desassociar um endereço IP público de uma máquina virtual (VM), depois que a VM foi criada e associada com esse endereço IP. Dissociação parecerá funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original. Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM associada originalmente e não para um novo. Atualmente, você deve usar apenas os novos endereços IP públicos para criação de nova VM.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Pode demorar até uma hora para que os locatários podem criar bancos de dados em um novo SQL ou MySQL SKU. 
- Criação de itens diretamente em SQL e os servidores que não são realizados pelo provedor de recursos de hospedagem MySQL não tem suporte e pode resultar em um estado incompatível.
 
### <a name="app-service"></a>Serviço de Aplicativo
- Um usuário deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedimentos FRU (unidade) do campo

- Durante a execução de atualização, não têm patches imagens offline. Se você precisar substituir um nó de unidade de escala, trabalhe com seu fornecedor de hardware de OEM para garantir que o nó substituído tem o nível de patch mais recente.

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização 1710 [aqui](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).
- Para obter informações sobre como aplicar atualizações, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
