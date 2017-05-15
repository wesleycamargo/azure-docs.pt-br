---
title: "Personalize uma máquina virtual do Linux na primeira inicialização do Azure | Microsoft Docs"
description: "Saiba como usar inicialização de nuvem e o Key Vault para personalizar as máquinas virtuais do Linux na primeira vez em que são iniciadas no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 188c4758843a49ca38a151835d561c5f2d58d3a0
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---

# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Como personalizar uma máquina virtual de Linux na primeira inicialização
Para criar máquinas virtuais (VMs) de uma maneira rápida e consistente, alguma forma de automação é em geral desejada. Uma abordagem comum para personalizar uma VM na primeira inicialização é utilizar [inicialização de nuvem](https://cloudinit.readthedocs.io). Este tutorial descreve como utilizar a inicialização de nuvem para instalar pacotes, configurar o servidor de web NGINX e implantar um aplicativo Node. js automaticamente.

As etapas deste tutorial podem ser concluídas usando o módulo mais recente do [Azure CLI 2.0](/cli/azure/install-azure-cli).


## <a name="cloud-init-overview"></a>Visão geral da inicialização de nuvem
[Inicialização de nuvem](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como a inicialização de nuvem é executada durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalação e a inicialização de nuvem utiliza automaticamente a ferramenta de gerenciamento de pacote nativo para a distribuição que você selecionar.

Estamos trabalhando com parceiros para incluir a inicialização de nuvem e trabalhar nas imagens que eles fornecem para o Azure. A tabela a seguir descreve a disponibilidade de inicialização de nuvem atual nas imagens da plataforma Azure:

| Alias | Editor | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canônico |UbuntuServer |14.04.4-LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |


## <a name="create-cloud-init-config-file"></a>Criar arquivo de configuração cloud-init
Para ver a inicialização de nuvem em ação, crie uma VM que instala o NGINX e execute um simples "Hello World" do aplicativo do Node. js. A seguinte configuração de inicialização de nuvem instala os pacotes necessários, cria um aplicativo do Node. js, em seguida, inicializa e inicia o aplicativo.

Crie um arquivo chamado *cloud-init.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Para obter mais informações sobre opções de configuração de inicialização de nuvem, consulte [exemplos de configuração de inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)]

## <a name="create-virtual-machine"></a>Criar máquina virtual
Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#create). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupAutomate* no local *westus*:

```azurecli
az group create --name myResourceGroupAutomate --location westus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#create). Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *myAutomatedVM*:

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Demora alguns minutos para que a VM seja criada, os pacotes para instalar e iniciar o aplicativo. Quando a VM tiver sido criada, observe o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o aplicativo do Node. js por meio de um navegador da web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 80 da Internet com [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Testar o aplicativo da web
Agora, abra um navegador da Web e digite *http://<publicIpAddress>* na barra de endereços. Forneça seu próprio endereço de IP público do processo de criação da máquina virtual. Seu aplicativo Node. js é exibido como no exemplo a seguir:

![Exibir o site NGINX em execução](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Injetar certificados do Cofre de chave
Essa seção mostra como você pode armazenar certificados no Cofre de Chaves do Azure e colocá-los durante a implantação da máquina virtual com segurança. Em vez de usar uma imagem personalizada que inclui os certificados embutida, esse processo garante que os certificados mais recentes são injetados em uma máquina virtual na primeira inicialização. Durante o processo, o certificado nunca deixa a plataforma Azure ou é exposto em um script, o histórico de linha de comando ou o modelo.

O Azure Key Vault protege chaves e segredos criptográficos, como certificados ou senhas. O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Este cenário apresenta alguns conceitos de Cofre da Chave para criar e usar um certificado, porém não é uma visão geral detalhada sobre como usar o Cofre da Chave.

As etapas a seguir mostram como você pode:

- Criar um Cofre de chaves do Azure
- Gerar ou carregar um certificado para o Cofre da Chave
- Criar um segredo do certificado para inserir em uma máquina virtual
- Criar uma máquina virtual e inserir o certificado

### <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure
Primeiro, crie um Cofre de Chaves com o [az keyvault create](/cli/azure/keyvault#create) e habilitá-lo para uso quando você implanta uma máquina virtual. Cada Cofre de Chave requer um nome exclusivo e deve estar escrito com todas as letras minúsculas. Substitua *<mykeyvault>* no exemplo a seguir com seu próprio nome exclusivo do Key Vault:

```azurecli
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Gerar certificado e armazenar no Cofre da Chave
Para uso em produção, você deve importar um certificado válido assinado por um fornecedor confiável com o [az keyvault certificate import](/cli/azure/certificate#import). Para este tutorial, o exemplo a seguir mostra como você pode gerar um certificado autoassinado com [criar certificado de keyvault az](/cli/azure/certificate#create) que usa a política de certificado padrão:

```azurecli
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparar o certificado para uso com a VM
Para usar o certificado durante o processo de criação de VM, obtenha a identificação do seu certificado com as [ versões secretas de az keyvault](/cli/azure/keyvault/secret#list-versions). Converter o certificado com [az vm format-secret](/cli/azure/vm#format-secret). O exemplo a seguir atribui a saída desses comandos variáveis de facilidade de uso nas próximas etapas:

```azurecli
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Criar a configuração de inicialização de nuvem para proteger o NGINX
Quando você cria uma VM, certificados e chaves são armazenados no diretório protegido */var/lib/waagent/*. Para automatizar adicionando o certificado para a máquina virtual e configurando o NGINX, você pode expandir a configuração de inicialização de nuvem do exemplo anterior.

Crie um arquivo chamado *cloud-init-secured.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Criar VM segura
Agora, crie uma VM com [az vm create](/cli/azure/vm#create). Os dados do certificado são injetados no cofre da chave com o `--secrets` parâmetro. Como no exemplo anterior, você também passa a configuração de inicialização de nuvem com o `--custom-data` parâmetro:

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos para que a VM seja criada, os pacotes para instalar e iniciar o aplicativo. Quando a VM tiver sido criada, observe o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o aplicativo do Node. js por meio de um navegador da web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 443 da Internet com [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Testar o aplicativo da Web protegido
Agora, abra um navegador da Web e digite *https://<publicIpAddress>* na barra de endereços. Forneça seu próprio endereço de IP público do processo de criação da máquina virtual. Se você usou um certificado autoassinado, aceite o aviso de segurança:

![Aceite o aviso de segurança do navegador da web](./media/tutorial-automate-vm-deployment/browser-warning.png)

Seu site NGINX e Node. js seguro é exibido como no exemplo a seguir:

![Exibir o site NGINX em execução](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como personalizar uma VM na primeira inicialização. Vá para o próximo tutorial para aprender a gerenciar imagens de VM.

[Criar imagens de VM personalizada](./tutorial-custom-images.md)

