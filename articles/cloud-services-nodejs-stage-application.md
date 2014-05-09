<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Implantação de preparo" pageTitle="Preparar uma implantação de serviço de nuvem (Node.js) - Azure" metaKeywords="Preparo do Azure, preparo de aplicativo do Azure, ambiente de teste do Azure, ambiente de preparo do Azure, permuta do IP Virtual do Azure, permuta do VIP do Azure" description="Saiba como implantar seu aplicativo do Azure em um ambiente de preparo, e em seguida implantá-lo em um ambiente de produção usando uma permuta de VIP (IP Virtual)." metaCanonical=" " services="cloud-services" documentationCenter="Node.js" title="Preparando um aplicativo no Azure" authors="" solutions="" manager="" editor="" />







# Preparando um aplicativo no Azure

Um aplicativo empacotado pode ser implantado no ambiente de preparo no Azure para ser testado antes de movê-lo para o ambiente de produção onde o aplicativo pode ser acessado na internet. O ambiente de preparo é exatamente igual ao ambiente de produção, exceto que você só pode acessar o aplicativo de preparo com uma URL ofuscada, que é gerada pelo Azure. Após verificar que seu aplicativo está funcionando corretamente, ele pode ser implantado no ambiente de produção executando uma permuta do VIP (IP Virtual).

<div class="dev-callout">
	<b>Observação</b>
	<p>As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure.</p>
	</div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Preparar um Aplicativo]
-   [Etapa 2: Implantar um aplicativo em Produção permutando VIPs]

<h2><a id="step1"></a>Etapa 1: Preparar um aplicativo</h2>

Esta tarefa aborda como preparar um aplicativo usando o **PowerShell do Azure**.

1.  Ao publicar um serviço, basta passar o parâmetro **-Slot** para o cmdlet **Publish-AzureServiceProject**.

    **Preparo do -Slot Publish-AzureServiceProject**

2.  Faça logon no [Portal de Gerenciamento do Azure] e selecione **Serviços de Nuvem**. Após o serviço de nuvem ser criado e o status da coluna **Preparo** ter sido atualizada para **Executando**, clique no nome de serviço.

	![portal exibindo um serviço em execução][cloud-service]

3.  Selecione o **Painel** e, em seguida, selecione **Preparo**.

	![painel de serviço de nuvem][cloud-service-dashboard]

4. Observe o valor na entrada da **URL do Site** à direita. O nome DNS é uma ID interna ofuscada que é gerada pelo Azure.

    ![url do site][cloud-service-staging-url]

Agora você pode verificar que o aplicativo está funcionando corretamente no ambiente de preparo usando a URL do site de preparo.

Para um cenário de atualização, no qual o aplicativo de preparo é uma versão atualizada de uma que já foi implantada na produção, você pode [atualizar o aplicativo no ambiente de produção, permutando os VIPs][Step 2: Deploy an Application to Production by Swapping VIPs].

<h2><a id="step2"></a>Etapa 2: Atualizar um aplicativo em produção permutando VIPs</h2>

Após verificar a versão atualizada de um aplicativo no ambiente de preparo, você pode rapidamente torná-lo disponível em produção permutando os VIPs (IPs virtuais) do ambiente de preparo e de produção.

<div class="dev-callout">
<b>Observação</b>
<p>Essa etapa pressupõe que você já implantou um aplicativo na produção e preparou a versão atualizada do aplicativo.</p>
</div>

1.  Faça logon no [Portal de Gerenciamento do Azure],
    clique em **Serviços de Nuvem** e, em seguida, selecione o nome do serviço.

2.  No **Painel**, selecione **Preparo** e, em seguida, clique em **Permutar** na parte inferior da página. Isso abre a caixa de diálogo Permuta de VIP.

    ![caixa de diálogo permuta de vip][vip-swap-dialog]

3.  Revise as informações e, em seguida, clique em **OK**. As duas implantações começam a ser atualizadas à medida que o ambiente de preparo é permutado para a produção e a implantação de produção é permutada para o preparo.

Você preparou uma implantação e atualizou uma implantação em produção com êxito permutando VIPs com a implantação em preparo.

## Recursos adicionais

- [Como implantar uma atualização de serviço para a produção permutando VIPs no Azure]
- [Visão geral do gerenciamento de implantações no Azure]

  [Etapa 1: Preparar um aplicativo]: #step1
  [Etapa 2: Implantar um aplicativo em Produção permutando VIPs]: #step2
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Como implantar uma atualização de serviço para a produção permutando VIPs no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee517253.aspx
  [Visão geral do gerenciamento de implantações no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh386336.aspx

