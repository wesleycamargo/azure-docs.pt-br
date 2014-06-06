<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Introdução ao Linux" pageTitle="Introdução ao Linux no Azure - Tutorial do Azure" metaKeywords="Linux Azure vm, vm Linux" description="Saiba como usar máquinas virtuais Linux no Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introdução ao Linux no Azure" authors=""  solutions="" writer="" manager="" editor=""  />





#Introdução ao Linux no Azure

Este tópico fornece uma visão geral de alguns aspectos do uso de máquinas virtuais Linux na nuvem do Azure. A implantação de uma máquina virtual Linux é um processo simples ao usar uma imagem já existente na Galeria. 

## Sumário ##

* [Autenticação: nomes de usuário, senhas e chaves SSH.](#authentication)
* [Geração e uso de chaves SSH para registro em log em máquinas virtuais Linux.](#keygeneration)
* [Obtendo privilégios de superusuário usando sudo](#superuserprivileges)
* [Configuração do firewall](#firewallconfiguration)
* [Alterações de nome do host](#hostnamechanges)
* [Captura de imagem de máquina virtual](#virtualmachine)
* [Anexando discos](#attachingdisks)

<h2><a id="authentication"></a>Autenticação: nomes de usuário, senhas e chaves SSH. </h2>

Ao criar uma máquina virtual Linux usando o Portal de Gerenciamento do Azure, você precisa fornecer um nome de usuário, senha e (opcionalmente) uma chave pública SSH. A escolha de um nome de usuário para a implantação de uma máquina virtual Linux no Azure está sujeita à seguinte restrição: nomes de contas do sistema já presentes na máquina virtual não são permitidos - root por exemplo.  Se você não fornecer a chave pública SSH, você poderá fazer logon máquina virtual usando o nome do usuário e a senha especificados. Se você optar por carregar uma chave pública SSH no Portal de Gerenciamento, a autenticação baseada em senha será desabilitada e você precisará usar a chave privada SSH correspondente para autenticar com a máquina virtual e fazer logon.

<h2><a id="keygeneration"></a>Geração de chave SSH </h2>

A versão atual do Portal de Gerenciamento aceita apenas chaves públicas SSH encapsuladas em um certificado X509. Siga as etapas a seguir para gerar e usar chaves SSH com o Azure.

1. Use openssl para gerar um certificado X509 com um par de chaves RSA de 2048 bits.
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	Responda a algumas perguntas que o openssl faz (você pode deixá-las em branco). O conteúdo desses campos não é usado pela plataforma.

2. Altere as permissões na chave privada para protegê-la.

		chmod 600 myPrivateKey.key

3. Converter o myCert.pem em myCert.cer (certificado X509 codificado em DER)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Carregue o myCert.cer ao criar a máquina virtual Linux. O processo de provisionamento irá instalar automaticamente a chave pública neste certificado no arquivo authorized_keys para o usuário especificado na máquina virtual.

5. Conecte-se à máquina virtual Linux usando o ssh.

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	Você será solicitado a aceitar a impressão digital da chave pública do host na primeira vez que você efetuar o login.

6. Opcionalmente, você pode copiar 'myPrivateKey.key' em ~/.ssh/id_rsa para que seu cliente openssh possa selecionar automaticamente isso sem usar a opção -i.

<h2><a id="superuserprivileges"></a>Obtendo privilégios de superusuário usando sudo</h2> 

A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. Essa conta é configurada pelo Agente Linux do Azure para poder elevar privilégios para root (conta de superusuário) usando a ferramenta sudo. Quando estiver conectado usando essa conta de usuário, você poderá executar comandos como root usando o "comando sudo". Opcionalmente, você pode obter um shell de root usando **sudo -s**.

<h2><a id="firewallconfiguration"></a>Configuração do firewall </h2>

O Azure fornece um filtro de pacote de entrada que restringe a conectividade para portas especificadas no Portal de Gerenciamento. Por padrão, a única porta permitida é SSH. Você pode abrir o acesso a portas adicionais em sua máquina virtual Linux adicionando regras no Portal de Gerenciamento.

As imagens do Linux na galeria não habilitam o firewall iptables dentro das máquinas virtuais Linux. Se desejado, o firewall IPtables poderá ser configurado para fornecer recursos adicionais.

<h2><a id="hostnamechanges"></a>Alterações de nome do host </h2>

Ao implantar uma instância de uma imagem do Linux inicialmente, você precisa fornecer um nome de host para a máquina virtual. Quando a máquina virtual está em execução, esse nome de host é publicado nos servidores DNS da plataforma, de forma que as várias máquinas virtuais conectadas entre si possam executar pesquisas de endereço IP usando nomes de host. Se forem desejadas alterações no nome do host depois da implantação de uma máquina virtual, use o comando **hostname newname**. O Agente Linux do Azure inclui funcionalidade para detectar automaticamente essa alteração de nome e configurar adequadamente a máquina virtual para persistir essa alteração e, além disso, publicar essa alteração nos servidores DNS da plataforma. Consulte o arquivo README do Agente Linux do Azure Linux para obter detalhes adicionais e opções de configuração relacionadas a essa funcionalidade.

<h2><a id="virtualmachine"></a>Captura de imagem de máquina virtual </h2>

O Azure fornece a capacidade de capturar o estado de uma máquina virtual existente em uma imagem que, posteriormente, pode ser usada para implantar instâncias de máquinas virtuais adicionais. O Agente Linux do Azure pode ser usado para reverter algumas das personalizações que foram executadas durante o processo de provisionamento. Você pode seguir as seguintes etapas para capturar uma máquina virtual como uma imagem:

1. Execute **waagent-deprovision** para desfazer a personalização do provisionamento. Ou **waagent -deprovision+user** para, opcionalmente, excluir a conta de usuário especificada durante o provisionamento e todos os dados associados.

2. Desligue a máquina virtual usando as ferramentas do Portal de Gerenciamento.

3. Clique em Capturar no Portal de Gerenciamento ou use as ferramentas para capturar a máquina virtual como uma imagem.

<h2><a id="attachingdisks"></a>Anexando discos </h2>

Isso é abordado passo a passo no tutorial sobre como [criar uma máquina virtual Linux](../virtual-machine-from-gallery/).


