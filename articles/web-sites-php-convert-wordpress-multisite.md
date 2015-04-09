<properties 
	pageTitle="Converter WordPress em Multissite no Serviço de Aplicativo do Azure" 
	description="Saiba como selecionar um aplicativo Web WordPress existente criado por meio da galeria no Azure e convertê-lo em Multissite WordPress" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>



# Converter WordPress em Multissite no Serviço de Aplicativo do Azure

## Visão geral

*Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Neste tutorial, você aprenderá como tirar um aplicativo Web do WordPress existente criado por meio da galeria no Azure e convertê-lo em uma instalação Multissite WordPress. Além disso, você aprenderá a atribuir um domínio personalizado para cada um dos subsites dentro de sua instalação.

Presume-se que você tem uma instalação existente do WordPress. Se você não fizer isso, siga as orientações apresentadas no [Criar um site do WordPress da galeria no Azure][website-from-gallery].

Converter uma única instalação WordPress existente em Multissite geralmente é bastante simples, e muitas das etapas iniciais provêm diretamente da página [Criar uma rede][wordpress-codex-create-a-network] no [Codex do WordPress](http://codex.wordpress.org).

Vamos começar.

## Permitir que o multissite

Primeiro, você precisa habilitar o Multissite por meio do arquivo `wp-config.php` com a constante **WP\_ALLOW\_MULTISITE**. Há dois métodos para editar os arquivos do seu aplicativo Web: a primeira é por meio de FTP e o segundo é por meio do Git. Se você não estiver familiarizado com a instalação de qualquer um desses métodos, consulte os seguintes tutoriais:

* [Site do PHP com o MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site do PHP com o MySQL e Git][website-w-mysql-and-git-git-setup]

Abra o arquivo `wp-config.php` com o editor de sua preferência e adicione o seguinte acima da linha `/* Isso é tudo, interrompa a edição! Divirta-se publicando no blog. Linha */`.

	/* Multissite */

	define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvá-lo e carregá-lo de volta para o servidor!

## Configuração de rede

Efetue logon na área *wp-admin* do seu aplicativo Web e você verá um novo item sob o menu **Ferramentas**, chamado **Configuração de rede**. Clique em **Configuração de rede** e preencha os detalhes da sua rede.

![Network Setup Screen][wordpress-network-setup]

Este tutorial usa o esquema de site de *Sub-directories* porque ele sempre deve funcionar, e iremos configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível configurar uma instalação de subdomínio se você mapear adequadamente um domínio por meio do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e do DNS curinga de configuração.

Para obter mais informações sobre o subdomínio vs. configurações de subpasta, consulte o artigo [Tipos de rede multissite][wordpress-codex-types-of-networks] no Codex do WordPress.

## Habilitar a rede

A rede agora está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalize as configurações de `wp-config.php` e certifique-se que o `web.config` roteie cada site corretamente.


Depois de clicar no botão **Instalar** na página *Network Setup*, o WordPress tentará atualizar os arquivos `wp-config.php` e `web.config`. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Editar e salvar os arquivos.


Depois de fazer essas atualizações você precisará fazer logoff e um novo logon no painel wp-admin.

Agora, deve haver um menu adicional na barra de admin rotulada como **Meus Sites** Esse menu permite que você controle sua nova rede por meio do painel de controle **Admin de rede**.

# Adicionando domínios personalizados

O plug-in [Mapeamento de domínio do WordPress MU (Multiusuário)][wordpress-plugin-wordpress-mu-domain-mapping] torna facílimo adicionar domínios personalizados a qualquer site na sua rede. O plug-in funcionar corretamente, é necessário fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

##  Habilitar o mapeamento de domínio para o aplicativo Web

O modo **Gratuito** do plano de [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) não dá suporte a adição de domínios personalizados para aplicativos Web. Você precisará alternar para o modo **Padrão** ou **Compartilhado**. Para fazer isso:

* Efetue logon no Portal do Azure e localize seu aplicativo Web. 
*  Clique na guia **Escala** na área de conteúdo principal
* Em **Geral**, selecione *SHARED* ou *STANDARD*
* Clique em **Salvar**

Você pode receber uma mensagem pedindo-lhe para verificar a alteração e confirmar que o seu aplicativo Web agora pode incorrer em custos, dependendo do uso e de outras configurações definidas por você.

Demora alguns segundos para processar as novas configurações, portanto agora é um bom momento para iniciar a configuração do domínio.

## Verifique o seu domínio

Antes que Aplicativos Web permitam a você mapear um domínio para o site, você precisa primeiro verificar se você tem autorização para mapear tal domínio. Para fazer isso, você deve adicionar um novo registro CNAME à sua entrada DNS.

* Efetuar login no Gerenciador de DNS do seu domínio
* Criar um novo CNAME *awverify*
* Aponte *awverify* para *awverify.YOUR_DOMAIN.azurewebsites.net*

Levará algum tempo para que as alterações DNS entrar em vigor, portanto, se estas etapas não funcionar imediatamente, uma xícara de café, e em seguida, volte e tente novamente.

##  Adicionar o nome de domínio ao aplicativo Web

Voltar ao seu aplicativo Web por meio do Portal do Azure, clique em **Configurações** e, em seguida, clique em **Domínios personalizados e SSL**.

Quando as *Configurações de SSL* forem exibidas, você verá os campos onde digitará todos os domínios que você deseja atribuir ao seu aplicativo Web. Se um domínio não estiver listado aqui, ele não estará disponível para mapeamento dentro do WordPress, independentemente de como o domínio DNS for configurado.

![Manage custom domains dialog][wordpress-manage-domains]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o registro CNAME que você criou anteriormente. Se o DNS não foi totalmente propagado, um indicador vermelho será exibido. Se tiver êxito, você verá uma marca de seleção verde. 

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro para o seu domínio.

## Configurar o domínio de um registro

Se as outras etapas forem bem-sucedidas, agora você pode atribuir o domínio a seu aplicativo Web do Azure por meio de um registro A DNS. 

É importante observar aqui que aplicativos Web do Azure aceitam registros CNAME e A; no entanto, você *deve* usar um registro para habilitar o mapeamento de domínio correto. Um CNAME não pode ser encaminhado para outro CNAME, que é o Azure criada para você com YOUR_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, volte para o Gerenciador de DNS e configurar o registro para apontar para esse IP.


## Instalar e configurar o plug-in

O Multissite WordPress atualmente não tem um método para mapear domínios personalizados. No entanto, há um plug-in chamado [Mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] que adiciona a funcionalidade para você. Login à parte de seu site do administrador de rede e instalar o **mapeamento de domínio do WordPress MU** plug-in.

Após instalar e ativar o plug-in, visite **configurações** > **mapeamento de domínio** para configurar o plug-in. Na primeira caixa de texto, *Endereço IP do servidor*, insira o endereço IP usado por você para configurar o registro A para o domínio. Defina quaisquer *Opções de Domínio* que você desejar (os padrões geralmente funcionam bem) e clique em **Salvar**.

## Mapear o domínio

Visite o **Painel** para o site para o qual você deseja mapear o domínio. Clique em **Ferramentas** > **Mapeamento de domínio** e digite o novo domínio na caixa de texto, então clique em **Adicionar**

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você deseja ter todo o tráfego enviado para o novo domínio, selecione a caixa  *Domínio primário para este blog* antes de salvar. Você pode adicionar um número ilimitado de domínios em um site, mas apenas um pode ser o primário.

## Fazê-lo novamente

Aplicativos Web do Azure permitem que você adicione um número ilimitado de domínios a um aplicativo Web. Para adicionar outro domínio, você precisará executar o **Verifique se seu domínio** e **o domínio de um registro de instalação** seções para cada domínio.	

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png


<!--HONumber=49-->