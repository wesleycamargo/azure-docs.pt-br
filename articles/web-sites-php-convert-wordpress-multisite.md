<properties linkid="develop-php-tutorials-convert-wordpress-to-multisite" urlDisplayName="Convert a WordPress Site to a Multisite" pageTitle="Convert a WordPress Site to a Multisite" metaKeywords="WordPress, Multisite" description="Learn how to take an existing WordPress website created through the gallery in Azure and convert it to WordPress Multisite" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Convert a WordPress Site to a Multisite" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Converter um site do WordPress em um Multissite

*Por [Ben Lobaugh][Ben Lobaugh], [Microsoft Open Technologies Inc.][Microsoft Open Technologies Inc.]*

Neste tutorial, você aprenderá como tirar um site de web do WordPress existente criado por meio da galeria no Azure e converter em um multissite WordPress instalar. Além disso, você aprenderá a atribuir um domínio personalizado para cada um dos subsites dentro de sua instalação.

Presume-se que você tem uma instalação existente do WordPress. Se você não fizer isso, siga as orientações apresentadas no [criar um site do WordPress da galeria no Azure][criar um site do WordPress da galeria no Azure]

Convertendo um WordPress existente instalar único site multissite geralmente é bastante simple e muitas das etapas iniciais são provenientes diretamente o [criar uma rede][criar uma rede] página no [WordPress Codex][WordPress Codex]

Vamos começar.

## Permitir que o multissite

Primeiro você precisa habilitar o Multisite por meio do arquivo `wp-config.php` com a constante **WP\_ALLOW\_MULTISITE**. Há dois métodos para editar seus arquivos do site: o primeiro é por meio do FTP, e o segundo por meio do Git. Se você não estiver familiarizado com a instalação de qualquer um desses métodos, consulte os seguintes tutoriais:

-   [Site do PHP com o MySQL e FTP][Site do PHP com o MySQL e FTP]

-   [Site do PHP com o MySQL e Git][Site do PHP com o MySQL e Git]

Abra o arquivo `wp-config.php` com o editor da sua escolha e adicione o seguinte acima da linha `/* That's all, stop editing! Happy blogging. */`.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvá-lo e carregá-lo de volta para o servidor!

## Configuração de rede

Efetuar login na *wp-admin* área de seu site e você verá um novo item sob o **ferramentas** menu chamado **o programa de instalação de rede** Clique em **o programa de instalação de rede** e preencha os detalhes da sua rede.

![Tela de configuração de rede][Tela de configuração de rede]

Este tutorial usa a *subdiretórios* site esquema porque ele sempre deve funcionar, e vão configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível para um subdomínio instalar se você mapear um domínio por meio de curinga o Portal e a configuração DNS corretamente a instalação.

Para obter mais informações sobre o subdomínio vs subpasta configurações Consulte o [tipos de rede multissite][tipos de rede multissite] artigo sobre o WordPress Codex.

## Ativar a rede

A rede agora está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalizar as configurações `wp-config.php` e certifique-se que o `web.config` roteie corretamente a cada site.

Depois de clicar o botão **instalar** na página *Instalação da rede*, WordPress tentará atualizar os arquivos `wp-config.php` e `web.config`. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Editar e salvar os arquivos.

Depois de fazer essas atualizações, você precisa fazer logoff e re-login ao painel wp-admin.

Agora deve haver um menu adicional na barra de admin de rotulado **Meus Sites** Esse menu permite que você controle sua nova rede através do **Admin de rede** painel de controle.

# Adicionando domínios personalizados

O [mapeamento de domínio do WordPress MU][mapeamento de domínio do WordPress MU] plug-in facilita adicionar domínios personalizados para qualquer site na sua rede. O plug-in funcionar corretamente, é necessário fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

## Habilitar o mapeamento de domínio para o site

O modo de site gratuito padrão não suporta a adição de domínios personalizados para sites Azure. Você precisará alternar para o modo padrão ou compartilhado. Para fazer isso:

-   Efetuar login no Portal do Azure e localize seu site da web.
-   Clique no **escala** guia na área de conteúdo principal
-   Em **geral**, selecione *compartilhado* ou *padrão*
-   Clique em **Salvar**

Você pode receber uma mensagem pedindo-lhe para verificar a alteração e confirmar o seu site da web agora podem incorrer em custos, dependendo do uso e outra configuração definida por você.

Demora alguns segundos para processar as novas configurações, agora é um bom momento para iniciar a configuração do domínio.

## Verifique se seu domínio

Antes de Sites do Azure permite mapear um domínio para o site, você precisa verificar se você tem autorização para mapear o domínio. Para fazer isso, você deve adicionar um novo registro CNAME para sua entrada DNS.

-   Efetuar login no Gerenciador de DNS do seu domínio
-   Criar um novo CNAME *awverify*
-   Aponte a *awverify* em *awverify.YOUR\_DOMAIN.azurewebsites.net*

Levará algum tempo para que as alterações DNS entrar em vigor, portanto, se estas etapas não funcionar imediatamente, uma xícara de café, e em seguida, volte e tente novamente.

## Adicionar o domínio para seu site

Retornar ao site da web por meio do Portal do Azure e clique neste momento o **configurar** guia. O **gerenciar domínios** botão deve estar disponível. Clique.

O *gerenciar domínios personalizados* diálogo Mostrar pop-up. Isso é onde você digitará todos os domínios que você deseja atribuir ao seu site. Se um domínio não estiver listado aqui, ela não estará disponível para mapeamento dentro de WordPress, independentemente de como o domínio DNS é o programa de instalação.

![Caixa de diálogo domínios personalizados gerenciar][Caixa de diálogo domínios personalizados gerenciar]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o *awverify* registro CNAME criado anteriormente. Se o DNS não totalmente tem propigated, um indicador vermelho será exibido. Se tiver êxito, você verá uma marca de seleção verde.

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro para o seu domínio.

## Configurar o domínio de um registro

Se as outras etapas forem bem-sucedidas, agora você pode atribuir o domínio para seu site Azure por meio de um registro DNS A.

É importante observar aqui que Azure sites da web aceita CNAME e registros, no entanto você *deve* usar um registro para habilitar o mapeamento de domínio correto. Um CNAME não pode ser encaminhado para outro CNAME, que é o Azure criada para você com YOUR\_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, volte para o Gerenciador de DNS e configurar o registro para apontar para esse IP.

## Instalar e configurar o plug-in

Multissite WordPress atualmente não tem um método para mapear domínios personalizados. No entanto, há um plug-in chamado [mapeamento de domínio do WordPress MU][mapeamento de domínio do WordPress MU] que adiciona a funcionalidade para você. Login à parte de seu site do administrador de rede e instalar o **mapeamento de domínio do WordPress MU** plug-in.

Após instalar e ativar o plug-in, visite **configurações** \> **mapeamento de domínio** para configurar o plug-in. Na primeira caixa de texto, *endereço IP do servidor*, insira o endereço de IP é usada para configurar o registro para o domínio. Defina qualquer *opções de domínio* você desejo (os padrões geralmente são bem) e clique em **salvar**

## Mapa de domínio

Visite o **painel** para o site que deseja mapear o domínio. Clique em **ferramentas** \> **mapeamento de domínio** e digite o novo domínio na caixa de texto e clique em **Add**

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você deseja ter todo o tráfego enviado para o novo domínio, seleção de *o domínio primário para este blog* caixa antes de salvar. Você pode adicionar um número ilimitado de domínios em um site, mas apenas um pode ser a principal.

## Fazê-lo novamente

Azure Sites da Web permite que você adicione um número ilimitado de domínios para um site da web. Para adicionar outro domínio, você precisará executar o **Verifique se seu domínio** e **o domínio de um registro de instalação** seções para cada domínio.

  [Ben Lobaugh]: http://ben.lobaugh.net
  [Microsoft Open Technologies Inc.]: http://msopentech.com
  [criar um site do WordPress da galeria no Azure]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-from-gallery/
  [criar uma rede]: http://codex.wordpress.org/Create_A_Network
  [WordPress Codex]: http://codex.wordpress.org
  [Site do PHP com o MySQL e FTP]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
  [Site do PHP com o MySQL e Git]: https://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-git/#header-1
  [Tela de configuração de rede]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
  [tipos de rede multissite]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
  [mapeamento de domínio do WordPress MU]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/
  [Caixa de diálogo domínios personalizados gerenciar]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png
