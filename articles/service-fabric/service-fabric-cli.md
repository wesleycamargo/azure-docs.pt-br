---
title: "Introdução à CLI do Azure Service Fabric (sfctl)"
description: Saiba como usar a CLI do Azure Service Fabric. Saiba como se conectar a um cluster e como gerenciar aplicativos.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 2faca2887f25b45d833dea7b2259277466290670
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---
# <a name="azure-service-fabric-command-line"></a>Linha de comando do Azure Service Fabric

A CLI do Azure Service Fabric (sfctl) é um utilitário da linha de comando para interagir e gerenciar as entidades do Azure Service Fabric. O sfctl pode ser usado com clusters do Windows ou Linux. O sfctl é executado em qualquer plataforma onde o python é suportado.

## <a name="prerequisites"></a>Pré-requisitos

Antes da instalação, verifique se seu ambiente tem o python e o pip instalados. Para obter mais informações, veja a [documentação de início rápido do pip](https://pip.pypa.io/en/latest/quickstart/) e a [documentação de instalação do python](https://wiki.python.org/moin/BeginnersGuide/Download) oficial.

Embora haja suporte para os pythons 2.7 e 3.6, é recomendável usar o python 3.6. A seção a seguir mostra como instalar todos os pré-requisitos e a CLI.

## <a name="install-pip-python-and-sfctl"></a>Instalar pip, Python e sfctl

Embora haja várias maneiras de instalar pip e Python em sua plataforma, aqui estão algumas etapas para configurar rapidamente o Python 3.6 e o pip nos principais sistemas operacionais:

### <a name="windows"></a>Windows

Para Windows 10, Server 2016 e Server 2012R2, você pode usar as instruções de instalação padrão oficiais. O instalador do Python também instala o pip por padrão.

- Navegue até a [página oficial de downloads do Python](https://www.python.org/downloads/) e baixe a versão mais recente do Python 3.6
- Iniciar o instalador
- Selecione a opção na parte inferior do prompt para `Add Python 3.6 to PATH`
- Selecione `Install Now`
- Concluir a instalação

Agora você deve poder abrir uma nova janela de comando e obter a versão do Python e do pip:

```bat
python --version
pip --version
```

Em seguida, execute isto para instalar a CLI do Service Fabric

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

Para o Ubuntu 16.04 Desktop, você pode instalar o Python 3.6 usando um PPA de terceiros:

No terminal, execute os seguintes comandos:

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

Em seguida, para instalar o sfctl apenas na instalação do Python 3.6, execute o seguinte comando:

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Essas etapas não afetam as versões de Python 3.5 e 2.7 instaladas no sistema. Não tente modificar essas instalações se não estiver familiarizado com Ubuntu.

### <a name="macos"></a>MacOS

Para MacOS, é recomendável usar o [Gerenciador de pacotes HomeBrew](https://brew.sh). Instale o HomeBrew, se já não estiver instalado, executando o seguinte comando:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Em seguida, no terminal, instale o Python 3.6, o pip e o sfctl

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

Essas etapas não modificam a instalação do sistema do Python 2.7.

## <a name="cli-syntax"></a>Sintaxe da CLI

Os comandos são prefixados sempre `sfctl`. Para obter informações gerais sobre todos os comandos que você pode usar, use `sfctl -h`. Para obter ajuda com um único comando, use `sfctl <command> -h`.

Os comandos seguem uma estrutura repetida, com o destino do comando antes do verbo ou da ação:

```azurecli
sfctl <object> <action>
```

Neste exemplo, `<object>` é o destino para `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Por exemplo, execute o seguinte para selecionar e conectar o cluster com o nome `testcluster.com`.

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser antecedido por `http` ou `https`. Ele deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para clusters protegidos com um certificado, você pode especificar um certificado PEM codificado. O certificado pode ser especificado como um único arquivo ou um par de certificado e chave.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para saber mais, confira [Conectar-se a um cluster seguro do Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operações básicas

As informações de conexão do cluster persistem em várias sessões do sfctl. Depois de selecionar um cluster do Service Fabric, você poderá executar todos os comandos do Service Fabric no cluster.

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

Algumas sugestões e dicas para resolver problemas comuns.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado de PFX para PEM

A CLI do Service Fabric dá suporte aos certificados do cliente como arquivos PEM (extensão .pem). Se você usa arquivos PFX do Windows, deve converter esses certificados em formato PEM. Para converter um arquivo PFX em PEM, use o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para saber mais, confira a [documentação OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de conexão

Algumas operações podem gerar a seguinte mensagem:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto de extremidade do cluster especificado está disponível e está ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Para atualizar o ponto de extremidade, use `sfctl cluster select`.

### <a name="detailed-logs"></a>Logs detalhados

Os logs detalhados costumam ser úteis para depurar ou relatar um problema. Existe um sinalizador `--debug` global que aumenta o detalhamento dos arquivos de log.

### <a name="command-help-and-syntax"></a>Sintaxe e ajuda de comando

Para obter ajuda com um comando ou um grupo de comandos específico, use o sinalizador `-h`:

```azurecli
sfctl application -h
```

Outro exemplo:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo com a CLI do Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)

