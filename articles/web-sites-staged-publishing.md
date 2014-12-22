<properties urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Preparação de implantação nos sites do Microsoft Azure" metaKeywords="Sites do Microsoft Azure, implantação de preparo, slots de site" description="Learn how to use staged publishing on Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Websites" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="9/9/2014" ms.author="cephalin" />

<a name="Overview"></a>
#Implantação de preparo em sites do Microsoft Azure#
Ao implantar seu aplicativo nos sites do Azure, você pode implantar em um slot de implantação separado ao invés do slot de produção padrão e ambos são, na verdade, sites ao vivo com seus próprios nomes do host. Essa opção está disponível no modo **Padrão** do plano de hospedagem na Web. Além disso, você pode permutar os sites e as configurações de site entre dois slots de implantação, incluindo o slot de produção. Implantar o seu aplicativo em um slot de implantação tem os seguintes benefícios:

- É possível validar as alterações no site em um slot de implantação de preparo antes de permutá-lo com o slot de produção.

- Depois da permuta, o slot com o antigo site de preparo tem agora o antigo site de produção. Se as alterações permutadas no slot de produção não forem o que você esperava, é possível fazer a mesma permuta imediatamente para ter o "último site bom" de volta. 
 
- Implantar um site em um slot inicial e depois permutá-lo para produção garante que todas as instâncias do slot estejam prontas antes de serem permutadas para produção. Isso elimina o tempo de inatividade quando você for implantar seu site. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de permuta. 

O plano **Padrão** dá suporte a quatro slots de implantação para cada site, além o slot de produção. 

## Sumário ##
- [Para adicionar um slot de implantação a um site](#Add)
- [Sobre a configuração de slots de implantação](#AboutConfiguration)
- [Para permutar slots de implantação](#Swap)
- [Reverter um site de produção para preparo](#Rollback)
- [Para excluir um slot de site](#Delete)
- [Cmdlets do PowerShell do Azure para slots de site](#PowerShell)
- [Comandos da interface de linha de comando entre plataformas do Azure (xplat-cli) para slots de site](#CLI)

<a name="Add"></a>
##Para adicionar um slot de implantação a um site##

O site deve estar sendo executado no plano de hospedagem **Padrão** para habilitar o uso de vários slots de implantação. 

1. Na página Início Rápido ou na seção Visão Rápida na página Painel do seu site, clique em **Adicionar um novo slot de implantação**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [WACOM.NOTE]
	> Se o site ainda não estiver no modo **Padrão**, você receberá a mensagem **Você deve estar em modo padrão para habilitar a publicação de preparação**. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do site antes de continuar.
	
2. Na caixa de diálogo **Adicionar novo slot de implantação**, nomeie o slot e, se quiser, selecione clonar a configuração de site de outro slot de implantação existente. Clique na marca de seleção para continuar. 
	
	![Configuration Source][ConfigurationSource1]
	
Na primeira vez em que você criar um slot, só haverá duas opções: clonar a configuração de slot padrão em produção ou não. 
	
	Se já tiver criado vários slots, você poderá clonar a configuração de um slot diferente do que estiver em produção:
	
	![Configuration sources][MultipleConfigurationSources]

5. Na lista de sites, expanda a marcação à esquerda do nome do site para revelar o slot de implantação. Ele terá o nome do seu site de produção, seguido pelo nome do slot de implantação. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. Quando você clica no nome do slot do site de implantação, uma página é aberta com um conjunto de guias, exatamente como em qualquer site. O <strong><i>nome-de-seu-site</i>(<i>nome-do-slot-de-implantação</i>)</strong> aparecerá na parte superior da página do portal para lembrá-lo de que você está visualizando o slot do site de implantação.
	
	![Deployment Slot Title][StagingTitle]
	
5. Clique na URL do site na exibição do painel. Observe que o slot de implantação tem um nome do host próprio e também é um site ao vivo. Para limitar o acesso público ao slot de implantação, consulte [Sites do Azure - bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

	-	 

Não há conteúdo. É possível implantar no slot a partir de uma ramificação diferente do repositório, ou mesmo de um repositório diferente. Você também pode alterar a configuração do slot. Use o perfil de publicação ou as credenciais de implantação associadas ao slot de implantação para atualizar o conteúdo.  Por exemplo, você pode [publicar neste slot com o git](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
##Sobre a configuração de slots de implantação##
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. A lista a seguir mostra a configuração que será alterada com a permuta de slots.

**Configurações que serão alteradas com a permuta de slot**:

- Configurações gerais
- Cadeias de conexão
- Mapeamentos de manipulador
- Configurações de monitoramento e diagnóstico

**Configurações que não serão alteradas com a permuta de slot**:

- Pontos de extremidade de publicação
- Nomes de domínio personalizados
- Associações e certificados SSL
- Configurações de escala

**Observações**:

- Vários slots de implantação estão disponíveis apenas para sites no modo **Padrão** do plano de hospedagem na Web.

- Quando o site tem vários slots, não é possível mudar o plano de hospedagem.

- Um slot que você pretenda permutar para a produção precisa ser configurado exatamente como desejado para quando estiver em produção.

- Por padrão, um slot de implantação irá apontar para o mesmo banco de dados que o site de produção. No entanto, você pode configurar o slot de implantação para apontar para um banco de dados alternativo alterando a(s) cadeia(s) de conexão do banco de dados para o slot de implantação. Em seguida, você pode restaurar a(s) cadeia(s) de conexão de banco de dados original no slot de implantação antes de permutá-la(s) para a produção.


<a name="Swap"></a>
##Para permutar slots de implantação##

1. Para permutar os slots de implantação, selecione o slot de implantação na lista de sites que você quer permutar e clique no botão **Permutar** na barra de comando. 
	
	![Swap Button][SwapButtonBar]
	
2. A caixa de diálogo Permutar Implantações é exibida. A caixa de diálogo permite que você escolha qual slot de site deve ser a origem e qual site deve ser o destino.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Clique na marca de seleção para concluir a operação. Quando a operação for concluída, os slots de site terão sido permutados.


<a name="Rollback"></a>
##Reverter um site de produção para preparo##
Se algum erro for identificado na produção após uma permuta de slot, reverta os slots para os estados pré-permuta permutando ambos os slots imediatamente. 

<a name="Delete"></a>
##Para excluir um slot de site##

Na barra de comando na parte inferior do portal de sites do Azure, clique em **Excluir**. Você terá a opção de excluir o site e todos os slots de implantação ou de excluir apenas o slot de implantação. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Observações**:

- O dimensionamento não está disponível para slots de não produção. Está disponível apenas para slots de produção.

- O gerenciamento de recurso vinculado não tem suporte para slots de não produção. 

- Se desejar, você ainda poderá publicar diretamente no slot de produção.

- Atualmente, seus slots de implantação (sites) compartilham os mesmos recursos que seus slots de produção (sites) e são executados nas mesmas VMs. Se executar teste de estresse em um slot de preparo, seu ambiente de produção experimentará uma carga comparável de estresse. 
	
	> [WACOM.NOTE] No [Portal de visualização do Azure](https://portal.azure.com), e só nele, você pode evitar este possível impacto a um slot de produção. Basta mover temporariamente o slot de não produção para um Plano de hospedagem na Web diferente. Observe que os slots de teste e produção devem, mais uma vez, compartilhar o mesmo plano de hospedagem na Web antes que você possa permutar o slot de teste com o de produção.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Cmdlets do PowerShell do Azure para slots de site 

O PowerShell do Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte para gerenciamento de slots de implantação para sites do Azure. 

- Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Windows Azure, consulte [Como instalar e configurar o PowerShell do Windows Azure](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell).  

- Para listar os cmdlets disponíveis para sites do Azure no PowerShell, chame `help AzureWebsite`. 

----------

###Get-AzureWebsite
O cmdlet **Get-AzureWebsite** apresenta informações sobre sites do Azure da assinatura atual, como no exemplo a seguir. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
Você pode criar um slot de site para qualquer site no modo Padrão, usando o cmdlet **New-AzureWebsite** e especificando os nomes do site e do slot. Indique também a mesma região do site para a criação do slot de implantação, como no exemplo a seguir. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
Você pode usar o cmdlet **Publish-AzureWebsiteProject** para implantação de conteúdo, como no exemplo a seguir. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
Após as atualizações do conteúdo e da configuração terem sido aplicadas ao novo slot, você pode validar as atualizações navegando até o slot usando o cmdlet **Show-AzureWebsite**, como no exemplo a seguir.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
O cmdlet **Show-AzureWebsiteSlot** pode executar uma operação de permuta para tornar o slot de implantação atualizado no site de produção, como no exemplo a seguir. O site de produção não passará por nenhuma experiência de tempo de inatividade, nem passará por uma inicialização a frio. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
Se um slot de implantação não for mais necessário, ele poderá ser excluído usando o cmdlet **Remove-AzureWebsite**, como no exemplo a seguir.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Comandos da interface de linha de comando entre plataformas do Azure (xplat-cli) para slots de site

A Interface de linha de comando entre plataformas do Azure (xplat-cli) fornece comandos entre plataformas para trabalhar com o Azure, incluindo suporte para gerenciamento de slots de implantação em sites do Azure. 

- Para obter instruções sobre como instalar e configurar a xplat-cli, incluindo informações sobre como conectar a xplat-cli com sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](http://www.windowsazure.com/pt-br/documentation/articles/xplat-cli). 

-  Para listar os comandos disponíveis para sites do Azure na xplat-cli, chame `azure site -h`. 

----------
###azure site list
Para obter informações sobre sites do Azure na assinatura atual, chame **azure site list**, como no exemplo a seguir.
 
`azure site list siteslotstest`

----------
###azure site create
Para criar um slot de site para qualquer site no modo Padrão, chame **azure site create** e especifique o nome de um site existente e o nome do slot a ser criado, como no exemplo a seguir.

`azure site create siteslotstest --slot staging`

Para habilitar o controle do código-fonte para o novo slot, use a opção **--git**, como no exemplo a seguir.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
Para tornar o slot de implantação atualizado no site de produção, use o comando **azure site swap** para executar uma operação de permuta, como no exemplo a seguir. O site de produção não passará por nenhuma experiência de tempo de inatividade, nem passará por uma inicialização a frio. 

`azure site swap siteslotstest`

----------
###excluir site do azure
Para excluir um slot de implantação que não seja mais necessário, use o comando **azure site delete**, como no exemplo a seguir.

`azure site delete siteslotstest --slot staging`

----------
## Próximas etapas ##
[Sites do Azure - bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Avaliação gratuita do Microsoft Azure](http://azure.microsoft.com/pt-br/pricing/free-trial/)


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
