<properties 
	pageTitle="Preparar uma implantação do serviço de nuvem (Node.js) - Azure" 
	description="Saiba como implantar seu aplicativo Azure para um ambiente de preparo e depois implantar em um ambiente de produção usando a permuta do IP Virtual (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Preparando um aplicativo no Azure

Um aplicativo empacotado pode ser implantado no ambiente de preparo no
Azure para ser testado antes de movê-lo para o ambiente de produção
no qual o aplicativo está acessível na Internet. O
ambiente de preparo é exatamente igual ao ambiente de produção, exceto
que você só pode acessar o aplicativo preparado com uma URL ofuscada
que é gerada pelo Azure. Após ter verificado que o seu
aplicativo está funcionando corretamente, pode ser implantado para o ambiente de produção
ao executar uma troca de IP Virtual (VIP).

> [AZURE.NOTE] As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Preparar um aplicativo]
-   [Etapa 2: Implantar um aplicativo na produção permutando VIPs]

<h2><a id="step1"></a>Etapa 1: Preparar um aplicativo</h2>

Esta tarefa aborda como preparar um aplicativo usando o **PowerShell do
Microsoft Azure**.

1.  Ao publicar um serviço, basta passar o parâmetro **-Slot** para
    o cmdlet do **Publish-AzureServiceProject**.

    **Prepado do Publish-AzureServiceProject -Slot**

2.  Faça logon no [Portal de Gerenciamento do Azure] e selecione **Serviços de Nuvem**. Após o serviço de nuvem ser criado e o status da coluna **Preparo** ter sido atualizada para **Executando**, clique no nome de serviço.

	![portal displaying a running service][cloud-service]

3.  Selecione o **Painel** e, em seguida, selecione **Preparo**.

	![cloud service dashboard][cloud-service-dashboard]

4. Observe o valor na entrada da **URL do Site** à direita. O nome DNS é uma ID interna ofuscada que é gerada pelo Azure.

    ![site url][cloud-service-staging-url]

Agora você pode verificar que o aplicativo está funcionando corretamente no ambiente de preparo usando a URL do site de preparo.

Para um cenário de atualização, na qual o aplicativo de preparo é uma versão atualizada
de um dos que já foi implantado na produção, você pode
[atualizar o aplicativo no ambiente de produção, trocando os
VIPs][Etapa 2: Implantar um aplicativo na produção permutando VIPs].

<h2><a id="step2"></a>Etapa 2: Atualizar um aplicativo na produção permutando VIPs</h2>

Depois de verificar a versão atualizada de um aplicativo no
ambiente de preparo, você pode torná-lo disponível na produção ao
trocar os IPs virtuais (VIPs) dos ambientes de preparação e produção
.

> [AZURE.NOTE] Essa etapa pressupõe que você já implantou um
aplicativo na produção e preparou a versão atualizada do
aplicativo.

1.  Faça logon no [Portal de Gerenciamento do Azure],
    clique em **serviços de nuvem** e, em seguida, selecione o nome do serviço.

2.  No **Painel**, selecione **Preparo** e, em seguida, clique em **Permutar** na parte inferior da página. Isso abre a caixa de diálogo de Troca de VIP
    caixa de diálogo.

    ![vip swap dialog][vip-swap-dialog]

3.  Revise as informações e, em seguida, clique em **OK**. As duas implantações
    estão sendo atualizadas conforme a implantação de preparo alterna para produção e
    a implantação de produção alterna para o preparo.

Você preparou com sucesso uma implantação e atualização de uma implantação de produção
ao trocar VIPs com a implantação em preparo.

## Recursos adicionais

- [Como implantar um serviço de atualização para produção ao trocar VIPs no Azure]
- [Visão geral do gerenciamento de implantações no Azure]

  [Etapa 1: Preparar um aplicativo]: #step1
  [Etapa 2: Implantar um aplicativo na produção permutando VIPs]: #step2
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Como implantar um serviço de atualização para produção ao trocar VIPs no Azure]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Visão geral do gerenciamento de implantações no Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
