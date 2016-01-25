<properties
	pageTitle="Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure"
	description="Saiba como usar a publicação em estágios para aplicativos Web no Serviço de Aplicativo do Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>

# Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure
<a name="Overview"></a>

Ao implantar seu aplicativo Web no [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714), você pode implantar em um slot de implantação separado em vez do slot de produção padrão ao ser executado no modo do plano do Serviço de Aplicativo **Padrão** ou **Premium**. Slots de implantação são, na verdade, aplicativos Web online com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo Web podem ser permutados entre dois slots de implantação, incluindo o slot de produção. Implantar o seu aplicativo em um slot de implantação tem os seguintes benefícios:

- É possível validar as alterações no aplicativo Web em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.

- Implantar um aplicativo Web em um slot inicial e depois permutá-lo, enviando-o para produção, garante que todas as instâncias do slot estejam prontas antes dessa permuta. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo Web. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de permuta. Todo esse fluxo de trabalho pode ser automatizado por meio da configuração de [Permuta Automática](#configure-auto-swap-for-your-web-app) quando a validação de pré-permuta não é necessária.

- Após uma permuta, o slot com o aplicativo Web preparado anteriormente tem agora o aplicativo Web de produção anterior. Se as alterações permutadas no slot de produção não forem o que você esperava, é possível fazer a mesma permuta imediatamente para ter o "último site bom" de volta.

Cada modo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Para descobrir o número de slots ao qual seu aplicativo Web dá suporte, consulte [Preços do Serviço de Aplicativo](/pricing/details/app-service/).

- Quando seu aplicativo Web tem vários slots, você não pode alterar o modo.

- O dimensionamento não está disponível para slots de não produção.

- O gerenciamento de recurso vinculado não tem suporte para slots de não produção. Somente no[ Portal do Azure ](http://go.microsoft.com/fwlink/?LinkId=529715)você pode evitar esse impacto potencial em um slot de produção, mudando temporariamente o slot de não produção para um modo de plano de Serviço de Aplicativo diferente. Observe que o slot de não produção deve uma vez mais compartilhar o mesmo modo com o slot de produção antes que você possa alternar os dois slots.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## Adicionar um slot de implantação a um aplicativo Web ##

O aplicativo Web deve estar em execução no modo **Padrão** ou **Premium** para que você habilite vários slots de implantação.

1. No [Portal do Azure](https://portal.azure.com/), abra a folha de seu aplicativo Web.
2. Clique em **Configurações** e então em **Slots de implantação**. Em seguida, na folha **Slots de implantação**, clique em **Adicionar Slot**.

	![Adicionar um novo slot de implantação][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]Se o aplicativo Web ainda não está no modo **Padrão** ou **Premium**, você receberá uma mensagem indicando os modos com suporte para habilitar a publicação em estágios. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do aplicativo Web antes de continuar.

2. Na folha **Adicionar um slot**, nomeie o slot e opte por clonar a configuração de aplicativo Web por meio de outro slot de implantação existente. Clique na marca de seleção para continuar.

	![Fonte de configuração][ConfigurationSource1]

	Na primeira vez em que você adicionar um slot, você terá somente duas opções: clonar configuração do slot padrão em produção ou não clonar.

	Se já tiver criado vários slots, você poderá clonar a configuração de um slot diferente do que estiver em produção:

	![Fontes de configuração][MultipleConfigurationSources]

5. Na folha **Slots de implantação**, clique no slot de implantação para abrir uma folha para o slot, com um conjunto de métricas e configuração como qualquer outro aplicativo Web. **nome-do-seu-aplicativo-web-nome-do-slot-de-implantação** aparecerá na parte superior da folha para lembrá-lo de que você está exibindo o slot de implantação.

	![Título do slot de implantação][StagingTitle]

5. Clique na URL do aplicativo, na folha do slot. Observe que o slot de implantação tem seu próprio nome de host e também é um aplicativo em tempo real. Para limitar o acesso público ao slot de implantação, consulte [Aplicativo Web do Serviço de Aplicativo - bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não há nenhum conteúdo após a criação do slot de implantação. É possível implantar no slot a partir de uma ramificação diferente do repositório, ou mesmo de um repositório diferente. Você também pode alterar a configuração do slot. Use o perfil de publicação ou as credenciais de implantação associadas ao slot de implantação para atualizar o conteúdo. Por exemplo, você pode [publicar neste slot com o git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configuração de slots de implantação ##
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Além disso, alguns elementos de configuração seguirão o conteúdo em uma permuta (não específicos do slot) enquanto outros elementos de configuração permanecerão no mesmo slot após uma permuta (específicos do slot). A lista a seguir mostra a configuração que será alterada com a permuta de slots.

**Configurações que são permutadas**:

- Configurações gerais - como a versão do framework, 32/64 bits, Web sockets
- Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
- Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
- Mapeamentos de manipulador
- Configurações de monitoramento e diagnóstico
- Conteúdo de Trabalhos Web

**Configurações que não são permutadas**:

- Pontos de extremidade de publicação
- Nomes de domínio personalizados
- Associações e certificados SSL
- Configurações de escala
- Agendadores de Trabalhos Web

Para configurar uma cadeia de conexão ou configuração de aplicativo para que se fixe a um slot (não permutada), acesse a folha **Configurações** do aplicativo para um slot específico e marque a caixa **Configuração do slot** para os elementos de configuração que devem se fixar ao slot. Observe que marcar um elemento de configuração como específico do slot tem o efeito de estabelecer esse elemento como não passível de permuta, em todos os slots de implantação associados ao aplicativo Web.

![Configurações de slot][SlotSettings]

<a name="Swap"></a>
## Para permutar slots de implantação ##

>[AZURE.IMPORTANT]Antes de permutar um aplicativo Web por meio de um slot de implantação para produção, verifique se todas as configurações específicas de slot estão configuradas exatamente como você deseja tê-las no destino da permuta.

1. Para permutar slots de implantação, clique no botão **Permutar** na barra de comandos do aplicativo Web ou na barra de comandos de um slot de implantação. Verifique se a origem e o destino da permuta estão definidos corretamente. Geralmente, o destino da permuta seria o slot de produção.  

	![Botão permutar][SwapButtonBar]

3. Clique em **OK** para concluir a operação. Quando a operação for concluída, os slots de implantação terão sido permutados.

## Configurar a Permuta Automática para seu aplicativo Web ##

A Permuta Automática simplifica cenários DevOps em q você deseja implantar continuamente seu aplicativo Web, sem nenhuma inicialização a frio nem tempo de inatividade para clientes finais do aplicativo Web. Quando um slot de implantação estiver configurado para Permutação Automática em produção, sempre que você enviar por push a atualização de código para esse slot, o Serviço de Aplicativo permutará automaticamente o aplicativo Web para produção depois que ele já tiver feito seu aquecimento no slot.

>[AZURE.IMPORTANT]Ao habilitar a Permuta Automática para um slot, verifique se a configuração do slot é exatamente a configuração pretendida para o slot de destino (geralmente o slot de produção).

Configurar a Permuta Automática para um slot é fácil. Siga as etapas abaixo:

1. Na folha **Slots de Implantação**, selecione um slot que não seja de produção e clique em **Todas as configurações** para a folha desse slot.  

	![][Autoswap1]

2. Clique em **Configurações do Aplicativo**. Selecione **Ativar** para **Permutação automática**, selecione o slot de destino desejado em **Slot de permutação automática** e clique em **Salvar** na barra de comandos. Verifique se a configuração para o slot é exatamente igual à configuração desejada para o slot de destino.

	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída.

	![][Autoswap2]

	>[AZURE.NOTE]Para testar a Permuta Automática para seu aplicativo Web, primeiro você poderá selecionar um slot de destino que não seja de produção em **Slot de Permuta Automática** para se familiarizar com o recurso.

3. Execute um envio de código por push para esse slot de implantação. A Permuta Automática ocorrerá após um curto período de tempo e a atualização será refletida na URL do seu slot de destino.

<a name="Multi-Phase"></a>
## Usar permuta multifase para seu aplicativo Web ##

A permuta multifase está disponível para simplificar a validação no contexto de elementos de configuração criados para permanecer em um slot, como cadeias de conexão. Nesses casos, é útil aplicar esses elementos de configuração do alvo de permuta à origem da permuta e validar antes da permuta realmente entrar em vigor. Depois que os elementos de configuração do alvo de permuta são aplicados à origem de permuta, as ações disponíveis são concluir a permuta ou reverter para a configuração original para a origem de permuta, o que também tem o efeito de cancelar a permuta. Exemplos de cmdlets do Azure PowerShell disponíveis para permuta multifase são incluídos nos cmdlets do Azure PowerShell para a seção de slots de implantação.

<a name="Rollback"></a>
## Para reverter um aplicativo de produção após a permuta ##
Se algum erro for identificado na produção após uma permuta de slot, reverta os slots para os estados pré-permuta permutando ambos os slots imediatamente.

<a name="Delete"></a>
## Para excluir um slot de implantação##

Na folha para um slot de implantação, clique em **Excluir** na barra de comandos.

![Excluir um slot de implantação][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets do PowerShell do Azure para slots de implantação

O PowerShell do Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação de aplicativos Web no Serviço de Aplicativo do Azure.

- Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](../install-configure-powershell.md).  

- Para usar o novo modo do Gerenciador de Recursos do Azure para cmdlets do PowerShell, comece pelo seguinte: `Switch-AzureMode -Name AzureResourceManager`.

----------

### Criar um aplicativo Web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Crie um slot de implantação para um aplicativo Web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -SlotName [deployment slot name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Inicie a permuta multifase e aplique a configuração de slot de destino ao slot de origem

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Reverta a primeira fase da permuta multifase e restaure a configuração do slot de origem

`Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01`

----------

### Permute slots de implantação

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Exclua um slot de implantação

`Remove-AzureResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01`

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## Comandos da interface de linha de comando do Azure (CLI do Azure) para slots de implantação

A CLI do Azure fornece comandos entre plataformas para trabalhar com o Azure, incluindo suporte ao gerenciamento de slots de implantação de aplicativo Web.

- Para obter instruções sobre como instalar e configurar a CLI do Azure, incluindo informações sobre como conectar a CLI do Azure com sua assinatura do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

-  Para listar os comandos disponíveis para o Serviço de Aplicativo do Azure na CLI do Azure, chame `azure site -h`.

----------
### azure site list
Para obter informações sobre aplicativos Web na assinatura atual, chame **azure site list**, como no exemplo a seguir.

`azure site list webappslotstest`

----------
### azure site create
Para criar um slot de implantação, chame **azure site create** e especifique o nome de um aplicativo Web existente e o nome do slot para criar, como no exemplo a seguir.

`azure site create webappslotstest --slot staging`

Para habilitar o controle do código-fonte para o novo slot, use a opção **--git**, como no exemplo a seguir.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Para fazer com que o slot de implantação atualizado se torne o aplicativo de produção, use o comando **azure site swap** para executar uma operação de permuta, como no exemplo a seguir. O aplicativo de produção não passará por nenhuma experiência de tempo de inatividade, nem passará por uma inicialização a frio.

`azure site swap webappslotstest`

----------
### azure site delete
Para excluir um slot de implantação que não seja mais necessário, use o comando **excluir de site azure**, como no exemplo a seguir.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas ##
[Aplicativo Web do Serviço de Aplicativo do Azure – bloquear o acesso Web a slots de implantação não sejam de produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Avaliação gratuita do Microsoft Azure](/pricing/free-trial/)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=AcomDC_0114_2016-->