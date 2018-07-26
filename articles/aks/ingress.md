---
title: Configurar entrada com o cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e configurar um controlador de ingresso NGINX que usa Vamos Criptografar para geração de certificado SSL automática no cluster do Serviço de Kubernetes do Azure (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c65cfec41c2002fd4d4ff27ea74daf0bb4246b5f
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145590"
---
# <a name="deploy-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Implante um controlador de ingresso HTTPS no Serviço de Kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço externo pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente certificados [Vamos Criptografar][lets-encrypt]. Finalmente, vários aplicativos executam no cluster do AKS, cada um dos quais é acessível em um único endereço.

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o NGINX ingress controller, Gerenciador de certificado e um aplicativo web de exemplo. Você precisa ter o Helm inicializado dentro do cluster do AKS e usar uma conta de serviço para Tiller. Para obter mais informações sobre como configurar e usar o Helm, consulte [Instalar aplicativos com Helm no Serviço de Kubernetes do Azure (AKS)][use-helm].

Este artigo exige que você esteja executando a versão 2.0.41 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="install-an-ingress-controller"></a>Instalar um controlador de entrada

Use Helm para instalar o controlador de entrada NGINX. Para obter informações de implantação detalhadas, consulte a [documentação][nginx-ingress].

O exemplo a seguir instala o controlador no `kube-system` namespace. Você pode especificar um namespace diferente para seu próprio ambiente. Se o cluster AKS não for habilitado em RBAC, adicione `--set rbac.create=false` ao comando.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Para obter o endereço IP público, use `kubectl get service` o comando de serviço kubectl get. Pode levar alguns minutos até que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, página 404 padrão do controlador de ingresso NGINX é exibida, conforme mostrado no exemplo a seguir:

![Back-end de NGINX padrão](media/ingress/default-back-end.png)

## <a name="configure-a-dns-name"></a>Configurar nome DNS

Para que os certificados HTTPS funcione corretamente, configure um FQDN para o endereço IP do controlador de entrada. Atualize o script com o endereço IP do controlador de entrada e o nome que você pretender usar no FQDN.

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

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

Para instalar o controlador cert-manager, use o comando `helm install` de instalação Helm a seguir:

```console
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se o cluster não for habilitado para RBAC, em vez disso, use o seguinte comando:

```console
helm install stable/cert-manager \
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

Para criar a coleção, use o `kubectl create -f cluster-issuer.yaml`comando: 

```
$ kubectl create -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Criar objeto de certificado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X.509 desejado. Para saber mais, confira [certificados cert-manager][cert-manager-certificates].

Crie o recurso de certificado, como `certificates.yaml`, com o seguinte manifesto de exemplo. Atualizar o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior.

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

Para criar o recurso de certificado, use o `kubectl create -f certificates.yaml` comando.

```
$ kubectl create -f certificates.yaml

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

Crie um arquivo chamado `hello-world-ingress.yaml` e copie no exemplo YAML a seguir:

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

Crie o recurso de entrada usando o `kubectl create -f hello-world-ingress.yaml` comando.

```
$ kubectl create -f hello-world-ingress.yaml

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

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
