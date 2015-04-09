<properties
	pageTitle="Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure"
	description="Saiba como usar a publicação em estágios para aplicativos Web no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

<a name="Overview"></a>
# Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure

Quando você implantar seu aplicativo Web no [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714), você pode implantar em um slot de implantação separado em vez do slot de produção padrão ao ser executado no modo do plano de Serviço de Aplicativo **Padrão** ou **Premium**. Slots de implantação são, na verdade, aplicativos Web online com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo Web podem ser trocados entre dois slots de implantação, incluindo o slot de produção. Implantar o seu aplicativo em um slot de implantação tem os seguintes benefícios:

- É possível validar as alterações no aplicativo Web em um slot de implantação de preparo antes de trocá-lo pelo slot de produção.

- Implantar um aplicativo Web em um slot inicial e depois trocá-lo, enviando-o para produção, garante que todas as instâncias do slot estejam prontas antes dessa troca. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo Web. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de troca. Todo esse fluxo de trabalho pode ser automatizado ao se configurar [Troca automática](#Configure-Auto-Swap-for-your-web-app) quando a validação prévia à troca não é necessária.

- Após uma troca, o slot com o aplicativo Web preparado anteriormente tem agora o aplicativo Web de produção anterior. Se as alterações trocadas e enviadas ao slot de produção não forem o que você esperava, é possível fazer a mesma permuta imediatamente para ter o "último site bom conhecido" de volta.

Cada modo de plano de Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Para descobrir o número de slots ao qual seu aplicativo Web dá suporte, consulte [Preços de Serviço de Aplicativo](/pricing/details/app-service/). 

- Quando seu aplicativo Web tem vários slots, você não pode alterar o modo.

- O dimensionamento não está disponível para slots de não produção.

- O gerenciamento de recurso vinculado não tem suporte para slots de não produção.

	> [AZURE.NOTE] Somente no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) você pode evitar esse impacto potencial em um slot de produção, mudando temporariamente o slot de não produção para um modo de plano de Serviço de Aplicativo diferente. Observe que o slot de não produção deve uma vez mais compartilhar o mesmo modo com o slot de produção antes que você possa alternar os dois slots.

<a name="Add"></a>
## Adicionar um slot de implantação a um aplicativo Web ##

O aplicativo Web deve estar em execução no modo **Padrão** ou **Premium** para que você habilite vários slots de implantação.

1. No [Portal de Visualização do Azure](https://portal.azure.com/), abra a lâmina de seu aplicativo Web.
2. Clique em **Slots de implantação**. Em seguida, na lâmina **Slots de implantação**, clique em **Adicionar Slot**.

	![Add a new deployment slot][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	> Se o aplicativo Web ainda não está no modo **Padrão** ou **Premium**, você receberá uma mensagem indicando os modos com suporte para habilitar a publicação em estágios. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do aplicativo Web antes de continuar.

2. Na lâmina **Adicionar um slot**, nomeie o slot e opte por clonar a configuração de aplicativo Web por meio de outro slot de implantação existente. Clique na marca de seleção para continuar.

	![Configuration Source][ConfigurationSource1]

	Na primeira vez em que você adicionar um slot, você terá somente duas opções: clonar configuração do slot padrão em produção ou não clonar.

	Se já tiver criado vários slots, você poderá clonar a configuração de um slot diferente do que estiver em produção:

	![Configuration sources][MultipleConfigurationSources]

5. Na lâmina **Slots de implantação**, clique no slot de implantação para abrir uma lâmina para o slot, com um conjunto de métricas e configuração exatamente como aqueles de qualquer outro aplicativo Web. <strong><i>nome-do-seu-aplicativo-Web</i>-<i>nome-do-slot-de-implantação</i></strong> aparecerá na parte superior da lâmina para lembrá-lo que você está visualizando o slot de implantação.

	![Deployment Slot Title][StagingTitle]

5. Clique na URL do aplicativo, na lâmina do slot. Observe que o slot de implantação tem seu próprio nome de host e também é um aplicativo em tempo real. Para limitar o acesso público ao slot de implantação, consulte [Aplicativo Web do Serviço de Aplicativo - bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não há nenhum conteúdo após a criação do slot de implantação. É possível implantar no slot a partir de uma ramificação diferente do repositório, ou mesmo de um repositório diferente. Você também pode alterar a configuração do slot. Use o perfil de publicação ou as credenciais de implantação associadas ao slot de implantação para atualizar o conteúdo.  Por exemplo, você pode [publicar nesse slot com o git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configuração de slots de implantação ##
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Além disso, alguns elementos de configuração seguirão o conteúdo em uma troca (não específicos do slot) enquanto outros elementos de configuração permanecerão no mesmo slot após uma troca (específicos do slot). A lista a seguir mostra a configuração que será alterada com a troca de slots.

**Configurações que são trocadas**:

- Configurações gerais - como a versão do framework, 32/64 bits, Web sockets
- Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
- Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
- Mapeamentos de manipulador
- Configurações de monitoramento e diagnóstico
- Conteúdo de Trabalhos Web

**Configurações que não são trocadas**:

- Pontos de extremidade de publicação
- Nomes de domínio personalizados
- Associações e certificados SSL
- Configurações de escala
- Agendadores de Trabalhos Web

Para configurar uma cadeia de conexão ou configuração de aplicativo para que se fixe a um slot (não trocada), acesse a lâmina **Configurações do aplicativo** para um slot específico e selecione a caixa **Configuração do slot** para os elementos de configuração que devem se fixar ao slot. Observe que marcar um elemento de configuração como específico do slot tem o efeito de estabelecer esse elemento como não passível de troca, em todos os slots de implantação associados ao aplicativo Web.

![Slot settings][SlotSettings]

<a name="Swap"></a>
## Para permutar slots de implantação ##

>[AZURE.IMPORTANTE] antes de permutar um aplicativo Web por meio de um slot de implantação para produção, certifique-se de que todas as configurações específicas de slot estejam configuradas exatamente como você deseja tê-las no destino da troca.

1. Para trocar slots de implantação, clique no botão **Permutar** na barra de comandos do aplicativo Web ou na barra de comandos de um slot de implantação. Certifique-se de que a origem e o destino da permutação estejam definidos corretamente. Geralmente, o destino da troca seria o slot de produção.  

	![Swap Button][SwapButtonBar]

3. Clique em **OK** para concluir a operação. Quando a operação for concluída, os slots de implantação terão sido permutados.

## Configurar a troca automática para seu aplicativo Web ##

A Troca automática simplifica cenários DevOps em q você deseja implantar continuamente seu aplicativo Web, sem nenhuma inicialização a frio nem tempo de inatividade para clientes finais do aplicativo Web. Quando um slot de implantação estiver configurado para Permutação Automática em produção, sempre que você enviar por push a atualização de código para esse slot, o Serviço de Aplicativo permutará automaticamente o aplicativo Web para produção depois que ele já tiver feito seu aquecimento no slot.

>[AZURE.IMPORTANTE] quando você habilita a Permutação Automática para um slot, verifique se a configuração do slot é exatamente a configuração pretendida para o slot de destino (geralmente o slot de produção).

Configurar a Permutação Automática para um slot é fácil. Siga as etapas abaixo:

1. Na lâmina **Slots de implantação**, selecione um slot que não seja de produção e clique em **Todas as configurações** para a lâmina desse slot.  

	![][Autoswap1]

2. Clique em **Configurações do aplicativo**. Selecione **Ativar** para **Permutação automática**, selecione o slot de destino desejado em **Slot de permutação automática** e clique em **Salvar** na barra de comandos. Certifique-se de que a configuração para o slot é exatamente igual à configuração que se pretende usar no slot de destino.

	Na guia **Notificações**, a mensagem **SUCESSO** piscará em verde quando a operação for concluída.

	![][Autoswap2]

	>[AZURE.NOTE] Para testar a Permutação automática para seu aplicativo Web, primeiro você poderá selecionar um slot de destino que não seja de produção em **Slot de permutação automática** para se familiarizar com o recurso.  

3. Execute um envio de código por push para esse slot de implantação. A Permutação automática ocorrerá após um curto período de tempo e a atualização será refletida na URL do seu slot de destino.

<a name="Rollback"></a>
## Para reverter um aplicativo de produção após a troca ##
Se algum erro for identificado na produção após uma permutação de slot, reverta os slots para os estados pré-permutação trocando os mesmos dois slots imediatamente.

<a name="Delete"></a>
## Para excluir um slot de implantação ##

Na lâmina para um slot de implantação, clique em **Excluir** na barra de comandos.  

![Delete a Deployment Slot][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets do PowerShell do Azure para slots de implantação

O PowerShell do Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação de aplicativos Web no Serviço de Aplicativo do Azure.

- Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](install-configure-powershell.md).  

- Para listar os cmdlets disponíveis para o Serviço de Aplicativo do Azure no PowerShell, chame `help AzureWebsite`.

----------

### Get-AzureWebsite
O cmdlet **Get-AzureWebsite** apresenta informações sobre aplicativos Web do Azure da assinatura atual, como no exemplo a seguir.

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
Você pode criar um slot de implantação usando o cmdlet **New-AzureWebsite** e especificando os nomes tanto do aplicativo Web quanto do slot. Indique também a mesma região que a do aplicativo Web para a criação do slot de implantação, conforme demonstrado no exemplo a seguir.

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
Você pode usar o cmdlet **Publish-AzureWebsiteProject** para implantação de conteúdo, como no exemplo a seguir.

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
Após as atualizações do conteúdo e da configuração terem sido aplicadas ao novo slot, você pode validar as atualizações navegando até o slot usando o cmdlet **Show-AzureWebsite**, como no exemplo a seguir.

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
O cmdlet **Show-AzureWebsiteSlot** pode executar uma operação de permuta para tornar o slot de implantação atualizado no site de produção, como no exemplo a seguir. O aplicativo de produção não passará por nenhuma experiência de tempo de inatividade, nem passará por uma inicialização a frio.

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
Se um slot de implantação não for mais necessário, ele poderá ser excluído usando o cmdlet **Remove-AzureWebsite**, como no exemplo a seguir.

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Comandos da interface de linha de comando entre plataformas do Azure (xplat-cli) para slots de implantação

A Interface de linha de comando entre plataformas do Azure (xplat-cli) fornece comandos entre plataformas para trabalhar com o Azure, incluindo suporte ao gerenciamento de slots de implantação de aplicativo Web.

- Para obter instruções sobre como instalar e configurar a xplat-cli, incluindo informações sobre como conectar a xplat-cli com sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](xplat-cli.md).

-  Para listar os comandos disponíveis para o Serviço de Aplicativo do Azure na xplat-cli, chame `azure site -h`.

----------
### azure site list
Para obter informações sobre aplicativos Web na assinatura atual, chame **azure site list**, como no exemplo a seguir.

`azure site list webappslotstest`

----------
### azure site create
Para criar um slot de implantação, chame **criar site do azure** e especifique o nome de um aplicativo Web existente e o nome do slot para criar, como no exemplo a seguir.

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

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas Etapas ##
[Aplicativo Web do Serviço de Aplicativo do Azure - bloquear o acesso Web a slots de implantação não sejam de produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Avaliação gratuita do Microsoft Azure](/pricing/free-trial/)

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=49-->