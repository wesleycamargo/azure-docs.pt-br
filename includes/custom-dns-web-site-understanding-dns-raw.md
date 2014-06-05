O Sistema de Nomes de Domínio (DNS) é usado para localizar coisas na internet. Por exemplo, quando você digita um endereço em seu navegador ou clica em um link em uma página da web, ele usa o DNS para traduzir o domínio em um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito amigável ao ser humano. Por exemplo, é mais fácil se lembrar de um nome DNS como **contoso.com** do que se lembrar de um endereço IP, como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS se baseia em *registros*. Os registros associam um determinado *nome*, como **contoso.com**, a um endereço IP ou a outro nome DNS. Quando um aplicativo, como um navegador da web, pesquisa um nome no DNS, ele localiza o registro e usa tudo o que ele aponta como o endereço. Se o valor apontado for um endereço IP, o navegador usará esse valor. Se ele apontar para outro nome DNS, o aplicativo precisará fazer a resolução novamente. Em última análise, todas as resoluções de nome acabarão em um endereço IP.

Quando você cria um site do Azure, um nome DNS é atribuído automaticamente ao site. Esse nome assume a forma de **&lt;yoursitename&gt;.azurewebsites.net**. Há também um endereço IP virtual disponível para uso durante a criação de registros DNS, para você criar registros que apontem para o **.azurewebsites.net** ou apontar para o endereço IP.

> [WACOM.NOTE] O endereço IP de seu site será alterado se você excluir e recria seu site ou alterar o modo de site para livre depois que ele tiver sido definido como básico, compartilhado ou padrão.

Também existem vários tipos de registros, cada um com suas próprias funções e limitações, mas para sites nos preocupamos apenas com dois, os registros *CNAME* e *A*.

###Registro CNAME ou de alias

Um registro CNAME mapeia um nome DNS *específico* como **mail.contoso.com** ou **www.contoso.com** para outro nome de domínio (canônico). No caso de sites do Azure, o nome de domínio canônico é o nome de domínio **&lt;myapp>.azurewebsites.net** de seu site. Uma vez criado, o CNAME cria um alias para o nome de domínio **&lt;myapp>.azurewebsites.net**. Como a entrada CNAME resolverá o endereço IP de seu nome de domínio **&lt;myapp>.azurewebsites.net** automaticamente, se o endereço IP do site for alterado, você não precisará realizar nenhuma ação.

> [WACOM.NOTE] Alguns registradores de domínio só permitem que você mapeie subdomínios ao usar um registro CNAME, como **www.contoso.com**, e não nomes raiz, como **contoso.com**. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida pelo seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia sobre o registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - Implementação e especificação</a>.

###Registro A

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga* como **\*.contoso.com**, para um endereço IP. No caso de um site do Azure, o IP virtual do serviço ou um endereço IP específico que você comprou para seu site.

Os principais benefícios de um registro A em relação a um registro CNAME são:

* Você pode mapear um domínio raiz como **contoso.com** para um endereço IP; muitos registradores só permitem isso com registros A

* Você pode ter uma entrada usando um curinga, como **\*.contoso.com**, o que identificaria solicitações para vários subdomínios como **mail.contoso.com**, **login.contoso.com**, ou **www.contso.com**.

> [WACOM.NOTE] Como um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações feitas no endereço IP de seu site. É fornecido um endereço IP para uso com registros A quando você define as configurações de nome de domínio personalizado para seu site; no entanto, esse valor poderá ser alterado se você excluir e recriar seu site ou alterar o modo de site de volta para gratuito.

###Aspectos específicos de DNS do site

O uso de um registro A com sites do Azure exige que você crie primeiro um registro CNAME mapeando:

* Um nome DNS de **www** para seu **&lt;yourwebsitename&gt;.azurewebsites.net**.
OU
* Um nome DNS de **awverify.www** para **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.

Esse registro CNAME é usado para verificar se você tem o domínio que está tentando usar. Isso vai além da criação de um registro A apontando para o endereço IP virtual de seu site.

Você pode encontrar o endereço IP, bem como os nomes **awverify.www** e **.azurewebsites.net** para seu site, executando as seguintes etapas:

1. No navegador, abra o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar domínios** na parte inferior da página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Na caixa de diálogo **Gerenciar Domínios Personalizados**, você verá as informações de **awverify**, o nome de domínio **.azurewebsites.net** atribuído no momento e o endereço IP virtual.


