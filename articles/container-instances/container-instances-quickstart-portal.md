---
title: Início Rápido – Executar um aplicativo nas Instâncias de Contêiner do Azure – Portal
description: Neste início rápido, você usará o portal do Azure para implantar um aplicativo de contêiner do Docker para execução em um contêiner isolado nas Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: d6a1d442eca0cf5e433a82fb52ed54b09b56c779
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566066"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-in-the-azure-portal"></a>Início Rápido: Executar um aplicativo de contêiner nas Instâncias de Contêiner do Azure no portal do Azure

Use as Instâncias de Contêiner do Azure para executar contêineres do Docker no Azure de maneira simples e rápida. Não é necessário implantar máquinas virtuais nem usar uma plataforma de orquestração de contêiner completa, como o Kubernetes. Neste início rápido, você usará o portal do Azure para criar um contêiner no Azure e disponibilizar seu aplicativo com um FQDN (nome de domínio totalmente qualificado). Após definir algumas configurações e implantar o contêiner, você poderá navegar até o aplicativo em execução:

![Aplicativos implantados nas Instâncias de Contêiner do Azure exibidos no navegador][aci-portal-07]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Instâncias de Contêiner**.

![Começar a criar uma nova instância de contêiner no Portal do Azure][aci-portal-01]

Insira os seguintes valores nas caixas de texto **Nome do contêiner**, **Imagem de contêiner** e **Grupo de recursos**. Deixe os outros valores com seus padrões e selecione **OK**.

* Nome do contêiner: `mycontainer`
* Imagem de contêiner: `microsoft/aci-helloworld`
* Grupo de recursos: **Criar novo** > `myResourceGroup`

![Configurando as definições básicas para uma nova instância de contêiner no Portal do Azure][aci-portal-03]

Para este início rápido, deixe a configuração padrão **Público** para implantar a imagem `microsoft/aci-helloworld` do Registro do Hub do Docker público. Esta imagem empacota um pequeno aplicativo Web escrito no Node.js que veicula a uma página HTML estática.

Em **Configuração**, especifique um **rótulo do nome DNS** para seu contêiner. O nome  deve ser exclusivo dentro da região do Azure em que você criar a instância do contêiner. Seu contêiner estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`.

Deixe as outras configurações em **Configuração** com seus padrões e selecione **OK** para validar a configuração.

![Configurando uma nova instância de contêiner no Portal do Azure][aci-portal-04]

Quando a validação for concluída, um resumo das configurações de seu contêiner será exibido. Selecione **OK** para enviar sua solicitação de implantação de contêiner.

![Resumo das configurações para uma nova instância de contêiner no Portal do Azure][aci-portal-05]

Quando a implantação é iniciada, aparece uma notificação indicando que a implantação está em andamento. Outra notificação será exibida quando o grupo de contêiner tiver sido implantado.

![Progresso da criação de uma nova instância de contêiner no Portal do Azure][aci-portal-08]

Abra a visão geral do grupo de contêiner navegando até **Grupos de Recursos** > **myResourceGroup** > **mycontainer**. Anote o **FQDN** (nome de domínio totalmente qualificado) da instância do contêiner, bem como seu **Status**.

![Visão geral do grupo de contêineres no Portal do Azure][aci-portal-06]

Uma vez seu **Status** é *Executando*, navegue para o FQDN do contêiner em seu navegador.

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-portal-07]

Parabéns! Ao configuras apenas algumas configurações, você implantou um aplicativo publicamente acessível em Instâncias de Contêiner do Azure.

## <a name="view-container-logs"></a>Exibir logs do contêiner

Exibir os logs para uma instância de contêiner é útil ao solucionar problemas com o contêiner ou o aplicativo que é executado.

Para exibir os logs do contêiner, em **Configurações**, selecione **Contêineres** e, em seguida, **Logs**. Você verá a solicitação HTTP GET gerada quando você exibiu o aplicativo em seu navegador.

![Logs de contêiner no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contêiner, selecione **Visão geral** para a instância de contêiner *mycontainer*, em seguida, selecione **Excluir**.

![Excluir a instância de contêiner no Portal do Azure][aci-portal-09]

Escolha **Sim** quando a caixa de diálogo de confirmação aparecer.

![Confirmação de exclusão de uma instância de contêiner no Portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de Contêiner do Azure com base em uma imagem em no registro do Hub do Docker público. Se você quiser criar uma imagem de contêiner e implantá-la usando um Registro de Contêiner do Azure privado, prossiga para o tutorial das Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/