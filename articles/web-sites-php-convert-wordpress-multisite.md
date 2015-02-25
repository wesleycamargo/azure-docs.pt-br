<properties 
	pageTitle="Converter um site do WordPress em um Multissite" 
	description="Saiba como selecionar um site WordPress existente criado por meio da galeria no Azure e convertê-lo em multissite WordPress" 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/28/2014" 
	ms.author="tomfitz"/>





# Converter um site do WordPress em um Multissite

* Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Neste tutorial, você aprenderá como tirar um site do WordPress existente criado por meio da galeria no Azure e converter em uma instalação de multissite do WordPress. Além disso, você aprenderá a atribuir um domínio personalizado para cada um dos subsites dentro de sua instalação.

Presume-se que você tem uma instalação existente do WordPress. Se você não fizer isso, siga as orientações apresentadas no [criar um site do WordPress da galeria no Azure][website-from-gallery].

Convertendo um WordPress existente instalar único site multissite geralmente é bastante simple e muitas das etapas iniciais são provenientes diretamente o [criar uma rede][wordpress-codex-create-a-network] página no [WordPress Codex](http://codex.wordpress.org).

Vamos começar.

## Permitir que o multissite

Primeiro, você precisa ativar multissite através do arquivo `wp-config.php` com a constante **WP\_ALLOW\_MULTISITE**. Há dois métodos para editar os arquivos do site: o primeiro é por meio de FTP e o segundo através de Git. Se você não estiver familiarizado com a instalação de qualquer um desses métodos, consulte os seguintes tutoriais:

* [Site do PHP com o MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site do PHP com o MySQL e Git][website-w-mysql-and-git-git-setup]

Abra o arquivo  `wp-config.php` com o editor da sua escolha e adicione o seguinte acima da linha `/* That's all, stop editing! Happy blogging. */`.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvá-lo e carregá-lo de volta para o servidor!

## Configuração de rede

Efetue logon na área *wp-admin* de seu site e você verá um novo item sob o menu **Ferramentas** chamado **Configuração de rede**. Clique em **o programa de instalação de rede** e preencha os detalhes da sua rede.

![Network Setup Screen][wordpress-network-setup]

Este tutorial usa o esquema de site  *Sub-directories* porque ele sempre deve funcionar, e iremos configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível para um subdomínio instalar se você mapear um domínio por meio de curinga o Portal e a configuração DNS corretamente a instalação.

Para obter mais informações sobre as configurações de subdomínio e subdiretório, consulte o artigo [Tipos de rede multissitewordpress-codex-types-of-networks][wordpress-codex-types-of-networks] sobre o codex do WordPress.

## Ativar a rede

A rede agora está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalizar as configurações  `wp-config.php` e certifique-se que o  `web.config` roteie corretamente a cada site.


Depois de clicar no botão **Instalar** na página *Network Setup*, o WordPress tentará atualizar os arquivos `wp-config.php` e `web.config`. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Editar e salvar os arquivos.


Depois de fazer essas atualizações, você precisa fazer logoff e re-login ao painel wp-admin.

Agora deve haver um menu adicional na barra de admin de rotulado **Meus Sites**. Esse menu permite que você controle sua nova rede através do **Admin de rede** painel de controle.

# Adicionando domínios personalizados

O plug-in [Mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] facilita adicionar domínios personalizados para qualquer site na sua rede. O plug-in funcionar corretamente, é necessário fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

## Habilitar o mapeamento de domínio para o site

O modo de site gratuito padrão não dá suporte à adição de domínios personalizados para sites do Azure. Você precisará alternar para o modo padrão ou compartilhado. Para fazer isso:

* Efetuar login no Portal do Azure e localize seu site da web. 
* Clique na guia **Escala** na área de conteúdo principal
* Em **geral**, selecione *SHARED* ou *STANDARD*
* Clique em **Salvar**

Você pode receber uma mensagem pedindo-lhe para verificar a alteração e confirmar o seu site da web agora podem incorrer em custos, dependendo do uso e outra configuração definida por você.

Demora alguns segundos para processar as novas configurações, agora é um bom momento para iniciar a configuração do domínio.

## Verifique se seu domínio

Antes dos sites do Azure permitirem mapear um domínio para o site, você precisa verificar se tem autorização para mapear o domínio. Para fazer isso, você deve adicionar um novo registro CNAME para sua entrada DNS.

* Efetuar login no Gerenciador de DNS do seu domínio
* Criar um novo CNAME *awverify*
* Apontar *awverify* para *awverify.YOUR_DOMAIN.azurewebsites.net*

Levará algum tempo para que as alterações DNS entrar em vigor, portanto, se estas etapas não funcionar imediatamente, uma xícara de café, e em seguida, volte e tente novamente.

## Adicionar o domínio para seu site

Retorne ao seu site por meio do Portal do Azure e neste momento cique na guia **CONFIGURE**. O botão **MANAGE DOMAINS** deve estar disponível. Clique.

A caixa de diálogo *Manage custom domains* mostra o pop-up. Isso é onde você digitará todos os domínios que você deseja atribuir ao seu site. Se um domínio não estiver listado aqui, ela não estará disponível para mapeamento dentro de WordPress, independentemente de como o domínio DNS é o programa de instalação.

![Caixa de diálogo domínios personalizados gerenciar][wordpress-manage-domains]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o registro CNAME  *awverify* criado anteriormente. Se o DNS não totalmente tem propigated, um indicador vermelho será exibido. Se tiver êxito, você verá uma marca de seleção verde. 

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro para o seu domínio.

## Configurar o domínio de um registro

Se as outras etapas forem bem-sucedidas, agora você poderá atribuir o domínio para seu site do Azure por meio de um registro DNS A. 

É importante observar aqui que sites do Azure aceitam CNAME e registros A, no entanto você  *must* usar um registro A para habilitar o mapeamento de domínio correto. Um CNAME não pode ser encaminhado para outro CNAME, que é o Azure criada para você com YOUR_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, volte para o Gerenciador de DNS e configurar o registro para apontar para esse IP.


## Instalar e configurar o plug-in

O multissite WordPress atualmente não tem um método para mapear domínios personalizados. No entanto, há um plug-in chamado [mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] que adiciona a funcionalidade para você. Login à parte de seu site do administrador de rede e instalar o **mapeamento de domínio do WordPress MU** plug-in.

Após instalar e ativar o plug-in, visite **configurações** > **mapeamento de domínio** para configurar o plug-in. Na primeira caixa de texto,  *Server IP Address*, insira o endereço IP que você usou para configurar o registro A para o domínio. Defina qualquer *Domain Options* que você deseja (os padrões geralmente são bons) e clique em **Salvar**.

## Mapa de domínio

Visite o **painel** para o site que deseja mapear o domínio. Clique em **ferramentas** > **mapeamento de domínio** e digite o novo domínio na caixa de texto e clique em **Add**.

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você deseja ter todo o tráfego enviado para o novo domínio, marque a caixa de seleção *Primary domain for this blog* antes de salvar. Você pode adicionar um número ilimitado de domínios em um site, mas apenas um pode ser o principal.

## Fazê-lo novamente

Os sites do Azure permitem que você adicione um número ilimitado de domínios a um site. Para adicionar outro domínio, você precisará executar o **Verifique se seu domínio** e **o domínio de um registro de instalação** seções para cada domínio.	

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png



<!--HONumber=42-->
