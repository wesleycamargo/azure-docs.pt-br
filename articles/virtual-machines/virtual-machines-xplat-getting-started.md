<properties
   pageTitle="Como criar uma máquina Virtual do Azure com a CLI do Azure | Microsoft Azure"
   description="Este tópico descreve como instalar a CLI do Azure em qualquer plataforma, como usá-la para se conectar à sua conta do Azure e como criar uma VM a partir da CLI do Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Como criar uma VM usando a interface de linha de comando do Azure (CLI do Azure)
A CLI do Azure é uma ótima maneira de gerenciar sua infraestrutura do Azure de qualquer plataforma.

Instalar a CLI do Azure e ter uma assinatura do Azure impedirá você de criar uma VM imediatamente, portanto, vamos cuidar dessas etapas. Se você não tiver uma conta do Azure, [obtenha uma gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## Como instalar a CLI do Azure

Siga as instruções para [instalar a CLI do Azure](../xplat-cli.md#install).

## Conectando ao Azure usando a CLI do Azure

Você pode conectar sua instalação da CLI do Azure a uma conta pessoal, empresarial ou escolar do Azure. Para compreender as diferenças e escolher, consulte [Como se conectar à sua assinatura do Azure](../xplat-cli.md#configure).

## Criando uma VM no Azure e conectando-se a ela

A criação de uma máquina virtual começa com a escolha (ou carregamento) de uma imagem e o uso do comando `azure vm create`.

1. Para escolher uma imagem da linha de comando, você poderá listar as imagens da VM disponíveis usando o comando `azure vm image list`. Como há muitas imagens, é aconselhável paginar os resultados usando `more` ou filtrá-los usando `grep` (Linux) ou `findstr` (Windows). Por exemplo, se você estiver procurando por imagens Ubuntu no Linux, use um comando como este:

        azure vm image list | grep Ubuntu

    Ainda assim, isso resultaria em uma longa lista de imagens, que você pode refinar ainda mais por versão:

        azure vm image list | grep Ubuntu-14_10

    A partir daqui, você pode escolher uma imagem e usar o comando `show` para exibir suas propriedades com mais detalhes:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-br-30GB

2. Depois de escolher uma imagem de VM, use o comando `vm create` para criar a imagem. Este comando tem muitas opções que você pode listar com o comando `help`:

        vm create --help

    Junto com a imagem da etapa 1, os principais argumentos de que você precisa para criar uma máquina virtual são local, nome DNS e nome de usuário.

    Para autenticação, você pode escolher especificar uma senha (na linha de comando ou interativamente) ou autenticar usando um certificado. Se você escolher uma senha, ela precisará ter pelo menos 8 caracteres, conter letras minúsculas e maiúsculas e conter um caractere especial (por exemplo, um entre !@#$%^&+=). É uma boa idéia colocar a senha entre aspas e caracteres de escape especiais se você estiver transmitindo na linha de comando.

    Para escolher um local, use o comando `vm location list` para selecionar uma região perto de você.

  O nome DNS escolhido precisa ser exclusivo (ele será mapeado para `dnsname.cloudapp.net`) e será igual ao nome de computador se você não especificar um nome de computador na linha de comando separadamente.

    The Linux example below creates a VM in West US, opens the default SSH port 22 (the -e argument), and creates a user called `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-br-30GB "myadminuser" "myAdm1n@passwd"

## Próximas etapas

Vamos fazer algo com sua VM.

Como o exemplo acima abriu a porta SSH padrão, é fácil conectar-se à VM depois que ela estiver em funcionamento. Em uma linha de comando do Linux:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Um ótimo lugar para ver mais exemplos de como usar a CLI do Azure para gerenciar sua infraestrutura do Azure é a [página de referência de comandos da CLI do Azure](../virtual-machines-command-line-tools.md)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!---HONumber=July15_HO3-->