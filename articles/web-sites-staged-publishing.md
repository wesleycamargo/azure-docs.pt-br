<properties linkid="web-sites-staged-publishing" urlDisplayName="Como preparar sites no Microsoft Azure" pageTitle="Implantação de preparo em Sites do Microsoft Azure" metaKeywords="Sites do Microsoft Azure, Implantação de Preparo, Slots do Site" description="Saiba como usar a implantação de preparo em Sites do Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Implantação de preparo em Sites do Microsoft Azure" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

#Implantação de preparo em Sites do Microsoft Azure#

## Sumário ##
- [Visão geral](#Overview)
- [Para adicionar um slot de implantação a um site](#Add)
- [Sobre a configuração de slots de implantação](#AboutConfiguration)
- [Para permutar slots de implantação](#Swap)
- [Reverter um site de produção para preparo](#Rollback)
- [Para excluir um slot de site](#Delete)
- [Cmdlets do PowerShell do Azure para slots de site](#PowerShell)
- [Comandos da interface de linha de comando entre plataformas do Azure (xplat-cli) para slots de site](#CLI)

<a name="Overview"></a>
##Visão geral##
A opção de criação de slots de sites em modo padrão em execução em sites do Microsoft Azure, permite um fluxo de trabalho de implantação de preparo Crie um slot de site de desenvolvimento ou de preparo para cada site de produção padrão (que agora se torna um slot de produção) e permute esses slots sem nenhum tempo de inatividade. A implantação de preparo não é válida para os seguintes cenários:

- **Validação antes da implantação** - Depois de implantar o conteúdo ou a configuração em um slot de site de preparo, você pode validar as alterações antes de permutar essas alterações para a produção.

- **Criando e integrando o conteúdo do site** - Você pode adicionar incrementalmente atualizações do conteúdo ao seu slot de implantação de preparo e, em seguida, permutar o slot de implantação de preparo para a produção quando suas atualizações estiverem concluídas.

- **Reversão de um site de produção** - Se as alterações permutadas para a produção não ocorrerem como o esperado, você poderá, imediatamente, permutar o conteúdo original de volta para produção. 

O Microsoft Azure aquece todas as instâncias do slot de site de origem antes da permuta para a produção eliminando inicializações frias quando você implanta o conteúdo. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de permuta. Atualmente, apenas um slot de implantação além do slot de produção padrão tem suporte do site padrão.   

<a name="Add"></a>
##Para adicionar um slot de implantação a um site##

O site deve estar em execução no modo Padrão para habilitar a criação do slot de site.

1. Na página Início Rápido ou na seção Visão Rápida na página Painel do seu site, clique em **Adicionar um novo slot de implantação**. 
	
	![Adicionar um novo slot de implantação][QGAddNewDeploymentSlot]
	
	> [WACOM.NOTE]
	> Se o site ainda não estiver no modo Padrão, você receberá a mensagem: **Você deve estar em modo padrão para habilitar a publicação de preparação**. Neste momento, você tem a opção de selecionar **Atualizar** e navegar para a guia **Escala** do site antes de continuar.
	
2. A caixa de diálogo **Adicionar Novo Slot de Implantação** é exibida.
	
	![Adicionar a caixa de diálogo Novo Slot de Implantação][AddNewDeploymentSlotDialog]
	
	Forneça um nome para o slot de implantação. O nome não pode exceder 60 caracteres alfanuméricos. Não são permitidos caracteres especiais ou espaços.
	
3. Na lista de sites, expanda a marcação à esquerda do nome do site para revelar o slot de implantação. Ele terá o nome do seu site de produção seguido pelo nome do slot de implantação que você forneceu entre parênteses. 
	
	![Lista de sites com slot de implantação][SiteListWithStagedSite]
	
4. Quando você seleciona o nome do slot do site de implantação, uma página é aberta com um conjunto de guias, exatamente como em qualquer site. O <strong><i>your-website-name</i>(<i>deployment-slot-name</i>)</strong> aparecerá na parte superior da página do portal para lembrá-lo de que você está visualizando o slot do site de preparo.
	
	![Título do slot de implantação][StagingTitle]
	
Agora você pode atualizar o conteúdo e a configuração do slot do site de implantação. Use o perfil de publicação ou as credenciais de implantação associadas ao slot de site de implantação para atualizar o conteúdo. 

<a name="AboutConfiguration"></a>
##Sobre a configuração de slots de implantação##
Por padrão, quando um slot de implantação é criado, a configuração do slot de implantação é clonada do slot do site de produção. As configurações para todos os slots de site são editáveis.

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

- Os slots de implantação estão disponíveis apenas para sites em modo Padrão.

- Se você alterar um site para modo Gratuito, Compartilhado ou Básico, o site não será mais permutável.

- Um slot de implantação que você pretende permutar para a produção precisa ser configurado exatamente como deseja tê-lo em produção.

- Por padrão, um slot de implantação irá apontar para o mesmo banco de dados que o site de produção. No entanto, você pode configurar o slot de implantação para apontar para um banco de dados alternativo alterando a(s) cadeia(s) de conexão do banco de dados para o slot de implantação. Em seguida, você pode restaurar a(s) cadeia(s) de conexão do banco de dados original no slot de implantação antes de permutá-la(s) para a produção.


<a name="Swap"></a>
##Para permutar slots de implantação##

1. Para permutar os slots de implantação, selecione o slot de implantação na lista de sites e clique no botão **Permutar** na barra de comando. 
	
	![Botão permutar][SwapButtonBar]
	
2. A caixa de diálogo Permutar Implantações é exibida. A caixa de diálogo permite que você escolha qual slot de site deve ser a origem e qual site deve ser o destino.
	
	![Caixa de diálogo Permutar Implantações][SwapDeploymentsDialog]
	
3. Clique na marca de seleção para concluir a operação. Quando a operação for concluída, os slots de site terão sido permutados.


<a name="Rollback"></a>
##Reverter um site de produção para preparo##
Se for identificado qualquer erro no conteúdo ou na configuração permutada para a produção, você poderá simplesmente permutar um slot de implantação (anteriormente, seu site de produção) de volta para a produção e efetuar correções adicionais à nova versão do site, enquanto ele estiver em modo de preparo. 

> [WACOM.NOTE]
> Para uma reversão ser bem-sucedida, o slot do site de implantação ainda deve conter o conteúdo inalterado e a configuração do site de produção anterior.

<a name="Delete"></a>
##Para excluir um slot de site##

Na barra de comando na parte inferior do Portal de Sites do Azure, clique em **Excluir**. Você terá a opção de excluir o site e todos os slots de implantação ou de excluir apenas o slot de implantação. 

![Excluir um slot de site][DeleteStagingSiteButton]

Depois de responder **Sim** à mensagem de confirmação, um ou todos os slots serão excluídos, dependendo da opção escolhida.

**Observações**:

- O dimensionamento não está disponível para slots de sites que não sejam de produção. Está disponível apenas para slots de site de produção.

- O gerenciamento de recurso vinculado não tem suporte para slots de site que não sejam de produção. 

- Se desejar, você ainda poderá publicar diretamente no slot de seu site de produção.

- Atualmente, seus slots de implantação (sites) compartilham os mesmos recursos que seus slots de produção (sites) e são executados nas mesmas VMs. Se executar teste de estresse em um slot de preparo, seu ambiente de produção experimentará uma carga comparável de estresse. 

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Cmdlets do PowerShell do Azure para slots de site 

O PowerShell do Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte para o gerenciamento de slots de implantação para Sites do Azure. 

- Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Windows Azure, consulte [Como instalar e configurar o PowerShell do Windows Azure](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell).  

- Para listar os cmdlets disponíveis para sites do Azure no PowerShell, chame `help AzureWebsite`. 

----------

###Get-AzureWebsite
O cmdlet **Get-AzureWebsite** apresenta informações sobre sites do Azure da assinatura atual, como no exemplo a seguir. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
Você pode criar um slot de site para qualquer site no modo Padrão, usando o cmdlet **New-AzureWebsite** e especificando os nomes do site e do slot. Indique também a mesma região do site para a criação do slot de implantação, como no exemplo a seguir. 

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

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

A Interface de linha de comando entre plataformas do Azure (xplat-cli) fornece comandos entre plataformas para trabalhar com o Azure, incluindo suporte para gerenciamento de slots de implantação em Sites do Azure. 

- Para obter instruções sobre como instalar e configurar a xplat-cli, incluindo informações sobre como conectar a xplat-cli com sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](http://www.windowsazure.com/pt-br/documentation/articles/xplat-cli). 

-  Para listar os comandos disponíveis para Sites do Azure na xplat-cli, chame `azure site -h`. 

----------
###azure site list
Para obter informações sobre Sites do Azure na assinatura atual, chame **azure site list**, como no exemplo a seguir.
 
`azure site list siteslotstest`

----------
###azure site create
Para criar um slot de site para qualquer site no modo Padrão, chame **azure site create** e especifique o nome de um site existente e o nome do slot a ser criado, como no exemplo a seguir.

`azure site create siteslotstest --slot staging`

Para habilitar o controle do código-fonte para o novo slot, use a opção **--git**, como no exemplo a seguir.
 
`azure site create â€“-git siteslotstest --slot staging`

----------
###azure site swap
Para tornar o slot de implantação atualizado no site de produção, use o comando **azure site swap** para executar uma operação de permuta, como no exemplo a seguir. O site de produção não passará por nenhuma experiência de tempo de inatividade, nem passará por uma inicialização a frio. 

`azure site swap siteslotstest`

----------
###excluir site do azure
Para excluir um slot de implantação que não seja mais necessário, use o comando **excluir de site azure**, como no exemplo a seguir.

`azure site delete siteslotstest --slot staging`

----------


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png

