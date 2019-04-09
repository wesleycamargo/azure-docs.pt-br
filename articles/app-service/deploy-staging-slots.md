---
title: Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar a publicação em estágios para aplicativos Web no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 544ef8947f3a593071cabea018c722db96ab1475
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266198"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no Serviço de Aplicativo do Azure
<a name="Overview"></a>

> [!NOTE]
> Este guia de instruções mostra como gerenciar slots usando uma nova página de gerenciamento de versão prévia. Os clientes que estão familiarizados com a página de gerenciamento existente podem continuar usando essa página de gerenciamento de slots como antes. 
>

Quando você implanta seu aplicativo da web, aplicativo da web no Linux, back-end móvel e aplicativo de API no [App Service](https://go.microsoft.com/fwlink/?LinkId=529714), é possível implantar em um slot de implantação separado em vez do slot de produção padrão ao executar **Sites do plano Standard**, **Premium** ou **Isolado** Nível do plano de serviço do aplicativo. Os slots de implantação são, na verdade, aplicativos online com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. A implantação do aplicativo em um slot de não produção traz os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
* Implantar um aplicativo em um slot primeiro e alterná-lo para produção garantem que todas as instâncias do slot estejam aquecidas antes de alterná-lo para produção. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo. O redirecionamento de tráfego é contínuo, e nenhuma solicitação é removida devido a operações de alternância. Todo esse fluxo de trabalho pode ser automatizado pela configuração da [Alternância Automática](#Auto-Swap) quando a validação de pré-alternância não é necessária.
* Após a troca, o slot com o aplicativo de preparo anterior terá o aplicativo de produção anterior. Se as alterações alternadas para o slot de produção não correspondem às suas expectativas, você pode realizar a mesma alternância imediatamente para ter o "último site válido conhecido" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Para descobrir o número de slots que a sua camada de aplicativo dá suporte, consulte [Limites de Serviço de Aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Para dimensionar seu aplicativo para uma outra camada, a camada de destino deve suportar o número de slots que seu aplicativo já usa. Por exemplo, se o aplicativo tiver mais de cinco slots, você não poderá reduzi-lo verticalmente para a camada **Standard**, pois a camada **Standard** dá suporte a apenas cinco slots de implantação.

<a name="Add"></a>

## <a name="add-slot"></a>Adicionar um slot
O aplicativo precisa estar em execução na camada **Standard**, **Premium** ou **Isolado** para que seja possível habilitar vários slots de implantação.

1. No [Portal do Azure](https://portal.azure.com/), abra a [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources) do seu aplicativo.

2. No painel de navegação à esquerda, escolha a opção **Slots de implantação (Versão Prévia)** e, em seguida, clique em **Adicionar Slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver na camada **Standard**, **Premium** ou **Isolado**, você receberá uma mensagem indicando as camadas compatíveis para habilitar a publicação em etapas. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do aplicativo antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar um slot**, dê um nome ao slot e escolha se deseja clonar a configuração de aplicativo de outro slot de implantação existente. Clique em **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    É possível clonar a configuração de qualquer slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, cadeias de conexão, versões da estrutura de linguagem, soquetes da Web, versão HTTP e número de bits da plataforma.

4. Depois que o slot for adicionado, clique em **Fechar** para fechar a caixa de diálogo. O novo slot agora é mostrado na página **Slots de implantação (Versão Prévia)**. Por padrão, o **% do Tráfego** está definido como 0 para o novo slot, com todo o tráfego de clientes roteado para o slot de produção.

5. Clique no novo slot de implantação para abrir a página de recursos desse slot.
   
    ![Título do slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de preparo tem uma página de gerenciamento como qualquer outro aplicativo do Serviço de Aplicativo. É possível alterar a configuração do slot. O nome do slot é mostrado na parte superior da página para lembrá-lo de que você está exibindo o slot de implantação.

6. Clique na URL do aplicativo na página de recursos do slot. O slot de implantação tem seu próprio nome do host e também é um aplicativo dinâmico. Para limitar o acesso público ao slot de implantação, confira [Restrições de IP do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md).

O novo slot de implantação não tem nenhum conteúdo, mesmo se as configurações são clonadas de outro slot. Por exemplo, você pode [publicar neste slot com o git](app-service-deploy-local-git.md). É possível fazer uma implantação no slot de outro branch do repositório ou de outro repositório. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Além disso, alguns elementos de configuração seguem o conteúdo em uma alternância (não específicos do slot), enquanto outros permanecem no mesmo slot após uma alternância (específicos do slot). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais - como a versão do framework, 32/64 bits, Web sockets
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Configurações de monitoramento e diagnóstico
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos de extremidade de serviço *
* A CDN do Azure *

Recursos marcados com um * estão planejadas para ficar adesivo no slot. 

**Configurações que não são alternadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Associações SSL e certificados privados
* Configurações de escala
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações do protocolo (HTTP**S**, versão do TLS, certificados de cliente)
* Configurações de log de diagnóstico
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Para definir uma configuração de aplicativo ou uma cadeia de conexão para que permaneçam em um slot específico (não alternado), navegue para a página **Configurações de aplicativo** desse slot e, em seguida, marque a caixa **Configuração do Slot** para os elementos de configuração que devem permanecer no slot. A marcação de um elemento de configuração como específico do slot informa ao Serviço de Aplicativo de que ele não é alternável. 

![Configuração do slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots 
Alterne os slots de implantação na página **Slots de implantação (Versão Prévia)** do aplicativo. 

Você também pode alternar os slots nas páginas **Visão Geral** e **Slots de implantação**, mas atualmente elas fornecem a experiência antiga. Este guia mostra como usar a nova interface do usuário na página **Slots de implantação (Versão Prévia)**.

> [!IMPORTANT]
> Antes de alternar um aplicativo de um slot de implantação para produção, verifique se todas as configurações estão definidas exatamente como você deseja tê-las no destino da alternância.
> 
> 

Para alternar os slots de implantação, siga estas etapas:

1. Navegue para a página **Slots de implantação (Versão Prévia)** do aplicativo e clique em **Alternância**.
   
    ![Botão permutar](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **Alternância** mostra as configurações nos slots de origem e de destino selecionados que serão alterados.

2. Selecione os slots de **Origem** e de **Destino** desejados. Geralmente, o destino é o slot de produção. Além disso, clique nas guias **Alterações na Origem** e **Alterações no Destino** e verifique se as alterações de configuração são esperadas. Quando terminar, você poderá alternar os slots imediatamente clicando em **Alternância**.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o slot de destino será executado com as novas configurações antes que a alternância realmente ocorra, não clique em **Alternância**, mas siga as instruções descritas em [Alternância com visualização](#Multi-Phase).

3. Quando terminar, feche a caixa de diálogo clicando em **Fechar**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com visualização (troca de várias fases)

> [!NOTE]
> Não há suporte para a alternância com visualização em aplicativos Web no Linux.

Antes de alternar para produção como o slot de destino, valide se o aplicativo está sendo executado com as configurações alternadas antes que a alternância ocorra. O slot de origem também é aquecido antes da conclusão da alternância, o que também é desejável para aplicativos críticos.

Quando você executa uma alternância com visualização, o Serviço de Aplicativo faz o seguinte quando a alternância é iniciada:

- Mantém o slot de destino inalterado, de modo que a carga de trabalho existente nesse slot (como produção) não seja afetada.
- Aplica os elementos de configuração do slot de destino ao slot de origem, incluindo as configurações de aplicativo e as cadeias de conexão específicas do slot.
- Reinicia os processos de trabalho no slot de origem usando esses elementos de configuração. Procure o slot de origem e veja o aplicativo ser executado com as alterações de configuração.

Se você concluir a alternância em uma etapa separada, o Serviço de Aplicativo moverá o slot de origem aquecido para o slot de destino e o slot de destino para o slot de origem. Se você cancelar a alternância, o Serviço de Aplicativo aplicará novamente os elementos de configuração do slot de origem ao slot de origem.

Para realizar a alternância com visualização, siga estas etapas.

1. siga as etapas descritas em [Alternar slots de implantação](#Swap), mas selecione **Realizar alternância com visualização**.

    ![Alternância com visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem é alterada na fase 1 e como os slots de origem e de destino são alterados na fase 2.

2. Quando estiver pronto para iniciar a alternância, clique em **Iniciar Alternância**.

    Quando a fase 1 for concluída, você será notificado na caixa de diálogo. Visualize a alternância no slot de origem navegando para `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando estiver pronto para concluir a alternância pendente, selecione **Concluir Alternância** em **Ação de alternância** e clique em **Concluir Alternância**.

    Para cancelar uma alternância pendente, selecione **Cancelar Alternância** e clique em **Cancelar Alternância**.

4. Quando terminar, feche a caixa de diálogo clicando em **Fechar**.

Para automatizar uma alternância de várias fases, confira Automatização com o PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Reverter a alternância
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma alternância de slot, restaure os slots para seus estados de pré-alternância alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> Não há suporte para a Alternância Automática em aplicativos Web no Linux.

A Alternância Automática simplifica cenários de DevOps em que você deseja implantar seu aplicativo continuamente, sem inicialização a frio e tempo de inatividade para os clientes finais do aplicativo. Quando um slot é alternado automaticamente para produção, sempre que você envia por push as alterações de código para esse slot, o Serviço de Aplicativo alterna o aplicativo automaticamente para produção depois que ele é aquecido no slot de origem.

   > [!NOTE]
   > Antes de configurar a Alternância Automática para o slot de produção, considere a possibilidade de testar a Alternância Automática em um slot de destino de não produção primeiro.
   > 

Para configurar a Alternância Automática, siga estas etapas:

1. Navegue para a página de recursos do aplicativo. Selecione **Slots de implantação (Versão Prévia)** > *\<slot de origem desejado>* > **Configurações de aplicativo**.
   
2. Em **Alternância Automática**, selecione **Ativado**, selecione o slot de destino desejado em **Slot de Alternância Automática** e clique em **Salvar** na barra de comandos. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. A Troca Automática ocorrerá após um curto período de tempo, e a atualização será refletida na URL do seu slot de destino.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Aquecimento personalizado
Ao usar a [Alternância Automática](#Auto-Swap), alguns aplicativos podem exigir ações de aquecimento personalizado antes da alternância. O elemento de configuração `applicationInitialization` em web.config permite que você especifique ações de inicialização personalizada a serem executadas. A operação de alternância aguarda a conclusão desse aquecimento personalizado antes da alternância com o slot de destino. Este é está um exemplo fragmento do web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações sobre como personalizar o `applicationInitialization` elemento, consulte [falhas de permuta do slot de implantação mais comuns e como corrigi-los](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma ou mais das seguintes [configurações do aplicativo](web-sites-configure.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para executar o ping para aquecer seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começa com uma barra (“/”) como o valor. Por exemplo, `/statuscheck`. O valor padrão é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista separada por vírgulas dos códigos HTTP. Por exemplo: `200,202`. Se o código de status retornado não estiver na lista, as operações de aquecimento e de troca são interrompidas. Por padrão, todos os códigos de resposta são válidos.

## <a name="monitor-swap"></a>Monitorar a alternância

Se a operação de alternância levar muito tempo para ser concluída, você poderá obter informações sobre a operação de alternância no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos do aplicativo no portal, no painel de navegação à esquerda, selecione **Log de atividades**.

Uma operação de troca é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-lo e selecionar uma das suboperações ou um dos erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear o tráfego

Por padrão, todas as solicitações de cliente para a URL de produção do aplicativo (`http://<app_name>.azurewebsites.net`) são roteadas para o slot de produção. É possível rotear uma parte do tráfego para outro slot. Esse recurso é útil se você precisa de comentários do usuário para uma nova atualização, mas não está pronto para liberá-la para produção.

### <a name="route-production-traffic-automatically"></a>Rotear o tráfego de produção automaticamente

Para rotear o tráfego de produção automaticamente, siga estas etapas:

1. Navegue para a página de recursos do aplicativo e selecione **Slots de implantação (Versão Prévia)**.

2. Na coluna **% do Tráfego** do slot para o qual você deseja rotear, especifique um percentual (entre 0 e 100) para representar a quantidade de tráfego total que deseja rotear. Clique em **Salvar**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração é salva, o percentual especificado de clientes é roteado aleatoriamente para o slot de não produção. 

Depois que um cliente é roteado automaticamente para um slot específico, ele é "fixado" nesse slot durante toda essa sessão do cliente. No navegador do cliente, você pode ver em qual slot a sessão está fixada observando o cookie `x-ms-routing-name` nos cabeçalhos HTTP. Uma solicitação roteada para o slot "de preparo" tem o cookie `x-ms-routing-name=staging`. Uma solicitação roteada para o slot de produção tem o cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Rotear o tráfego de produção manualmente

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode rotear solicitações para um slot específico. Isso é útil quando você deseja que os usuários consigam aceitar ou recusar seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de consulta `x-ms-routing-name`.

Para permitir que os usuários recusem seu aplicativo beta, é possível colocar este link na página da Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` especifica o local de produção. Quando o navegador do cliente acessar o link, ele não só será redirecionado para o slot de produção, mas todas as solicitações seguintes conterão o cookie `x-ms-routing-name=self` que fixa a sessão no slot de produção.

Para permitir que os usuários aceitem seu aplicativo beta, defina o mesmo parâmetro de consulta como o nome do slot de não produção, por exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>Excluir um slot

Navegue para a página de recursos do aplicativo. Selecione **Slots de implantação (Versão Prévia)** > *\<slot a ser excluído>* > **Visão Geral**. Clique em **Excluir** na barra de comandos.  

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Criar um aplicativo Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Criar um slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Iniciar uma alternância com visualização (alternância de várias fases) e aplicar a configuração do slot de destino ao slot de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Cancelar uma alternância pendente (alternância com revisão) e restaurar a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Permute slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorar eventos de alternância no Log de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Excluir um slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatização com a CLI

Para obter os comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [Slot de implantação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Próximos passos
[Bloquear o acesso a slots de não produção](app-service-ip-restrictions.md)
