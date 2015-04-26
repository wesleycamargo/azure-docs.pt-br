O Sistema de Nomes de Domínio (DNS) é usado para localizar coisas na internet. Por exemplo, quando você digita um endereço em seu navegador ou clica em um link em uma página da web, ele usa o DNS para traduzir o domínio em um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito amigável ao ser humano. Por exemplo, é mais fácil se lembrar de um nome DNS como **contoso.com** do que se lembrar de um endereço IP, como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS se baseia em *records*. Os registros associam um determinado *name*, como **contoso.com**, a um endereço IP ou a outro nome DNS. Quando um aplicativo, como um navegador da web, pesquisa um nome no DNS, ele localiza o registro e usa tudo o que ele aponta como o endereço. Se o valor apontado for um endereço IP, o navegador usará esse valor. Se ele apontar para outro nome DNS, o aplicativo precisará fazer a resolução novamente. Em última análise, todas as resoluções de nome acabarão em um endereço IP.

Quando você cria um site do Azure, um nome DNS é atribuído automaticamente a ele. Esse nome assume a forma de **&lt;nome_do_seu_site&gt;.azurewebsites.net**. Quando você adiciona seu site como um ponto de extremidade do Gerenciador de Tráfego do Azure, o site permanece acessível por meio do domínio **&lt;seu_perfil_do_gerenciador_de_tráfego&gt;.trafficmanager.net**.

> [AZURE.NOTE] Quando o site estiver configurado como um ponto de extremidade do Gerenciador de Tráfego, você usará o endereço **.trafficmanager.net** ao criar registros DNS.

> [AZURE.NOTE] Só é possível usar registros CNAME com o Gerenciador de Tráfego

Também existem vários tipos de registros, cada um com suas próprias funções e limitações, mas para sites configurados como pontos de extremidade do Gerenciador de Tráfego, nos preocupamos apenas com um: os registros *CNAME*.

###Registro CNAME ou de alias

Um registro CNAME mapeia um nome DNS *specific*, como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canônico). No caso dos sites do Azure usando o Gerenciador de Tráfego, o nome de domínio canônico é o nome de domínio **&lt;meu_aplicativo>.trafficmanager.net** do perfil do Gerenciador de Tráfego. Quando criado, o CNAME cria um alias para o nome de domínio **&lt;meu_aplicativo>.trafficmanager.net**. A entrada CNAME resolverá o endereço IP de seu nome de domínio **&lt;meu_aplicativo>.trafficmanager.net** automaticamente, portanto, se o endereço IP do site for alterado, nenhuma ação será necessária.

Depois que o tráfego chega ao Gerenciador de Tráfego, ele encaminha o tráfego para o site, usando o método de balanceamento de carga para o qual está configurado. Isso é completamente transparente para os visitantes do site. Eles verão apenas o nome de domínio personalizado no navegador.

> [AZURE.NOTE] Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como **www.contoso.com**, e não nomes de raiz, como **contoso.com**. Para mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de Domínio IETF - Implementação e Especificação</a>.

<!--HONumber=42-->
