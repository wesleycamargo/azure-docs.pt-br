---
title: Início Rápido – criar um registro particular do Docker no Azure – portal do Azure
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker com o Portal do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 865c53fdda60f6a0384157ec68042b4b8b243a7a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255356"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Início Rápido: criar um registro de contêiner privado usando o portal do Azure

Um registro de contêiner do Azure é um registro particular do Docker no Azure no qual você pode armazenar e gerenciar suas imagens de contêiner particulares do Docker. Neste início rápido, você cria um registro de contêiner com o portal do Azure, envia por push uma imagem de contêiner para o registro e, por fim, implanta o contêiner do registro nas ACI (Instâncias de Contêiner do Azure ).

Para concluir este guia de início rápido, você deve ter o Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-01]

Insira os valores para **Nome do registro** e **Grupo de recursos**. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Para este início rápido crie um novo grupo de recursos no local `West US` denominado `myResourceGroup` e para **SKU**, selecione “Basic”. Selecione **Criar** para implantar a instância do ACR.

![Criar um registro de contêiner no Portal do Azure][qs-portal-03]

Neste guia de início rápido, criamos um registro *Básico*. O Registro de Contêiner do Azure está disponível em várias SKUs diferentes, descritos brevemente na tabela a seguir. Para obter detalhes estendidos sobre cada um, consulte [SKUs de registro de contêiner][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Quando a mensagem **Implantação com êxito** for exibida, selecione o registro de contêiner no portal e, em seguida, selecione as **Chaves de acesso**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-05]

Em **Usuário administrador**, selecione **Habilitar**. Anote os seguintes valores:

* Servidor de logon
* Nome de Usuário
* Senha

Você usará esses valores nas etapas seguintes ao trabalhar com o registro na CLI do Docker.

![Criar um registro de contêiner no Portal do Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Para fazer isso, use o comando [docker login][docker-login]. Substitua os valores *nome de usuário*, *senha* e *servidor de logon* por aqueles que você anotou na etapa anterior.

```bash
docker login --username <username> --password <password> <login server>
```

O comando retorna `Login Succeeded` na conclusão. Você também poderá ver um aviso de segurança recomendando usar o parâmetro `--password-stdin`. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Consulte a referência do comando [docker login][docker-login] para ver mais informações.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para o Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se necessário, execute o seguinte comando para efetuar pull de uma imagem existente do Hub do Docker.

```bash
docker pull microsoft/aci-helloworld
```

Antes de enviar a imagem por push para o registro, você deve marcar a imagem com o nome do servidor de logon do ACR. Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o *servidor de logon* pelo nome do servidor de logon que você anotou anteriormente. Adicione um *nome do repositório* tais como **`myrepo`** para colocar sua imagem em um repositório.

```bash
docker tag microsoft/aci-helloworld <login server>/<repository name>/aci-helloworld:v1
```

Por fim, use [docker push][docker-push] para enviar a imagem por push para a instância do ACR. Substitua *servidor de logon* pelo nome do servidor de logon da sua instância do ACR e substitua o *nome do repositório* pelo nome do repositório usado no comando anterior.

```bash
docker push <login server>/<repository name>/aci-helloworld:v1
```

A saída de um comando `docker push` bem sucedido é semelhante a:

```
The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

## <a name="list-container-images"></a>Listar imagens de contêiner

Para listar as imagens na sua instância do ACR, navegue até o seu registro no portal e selecione **Repositórios** e, em seguida, selecione o repositório que você criou com `docker push`.

Neste exemplo, selecionamos o repositório **aci-helloworld** e nós podemos ver a imagem marcada com `v1` em **TAGS**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Implantar imagem nas ACI

Para implantar em uma instância a partir do registro, precisamos navegar até o repositório (aci-helloworld) e clicar no botão de reticências ao lado de v1.

![Iniciar uma Instância de Contêiner do Azure a partir do portal][qs-portal-10]

Será exibido um menu de contexto; selecione **Executar instância**:

![Iniciar o menu de contexto das ACI][qs-portal-11]

Preencha o **Nome do contêiner**, verifique se a assinatura correta está selecionada, selecione o **Grupo de recursos**: existente: “myResourceGroup”. Certifique-se de que as opções de “Endereço IP público” estão habilitadas definindo essa configuração para **Sim** e, em seguida, clique em **OK** para iniciar a Instância de Contêiner do Azure.

![Iniciar opções de implantação nas ACI][qs-portal-12]

Quando a implantação é iniciada, um bloco é colocado no painel do portal indicando o progresso da implantação. Após a conclusão da implantação, o bloco é atualizado para mostrar o novo grupo de contêineres **mycontainer**.

![Status da implantação das ACI][qs-portal-13]

Selecione o grupo de contêineres mycontainer para exibir as propriedades do grupo de contêineres. Anote o **Endereço IP** do grupo de contêineres, bem como o **STATUS** do contêiner.

![Detalhes do contêiner das ACI][qs-portal-14]

## <a name="view-the-application"></a>Exibir o aplicativo

Depois que o contêiner é movido para o estado **Em Execução**, use seu navegador favorito para acessar o endereço IP que você anotou na etapa anterior e exibir o aplicativo hospedado no seu novo contêiner.

![Aplicativo Olá, Mundo no navegador][qs-portal-15]

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar seus recursos, navegue até o grupo de recursos **myResourceGroup** no portal. Depois que o grupo de recursos for carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos, o Registro de Contêiner do Azure e todas as Instâncias de Contêiner do Azure.

![Excluir um grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com a CLI do Azure e iniciou uma instância dele nas Instâncias de Contêiner do Azure. Continue com o tutorial das Instâncias de Contêiner do Azure a fim de ver as ACI com mais detalhes.

> [!div class="nextstepaction"]
> [Tutoriais sobre Instâncias de Contêiner do Azure][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
