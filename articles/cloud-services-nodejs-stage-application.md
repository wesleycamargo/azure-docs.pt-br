<properties urlDisplayName="Staging Deployment" pageTitle="Preparar uma implanta&ccedil;&atilde;o do servi&ccedil;o de nuvem (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Saiba como implantar seu aplicativo Azure para um ambiente de preparo e depois implantar em um ambiente de produ&ccedil;&atilde;o usando a permuta do IP Virtual (VIP)." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Preparando um aplicativo no Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Preparando um aplicativo no Azure

Um aplicativo empacotado pode ser implantado no ambiente de preparo no
Azure para ser testado antes de movê-lo para o
ambiente de produção onde o aplicativo pode ser acessado na internet. O
ambiente de preparo é exatamente igual ao ambiente de produção, exceto
que você só poderá acessar o aplicativo preparado com uma URL ofuscada,
que é gerada pelo Azure. Após verificar que seu
aplicativo está funcionando corretamente, ele pode ser implantado no
ambiente de produção executando uma permuta do VIP (IP Virtual).

<div class="dev-callout">
    <b>Observa&ccedil;&atilde;o</b>
    <p>As etapas deste artigo se aplicam somente a aplicativos de n&oacute; hospedados como um Servi&ccedil;o de Nuvem do Azure.</p>
    </div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Preparar um aplicativo][Etapa 1: Preparar um aplicativo]
-   [Etapa 2: Implantar um aplicativo na produção permutando VIPs][Etapa 2: Implantar um aplicativo na produção permutando VIPs]

## <span id="step1"></span></a>Etapa 1: Preparar um aplicativo

Esta tarefa aborda como preparar um aplicativo usando o **PowerShell do Windows
Azure**.

1.  Ao publicar um serviço, basta passar o parâmetro **-Slot** para
    o cmdlet **Publish-AzureServiceProject**.

    **Preparo do -Slot Publish-AzureServiceProject**

2.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] e selecione **Serviços de Nuvem**. Após o serviço de nuvem ser criado e o status da coluna **Preparo** ter sido atualizada para **Executando**, clique no nome de serviço.

    ![portal exibindo um serviço em execução][portal exibindo um serviço em execução]

3.  Selecione o **Painel** e, em seguida, selecione **Preparo**.

    ![painel de serviço de nuvem][painel de serviço de nuvem]

4.  Observe o valor na entrada da **URL do Site** à direita. O nome DNS é uma ID interna ofuscada que é gerada pelo Azure.

    ![url do site][url do site]

Agora você pode verificar que o aplicativo está funcionando corretamente no ambiente de preparo usando a URL do site de preparo.

Para um cenário de atualização, no qual o aplicativo de preparo é uma
versão atualizada de uma que já foi implantada na produção, você pode
[atualizar o aplicativo no ambiente de produção, permutando os
VIPs][Etapa 2: Implantar um aplicativo na produção permutando VIPs].

## <span id="step2"></span></a>Etapa 2: Atualizar um aplicativo na produção permutando VIPs

Após verificar a versão atualizada de um aplicativo no
ambiente de preparo, você pode rapidamente torná-lo disponível em produção
permutando os VIPs (IPs virtuais) do
ambiente de preparo e de produção.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Essa etapa pressup&otilde;e que voc&ecirc; j&aacute; implantou um
aplicativo na produ&ccedil;&atilde;o e preparou a vers&atilde;o atualizada do
aplicativo.</p>
</div>

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure],
    clique em **Serviços de Nuvem** e, em seguida, selecione o nome do serviço.

2.  No **Painel**, selecione **Preparo** e, em seguida, clique em **Permutar** na parte inferior da página. Isso abre a caixa de
    diálogo Permuta de VIP.

    ![caixa de diálogo permuta de vip][caixa de diálogo permuta de vip]

3.  Revise as informações e, em seguida, clique em **OK**. As duas implantações
    começam a ser atualizadas à medida que o ambiente de preparo é permutado para a produção e
    a implantação de produção é permutada para o preparo.

Você preparou uma implantação e atualizou uma
implantação em produção com êxito permutando VIPs com a implantação em preparo.

## Recursos adicionais

-   [Como implantar um serviço atualizado na produção permutando VIPs no Azure][Como implantar um serviço atualizado na produção permutando VIPs no Azure]
-   [Visão geral do gerenciamento de implantações no Azure][Visão geral do gerenciamento de implantações no Azure]

  [Etapa 1: Preparar um aplicativo]: #step1
  [Etapa 2: Implantar um aplicativo na produção permutando VIPs]: #step2
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [portal exibindo um serviço em execução]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [painel de serviço de nuvem]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [url do site]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [caixa de diálogo permuta de vip]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Como implantar um serviço atualizado na produção permutando VIPs no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee517253.aspx
  [Visão geral do gerenciamento de implantações no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh386336.aspx
