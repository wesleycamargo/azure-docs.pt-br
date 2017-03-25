---
title: "Comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure"
description: "Saiba como comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: .net
author: apurvajo
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: edcb6d37eb4d82ff5928ee33cf456c3795eb8131
ms.lasthandoff: 03/10/2017


---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure
> [!div class="op_single_selector"]
> * [Comprar certificado SSL no Azure](web-sites-purchase-ssl-web-site.md)
> * [Usar certificado SSL de outro lugar](web-sites-configure-ssl-certificate.md)
> 
> 

Por padrão, o **[Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** já habilita o HTTPS para seu aplicativo Web com um certificado curinga para o *domínio .azurewebsites.net. Se não planeja configurar um domínio personalizado, você pode aproveitar o certificado HTTPS padrão. No entanto, assim como todos os*[domínios curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), ele não é tão seguro quanto usar um domínio personalizado com seu próprio certificado. Agora, o Serviço de Aplicativo do Azure fornece uma maneira realmente simplificada de comprar e gerenciar um certificado SSL diretamente do Portal do Azure, sem precisar sair do portal.  
Este artigo explica como comprar e configurar um certificado SSL para o **[Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** em três etapas simples. 

> [!NOTE]
> Os certificados SSL para nomes de domínio personalizados não podem ser usados com um aplicativo Web Gratuito e Compartilhado. É preciso que você configure seu aplicativo Web para o modo Basic, Standard ou Premium, o que pode alterar o quanto você é cobrado por sua assinatura. Confira **[Detalhes de preços dos aplicativos Web](https://azure.microsoft.com/pricing/details/web-sites/)** para saber mais.
> 
> 

## <a name="bkmk_Overview"></a>Visão geral
> [!NOTE]
> Não tente adquirir um certificado SSL usando uma assinatura que não tenha um cartão de crédito ativo associado a ela. Isso pode resultar na desabilitação de sua assinatura. 
> 
> 

## <a name="a-purchase-store-and-assign-an-ssl-certificate-for-your-custom-domain-a"></a><a> Comprar, armazenar e atribuir um certificado SSL para seu domínio personalizado </a>
Para habilitar HTTPS para um domínio personalizado, como contoso.com, primeiramente você deve **[configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](web-sites-custom-domain-name.md)**

Antes de solicitar um certificado SSL, você deve primeiro determinar quais nomes de domínio serão protegidos pelo certificado. Isso determinará o tipo de certificado que você precisa ter. Se você precisar apenas garantir um único nome de domínio, como contoso.com ou www.contoso.com, um certificado Standard (básico) será suficiente. Se precisar proteger vários nomes de domínio, como contoso.com, www.contoso.com e mail.contoso.com, você poderá obter um **[certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate)**

## <a name="bkmk_purchasecert"></a>Etapa 0: Fazer um pedido de certificado SSL
Nesta Etapa, você aprenderá a fazer um pedido de um certificado SSL de sua escolha.

1. No **[portal do Azure](https://portal.azure.com/)**, clique em Procurar, digite "Certificados do Serviço de Aplicativo" na barra de pesquisa, selecione "Certificados do Serviço de Aplicativo" no resultado e clique em Adicionar. 
   
   ![inserir imagem de criação usando procurar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
   ![inserir imagem de criação usando procurar](./media/app-service-web-purchase-ssl-web-site/add.jpg)
2. Insira o **nome amigável** de seu certificado SSL.
3. Insira o **Nome do Host**
   
   > [!NOTE]
   > Essa é uma das partes mais importantes do processo de compra. Digite corretamente o nome do host (domínio personalizado) que você deseja proteger com esse certificado. **NÃO** acrescente WWW ao nome do host. Por exemplo, se o seu nome de domínio personalizado for www.contoso.com, basta digitar contoso.com no campo Nome do Host, o certificado em questão protegerá os domínios raiz e www. 
   > 
   > 
4. Selecione sua **assinatura**. 
   
   Se você tiver várias assinaturas, crie um Certificado SSL na mesma assinatura que você usou para seu domínio personalizado ou um aplicativo Web em questão.
5. Selecione ou crie um **Grupo de Recursos**.
   
   Os grupos de recursos permitem gerenciar recursos do Azure relacionados como uma unidade e são úteis durante o estabelecimento de regras de controle de acesso baseado em função (RBAC) para seus aplicativos. Para saber mais, consulte Gerenciar seus recursos do Azure.
6. Selecione o **SKU do certificado** 
   
   Por fim, selecione o SKU do certificado que atenda às suas necessidades e clique em Criar. Atualmente, o Serviço de Aplicativo do Azure permite a compra de dois SKUs diferentes
   
        •    S1 – Standard Certificate with 1-year validity and auto renewal  
        •    W1 – Wild card Certificate with 1-year validity and auto renewal      
   Confira **[Detalhes de preços dos aplicativos Web](https://azure.microsoft.com/pricing/details/web-sites/)** para saber mais.

![inserir imagem do SKU do certificado](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [!NOTE]
> A criação do Certificado SSL levará de um a 10 minutos. Esse processo executa várias etapas em segundo plano que seriam muito difíceis de realizar manualmente.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>Etapa 1: Armazenar o certificado no Cofre da Chave do Azure
Nessa Etapa, você aprenderá a colocar em armazenamento um Certificado SSL que você adquiriu no Cofre da Chave do Azure de sua escolha.

1. Após a conclusão da compra do Certificado SSL, você precisará abrir manualmente a folha Recursos dos **Certificados de Serviço de Aplicativo** navegando até ela novamente (confira a Etapa 1 acima)   
   
   ![inserir imagem de pronto para armazenar no KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Você perceberá que o status do certificado é **"Emissão Pendente"** , pois há mais algumas etapas que precisam ser concluídas antes de começar a usar esses certificados.
2. Clique em **"Configuração do Certificado"** na folha Propriedades do Certificado e clique em **"Etapa 1: Armazenar"** para armazenar esse certificado no Cofre de Chaves do Azure.
3. Na folha **"Status do Cofre de Chaves"**, clique em **"Repositório do Cofre de Chaves"** para escolher um Cofre de Chaves existente a fim de armazenar esse certificado **OU "Criar Novo Cofre de Chaves"** para criar um novo Cofre de Chaves no mesmo grupo de recursos e na mesma assinatura.
   
   ![inserir imagem de criação de novo KV](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > O Cofre da Chave do Azure tem custos bem mínimos para armazenar esse certificado. Veja **[Detalhes de preços do Cofre de Chaves do Azure](https://azure.microsoft.com/pricing/details/key-vault/)** para obter mais informações.
   > 
   > 
4. Depois de selecionar o Repositório do Cofre de Chaves para armazenar esse certificado, armazene-o clicando no botão **"Armazenar"** na parte superior da folha **"Status do Cofre de Chaves"**.  
   
    Isso deve concluir a etapa de armazenamento do certificado que você adquiriu com o Cofre da Chave do Azure de sua escolha. Após atualizar a folha, você também deverá ver a marca de verificação verde nessa etapa.

## <a name="bkmk_VerifyOwnership"></a>Etapa 2: Verificar a propriedade do domínio
Nesta etapa, você aprenderá a realizar a Verificação de Propriedade de Domínio de um Certificado SSL para o qual você acabou de fazer um pedido. 

1. Clique em **"Etapa 2: Verificar"** na folha **"Configuração de Certificado"**. Há três tipos de Verificação de domínio com suporte dos Certificados do Serviço de Aplicativo.
   
   * **Verificação de domínio** 
     
     * Este será o processo mais conveniente **SOMENTE SE** você tiver **[adquirido seu domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)**
     * Clique no botão **"Verificar"** para concluir esta etapa.
     * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
   * **Verificação por email**
     
     * Um email de verificação já foi enviado para os endereços de email associados a esse domínio personalizado.
     * Abra o email e clique no link de verificação para concluir a etapa Verificação por email. 
     * Se você precisar reenviar o email de verificação, clique no botão **"Reenviar Email"** .
   * **Verificação manual**    
     
      **Verificação da página da Web em HTML (funciona somente com SKU de Certificado Padrão)**

        * Crie um arquivo HTML chamado **"starfield.html"**
        * O conteúdo desse arquivo deve ter exatamente o mesmo nome do Token de Verificação de Domínio. (Você pode copiar o token na folha Status de Verificação de Domínio)
        * Carregue esse arquivo na raiz do servidor Web que hospeda seu domínio **/.well-known/pki-validation/starfield.html**
        * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
          
          Por exemplo, se você estiver comprando um certificado padrão para **contosocertdemo.com** com Token de Verificação de Domínio **tgjgthq8d11ttaeah97s3fr2sh**, uma solicitação da Web feita para **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** deverá retornar **tgjgthq8d11ttaeah97s3fr2sh**.

      **Verificação de registro TXT do DNS**
        
        * Usando o gerenciador de DNS, crie um registro TXT no subdomínio **'@'** com um valor igual ao **Token de Verificação de Domínio.**
        * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
          
          Por exemplo, para executar a validação de um certificado curinga com o nome de host **\*.contosocertdemo.com** ou **\*.subdomain.contosocertdemo.com** e o Token de Verificação de Domínio **tgjgthq8d11ttaeah97s3fr2sh**, você precisa criar um registro TXT em **contosocertdemo.com** com o valor **tgjgthq8d11ttaeah97s3fr2sh**     

## <a name="bkmk_AssignCertificate"></a>Etapa 3: Atribuir o certificado ao Aplicativo do Serviço de Aplicativo
Nesta Etapa, você aprenderá a atribuir esse certificado recém-adquirido para seus Aplicativos do Serviço de Aplicativo. 

> [!NOTE]
> Antes de executar as etapas nesta seção, você precisa ter associado um nome de domínio personalizado ao seu aplicativo. Para saber mais, confira **[Configurando um nome de domínio personalizado para um aplicativo Web.](web-sites-custom-domain-name.md)**
> 
> 

1. No seu navegador, abra o **[portal do Azure.](https://portal.azure.com/)**
2. Clique na opção **Serviço de Aplicativo** no lado esquerdo da página.
3. Clique no nome do aplicativo ao qual você deseja atribuir a esse certificado. 
4. Em **Configurações**, clique em **Certificados SSL**
5. Clique em **Importar Certificado do Serviço de Aplicativo** e selecione o certificado que você acabou de adquirir
   
   ![inserir imagem de Importar Certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)
6. Na seção **associações ssl**, clique em **Adicionar associações**
7. Na folha **Adicionar Associação SSL** , use os menus suspensos para selecionar o nome de domínio a ser protegido com SSL e o certificado a ser usado. Você também pode selecionar se deseja usar **[SNI (Indicação de Nome de Servidor)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseado em IP.
   
    ![inserir imagem de Associações SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
8. Clique em **Adicionar Associação** para salvar as alterações e habilitar o SSL.

Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá executar as seguintes etapas adicionais:

* Depois de ter configurado uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Encontre esse endereço IP na página **Domínio personalizado** nas configurações do aplicativo, logo acima da seção **Nomes de host**. Ele estará listado como **Endereço IP Externo**
  
    ![inserir imagem de IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
  
    Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI, ou não estiver configurado para usar SSL, nenhum endereço será listado para essa entrada.

* Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior.
   Nesse momento, você poderá visitar o seu aplicativo usando HTTPS:// em vez de HTTP:// para verificar se o certificado foi configurado corretamente.

## <a name="bkmk_Rekey"></a>Exportar Certificado do Serviço de Aplicativo
Você pode criar uma cópia local do PFX de um Certificado do Serviço de Aplicativo para que você possa usá-lo com outros Serviços do Azure. Para obter mais informações, **[leia nossa postagem no blog](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)**

## <a name="bkmk_Rekey"></a>Criar novas chaves e sincronizar o certificado
1. Por motivos de segurança, se você precisar chavear novamente seu certificado, basta selecionar a opção **"Rechaveamento e Sincronização"** na folha **"Propriedades do Certificado"**. 
2. Clique no botão **"Criar Nova Chave"** para iniciar o processo. Esse processo pode demorar de um a 10 minutos para ser concluído. 
   
    ![inserir imagem de Nova Chave SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)
3. A criação de uma nova chave para o certificado causará a emissão de um novo certificado pela autoridade de certificação.
4. Você não será cobrado pela criação da nova chave durante o tempo de vida do certificado. 
5. A criação de uma nova chave para o certificado passará pelo estado de Emissão Pendente. 
6. Quando o certificado estiver pronto, sincronize seus recursos usando esse certificado para evitar interrupções no serviço.
7. A opção de sincronização não está disponível para certificados que ainda não foram atribuídos ao aplicativo Web. 

## <a name="more-resources"></a>Mais Recursos
* [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure](web-sites-configure-ssl-certificate.md)
* [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)
* [Central de confiabilidade do Microsoft Azure](/support/trust-center/security/)
* [Opções de configuração desbloqueadas nos Sites do Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 


