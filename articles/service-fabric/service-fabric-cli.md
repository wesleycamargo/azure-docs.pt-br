---
title: "Introdução à CLI do Azure Service Fabric"
description: Saiba como usar a CLI do Azure Service Fabric. Saiba como se conectar a um cluster e como gerenciar aplicativos.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 851f04c8b5eee762ec43060f02c8b83f00c1782e
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="azure-service-fabric-cli"></a>CLI do Azure Service Fabric

A interface de linha de comando do Azure Service Fabric (CLI) é um utilitário de linha de comando para interagir com e gerenciar entidades do Service Fabric. A CLI do Service Fabric pode ser usada com clusters do Windows ou Linux. A CLI do Service Fabric é executada em qualquer plataforma onde Python tem suporte.

## <a name="prerequisites"></a>Pré-requisitos

Antes da instalação, verifique se seu ambiente tem o Python e o pip instalados. Para saber mais, veja a [documentação de início rápido do pip](https://pip.pypa.io/en/latest/quickstart/) e a [documentação de instalação do Python](https://wiki.python.org/moin/BeginnersGuide/Download) oficial.

Embora haja suporte para Python 2.7 e 3.6, recomendamos que você use Python 3.6. A seção a seguir mostra como instalar todos os pré-requisitos e a CLI.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instale o pip, Python e a CLI do Service Fabric

 Há várias maneiras de instalar o pip e o Python em sua plataforma. Aqui estão algumas etapas para obter os principais sistemas operacionais configurar rapidamente com Python 3.6 e pip.

### <a name="windows"></a>Windows

Para o Windows 10, o Windows Server 2016 e o Windows Server 2012 R2, use as instruções de instalação oficial padrão. O instalador do Python também instala o pip por padrão.

1. Vá até a [página oficial de downloads do Python](https://www.python.org/downloads/) e baixe a versão mais recente do Python 3.6.

2. Inicie o instalador.

3. Na parte inferior do aviso, selecione **Adicionar Python 3.6 a PATH**.

4. Selecione **Instalar Agora** e conclua a instalação.

Agora você deve poder abrir uma nova janela de comando e obter a versão do Python e do pip.

```bat
python --version
pip --version
```

Em seguida, execute o comando a seguir para instalar a CLI do Service Fabric:

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

Para a área de trabalho do Ubuntu 16.04, você pode instalar o Python 3.6 usando um arquivo de pacote pessoal de terceiros (PPA).

No terminal, execute os seguintes comandos:

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

Em seguida, para instalar a CLI do Service Fabric apenas na instalação do Python 3.6, execute o seguinte comando:

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Estas etapas não afetam a instalação do sistema do Python 3.5 e 2.7. Não tente modificar essas instalações se não estiver familiarizado com o Ubuntu.

### <a name="macos"></a>MacOS

Para MacOS, recomendamos que você use o [Gerenciador de pacotes do HomeBrew](https://brew.sh). Se o HomeBrew ainda não estiver instalado, instale-o ao executar o seguinte comando:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

No terminal, instale 3.6 Python, pip e a CLI do Service Fabric executando os seguintes comandos:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

Estas etapas não modificam a instalação do sistema do Python 2.7.

## <a name="cli-syntax"></a>Sintaxe da CLI

Os comandos sempre são prefixados com `sfctl`. Para obter informações gerais sobre todos os comandos que você pode usar, utilize `sfctl -h`. Para obter ajuda com um único comando, use `sfctl <command> -h`.

Os comandos seguem uma estrutura repetida, com o destino do comando antes do verbo ou da ação.

```azurecli
sfctl <object> <action>
```

Neste exemplo, `<object>` é o destino para `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Por exemplo, para selecionar e conectar o cluster com o nome `testcluster.com`, execute o seguinte comando:

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser antecedido por `http` ou `https`. Ele deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para clusters protegidos com um certificado, você pode especificar um certificado PEM codificado. O certificado pode ser especificado como um único arquivo ou como um par de certificado e chave.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para saber mais, confira [Conectar-se a um cluster seguro do Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operações básicas

As informações de conexão do cluster persistem por várias sessões da CLI do Service Fabric. Depois de selecionar um cluster do Service Fabric, você poderá executar todos os comandos do Service Fabric no cluster.

Por exemplo, para obter o estado de integridade do cluster do Service Fabric, use o seguinte comando:

```azurecli
sfctl cluster health
```

O comando resulta na saída a seguir:

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

Veja algumas sugestões e dicas para resolver problemas comuns.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado de PFX para PEM

A CLI do Service Fabric dá suporte aos certificados do cliente como arquivos PEM (extensão .pem). Se você usa arquivos PFX do Windows, deve converter esses certificados em formato PEM. Para converter um arquivo PFX em PEM, use o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para saber mais, confira a [documentação OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problemas de conexão

Algumas operações podem gerar a seguinte mensagem:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto de extremidade do cluster especificado está disponível e está ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Para atualizar o ponto de extremidade, use `sfctl cluster select`.

### <a name="detailed-logs"></a>Logs detalhados

Os logs detalhados costumam ser úteis para depurar ou relatar um problema. Um sinalizador `--debug` global que aumenta o detalhamento dos arquivos de log.

### <a name="command-help-and-syntax"></a>Sintaxe e ajuda de comando

Para obter ajuda com um comando ou um grupo de comandos específico, use o sinalizador `-h`.

```azurecli
sfctl application -h
```

Veja outro exemplo:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo com a CLI do Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)

