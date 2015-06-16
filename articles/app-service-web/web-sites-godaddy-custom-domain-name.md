<properties 
	pageTitle="Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (GoDaddy)" 
	description="Saiba como usar um nome de domínio do GoDaddy com Aplicativos Web do Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wadepickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (GoDaddy)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [Introdução](../../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Go Daddy](https://godaddy.com) com o [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS](#understanding-records)
-   [Adicionar um registro DNS a seu domínio personalizado](#bkmk_configurecname)
-   [Habilitar o domínio no aplicativo Web](#enabledomain)

<a name="understanding-records"></a>
##Compreendendo os registros DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]


<a name="bkmk_configurecname"></a>
## Adicionar um registro DNS a seu domínio personalizado 

Para associar seu domínio personalizado a um aplicativo Web no Serviço de Aplicativo, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1. Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar meus domínios**. Por último, selecione o menu suspenso para o nome de domínio que você deseje usar com seu aplicativo Web do Azure e selecione **Gerenciar DNS**.

	![página de domínio personalizado para GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. Na página **Detalhes do domínio**, role até guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio.

	![Guia Arquivo de Zona DNS](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	Selecione **Adicionar registro** para adicionar um registro existente.

	Para **editar** um registro existente, selecione o ícone de lápis e papel do lado do registro.

	> [AZURE.NOTE]Antes de adicionar os novos registros, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

4. Ao adicionar um registro, você deve primeiro, selecionar o tipo de registro.

	![selecione o tipo de registro](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	Em seguida, você deve fornecer o **Host** (o domínio ou subdomínio personalizado) e o que **Aponta**.

	![adicionar um registro de zona](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Ao adicionar um **registro (host) A** - você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu aplicativo Web do Azure.
	
		> [AZURE.NOTE]Ao usar um registro (host) A, você também deve adicionar um registro CNAME com a seguinte configuração:
		> 
		> * Um **Host** com valor **awverify** que **Aponta para** um valor **awverify.&lt;NomeDoSeuAplicativo&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A

	* Ao adicionar um **registro CNAME (alias)** - você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do aplicativo Web do Azure. Por exemplo, **contoso.azurwebsites.net**.


5. Ao concluir a adição ou a modificação dos registros, clique em **Concluir** para salvar as alterações.

<a name="enabledomain"></a>
## Habilitar o nome do domínio no seu aplicativo Web 

[AZURE.INCLUDE [modos](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=54--> 