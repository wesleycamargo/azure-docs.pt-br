<properties
	pageTitle="Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (GoDaddy)"
	description="Saiba como usar um nome de domínio do GoDaddy com Aplicativos Web do Azure"
	services="app-service"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>

# Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure (adquirido diretamente do GoDaddy)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [introdução](../../includes/custom-dns-web-site-intro.md)]

Se você tiver adquirido o domínio por meio de Aplicativos Web do Serviço de Aplicativo do Azure e consulte a etapa final de [Comprar domínio para aplicativos Web](custom-dns-web-site-buydomains-web-app.md).

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido diretamente de [GoDaddy](https://godaddy.com) com [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

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

	> [AZURE.NOTE] Antes de adicionar os novos registros, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

4. Ao adicionar um registro, você deve primeiro, selecionar o tipo de registro.

	![selecione o tipo de registro](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	Em seguida, você deve fornecer o **Host** (o domínio ou subdomínio personalizado) e o que **Aponta**.

	![adicionar um registro de zona](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Ao adicionar um **registro A (host)** - você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**), * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para* como o endereço IP do seu aplicativo Web do Azure.

	* Ao adicionar um **registro CNAME (alias)** - você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do aplicativo Web do Azure. Por exemplo, **contoso.azurewebsites.net**.

5. Clique em **Adicionar Outro**.
6. Selecione **TXT** como o tipo de registro e especifique um valor de **Host** de **@** e um valor de **Aponta para** de **&lt;yourwebappname&gt;.azurewebsites.net**.

	> [AZURE.NOTE] Esse registro TXT é usado pelo Azure para validar que você possui o domínio descrito pelo registro A do primeiro registro TXT. Depois que o domínio tiver sido mapeado para o aplicativo Web no Portal do Azure, essa entrada do registro TXT poderá ser removida.

5. Ao concluir a adição ou a modificação dos registros, clique em **Concluir** para salvar as alterações.

<a name="enabledomain"></a>
## Habilitar o nome do domínio no seu aplicativo Web

[AZURE.INCLUDE [modos](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!---HONumber=AcomDC_0824_2016-->
