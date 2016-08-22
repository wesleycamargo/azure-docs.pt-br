<properties
	pageTitle="Comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure"
	description="Saiba como comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure."
	services="app-service"
	documentationCenter=".net"
	authors="apurvajo"
	manager="stefsch"
	editor="cephalin"
	tags="buy-ssl-certificates"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="apurvajo"/>

#Comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure

> [AZURE.SELECTOR]
- [Comprar certificado SSL no Azure](web-sites-purchase-ssl-web-site.md)
- [Usar certificado SSL de outro lugar](web-sites-configure-ssl-certificate.md)

Por padrão, o **[Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** já habilita o HTTPS para seu aplicativo Web com um certificado curinga para o domínio *.azurewebsites.net. Se não planeja configurar um domínio personalizado, você pode aproveitar o certificado HTTPS padrão. No entanto, como com todos os *[domínios curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), ele não é tão seguro quanto usar um domínio personalizado com seu próprio certificado. Agora, o Serviço de Aplicativo do Azure fornece uma maneira realmente simplificada de comprar e gerenciar um certificado SSL diretamente do Portal do Azure, sem precisar sair do portal. Este artigo explica como comprar e configurar um certificado SSL para o **[Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** em três etapas simples.

> [AZURE.NOTE]
Os certificados SSL para nomes de domínio personalizados não podem ser usados com um aplicativo Web Gratuito e Compartilhado. É preciso que você configure seu aplicativo Web para o modo Basic, Standard ou Premium, o que pode alterar o quanto você é cobrado por sua assinatura. Confira **[Detalhes de preços dos aplicativos Web](https://azure.microsoft.com/pricing/details/web-sites/)** para saber mais.



##<a name="bkmk_Overview"></a>Visão geral
> [AZURE.NOTE]
Não tente adquirir um certificado SSL usando uma assinatura que não tenha um cartão de crédito ativo associado a ela. Isso pode resultar na desabilitação de sua assinatura.

##<a> Comprar, armazenar e atribuir um certificado SSL para seu domínio personalizado </a>
Para habilitar HTTPS para um domínio personalizado, como contoso.com, você deve primeiro **[configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](web-sites-custom-domain-name.md)**

Antes de solicitar um certificado SSL, você deve primeiro determinar quais nomes de domínio serão protegidos pelo certificado. Isso determinará o tipo de certificado que você precisa ter. Se você precisar apenas garantir um único nome de domínio, como contoso.com ou www.contoso.com, um certificado Standard (básico) será suficiente. Se você precisa proteger vários nomes de domínio, como contoso.com, www.contoso.com e mail.contoso.com, você poderá obter um **[certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate)**

##<a name="bkmk_purchasecert"></a>Etapa 0: Fazer um pedido de certificado SSL

Nesta Etapa, você aprenderá a fazer um pedido de um certificado SSL de sua escolha.

1.	No **[Portal do Azure](https://portal.azure.com/)**, clique em Procurar e digite "Certificados do Serviço de Aplicativo" na barra de pesquisa, selecione "Certificados do Serviço de Aplicativo" no resultado e clique em Adicionar.

    ![inserir imagem de criação usando procurar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![inserir imagem de criação usando procurar](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.	Insira o **nome amigável** de seu certificado SSL.

3.	Insira o **Nome do Host**
> [AZURE.NOTE]
    Essa é uma das partes mais importantes do processo de compra. Digite corretamente o nome do host (domínio personalizado) que você deseja proteger com esse certificado. **NÃO** acrescente WWW ao nome do host. Por exemplo, se o seu nome de domínio personalizado for www.contoso.com, basta digitar contoso.com no campo Nome do Host, o certificado em questão protegerá os domínios raiz e www.
    
4.	Selecione sua **assinatura**.

    Se você tiver várias assinaturas, crie um Certificado SSL na mesma assinatura que você usou para seu domínio personalizado ou um aplicativo Web em questão.
       
5.	Selecione ou crie um **Grupo de Recursos**.

    Os grupos de recursos permitem gerenciar recursos do Azure relacionados como uma unidade e são úteis durante o estabelecimento de regras de controle de acesso baseado em função (RBAC) para seus aplicativos. Para saber mais, consulte Gerenciar seus recursos do Azure.
     
6.	Selecione o **SKU do certificado**

    Por fim, selecione o SKU do certificado que atenda às suas necessidades e clique em Criar. Atualmente, o Serviço de Aplicativo do Azure permite que você adquira dois SKUs diferentes • S1 – Certificado Standard com um ano de validade e renovação automática • W1 – Certificado curinga com validade de um ano e renovação automática. Confira **[Detalhes de preços de aplicativos Web](https://azure.microsoft.com/pricing/details/web-sites/)** para saber mais.

![inserir imagem do SKU do certificado](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
A criação do Certificado SSL levará de um a 10 minutos. Esse processo executa várias etapas em segundo plano que seriam muito difíceis de realizar manualmente.

##<a name="bkmk_StoreKeyVault"></a>Etapa 1: Armazenar o certificado no Cofre da Chave do Azure

Nessa Etapa, você aprenderá a colocar em armazenamento um Certificado SSL que você adquiriu no Cofre da Chave do Azure de sua escolha.

1.	Após a conclusão da compra do Certificado SSL, você precisará abrir manualmente a folha Recursos dos **Certificados de Serviço de Aplicativo** navegando até ela novamente (confira a Etapa 1 acima)

    ![inserir imagem de pronto para armazenar no KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    Você perceberá que o status do certificado é **"Emissão Pendente"**, pois há mais algumas etapas que precisam ser concluídas antes de começar a usar esses certificados.
 
2. Clique em **"Configuração do Certificado"** dentro da folha Propriedades do Certificado e clique em **"Etapa 1: Armazenar"** para armazenar esse certificado no Cofre da Chave do Azure.

3.	Na folha **"Status do Cofre da Chave"**, clique em **"Repositório do Cofre da Chave"** para escolher um Cofre da Chave existente a fim de armazenar esse certificado **OU "Criar Novo Cofre da Chave"** para criar um novo Cofre da Chave dentro do mesmo grupo de recursos e da mesma assinatura.
 
    ![inserir imagem de criação de novo KV](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    O Cofre da Chave do Azure tem custos bem mínimos para armazenar esse certificado. Veja **[Detalhes de Preço do Cofre de Chaves do Azure](https://azure.microsoft.com/pricing/details/key-vault/)** para obter mais informações.

4. Depois de selecionar o Repositório do Cofre da Chave para armazenar esse certificado, vá em frente e armazene-o clicando no botão **"Armazenar"** na parte superior da folha **"Status do Cofre da Chave"**.

    Isso deve concluir a etapa de armazenamento do certificado que você adquiriu com o Cofre da Chave do Azure de sua escolha. Após atualizar a folha, você também deverá ver a marca de verificação verde nessa etapa.
    
##<a name="bkmk_VerifyOwnership"></a>Etapa 2: Verificar a propriedade do domínio

Nesta etapa, você aprenderá a realizar a Verificação de Propriedade de Domínio de um Certificado SSL para o qual você acabou de fazer um pedido.

1.	Clique em **"Etapa 2: Verificar"** na folha **"Configuração de Certificado"**. Há quatro tipos de Verificação de domínio com suporte dos Certificados do Serviço de Aplicativo.

    * **Verificação do Serviço de Aplicativo**
    
        * Este será o processo mais conveniente se o **seu domínio personalizado já estiver atribuído aos Aplicativos do Serviço de Aplicativo.** Este método listará todos os Aplicativos do Serviço de Aplicativo que atendem a esse critério. Por exemplo, neste caso, **contosocertdemo.com** é um domínio personalizado atribuído ao Aplicativo do Serviço de Aplicativo chamado **"ContosoCertDemo"** e, portanto, é o único Aplicativo do Serviço de Aplicativo listado aqui. Se houvesse uma implantação em várias regiões, todos os aplicativos de todas as regiões seriam listados.
        
           O método de verificação SÓ está disponível para compras de certificado Standard (Básico). Para os Certificados Curinga, ignore e vá para a opção B, C ou D abaixo.
        * Clique no botão **"Verificar"** para concluir esta etapa.
        * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
        
        ![inserir imagem de Verificação de Serviço de Aplicativo](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)

    * **Verificação de domínio**

        * Este será o processo mais conveniente **SOMENTE SE** você tiver **[adquirido seu domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)**
        
        * Clique no botão **"Verificar"** para concluir esta etapa.
        
        * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.

    * **Verificação por email**
        
        * Um email de verificação já foi enviado para os endereços de email associados a esse domínio personalizado.
         
        * Abra o email e clique no link de verificação para concluir a etapa Verificação por email.
        
        * Se você precisar reenviar o email de verificação, clique no botão **"Reenviar Email"**.
         
    * **Verificação manual**
                 
        1. **Verificação de página Web HTML**
        
            * Crie um arquivo HTML chamado **{Token de Verificação de Domínio}**.html (você pode copiar o token da folha Status de Verificação de Domínio)
            
            * O conteúdo desse arquivo deve ter exatamente o mesmo nome do **Token de Verificação de Domínio**.
            
            * Carregue esse arquivo na raiz do servidor Web que hospeda seu domínio.
            
            * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
            
            Por exemplo, se você estiver comprando um certificado padrão para contosocertdemo.com com o Token de Verificação de Domínio **'cAGgQrKc'**, uma solicitação da Web feita para **'http://contosocertdemo.com/cAGgQrKc.html'** deverá retornar **cAGgQrKc.**
        2. **Verificação de registro TXT do DNS**

            * Usando o Gerenciador de DNS, crie um registro TXT no subdomínio **'DZC'** com um valor igual ao **Token de Verificação do Domínio.**
            
            * Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.
                              
            Por exemplo, para executar a validação de um certificado curinga com o nome de host ***.contosocertdemo.com** ou ***.subdomain.contosocertdemo.com** e o Token de Verificação do Domínio **cAGgQrKc**, você precisa criar um registro TXT em dzc.contosocertdemo.com com o valor **cAGgQrKc.**


##<a name="bkmk_AssignCertificate"></a>Etapa 3: Atribuir o certificado ao Aplicativo do Serviço de Aplicativo

Nesta Etapa, você aprenderá a atribuir esse certificado recém-adquirido para seus Aplicativos do Serviço de Aplicativo.

> [AZURE.NOTE]
Antes de executar as etapas nesta seção, você precisa ter associado um nome de domínio personalizado ao seu aplicativo. Para saber mais, confira **[Configurando um nome de domínio personalizado para um aplicativo Web.](web-sites-custom-domain-name.md)**

1.	Em seu navegador, abra o **[Portal do Azure.](https://portal.azure.com/)**
2.	Clique na opção **Serviço de Aplicativo** no lado esquerdo da página.
3.	Clique no nome do aplicativo ao qual você deseja atribuir a esse certificado.
4.	Em **Configurações**, clique em **Domínios personalizados e SSL.**
5.	Na **seção de certificados**, clique em **Importar Certificado** e selecione o Certificado que você acabou de adquirir

    ![inserir imagem de Importar Certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.jpg)

6. Na seção **Associações SSL** da guia **Configurações de SLL**, use os menus suspensos para selecionar o nome de domínio a ser protegido com o SSL e o certificado a usar. Você também pode selecionar se deseja usar **[Indicação de Nome de Servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseado em IP.

    ![inserir imagem de Associações SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.jpg)

       • O SSL baseado em IP associa um certificado a um nome de domínio mapeando um endereço IP público dedicado do servidor ao nome de domínio. Isso exige que cada nome de domínio (contoso.com, fabricam.com etc.), associado ao seu serviço tenha um endereço IP dedicado. Esse é o método tradicional de associação do certificado SSL a um servidor Web. • O SSL baseado em SNI é uma extensão para SSL e **[o protocolo TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security)** que permite que vários domínios compartilhem o mesmo endereço IP, com certificados de segurança separados para cada domínio. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) oferecem suporte ao SNI. No entanto, navegadores mais antigos podem não oferecer esse suporte. Para saber mais sobre SNI, confira o artigo **[Indicação de Nome de Servidor](http://en.wikipedia.org/wiki/Server_Name_Indication)** no Wikipédia.
       
7. Clique em *Salvar* para salvar as alterações e habilitar SSL.



Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá executar as seguintes etapas adicionais:

* Depois de ter configurado uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Você encontrará esse endereço IP na página **Painel** do aplicativo, na seção **Visualização Rápida**. Ele estará listado como **Endereço IP Virtual:**
    
    ![inserir imagem de IP SSL](./media/app-service-web-purchase-ssl-web-site/IPSSL.jpg)

    Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI, ou não estiver configurado para usar SSL, nenhum endereço será listado para essa entrada.
    
2. Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior. Nesse momento, você poderá visitar o seu aplicativo usando HTTPS:// em vez de HTTP:// para verificar se o certificado foi configurado corretamente.


##<a name="bkmk_Rekey"></a>Criar novas chaves e sincronizar o certificado

1. Por motivos de segurança, se você precisar criar uma nova chave para seu certificado, basta selecionar a opção **"Criar Nova Chave e Sincronizar"** na folha **"Propriedades do Certificado"**.

2. Clique no botão **"Criar Nova Chave"** para iniciar o processo. Esse processo pode demorar de um a 10 minutos para ser concluído.

    ![inserir imagem de Nova Chave SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. A criação de uma nova chave para o certificado causará a emissão de um novo certificado pela autoridade de certificação.
4. Você não será cobrado pela criação da nova chave durante o tempo de vida do certificado.
5. A criação de uma nova chave para o certificado passará pelo estado de Emissão Pendente.
6. Quando o certificado estiver pronto, sincronize seus recursos usando esse certificado para evitar interrupções no serviço.
7. A opção de sincronização não está disponível para certificados que ainda não foram atribuídos ao aplicativo Web.

## Mais Recursos ##
- [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure](web-sites-configure-ssl-certificate.md)
- [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)
- [Central de confiabilidade do Microsoft Azure](/support/trust-center/security/)
- [Opções de configuração desbloqueadas nos Sites do Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<!---HONumber=AcomDC_0810_2016-->