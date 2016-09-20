<properties
   pageTitle="Abrir portas para uma VM Linux | Microsoft Azure"
   description="Saiba como abrir uma porta/criar um ponto de extremidade à sua VM Linux usando o modelo de implantação do Azure Resource Manager e a CLI do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# Como abrir portas para uma VM Linux no Azure
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou interface de rede de VM. Coloque os filtros, que controlam o tráfego de entrada e saída, em um Grupo de Segurança de Rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da Web na porta 80.

## Comandos rápidos
Para criar regras e um Grupo de Segurança de Rede, você precisa da [CLI do Azure](../xplat-cli-install.md) no modo Resource Manager (`azure config mode arm`).

Crie o Grupo de Segurança de Rede da seguinte forma, inserindo seus próprios nomes e localização adequadamente:

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Adicione uma regra para permitir o tráfego HTTP para seu servidor Web (ou ajuste para seu próprio cenário, como conectividade de banco de dados ou acesso SSH):

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Associe o Grupo de Segurança de Rede à interface de rede de sua VM:

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Você também pode associar o Grupo de Segurança de Rede à sub-rede de uma rede virtual, e não apenas à adaptador de rede em uma única VM:

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

- [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)
- [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
- [Visão geral do Azure Resource Manager para balanceadores de carga](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->