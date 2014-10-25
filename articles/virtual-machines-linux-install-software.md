<properties linkid="manage-linux-commontasks-install-software" urlDisplayName="Install software on VM" pageTitle="Install software on a Linux virtual machine - Azure" metaKeywords="" description="Learn how to install software on your Linux virtual machine in Azure by using CentOS/Red Hat or Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install software on your Linux virtual machine in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Instalar software na máquina virtual Linux no Azure

As distribuições do Linux tendem a usar "pacotes" de software para instalar software. Esses pacotes são normalmente gerenciados usando um conjunto de comandos, tais como `apt` ou `yum`. Também é possível instalar programas sem um pacote, como com um *tarball* do código-fonte.

Abordaremos como usar os gerenciadores de pacotes para algumas das distribuições do Linux comuns. As etapas serão diferentes com base em qual distribuição do Linux que você está usando.

**Observação:** Dependendo de como o ambiente está configurado, esses comandos talvez precisem ser executados com privilégios de raiz (via `sudo`).

## CentOS/Red Hat

CentOS acompanha `yum` para gerenciamento de pacotes. Com essa ferramenta, é possível instalar, desinstalar, atualizar, listar pacotes instalados e muito mais. Veja abaixo a sintaxe desses comandos.

### Instalando

Isso instalará um pacote, bem como todos os pacotes dos quais ele depende. Devido às dependências, mais de um pacote pode ser instalado.

    yum install [package name]

### Desinstalando

Isso desinstalará um pacote do computador. Lembre-se de que isso não removerá nenhuma dependência.

    yum remove [package name]

### Atualizando

Isso atualizará um pacote para a versão mais recente. O pacote deve ser instalado antes de atualizá-lo.

    yum update [package name]

### Listando pacotes instalados

Isso mostrará uma lista de todos os pacotes instalados no computador.

    yum list installed

## Ubuntu

Ubuntu acompanha `apt` (Ferramenta de Empacotamento Avançada) para gerenciamento de pacotes. Com essa ferramenta, é possível instalar, desinstalar, atualizar, listar pacotes instalados e muito mais. Veja abaixo a sintaxe desses comandos.

### Instalando

Isso instalará um pacote, bem como todos os pacotes dos quais ele depende. Devido às dependências, mais de um pacote pode ser instalado.

    apt-get install [package name]

### Desinstalando

Isso desinstalará um pacote do computador. Lembre-se de que isso não removerá nenhuma dependência.

    apt-get remove [package name]

### Atualizando

Para atualizar para uma versão nova, você precisará usar dois comandos: um para atualizar o índice de pacote e outro para atualizar os pacotes. Execute o seguinte comando para atualizar o índice de pacote:

    apt-get update

Assim que o índice de pacote for atualizado, execute o seguinte comando para atualizar os pacotes:

    apt-get upgrade
