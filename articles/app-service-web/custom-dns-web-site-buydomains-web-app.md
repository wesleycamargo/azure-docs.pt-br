<properties
	pageTitle="Como compara um nome de domínio personalizado nos Aplicativos Web do Serviço de Aplicativo do Azure"
	description="Saiba como comprar um nome de domínio personalizado com um aplicativo Web no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Quando você cria um aplicativo Web, o Azure o atribui a um subdomínio do azurewebsites.net. Por exemplo, se seu aplicativo Web é chamado **contoso**, a URL é **contoso.azurewebsites.net**. O Azure também fornece um endereço IP virtual.

Para um aplicativo Web de produção, é bem provável que você queira que os usuários vejam um nome de domínio personalizado. Este artigo explica como comprar e configurar um domínio personalizado com [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Visão geral

Se você não tiver um nome de domínio para seu aplicativo web, poderá comprar um facilmente no [Portal do Azure](https://portal.azure.com/). Durante o processo de compra você pode optar para que os registros DNS do domínio raiz e WWW sejam mapeados automaticamente para seu aplicativo da web. Também é possível gerenciar seu direito de domínio dentro do Portal do Azure.


Use as etapas a seguir para comprar os nomes de domínio e atribuir ao seu aplicativo Web.

1. No seu navegador, abra o [Portal do Azure](https://portal.azure.com/).

2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configurações** e, em seguida, selecione **Domínios personalizados**

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Na folha **Domínios personalizados**, clique em **Comprar domínios**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Na folha **Comprar domínios**, use a caixa de texto para digitar o nome de domínio que você deseja comprar e pressione Enter. Os domínios disponíveis sugeridos serão mostrados logo abaixo da caixa de texto. Selecione o domínio que você deseja comprar. É possível optar por adquirir vários domínios ao mesmo tempo.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Clique em **Informações de contato** e preencha o formulário de informações de contato do domínio.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] It is very important that you fill out all required fields with as much accuracy as possible, especially the email address. In case of purchasing the domain without "Privacy protection", you might be asked to verify your email before the domain becomes active. In some cases, incorrect data for contact information will result in failure to purchase domains. 

6. Agora você pode optar por

	a) "Renovar automaticamente" seu domínio todos os anos
	
	b) Aceitar a "Proteção da privacidade" que está incluída no preço de compra GRATUITAMENTE (exceto para TLDs cujo registro não dá suporte a Privacidade. Por exemplo: .co.in, .co.uk, etc.)
	
	c) "Atribuir nomes de host padrão" para WWW e raiz do domínio para o Aplicativo Web atual.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Option C configures DNS bindings and Hostname bindings automatically for you.  This way, your Web App can be accessed using custom domain as soon as the purchase is complete (baring DNS propagation delays in few cases). In case, your Web App is behind Azure Traffic Manager, you will not see an option to assign root domain, as A-Records do not work with the Traffic Manager. 
    You can always assign the domains/sub-domains purchased through one Web App to another Web App and vice-versa. See step 8 for more details. 
	
7. Clique em **Selecionar** na folha **Comprar domínios** e você verá as informações de compra na folha **Confirmação de compra**. Se você aceitar os termos legais e clicar em **Comprar**, seu pedido será enviado e você poderá monitorar o processo de compra em **Notificação**. A compra de domínio pode demorar alguns minutos para ser concluída.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Se você fez um pedido um domínio com êxito, pode gerenciar o domínio e atribuir ao seu aplicativo Web. Clique em **"..."** no lado direito do seu domínio. Depois disso, você pode **Cancelar compra** ou **Gerenciar domínio**. Clique em **Gerenciar domínio**, e assim poderemos vincular o **subdomínio** ao nosso aplicativo Web na folha **Gerenciar domínio**. Se você quiser vincular um **subdomínio** a um aplicativo Web diferente, execute esta etapa dentro do contexto do aplicativo da Web respectivo. Aqui, é possível optar por atribuir o domínio ao ponto de extremidade do Gerenciador de Tráfego (se o aplicativo Web estiver atrás do GT) simplesmente selecionando nome do Gerenciador de tráfego no menu suspenso. Ao fazer isso, o domínio/subdomínio será automaticamente atribuído a todos os aplicativos Web por trás do ponto de extremidade do Gerenciador de Tráfego.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] É possível "Cancelar compra" no prazo de 5 dias para obter reembolso total. Após 5 dias, não será mais possível "Cancelar comprar" e, em vez disso, você verá uma opção para "Excluir" o domínio. A exclusão do domínio resultará em sua liberação da assinatura sem reembolso e ele se tornará um domínio disponível.

Depois que a configuração estiver concluída, o nome do domínio personalizado será listado na seção **Vínculos de Hostname** de seu aplicativo Web.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao aplicativo Web.
 
## O que acontece com o domínio personalizado que você comprou

O domínio personalizado adquirido na folha **Domínios personalizados e SSL** está vinculado à assinatura do Azure. Como um recurso do Azure, esse domínio personalizado é separado e independente do Aplicativo do Serviço de Aplicativo para o qual você comprou o domínio. Isso significa que:

- No Portal do Azure, você pode usar o domínio personalizado que você comprou para mais de um aplicativo de Serviço de Aplicativo e não apenas para o aplicativo para o qual você adquiriu o domínio personalizado.
- Você pode gerenciar todos os domínios personalizados comprados na assinatura do Azure indo para a folha **Domínios personalizados e SSL** de *qualquer* aplicativo do Serviço de Aplicativo naquela assinatura.
- Você pode atribuir qualquer aplicativo do Serviço de Aplicativo a partir da mesma assinatura do Azure a um subdomínio nesse domínio personalizado.
- Se você decidir excluir um aplicativo do Serviço de Aplicativo, você pode optar por não excluir o domínio personalizado ao qual ele está vinculado se quiser continuar usando o domínio para outros aplicativos.

## Se não conseguir ver o domínio personalizado que você comprou

Se você comprou o domínio personalizado a partir da folha **Domínios personalizados e SSL**, mas não consegue ver o domínio personalizado em **gerenciando domínios**, verifique se as seguintes ações:

- A criação de domínio personalizado pode não ter terminado. Verifique o sino de notificação na parte superior do Portal do Azure para ver o andamento.
- A criação de domínio personalizado pode ter falhado por algum motivo. Verifique o sino de notificação na parte superior do Portal do Azure para ver o andamento.
- O criação do domínio personalizado pode ter sido bem-sucedida, mas a folha pode não ter sido atualizada. Tente abrir a folha **domínios personalizados e SSL**.
- Você pode ter excluído o domínio personalizado em algum momento. Verifique os logs de auditoria clicando em **Configurações** > **Logs de auditoria** a partir da folha principal do seu aplicativo.
- A folha **Domínios personalizados e SSL** que você está procurando pode pertencer a um aplicativo criado em uma assinatura diferente do Azure. Alterne para outro aplicativo em uma assinatura diferente e verifique sua folha **Domínios personalizados e SSL** . No portal, você não conseguirá ver nem gerenciar os domínios personalizados criados em uma assinatura do Azure diferente da do aplicativo. No entanto, se você clicar em **Gerenciamento avançado** na folha do domínio **Gerenciar domínio**, será redirecionado para o site do provedor do domínio, onde poderá [configurar manualmente seu domínio personalizado como qualquer domínio personalizado externo](web-sites-custom-domain-name.md) para os aplicativos criados em uma assinatura diferente do Azure.

<!---HONumber=AcomDC_0921_2016-->