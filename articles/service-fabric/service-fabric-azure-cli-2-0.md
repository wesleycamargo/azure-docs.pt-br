---
title: "Introdução ao Service Fabric e à CLI do Azure 2.0"
description: "Como usar o módulo de comandos do Service Fabric na CLI do Azure, versão 2.0, inclui a conexão ao cluster e o gerenciamento de aplicativos"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---
# Service Fabric e CLI do Azure 2.0
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

A nova CLI do Azure 2.0 agora inclui comandos para gerenciar clusters do Service Fabric. Esta documentação inclui etapas de introdução à CLI do Azure.

## Instalar a CLI 2.0 do Azure
<a id="install-azure-cli-20" class="xliff"></a>

A CLI do Azure agora inclui comandos para interagir e gerenciar clusters do Service Fabric. Você pode seguir o [processo de instalação padrão](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) para obter a CLI do Azure mais recente.

Para saber mais, confira a [documentação da CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

## Sintaxe da CLI
<a id="cli-syntax" class="xliff"></a>

Todos os comandos do Azure Service Fabric são prefixados com `az sf` na CLI do Azure. Para saber mais sobre os comandos disponíveis, você pode executar `az sf -h` para obter informações gerais. Ou pode executar `az sf <command> -h` para obter ajuda detalhada sobre um único comando.

Os comandos do Azure Service Fabric na CLI seguem um padrão de nomenclatura

```azurecli
az sf <object> <action>
```

Aqui, `<object>` é o destino de `<action>`.

## Selecionando um cluster
<a id="selecting-a-cluster" class="xliff"></a>

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Por exemplo, consulte o trecho de código a seguir para se conectar a um cluster não seguro.

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser prefixado por `http` ou `https`e deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para proteger clusters com um certificado, arquivos `pem` descriptografados ou `crt` e `key` têm suporte.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para saber mais, confira o [documento detalhado sobre como se conectar a clusters seguros](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> O comando select não executa nenhuma solicitação antes de retornar. Para verificar se um cluster foi especificado corretamente, execute um comando, por exemplo, `az sf cluster health` e verifique se o comando retorna sem erros.

## Executando operações básicas
<a id="performing-basic-operations" class="xliff"></a>

As informações de conexão do cluster são persistidas entre diferentes sessões da CLI do Azure. Quando um cluster do Service Fabric é selecionado, você pode executar qualquer comando do Service Fabric.

Por exemplo, para obter o estado de integridade do cluster do Service Fabric, execute o comando a seguir

```azurecli
az sf cluster health
```

O comando resulta na saída a seguir, supondo que a saída JSON foi especificada na configuração da CLI do Azure

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

## Dicas e Perguntas frequentes
<a id="tips-and-faq" class="xliff"></a>

Aqui estão algumas informações que podem ser úteis quando há problemas no uso dos comandos do Service Fabric na CLI do Azure

### Convertendo um certificado de PFX para PEM
<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

A CLI do Azure dá suporte a certificados de cliente como arquivos PEM (extensão `.pem`). Se estiver usando arquivos PFX do Windows, esses certificados precisarão ser convertidos no formato PEM. Para converter um arquivo PFX em PEM, use o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Consulte [documentação OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) para ver os detalhes.

### Problemas de conexão
<a id="connection-issues" class="xliff"></a>

Ao executar operações, você poderá encontrar o seguinte erro:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Nesse caso, verifique se o ponto de extremidade do cluster especificado está acessível e ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Use `az sf cluster select` para atualizar o ponto de extremidade.

### Obtendo logs detalhados
<a id="getting-detailed-logs" class="xliff"></a>

Quando depurar ou relatar um problema, será útil incluir logs detalhados. A CLI do Azure inclui um sinalizador `--debug` global que aumenta o nível de detalhes dos logs.

### Sintaxe e ajuda de comando
<a id="command-help-and-syntax" class="xliff"></a>

Os comandos do Service Fabric seguem a mesma convenção da CLI do Azure. Especifique o sinalizador `-h` para obter ajuda sobre um comando ou grupo de comandos específico. Por exemplo:

```azurecli
az sf application -h
```

ou o

```azurecli
az sf application create -h
```

