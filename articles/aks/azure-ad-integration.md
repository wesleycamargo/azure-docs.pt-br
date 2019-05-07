---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Como criar clusters do Serviço de Kubernetes do Azure (AKS) habilitados para Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138456"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, você pode entrar um cluster AKS usando o token de autenticação do Active Directory do Azure. Além disso, os administradores de cluster são capazes de configurar o controle de acesso baseado em função de Kubernetes (RBAC) com base na associação de grupo de diretório ou a identidade do usuário.

Este artigo mostra como implantar os pré-requisitos para o AKS e o Azure AD e, em seguida, como implantar um cluster do Azure AD habilitado e criar uma função RBAC básica no cluster AKS usando o portal do Azure. Você também pode [concluir essas etapas usando a CLI do Azure][azure-ad-cli].

As seguintes limitações se aplicam:

- O Azure AD só pode ser habilitado quando você cria um cluster novo habilitado para RBAC. Não é possível habilitar o Azure AD em um cluster existente do AKS.
- *Convidado* usuários no Azure AD, tais como se você estiver usando uma entrada federada de um diretório diferente, não têm suporte.

## <a name="authentication-details"></a>Detalhes de autenticação

A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Mais informações sobre OpenID Connect podem ser encontradas na [documentação do Open ID Connect][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS. Mais informações sobre autenticação de token do Webhook podem ser encontradas na [documentação de autenticação do webhook][kubernetes-webhook].

Para fornecer a autenticação do Azure AD para um cluster do AKS, dois aplicativos do AD do Azure são criados. O primeiro aplicativo é um componente de servidor que fornece a autenticação do usuário. O segundo aplicativo é um componente de cliente que é usado quando for solicitado pela CLI para autenticação. Esse aplicativo cliente usa o aplicativo de servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. As etapas para delegar permissões para cada um dos aplicativos devem ser concluídas por um administrador de locatário do Azure.

## <a name="create-server-application"></a>Criar aplicativo do servidor

O primeiro aplicativo do Azure AD é usado para obter uma associação do grupo de usuários do Azure AD. Crie esse aplicativo no portal do Azure.

1. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro**.

    * Nomeie o aplicativo, tais como *AKSAzureADServer*.
    * Para **suporte para tipos de conta**, escolha *contas neste diretório organizacional apenas*.
    * Escolher *Web* para o **URI de redirecionamento** digite e insira qualquer valor URI formatado como *https://aksazureadserver*.
    * Selecione **registrar** quando terminar.

1. Selecione **Manifesto** e edite o valor `groupMembershipClaims` para `"All"`.

    ![Atualizar a associação de grupo para todos](media/aad-integration/edit-manifest.png)

    **Salvar** as atualizações após a conclusão.

1. Na barra de navegação à esquerda do aplicativo do AD do Azure, selecione **certificados e segredos**.

    * Escolher **+ novo segredo do cliente**.
    * Adicione uma descrição da chave, como *servidor do AD do Azure do AKS*. Escolha um tempo de expiração e selecione **adicionar**.
    * Anote o valor da chave. Ele tem apenas exibido neste momento inicial. Quando você implanta um cluster do AKS do Azure AD habilitado, esse valor é referenciado como o `Server application secret`.

1. Na barra de navegação à esquerda do aplicativo do AD do Azure, selecione **permissões de API**, em seguida, escolher **+ adicionar uma permissão**.

    * Sob **APIs do Microsoft**, escolha *o Microsoft Graph*.
    * Escolher **permissões delegadas**, em seguida, coloque uma verificação de lado **Directory > Read (ler dados do diretório)**.
        * Se um padrão recebido permissão para **usuário > Read (entrar e ler perfil do usuário)** não existir, marque essa permissão.
    * Escolher **permissões de aplicativo**, em seguida, coloque uma verificação de lado **Directory > Read (ler dados do diretório)**.

        ![Conjunto de permissões do graph](media/aad-integration/graph-permissions.png)

    * Escolher **adicionar permissões** para salvar as atualizações.

    * Sob o **conceder consentimento** seção, escolha a opção **conceder consentimento do administrador**. Esse botão estiver esmaecido e não estará disponível se a conta atual não é um administrador de locatário.

        Quando as permissões tiverem sido concedidas com êxito, a notificação a seguir será exibida no portal:

        ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

1. Na barra de navegação à esquerda do aplicativo do AD do Azure, selecione **expor uma API**, em seguida, escolher **+ adicionar um escopo**.
    
    * Definir um *nome do escopo*, *nome de exibição de consentimento do administrador*, e *descrição de consentimento do administrador*, como *AKSAzureADServer*.
    * Verifique se o **estado** é definido como *habilitado*.

        ![Expor o aplicativo de servidor como uma API para uso com outros serviços](media/aad-integration/expose-api.png)

    * Escolher **adicionar escopo**.

1. Retornar para o aplicativo **visão geral** da página e anote as **ID do aplicativo (cliente)**. Quando você implanta um cluster do AKS do Azure AD habilitado, esse valor é referenciado como o `Server application ID`.

   ![Obter ID do aplicativo](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Criar aplicativo cliente

O segundo aplicativo do Azure AD é usado ao fazer logon com a CLI Kubernetes (`kubectl`).

1. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro**.

    * Nomeie o aplicativo, tais como *AKSAzureADClient*.
    * Para **suporte para tipos de conta**, escolha *contas neste diretório organizacional apenas*.
    * Escolher *Web* para o **URI de redirecionamento** digite e insira qualquer valor URI formatado como *https://aksazureadclient*.
    * Selecione **registrar** quando terminar.

1. Na barra de navegação à esquerda do aplicativo do AD do Azure, selecione **permissões de API**, em seguida, escolher **+ adicionar uma permissão**.

    * Selecione **Minhas APIs**, em seguida, escolha o aplicativo de servidor do AD do Azure criado na etapa anterior, como *AKSAzureADServer*.
    * Escolher **permissões delegadas**, em seguida, colocar uma marca de seleção ao lado de seu aplicativo de servidor do AD do Azure.

        ![Configurar permissões de aplicativo](media/aad-integration/select-api.png)

    * Selecione **adicionar permissões**.

    * Sob o **conceder consentimento** seção, escolha a opção **conceder consentimento do administrador**. Esse botão estiver esmaecido e não estará disponível se a conta atual não é um administrador de locatário.

        Quando as permissões tiverem sido concedidas com êxito, a notificação a seguir será exibida no portal:

        ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

1. Na barra de navegação à esquerda do aplicativo do AD do Azure, anote o **ID do aplicativo**. Ao implantar um cluster do AKS habilitado para Azure AD, esse valor será referido como `Client application ID`.

   ![Obtenha a ID do aplicativo](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obter a ID do locatário

Finalmente, obtenha a ID do locatário do Azure. Esse valor é usado quando você cria o cluster do AKS.

No portal do Azure, selecione **Azure Active Directory** > **Propriedades** e anote a **ID de Diretório**. Quando você cria um cluster do AKS do Azure AD habilitado, esse valor é referenciado como o `Tenant ID`.

![Obter a ID do locatário do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Implantar o cluster

Utilize o comando [az group create][az-group-create] para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Implante o cluster usando o comando [az aks create][az-aks-create]. Substitua os valores no comando de exemplo abaixo com os valores coletados durante a criação de aplicativos do Azure AD para a identificação do servidor de aplicativo e segredo, ID do aplicativo cliente e ID de locatário:

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Demora alguns minutos para criar o cluster do AKS.

## <a name="create-rbac-binding"></a>Criar associação do RBAC

Antes que uma conta do Azure Active Directory possa ser usada com o cluster do AKS, uma associação de função ou uma associação de função de cluster precisa ser criada. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Primeiro, use o [az aks get-credentials] [ az-aks-get-credentials] com o `--admin` argumento para entrar no cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie um ClusterRoleBinding para uma conta do Azure AD que você deseja conceder acesso ao cluster do AKS. O exemplo a seguir fornece acesso completo à conta para todos os namespaces no cluster.

- Se o usuário que você conceder que a associação de RBAC para está no mesmo locatário do AD do Azure, atribua permissões com base no nome de usuário principal (UPN). Vá para a etapa para criar um manifesto para o ClusterRuleBinding YAML.

- Se o usuário estiver em um Azure AD diferente de locatário, consultar e usar o *objectId* propriedade em vez disso. Se necessário, obtenha o *objectId* do usuário exigido de conta usando o [Mostrar de usuário do ad az] [ az-ad-user-show] comando. Forneça o nome principal do usuário (UPN) da conta necessária:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um arquivo, como *rbac-aad-user.yaml*e cole o seguinte conteúdo. Na última linha, substitua *userPrincipalName_or_objectId* com a ID de objeto ou UPN, dependendo se o usuário é o mesmo locatário do AD do Azure ou não.

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
  name: userPrincipalName_or_objectId
```

Aplique a associação com o comando [aplicar kubectl] [ kubectl-apply] conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Uma associação de função também pode ser criada para todos os membros de um grupo do Azure AD. Os grupos do Microsoft Azure AD são especificados com a ID de objeto do grupo, como mostrado no seguinte exemplo. Crie um arquivo, como *rbac-aad-group.yaml* e cole o seguinte conteúdo. Atualize a ID do objeto do grupo com um do seu locatário do Azure AD:

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

Aplique a associação com o comando [aplicar kubectl] [ kubectl-apply] conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster do Kubernetes com RBAC, consulte [Usar autorização de RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Acessar cluster com Azure AD

Em seguida, efetue pull do contexto para o usuário não administrador usando o comando [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de executar um `kubectl` de comando, você será solicitado a autenticar com o Azure. Siga na tela instruções para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Ao concluir, o token de autenticação é armazenado em cache. Você só é solicitado a fornecer a entrar quando o token tiver expirado ou o arquivo de configuração do Kubernetes criado novamente.

Caso receba uma mensagem de erro de autorização depois de entrar, verifique se:
1. O usuário você está tentando entrar como é não um convidado na instância do AD do Azure (esse cenário é geralmente o caso se você usar uma conta federada de um diretório diferente).
2. O usuário não é um membro de mais de 200 grupos.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Próximas etapas

Para usar grupos e usuários do AD do Azure para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos de cluster usando o controle de acesso baseado em função e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS)][rbac-authorization].

Para as práticas recomendadas de controle de identidade e recursos, consulte [práticas recomendadas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
