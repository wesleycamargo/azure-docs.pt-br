O DNS (Sistema de Nomes de Domínio) é usado para localizar recursos na Internet. Por exemplo, quando você insere o endereço de um site em seu navegador ou clica em um link em uma página da Web, ele usa o DNS para traduzir o domínio em um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito amigável ao ser humano. Por exemplo, é mais fácil se lembrar de um nome DNS como **contoso.com** do que se lembrar de um endereço IP, como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS se baseia em *records*. Os registros associam um determinado *name*, como **contoso.com**, a um endereço IP ou a outro nome DNS. Quando um aplicativo, como um navegador da web, pesquisa um nome no DNS, ele localiza o registro e usa tudo o que ele aponta como o endereço. Se o valor apontado for um endereço IP, o navegador usará esse valor. Se ele apontar para outro nome DNS, o aplicativo precisará fazer a resolução novamente. Em última análise, todas as resoluções de nome acabarão em um endereço IP.

Quando você cria um site do Azure, um nome DNS é atribuído automaticamente a ele. Esse nome assume a forma de **&lt;nome_do_seu_site&gt;.azurewebsites.net**. Há também um endereço IP virtual disponível para uso durante a criação de registros DNS, para você criar registros que apontem para o **.azurewebsites.net** ou apontar para o endereço IP.

> [AZURE.NOTE] O endereço IP de seu site será alterado se você excluir e recriar seu site ou alterar o modo de site para grátis depois que ele tiver sido definido como básico, compartilhado ou padrão.

Também existem vários tipos de registros, cada um com suas próprias funções e limitações, mas para sites nos preocupamos apenas com dois, os registros *A* e *CNAME*.

###Registro de endereço (registro A)

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *or a wildcard domain* como **\*.contoso.com**, para um endereço IP. No caso de um site do Azure, o IP virtual do serviço ou um endereço IP específico comprado para seu site.

Os principais benefícios de um registro A em relação a um registro CNAME são:

* Você pode mapear um domínio raiz como **contoso.com** para um endereço IP; muitos registradores só permitem isso com registros A

* Você pode ter uma entrada que usa um caractere curinga, como **\*.contoso.com**, que pode identificar solicitações para vários subdomínios como **mail.contoso.com**, **blogs.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE] Como um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações feitas no endereço IP de seu site. Um endereço IP é fornecido para uso com registros A quando você define configurações de nome de domínio personalizado para seu site; no entanto, esse valor poderá ser alterado se você excluir e recriar seu site ou alterar o modo desse site novamente para gratuito.

###Registro de Alias (registro CNAME)

Um registro CNAME mapeia um nome DNS *specific*, como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canônico). No caso de Sites do Azure, o nome de domínio canônico é o nome de domínio **&lt;<nomedoseusite>.azurewebsites.net** de seu site. Quando criado, o CNAME cria um alias para o nome de domínio **&lt;nomedoseusite>.azurewebsites.net**. A entrada CNAME resolverá o endereço IP de seu nome de domínio **&lt;nomedoseusite>.azurewebsites.net** automaticamente, portanto, se o endereço IP do site for alterado, nenhuma ação será necessária.

> [AZURE.NOTE] Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como **www.contoso.com**, e não nomes de raiz, como **contoso.com**. Para mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de Domínio IETF - Implementação e Especificação</a>.

###Especificações de DNS de site do Azure

O uso de um registro A com os Sites do Azure exige que você crie primeiro um dos seguintes registros CNAME:

* **Para o domínio raiz ou os subdomínios curinga** - um nome DNS de **awverify** para **awverify.&lt;nomedoseusite&gt;.azurewebsites.net**.

* **Para um subdomínio específico** - - um nome DNS de **awverify.&lt;sub-domínio>** para **awverify.&lt;nome_do_seu_site&gt;.azurewebsites.net**. Por exemplo, **awverify.blogs** se o registro A for para **blogs.contoso.com**.

Esse registro CNAME é usado para verificar se você tem o domínio que está tentando usar. Isso vai além da criação de um registro A que aponta para o endereço IP virtual de seu site.

Você pode encontrar o endereço IP, bem como o nome **awverify** e os nomes **.azurewebsites.net** para seu site realizando as etapas a seguir:

1. No seu navegador, abra o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e, em seguida, selecione **Gerenciar Domínios** na parte inferior da página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE] Se **gerenciar domínios** não estiver habilitado, então você está usando um site gratuito. Você não pode utilizar nomes de domínio personalizados com um site gratuito; por isso, precisa atualizar para o modo Compartilhado, Básico ou Padrão. Para obter mais informações sobre os modos de site, inclusive sobre como alterar o modo do site, consulte [Como dimensionar sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/).

6. Na caixa de diálogo **GERENCIAR DOMÍNIOS PERSONALIZADOS**, você verá as informações de **awverify**, o nome de domínio **.azurewebsites.net** atribuído no momento e o endereço IP virtual. Salve essas informações, pois elas serão utilizadas na criação de registros DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)


<!--HONumber=42-->
