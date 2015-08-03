#Configurando um nome de domínio personalizado para um Site do Azure.

Ao criar um site, o Azure oferece um subdomínio amigável no domínio azurewebsites.net para que seus usuários possam acessar seu site usando uma URL como http://&lt;mysite>.azurewebsites.net. No entanto, se você configurar seus sites para o modo Padrão ou Compartilhado, você pode mapear o seu site para o seu próprio nome de domínio.

Opcionalmente, você pode usar o Gerenciador de Tráfego do Azure para balancear a carga do tráfego de entrada para seu site. Para obter mais informações sobre como o Gerenciador de Tráfego funciona com Sites da Web, consulte [Controlar o tráfego de Sites da Web do Azure com o Gerenciador de Tráfego do Azure][trafficmanager].

> [AZURE.NOTE]Os procedimentos nesta tarefa se aplicam aos sites do Azure; para serviços de nuvem, consulte <a href="/develop/net/common-tasks/custom-dns/">Configurando um nome de domínio personalizado no Azure</a>.

> [AZURE.NOTE]As etapas nesta tarefa exigem que você configure seus sites no modo Compartilhado ou Padrão, que podem alterar o quanto você será cobrado por sua assinatura. Consulte <a href="/pricing/details/web-sites/">Detalhes de Preços dos Sites</a> para obter mais informações.

Neste artigo:

-   [Noções básicas sobre os registros CNAME e A](#understanding-records)
-   [Configurar seus sites para modo compartilhado ou padrão](#bkmk_configsharedmode)
-   [Adicionar seus sites ao Gerenciador de Tráfego](#trafficmanager)
-   [Adicionar um CNAME para seu domínio personalizado](#bkmk_configurecname)
-   [Adicionar um registro A em seu domínio personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Entender os registros CNAME e A</h2>

Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um site, de qualquer forma, cada um deles funciona de modo diferente.

###Registro CNAME ou de alias

Um registro CNAME mapeia um domínio *específico*, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o nome de domínio **&lt;myapp>.azurewebsites.net** de seu site do Azure ou o nome de domínio **&lt;myapp>.trafficmgr.com** de seu perfil do Gerenciador de Tráfego. Uma vez criado, o CNAME cria um alias para o nome de domínio **&lt;myapp>.azurewebsites.net** ou **&lt;myapp>.trafficmgr.com**. A entrada CNAME resolverá o endereço IP de seu nome de domínio **&lt;myapp>.azurewebsites.net** ou **&lt;myapp>.trafficmgr.com** automaticamente, por isso, se o endereço IP do site for alterado, você não precisará realizar qualquer ação.

> [AZURE.NOTE]Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como www.contoso.com, e não nomes de raiz, como contoso.com. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida por seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia sobre o registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de Domínio IETF - Implementação e Especificação</a>.

###Registro A

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga*, como ***.contoso.com**, para um endereço IP. No caso de um site do Azure, o IP virtual do serviço ou um endereço IP específico comprado para seu site. Portanto, o principal benefício de um registro A em relação a um registro CNAME é que você pode ter uma entrada que usa um curinga, como ***.contoso.com**, que lidaria com as solicitações com vários subdomínios, como **mail.contoso.com**, **login.contoso.com** ou **www.contoso.com**.

> [AZURE.NOTE]Como um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações feitas no endereço IP de seu site. Um endereço IP é fornecido para uso com registros A quando você define configurações de nome de domínio personalizado para seu site; no entanto, esse valor poderá ser alterado se você excluir e recriar seu site ou alterar o modo desse site novamente para gratuito.

> [AZURE.NOTE]Registros não podem ser usados para balanceamento de carga com Gerenciador de Tráfego. Para obter mais informações, consulte [Controlar o tráfego de Sites da Web do Azure com o Gerenciador de Tráfego][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurar seus sites para o modo compartilhado ou padrão</h2>

A configuração de um nome de domínio personalizado em um site está disponível somente para os modos Compartilhado e Padrão para Sites do Azure. Antes de mudar um site do modo de site Grátis para Compartilhado, ou Padrão, primeiro remova seus limites de gasto em vigor na assinatura do site. Para obter mais informações sobre os preços do modo compartilhado e padrão, consulte [Detalhes de preços][PricingDetails].

1. No navegador, abra o [Portal de gerenciamento][portal].
2. Na guia **Sites**, clique no nome do seu site.

	![][standardmode1]

3. Clique na guia **ESCALA**.

	![][standardmode2]


4. Na seção **geral**, defina o modo do site clicando em **COMPARTILHADO**.

	![][standardmode3]

	> [AZURE.NOTE]Se você for usar o Gerenciador de Tráfego com este site, é preciso selecionar o modo Padrão em vez de o Compartilhado.

5. Clique em **Salvar**.
6. Quando solicitado sobre o aumento nos custos de modo compartilhado (ou para o modo padrão se você optar por padrão), clique em **Sim** se você concordar.

	<!--![][standardmode4]-->**Observação**<br /> Se você receber um erro “Falha na configuração de escala para o site 'nome do site'", você poderá usar o botão de detalhes para obter mais informações.

<a name="trafficmanager"></a><h2>(Opcional) Adicionar seus sites ao Gerenciador de Tráfego</h2>

Se você deseja usar seu site com Gerenciador de Tráfego, execute as seguintes etapas.

1. Se você ainda não tiver um perfil do Gerenciador de Tráfego, use as informações em [Criar um perfil do Gerenciador de Tráfego usando Criação Rápida][createprofile] para criar um. Observe o nome de domínio **. trafficmgr.com** associado ao perfil do Gerenciador de Tráfego. Ele será usado em uma etapa posterior.

2. Use as informações contidas em [Adicionar ou Excluir Pontos de Extremidade][addendpoint] para adicionar o site como um ponto de extremidade em seu perfil do Gerenciador de Tráfego.

	> [AZURE.NOTE]Se o site não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado para o modo Padrão. Você deve usar o modo Padrão para o site para trabalhar com o Gerenciador de Tráfego.

3. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

4. Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias**, ou **Subdomínios**.

5. Você também deve fornecer o alias de domínio ou subdomínio para o CNAME. Por exemplo, **www** se você deseja criar um alias para **www.customdomain.com**.

5. Você também deve fornecer um nome do host que seja o nome do domínio canônico para este alias CNAME. Este é o nome **.trafficmgr.com** para seu site.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** a **contoso.trafficmgr.com**, o nome de domínio de um site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome do host/Subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca visualizará o host verdadeiro (contoso.azurewebsite.net), para que o processo de encaminhamento seja invisível para o usuário final.

> [AZURE.NOTE]Se você estiver usando o Gerenciador de Tráfego com um site, você não precisará seguir as seguintes etapas nas seções '**Adicionar um CNAME em seu domínio personalizado**' e 'Adicionar um registro A em seu domínio personalizado**'. O registro CNAME criado nas etapas anteriores roteará o tráfego de entrada para o Gerenciador de Tráfego, que encaminha o tráfego para o(s) ponto(s) de extremidade do site da web.

<a name="bkmk_configurecname"></a><h2>Adicionar um CNAME para seu domínio personalizado</h2>

Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para localizar o nome de domínio **.azurewebsite.net** atribuído ao seu site.

	* Faça logon no [Portal de Gerenciamento do Azure][portal], selecione o seu site, selecione **Painel** e localize a entrada **URL do Site** na seção **visualização rápida**.

	* Instale e configure o [Azure Powershell](/manage/install-and-configure-windows-powershell/) e use o seguinte comando:

			get-azurewebsite yoursitename | select hostnames

	* Instale a configure a [Interface de linha de comando do Azure](/manage/install-and-configure-cli/) e, depois, use o seguinte comando:

			azure site domain list yoursitename

	Salve este nome **.azurewebsite.net**, pois ele será usado nas etapas a seguir.

3. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

4. Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias**, ou **Subdomínios**.

5. Você também deve fornecer o alias de domínio ou subdomínio para o CNAME. Por exemplo, **www** se você deseja criar um alias para **www.customdomain.com**. Se você deseja criar um alias para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.

5. Você também deve fornecer um nome do host que seja o nome do domínio canônico para este alias CNAME. Este é o nome **.azurewebsite.net** para seu site.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** a **contoso.azurewebsite.net**, o nome de domínio de um site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome do host/Subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca visualizará o host verdadeiro (contoso.azurewebsite.net), para que o processo de encaminhamento seja invisível para o usuário final.

> [AZURE.NOTE]O exemplo acima aplica-se somente ao tráfego no subdomínio __www__. Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se quiser direcionar o tráfego de subdomínios, como *.contoso.com, para o endereço azurewebsite.net, você poderá configurar uma entrada __Redirecionamento de URL__ ou __Encaminhamento de URL__ em suas configurações DNS ou criar um registro A.

> [AZURE.NOTE]Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. Você não pode definir o CNAME para o site até que o CNAME seja propagado. É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###Adicionar o nome de domínio para seu site

Após o registro CNAME para o nome de domínio ter sido propagado, você deve associá-lo com seu site. Você pode adicionar o nome de domínio personalizado definido pelo registro CNAME ao seu site por meio de qualquer interface de linha de comando do Azure (CLI do Azure) ou usando o Portal de Gerenciamento do Azure.

**Adicionar um nome de domínio usando as ferramentas da linha de comando**

Instale a configure a [Interface de linha de comando do Azure](/manage/install-and-configure-cli/) e, depois, use o comando a seguir:

	azure site domain add customdomain yoursitename

Por exemplo, o seguinte adicionará um nome de domínio personalizado de **www.contoso.com** para o site **contoso.azurewebsite.net**:

	azure site domain add www.contoso.com contoso

Você pode confirmar se o nome de domínio personalizado foi adicionado ao site usando o seguinte comando:

	azure site domain list yoursitename

A lista retornada por este comando deve conter o nome de domínio personalizado, bem como a entrada **.azurewebsite.net** padrão.

**Adicionar um nome de domínio usando o Portal de Gerenciamento do Azure**

1. No seu navegador, abra o [Portal de Gerenciamento do Azure][portal].

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar Domínios** na parte inferior da página.

	![][setcname2]

6. Na caixa de texto **NOMES DE DOMÍNIO**, digite o nome do domínio configurado.

	![][setcname3]

6. Clique na marca de seleção para aceitar o nome de domínio.

Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configurar** do seu site.

<a name="bkmk_configurearecord"></a><h2>Adicionar um registro A em seu domínio personalizado</h2>

Para criar um registro, primeiro você deve encontrar o endereço IP do seu site. Em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos. Além de criar um registro A, você também deve criar um registro CNAME que o Azure usa para verificar o registro A.

1. No seu navegador, abra o [Portal de Gerenciamento do Azure][portal].

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar Domínios** na parte inferior da tela.

	![][setcname2]

5. Na caixa de diálogo **Gerenciar domínios personalizados**, localize o **Endereço IP a ser usado ao configurar registros A**. Copie o endereço IP. Isso será usado ao criar o registro A.

5. Na caixa de diálogo **Gerenciar domínios personalizados**, observe o nome de domínio awverify no final do texto na parte superior da caixa de diálogo. Ela deve ser **awverify.mysite.azurewebsites.net** onde **mysite** é o nome do seu site. Copie isso, uma vez que é o nome de domínio usado ao criar o registro CNAME de verificação.

6. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

6. Encontre onde você pode selecionar ou inserir registros CNAME e A. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas.

7. Execute as seguintes etapas para criar o registro A:

	1. Selecione ou digite o domínio ou subdomínio que usará o registro A. Por exemplo, selecione **www** se você deseja criar um alias para **www.customdomain.com**. Se você desejar criar uma entrada curinga para todos os subdomínios, digite '__*__'. Ele abrange todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.

		Se você deseja criar um registro A para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.

	2. Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

		Por exemplo, o seguinte registro A encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do nosso aplicativo implantado:

		<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome do host/Subdomínio</strong></td>
<td><strong>Endereço IP</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>Este exemplo demonstra como criar um registro A para o domínio raiz. Se você desejar criar uma entrada curinga para abranger todos os subdomínios, você digitaria '__*__' como o subdomínio.

7. Em seguida, crie um registro CNAME com um alias de **awverify**e um domínio canônico de **awverify.mysite.azurewebsites.net** que você tiver obtido anteriormente.

	> [AZURE.NOTE]Embora um alias da awverify possa funcionar para alguns registradores, outras pessoas podem exigir o nome de domínio de alias completo de awverify.www.customdomainname.com ou awverify.customdomainname.com.

	Por exemplo, o seguinte criará um registro CNAME que o Azure pode usar para verificar a configuração de registro A.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome do host/Subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>awverify</td>
<td>awverify.contoso.azurewebsites.NET</td>
</tr>
</table>

> [AZURE.NOTE]Pode levar algum tempo para o CNAME awverify propagar através do sistema DNS. Você não pode definir o nome de domínio personalizado definido pelo registro A para o site até que o CNAME awverify tenha sido propagado. É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###Adicionar o nome de domínio para seu site

Após o registro CNAME **awverify** para o nome de domínio tiver sido propagado, você pode associar o domínio personalizado definido pelo registro A ao seu site. Você pode adicionar o nome de domínio personalizado definido pelo registro A ao seu site, por meio da CLI do Azure ou então pelo uso do Portal de Gerenciamento do Azure.

**Para adicionar um nome de domínio usando a CLI do Azure (Interface de linha de comando do Azure)**

Instale e configure a [CLI do Azure](/manage/install-and-configure-cli/) e, em seguida, use o seguinte comando:

	azure site domain add customdomain yoursitename

Por exemplo, o seguinte adicionará um nome de domínio personalizado de **contoso.com** para o site **contoso.azurewebsite.net**:

	azure site domain add contoso.com contoso

Você pode confirmar se o nome de domínio personalizado foi adicionado ao site usando o seguinte comando:

	azure site domain list yoursitename

A lista retornada por este comando deve conter o nome de domínio personalizado, bem como a entrada **.azurewebsite.net** padrão.

**Adicionar um nome de domínio usando o Portal de Gerenciamento do Azure**

1. No seu navegador, abra o [Portal de Gerenciamento do Azure][portal].

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar Domínios** na parte inferior da página.

	![][setcname2]

6. Na caixa de texto **NOMES DE DOMÍNIO**, digite o nome do domínio configurado.

	![][setcname3]

6. Clique na marca de seleção para aceitar o nome de domínio.

Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configurar** do seu site.

> [AZURE.NOTE]Depois de ter adicionado o nome de domínio personalizado definido pelo registro A para seu site, você pode remover o registro CNAME awverify usando as ferramentas fornecidas pelo seu registrador. No entanto, se desejar adicionar outro registro A no futuro, você terá que recriar o registro awverify antes de associar o novo nome de domínio definido pelo novo registro A ao site.

## Próximas etapas

-   [Como gerenciar sites](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurar um certificado SSL para os sites](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]: #bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../article/app-service-web/web-sites-traffic-manager.md
[addendpoint]: http://msdn.microsoft.com/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

<!---HONumber=July15_HO4-->