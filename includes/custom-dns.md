# Configurando um nome de domínio personalizado para um serviço de nuvem do Azure
> [!NOTE]
> Obtendo mais rapidez - use o NOVO [passo a passo guiado do Azure](http://support.microsoft.com/kb/2990804)!  Ele acelera a associação de um nome de domínio personalizado E a proteção da comunicação (SSL) com os Serviços de Nuvem do Azure ou Sites do Azure.
> 
> 

Quando você cria um aplicativo no Azure, o Azure fornece um subdomínio do domínio cloudapp.net para que seus usuários possam acessar o aplicativo em uma URL como http://&lt;*myapp*>.cloudapp.net. No entanto, você também pode expor sua aplicação em seu próprio nome de domínio, como contoso.com.

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam aos Serviços de Nuvem do Azure. Para contas de armazenamento, consulte [Configurando um nome de domínio personalizado para uma conta de armazenamento do Azure](../articles/storage/storage-custom-domain-name.md). Para sites, consulte [Configurando um nome de domínio personalizado para um site do Azure](../articles/app-service-web/web-sites-custom-domain-name.md).
> 
> 

## Entender os registros CNAME e A
Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), de qualquer forma, cada um deles funciona de modo diferente. Quando você usa registros com serviços de nuvem do Azure, precisa fazer algumas considerações específicas antes de decidir qual deles usar.

### Registro CNAME ou de alias
Um registro CNAME mapeia um domínio *specific*, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o nome de domínio **&lt;meuaplicativo>.cloudapp.net** do seu aplicativo hospedado no Azure. Uma vez criado, o CNAME cria um alias para o **&lt;meuaplicativo>. cloudapp.net**. A entrada CNAME resolverá o endereço IP do seu serviço **&lt;meuaplicativo>. cloudapp.net** automaticamente; portanto, se o endereço IP do serviço de nuvem for alterado, nenhuma ação será necessária.

> [!NOTE]
> Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME como www.contoso.com, e não ao usar nomes raiz como contoso.com. Para mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registro CNAME</a> ou o documento <a href="http://tools.ietf.org/html/rfc1035">Nomes de Domínio IETF - Implementação e Especificação</a>.
> 
> 

### Registro A
Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *or a wildcard domain* como ***.contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, a principal vantagem de um registro A em relação a um registro CNAME é que você pode ter uma entrada que usa um caractere curinga, como ***.contoso.com**, que pode identificar solicitações de vários subdomínios como **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [!NOTE]
> Uma vez que um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações ao endereço IP do seu serviço de nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implantar em um slot vazio (produção ou preparo.) Se você excluir a implantação para o slot, o endereço IP será liberado pelo Azure e quaisquer implantações futuras no slot poderão receber um novo endereço IP.
> 
> Convenientemente, o endereço IP do slot de uma determinada implantação (de produção ou de preparo) é mantido durante a troca entre implantações de preparo e de produção ou durante a execução de uma atualização in-loco de uma implantação existente. Para obter mais informações sobre a permuta dos VIPs, consulte [Como gerenciar Serviços de Nuvem](../articles/cloud-services/cloud-services-how-to-manage.md).
> 
> 

## Adicionar um registro CNAME para seu domínio personalizado
Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para localizar o nome de domínio **.cloudapp.net** atribuído ao seu serviço de nuvem.
   
   * Faça logon no [Portal de Gerenciamento do Azure], selecione o seu serviço de nuvem, selecione **Painel** e localize a entrada **URL do Site** na seção **visualização rápida**.
     
           ![quick glance section showing the site URL][csurl]
   * Instalar e configurar o [Powershell do Azure](../articles/install-configure-powershell.md)e, em seguida, use o seguinte comando:
     
     Get-AzureDeployment -ServiceName yourservicename | Select Url
   
   Salve o nome de domínio usado na URL retornada por qualquer método, pois você precisará dele durante a criação de um registro CNAME.
2. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**.
3. Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve olhar para as palavras **CNAME**, **Alias** ou **Subdomínios**.
4. Você também deverá fornecer o alias do domínio ou do subdomínio para o CNAME, como **www**, se desejar criar um alias para **www.customdomain.com**. Se você deseja criar um alias para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.
5. Em seguida, você deve fornecer um nome do host canônico, que, neste caso, é o domínio **cloudapp.net** do seu aplicativo.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado de seu aplicativo implantado:

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

Um visitante de **www.contoso.com** nunca visualizará o verdadeiro host
(contoso.cloudapp.net), para que o processo de encaminhamento seja invisível para o
usuário final.

> [!NOTE]
> O exemplo acima aplica-se somente ao tráfego no subdomínio <strong>www</strong>. Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se quiser direcionar tráfego de subdomínios, como *.contoso.com, para o endereço cloudapp.net, você pode configurar uma entrada de <strong>Redirecionamento de URL</strong> ou <strong>Encaminhamento de URL</strong> em suas configurações DNS, ou criar um registro A.
> 
> 

## Adicionar um registro A para seu domínio personalizado
Para criar um registro, primeiro você deve encontrar o endereço IP do seu serviço de nuvem. Então, em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.
   
   * Faça logon no [Portal de Gerenciamento do Azure], selecione seu serviço de nuvem, selecione **Painel**e localize a entrada **endereço IP Virtual Público (VIP)** na seção **visão rápida**.
     
            ![quick glance section showing the VIP][vip]
   * Instalar e configurar o [Powershell do Azure](../articles/install-configure-powershell.md)e, em seguida, use o seguinte comando:
     
      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
     
     Se você tiver vários pontos de extremidade associados ao seu serviço de nuvem, você receberá várias linhas que contém o endereço IP, mas tudo deve exibir o mesmo endereço.
   
   Salve o endereço IP, pois você precisará dele durante a criação de um registro.
2. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**.
3. Agora, encontre onde você pode selecionar ou inserir registros A. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas.
4. Selecione ou digite o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se você deseja criar um alias para **www.customdomain.com**. Se você desejar criar uma entrada curinga para todos os subdomínios, digite '*****'. Isso abrangerá todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**, e **www.customdomain.com**.
   
   Se você deseja criar um registro A para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.
5. Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

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

Este exemplo demonstra como criar um registro A para o domínio raiz. Se você deseja criar uma entrada curinga para abranger todos os subdomínios, você deve digitar '*****' como o subdomínio.

## Próximas etapas
* [Como gerenciar serviços de nuvem](../articles/cloud-services/cloud-services-how-to-manage.md)
* [Como mapear o conteúdo da CDN para um domínio personalizado][Como mapear o conteúdo da CDN para um domínio personalizado]

[Expor seu aplicativo em um domínio personalizado]: #access-app
[Adicionar um registro CNAME para seu domínio personalizado]: #add-cname
[Expor seus dados em um domínio personalizado]: #access-data
[Trocas de VIP]: http://msdn.microsoft.com/library/ee517253.aspx
[Criar um registro CNAME que associe o subdomínio à conta de armazenamento]: #create-cname
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Caixa de diálogo Validar Domínio Personalizado]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[Como mapear o conteúdo da CDN para um domínio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx
[vip]: ./media/custom-dns/csvip.png
[csurl]: ./media/custom-dns/csurl.png

<!--HONumber=49-->