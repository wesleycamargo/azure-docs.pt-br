---
title: Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar a publicação em estágios para aplicativos Web no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: ea9167404034911a0e917374fbdb9962da1578d5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257826"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no Serviço de Aplicativo do Azure
<a name="Overview"></a>

Ao implantar seu aplicativo Web, aplicativo Web no Linux, back-end móvel e aplicativo de API no [Serviço de Aplicativo](https://go.microsoft.com/fwlink/?LinkId=529714), você pode implantar em um slot de implantação separado em vez de no slot de produção padrão quando estiver executando no tipo do plano do Serviço de Aplicativo **Standard** ou **Premium**. Os slots de implantação são, na verdade, aplicativos online com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. Implantar o seu aplicativo em um slot de implantação tem os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
* Implantar um aplicativo em um slot inicial e depois permutá-lo, enviando-o para produção, garante que todas as instâncias do slot estejam prontas antes dessa troca. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de permuta. Todo esse fluxo de trabalho pode ser automatizado por meio da configuração de [Permuta Automática](#Auto-Swap) quando a validação de pré-permuta não é necessária.
* Após a troca, o slot com o aplicativo de preparo anterior terá o aplicativo de produção anterior. Se as alterações permutadas no slot de produção não forem o que você esperava, é possível fazer a mesma permuta imediatamente para ter o "último site bom" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Para descobrir o número de slots que a sua camada de aplicativo dá suporte, consulte [Limites de Serviço de Aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Para dimensionar seu aplicativo para uma outra camada, a camada de destino deve suportar o número de slots que seu aplicativo já usa. Por exemplo, se o seu aplicativo tiver mais de 5 slots, você não poderá reduzi-lo para a camada **Padrão**, pois a camada **Padrão** suporta apenas 5 slots de implantação.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Adicionar um slot de implantação
O aplicativo deve estar em execução no tipo **Standard** ou **Premium** para que você habilite vários slots de implantação.

1. No [Portal do Azure](https://portal.azure.com/), abra a [folha de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de seu aplicativo.
2. Escolha a opção **Slots de implantação** e clique em **Adicionar Slot**.
   
    ![Adicionar um novo slot de implantação][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver no tipo **Standard** ou **Premium**, você receberá uma mensagem indicando os tipos compatíveis para habilitar a publicação em etapas. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do aplicativo antes de continuar.
   > 
   > 
3. Na folha **Adicionar um slot**, nomeie o slot e opte por clonar a configuração de aplicativo por meio de outro slot de implantação existente. Clique na marca de seleção para continuar.
   
    ![Fonte de configuração][ConfigurationSource1]
   
    Na primeira vez em que você adicionar um slot, você terá somente duas opções: clonar configuração do slot padrão em produção ou não clonar.
    Se já tiver criado vários slots, você poderá clonar a configuração de um slot diferente do que estiver em produção:
   
    ![Fontes de configuração][MultipleConfigurationSources]
4. Na folha de recursos do aplicativo, clique em **Slots de implantação**, clique em um slot de implantação para abrir a folha de recursos desse slot, que contém um conjunto de métricas e configuração como qualquer outro aplicativo. O nome do slot é exibido na parte superior da folha para lembrar que você está visualizando o slot de implantação.
   
    ![Título do slot de implantação][StagingTitle]
5. Clique na URL do aplicativo, na folha do slot. Observe que o slot de implantação tem seu próprio nome de host e também é um aplicativo em tempo real. Para limitar o acesso público ao slot de implantação, consulte [Aplicativo Web do Serviço de Aplicativo - bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não há nenhum conteúdo após a criação do slot de implantação. É possível implantar no slot a partir de uma ramificação diferente do repositório, ou mesmo de um repositório diferente. Você também pode alterar a configuração do slot. Use o perfil de publicação ou as credenciais de implantação associadas ao slot de implantação para atualizar o conteúdo.  Por exemplo, você pode [publicar neste slot com o git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Além disso, alguns elementos de configuração seguirão o conteúdo em uma permuta (não específicos do slot) enquanto outros elementos de configuração permanecerão no mesmo slot após uma permuta (específicos do slot). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais - como a versão do framework, 32/64 bits, Web sockets
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Configurações de monitoramento e diagnóstico
* Conteúdo de Trabalhos Web

**Configurações que não são permutadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Associações e certificados SSL
* Configurações de escala
* Agendadores de Trabalhos Web

Para definir uma cadeia de conexão ou configuração de aplicativo e fixar um slot (não trocado), acesse a folha **Configurações de Aplicativo** de um slot específico e, em seguida, selecione a caixa **Configuração do Slot** dos elementos de configuração que devem se fixar ao slot. Marcar um elemento de configuração como específico do slot tem o efeito de estabelecer esse elemento como não passível de troca, em todos os slots de implantação associados ao aplicativo.

![Configurações de slot][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Permute slots de implantação 
Você pode trocar os slots de implantação na exibição **Visão geral** ou **Slots de implantação** da folha de recursos do aplicativo.

> [!IMPORTANT]
> Antes de trocar um aplicativo por meio de um slot de implantação para produção, verifique se todas as configurações específicas de slot estão configuradas exatamente como você deseja tê-las no destino da troca.
> 
> 

1. Para trocar slots de implantação, clique no botão **Trocar** na barra de comandos do aplicativo ou na barra de comandos de um slot de implantação.
   
    ![Botão permutar][SwapButtonBar]

2. Verifique se a origem e o destino da permuta estão definidos corretamente. Geralmente, o destino da troca é o slot de produção. Clique em **OK** para concluir a operação. Quando a operação for concluída, os slots de implantação terão sido permutados.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para o tipo de troca **Troca com visualização**, veja [Troca com visualização (troca de várias fases)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Troca com visualização (troca de várias fases)

Troca com visualização, ou troca de várias fases, simplifica a validação de elementos de configuração específicos ao slot, como cadeias de conexão.
Para cargas de trabalho críticas, convém validar se o aplicativo está se comportando conforme o esperado quando a configuração do slot de produção é aplicada, e você deve executar essa validação *antes* de o aplicativo ser trocado para produção. A troca com visualização é o que você precisa.

> [!NOTE]
> Não há suporte para a troca com visualização em aplicativos Web no Linux.

Quando você usa a opção **Troca com visualização** (consulte [Trocar slots de implantação](#Swap)), o Serviço de Aplicativo faz o seguinte:

- Mantém o slot de destino inalterado para que a carga de trabalho existente nesse slot (como produção) não seja afetada.
- Aplica os elementos de configuração do slot de destino ao slot de origem, incluindo as configurações de aplicativo e as cadeias de conexão específicas ao slot.
- Reinicia os processos de trabalho no slot de origem usando esses elementos de configuração mencionados anteriormente.
- Quando você conclui a troca: move o slot de origem pré-preparado para o slot de destino. O slot de destino é movido para o slot de origem como em uma troca manual.
- Quando você cancela a troca: reaplica os elementos de configuração do slot de origem ao slot de origem.

Você pode visualizar exatamente como o aplicativo se comportará com a configuração do slot de destino. Após a conclusão da validação, você poderá completar a troca em outra etapa. Essa etapa tem a vantagem adicional de que o slot de origem já está preparado com a configuração desejada, e os clientes não enfrentarão nenhum tempo de inatividade.  

Exemplos de cmdlets do Azure PowerShell disponíveis para permuta multifase são incluídos nos cmdlets do Azure PowerShell para a seção de slots de implantação.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática
A Troca Automática simplifica cenários DevOps em q você deseja implantar continuamente seu aplicativo, sem nenhuma inicialização a frio nem tempo de inatividade para clientes finais do aplicativo. Quando um slot de implantação estiver configurado para Troca Automática para produção, sempre que você enviar por push a atualização de código para esse slot, o Serviço de Aplicativo trocará automaticamente o aplicativo para produção depois que ele já tiver feito sido preparado no slot.

> [!IMPORTANT]
> Ao habilitar a Permuta Automática para um slot, verifique se a configuração do slot é exatamente a configuração pretendida para o slot de destino (geralmente o slot de produção).
> 
> 

> [!NOTE]
> Não há suporte para a Troca Automática em aplicativos Web no Linux.

Configurar a Permuta Automática para um slot é fácil. Siga estas etapas:

1. Em **Slots de Implantação**, selecione um slot que não esteja em produção e escolha **Configurações do Aplicativo** na folha de recursos do slot.  
   
    ![][Autoswap1]
2. Selecione **Ativar** para **Troca Automática**, escolha o slot de destino desejado em **Slot de Troca Automática** e clique em **Salvar** na barra de comandos. Verifique se a configuração para o slot é exatamente igual à configuração desejada para o slot de destino.
   
    A guia **Notificações** piscará **SUCESSO** em verde quando a operação for concluída.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Para testar a Troca Automática para seu aplicativo, primeiro você poderá selecionar um slot de destino que não seja de produção em **Slot de Troca Automática** para se familiarizar com o recurso.  
   > 
   > 
3. Execute um envio de código por push para esse slot de implantação. A Troca Automática ocorrerá após um curto período de tempo, e a atualização será refletida na URL do seu slot de destino.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Reverter um aplicativo de produção após a troca
Se algum erro for identificado na produção após uma permuta de slot, reverta os slots para os estados pré-permuta permutando ambos os slots imediatamente.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Aquecimento personalizado antes da permuta
Alguns aplicativos podem exigir ações personalizadas de aquecimento. O elemento de configuração `applicationInitialization` no web.config permite que você especifique ações de inicialização personalizadas a serem executadas antes de uma solicitação ser recebida. A operação de troca aguarda esse aquecimento personalizado ser concluído. Este é está um exemplo fragmento do web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostname="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap-progress"></a>Monitorar o progresso da troca

Às vezes, a operação de troca leva algum tempo para ser concluída, por exemplo, quando o aplicativo que é trocado tem um tempo de aquecimento longo. Você pode obter mais informações sobre as operações de troca no [Log de Atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) no [portal do Azure](https://portal.azure.com).

Em sua página de aplicativo do portal, no painel de navegação esquerdo, selecione **Log de atividades**.

Uma operação de troca é exibida na consulta de log como `Slotsswap`. Você pode expandi-lo e selecionar uma das suboperações ou um dos erros para ver os detalhes.

![Log de atividades de troca de slot](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Excluir um slot de implantação
Na folha de um slot de implantação, abra a folha do slot de implantação, clique em **Visão geral** (a página padrão) e clique em **Excluir** na barra de comandos.  

![Excluir um slot de implantação][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatizar com o Azure PowerShell

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

* Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Criar um aplicativo Web
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Criar um slot de implantação
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Iniciar uma troca com versão prévia (troca de várias fases) e aplicar a configuração do slot de destino ao slot de origem
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Cancelar uma troca pendente (troca com revisão) e restaurar a configuração do slot de origem
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Permute slots de implantação
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorar eventos de troca Log de Atividades
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Exclua um slot de implantação
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatizar com a CLI do Azure

Para obter os comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [Slot de implantação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Próximas etapas
[Aplicativo Web do Serviço de Aplicativo do Azure – bloquear o acesso Web a slots de implantação não sejam de produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Introdução ao Serviço de Aplicativo no Linux](../app-service/containers/app-service-linux-intro.md)  
[Avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

