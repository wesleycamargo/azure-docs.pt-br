<properties urlDisplayName="Convert a WordPress Site to a Multisite" pageTitle="Converter um site WordPress em um multissite" metaKeywords="WordPress, multissite" description="Saiba como selecionar um site WordPress existente criado por meio da galeria no Azure e convertê-lo em multissite WordPress" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Convert a WordPress Site to a Multisite" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/28/2014" ms.author="tomfitz" />





# Converter um site do WordPress em um Multissite

*Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Neste tutorial, você aprenderá como tirar um site do WordPress existente criado por meio da galeria no Azure e converter em uma instalação de multissite do WordPress. Além disso, você aprenderá a atribuir um domínio personalizado para cada um dos subsites dentro de sua instalação.

Presume-se que você tem uma instalação existente do WordPress. Se você não fizer isso, siga as orientações apresentadas em [Criar um site do WordPress da galeria no Azure][website-from-gallery].

Converter uma instalação única do WordPress em multissite geralmente é bastante simples e muitas das etapas iniciais são provenientes diretamente da página [Criar uma rede][wordpress-codex-create-a-network] no [codex do WordPress](http://codex.wordpress.org).

Vamos começar.

## Permitir que o multissite

Primeiro você precisa habilitar o multissite por meio do arquivo `wp-config.php` com a constante **WP\_ALLOW\_MULTISITE**. Há dois métodos para editar seus arquivos do site: o primeiro é por meio do FTP, e o segundo por meio do Git. Se você não estiver familiarizado com a instalação de qualquer um desses métodos, consulte os seguintes tutoriais:

* [Site do PHP com o MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site do PHP com o MySQL e Git][website-w-mysql-and-git-git-setup]

Abra o arquivo `wp-config.php` com o editor de sua preferência e adicione o seguinte acima da linha `/* That's all, stop editing! Blogs feliz. * /'.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvá-lo e carregá-lo de volta para o servidor!

## Configuração de rede

Efetue logon na área *wp-admin* de seu site e você verá um novo item sob o menu **ferramentas** chamado **Configuração de rede**. Clique em **Configuração de rede** e preencha os detalhes da sua rede.

![Network Setup Screen][wordpress-network-setup]

Este tutorial usa o esquema de site de *subdiretórios* porque ele sempre deve funcionar, e iremos configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível configurar uma instalação de subdomínio se você mapear um domínio por meio do portal e do DNS curinga de configuração adequadamente.

Para obter mais informações sobre as configurações de subdomínio e subdiretório, consulte o artigo [Tipos de rede multissite][wordpress-codex-types-of-networks] sobre o codex do WordPress.

## Ativar a rede

A rede agora está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalize as configurações `wp-config.php` e certifique-se de que `web.config` direcione os sites corretamente.


Depois de clicar no botão **Instalar** na página *Configuração de rede*, o WordPress tentará atualizar os arquivos `wp-config.php` e `web.config`. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Edite e salve os arquivos.


Depois de fazer essas atualizações, você precisa fazer logoff e re-login ao painel wp-admin.

Agora deve haver um menu adicional na barra de administração chamado **Meus Sites**. Esse menu permite que você controle sua nova rede através do painel **Administração de rede**.

# Adicionando domínios personalizados

O plug-in [Mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] facilita adicionar domínios personalizados para qualquer site na sua rede. Para o plug-in funcionar corretamente, é necessário fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

## Habilitar o mapeamento de domínio para o site

O modo de site gratuito padrão não dá suporte à adição de domínios personalizados para sites do Azure. Você precisará alternar para o modo padrão ou compartilhado. Para fazer isso:

* Efetue logon no Portal do Azure e localize seu site. 
* Clique na guia **Escala** na área de conteúdo principal
* Em **Geral**, selecione *COMPARTILHADO* ou *PADRÃO*
*  Clique em **Salvar**.

Você pode receber uma mensagem pedindo-lhe para verificar a alteração e confirmar o seu site da web agora podem incorrer em custos, dependendo do uso e outra configuração definida por você.

Demora alguns segundos para processar as novas configurações, agora é um bom momento para iniciar a configuração do domínio.

## Verifique se seu domínio

Antes dos sites do Azure permitirem mapear um domínio para o site, você precisa verificar se tem autorização para mapear o domínio. Para fazer isso, você deve adicionar um novo registro CNAME em sua entrada DNS.

* Efetuar login no Gerenciador de DNS do seu domínio
* Crie um novo CNAME *awverify*
* Aponte *awverify* para *awverify.YOUR_DOMAIN.azurewebsites.net*

Levará algum tempo para que as alterações DNS entrar em vigor, portanto, se estas etapas não funcionar imediatamente, uma xícara de café, e em seguida, volte e tente novamente.

## Adicionar o domínio para seu site

Retorne ao site por meio do Portal do Azure, e clique na guia **CONFIGURAR**. O **gerenciar domínios** botão deve estar disponível. Clique.

O diálogo *Gerenciar domínios personalizados* é exibido. Isso é onde você digitará todos os domínios que você deseja atribuir ao seu site. Se um domínio não estiver listado aqui, ele não estará disponível para mapeamento dentro do WordPress, independentemente de como o domínio DNS está configurado.

![Manage custom domains dialog][wordpress-manage-domains]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o registro CNAME *awverify* criado anteriormente. Se o DNS não totalmente tem propigated, um indicador vermelho será exibido. Se tiver êxito, você verá uma marca de seleção verde. 

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro A para o seu domínio.

## Configurar o domínio de um registro

Se as outras etapas forem bem-sucedidas, agora você poderá atribuir o domínio para seu site do Azure por meio de um registro DNS A. 

É importante observar aqui que sites do Azure aceitam CNAME e registros A, no entanto você *deve* usar um registro A para habilitar o mapeamento de domínio correto. Um CNAME não pode ser encaminhado para outro CNAME, que é o que o Azure criou para você com YOUR_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, volte para o Gerenciador de DNS e configurar o registro para apontar para esse IP.


## Instalar e configurar o plug-in

O multissite WordPress atualmente não tem um método para mapear domínios personalizados. No entanto, há um plug-in chamado [mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] que adiciona a funcionalidade para você. Faça logon na parte de administrador de rede do seu site e instale o plug-in **Mapeamento de domínio do WordPress MU**.

Após instalar e ativar o plug-in, acesse **Configurações** > **Mapeamento de domínio** para configurar o plug-in. Na primeira caixa de texto, *endereço IP do servidor*, insira o endereço IP que você usou para configurar o registro A para o domínio. Defina quaisquer *opções de domínio* que desejar (geralmente são usados os padrões) e clique em **Salvar**.

## Mapa de domínio

Acesse o **Painel** para o site que deseja mapear o domínio. Clique em **Ferramentas** > **Mapeamento de domínio** e digite o novo domínio na caixa de texto e clique em **Adicionar**.

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você deseja ter todo o tráfego enviado para o novo domínio, selecione a caixa *domínio primário para esse blog* antes de salvar. Você pode adicionar um número ilimitado de domínios em um site, mas apenas um pode ser o principal.

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

