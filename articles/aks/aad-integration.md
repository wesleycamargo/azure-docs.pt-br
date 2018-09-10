---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Como criar clusters do Serviço de Kubernetes do Azure (AKS) habilitados para Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 8/9/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bbf7ad201a70a315b75ed5e1f35671e4a5604fc
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142030"
---
# <a name="integrate-azure-active-directory-with-aks"></a>Integrar o Azure Active Directory ao AKS

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, é possível fazer logon em um cluster do AKS usando o token de autenticação do Azure Active Directory. Além disso, os administradores de cluster podem configurar o controle de acesso baseado em função (RBAC) do Kubernetes com base na identidade de um usuário ou associação no grupo de diretórios.

Este artigo mostra como implantar os pré-requisitos para AKS e Azure AD e como implantar um cluster habilitado para Azure AD e criar uma função simples do RBAC no cluster do AKS.

As seguintes limitações se aplicam:

- No momento, não é possível atualizar os clusters do AKS existentes que não sejam habilitados pelo RBAC para uso do RBAC.
- Os usuários *convidados* no Azure AD, por exemplo, se você estiver usando um logon federado de um diretório diferente, não têm suporte.

## <a name="authentication-details"></a>Detalhes de autenticação

A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Mais informações sobre OpenID Connect podem ser encontradas na [documentação do Open ID Connect][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS. Mais informações sobre autenticação de token do Webhook podem ser encontradas na [documentação de autenticação do webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. Essa operação deve ser concluída por um administrador de locatário do Azure.

## <a name="create-server-application"></a>Criar aplicativo do servidor

O primeiro aplicativo do Azure AD é usado para obter uma associação do grupo de usuários do Azure AD.

1. Selecione **Azure Active Directory** > **Registros do aplicativo** > **Novo registro do aplicativo**.

  Forneça um nome ao aplicativo, selecione **aplicativo Web/API** para o tipo de aplicativo e insira qualquer valor formatado para **URL de Entrada**. Selecione **Criar** ao terminar.

  ![Criar um registro do Azure AD](media/aad-integration/app-registration.png)

2. Selecione **Manifesto** e edite o valor `groupMembershipClaims` para `"All"`.

  Salve as atualizações depois de concluído.

  ![Atualizar a associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. De volta ao aplicativo do Azure AD, selecione **Configurações** > **Chaves**.

  Adicione uma descrição de chave, selecione um prazo de expiração e selecione **Salvar**. Anote o valor da chave. Ao implantar um cluster do AKS habilitado pelo Azure AD, esse valor será referido como `Server application secret`.

  ![Obter a chave privada do aplicativo](media/aad-integration/application-key.png)

4. Retorne ao aplicativo do Azure AD, selecione **Configurações** > **Permissões necessárias** > **Adicionar** > **Selecionar um API** > **Microsoft Graph** > **Selecionar**.

  ![Selecionar a API do Graph](media/aad-integration/graph-api.png)

5. Em **APPLICATION PERMISSIONS** marque a caixa ao lado de **Ler dados de diretório**.

  ![Definir permissões de gráfico de aplicativo](media/aad-integration/read-directory.png)

6. Em **DELEGATED PERMISSIONS**, marque a caixa ao lado de **Entrar e ler o perfil do usuário** e **Ler dados de diretório**. Salve as atualizações quando concluídas.

  ![Definir permissões de gráfico de aplicativo](media/aad-integration/delegated-permissions.png)

7. Selecione **Concluir**, escolha *Microsoft Graph* na lista de APIs e selecione **Conceder permissões**. Esta etapa falhará se a conta atual não for um administrador do locatário.

  ![Definir permissões de gráfico de aplicativo](media/aad-integration/grant-permissions.png)

  Quando as permissões tiverem sido concedidas com êxito, a notificação a seguir será exibida no portal:

  ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

8. Retorne ao aplicativo e anote a **ID do Aplicativo**. Ao implantar um cluster do AKS habilitado para Azure AD, esse valor será referido como `Server application ID`.

  ![Obter ID do aplicativo](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Criar aplicativo cliente

O segundo aplicativo do Azure AD é usado ao registrar em log com a CLI do Kubernetes (kubectl.)

1. Selecione **Azure Active Directory** > **Registros do aplicativo** > **Novo registro do aplicativo**.

  Forneça um nome ao aplicativo, selecione **Nativo** para o tipo de aplicativo e insira qualquer valor formatado de URI para **URI de Redirecionamento**. Selecione **Criar** ao terminar.

  ![Criar registro de AAD](media/aad-integration/app-registration-client.png)

2. No aplicativo do Azure AD, selecione **Configurações** > **Permissões necessárias** > **Adicionar** > **Selecionar uma API** e pesquise o nome do aplicativo do servidor criado na última etapa deste documento.

  ![Configurar permissões de aplicativo](media/aad-integration/select-api.png)

3. Coloque uma marca de seleção ao lado do aplicativo e clique em **Selecionar**.

  ![Selecione o ponto de extremidade do aplicativo do servidor do AAD do AKS](media/aad-integration/select-server-app.png)

4. Selecione **Concluído** e **Conceder Permissões** para concluir essa etapa.

  ![Conceder permissões](media/aad-integration/grant-permissions-client.png)

5. De volta ao aplicativo AD, anote a **ID do Aplicativo**. Ao implantar um cluster do AKS habilitado para Azure AD, esse valor será referido como `Client application ID`.

  ![Obtenha a ID do aplicativo](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obter a ID do locatário

Finalmente, obtenha a ID do locatário do Azure. Esse valor também é usado ao implantar o cluster do AKS.

No portal do Azure, selecione **Azure Active Directory** > **Propriedades** e anote a **ID de Diretório**. Ao implantar um cluster do AKS habilitado para Azure AD, esse valor será referido como `Tenant ID`.

![Obter a ID do locatário do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Implantar o cluster

Utilize o comando [az group create][az-group-create] para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myAKSCluster --location eastus
```

Implante o cluster usando o comando [az aks create][az-aks-create]. Substitua os valores no comando de exemplo abaixo pelos valores coletados ao criar os aplicativos do Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Criar associação do RBAC

Antes que uma conta do Azure Active Directory possa ser usada com o cluster do AKS, uma associação de função ou uma associação de função de cluster precisa ser criada. As *funções* definem as permissões a serem concedidas, e as *associações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Primeiro, use o comando [az aks get-credentials][az-aks-get-credentials] com o argumento `--admin` para fazer logon no cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Em seguida, use o manifesto a seguir para criar um ClusterRoleBinding para uma conta do Azure AD. Atualize o nome de usuário com um do seu locatário do Azure AD. Este exemplo fornece à conta acesso total a todos os namespaces do cluster:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: "user@contoso.com"
```

Uma associação de função também pode ser criada para todos os membros de um grupo do Azure AD. Os grupos do Microsoft Azure AD são especificados com a ID de objeto do grupo, como mostrado no seguinte exemplo:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Para obter mais informações sobre como proteger um cluster do Kubernetes com RBAC, consulte [Usar autorização de RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Acessar cluster com Azure AD

Em seguida, efetue pull do contexto para o usuário não administrador usando o comando [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Depois de executar qualquer comando kubectl, você será solicitado a autenticar com o Azure. Siga as instruções na tela.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Após concluído, o token de autenticação será armazenado em cache. Você será novamente solicitado a fazer logon somente quando o token expirar ou o arquivo de configuração do Kubernetes for recriado.

Caso receba uma mensagem de erro de autorização depois de entrar, verifique se o usuário com o qual você está entrando é um convidado no Microsoft Azure AD. Geralmente, isso se aplica quando você usa um logon federado de outro diretório.
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como proteger os clusters do Kubernetes com RBAC com a documentação [Usar autorização de RBAC][rbac-authorization].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
