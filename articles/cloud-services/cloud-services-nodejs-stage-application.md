<properties 
	pageTitle="Preparar uma implantação do serviço de nuvem (Node.js) | Microsoft Azure" 
	description="Saiba como implantar seu aplicativo Azure para um ambiente de preparo e depois implantar em um ambiente de produção usando a permuta do IP Virtual (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="06/24/2016" 
	ms.author="robmcm"/>



# Preparando um aplicativo no Azure

Um aplicativo empacotado pode ser implantado no ambiente de preparo no Azure para ser testado antes de movê-lo para o ambiente de produção onde o aplicativo pode ser acessado na Internet. O ambiente de preparo é exatamente igual ao ambiente de produção, exceto que você só poderá acessar o aplicativo preparado com uma URL ofuscada que é gerada pelo Azure. Após verificar que seu aplicativo está funcionando corretamente, ele pode ser implantado no ambiente de produção executando uma permuta do VIP (IP Virtual).

> [AZURE.NOTE] As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure.

## Etapa 1: Preparar um aplicativo

Esta tarefa aborda como preparar um aplicativo usando o **Microsoft Azure PowerShell**.

1.  Ao publicar um serviço, basta passar o parâmetro **-Slot** para o cmdlet **Publish-AzureServiceProject**.

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Faça logon no [portal clássico do Azure] e selecione **Serviços de Nuvem**. Após o serviço de nuvem ser criado e o status da coluna **Preparo** ter sido atualizada para **Executando**, clique no nome de serviço.

	![portal exibindo um serviço em execução][cloud-service]

3.  Selecione o **Painel** e, em seguida, selecione **Preparo**.

	![painel de serviço de nuvem][cloud-service-dashboard]

4. Observe o valor na entrada da **URL do Site** à direita. O nome DNS é uma ID interna ofuscada que é gerada pelo Azure.

    ![url do site][cloud-service-staging-url]

Agora você pode verificar que o aplicativo está funcionando corretamente no ambiente de preparo usando a URL do site de preparo.

## Etapa 2: Atualizar um aplicativo em produção permutando VIPs

Após verificar a versão atualizada de um aplicativo no ambiente de preparo, você pode rapidamente torná-lo disponível em produção permutando os VIPs (IPs virtuais) do ambiente de preparo e de produção.

> [AZURE.NOTE] Essa etapa pressupõe que você já implantou um aplicativo na produção e preparou a versão atualizada do aplicativo.

1.  Faça logon no [portal clássico do Azure], clique em **Serviços de Nuvem** e selecione o nome do serviço.

2.  No **Painel**, selecione **Preparo** e, em seguida, clique em **Permutar** na parte inferior da página. Isso abre a caixa de diálogo Permuta de VIP.

    ![caixa de diálogo permuta de vip][vip-swap-dialog]

3.  Revise as informações e, em seguida, clique em **OK**. As duas implantações começam a ser atualizadas à medida que o ambiente de preparo é permutado para a produção e a implantação de produção é permutada para o preparo.

Você preparou uma implantação e atualizou uma implantação em produção com sucesso permutando VIPs com a implantação em preparo.

## Recursos adicionais

- [Como implantar um serviço atualizado na produção permutando VIPs no Azure]

[portal clássico do Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Como implantar um serviço atualizado na produção permutando VIPs no Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production

<!---HONumber=AcomDC_0629_2016-->