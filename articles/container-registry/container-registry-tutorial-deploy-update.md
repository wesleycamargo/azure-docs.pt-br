---
title: "Tutorial do Registro de Contêiner do Azure – Enviar por push uma imagem atualizada para implantações regionais"
description: "Envie por push uma imagem do Docker modificada para o Registro que contém o Azure com replicação geográfica e veja as alterações implantadas automaticamente nos aplicativos Web em execução em várias regiões. Parte três de uma série de três partes."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, Contêineres, Registro, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 76e6e1b826f37bfea7a8463808566191753e4f2d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Enviar por push uma imagem atualizada para implantações regionais

Esta é a parte três de uma série de tutoriais de três partes. No [tutorial anterior](container-registry-tutorial-deploy-app.md), a replicação geográfica foi configurada para duas implantações de aplicativo Web regionais diferentes. Neste tutorial, você primeiro modifica o aplicativo, em seguida, cria uma nova imagem de contêiner e envia por push para o Registro com replicação geográfica. Por fim, você exibe a alteração, implantada automaticamente pelos webhooks de Registro de Contêiner do Azure, em ambas as instâncias do aplicativo Web.

Neste tutorial, a parte final da série:

> [!div class="checklist"]
> * Modifique o aplicativo Web HTML
> * Crie e marque a imagem do Docker
> * Envie a alteração por push ao Registro de Contêiner do Azure
> * Exiba o aplicativo atualizado em duas regiões diferentes

Se você ainda não tiver configurado as duas implantações regionais do *Aplicativo Web para Contêineres*, retorne para o tutorial anterior da série [Deploy web app from Azure Container Registry](container-registry-tutorial-deploy-app.md) (Implantar o aplicativo Web do Registro de Contêiner do Azure).

## <a name="modify-the-web-application"></a>Modificar o aplicativo web

Nesta etapa, faça uma alteração no aplicativo Web que será altamente visível depois que você enviar por push a imagem de contêiner atualizada para o Registro de Contêiner do Azure.

Localize o arquivo `AcrHelloworld/Views/Home/Index.cshtml` na fonte do aplicativo [clonado do GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) em um tutorial anterior e abra-o em seu editor de texto favorito. Adicione a seguinte linha acima da linha `<img>`:

```html
<h1>MODIFIED</h1>
```

O `Index.cshtml` modificado deve ser semelhante a:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>Recompilar a imagem

Agora que você atualizou o aplicativo Web, recompile a imagem de contêiner. Como antes, use o nome da imagem totalmente qualificado, incluindo a URL do servidor de logon, para a marca:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Antes de realizar a implantação para o Registro de Contêiner do Azure, execute a imagem localmente para verificar se o build foi bem-sucedido.

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

Navegue até http://localhost:8080 no navegador da Web para confirmar que o contêiner está funcionando e sua modificação é exibida.

![IMAGEM DE CONTÊINER LOCAL][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Agora, envie por push a imagem de contêiner *acr-helloworld* para seu Registro com replicação geográfica. Aqui, você está executando um único comando `docker push` para implantar a imagem atualizada para as réplicas de Registro nas regiões *Oeste dos EUA* e *Leste dos EUA*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="view-the-webhook-logs"></a>Exibir os logs do webhook

Enquanto a imagem está sendo replicada, você pode ver o webhooks do Registro de Contêiner do Azure sendo disparados.

Para ver o webhooks regionais que foram criados quando você implantou o contêiner para *Aplicativos Web para Contêineres* em um tutorial anterior, navegue até o Registro de contêiner no Portal do Azure e selecione **Webhooks** em **SERVIÇOS**.

![Webhooks de Registro de contêiner no Portal do Azure][tutorial-portal-01]

Selecione cada Webhook para ver o histórico de suas chamadas e respostas. Você deve ver uma linha para a ação **push** nos logs de ambos os Webhooks. Aqui, o log para o Webhook localizado na região *Oeste dos EUA* mostra a ação **push** disparada pela `docker push` na etapa anterior:

![Log dos Webhooks de Registro de contêiner no Portal do Azure (Oeste dos EUA)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Exibir o aplicativo Web atualizado

Os Webhooks notificam os aplicativos Web que uma nova imagem foi enviada por push no Registro, que implanta automaticamente o contêiner atualizado para os dois aplicativos Web regionais.

Verifique se que o aplicativo foi atualizado nas duas implantações navegando até as implantações de aplicativo Web regionais no navegador da Web. Como um lembrete, você pode encontrar a URL para o aplicativo Web implantado no canto superior direito de cada guia de visão geral do Serviço de Aplicativo.

![Visão geral do Serviço de Aplicativo no Portal do Azure][tutorial-portal-03]

Para ver o aplicativo atualizado, selecione o link na visão geral do Serviço de Aplicativo. Aqui está uma exibição de exemplo da execução do aplicativo no *Oeste dos EUA*:

![Exibição de navegador do aplicativo Web modificado em execução na região Oeste dos EUA][deployed-app-westus-modified]

Verifique se a imagem de contêiner atualizada também foi implantada na implantação do *Leste dos EUA* exibindo-a no navegador.

![Exibição de navegador do aplicativo Web modificado em execução na região Leste dos EUA][deployed-app-eastus-modified]

Com um único `docker push`, atualizamos as duas implantações de aplicativo Web regionais e o Registro de Contêiner do Azure forneceu as imagens de contêiner de repositórios em proximidade com a rede.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atualizou e enviou por push uma nova versão do contêiner de aplicativo Web para seu Registro com replicação geográfica. Os Webhooks no Registro de Contêiner do Azure notificaram os Serviços de Aplicativos sobre a atualização, o que disparou um pull local dos Registros replicados.

Neste, o tutorial final da série, você:

> [!div class="checklist"]
> * Atualizou o aplicativo Web HTML
> * Criou e marcou a imagem do Docker
> * Enviou a alteração por push ao Registro de Contêiner do Azure
> * Exibiu o aplicativo atualizado em duas regiões diferentes

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png