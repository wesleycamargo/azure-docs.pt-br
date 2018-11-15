---
title: Crie um controlador de entrada HTTP com um endereço IP estático no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e configurar um controlador de entrada NGINX com um endereço IP público estático em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3e65fc863d065e68948f417fcc22ececcf5271c8
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515430"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Crie um controlador de entrada com um endereço IP público estático no AKS (Serviço de Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) O controlador de entrada é configurado com um endereço IP público estático. O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente certificados [Vamos Criptografar][lets-encrypt]. Finalmente, dois aplicativos são executados no cluster do AKS, cada um dos quais é acessível em um único endereço IP.

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de ingresso que usa o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público dinâmico][aks-ingress-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o NGINX ingress controller, Gerenciador de certificado e um aplicativo web de exemplo. Você precisa ter o Helm inicializado dentro do cluster do AKS e usar uma conta de serviço para Tiller. Verifique se você está usando a versão mais recente do Helm. Para obter instruções de atualização, confira os [Documentos de instalação do Helm][helm-install]. Para obter mais informações sobre como configurar e usar o Helm, consulte [Instalar aplicativos com Helm no Serviço de Kubernetes do Azure (AKS)][use-helm].

Este artigo exige que você esteja executando a versão 2.0.41 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por padrão, um controlador de entrada é criado com uma nova atribuição de endereço IP público. Esse endereço IP público somente é estático durante o período de vida do controlador de entrada e será perdido se o controlador for excluído e recriado. Um requisito de configuração comum é fornecer ao controlador de entrada NGINX um endereço IP público estático existente. O endereço IP público estático permanecerá se o controlador de entrada for excluído. Essa abordagem permite que você use registros DNS existentes e configurações de rede consistentemente durante todo o ciclo de vida dos seus aplicativos.

Se você precisar criar um endereço IP público estático, obtenha primeiro o nome do grupo de recursos do cluster do AKS com o comando [az aks show][az-aks-show]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Em seguida, crie um endereço IP público com o método de alocação *estático* usando o comando [az network public-ip create][az-network-public-ip-create]. O exemplo a seguir cria um endereço IP público denominado *myAKSPublicIP* no grupo de recursos do cluster AKS obtido na etapa anterior:

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Agora, implemente o gráfico *nginx -gresso* com o Helm. Adicione o parâmetro `--set controller.service.loadBalancerIP` e especifique seu próprio endereço IP público criado na etapa anterior. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

> [!TIP]
> Os exemplos a seguir instalam o controlador de entrada e os certificados no namespace `kube-system`. É possível especificar um namespace diferente para seu próprio ambiente se desejado. Além disso, se o cluster AKS não for habilitado em RBAC, adicione `--set rbac.create=false` aos comandos.

```console
helm install stable/nginx-ingress \
    --namespace kube-system \
    --set controller.service.loadBalancerIP="40.121.63.72"  \
    --set controller.replicaCount=2
```

Quando o serviço de balanceador de carga do Kubernetes é criado para o controlador de entrada NGINX, seu endereço IP estático é atribuído, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Nenhuma regra de ingresso foi criada ainda, portanto, a página 404 padrão do controlador de entrada NGINX será exibida se você navegar para o endereço IP público. As regras de ingresso são configuradas nas etapas a seguir.

## <a name="configure-a-dns-name"></a>Configurar nome DNS

Para que os certificados HTTPS funcione corretamente, configure um FQDN para o endereço IP do controlador de entrada. Atualize o script com o endereço IP do controlador de entrada e o nome que você pretender usar no FQDN.

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

O controlador de entrada agora está acessível por meio do FQDN.

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada NGINX dá suporte para terminação TLS. Há várias maneiras de recuperar e configurar certificados para HTTPS. Este artigo demonstra como usar o [manager cert][cert-manager], que fornece a geração automática do certificado [Vamos Criptografar][lets-encrypt] e funcionalidade de gerenciamento.

> [!NOTE]
> Este artigo usa o `staging` ambiente para Vamos criptografar. Em implantações de produção, use `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições do recurso e ao instalar o gráfico do Helm.

Para instalar o controlador cert-manager em um cluster habilitado em RBAC, use o comando `helm install` a seguir. Novamente, se desejado, altere `--namespace` para algo diferente de *kube-system*:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se o cluster não for habilitado para RBAC, em vez disso, use o seguinte comando:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Para obter mais informações sobre a configuração docert-manager, consulte o projeto [cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar o emissor de cluster de CA

Antes dos certificados poderem ser emitidos, o cert-manager requer um [Emissor][cert-manager-issuer] ou recurso [ClusterIssuer][cert-manager-cluster-issuer]. Esses recursos Kubernetes são idênticos na funcionalidade porém `Issuer` funcionam em um único namespace onde `ClusterIssuer` funciona em todos os namespaces. Para obter mais informações, consulte a [documentação do emissor do cert-manager][cert-manager-issuer].

Crie um emissor de cluster, como `cluster-issuer.yaml`, usando o manifesto a seguir. Atualize o endereço de email com um endereço válido da sua organização:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Para criar a coleção, use o `kubectl apply -f cluster-issuer.yaml`comando: 

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Criar objeto de certificado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X.509 desejado. Para saber mais, confira [certificados cert-manager][cert-manager-certificates].

Crie o recurso de certificado, como `certificates.yaml`, com o seguinte manifesto de exemplo. Atualizar o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior. Se você usar um controlador de entrada somente interno, especifique o nome DNS interno para seu serviço.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Para criar o recurso de certificado, use o `kubectl apply -f certificates.yaml` comando.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e uma solução de gerenciamento de certificados foi configurado. Agora, vamos executar duas demonstrações de aplicativos no cluster do AKS. Neste exemplo, o Helm é utilizado para executar várias instâncias de um aplicativo simples hello world.

Antes de instalar os gráficos do Helm de exemplo, adicione o repositório de exemplos do Azure para seu ambiente de Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld
```

Em seguida, instale uma segunda instância do aplicativo demo. Para a segunda instância, especifique um novo título para que os dois aplicativos sejam visualmente distintos. Você também precisa especificar um nome de serviço exclusivo:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos estão em execução no cluster de Kubernetes, no entanto, foram configurados com um serviço do tipo `ClusterIP`. Dessa forma, os aplicativos não são acessíveis a partir da Internet. Para torná-los disponíveis publicamente, crie um recurso de entrada de Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é roteado para o serviço `ingress-demo`. Atualize o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador da web para o FQDN do controlador de entrada de Kubernetes, tais como *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Como esses exemplos usam `letsencrypt-staging`, o certificado SSL emitido não é confiável pelo navegador. Aceite a mensagem de aviso para continuar a seu aplicativo. As informações de certificado mostram que este certificado *Fake LE Intermediate X1* é emitido pelo Vamos Criptografar. Esse certificado falso indica que `cert-manager` processou a solicitação corretamente e que foi recebida de um certificado do provedor:

![Certificado de preparo Vamos Criptografar](media/ingress/staging-certificate.png)

Quando você altera Vamos Criptografar para usar `prod` em vez de `staging`, um certificado confiável emitido pelo vamos criptografar é usado, conforme mostrado no exemplo a seguir:

![Certificado Vamos Criptografar](media/ingress/certificate.png)

O aplicativo de demonstração é mostrado no navegador da web:

![Exemplo de aplicativo um](media/ingress/app-one.png)

Agora, adicione o caminho */hello-world-two* para o FQDN, como *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. O segundo aplicativo de demonstração com o título personalizado é mostrado:

![Exemplo de aplicativo dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso, os certificados e os aplicativos de exemplo. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar, remova primeiro os recursos do certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora liste as versões do Helm com o comando `helm list`. Procure gráficos com o nome *nginx-ingress*, *cert-manager* e *aks-helloworld* conforme mostrado na saída do exemplo a seguir:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Tue Oct 16 17:44:28 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
alliterating-peacock    1           Tue Oct 16 18:03:11 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
mollified-armadillo     1           Tue Oct 16 18:04:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Tue Oct 16 18:04:56 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Excluir as versões com o `helm delete` comando. O exemplo a seguir exclui a implantação de ingresso NGINX, o gerenciador de certificados e os dois aplicativos de exemplo Olá, Mundo do AKS.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Por fim, remova o endereço IP público estático criado para o controlador de entrada. Forneça seu nome do grupo de recursos de cluster *MC_* obtidos na primeira etapa deste artigo, tal como *MC_meuGrupoDeRecursos_meuClusterCluster_lestedoseua*:

```azurecli
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]
- [cert-manager][cert-manager]

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada com um IP público dinâmico e configurar Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
