<properties linkid="manage-linux-commontasks-install-software" urlDisplayName="Instalar software na VM" pageTitle="Instalar software em uma máquina virtual Linux - Azure" metaKeywords="" description="Aprender a instalar o software na máquina virtual Linux no Azure usando CentOS/Red Hat ou Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalar software na máquina virtual Linux no Azure" authors="" solutions="" manager="" editor="" />





#Instalar software na máquina virtual Linux no Azure

As distribuições do Linux tendem a usar "pacotes" de software para instalar software. Esses pacotes normalmente são gerenciados usando-se um conjunto de comandos, como `apt` ou `yum`. Também é possível instalar programas sem um pacote, como com um _tarball_ do código-fonte.

Abordaremos como usar os gerenciadores de pacotes para algumas das distribuições do Linux comuns. As etapas serão diferentes com base em qual distribuição do Linux que você está usando.

**Observação:** dependendo de como o ambiente está configurado, esses comandos talvez precisem ser executados com privilégios de raiz (via `sudo`).

##CentOS/Red Hat

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


Ubuntu
------

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

