---
title: "Converter WordPress em Multissite no Serviço de Aplicativo do Azure"
description: "Saiba como selecionar um aplicativo Web WordPress existente criado por meio da galeria no Azure e convertê-lo em Multissite WordPress"
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: fe52dbf4-179c-42f1-adf9-d6a9af920c39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 45a5c8f16dd70f65967907c18752f4f98ffa75ea


---
# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Converter WordPress em Multissite no Serviço de Aplicativo do Azure
## <a name="overview"></a>Visão geral
*Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Neste tutorial, você aprenderá converter um aplicativo Web do WordPress criado por meio da galeria no Azure em uma instalação multissite do WordPress. Além disso, você aprenderá a atribuir um domínio personalizado para cada um dos subsites dentro de sua instalação.

Presume-se que você tem uma instalação existente do WordPress. Se você não fizer isso, siga as orientações apresentadas no [criar um site do WordPress da galeria no Azure][website-from-gallery].

Convertendo um WordPress existente instalar único site multissite geralmente é bastante simple e muitas das etapas iniciais são provenientes diretamente o [Criar uma rede][wordpress-codex-create-a-network] página no [WordPress Codex](http://codex.wordpress.org).

Vamos começar.

## <a name="allow-multisite"></a>Permitir que o multissite
Primeiro você precisa habilitar o Multissite por meio do arquivo `wp-config.php` com a constante **WP\_ALLOW\_MULTISITE**. Há dois métodos para editar os arquivos do seu aplicativo Web: a primeira é por meio de FTP e o segundo é por meio do Git. Se você não estiver familiarizado com a instalação de qualquer um desses métodos, consulte os seguintes tutoriais:

* [Site do PHP com o MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]
* [Site do PHP com o MySQL e Git][website-w-mysql-and-git-git-setup]

Abra o arquivo `wp-config.php` com o editor da sua escolha e adicione o seguinte acima da linha `/* That's all, stop editing! Happy blogging. */`.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvá-lo e carregá-lo de volta para o servidor!

## <a name="network-setup"></a>Configuração de rede
Faça logon na área *wp-admin* de seu aplicativo Web e você verá um novo item no menu **Ferramentas**, chamado **Configuração de Rede**. Clique em **o programa de instalação de rede** e preencha os detalhes da sua rede.

![Tela de configuração de rede][wordpress-network-setup]

Este tutorial usa a *subdiretórios* site esquema porque ele sempre deve funcionar, e vão configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível configurar uma instalação de subdomínio se você mapear adequadamente um domínio por meio do [Portal do Azure](https://portal.azure.com) e do DNS curinga de configuração.

Para obter mais informações sobre o subdomínio vs subpasta configurações Consulte o [Tipos de rede multissite][wordpress-codex-types-of-networks] artigo sobre o WordPress Codex.

## <a name="enable-the-network"></a>Ativar a rede
A rede agora está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalize as configurações `wp-config.php` e verifique se `web.config` roteia corretamente cada site.

Depois que você clicar no botão **Instalar** na página *Configuração de Rede*, o WordPress tentará atualizar os arquivos `wp-config.php` e `web.config`. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Editar e salvar os arquivos.

Depois de fazer essas atualizações, você precisará fazer logoff e depois fazer logon no painel wp-admin.

Agora deve haver um menu adicional na barra de administrador com o rótulo **Meus Sites**. Esse menu permite que você controle sua nova rede através do painel **Administrador de rede**.

## <a name="adding-custom-domains"></a>Adicionando domínios personalizados
O [Mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] plug-in facilita adicionar domínios personalizados para qualquer site na sua rede. O plug-in funcionar corretamente, é necessário fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

## <a name="enable-domain-mapping-to-the-web-app"></a>Habilitar o mapeamento de domínio para o aplicativo Web
O **Gratuito** [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) não dá suporte a adição de domínios personalizados para aplicativos Web. Você precisará mudar para o modo **Compartilhado** ou **Padrão**. Para fazer isso:

* Faça logon no Portal do Azure e localize seu aplicativo Web. 
* Clique na guia **Escalar verticalmente** em **Configurações**.
* Em **Geral**, selecione *COMPARTILHADO* ou *STANDARD*
* Clique em **Salvar**

Você pode receber uma mensagem pedindo-lhe para verificar a alteração e confirmar que o seu aplicativo Web agora pode incorrer em custos, dependendo do uso e de outras configurações definidas por você.

Demora alguns segundos para processar as novas configurações, agora é um bom momento para iniciar a configuração do domínio.

## <a name="verify-your-domain"></a>Verifique se seu domínio
Para que os Aplicativos Web do Azure lhe permitam mapear um domínio para o site, primeiro você precisa verificar se tem autorização para mapear tal domínio. Para fazer isso, você deve adicionar um novo registro CNAME para sua entrada DNS.

* Efetuar login no Gerenciador de DNS do seu domínio
* Criar um novo CNAME *awverify*
* Aponte o *awverify* para *awverify.YOUR_DOMAIN.azurewebsites.net*

Levará algum tempo para que as alterações DNS entrar em vigor, portanto, se estas etapas não funcionar imediatamente, uma xícara de café, e em seguida, volte e tente novamente.

## <a name="add-the-domain-to-the-web-app"></a>Adicionar o nome de domínio ao aplicativo Web
Voltar ao seu aplicativo Web por meio do Portal do Azure, clique em **Configurações** e, em seguida, clique em **Domínios personalizados e SSL**.

Quando as *Configurações de SSL* forem exibidas, você verá os campos onde digitará todos os domínios que você deseja atribuir ao seu aplicativo Web. Se um domínio não estiver listado aqui, ela não estará disponível para mapeamento dentro de WordPress, independentemente de como o domínio DNS é o programa de instalação.

![Caixa de diálogo domínios personalizados gerenciar][wordpress-manage-domains]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o registro CNAME que você criou anteriormente. Se o DNS não tiver sido propagado totalmente, um indicador vermelho será exibido. Se tiver êxito, você verá uma marca de seleção verde. 

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro para o seu domínio.

## <a name="setup-the-domain-a-record"></a>Configurar o domínio de um registro
Se as outras etapas forem bem-sucedidas, agora você pode atribuir o domínio a seu aplicativo Web do Azure por meio de um registro A DNS. 

É importante observar aqui que aplicativos Web do Azure aceitam registros CNAME e A; no entanto, você *deve* usar um registro para habilitar o mapeamento de domínio correto. Um CNAME não pode ser encaminhado para outro CNAME, que é o Azure criada para você com YOUR_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, volte para o Gerenciador de DNS e configurar o registro para apontar para esse IP.

## <a name="install-and-setup-the-plugin"></a>Instalar e configurar o plug-in
Atualmente, o multissite do WordPress não tem um método interno para mapear domínios personalizados. No entanto, há um plug-in chamado [Mapeamento de domínio do WordPress MU][wordpress-plugin-wordpress-mu-domain-mapping] que adiciona a funcionalidade para você. Login à parte de seu site do administrador de rede e instalar o **mapeamento de domínio do WordPress MU** plug-in.

Após instalar e ativar o plug-in, visite **Configurações** > **mapeamento de domínio** para configurar o plug-in. Na primeira caixa de texto, *endereço IP do servidor*, insira o endereço de IP é usada para configurar o registro para o domínio. Defina qualquer *opções de domínio* você desejo (os padrões geralmente são bem) e clique em **salvar**

## <a name="map-the-domain"></a>Mapa de domínio
Visite o **painel** para o site que deseja mapear o domínio. Clique em **Ferramentas** > **Mapeamento de domínio** e digite o novo domínio na caixa de texto e clique em **Adicionar**.

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você deseja ter todo o tráfego enviado para o novo domínio, seleção de *o domínio primário para este blog* caixa antes de salvar. Você pode adicionar um número ilimitado de domínios em um site, mas apenas um pode ser o principal.

## <a name="do-it-again"></a>Fazê-lo novamente
Aplicativos Web do Azure permitem que você adicione um número ilimitado de domínios a um aplicativo Web. Para adicionar outro domínio, você precisará executar as seções **Verifique se seu domínio** e **Configurar registro do domínio A** para cada domínio.    

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

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





<!--HONumber=Jan17_HO3-->


