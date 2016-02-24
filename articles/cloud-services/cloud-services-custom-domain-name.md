<properties
	pageTitle="Configurar um nome de domínio personalizado nos Serviços de Nuvem | Microsoft Azure"
	description="Saiba como expor seus dados ou seu aplicativo do Azure em um domínio personalizado definindo as configurações de DNS."
	services="cloud-services"
	documentationCenter=".net"
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="adegeo"/>

# Configurando um nome de domínio personalizado para um serviço de nuvem do Azure

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-custom-domain-name-portal.md)
- [Azure classic portal](cloud-services-custom-domain-name.md)


Quando você cria um Serviço de Nuvem, o Azure o atribui a um subdomínio do cloudapp.net. Por exemplo, se o Serviço de Nuvem for chamado "contoso", os usuários poderão acessar seu aplicativo em uma URL como http://contoso.cloudapp.net. O Azure também fornece um endereço IP virtual.

No entanto, você também pode expor sua aplicação em seu próprio nome de domínio, como contoso.com. Este artigo explica como reservar ou configurar um nome de domínio personalizado para funções Web do Serviço de Nuvem.

Você já entendeu o que são os registros CNAME e A? [Pule a explicação](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Comece a trabalhar com mais agilidade: use o NOVO [guia passo a passo do Azure](http://support.microsoft.com/kb/2990804)! Ele acelera a associação de um nome de domínio personalizado E a proteção da comunicação (SSL) com os Serviços de Nuvem do Azure ou Sites do Azure.

<p/>

> [AZURE.NOTE]
> Os procedimentos nesta tarefa se aplicam aos Serviços de Nuvem do Azure. Para os Serviços de Aplicativos, consulte [isto](../app-service-web/web-sites-custom-domain-name.md). Para as contas de armazenamento, consulte [isto](../storage/storage-custom-domain-name.md).


## Entender os registros CNAME e A

Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), de qualquer forma, cada um deles funciona de modo diferente. Quando você usa registros com serviços de nuvem do Azure, precisa fazer algumas considerações específicas antes de decidir qual deles usar.

### Registro CNAME ou de alias

Um registro CNAME mapeia um domínio *específico*, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o nome de domínio **[myapp].cloudapp.net** do seu aplicativo hospedado no Azure. Uma vez criado, o CNAME cria um alias para o **[myapp].cloudapp.net**. A entrada CNAME resolverá o endereço IP do seu serviço **[myapp].cloudapp.net** automaticamente. Portanto, se o endereço IP do serviço de nuvem for alterado, nenhuma ação será necessária.

> [AZURE.NOTE]
> Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como www.contoso.com, e não nomes de raiz, como contoso.com. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida pelo seu registrador, [a entrada da Wikipédia sobre o registro CNAME](http://en.wikipedia.org/wiki/CNAME_record), ou do documento de implementação e especificação [IETF Domain Names](http://tools.ietf.org/html/rfc1035).

### Registro A

Um registro A mapeia um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga*, como ***.contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registro A em relação a um registro CNAME é que você pode ter uma entrada que usa um curinga, como ***.contoso.com**, que lidaria com as solicitações com vários subdomínios, como **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE]
> Uma vez que um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações ao endereço IP do seu serviço de nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implantar em um slot vazio (produção ou preparo.) Se você excluir a implantação para o slot, o endereço IP será liberado pelo Azure e quaisquer implantações futuras no slot poderão receber um novo endereço IP.
>
> Convenientemente, o endereço IP do slot de uma determinada implantação (de produção ou de preparo) é mantido durante a troca entre implantações de preparo e de produção ou durante a execução de uma atualização in-loco de uma implantação existente. Para saber mais sobre a execução dessas ações, consulte [Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md).


## Adicionar um registro CNAME para seu domínio personalizado

Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para localizar o nome de domínio **.cloudapp.net** atribuído ao seu serviço de nuvem.

    * Faça logon no [portal clássico do Azure], selecione o seu serviço de nuvem, selecione **Painel** e localize a entrada **URL do Site** na seção **visualização rápida**.
    
        ![seção rapidamente mostrando a URL do site][csurl]
    
        **OR**
    
    * Instale e configure o [Azure Powershell](../install-configure-powershell.md) e use o seguinte comando:
        
        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Salve o nome de domínio usado na URL retornada por qualquer método, pois você precisará dele durante a criação de um registro CNAME.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nomes**.

2.  Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias**, ou **Subdomínios**.

3.  Você também deverá fornecer o alias do domínio ou do subdomínio para o CNAME, como **www**, se desejar criar um alias para **www.customdomain.com**. Se você deseja criar um alias para o domínio raiz, ele pode estar listado como o símbolo **'@**' nas ferramentas de DNS do registrador.

4. Em seguida, você deve fornecer um nome do host canônico, que, neste caso, é o domínio **cloudapp.net** do seu aplicativo.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado de seu aplicativo implantado:

| Alias/Nome do host/Subdomínio | Domínio canônico |
| ------------------------- | -------------------- |
| www | contoso.cloudapp.net |

Um visitante de **www.contoso.com** nunca verá o host verdadeiro (contoso.cloudapp.net) e, portanto, o processo de encaminhamento será invisível ao usuário final.

> [AZURE.NOTE]
> O exemplo acima aplica-se somente ao tráfego no subdomínio **www**. Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se quiser direcionar tráfego de subdomínios, como *.contoso.com, para o endereço cloudapp.net, você pode configurar uma entrada **Redirecionamento de URL** ou **Encaminhamento de URL** em suas configurações DNS, ou criar um registro A.


## Adicionar um registro A ao seu domínio personalizado

Para criar um registro, primeiro você deve encontrar o endereço IP do seu serviço de nuvem. Então, em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.
    
    * Faça logon no [portal clássico do Azure], selecione o seu serviço de nuvem, selecione **Painel** e localize a entrada **Endereço VIP (IP virtual) público** na seção **visualização rápida**.
    
        ![seção rapidamente mostrando a VIP][vip]
    
        **OR**
    
    * Instale e configure o [Azure Powershell](../install-configure-powershell.md) e use o seguinte comando:
    
        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Se você tiver vários pontos de extremidade associados ao seu serviço de nuvem, você receberá várias linhas que contém o endereço IP, mas tudo deve exibir o mesmo endereço.
    
    Salve o endereço IP, pois você precisará dele durante a criação de um registro.

1.  Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nomes**.

2.  Agora, encontre onde você pode selecionar ou inserir registros A. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas.

3. Selecione ou digite o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se você deseja criar um alias para **www.customdomain.com**. Se você desejar criar uma entrada curinga para todos os subdomínios, digite '__*__'. Ele abrange todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.

    Se você deseja criar um registro A para o domínio raiz, ele poderá estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.

4. Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

Por exemplo, o seguinte registro A encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do seu aplicativo implantado:

| Nome do host/Subdomínio | Endereço IP |
| ------------------- | -------------- |
| @ | 137\.135.70.239 |


Este exemplo demonstra como criar um registro A para o domínio raiz. Se você desejar criar uma entrada curinga para abranger todos os subdomínios, você digitaria '__*__' como o subdomínio.

>[AZURE.WARNING]
> Endereços IP no Azure são dinâmicos por padrão. Você provavelmente desejará usar um [endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) para garantir que seu endereço IP não seja alterado.

## Próximas etapas

* [Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md)
* [Como mapear o conteúdo da CDN para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate.md).




[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[portal clássico do Azure]: https://manage.windowsazure.com
[Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[vip]: ./media/cloud-services-custom-domain-name/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name/csurl.png
 

<!---HONumber=AcomDC_0121_2016-->