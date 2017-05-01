---
title: "Adicionar um certificado SSL ao seu aplicativo de serviço de aplicativo do Azure | Microsoft Docs"
description: "Saiba como adicionar um certificado SSL ao seu aplicativo de serviço de aplicativo."
services: app-service
documentationcenter: .net
author: ahmedelnably
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
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>Adicionar um certificado SSL ao seu aplicativo de serviço de aplicativo
> [!div class="op_single_selector"]
> * [Comprar um certificado SSL no Azure](web-sites-purchase-ssl-web-site.md)
> * [Usar o certificado SSL de qualquer outro lugar](web-sites-configure-ssl-certificate.md)
> 
> 

Por padrão, o [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) habilita o HTTPS para seu aplicativo Web com um certificado curinga para o domínio \*.azurewebsites.net. Se não planeja configurar um domínio personalizado, você pode aproveitar o certificado HTTPS padrão. No entanto, assim como todos os [domínios curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), o certificado curinga do Azure não é tão seguro quanto usar um domínio personalizado com seu próprio certificado.

Serviço de aplicativo fornece uma maneira simplificada de comprar e gerenciar um certificado SSL no portal do Azure. 

Este artigo explica como comprar e configurar um certificado SSL para o [o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) aplicativo. 

> [!NOTE]
> É possível usar certificados SSL para nomes de domínio personalizado para aplicativos hospedados em um plano do Serviço de Aplicativo compartilhado gratuito. Para usar certificados SSL, seu aplicativo Web deve ser hospedado em um plano do Serviço de Aplicativo Básico, Standard ou Premium. Alterar o tipo de assinatura pode alterar o quanto você será cobrado por sua assinatura. Para saber mais, veja [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).
> 
> 

> [!WARNING]
> Não tente adquirir um certificado SSL usando uma assinatura para o qual você não tiver conectado um cartão de crédito válido. Isso pode causar a sua assinatura será desabilitada. 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para habilitar HTTPS para um domínio personalizado, comece [mapear um nome de domínio personalizado para seu aplicativo do Azure](web-sites-custom-domain-name.md).

Antes de solicitar um certificado SSL, primeiro determine quais nomes de domínio que você protegerá com o certificado. Isso determina o tipo de certificado que você precisa. Se você deseja proteger um único nome de domínio, como contoso.com *ou* www.contoso.com, você pode usar um certificado (básico) padrão. Se precisar proteger vários nomes de domínio, como contoso.com, www.contoso.com *e* mail.contoso.com, você poderá obter um [certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate).

## <a name="bkmk_purchasecert"></a>Comprar um certificado SSL

1. No [portal do Azure](https://portal.azure.com/), no menu, selecione **procurar**. No **pesquisa** , digite **certificado do serviço de aplicativo**. Nos resultados da pesquisa, selecione **certificados de serviço de aplicativo**. 

   ![Criar usando Procurar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. Sobre o **certificados de serviço de aplicativo** página, selecione **adicionar**. 

   ![Adicionar um certificado](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. Insira um **nome** para seu certificado SSL.
4. Insira o **nome do host**.
   
   > [!WARNING]
   > Essa é uma das partes mais importantes do processo de compra. Certifique-se de que você insira o nome de host correto (nome de domínio personalizado) que você deseja proteger esse certificado. *Não* adicionar "www" para o início do nome do host. Por exemplo, se o nome de domínio personalizado for www.contoso.com, digite **contoso.com** no **nome do Host** caixa. O certificado protegerá os domínios raiz e www. 
   > 

5. Selecione sua **assinatura**. 
   
   Se você tiver várias assinaturas, crie o certificado SSL na mesma assinatura que você usa para seu domínio personalizado ou um aplicativo Web.

6. Selecione ou crie um **Grupo de Recursos**.
   
   Você pode usar grupos de recursos para gerenciar recursos relacionados do Azure como uma unidade. Grupos de recursos são úteis quando você deseja estabelecer regras de controle de acesso baseado em função (RBAC) para seus aplicativos. Para saber mais, consulte Gerenciar seus recursos do Azure.

7. Selecione o **SKU do certificado**. 
   
   Selecione o SKU do certificado que atenda às suas necessidades e então selecione **Criar**. 
   
   Você pode escolher entre duas SKUS no serviço de aplicativo:
   * **S1**: certificado padrão com renovação automática e de validade de um ano  
   * **W1**: certificado curinga com renovação automática e de validade de um ano       
  
    ![Certificado SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    Para saber mais, veja [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> Criar um certificado SSL pode levar até 10 minutos. O processo envolve várias etapas que ocorrem em segundo plano.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>Armazenar o certificado no Azure Key Vault

1. Quando você terminar a compra de certificado SSL, no portal do Azure, vá para o **certificados de serviço de aplicativo** folha.

   ![Pronto para armazenar no cofre da chave do certificado](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Observe que o status do certificado é **emissão pendente**. Você precisa executar algumas etapas antes de começar a usar este certificado.

2. Sobre o **propriedades do certificado** folha, selecione **certificado configuração**. Para armazenar o certificado no cofre da chave, selecione **etapa 1: armazenar**.
3. No **Status do Cofre de chave** folha, para selecionar um cofre de chave existente para armazenar esse certificado, selecione **chave cofre repositório**.  Para criar um novo cofre de chave na mesma assinatura e grupo de recursos, selecione **criar novo cofre de chave**.

   ![Criar novo cofre de chaves](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > O cofre da chave do Azure oferece encargos mínimo para armazenar o certificado. Para saber mais, veja [Preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
   > 
   > 

4. Depois de selecionar o repositório de Cofre de chaves para armazenar o certificado, na parte superior do **chave cofre Status** folha, selecione o **armazenar** botão.  
   
Para verificar sua seleção, você pode clicar em botão Atualizar do navegador. Uma marca de seleção verde mostra que esta etapa é concluída.

## <a name="bkmk_VerifyOwnership"></a>Verificar a propriedade do domínio

1. Sobre o **configuração do certificado** folha, selecione **etapa 2: Verifique se**.
2. Selecione opções de verificação usando as informações a seguir. 

Certificados de serviço de aplicativo oferecer suporte a três tipos de verificação de domínio:

   * Verificação de domínio
   * Verificação por email
   * Verificação manual

### <a name="domain-verification"></a>Verificação de domínio 
     
A verificação de domínio é o processo mais conveniente, mas *somente* se você tiver [adquirido seu domínio personalizado no Serviço de Aplicativo do Azure](custom-dns-web-site-buydomains-web-app.md).

1. Para concluir esta etapa, selecione **verificar**.
2. Para atualizar o status do certificado após a conclusão da verificação, selecione **Atualizar**. Pode demorar alguns minutos até a verificação ser concluída.

### <a name="mail-verification"></a>Verificação por email
     
Com um domínio personalizado, é enviado um email de verificação para o endereço de email associado ao domínio. 

1. Para concluir a etapa de verificação por email, abra o email e clique no link de verificação. 
2. Se você precisar reenviar o email de verificação, clique no botão **Reenviar Email**.

### <a name="manual-verification"></a>Verificação manual    
     
**Verificação da página da Web em HTML (funciona somente com SKU de certificado Standard)**

1. Crie um arquivo HTML chamado starfield.html. O conteúdo desse arquivo deve ser o nome exato do token de verificação de domínio. (Você pode copiar o token na folha **Status de Verificação de Domínio**).
2. Carregue esse arquivo para a raiz do servidor Web onde você hospeda o seu domínio. Por exemplo, /.well-known/pki-validation/starfield.html.
3.  Quando a verificação for concluída, para atualizar o status do certificado, selecione **atualização**. Pode demorar alguns minutos até a verificação ser concluída.

    Por exemplo, se você estiver comprando um certificado padrão para **contosocertdemo.com** com Token de Verificação de Domínio **tgjgthq8d11ttaeah97s3fr2sh**, uma solicitação da Web feita para **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** deverá retornar **tgjgthq8d11ttaeah97s3fr2sh**.

**Verificação de registro TXT do DNS**
        
1. Usando o gerenciador de DNS, crie um registro TXT no subdomínio **@** com um valor igual ao **token de verificação de domínio.**
2. Para atualizar o status do certificado quando a verificação for concluída, selecione **atualização**. Pode demorar alguns minutos até a verificação ser concluída.
 
   Por exemplo, para executar a validação de um certificado curinga com o nome de host **\*.contosocertdemo.com** ou **\*.subdomain.contosocertdemo.com** e o Token de Verificação de Domínio **tgjgthq8d11ttaeah97s3fr2sh**, crie um registro TXT em **contosocertdemo.com** com o valor **tgjgthq8d11ttaeah97s3fr2sh**.     

## <a name="bkmk_AssignCertificate"></a>Atribuir o certificado a um aplicativo de serviço de aplicativo

> [!NOTE]
> Antes de concluir as etapas nesta seção, você precisa ter associado um nome de domínio personalizado ao seu aplicativo. Para saber mais, confira [Configurar um nome de domínio personalizado para um aplicativo Web](web-sites-custom-domain-name.md).
> 
> 

1. No [portal do Azure](https://portal.azure.com/), no menu, selecione **o serviço de aplicativo**.
2. Selecione o nome do aplicativo que você deseja atribuir a esse certificado. 
3. Vá para **configurações** > **certificados SSL** > **importar o certificado do serviço de aplicativo**e, em seguida, selecione o certificado.

   ![Importar certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. Na seção **Associações SSL**, selecione **Adicionar associações**
5. Sobre o **Adicionar associação de SSL** folha, selecione o nome de domínio que você deseja proteger com o certificado SSL. Selecione o certificado que você deseja usar. Você também pode selecionar se deseja usar [SNI (Indicação de Nome de Servidor)](http://en.wikipedia.org/wiki/Server_Name_Indication) ou SSL baseado em IP.

   ![Associações SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * Para associar um certificado a um nome de domínio, o SSL baseado em IP é mpeia um endereço IP público dedicado do servidor para o nome de domínio. Quando você usa o SSL com base em IP, cada nome de domínio (por exemplo, contoso.com ou fabricam.com) associado ao seu serviço deve ter um endereço IP dedicado. Este é o método tradicional de associar certificados SSL a um servidor Web.
    * SSL baseado em SNI é uma extensão para SSL e [Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS). Quando você usa o SSL baseado em SNI, vários domínios podem compartilhar o mesmo endereço IP. Cada domínio possui um certificado de segurança separadas. Suportam a navegadores mais modernos, incluindo o Internet Explorer, Chrome, Firefox e Opera, SNI. Navegadores mais antigos podem não suportar SNI. Para saber mais sobre SNI, veja [Indicação de Nome de Servidor](http://en.wikipedia.org/wiki/Server_Name_Indication) na Wikipédia.

6. Para salvar suas alterações e habilitar o SSL, selecione **Adicionar associação**.

Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá concluir as seguintes etapas adicionais:

1.  Depois de configurar uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Para localizar o endereço IP, vá para **configurações** > **domínio personalizado**. Logo acima do **nomes de host** seção, seu endereço IP é listado como **endereço IP externo**.

   ![SSL baseado em IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  Observe que esse endereço IP é diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se seu aplicativo é configurado para usar SSL baseado em SNI, ou se ele não foi configurado para usar SSL, nenhum endereço IP está listado aqui.

2.  Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP usado na etapa anterior.

3.  Para verificar se o certificado foi configurado corretamente, vá para seu aplicativo usando HTTPS:// em vez de HTTP://.

## <a name="bkmk_Export"></a>Exportar Certificado do Serviço de Aplicativo
Você pode criar uma cópia local do PFX de um certificado do serviço de aplicativo. Quando você tem uma cópia local, você pode usar o certificado com outros serviços do Azure. Para saber mais, veja nossa postagem de blog [Criar uma cópia local do PFX de seu certificado do Serviço de Aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

## <a name="bkmk_Renew"></a>Renovar automaticamente seu certificado do Serviço de Aplicativo
Para definir as configurações de renovação automática do seu certificado ou para renovar manualmente o certificado, na folha **Propriedades do Certificado**, selecione **Configurações da Renovação Automática**. 

![Configurações de renovação automática](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

Para renovar o certificado automaticamente antes de expirar, defina **renovar automaticamente** para **em**. Essa é a opção padrão. Se a renovação automática está ativada, podemos tentar renovar seu certificado iniciando no dia 90º antes da expiração do certificado. Se você criou associações SSL em seus aplicativos de serviço de aplicativo no portal do Azure, as associações também são atualizadas quando o novo certificado está pronto (como em cenários de recriação de chaves e sincronização). 

Se você quiser manipular renovações manualmente, defina **renovar automaticamente** para **Off**. Você só poderá renovar manualmente um certificado do Serviço de Aplicativo quando a expiração for em até 90 dias.

## <a name="bkmk_Rekey"></a>Criar novas chaves e seu certificado de sincronização

1. Se você precisar refazer a chave do seu certificado (por questões de segurança), na folha **Propriedades do Certificado**, selecione **Refazer chave e Sincronizar**. 
2. Selecione **Rechaveamento**. O processo poderá levar até 10 a 20 minutos para terminar. 

   ![Rechaveamento SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

Eis algumas informações adicionais sobre a recriação de chaves:

* O certificado de troca de chaves acumula o certificado com um novo certificado. O novo certificado é emitido da autoridade de certificação.
* Você não é cobrado para troca de chaves para o tempo de vida do certificado. 
* O certificado de troca de chaves oferece o certificado de uma **emissão pendente** status. 
* Quando o certificado estiver pronto, para impedir a interrupção do serviço, sincronize seus recursos usando o certificado.
* A opção de sincronização não está disponível para certificados que ainda não foram atribuídos a um aplicativo Web. 

## <a name="next-steps"></a>Próximas etapas

* [Proteger o domínio personalizado de seu aplicativo com HTTPS](web-sites-configure-ssl-certificate.md)
* [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)
* [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> Para obter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/). Você pode criar um aplicativo de Web inicial de curta duração, no serviço de aplicativo. Nenhum cartão de crédito é exigido e não há compromissos.
> 
> 

