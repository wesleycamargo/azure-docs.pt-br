---
title: "Tutorial - Compilar um aplicativo altamente disponível em VMs do Azure | Microsoft Docs"
description: "Saiba como criar um aplicativo altamente disponível e seguro em três VMs Linux com um balanceador de carga no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 65ff0e03af6b43478f645274adfeb46d28418894
ms.lasthandoff: 04/07/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-linux-virtual-machines-in-azure"></a>Criar um aplicativo altamente disponível com balanceamento de carga em máquinas virtuais Linux no Azure
Neste tutorial, você criará um aplicativo altamente disponível e resistente a eventos de manutenção. O aplicativo usa um balanceador de carga, um conjunto de disponibilidade e três VMs (máquinas virtuais) Linux. Este tutorial compila um aplicativo do Node.js, mas você pode usá-lo para implantar uma estrutura de aplicativo diferente usando os mesmos componentes e diretrizes de alta disponibilidade.

## <a name="step-1---azure-prerequisites"></a>Etapa 1: pré-requisitos do Azure
Para concluir este tutorial, abra uma janela de terminal e certifique-se de que a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-azure-cli) esteja instalada. Se você ainda não estiver conectado à sua assinatura do Azure, faça logon com [az login](/cli/azure/#login) e siga as instruções na tela:

```azurecli
az login
```

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Antes de criar os outros recursos do Azure, será necessário criar um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>Etapa 2: criar conjunto de disponibilidade
As máquinas virtuais podem ser criadas em domínios lógicos de falhas e de atualização. Cada domínio lógico representa uma parte do hardware do datacenter subjacente do Azure. Quando você cria duas ou mais VMs, seus recursos de computação e armazenamento são distribuídos entre esses domínios. Essa distribuição mantém a disponibilidade de seu aplicativo, caso um componente de hardware passe por manutenção. Os conjuntos de disponibilidade permitem que você defina esses domínios lógicos de falha e de atualização.

Crie um conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set#create). O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>Etapa 3: criar um balanceador de carga
Um Azure Load Balancer distribui o tráfego entre um conjunto de VMs definidas usando regras de balanceador de carga. Uma investigação de integridade monitora uma determinada porta em cada VM e distribui o tráfego somente para uma VM operacional.

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar seu aplicativo na Internet, atribua um endereço IP público para o balanceador de carga. Crie um endereço IP público com [az network public-ip create](/cli/azure/public-ip#create). O exemplo a seguir cria um endereço IP público chamado `myPublicIP`:

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um balanceador de carga com [az network lb create](/cli/azure/network/lb#create). O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer` usando o endereço `myPublicIP`:

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Crie uma investigação de integridade com base em um protocolo ou página de verificação de integridade específica ao seu aplicativo. O exemplo a seguir cria uma investigação TCP, embora você possa ampliar esse exemplo e adicionar `--path healthcheck.js`, por exemplo. O `healthcheck.js` deve ser criado e retornar a reposta **HTTP 200 OK** para o balanceador de carga a fim de manter o host em rotação.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#create). O exemplo a seguir cria uma investigação de integridade chamada `myHealthProbe` que monitora cada VM:

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs.

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#create). O exemplo a seguir cria uma regra chamada `myLoadBalancerRule`, usa a investigação de integridade `myHealthProbe` e equilibra o tráfego na porta `80`:

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>Etapa 4: configurar a rede
Cada VM tem uma ou mais NICs (placas de interface de rede virtual) que se conecta a uma rede virtual. Essa rede virtual é protegida para filtrar o tráfego com base em regras de acesso definidos.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Para fornecer conectividade de rede às suas VMs, crie uma rede virtual com [az network vnet create](/cli/azure/vnet#create). O exemplo a seguir cria uma rede virtual chamada `myVnet` com uma sub-rede chamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>Configurar a segurança de rede
Os grupos de segurança de rede usam regras para controlar o fluxo de tráfego nas VMs. Você define essas regras, como a permissão do tráfego na porta 80. Enquanto o balanceador de carga distribui o tráfego entre as VMs, as regras de grupo de segurança de rede garantem a permissão apenas do tráfego permitido.

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Para permitir que o tráfego da Web acesse seu aplicativo, crie uma regra de grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule#create). O exemplo a seguir cria uma regra de grupo de segurança de rede chamada `myNetworkSecurityGroupRule`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>Criar placas de interface de rede virtual 
Os balanceadores de carga funcionam com o recurso NIC virtual, em vez da VM real. A NIC virtual é conectada ao balanceador de carga e depois anexada a uma VM.

Crie a NIC virtual com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir):

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>Etapa 5: compilar seu aplicativo
**cloud_init** é uma abordagem amplamente usada para personalizar uma VM. Você pode usar **inicialização na nuvem**para instalar pacotes e gravar arquivos. Como **cloud-init** é executado durante a implantação inicial, não há etapas adicionais para executar o aplicativo. O balanceador de carga começa a distribuir o tráfego depois que a VM termina a implantação e a execução do aplicativo. Para saber mais sobre como usar **cloud_init**, consulte [Usar cloud-init para personalizar uma VM Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A configuração de **cloud_init** a seguir instala **nodejs** e **npm**, depois instala e configura o **nginx** como um proxy da Web para seu aplicativo. A configuração também cria um aplicativo 'Hello, World' simples do Node. js, em seguida inicializa e inicia o aplicativo com **Express**. Se você quiser usar uma estrutura de aplicativo diferente, ajuste os pacotes e o aplicativo implantado adequadamente.

Crie um arquivo chamado `cloud-init.txt` e cole a seguinte configuração:

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
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>Etapa 6: criar máquinas virtuais
Com todos os componentes subjacentes aplicados, agora você pode criar VMs altamente disponíveis para executar seu aplicativo.

### <a name="create-vms"></a>Criar VMs
Crie as VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria três VMs e gera chaves SSH, se elas ainda não existirem:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Demora alguns minutos para criar e configurar todas as três VMs. A investigação de integridade do balanceador de carga detecta automaticamente quando o aplicativo está em execução em cada VM. Quando o aplicativo estiver em execução, a regra do balanceador de carga começará a distribuir o tráfego.

### <a name="test-your-app"></a>Testar seu aplicativo
Obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip#show). O exemplo a seguir obtém o endereço IP para `myPublicIP` criado anteriormente:

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Insira o endereço IP público em um navegador da Web. O aplicativo é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego:

![Executar o aplicativo Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Force a atualização de seu navegador da Web para ver o balanceador de carga distribuir tráfego entre todas as três VMs que executam seu aplicativo.


## <a name="step-7---management-tasks"></a>Etapa 7: tarefas de gerenciamento
Talvez seja necessário fazer a manutenção nas VMs que executam seu aplicativo, como a instalação de atualizações do sistema operacional. Para lidar com o aumento de tráfego em seu aplicativo, talvez seja necessário adicionar outras VMs. Esta seção mostra como remover ou adicionar uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Remova uma VM do pool de endereços de back-end com [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove). O exemplo a seguir remove a NIC virtual para **myVM2** de `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Force a atualização de seu navegador da Web para ver o balanceador de carga distribuir tráfego entre as duas VMs restantes que executam seu aplicativo. Agora você pode executar a manutenção na VM, como instalação de atualizações do sistema operacional ou execução de uma reinicialização da VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Após executar a manutenção da VM, ou se você precisar expandir a capacidade, adicione uma VM ao pool de endereços de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add). O exemplo a seguir adiciona a NIC virtual para **myVM2** ao `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Próximas etapas
Este tutorial cria uma infraestrutura de aplicativos altamente disponíveis usando os recursos individuais do Azure. Você também pode usar os Conjuntos de Dimensionamento de Máquinas Virtuais para escalar e reduzir verticalmente automaticamente o número de VMs que executam o aplicativo. Continue com o próximo tutorial – [Criar um aplicativo altamente disponível no Linux com Conjuntos de Dimensionamento de Máquinas Virtuais](tutorial-convert-to-vmss.md).

Para saber mais sobre alguns dos recursos de alta disponibilidade apresentados neste tutorial, consulte as seguintes informações:

- [Gerenciar a disponibilidade de máquinas virtuais do Linux](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Visão geral do Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controlar o fluxo de tráfego de rede com grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md)
- [Scripts de exemplo da CLI do Azure](../windows/cli-samples.md)
