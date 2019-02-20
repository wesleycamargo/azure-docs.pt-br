---
title: Proteger os pods com políticas de rede no Serviço de Kubernetes do Azure (AKS)
description: Saiba como proteger o tráfego de entrada e saída dos pods usando políticas de rede do Kubernetes no Serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232167"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)

Ao executar aplicativos modernos baseados em microsserviços no Kubernetes, muitas vezes você deseja controlar quais componentes podem se comunicar uns com os outros. O princípio de privilégio mínimo deve ser aplicado a como o tráfego pode fluir entre os pods em um cluster AKS. Por exemplo, você provavelmente deseja bloquear o tráfego diretamente para os aplicativos de back-end. No Kubernetes, o recurso *Política de Rede* possibilita definir regras para o tráfego de entrada e saída entre os pods em um cluster.

Este artigo mostra como usar políticas de rede para controlar o fluxo de tráfego entre os pods no AKS.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.56 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Por padrão, todos os pods em um cluster do AKS podem enviar e receber tráfego sem limitações. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Por exemplo, os aplicativos de back-end geralmente são expostos somente aos serviços de front-end necessários ou os componentes de banco de dados são acessíveis somente para as camadas de aplicativos que se conectam a eles.

As políticas de rede são recursos do Kubernetes que possibilitam controlar o fluxo de tráfego entre os pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos atribuídos, namespace ou porta de tráfego. As políticas de rede são definidas como manifestos YAML e podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou serviço.

Para ver as políticas de rede em ação, vamos criar e, em seguida, expandir uma política que define o fluxo de tráfego da seguinte maneira:

* Nega todo o tráfego ao pod.
* Permite o tráfego com base nos rótulos do pod.
* Permite o tráfego com base no namespace.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Cria um cluster do AKS e habilita a política de rede

A política de rede só pode ser habilitada ao criar o cluster. Você não pode habilitar a política de rede em um cluster existente do AKS. Para criar um AKS com a política de rede, primeiro habilite um sinalizador de recurso na sua assinatura. Para registrar o sinalizador de recurso *EnableNetworkPolicy*, use o comando [az feature register][az-feature-register] como mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft.ContainerService* usando o comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Para usar a política de rede com um cluster do AKS, você deve usar o [Plug-in CNI do Azure][azure-cni] e definir sua própria rede virtual e sub-redes. Para saber mais sobre como planejar os intervalos de sub-rede necessários, consulte [Configurar a rede avançada][use-advanced-networking]. O exemplo de script a seguir:

* Cria uma rede virtual e uma sub-rede.
* Cria uma entidade de serviço do AAD (Azure Active Directory) para uso com o cluster do AKS.
* Atribui permissões de *Colaborador* para a entidade de serviço do cluster do AKS em uma rede virtual.
* Cria um cluster do AKS na rede virtual definida e habilita a política de rede.

Forneça sua própria *SP_PASSWORD* segura. Se desejar, substitua as variáveis *RESOURCE_GROUP_NAME* e *CLUSTER_NAME*:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

São necessários alguns minutos para criar o cluster. Ao finalizar, configure `kubectl` para se conectar ao cluster do Kubernetes usando o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para um pod

Antes de definir as regras para permitir o tráfego de rede específico, primeiro crie uma política para negar todo o tráfego de rede. Essa política fornece um ponto de partida para começar a lista de permissões somente com o tráfego desejado. Você também pode ver claramente que o tráfego é removido quando a política de rede é aplicada.

Para nosso exemplo de ambiente de aplicativo e de regras de tráfego, primeiro vamos criar um namespace chamado *development* para executar nossos pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Agora, crie um exemplo de pod de back-end que executa o NGINX. Este pod de back-end pode ser usado para simular um exemplo de aplicativo baseado na Web de back-end. Crie este pod no namespace *development* e abra a porta *80* para servir o tráfego da Web. Rotule o pod com *app=webapp,role=backend* para que possamos alcançá-lo com uma política de rede na próxima seção:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Para testar se você consegue acessar com êxito a página padrão do NGINX na Web, crie outro pod e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez no prompt de shell, use `wget` para confirmar que você pode acessar a página padrão do NGINX na Web:

```console
wget -qO- http://backend
```

A saída de exemplo a seguir mostra que a página padrão do NGINX na Web retornou:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que você confirmou que pode acessar a página básica do NGINX na Web no pod de back-end de exemplo, crie uma política de rede para negar todo o tráfego. Crie um arquivo chamado `backend-policy.yaml` e cole o manifesto YAML a seguir. Esse manifesto usa um *podSelector* para anexar a política a pods que têm o rótulo *app:webapp,role:backend*, como no pod NGINX de exemplo. Nenhuma regra é definida em *ingress*, portanto, todo o tráfego de entrada para o pod é negado:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Aplique a política de rede usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede

Vamos ver se você pode acessar a página da Web do NGINX no pod de back-end novamente. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez no prompt de shell, use `wget` para ver se você pode acessar a página padrão do NGINX na Web. Desta vez, defina um valor de tempo limite em *2* segundos. A política de rede agora bloqueia todo o tráfego de entrada, portanto, a página não pode ser carregada, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base em um rótulo de pod

Na seção anterior, um pod NGINX de back-end foi agendado e uma política de rede foi criada para negar todo o tráfego. Agora vamos criar um pod de front-end e atualizar a política de rede para permitir o tráfego dos pods de front-end.

Atualize a política de rede para permitir o tráfego dos pods com os rótulos *app:webapp,role:frontend* e em qualquer namespace. Edite o arquivo *backend-policy.yaml* anterior e adicione uma regra de entrada *matchLabels* para que o manifesto se pareça com o exemplo a seguir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Aplique a política de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Agora agende um pod que está rotulado como *app=webapp,role=frontend* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Uma vez no prompt de shell, use `wget` para ver se você pode acessar a página padrão do NGINX na Web:

```console
wget -qO- http://backend
```

Como a regra de entrada permite o tráfego com pods que têm rótulos *app:webapp,role:frontend*, o tráfego do pod de front-end é permitido. A saída de exemplo a seguir mostra a página Web padrão do NGINX retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod é excluído automaticamente:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testar um pod sem um rótulo correspondente

A política de rede permite o tráfego dos pods rotulados com *app:webapp,role:frontend*, mas deve negar todos os outros tráfegos. Vamos testar se outro pod sem esses rótulos não pode acessar o pod NGINX de back-end. Crie outro pod de teste e anexe uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez no prompt de shell, use `wget` para ver se você pode acessar a página padrão do NGINX na Web. A política de rede bloqueia o tráfego de entrada, portanto, a página não pode ser carregada, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego somente dentro de um namespace definido

Nos exemplos anteriores, você criou uma política de rede que negou todo o tráfego, em seguida, atualizou a política para permitir o tráfego dos pods com um rótulo específico. Uma outra necessidade comum é limitar o tráfego somente dentro de um namespace específico. Se os exemplos anteriores foram para o tráfego em um namespace *development*, talvez você queira criar uma política de rede que impeça o tráfego de outro namespace, tal como *production*, de alcançar os pods.

Primeiro crie um novo namespace para simular um namespace de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende um pod de teste no namespace *production* que esteja rotulado como *app=webapp,role=frontend*. Anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Uma vez no prompt de shell, use `wget` para confirmar que você pode acessar a página padrão do NGINX na Web:

```console
wget -qO- http://backend.development
```

Como os rótulos para o pod correspondem com o que é permitido na política de rede no momento, o tráfego é permitido. A política de rede não examina os namespaces, somente os rótulos de pod. A saída de exemplo a seguir mostra a página Web padrão do NGINX retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente:

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Agora vamos atualizar a regra de entrada da seção *namespaceSelector* para permitir apenas o tráfego de dentro do namespace *development*. Edite o arquivo de manifesto *backend-policy.yaml*, como mostrado no exemplo a seguir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Nos exemplos mais complexos, você pode definir várias regras de entrada, como usar um *namespaceSelector* e, em seguida, um *podSelector*.

Aplique a política de rede atualizada usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agora agende outro pod no namespace *production* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Uma vez no prompt do shell, use `wget` para ver a política de rede agora negar o tráfego:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Saia do pod de teste:

```console
exit
```

Com o tráfego negado do namespace *production*, agora agende um pod de teste novamente no namespace *development* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Uma vez no prompt do shell, use `wget` para ver a política de rede permitir o tráfego:

```console
wget -qO- http://backend
```

Como o pod está agendado no namespace que corresponde ao que é permitido na política de rede, o tráfego é permitido. A saída de exemplo a seguir mostra a página padrão do NGINX na Web retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão de terminal anexada. O pod de teste é excluído automaticamente:

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criamos dois namespaces e aplicamos uma política de rede. Para limpar esses recursos, use o comando [kubectl delete][kubectl-delete] e especifique os nomes de recurso da seguinte maneira:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre recursos de rede, consulte [Conceitos de rede para aplicativos no Serviço de Kubernetes do Azure (AKS)][concepts-network].

Para saber mais sobre como usar as políticas, consulte [Políticas de rede do Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
