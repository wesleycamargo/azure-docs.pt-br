---
title: "Introdução ao Azure Service Fabric e à CLI do Azure 2.0"
description: "Saiba como usar o módulo de comandos do Azure Service Fabric na CLI do Azure, versão 2.0. Saiba como se conectar a um cluster e como gerenciar aplicativos."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Service Fabric e CLI do Azure 2.0

A Azure CLI (ferramenta de linha de comando) versão 2.0 inclui comandos para ajudá-lo a gerenciar clusters do Azure Service Fabric. Aprenda a usar a CLI do Azure e o Azure Service Fabric.

## <a name="install-azure-cli-20"></a>Instalar a CLI 2.0 do Azure

Você pode usar comandos da CLI do Azure 2.0 para gerenciar e interagir com clusters do Service Fabric. Para obter a versão mais recente da CLI do Azure, siga o [processo de instalação padrão da CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Para saber mais, confira a [Visão geral da CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Sintaxe da CLI do Azure

Na CLI do Azure, todos os comandos do Service Fabric são prefixados com `az sf`. Para obter informações gerais sobre os comandos que você pode usar, use `az sf -h`. Para obter ajuda com um único comando, use `az sf <command> -h`.

Os comandos do Service Fabric na CLI do Azure seguem este padrão de nomenclatura:

```azurecli
az sf <object> <action>
```

Aqui, `<object>` é o destino de `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Para obter um exemplo, veja o código a seguir. O código se conecta a um cluster não seguro.

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser antecedido por `http` ou `https`. Ele deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para clusters protegidos por um certificado, você pode usar arquivos .pem não criptografados ou arquivos .crt e .key. Por exemplo:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para saber mais, confira [Conectar-se a um cluster seguro do Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> O comando `select` não agir em nenhuma solicitação antes de retornar. Para verificar se você especificou um cluster corretamente, use um comando como `az sf cluster health`. Verifique se o comando não retorna erros.

## <a name="basic-operations"></a>Operações básicas

As informações de conexão do cluster persistem por várias sessões da CLI do Azure. Depois de selecionar um cluster do Service Fabric, você poderá executar todos os comandos do Service Fabric no cluster.

Por exemplo, para obter o estado de integridade do cluster do Service Fabric, use o seguinte comando:

```azurecli
az sf cluster health
```

O comando resulta na saída abaixo (supondo que a saída JSON foi especificada na configuração da CLI do Azure):

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

As informações a seguir poderão ser úteis se você tiver problemas ao usar comandos do Service Fabric na CLI do Azure.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado de PFX para PEM

A CLI do Azure dá suporte a certificados de cliente como arquivos PEM (extensão .pem). Se você usa arquivos PFX do Windows, deve converter esses certificados em formato PEM. Para converter um arquivo PFX em PEM, use o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para saber mais, confira a [documentação OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de conexão

Algumas operações podem gerar a seguinte mensagem:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto de extremidade do cluster especificado está disponível e está ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Para atualizar o ponto de extremidade, use `az sf cluster select`.

### <a name="detailed-logs"></a>Logs detalhados

Os logs detalhados costumam ser úteis para depurar ou relatar um problema. A CLI do Azure oferece um sinalizador global `--debug` que aumenta o nível de detalhes dos arquivos de log.

### <a name="command-help-and-syntax"></a>Sintaxe e ajuda de comando

Os comandos do Service Fabric seguem a mesma convenção da CLI do Azure. Para obter ajuda com um comando ou um grupo de comandos específico, use o sinalizador `-h`:

```azurecli
az sf application -h
```

Veja outro exemplo:

```azurecli
az sf application create -h
```

