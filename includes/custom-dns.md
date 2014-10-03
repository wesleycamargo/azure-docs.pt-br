# Configurando um nome de domínio personalizado para um serviço de nuvem do Azure

Quando você cria um aplicativo no Azure, ele fornece um subdomínio no domínio cloudapp.net de modo que seus usuários possam acessar seu aplicativo em uma URL como [http://\<\*myapp][]\*\>.cloudapp.net. No entanto, você também pode expor seu aplicativo em seu próprio nome de domínio, por exemplo, contoso.com.

> [WACOM.NOTE]
> Os procedimentos nesta tarefa se aplicam aos Serviços de Nuvem do Azure. Para contas de armazenamento, consulte [Configurando um nome de domínio personalizado para uma conta de armazenamento do Azure][]. Para sites, consulte [Configurando um nome de domínio personalizado para um site do Azure][].

Neste artigo:

-   [Noções básicas sobre os registros CNAME e A][]
-   [Adicionar um registro CNAME para seu domínio personalizado][]
-   [Adicionar um registro A para seu domínio personalizado][]

## <a name="access-app"></a>Entender os registros CNAME e A

Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), de qualquer forma, cada um deles funciona de modo diferente. Quando você usa registros com serviços de nuvem do Azure, precisa fazer algumas considerações específicas antes de decidir qual deles usar.

### Registro CNAME ou de alias

Um registro CNAME mapeia um domínio *específico*, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o nome de domínio **\<meuaplicativo\>.cloudapp.net** do seu aplicativo hospedado no Azure. Uma vez criado, o CNAME cria um alias para o **\<meuaplicativo\>.cloudapp.net**. A entrada CNAME resolverá o endereço IP do seu serviço **\<meuaplicativo\>.cloudapp.net** automaticamente, portanto, se o endereço IP do serviço de nuvem for alterado, nenhuma ação será necessária.

> [WACOM.NOTE]
> Alguns registradores de domínios permitem que você mapeie subdomínios apenas ao usar um registro CNAME, como www.contoso.com, e não nomes raiz, como contoso.com. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida pelo seu registrador, [a entrada da Wikipédia no registro CNAME][], ou o documento [Nomes de domínio IETF - Implementação e especificação][].

### Registro A

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga*, como **\*.contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registro A em relação a um registro CNAME é que você pode ter uma entrada que usa um curinga, como **.contoso.com**, que lidaria com solicitações para vários subdomínios, como **mail.contoso.com**, **login.contoso.com**, ou **www.contoso.com**.

> [WACOM.NOTE]
> Uma vez que um registro A é mapeado para um endereço IP estático, ele não pode resolver automaticamente as alterações no endereço IP do seu serviço de nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implantar em um slot vazio (produção ou preparo.) Se você excluir a implantação para o slot, o endereço IP será liberado pelo Azure e quaisquer implantações futuras no slot poderão receber um novo endereço IP.
>
> Convenientemente, o endereço IP do slot de uma determinada implantação (de produção ou de preparo) é mantido durante a troca entre implantações de preparo e de produção ou durante a execução de uma atualização in-loco de uma implantação existente. Para obter mais informações sobre a permuta dos VIPs, consulte [Como gerenciar Serviços de Nuvem (a página pode estar em inglês)][].

## <a name="add-cname"></a>Adicionar um registro CNAME para seu domínio personalizado

Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1.  Use um dos seguintes métodos para localizar o nome de domínio **.cloudapp.net** atribuído ao seu serviço de nuvem.
 -   Faça logon no [Portal de Gerenciamento do Azure][], selecione o seu serviço de nuvem, selecione **Painel** e localize a entrada **URL do Site** na seção **visualização rápida**.

          ![quick glance section showing the site URL][csurl]

 -   Instale e configure o [Azure Powershell][] e use o seguinte comando:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Salve o nome de domínio usado na URL retornada por qualquer método, pois você precisará dele durante a criação de um registro CNAME.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

2.  Agora, encontre onde você pode selecionar ou inserir CNAMEs. Talvez você tenha que selecionar o tipo de registro em uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias**, ou **Subdomínios**.

3.  Você também deverá fornecer o alias do domínio ou do subdomínio para o CNAME, como **www**, se desejar criar um alias para **www.customdomain.com**. Se você desejar criar um alias para o domínio raiz, ele poderá estar listado como o símbolo '**@**' nas ferramentas de DNS do seu registrador.
4.  Em seguida, você deve fornecer um nome do host canônico, que, neste caso, é o domínio **cloudapp.net** do seu aplicativo.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado de seu aplicativo implantado:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome do host/Subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca visualizará o host verdadeiro
(contoso.cloudapp.net), portanto, o processo de encaminhamento será invisível ao
usuário final.

> [WACOM.NOTE]
> O exemplo acima aplica-se somente ao tráfego no subdomínio **www**. Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se quiser direcionar tráfego de subdomínios, como \*.contoso.com, para o endereço cloudapp.net, você pode configurar uma entrada **Redirecionamento de URL** ou **Encaminhamento de URL** em suas configurações DNS, ou criar um registro A.

## <a name="add-aname"></a>Adicionar um registro A para seu domínio personalizado

Para criar um registro, primeiro você deve encontrar o endereço IP do seu serviço em nuvem. Então, em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos.

1.  Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.

-   Faça logon no [Portal de Gerenciamento do Azure][], selecione o seu serviço de nuvem, selecione **Painel** e localize a entrada **Endereço VIP (IP virtual) público** na seção **visualização rápida**.

         ![quick glance section showing the VIP][vip]

-   Instale e configure o [Azure Powershell][] e use o seguinte comando:

    get-azurevm -servicename yourservicename | get-azureendpoint -VM {$\_.VM} | select Vip

    Se você tiver vários pontos de extremidade associados ao seu serviço de nuvem, você receberá várias linhas que contém o endereço IP, mas tudo deve exibir o mesmo endereço.

Salve o endereço IP, pois você precisará dele durante a criação de um registro.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**.

2.  Agora, encontre onde você pode selecionar ou inserir registros A. Talvez você tenha que selecionar o tipo de registro em uma lista suspensa ou acessar uma página de configurações avançadas.

3.  Selecione ou digite o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se você deseja criar um alias para **www.customdomain.com**. Se você desejar criar uma entrada curinga para todos os subdomínios, digite '\_\_\*\_\_'. Ele abrange todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**

<p>
Se você desejar criar um registro A para o domínio raiz, ele pode estar listado como o símbolo

<script type="text/javascript">
<!--
h='&#42;&#42;';a='&#64;';n='&#x27;&#42;&#42;';e=n+a+h;
document.write('<a h'+'ref'+'="ma'+'ilto'+':'+e+'" clas'+'s="em' + 'ail">'+e+'<\/'+'a'+'>');
// -->
</script>
<noscript>
'\*\* at \*\*

</noscript>
' nas ferramentas de DNS do seu registrador.

</p>
1.  Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

Por exemplo, o seguinte registro A encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do seu aplicativo implantado:

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

Este exemplo demonstra como criar um registro A para o domínio raiz. Se você desejar criar uma entrada curinga para abranger todos os subdomínios, você digitaria '__*__' como o subdomínio.

## Próximas etapas

-   [Como gerenciar serviços de nuvem][Como gerenciar Serviços de Nuvem (a página pode estar em inglês)]
-   [Como mapear o conteúdo CDN para um domínio personalizado][]

  [http://\<\*myapp]: http://<*myapp
  [Configurando um nome de domínio personalizado para uma conta de armazenamento do Azure]: ../storage-custom-domain-name/
  [Configurando um nome de domínio personalizado para um site do Azure]: ../web-sites-custom-domain-name/
  [Noções básicas sobre os registros CNAME e A]: #access-app
  [Adicionar um registro CNAME para seu domínio personalizado]: #add-cname
  [Adicionar um registro A para seu domínio personalizado]: #add-aname
  [a entrada da Wikipédia no registro CNAME]: http://en.wikipedia.org/wiki/CNAME_record
  [Nomes de domínio IETF - Implementação e especificação]: http://tools.ietf.org/html/rfc1035
  [Como gerenciar Serviços de Nuvem (a página pode estar em inglês)]: ../cloud-services-how-to-manage/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Azure Powershell]: ../install-configure-powershell/
  [Como mapear o conteúdo CDN para um domínio personalizado]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx
