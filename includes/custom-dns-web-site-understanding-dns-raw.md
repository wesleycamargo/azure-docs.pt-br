O DNS (Sistema de Nomes de Domínio) é usado para localizar recursos na Internet. Por exemplo, quando você insere o endereço de um aplicativo Web em seu navegador ou clica em um link em uma página da Web, ele usa o DNS para traduzir o domínio em um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito amigável ao ser humano. Por exemplo, é mais fácil se lembrar de um nome DNS como **contoso.com** do que se lembrar de um endereço IP, como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS se baseia em *registros*. Os registros associam um determinado *nome*, como **contoso.com**, a um endereço IP ou a outro nome DNS. Quando um aplicativo, como um navegador da web, pesquisa um nome no DNS, ele localiza o registro e usa tudo o que ele aponta como o endereço. Se o valor apontado for um endereço IP, o navegador usará esse valor. Se ele apontar para outro nome DNS, o aplicativo precisará fazer a resolução novamente. Em última análise, todas as resoluções de nome acabarão em um endereço IP.

Quando você cria um aplicativo Web no Serviço de Aplicativo, um nome DNS é atribuído automaticamente ao aplicativo Web. Esse nome assume o formato **&lt;yourwebappname&gt;.azurewebsites.net**. Há também um endereço IP virtual disponível para uso durante a criação de registros DNS, para que você possa criar registros que apontem para **.azurewebsites.net**, ou para que você possa apontar para o endereço IP.

> [AZURE.NOTE]O endereço IP do seu aplicativo Web será alterado se você excluir e recriar seu aplicativo Web, ou então se alterar o modo de plano de Serviço de Aplicativo para **Gratuito** depois de ele ter sido definido como **Básico**, **Compartilhado** ou **Padrão**.

Também existem vários tipos de registros, cada um com suas próprias funções e limitações, mas, para aplicativos Web, nos preocupamos apenas com dois, os registros *A* e *CNAME*.

###Registro de endereço (registro A)

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga*, como ***.contoso.com**, para um endereço IP. No caso de um aplicativo Web no Serviço de Aplicativo, será o IP virtual do serviço ou então um endereço IP específico que você adquiriu para seu aplicativo Web.

Os principais benefícios de um registro A em relação a um registro CNAME são:

* Você pode mapear um domínio raiz, como **contoso.com**, para um endereço IP; muitos registradores só permitem isso com registros A

* É possível ter uma entrada que usa um curinga, como ***.contoso.com**, que lidaria com solicitações para vários subdomínios, como **mail.contoso.com**, **blogs.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE]Já que um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações feitas no endereço IP de seu aplicativo Web. Um endereço IP para uso com registros A é fornecido quando você define as configurações de nome de domínio personalizado para seu aplicativo Web; no entanto, esse valor poderá ser alterado se você excluir e recriar seu aplicativo Web, ou se alterar o modo do plano do Serviço de Aplicativo de volta para **Gratuito**.

###Registro de Alias (registro CNAME)

Um registro CNAME mapeia um nome DNS *específico* como **mail.contoso.com** ou **www.contoso.com** para outro nome de domínio (canônico). No caso de aplicativos Web do Serviço de Aplicativo, o nome de domínio canônico é o nome de domínio **&lt;yourwebappname>.azurewebsites.net** do seu aplicativo Web. Uma vez criado, o CNAME cria um alias para o nome de domínio **&lt;yourwebappname>.azurewebsites.net**. A entrada CNAME será resolvida automaticamente para o endereço IP de seu nome de domínio **&lt;yourwebappname>.azurewebsites.net**, de modo que se o endereço IP do aplicativo Web for alterado, você não precisará realizar nenhuma ação.

> [AZURE.NOTE]Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como **www.contoso.com**, e não nomes de raiz, como **contoso.com**. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida por seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia sobre o registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de Domínio IETF - Implementação e Especificação</a>.

###Dados específicos de DNS do aplicativo Web

O uso de um registro A com os Aplicativos Web do Azure exige que você crie primeiro um dos registros CNAME a seguir:

* **Para o domínio raiz ou os subdomínios curinga** - um nome DNS de **awverify** para **awverify.NomeDoSeuAplicativoWeb.azurewebsites.net**.

* **Para um subdomínio específico** - um nome DNS de **awverify.&lt;sub-domain>** para **awverify.&lt;yourwebappname&gt;.azurewebsites.net**. Por exemplo, **awverify.blogs**, se o registro A for para **blogs.contoso.com**.

Esse registro CNAME é usado para verificar se você tem o domínio que está tentando usar. Isso vai além da criação de um registro A apontando para o endereço IP virtual de seu aplicativo Web.

Você pode encontrar o endereço IP, bem como o nome **awverify** e os nomes **.azurewebsites.net** para seu aplicativo Web, executando as seguintes etapas:

1. No seu navegador, abra o [Portal do Azure](https://portal.azure.com).

2. Na lâmina **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Todas as Configurações** e, em seguida, selecione **Domínios personalizados e SSL** na parte inferior da página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

> [AZURE.NOTE] Se **Gerenciar domínios** não estiver habilitado, então você está usando um aplicativo Web **Gratuito**. Você não pode usar nomes de domínio personalizados com um aplicativo Web **Gratuito**, e deve atualizar o plano de Serviço de Aplicativo para o modo **Compartilhado**, **Básico** ou **Padrão**. Para obter mais informações sobre os modos de plano de Serviço de Aplicativo, inclusive sobre como alterar o modo do aplicativo Web, consulte [Como dimensionar aplicativos Web](../articles/web-sites-scale.md).

6. No diálogo **GERENCIAR DOMÍNIOS PERSONALIZADOS**, você verá as informações de **awverify**, o nome de domínio **.azurewebsites.net** atribuído no momento e os endereços IP virtuais. Salve essas informações, pois elas serão utilizadas na criação de registros DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)

<!----HONumber=July15_HO3-->