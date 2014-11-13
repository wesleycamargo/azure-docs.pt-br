<properties urlDisplayName="Intro to Linux" pageTitle="Introdu&ccedil;&atilde;o ao Linux no Azure - Tutorial do Azure" metaKeywords="Azure Linux vm, Linux vm" description="Saiba como usar m&aacute;quinas virtuais Linux no Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introdu&ccedil;&atilde;o ao Linux no Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Introdução ao Linux no Azure

Este tópico apresenta uma visão geral de alguns aspectos do uso de máquinas virtuais Linux na nuvem do Azure. A implantação de uma máquina virtual Linux é um processo simples ao usar uma imagem já existente na Galeria.

## Sumário

-   [Autenticação: Nomes de usuário, senhas e chaves SSH.][Autenticação: Nomes de usuário, senhas e chaves SSH.]
-   [Geração e uso de chaves SSH para registro em log em máquinas virtuais Linux.][Geração e uso de chaves SSH para registro em log em máquinas virtuais Linux.]
-   [Obtendo privilégios de superusuário usando sudo][Obtendo privilégios de superusuário usando sudo]
-   [Configuração do firewall][Configuração do firewall]
-   [Alterações de nome do host][Alterações de nome do host]
-   [Captura de imagem de máquina virtual][Captura de imagem de máquina virtual]
-   [Anexando discos][Anexando discos]

## <span id="authentication"></span></a>Autenticação: Nomes de usuário, senhas e chaves SSH

Ao criar uma máquina virtual Linux usando o Portal de Gerenciamento do Azure, você deve fornecer um nome de usuário, uma senha e (como opção) uma chave pública SSH. A escolha de um nome de usuário para implantação de uma máquina virtual Linux no Azure está sujeita à seguinte restrição: nomes de contas do sistema (UID \<100) já presentes na máquina virtual não são permitidos; “root”, por exemplo.

-   Consulte [Como usar SSH com Linux no Azure][Como usar SSH com Linux no Azure]

### <span id="keygeneration"></span></a>Geração da chave SSH

A versão atual do Portal de Gerenciamento aceita apenas chaves públicas SSH encapsuladas em um certificado X509. Siga as etapas abaixo para gerar e usar chaves SSH com o Azure.

1.  Use openssl para gerar um certificado X509 com um par de chaves RSA de 2.048 bits.

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Responda a algumas perguntas que o openssl faz (você pode deixá-las em branco). O conteúdo desses campos não é usado pela plataforma.

2.  Altere as permissões na chave privada para protegê-la.

        chmod 600 myPrivateKey.key

3.  Converter o `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER)

        openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Carregue o `myCert.cer` ao criar a máquina virtual Linux. O processo de provisionamento instalará automaticamente a chave pública nesse certificado no arquivo `~/.ssh/authorized_keys` para o usuário especificado na máquina virtual.

5.  Conecte-se à máquina virtual Linux usando o ssh.

        ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    Você será solicitado a aceitar a impressão digital da chave pública do host na primeira vez que você efetuar o login.

6.  Opcionalmente, você pode copiar `myPrivateKey.key` to `~/.ssh/id_rsa` para que seu cliente openssh possa selecionar automaticamente isso sem usar a opção -i.
     Como alternativa, você pode modificar `~/.ssh/config` para incluir uma seção da sua máquina virtual:

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key

### Gerar uma chave com base em uma chave compatível com OpenSSH existente

O exemplo anterior descreve como criar uma nova chave a ser usada com o Windows Azure. Em alguns casos, os usuários talvez já tenham um par de chaves OpenSSH pública e privada compatíveis existente e queira usar as mesmas chaves com o Windows Azure.

As chaves privadas OpenSSH são diretamente legíveis pelo utilitário `openssl`. O seguinte comando usará uma chave privada SSH existente (id\_rsa no exemplo abaixo) e criará a chave pública `.pem` necessária ao Microsoft Azure:

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

O arquivo **myCert.pem** é a chave pública que pode ser usada para provisionar uma máquina virtual Linux no Windows Azure. Durante o provisionamento, o arquivo `.pem` será convertido em uma chave pública `openssh` compatível e colocada em `~/.ssh/authorized_keys`.

## <span id="superuserprivileges"></span></a>Obtendo privilégios de superusuário usando `sudo`

A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. Essa conta é configurada pelo Agente Linux do Azure para poder elevar privilégios para raiz (conta de superusuário) usando o utilitário `sudo`. Depois de fazer logon usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando.

    # sudo <COMMAND>

Opcionalmente, você pode obter um shell de root usando **sudo -s**.

-   Consulte [Usando privilégios de raiz em máquinas virtuais Linux do Azure][Usando privilégios de raiz em máquinas virtuais Linux do Azure]

## <span id="firewallconfiguration"></span></a>Configuração do firewall

O Azure fornece um filtro de pacote de entrada que restringe a conectividade a portas especificadas no Portal de Gerenciamento. Por padrão, a única porta permitida é SSH. Você pode abrir acesso a portas adicionais na máquina virtual Linux configurando pontos de extremidade no Portal de Gerenciamento.

-   Consulte: [Como instalar pontos de extremidade em uma máquina virtual][Como instalar pontos de extremidade em uma máquina virtual]

As imagens do Linux na Galeria do Azure não habilitam o firewall *iptables* por padrão. Se desejado, o firewall poderá ser configurado para fornecer filtragem adicional.

## <span id="hostnamechanges"></span></a>Alterações de nome do host

Ao implantar uma instância de uma imagem do Linux inicialmente, você precisa fornecer um nome de host para a máquina virtual. Quando a máquina virtual está em execução, esse nome de host é publicado nos servidores DNS da plataforma, de forma que as várias máquinas virtuais conectadas entre si possam executar pesquisas de endereço IP usando nomes de host.

Se forem desejadas alterações no nome do host depois da implantação de uma máquina virtual, use o comando

    # sudo hostname <newname>

O Agente Linux do Azure inclui uma funcionalidade para detectar automaticamente essa alteração de nome e configurar corretamente a máquina virtual para persistir nessa alteração e, além disso, publicá-la nos servidores DNS da plataforma.

-   [Guia do usuário do agente Linux para o Azure][Guia do usuário do agente Linux para o Azure]

### Imagens do Ubuntu

As imagens do Ubuntu utilizam inicialização de nuvem, que fornece recursos adicionais para inicializar uma máquina virtual.

-   Consulte [Dados personalizados e inicialização de nuvem no Microsoft Azure][Dados personalizados e inicialização de nuvem no Microsoft Azure]

## <span id="virtualmachine"></span></a>Captura de imagem da máquina virtual

O Azure oferece a possibilidade de capturar o estado de uma máquina virtual existente em uma imagem que pode ser usada depois na implantação de instâncias de máquina virtual. O Agente Linux do Azure pode ser usado para reverter algumas das personalizações que foram realizadas durante o processo de provisionamento. Você pode seguir as seguintes etapas para capturar uma máquina virtual como uma imagem:

1.  Execute **waagent-deprovision** para desfazer a personalização do provisionamento. Ou **waagent -deprovision+user** para, opcionalmente, excluir a conta de usuário especificada durante o provisionamento e todos os dados associados.

2.  Desligue a máquina virtual.

3.  Clique em *Capturar* no Portal de Gerenciamento ou use o PowerShell ou as ferramentas de CLI para capturar a máquina virtual como uma imagem.

-   Consulte: [Como capturar uma máquina virtual Linux para ser usada como um modelo][Como capturar uma máquina virtual Linux para ser usada como um modelo]

## <span id="attachingdisks"></span></a>Anexando discos

Cada máquina virtual tem um *disco de recursos* anexado. Como os dados em um disco de recurso talvez não sejam duráveis nas reinicializações, ele costuma ser usado por aplicativos e processos em execução na máquina virtual para o armazenamento de dados transitório e **temporário**. Ele também é usado para armazenar páginas ou trocar arquivos para o sistema operacional.

No Linux, o disco de recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu).

    >[WACOM.NOTE] Note that the resource disk is a **temporary** disk, and might be deleted and reformatted when the VM is rebooted.

No Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc`, e os usuários precisarão particionar, formatar e montar esse recurso. Isso é abordado passo a passo no tutorial: [Como anexar um disco de dados a uma máquina virtual][Como anexar um disco de dados a uma máquina virtual].

-   Consulte também: [Configurar RAID de software no Linux][Configurar RAID de software no Linux]

  [Autenticação: Nomes de usuário, senhas e chaves SSH.]: #authentication
  [Geração e uso de chaves SSH para registro em log em máquinas virtuais Linux.]: #keygeneration
  [Obtendo privilégios de superusuário usando sudo]: #superuserprivileges
  [Configuração do firewall]: #firewallconfiguration
  [Alterações de nome do host]: #hostnamechanges
  [Captura de imagem de máquina virtual]: #virtualmachine
  [Anexando discos]: #attachingdisks
  [Como usar SSH com Linux no Azure]: ../linux-use-ssh-key/
  [Usando privilégios de raiz em máquinas virtuais Linux do Azure]: ../virtual-machines-linux-use-root-privileges/
  [Como instalar pontos de extremidade em uma máquina virtual]: ../virtual-machines-set-up-endpoints/
  [Guia do usuário do agente Linux para o Azure]: ../virtual-machines-linux-agent-user-guide/
  [Dados personalizados e inicialização de nuvem no Microsoft Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Como capturar uma máquina virtual Linux para ser usada como um modelo]: ../virtual-machines-linux-capture-image/
  [Como anexar um disco de dados a uma máquina virtual]: ../virtual-machines-linux-how-to-attach-disk/
  [Configurar RAID de software no Linux]: ../virtual-machines-linux-configure-raid/
