# Configurando um nome de domínio personalizado para um serviço em nuvem do Azure

Quando você cria um site, o Azure fornece um subdomínio amigável no domínio azurewebsites.net para que os usuários possam acessar o seu site usando uma URL como http://&lt;*myapp*>.cloudapp.net. No entanto, você também pode expor seu aplicativo em seu próprio nome de domínio, por exemplo, contoso.com.

> [WACOM.NOTE] 
> Os procedimentos nesta tarefa se aplicam aos serviços de nuvem do Azure. Para contas de armazenamento, consulte [Configurar um nome de domínio personalizado para um serviço de nuvem ou conta de armazenamento do Azure](../storage-custom-domain-name/). Para obter sites, consulte [Configurando um nome de domínio personalizado para um site do Azure](../web-sites-custom-domain-name/).

Neste artigo:

-   [Noções básicas sobre os registros CNAME e A](#access-app)
-   [Adicionar um registro CNAME para seu domínio personalizado](#add-cname)
-   [Adicionar um registro A para seu domínio personalizado](#add-aname)

<h2><a name="access-app"></a>Entenda os registros CNAME e A</h2>

Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), de qualquer forma, cada um deles funciona de modo diferente. Há também algumas considerações específicas ao usar registros com serviços de nuvem do Azure que você deve considerar antes de decidir qual deles usar.

###Registro CNAME ou de alias

Um registro CNAME mapeia um *domínio* específico, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o **&lt;myapp>.cloudapp.net** o nome de domínio da aplicação hospedada do Azure. Uma vez criado, o CNAME cria um alias para o **&lt;myapp>.cloudapp.net**. A entrada CNAME resolverá o endereço IP do seu **&lt;myapp>.cloudapp.net** serviço automaticamente, portanto, se o endereço IP do serviço em nuvem for alterado, você não precisa realizar qualquer ação.

> [WACOM.NOTE] 
> Alguns registradores de domínio só permitem que você mapeie subdomínios ao usar um registro CNAME, como www.contoso.com, e não os nomes raiz, por exemplo, contoso.com. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida pelo seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia sobre o registro CNAME</a>, ou do documento de implementação e especificação <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names</a>.

###Registro A

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga* como **\*.contoso.com**, para um endereço IP. No caso de um serviço em nuvem do Azure, o IP virtual do serviço. Portanto, a principal vantagem de um registro A em relação a um registro CNAME é que você pode ter uma entrada que usa um caractere curinga, como ***.contoso.com**, que pode identificar solicitações de vários subdomínios como **mail.contoso.com**, **login.contoso.com**, ou **www.contso.com**.

> [WACOM.NOTE]
>Uma vez que um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações ao endereço IP do seu serviço em nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implantar em um slot vazio (produção ou preparo.) Se você excluir a implantação do slot, o endereço IP é liberado pelo Azure e as futuras implantações no slot podem receber um novo endereço IP.
> 
> Convenientemente, o endereço IP de um slot de determinada implantação (produção ou preparação) é mantido durante a troca entre preparo e implantações de produção ou realizar uma atualização in-loco de uma implantação existente. Para obter mais informações sobre a permuta dos VIPs, consulte [Como gerenciar Serviços de Nuvem (a página pode estar em inglês)](../cloud-services-how-to-manage/).


<h2><a name="add-cname"></a>-   Adicionar um registro CNAME para seu domínio personalizado</h2>

Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para localizar o **.cloudapp.net** nome de domínio atribuído ao serviço em nuvem.

  * Logon para o [Portal de gerenciamento do Azure], selecione o seu serviço em nuvem, selecione **Painel**e localize a entrada da **URL do site** na seção **Visão rápida**.

  		  ![seção rapidamente mostrando a URL do site][csurl]

  * Instale e configure o [Azure Powershell](../install-configure-powershell/), em seguida, use o seguinte comando:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Salve o nome de domínio usado na URL retornada por qualquer método, pois você precisará dele durante a criação de um registro CNAME.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

2.  Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias**, ou **Subdomínios**.

3.  Você deve também fornecer o domínio ou subdomínio alias para CNAME, como **Web** se você deseja criar um alias para **www.customdomain.com** Se você deseja criar um alias para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.

4. Em seguida, você deve fornecer um nome de host canônico, seu aplicativo **cloudapp.net** domínio neste caso.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio de sua aplicação implantada:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome do host/Subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.cloudapp.NET</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca visualizará o host verdadeiro
(contoso.cloudapp.net), para que o processo de encaminhamento seja invisível para o
usuário final.

> [WACOM.NOTE]
> O exemplo acima aplica-se somente ao tráfego no subdomínio <strong>www</strong>. Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se quiser direcionar tráfego de subdomínios, como *.contoso.com, para o endereço cloudapp.net, você pode configurar uma entrada de <strong>URL de Redirecionamento</strong> ou <strong>Encaminhamento de URL</strong> em suas configurações DNS, ou criar um registro.


<h2><a name="add-aname"></a>Adicionar um registro A para seu domínio personalizado</h2>

Para criar um registro, primeiro você deve encontrar o endereço IP do seu serviço em nuvem. Então, em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.

  * Logon para o [Portal de gerenciamento do Azure], selecione o seu serviço em nuvem, selecione **Painel**e localize a entrada do **endereço IP Público Virtual (VIP)** na seção **Visão rápida**.

   		 ![seção rapidamente mostrando a VIP][vip]

  * Instale e configure o [Azure Powershell](../install-configure-powershell/), em seguida, use o seguinte comando:

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    Se você tiver vários pontos de extremidade associados ao seu serviço de nuvem, você receberá várias linhas que contém o endereço IP, mas tudo deve exibir o mesmo endereço.

  Salve o endereço IP, pois você precisará dele durante a criação de um registro.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

2.  Agora, encontre onde você pode selecionar ou inserir registros A. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas.

3. Selecione ou digite o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se você deseja criar um alias para **www.customdomain.com**. Se você deseja criar uma entrada de curinga para todos os subdomínios, digite '__*__'. Ela abrangerá todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**, e **www.customdomain.com**.

  Se você deseja criar um registro A para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.

4. Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

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
</table>

Este exemplo demonstra como criar um registro A para o domínio raiz. Se você deseja criar uma entrada de caractere curinga para cobrir todos os subdomínios, você digitaria '__*__' como o subdomínio.

## Próximas etapas

-   [Como gerenciar serviços de nuvem](../cloud-services-how-to-manage/)
-   [Como mapear o conteúdo CDN para um domínio personalizado][]

  [Expor seu aplicativo em um domínio personalizado]: #access-app
  [Adicionar um registro CNAME para seu domínio personalizado]: #add-cname
  [Expor seus dados em um domínio personalizado]: #access-data
  [Trocas de VIP]: http://msdn.microsoft.com/pt-BR/library/ee517253.aspx
   [Criar um registro CNAME que associe o subdomínio à conta de armazenamento]: #create-cname
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Caixa de diálogo Validar Domínio Personalizado]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [Como mapear o conteúdo CDN para um domínio personalizado]: http://msdn.microsoft.com/pt-BR/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
  [csurl]: ./media/custom-dns/csurl.png

