---
title: Início Rápido – Criar o primeiro contêiner de Instâncias de Contêiner do Azure com o Portal do Azure
description: Neste guia de início rápido, você usa o Portal do Azure para implantar um contêiner em Instâncias de Contêiner do Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Início Rápido: Criar o primeiro contêiner nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam criar e gerenciar contêineres do Docker no Azure, sem a necessidade de provisionar máquinas virtuais ou adotar um serviço de nível superior. Neste início rápido, você usa o portal do Azure para criar um contêiner no Azure e expô-lo na internet com um nome de domínio completamente qualificado (FQDN). Depois de configurar algumas configurações, você verá isso em seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-portal-07]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Instâncias de Contêiner**.

![Começar a criar uma nova instância de contêiner no Portal do Azure][aci-portal-01]

Insira os seguintes valores nas caixas de texto **Nome do contêiner**, **Imagem de contêiner** e **Grupo de recursos**. Deixe os outros valores com seus padrões e selecione **OK**.

* Nome do contêiner: `mycontainer`
* Imagem de contêiner: `microsoft/aci-helloworld`
* Grupo de recursos: `myResourceGroup`

![Configurando as definições básicas para uma nova instância de contêiner no Portal do Azure][aci-portal-03]

Você pode criar contêineres Windows e Linux nas Instâncias de Contêiner do Azure. Para este guia de início rápido, deixe a configuração padrão de **Linux** para implantar a imagem baseada em Linux `microsoft/aci-helloworld`.

Em **Configuração**, especifique um **rótulo do nome DNS** para seu contêiner. O nome  deve ser exclusivo dentro da região do Azure em que você criar a instância do contêiner. Seu contêiner estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`.

Deixe as outras configurações em **Configuração** com seus padrões e selecione **OK** para validar a configuração.

![Configurando uma nova instância de contêiner no Portal do Azure][aci-portal-04]

Quando a validação for concluída, um resumo das configurações de seu contêiner será exibido. Selecione **OK** para enviar sua solicitação de implantação de contêiner.

![Resumo das configurações para uma nova instância de contêiner no Portal do Azure][aci-portal-05]

Quando a implantação for iniciada, um bloco é colocado no painel do portal indicando o progresso da implantação. Uma vez implantado, o bloco exibe a nova instância de contêiner.

![Progresso da criação de uma nova instância de contêiner no Portal do Azure][aci-portal-08]

Selecione a instância de contêiner **mycontainer** para exibir suas propriedades. Anote o **FQDN** (nome de domínio totalmente qualificado) da instância do contêiner, bem como seu **Status**.

![Visão geral do grupo de contêineres no Portal do Azure][aci-portal-06]

Uma vez seu **Status** é *Executando*, navegue para o FQDN do contêiner em seu navegador.

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-portal-07]

Parabéns! Ao configuras apenas algumas configurações, você implantou um aplicativo publicamente acessível em Instâncias de Contêiner do Azure.

## <a name="view-container-logs"></a>Exibir logs do contêiner

Exibir os logs para uma instância de contêiner é útil ao solucionar problemas com o contêiner ou o aplicativo que é executado.

Para visualizar logs do contêiner, em **CONFIGURAÇÕES**, selecione **Contêineres**, em seguida, **Logs**. Você verá a solicitação HTTP GET gerada quando você exibiu o aplicativo em seu navegador.

![Logs de contêiner no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contêiner, selecione **Visão geral** para a instância de contêiner *mycontainer*, em seguida, selecione **Excluir**.

![Excluir a instância de contêiner no Portal do Azure][aci-portal-09]

Escolha **Sim** quando a caixa de diálogo de confirmação aparecer.

![Confirmação de exclusão de uma instância de contêiner no Portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de Contêiner do Azure com base em uma imagem em no registro do Hub do Docker público. Se você quiser tentar criar uma imagem de contêiner sozinho e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

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