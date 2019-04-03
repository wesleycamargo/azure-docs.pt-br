---
title: Linguagens compatíveis para a Máquina Virtual de Ciência de Dados
titleSuffix: Azure
description: Saiba mais sobre as linguagens de programação e ferramentas relacionadas que estão instaladas previamente na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 586f37ff972a6102da351794365f719a185857fc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877407"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Linguagens com suporte na Máquina Virtual de Ciência de Dados 

A DSVM (Máquina Virtual de Ciência de Dados) vem com várias linguagens e ferramentas de desenvolvimento pré-criadas para criar seus aplicativos de AI. Aqui estão algumas das maior destaque. 

## <a name="python-windows-server-2016-edition"></a>Python (edição do Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Versões de linguagem com suporte | 2.7 e 3.6 |
| Edições do DSVM com suporte      | Windows Server 2016     |
| Como é configurado/instalado no DSVM?  | Dois ambientes `conda` globais são criados. <br /> O ambiente * `root` localizado em `/anaconda/` é Python 3.6. <br/> O ambiente * `python2` localizado em `/anaconda/envs/python2` é Python 2.7       |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter para Python estão incluídos     |
| Ferramentas relacionadas ao DSVM      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 criado antes de março de 2018 contém Python 3.5 e Python 2.7. Além disso, Python 2.7 é o ambiente **raiz** do Conda e **py35** é o ambiente do Python 3.5. 

### <a name="how-to-use--run-it"></a>Como usar/executar?    

* Em execução no prompt de comando

Abra o prompt de comando e faça o seguinte, dependendo da versão do Python que você deseja executar. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Como usar em um IDE

Use as Ferramentas Python para Visual Studio (PTVS) instalado no Visual Studio Community Edition. A única configuração do ambiente automaticamente no PTVS por padrão é Python 3.6. 

> [!NOTE]
> Para apontar o PTVS para Python 2.7, você precisa criar um ambiente personalizado no PTVS. Para definir esses caminhos de ambiente no Visual Studio Community Edition, navegue até **Ferramentas** -> **Ferramentas do Python** -> **Ambientes do Python** e clique em **+ Personalizado**. Em seguida, defina o local como `c:\anaconda\envs\python2` e clique em _Detecção Automática_. 

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como _Python [Conda Root]_ para Python 3.6 e _Python [Conda env:python2]_ para o ambiente Python 2.7. 

* Instalando pacotes Python

Os ambientes Python padrão no DSVM são ambientes globais legíveis por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, ative para a raiz ou ambiente python2 usando o comando `activate` como Administrador. Em seguida, você pode usar o gerenciador de pacotes como `conda` ou `pip` para instalar ou atualizar pacotes. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux e edição do Windows Server 2012)

|    |           |
| ------------- | ------------- |
| Versões de linguagem com suporte | 2.7 e 3.5 |
| Edições do DSVM com suporte      | Linux, Windows Server 2012    |
| Como é configurado/instalado no DSVM?  | Dois ambientes `conda` globais são criados. <br /> O ambiente * `root` localizado em `/anaconda/` é Python 2.7. <br/> O ambiente * `py35` localizado em `/anaconda/envs/py35` é Python 3.5       |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter para Python estão incluídos     |
| Ferramentas relacionadas ao DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Como usar/executar?    

**Linux**
* Executando no terminal

Abra o terminal e faça o seguinte dependendo da versão do Python que você deseja executar. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Como usar em um IDE

Use PyCharm instalado no Visual Studio Community Edition. 

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como _Python [Conda Root]_ para Python 2.7 e _Python [Conda env:py35]_ para o ambiente Python 3.5. 

* Instalando pacotes Python

Os ambientes Python padrão no DSVM são ambientes globais legíveis por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, ative para a raiz ou ambiente py35 usando o comando `source activate` como Administrador ou usuário com permissão sudo. Em seguida, você pode usar um gerenciador de pacotes como `conda` ou `pip` para instalar ou atualizar pacotes. 

**Windows 2012**
* Em execução no prompt de comando

Abra o prompt de comando e faça o seguinte, dependendo da versão do Python que você deseja executar. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Como usar em um IDE

Use as Ferramentas Python para Visual Studio (PTVS) instalado no Visual Studio Community Edition. A única configuração do ambiente automaticamente no PTVS em Python 2.7. 
> [!NOTE]
> Para apontar o PTVS para Python 3.5, você precisa criar um ambiente personalizado no PTVS. Para definir esses caminhos de ambiente no Visual Studio Community Edition, navegue até **Ferramentas** -> **Ferramentas do Python** -> **Ambientes do Python** e clique em **+ Personalizado**. Em seguida, defina o local como `c:\anaconda\envs\py35` e clique em _Detecção Automática_. 

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como _Python [Conda Root]_ para Python 2.7 e _Python [Conda env:py35]_ para o ambiente Python 3.5. 

* Instalando pacotes Python

Os ambientes Python padrão no DSVM são ambientes globais legíveis por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, ative para a raiz ou ambiente py35 usando o comando `activate` como Administrador. Em seguida, você pode usar o gerenciador de pacotes como `conda` ou `pip` para instalar ou atualizar pacotes. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões de linguagem com suporte | Microsoft R Open 3.x (100% compatível com CRAN-R<br /> Microsoft R Server 9.x Developer Edition (uma plataforma R escalonável pronta para empresas)|
| Edições do DSVM com suporte      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter para R estão incluídos     |
| Ferramentas relacionadas ao DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Como usar/executar?    

**Windows**:

* Em execução no prompt de comando

Abra o prompt de comando e simplesmente digite `R`.

* Como usar em um IDE

Use RTVS (RTools para Visual Studio) instalado no Visual Studio Community Edition ou RStudio. Estão disponíveis no menu Iniciar ou como um ícone na área de trabalho. 

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como _R_ para usar o kernel Jupyter R (IRKernel). 

* Instalando pacotes R

R é instalado em DSVM em um ambiente global legível por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, execute R usando um dos métodos acima. Em seguida, você pode executar o gerenciador de pacotes R `install.packages()` para instalar ou atualizar pacotes. 

**Linux**:

* Executando no terminal

Abra terminal e simplesmente execute `R`.  

* Como usar em um IDE

Use o RStudio instalado no DSVM Linux.  

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como _R_ para usar o kernel Jupyter R (IRKernel). 

* Instalando pacotes R

R é instalado em DSVM em um ambiente global legível por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, execute R usando um dos métodos acima. Em seguida, você pode executar o gerenciador de pacotes R `install.packages()` para instalar ou atualizar pacotes. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versões de linguagem com suporte | 0.6 |
| Edições do DSVM com suporte      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: Instalado em `C:\JuliaPro-VERSION`<br /> Linux: Instalado em `/opt/JuliaPro-VERSION`    |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter para Julia estão incluídos     |
| Ferramentas relacionadas ao DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Como usar/executar?    

**Windows**:

* Em execução no prompt de comando

Abra o prompt de comando e simplesmente execute `julia`. 
* Como usar em um IDE

Use o IDE Julia `Juno` instalado no DSVM e disponível como um atalho na área de trabalho.

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como `Julia VERSION` 

* Instalando pacotes Julia

O local Julia padrão é um ambiente global legível por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, execute Julia usando um dos métodos acima. Em seguida, você pode executar os comandos do gerenciador de pacotes Julia como `Pkg.add()` para instalar ou atualizar pacotes. 


**Linux**:
* em execução no terminal.

Abra terminal e simplesmente execute `julia`. 
* Como usar em um IDE

Use `Juno` o IDE Julia instalado no DSVM e disponível como um atalho de menu do aplicativo.

* Como usar no Jupyter

Abra o Jupyter e clique no botão `New` para criar um novo bloco de anotações. Neste ponto, você pode escolher o tipo de kernel como `Julia VERSION` 

* Instalando pacotes Julia

O local Julia padrão é um ambiente global legível por todos os usuários. Porém, somente administradores podem gravar/instalar pacotes globais. Para instalar o pacote no ambiente global, execute Julia usando um dos métodos acima. Em seguida, você pode executar os comandos do gerenciador de pacotes Julia como `Pkg.add()` para instalar ou atualizar pacotes. 

## <a name="other-languages"></a>Outras linguagens

**C#**: Disponível no Windows e acessível por meio do Visual Studio Community Edition ou em um `Developer Command Prompt for Visual Studio`, no qual é possível executar o comando `csc`. 

**Java**: O OpenJDK está disponível nas edições para Linux e Windows do DSVM e definido no caminho. Você pode digitar o comando `javac` ou `java` no prompt de comando no Windows ou no shell bash no Linux para usar Java. 

**node.js**: node.js está disponível nas edições para Linux e Windows do DSVM e definido no caminho. Você pode digitar o comando `node` ou `npm` no prompt de comando no Windows ou no shell bash no Linux para acessar node.js. No Windows, a extensão Ferramentas Node.js para Visual Studio está instalada para fornecer um IDE gráfico para desenvolver seu aplicativo node.js. 

**F#**: Disponível no Windows e acessível por meio do Visual Studio Community Edition ou em um `Developer Command Prompt for Visual Studio`, no qual é possível executar o comando `fsc`. 


