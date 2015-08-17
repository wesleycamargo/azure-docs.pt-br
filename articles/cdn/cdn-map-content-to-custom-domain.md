<properties 
	 pageTitle="Como mapear o conteúdo da CDN (rede de distribuição de conteúdo) para um domínio personalizado" 
	 description="Este tópico demonstra como mapear conteúdo da CDN para um domínio personalizado." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="07/07/2015" 
	 ms.author="mazha"/>

#Como mapear o domínio personalizado para o ponto de extremidade da CDN (Rede de Distribuição de Conteúdo)

Você pode mapear um domínio personalizado para um ponto de extremidade da CDN para usar seu próprio nome de domínio em URLs para conteúdo armazenado em cache, em vez de usar o ponto de extremidade da CDN fornecido a você.

Há duas maneiras de mapear seu domínio personalizado para um ponto de extremidade da CDN.

1. **Criar um registro CNAME com seu registrador de domínios e mapear seu domínio e subdomínio personalizados para o ponto de extremidade da CDN** 
	
	Um Registro CNAME é um recurso do DNS que mapeia um domínio de origem a um domínio de destino. Nesse caso, o domínio de origem corresponde a seu domínio e subdomínio personalizados (o subdomínio é sempre necessário). O domínio de destino é o ponto de extremidade da CDN.

	O processo de mapeamento de seu domínio personalizado ao ponto de extremidade da CDN pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você estiver registrando o domínio no Portal de Gerenciamento do Azure. 
2. **Adicionar uma etapa de registro intermediário com cdnverify do Azure**

	Se atualmente o seu domínio personalizado oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que não permita um tempo de inatividade, então você pode usar o subdomínio **cdnverify** do Azure para fornecer uma etapa intermediária de registro para que os usuários possam acessar seu domínio durante o mapeamento do DNS.

> AZURE.NOTE
> 
-	Você deve criar um registro CNAME com seu registrador de domínios para mapear o domínio para o ponto de extremidade da CDN. Os registros CNAME mapeiam subdomínios específicos como www.meudominio.com ou meublog.meudominio.com. Não é possível mapear um registro CNAME para um domínio raiz, como meudominio.com.
-	Um subdomínio só pode ser associado a um ponto de extremidade da CDN. O registro CNAME que você criar roteará todo o tráfego endereçado ao subdomínio para o ponto de extremidade especificado. Por exemplo Se associar www.meudominio.com ao ponto de extremidade da CDN, você não poderá associá-lo a outros pontos de extremidade do Azure, como um ponto de extremidade de conta de armazenamento ou um ponto de extremidade de serviço de nuvem. No entanto, você pode usar outros subdomínios do mesmo domínio para pontos de extremidade de serviço diferentes. Você também pode mapear outros subdomínios para o mesmo ponto de extremidade da CDN.

Os procedimentos neste tópico mostram como:

-	[Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure](#subheading1)
-	[Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure usando o subdomínio intermediário cdnverify](#subheading2)
-	[Verificar se o subdomínio personalizado faz referência ao seu ponto de extremidade da CDN](#subheading3) 

##<a name="subheading1"></a>Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	No painel de navegação, clique em **CDN**.
3.	No modo de exibição de lista, clique no nome do ponto de extremidade da CDN ao qual você deseja associar o subdomínio, para navegar até a página de detalhes desse ponto de extremidade.
4.	Na faixa de opções, clique em **Gerenciar Domínios** para exibir a caixa de diálogo **Gerenciar domínios personalizados**. No texto da caixa de diálogo, você verá o nome do host, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME. O formato do nome do endereço do host aparecerá como **az#####.vo.msecnd.net** (em que **az#####** é o identificador de ponto de extremidade da CDN). Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME. Para este procedimento, ignore o texto que se refere ao subdomínio **cdnverify**.
5.	Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6.	Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras CNAME, Alias ou Subdomínios.
7.	Crie um novo registro CNAME que mapeie o subdomínio escolhido (por exemplo, **www** ou **cdn**) para o nome de host fornecido na caixa de diálogo **Gerenciar domínios personalizados**.
8.	Retorne à caixa de diálogo **Gerenciar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato www.meudominio.com ou cdn.meudominio.com.   

	O Azure verificará se o registro CNAME existe para o nome de domínio que você digitou. Se o CNAME estiver correto, seu domínio personalizado será validado e estará pronto para uso com seu conteúdo da CDN.

	Observe que, em alguns casos, pode levar algum tempo para que o registro CNAME se propague para servidores de nomes na Internet. Se seu domínio não for validado imediatamente e você achar que o registro CNAME está correto, aguarde alguns minutos e tente novamente.

##<a name="subheading2"></a>Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure usando o subdomínio intermediário cdnverify  


1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	No painel de navegação, clique em **CDN**.
3.	No modo de exibição de lista, clique no nome do ponto de extremidade da CDN ao qual você deseja associar o subdomínio, para navegar até a página de detalhes desse ponto de extremidade.
4.	Na faixa de opções, clique em **Gerenciar Domínios** para exibir a caixa de diálogo **Gerenciar domínios personalizados**. No texto da caixa de diálogo, você verá o nome do host, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME usando o subdomínio intermediário **cdnverify** . O formato do nome do endereço do host aparecerá como **cdnverify.az#####.vo.msecnd.net**. Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME.
5.	Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6.	Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
7.	Crie um novo registro CNAME e forneça um alias de subdomínio que inclua o subdomínio **cdnverify**. Por exemplo, o subdomínio especificado estará no formato **cdnverify.www** ou **cdnverify.cdn**. Forneça o nome de host, que é o ponto de extremidade da CDN, no formato **cdnverify.az#####.vo.msecnd.net** (em que **az#####** é o identificador de ponto de extremidade da CDN). O formato do nome do host é fornecido na caixa de diálogo **Gerenciar domínios personalizados**.
8.	Retorne à caixa de diálogo **Gerenciar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato **www.meudominio.com** ou **cdn.meudominio.com**. Observe que, nessa etapa, você não precisa prefaciar o subdomínio com **cdnverify**.  

	O Azure verificará se o registro CNAME existe para o nome de domínio cdnverify que você inseriu.
9.	Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para seu ponto de extremidade da CDN. Para concluir o processo, volte ao site do registrador do DNS e crie outro Registro CNAME que mapeie seu subdomínio para o ponto de extremidade da CDN. Por exemplo, especifique o subdomínio como **www** ou **cdn** e o nome de host como **az#####.vo.msecnd.net**. Com essa etapa, o registro do seu domínio personalizado está concluído. 
10.	Por fim, você pode excluir o Registro CNAME criado usando **cdnverify**, já que ele era necessário apenas como uma etapa intermediária.  


##<a name="subheading3"></a>Verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN

-	Depois de concluir o registro de seu domínio personalizado, você pode acessar o conteúdo armazenado em cache no ponto de extremidade da CDN usando o domínio personalizado. Primeiro, verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN armazenará conteúdo em cache em contêineres de blobs públicos. Para testar o domínio personalizado, verifique se o contêiner está definido para permitir acesso público e se contém pelo menos um blob.
-	Em seu navegador, navegue até o endereço do blob usando o domínio personalizado. Por exemplo, se seu domínio personalizado for **www.meudominio.com**, a URL para um blob armazenado em cache será semelhante à seguinte URL:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Se o ponto de extremidade da CDN estiver associado a um serviço de nuvem, o endereço de seu conteúdo armazenado em cache será semelhante à seguinte URL:

		http://www.mydomain.com/cdn/mycloudservice

##Consulte também


[Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=August15_HO6-->