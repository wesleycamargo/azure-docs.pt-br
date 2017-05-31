---
title: "Aplicativo de orquestração de patch do Azure Service Fabric | Microsoft Docs"
description: "Aplicativo para automatizar a aplicação de patch de SO em um cluster de Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Aplicativo para aplicar patch ao SO Windows em seu cluster do Service Fabric

O Aplicativo de Orquestração de Patch é um aplicativo do Service Fabric que permite automatizar a aplicação de patches do SO em um cluster do Service Fabric no Azure ou localmente, sem tempo de inatividade.

O Aplicativo de Orquestração de Patch inclui os seguintes recursos principais:

1. **Instalação Automática de Atualização do SO**: o Aplicativo de Orquestração de Patch garante que as atualizações sejam baixadas e instaladas automaticamente e o nó seja reinicializado conforme aplicável.
    Isso é feito em todos os nós de cluster sem tempo de inatividade.

1. **Integração de Integridade e Aplicação de Patch com Reconhecimento de Cluster**: o Aplicativo de Orquestração de Patch, enquanto aplica atualizações, monitora a integridade do cluster conforme ele avança atualizando um nó por vez ou um domínio de atualização por vez. 
    Se, a qualquer momento, ele detectar uma redução na integridade do cluster devido ao processo de aplicação de patch, ele interromperá o processo para evitar agravar o problema.

1. **Suporte a todos os clusters do Service Fabric**: o aplicativo é genérico o suficiente para funcionar tanto em clusters do Azure Service Fabric quanto em clusters autônomos.
    > [!NOTE]
    > Suporte a cluster autônomo estará disponível em breve.

## <a name="link-to-download-the-application-package"></a>Link para baixar o pacote de aplicativos

Baixe o aplicativo usando o [Link para download](https://go.microsoft.com/fwlink/P/?linkid=849590)

## <a name="internal-details-of-the-application"></a>Detalhes internos do aplicativo

O Aplicativo de Orquestração de Patch é composto pelos seguintes subcomponentes:

- **Serviço de Coordenador**: é um serviço com estado. O serviço é responsável por
    - Coordenar o trabalho do Windows Update em todo o cluster.
    - Armazena o resultado das operações concluídas do Windows Update.
- **Serviço de Agente do Nó**: é um serviço sem estado, executado em todos os nós de cluster do Service Fabric. O serviço é responsável por
    - Inicialização de NTService do Agente do Nó.
    - Monitoramento do NTService do Agente do Nó.
- **NTService do Agente do Nó**: é um serviço do Windows NT. O NTService do Agente do Nó é executado com privilégios mais altos (SISTEMA). Em comparação, o Serviço de Agente do Nó e o Serviço de Coordenador são executados em um nível inferior de privilégio (SERVIÇO DE REDE). O serviço é responsável por executar os seguintes trabalhos do Windows Update em todos os nós do cluster.
    - Desabilitar o Windows Update automático no nó.
    - Download, instalação do Windows Update de acordo com a política fornecida pelo usuário.
    - Reiniciar o computador após a instalação do Windows Update
    - Carregar o resultado do Windows Update para o Coordenador de Serviço.
    - Relatório de integridade de relatórios no caso de falha na operação após esgotar todas as novas tentativas.

> [!NOTE]
> O Aplicativo de Orquestração de Patch usa o **Gerenciador de Reparo** do serviço do sistema do Service Fabric para habilitar/desabilitar o nó e executar verificações de integridade. A tarefa de reparo criada pelo Aplicativo de Orquestração de Patch rastreia o progresso do Windows Update para cada nó.

## <a name="prerequisites-for-using-the-application"></a>Pré-requisitos para usar o aplicativo

### <a name="ensure-service-fabric-version-is-55-or-above"></a>Verifique se a versão do Service Fabric é 5.5 ou posterior

O Aplicativo de Orquestração de Patch pode ser executado em clusters que tenham o tempo de execução do Service Fabric v5.5 ou superior.

### <a name="enable-repair-manager-service-if-not-running-already"></a>Habilite o serviço do Gerenciador de Reparo (se ainda não estiver em execução)

O Aplicativo de Orquestração de Patch exige que o serviço do sistema do Gerenciador de Reparo esteja habilitado no cluster.

#### <a name="service-fabric-clusters-on-azure"></a>Clusters do Service Fabric no Azure

Clusters do Azure na camada de durabilidade Prata têm o Gerenciador de Reparo habilitado por padrão. Cluster do Azure na camada de durabilidade Ouro pode ou não ter o Gerenciador de Reparo habilitado, dependendo de há quanto tempo os clusters foram criados. Enquanto o cluster do Azure está na camada de durabilidade Bronze, por padrão, ele não tem o serviço de Gerenciador de Reparo habilitado. 

Você pode usar o [modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço de Gerenciador de Reparo em seus clusters do Service Fabric novos/existentes, caso não ainda os veja em execução na seção de serviços do sistema no Service Fabric Explorer.

Primeiro, obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo do Resource Manager personalizado. Em seguida, você pode habilitar o Gerenciador de Reparo usando as seguintes etapas:

1. Primeiro, verifique se a `apiversion` está definida como `2017-07-01-preview` para o recurso `Microsoft.ServiceFabric/clusters`, conforme mostrado no trecho a seguir. Se for diferente, você precisará atualizar a `apiVersion` para o valor `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora habilite o serviço Gerenciador de Reparo adicionando a seguinte seção `addonFeaturres` após a seção `fabricSettings`, conforme mostrado abaixo

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita a conclusão da atualização. Depois de concluído, você poderá ver o serviço do sistema do Gerenciador de Reparo em execução no seu cluster, que é chamado de `fabric:/System/RepairManagerService`, sob a seção de serviços do sistema no Service Fabric Explorer. 

#### <a name="standalone-on-premise-clusters"></a>Clusters locais autônomos

> [!NOTE]
> O suporte para cluster autônomo estará disponível em breve

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Desabilite o Windows Update Automático em todos os nós.

Ter o Windows Update automático habilitado no cluster do Service Fabric pode levar à perda de disponibilidade, já que vários nós poderão reiniciar ao mesmo tempo para concluir a atualização.

O Aplicativo de Orquestração de Patch, por padrão tenta desabilitar o Windows Update automático em cada nó de cluster.

No entanto, caso as configurações sejam gerenciadas pela política de grupo/administrador, é recomendável configurar a política do Windows Update explicitamente como "Notificar antes de baixar".


### <a name="optional-enable-windows-azure-diagnostics"></a>Opcional: habilitar o Diagnóstico do Microsoft Azure

Nos próximos dias, logs para o Aplicativo de Orquestração de Patch devem ser coletados como parte dos logs do Service Fabric em si. No entanto, até lá, os logs serão coletados localmente em cada um dos nós do cluster. Recomendamos configurar o WAD (Diagnóstico do Microsoft Azure) para que os logs sejam carregados de todos os nós para um local central.

As etapas para habilitar WAD são detalhadas [aqui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)

Os logs para o Aplicativo de Orquestração de Patch serão gerados nas seguintes IDs de provedor fixas.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Dentro da seção "WadCfg" no modelo do Azure Resource Manager, adicione a seção a seguir: 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> Caso o cluster do Service Fabric seja composto por vários tipos de nó, a seção acima precisará ser adicionada a todas as seções de "WadCfg".

## <a name="configuring-the-application"></a>Configurando o aplicativo

A seguir estão as configurações que podem ser definidas pelo usuário para ajustar o comportamento do Aplicativo de Orquestração de Patch de acordo com as próprias necessidades.

É possível substituir os valores padrão passando o parâmetro de aplicativo durante a criação/atualização do aplicativo. Parâmetros do aplicativo podem ser fornecidos especificando `ApplicationParameter` ao cmdlet `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de históricos de resultados do Windows Update, que devem ser armazenados em cache. <br>O valor padrão é 3000, supondo que <br> – O número de nós seja de 20 <br> – O número de atualizações acontecendo em um nó por mês seja de 5 <br> – O número de resultados por operação possa ser de 10 <br> – E os resultados para os últimos três meses devam ser armazenados |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo CoordinatorService para instalar atualizações do Windows em todos os nós de cluster do Service Fabric<br>                         Valores permitidos são: <br><br>                                                           <b>NodeWise</b> – o Windows Update é instalado em um nó por vez <br>                                                           <b>UpgradeDomainWise</b> – o Windows Update faria a instalação de um domínio de atualização por vez (no máximo todos os nós que pertencem a um domínio de atualização podem ir para o Windows Update)
|LogsDiskQuotaInMB   |long  <br> (Padrão: 1024)               |Tamanho máximo dos logs do Aplicativo de Orquestração de Patch em MB, que pode ser mantido localmente no nó
| WUQuery               | string<br>(Padrão: "IsInstalled=0")                | Consulta para obter atualizações do Windows; para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Bool <br> (padrão: True)                 | Esse sinalizador permite a instalação das atualizações do SO Windows.            |
| WUOperationTimeOutInMinutes | int <br>(Padrão: 90)                   | Especifica o tempo limite para qualquer operação do Windows Update (pesquisar/baixar/instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | int <br> (Padrão: 5)                  | Essa configuração define o número máximo de vezes que o serviço reagendaria o Windows Update no caso de falha persistente na operação          |
| WURescheduleTimeInMinutes | int <br>(Padrão: 30) | Essa configuração define o intervalo ao qual o serviço reagendaria o Windows Update no caso de persistência da falha |
| WUFrequency           | Cadeia de caracteres separada por vírgula (padrão: "Semanalmente, quarta-feira, 7:00:00")     | A frequência para a instalação do Windows Update. O formato e os Valores Possíveis são como abaixo <br>– Mensal, DD,HH:MM:SS Ex: Mensal, 5,12:22:32 <br> – Semanal, DIA,HH:MM:SS Ex: Semanal, terça-feira, 12:22:32  <br> – Diário, HH:MM:SS Ex: Diário, 12:22:32  <br> – Nenhum – indica que o Windows Update não deve ser executado  <br><br> OBSERVAÇÃO: todos os horários estão em UTC|
| AcceptWindowsUpdateEula | Bool <br>(Padrão: true) | Ao configurar esse sinalizador, o aplicativo aceita o EULA para o Windows Update em nome do proprietário do computador.              |


## <a name="steps-to-deploy-the-application"></a>Etapas para implantar o aplicativo

1. Conclua todas as etapas necessárias para preparar o cluster.
1. Implantar o aplicativo – isso pode ser feito no PowerShell usando as etapas mencionadas [aqui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)
1. Para configurar o aplicativo no momento da implantação, use `ApplicationParamater` para o cmdlet `New-ServiceFabricApplication`

    Para facilitar o uso, fornecemos o script Deploy.ps1 com nosso aplicativo. Para usá-lo.

    - Conecte-se ao cluster do Service Fabric usando `Connect-ServiceFabricCluster`
    - Execute o script do PowerShell Deploy.ps1 com o valor `ApplicationParameter` apropriado

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="steps-to-upgrade-the-application"></a>Etapas para atualizar o aplicativo

Para atualizar um Aplicativo de Orquestração de Patch existente usando o PowerShell, consulte as etapas de atualização do aplicativo mencionadas [aqui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)

Para alterar apenas os parâmetros do aplicativo, forneça `ApplicationParamater` ao cmdlet `Start-ServiceFabricApplicationUpgrade` com a versão de aplicativo existente.

## <a name="steps-to-remove-the-application"></a>Etapas para remover o aplicativo

Para remover o aplicativo, consulte as etapas mencionadas [aqui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)

Para facilitar o uso, fornecemos o script Undeploy.ps1 com nosso aplicativo. Para usá-lo.
    - Conecte-se ao cluster do Service Fabric usando ```Connect-ServiceFabricCluster```
    - Execute o script do PowerShell Undeploy.ps1

> [!NOTE]
> Mantenha a pasta de scripts e de aplicativos PatchOrchestrationApplication no mesmo diretório.

## <a name="viewing-the-windows-update-results"></a>Exibindo os resultados do Windows Update

O Aplicativo de Orquestração de Patch expõe a API REST para exibir os resultados históricos para o usuário.

Os resultados do Windows Update podem ser consultados registrando-se no cluster e descobrindo o endereço de réplica para o primário do coordenador de serviço e acessando a URL no navegador.
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults O ponto de extremidade REST para o CoordinatorService tem porta dinâmica, para verificar a URL exata, consulte o Service Fabric Explorer.
Ex: para o exemplo abaixo, os resultados estariam disponíveis na `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
 ![Imagem do Ponto de Extremidade Rest](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


O usuário pode acessar a URL de fora do cluster também, caso o proxy reverso esteja habilitado no cluster.
Ponto de extremidade que precisa ser atingido: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults Proxy reverso pode ser habilitado no cluster seguindo as etapas [aqui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 

> 
> [!WARNING]
> Depois que Proxy Reverso estiver configurado, todos os microsserviços do cluster que exponham um ponto de extremidade HTTP serão endereçáveis de fora do cluster.

## <a name="diagnostics--health-events"></a>Eventos de Diagnóstico/Integridade

### <a name="collecting-patch-orchestration-application-logs"></a>Coletar Logs do Aplicativo de Orquestração de Patch

Nos próximos dias, os logs do Aplicativo de Orquestração de Patch serão coletados como parte dos logs do Service Fabric.
Até esse momento, os logs podem ser coletados de uma das seguintes maneiras

#### <a name="locally-on-each-node"></a>Localmente em cada nó

Os logs são coletados localmente em cada nó de cluster do Service Fabric. O local para acessar os logs é \[Service Fabric\_Instalação\_Unidade\]:\\PatchOrchestrationApplication\\logs

Por exemplo: se o Service Fabric for instalado na unidade "D", o caminho será D:\\PatchOrchestrationApplication\\logs

#### <a name="central-location"></a>Local Central

Se WAD estiver configurado como parte das etapas de pré-requisitos, logs para o Aplicativo de Orquestração de Patch estarão disponíveis no Armazenamento do Azure.

### <a name="health-reports"></a>Relatórios de Integridade

O Aplicativo de Orquestração de Patch também publica relatórios de integridade em relação ao CoordinatorService ou ao NodeAgentService nos seguintes casos

#### <a name="windows-update-operation-failed"></a>Falha na operação do Windows Update

Se a operação do Windows Update falhar em um nó, o relatório de integridade em relação a NodeAgentService será gerado.
Detalhes do relatório de integridade poderiam conter o nome do nó problemático.

O relatório seria apagado automaticamente depois da conclusão bem-sucedida do patch no nó com problemas.

#### <a name="node-agent-ntservice-is-down"></a>O NTService do Agente do Nó está inativo

Se NodeAgentNTService estiver inativo em um nó, o relatório de integridade no nível de aviso será gerado em NodeAgentService

#### <a name="repair-manager-is-not-enabled"></a>O Gerenciador de Reparo não está habilitado

Se o serviço do Gerenciador de Reparo não for encontrado no cluster, o relatório de integridade no nível de aviso será gerado para o CoordinatorService.

## <a name="frequently-asked-questions"></a>Perguntas frequentes:

P. **Consigo ver meu Cluster em estado de erro quando o Aplicativo de Orquestração de Patch está em execução**.

R. O Aplicativo de Orquestração de Patch desabilitaria e/ou reiniciaria os nós; durante o processo de instalação, isso pode resultar em redução temporária da integridade do cluster.

Com base na política para aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch OU todo um domínio de atualização pode ficar inativo ao mesmo tempo.

Observe que, até o fim da instalação do Windows Update, os nós serão reabilitados após a reinicialização.

Ex: no caso abaixo, o cluster entrou no estado de erro temporariamente devido a dois nós inativos e a política MaxPercentageUnhealthNodes foi violada. É um erro temporário até que a operação de patch esteja em andamento.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas

P. **Posso usar o Aplicativo de Orquestração de Patch para clusters autônomos?**

R. Ainda não, o suporte para clusters autônomos estará disponível em breve.

P. **O Aplicativo de Orquestração de Patch está em estado de aviso**

R. Verifique se um relatório de integridade publicado em relação ao aplicativo é a causa raiz. Geralmente, o aviso conteria detalhes do problema.
O aplicativo deve esperado recuperar-se automaticamente desse estado caso o problema seja transitório.

P. **Meu cluster não está íntegro. No entanto, preciso fazer a atualização do SO com urgência**

R. O Aplicativo de Orquestração de Patch não instala atualizações enquanto o cluster não está íntegro.
Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do Aplicativo de Orquestração de Patch.

P. **Por que a execução da aplicação de patch no cluster leva tanto tempo?**

R. O tempo que o Aplicativo de Orquestração de Patch leva depende principalmente dos seguintes fatores:

- Política do CoordinatorService – política padrão de `NodeWise` resulta em aplicação de patch a apenas um nó por vez; especialmente no caso de clusters maiores, é recomendável usar a política `UpgradeDomainWise` para obter uma aplicação de patch mais rápida no cluster.
- Número de atualizações disponíveis para baixar e instalar – o tempo médio necessário para baixar e instalar uma atualização não deve exceder algumas horas.
- Desempenho da VM e largura de banda de rede.

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- O Aplicativo de Orquestração de Patch aceita o EULA do Windows Update em nome de usuário. A configuração opcionalmente pode ser desativada na configuração do aplicativo.

- O Aplicativo de Orquestração de Patch coleta a telemetria para acompanhar o uso e o desempenho.
    A telemetria do aplicativo segue a definição da configuração de telemetria do tempo de execução do Service Fabric (ativada por padrão).

## <a name="troubleshooting-help"></a>Ajuda para a solução de problemas

### <a name="node-not-coming-back-to-up-state"></a>O nó não volta para o estado Ativo

**P mó pode ficar preso no estado de Desabilitação** Isso pode acontecer quando um nó programado para a operação não pode ser desabilitado devido a verificação de segurança pendente. Para corrigir essa situação, verifique se há nós suficientes disponíveis em estado íntegro.

**O nó pode estar preso no estado Desabilitado pelos seguintes motivos**

- O nó foi desabilitado manualmente.
- O nó foi desabilitado devido a um trabalho de infraestrutura do Azure em andamento.
- O nó foi desabilitado temporariamente pelo Aplicativo de Orquestração de Patch para aplicar o patch ao nó.

**O nó pode estar preso no estado Inativo pelos seguintes motivos**

- O nó foi colocado no estado inativo manualmente.
- O nó está passando por reinicialização (pode ser disparada pelo Aplicativo de Orquestração de Patch).
- O nó está inativo devido a problemas de conectividade de rede ou VM/Computador com defeito.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O Aplicativo de Orquestração de Patch tenta instalar o Windows Update de acordo com a política de reagendamento. O serviço tentará recuperar o nó e ignorar a atualização de acordo com a política do aplicativo.

Nesse caso, será gerado um relatório de integridade no nível de aviso em relação ao Serviço de Agente do Nó.
O resultado para o Windows Update também conterá o possível motivo da falha.

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>A integridade do cluster entrou em estado de erro enquanto a atualização estava sendo instalada

Caso um Windows Update incorreto reduza a integridade do aplicativo/cluster em um nó ou um domínio de atualização específico, o Aplicação de Orquestração de Patch não continuará nenhuma operação subsequente do Windows Update até que o cluster esteja íntegro novamente.

O administrador precisa intervir e entender por que o Windows Update está causando fazendo a integridade do aplicativo/cluster apresentar problemas.

