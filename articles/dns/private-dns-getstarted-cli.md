---
title: Criar uma zona privada do DNS do Azure usando a CLI do Azure
description: Neste tutorial, você cria e testa uma zona e um registro DNS privados no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro de DNS privado usando a CLI do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/25/2018
ms.author: victorh
ms.openlocfilehash: 5559e2fc9b9cce95bd7d5d02a64d134e5eaa03be
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100598"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Criar uma zona privada do DNS do Azure usando a CLI do Azure

Este tutorial explica as etapas para criar sua primeira zona DNS privada e registrar usando a CLI do Azure.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Elas são chamadas de *redes virtuais de resolução*. Também é possível especificar uma rede virtual para a qual o DNS do Azure mantenha registros de nome do host sempre que uma VM for criada, mudar de IP ou for excluída.  Ela é chamada de *rede virtual de registro*.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma zona de DNS privado
> * Criar máquinas virtuais de teste
> * Criar um registro DNS adicional
> * Testar a zona privada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, você pode concluir este tutorial usando o [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para conter a zona DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Criar uma zona de DNS privado

Uma zona DNS é criada usando o comando `az network dns zone create` com um valor de *Privado* para o parâmetro **ZoneType**. O exemplo a seguir cria uma zona DNS chamada **contoso.local** no grupo de recursos chamado **MyAzureResourceGroup** e a disponibiliza na rede virtual chamada **MyAzureVnet** .

Observe que, se o parâmetro **ZoneType** for omitido, a zona será criada como uma zona pública e, portanto, ele é necessário para criar uma zona privada.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n contoso.local \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Se você quisesse criar uma zona apenas para a resolução de nome (sem criação automática de nome de host), poderia usar o parâmetro *resolution-vnets* em vez do parâmetro *registration-vnets*.

> [!NOTE]
> Você não poderá ver os registros de nome de host criados automaticamente. Mas, posteriormente, você irá testá-los para verificar se eles existem.

### <a name="list-dns-private-zones"></a>Listar zonas de DNS privado

Para enumerar zonas DNS, use `az network dns zone list`. Para obter ajuda, consulte `az network dns zone list --help`.

Especificar o grupo de recursos lista apenas as zonas dentro do grupo de recursos:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Omitir o grupo de recursos lista todas as zonas na assinatura:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, crie duas máquinas virtuais para que você possa testar a zona DNS privada:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Isso levará alguns minutos para ser concluído.

## <a name="create-an-additional-dns-record"></a>Criar um registro DNS adicional

Para criar um registro DNS, use o comando `az network dns record-set [record type] add-record`. Para obter ajuda para adicionar, por exemplo, os registros A, veja `azure network dns record-set A add-record --help`.

 O exemplo a seguir cria um registro com o nome relativo **db** na Zona DNS **contoso.local** no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registros é **db.contoso.local**. O tipo de registro é “A”, com o endereço IP “10.2.0.4”.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z contoso.local \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Exibir registros DNS

Para listar os registros DNS em sua zona, execute:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z contoso.local
```
Lembre-se de que você não verá registros A criados automaticamente para as duas máquinas virtuais de teste.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora você pode testar a resolução de nome para a zona privada **contoso.local**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar VMs para permitir ICMP de entrada

Você pode usar o comando ping para testar a resolução de nome. Portanto, configure o firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Conecte-se a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o comando a seguir:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Executar ping de VMs por nome

1. No prompt de comando do Windows PowerShell myVM02, execute ping em myVM01 usando o nome de host registrado automaticamente:
   ```
   ping myVM01.contoso.local
   ```
   Você deve ver uma saída semelhante a esta:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Execute ping no nome **db** que você criou anteriormente:
   ```
   ping db.contoso.local
   ```
   Você deve ver uma saída semelhante a esta:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Excluir todos os recursos

Quando não for mais necessário, exclua o grupo de recursos **MyAzureResourceGroup** para excluir os recursos criados neste tutorial.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou uma zona DNS privada, criou um registro DNS e testou a zona.
Agora você pode aprender mais sobre zonas DNS privadas.

> [!div class="nextstepaction"]
> [Usando o DNS do Azure para domínios privados](private-dns-overview.md)
