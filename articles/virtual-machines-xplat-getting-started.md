<properties
   pageTitle="Como criar uma Máquina Virtual do Azure com o xplat-cli"
   description="Este tópico descreve como instalar o xplat-cli em qualquer plataforma, como usá-lo para se conectar à sua conta do Azure e como criar uma VM pelo xplat-cli."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Criando uma VM usando a interface de linha de comando de plataforma cruzada do Azure (xplat-cli)
O xplat-cli é uma ótima maneira de gerenciar sua infraestrutura do Azure de qualquer plataforma.

Apenas instalar o xplat-cli e ter uma assinatura do Azure irá impedir a criação de uma VM imediatamente, portanto, vamos ver essas etapas. Se você não tiver uma conta do Azure, [obtenha uma gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## Instalar o xplat-cli

Siga estas instruções para instalar o [xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install).

## Conectando ao Azure com o xplat-cli

Você pode conectar sua instalação xplat-cli com uma conta pessoas do Azure ou com uma conta empresarial ou escolar do Azure. Para compreender as diferenças e escolher, consulte [Como se conectar à sua assinatura do Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure).

## Criando e conectando a uma VM no Azure

Criar uma máquina virtual começa com a escolha (ou carregamento) de uma imagem e o uso do comando  `azure vm create`.

1. Para escolher uma imagem da linha de comando, você pode listar as imagens da VM disponíveis usando o comando  `azure vm image list`. Como há muitas imagens, você desejará empaginar os resultados usando  `more` ou filtrar usando  `grep` (Linux) ou  `findstr` (Windows). Por exemplo, se você estiver procurando por imagens Ubuntu no Linux, use um comando como este:

        azure vm image list | grep Ubuntu

    Ainda assim, isso resultaria em uma longa lista de imagens, que você pode refinar ainda mais por versão:

        azure vm image list | grep Ubuntu-14_10

    A partir daqui, você pode escolher uma imagem e usar o comando  `show` para exibir suas propriedades com mais detalhes:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-br-30GB

2. Criando sua VM.

    Depois de escolher uma imagem de VM, use o comando  `vm create` para criar a imagem. Esse comando tem muitas opções que você pode listar com o comando de ajuda:

        vm create --help

    Junto com a imagem da etapa 1, os principais argumentos que você precisa para criar uma máquina virtual são local, nome DNS e nome de usuário.

    Para autenticação, você pode escolher especificar uma senha (na linha de comando ou interativamente) ou autenticar usando um certificado. Se você escolher uma senha, precisa ter pelo menos 8 caracteres, conter letras minúsculas e minúsculas e conter um caractere especial (por exemplo, um entre !@#$%^&+=). É uma boa idéia colocar a senha entre aspas e caracteres de escape especiais se você estiver transmitindo na linha de comando.

    Para escolher um local, você pode usar o comando  `vm location list` para selecionar uma região perto de você.

  O nome DNS que você escolher precisa ser exclusivo (ele será mapeado para  `dnsname.cloudapp.net`) e será o mesmo que o nome de computador se você não especificar um nome de computador na linha de comando separadamente.  

    O exemplo Linux a seguir cria uma VM no Oeste dos EUA, abre a porta SSH 22 padrão (o argumento -e) e cria um usuário chamado `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-pt-br-30GB "myadminuser" "myAdm1n@passwd"

## Próximas etapas

Vamos fazer algo com sua VM. 

Como o exemplo acima abriu a porta SSH padrão, é fácil conectar-se à VM depois que ela estiver em funcionamento. Em uma linha de comando do Linux:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Um ótimo lugar para ver mais exemplos de como usar o xplat-cli para gerenciar sua infraestrutura do Azure é a [ferramenta de linha de comando do Azure para Mac e Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
