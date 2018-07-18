---
title: Configurar entrada com o cluster do AKS (Serviço de Kubernetes do Azure)
description: Instale e configure um controlador de entrada NGINX em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096981"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Entrada HTTPS no AKS (Serviço do Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço externo pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este documento descreve uma implantação de exemplo do [Controlador de entrada NGINX][nginx-ingress] no cluster do AKS (Serviço de Kubernetes do Azure). Além disso, o projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente certificados [Vamos Criptografar][lets-encrypt]. Finalmente, vários aplicativos executam no cluster do AKS, cada um dos quais é acessível em um único endereço.

## <a name="install-an-ingress-controller"></a>Instalar um controlador de entrada

Use Helm para instalar o controlador de entrada NGINX. Consulte a [documentação][nginx-ingress] do controlador de entrada NGINX para obter informações detalhadas sobre a implantação.

Este exemplo instala o controlador no namespace `kube-system`, que pode ser modificado para um namespace de sua escolha. Se o cluster AKS não for habilitado em RBAC, adicione `--set rbac.create=false` ao comando. Para obter mais informações, consulte o gráfico [nginx-ingress][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Para obter o endereço IP público, use o comando de serviço kubectl get. Talvez demore um pouco até que o endereço IP seja atribuído ao serviço.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Como não foram criadas regras de entrada, se navegar para o endereço IP público você será encaminhado para a página 404 padrão dos controladores de entrada NGINX.

![Back-end de NGINX padrão](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Configurar nome DNS

Como são utilizados certificados HTTPS, será necessário configurar um nome FQDN para o endereço IP dos controladores de entrada. Para esse exemplo, um FQDN do Azure é criado com a CLI do Azure. Atualize o script com o endereço IP do controlador de entrada e o nome que você pretender usar no FQDN.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

O controlador de entrada deve agora estar acessível através do FQDN.

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada NGINX dá suporte para terminação TLS. Embora existam várias maneiras de recuperar e configurar certificados para HTTPS, este documento demonstra o uso de [cert-manager][cert-manager], que fornece a funcionalidade de gerenciamento e geração de certificado automática [Vamos Criptografar][lets-encrypt].

Para instalar o controlador cert-manager, use o comando de instalação Helm a seguir.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se o cluster não for habilitado em RBAC, use este comando.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Para obter mais informações sobre a configuração docert-manager, consulte o projeto [cert-manager][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Criar o emissor de cluster de CA

Antes dos certificados poderem ser emitidos, o cert-manager requer um [Emissor][cert-manager-issuer] ou recurso [ClusterIssuer][cert-manager-cluster-issuer]. Os recursos são idênticos na funcionalidade porém `Issuer` funcionam em um único namespace onde `ClusterIssuer` funciona em todos os namespaces. Para obter mais informações, consulte a [documentação do emissor do cert-manager][cert-manager-issuer].

Crie um emissor de cluster usando o manifesto a seguir. Atualize o endereço de email com um endereço válido da sua organização.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Criar objeto de certificado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X.509 desejado. Para saber mais, confira [certificados cert-manager][cert-manager-certificates].

Crie o recurso de certificado com o manifesto a seguir.

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Executar aplicativo

Neste ponto, um controlador de entrada e uma solução de gerenciamento de certificados foram configurados. Agora, execute alguns aplicativos no cluster do AKS.

Para esse exemplo, o Helm é utilizado para executar várias instâncias de um aplicativo simples hello world.

Antes de executar o aplicativo, adicione o repositório Helm de exemplos do Azure no seu sistema de desenvolvimento.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Execute o gráfico hello world do AKS com o comando a seguir:

```bash
helm install azure-samples/aks-helloworld
```

Em seguida, instale uma segunda instância do aplicativo hello world.

Para a segunda instância, especifique um novo título para que os dois aplicativos sejam visualmente distintos. Você também precisa especificar um nome de serviço exclusivo. Essas configurações podem ser vistas no comando a seguir.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos estão em execução no cluster de Kubernetes, no entanto, foram configurados com um serviço do tipo `ClusterIP`. Dessa forma, os aplicativos não são acessíveis a partir da Internet. Para torná-los disponíveis, crie um recurso de entrada de Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

Crie um nome de arquivo `hello-world-ingress.yaml` e copie o YAML a seguir.

Observe que o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é roteado para o serviço `ingress-demo`.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

Crie o recurso de entrada com o comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Navegue até o FQDN do controlador de entrada de Kubernetes e você deverá visualizar o aplicativo hello world.

![Exemplo de aplicativo um](media/ingress/app-one.png)

Em seguida, navegue até o FQDN do controlador de entrada com o caminho `/hello-world-two` e você deverá visualizar o aplicativo hello world com o título personalizado.

![Exemplo de aplicativo dois](media/ingress/app-two.png)

Observe também que a conexão é criptografada e que um certificado emitido pelo Vamos Criptografar é utilizado.

![Permitir criptografar o certificado](media/ingress/certificate.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o software demonstrado neste documento.

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
