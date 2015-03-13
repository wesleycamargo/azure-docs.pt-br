<properties 
	pageTitle="Configurar um nome de domínio para dados de blob em uma conta de armazenamento | Microsoft Azure" 
	description="Saiba como configurar um domínio personalizado para acessar os dados de blob em uma conta de armazenamento do Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2014" 
	ms.author="tamram"/>


# Configurar um nome de domínio personalizado para dados de blob em uma conta de armazenamento do Azure
Você pode configurar um domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure. O ponto de extremidade padrão para o serviço de Blob é https://<*mystorageaccount*>.blob.core.windows.net. Se você mapear um domínio personalizado e um subdomínio como **www.contoso.com** para o ponto de extremidade do blob para sua conta de armazenamento, logo, os usuários também poderão acessar dados do blob em sua conta de armazenamento usando esse domínio. 


> [AZURE.NOTE]	Os procedimentos nesta tarefa se aplicam a contas de armazenamento do Azure. Para os serviços de nuvem, consulte <a href = "/pt-br/develop/net/common-tasks/custom-dns/">Configurando um nome de domínio personalizado para um serviço em nuvem do Azure</a>; para os Sites, consulte <a href="/pt-br/develop/net/common-tasks/custom-dns-web-site/">Configurando um nome de domínio personalizado para um Site do Azure</a>. 

> [AZURE.NOTE]	Uma conta de Armazenamento Premium não pode ser mapeada para um nome de domínio personalizado. Consulte [Armazenamento premium: Armazenamento de alto desempenho para cargas de trabalho de máquina Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=521898) para obter informações sobre contas de armazenamento premium.

Existem duas maneiras de apontar seu domínio personalizado para o ponto de extremidade do blob de sua conta de armazenamento. A maneira mais simples é criar um Registro CNAME que mapeia seu domínio e subdomínio personalizados ao ponto de extremidade do blob. Um Registro CNAME é um recurso do DNS que mapeia um domínio de origem a um domínio de destino. Nesse caso, o domínio de origem é o seu domínio e subdomínio personalizados - observe que o subdomínio é sempre necessário. O domínio de destino é o ponto de extremidade de serviço Blob.

O processo de mapeamento de seu domínio personalizado ao ponto de extremidade do blob pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você estiver registrando o domínio no Portal de Gerenciamento do Azure. Se atualmente o seu domínio personalizado oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que não permita um tempo de inatividade, então você pode usar o subdomínio **asverify** do Azure para fornecer uma etapa intermediária de registro para que os usuários possam acessar seu domínio durante o mapeamento do DNS.

A tabela a seguir mostra exemplos de URLs para acessar os dados do blob em uma conta de armazenamento denominada **mystorageaccount**. O domínio personalizado registrado para a conta de armazenamento é **www.contoso.com**:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td style="width: 100px;"><strong>Tipo de recurso</strong></td>
			<td><strong>Formatos de URL</strong></td>
		</tr>
		<tr>
			<td>Conta de armazenamento</td>
			<td><strong>URL padrão:</strong> http://mystorageaccount.blob.core.windows.net<br />
			<strong>URL do domínio personalizado:</strong> http://www.contoso.com</td>
		</tr>
		<tr>
			<td>Blob</td>
			<td><strong>URL padrão:</strong> http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br /><strong>URL do domínio personalizado:</strong>
			http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr>
			<td>Contêiner raiz</td>
			<td><strong>URL padrão:</strong> http://mystorageaccount.blob.core.windows.net/myblob 
			<br/>ou<br />
			http://mystorageaccount.blob.core.windows.net/$root/myblob<br />
			<strong>URL do domínio personalizado:</strong> http://www.contoso.com/myblob
			<br/>ou<br />
			http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>

Esta tarefa mostrará como:



- <a href="#register-domain">Registrar um domínio personalizado para sua conta de armazenamento</a>
- <a href="#register-asverify">Registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio asverify intermediário</a>
- <a name="#verify-subdomain">Verificar se o subdomínio personalizado faz referência ao ponto de extremidade de serviço Blob</a>

<h2><a name="register-domain"></a>Registrar um domínio personalizado para sua conta de armazenamento</h2>

Use este procedimento para registrar seu domínio personalizado se você não se importar com a indisponibilidade temporária do domínio para os usuários ou se seu domínio personalizado não estiver atualmente hospedando um aplicativo. 

Se seu domínio personalizado no momento é oferecer suporte a um aplicativo que não pode ter nenhum tempo de inatividade, use o procedimento descrito no <a href="#register-asverify">registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio de intermediário asverify</a>.

Para configurar um nome de domínio personalizado, você deve criar um novo Registro CNAME com seu registrador de domínio. O Registro CNAME especifica um alias para um nome de domínio; nesse caso, ele mapeia o endereço do seu domínio personalizado ao ponto de extremidade de serviço Blob para sua conta de armazenamento.

Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas o conceito
é o mesmo. Observe que muitos pacotes de registro de domínio básico não oferecem a configuração do DNS, portanto, talvez seja necessário atualizar o pacote de registro de domínio antes de criar o Registro CNAME. 

1.  No Portal de Gerenciamento do Azure, navegue até a guia **Armazenamento**.

2.  Na guia **Armazenamento**, clique no nome da conta de armazenamento à qual você deseja mapear o domínio personalizado.

3.  Clique na guia **Configurar**.

4.  Na parte inferior da tela, clique em **Gerenciar Domínio** para exibir o diálogo **Gerenciar Domínio Personalizado**. No texto na parte superior da caixa de diálogo, você visualizará informações sobre como criar o Registro CNAME. Para este procedimento, ignore o texto que se refere ao subdomínio **asverify**.

5.  Faça logon no site do registrador de DNS e acesse a página de
    gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome do
    Domínio**, **DNS** ou **nome do gerenciamento de servidor**.

6.  Localize a seção de gerenciamento de CNAMEs. Talvez você precise ir para uma
    página de configurações avançadas e procurar as palavras **CNAME**, **Alias**,
    ou **subdomínios**.

7.  Crie um novo registro CNAME e forneça um alias de subdomínio, como **www** ou **photos**. Em seguida
    forneça um nome do host, que é o ponto de extremidade do serviço Blob, no formato **minhacontadearmazenamento.blob.core.windows.net** (em que **minhacontadearmazenamento** é o nome da sua conta de armazenamento). O nome do host que será usado é fornecido no texto da caixa de diálogo **Gerenciar Domínio personalizado**.

8.  Depois de criar o registro CNAME, volte ao diálogo **Gerenciar Domínio Personalizado** e insira o nome do seu domínio personalizado, incluindo o subdomínio, no campo **Nome de Domínio Personalizado**. Por exemplo, se o domínio for **contoso.com** e seu subdomínio for **www**, digite **www.contoso.com**; se o seu subdomínio for **fotos**, digite **photos.contoso.com**. Observe que o subdomínio é obrigatório.

9. Clique no botão **Registrar** para registrar seu domínio personalizado. 

	Se o registro for bem-sucedido, a mensagem **Seu domínio personalizado está ativo** será exibida. Os usuários podem agora exibir os dados do blob em seu domínio personalizado, contanto que tenham as permissões adequadas. 

<h2><a name="register-asverify"></a>Registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio asverify intermediário</h2>

Use este procedimento para registrar seu personalizado domínio se o seu domínio personalizado atualmente der suporte a um aplicativo com um SLA que não permita nenhum tempo de inatividade. Criando um CNAME que aponta de asverify.&lt;subdomínio&gt;.&lt;domíniopersonalizado&gt; para asverify.&lt;storageaccount&gt;.blob.core.windows.net, você pode registrar seu domínio com o Azure previamente. Você pode criar um segundo CNAME que aponta de &lt;subdomínio&gt;.&lt;domíniopersonalizado&gt; para &lt;storageaccount&gt;.blob.core.windows.net, no ponto em que o tráfego para seu domínio personalizado será direcionado para o ponto de extremidade do blob.

O subdomínio asverify é um subdomínio especial reconhecido pelo Azure. Ao anexar **asverify** ao seu próprio subdomínio, você permite que o Azure reconheça seu domínio personalizado sem modificar o registro DNS do domínio. Depois que você modificar o registro DNS do domínio, ele será mapeado ao ponto de extremidade do blob sem nenhum tempo de inatividade.

1.  No Portal de Gerenciamento do Azure, navegue até a guia **Armazenamento**.

2.  Na guia **Armazenamento**, clique no nome da conta de armazenamento à qual você deseja mapear o domínio personalizado.

3.  Clique na guia **Configurar**.

4.  Na parte inferior da tela, clique em **Gerenciar Domínio** para exibir o diálogo **Gerenciar Domínio Personalizado**. No texto na parte superior da caixa de diálogo, você visualizará informações sobre como criar o Registro CNAME usando o subdomínio **asverify**.

5.  Faça logon no site do registrador de DNS e acesse a página de
    gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome do
    Domínio**, **DNS** ou **nome do gerenciamento de servidor**.

6.  Localize a seção de gerenciamento de CNAMEs. Talvez você precise ir para uma
    página de configurações avançadas e procurar as palavras **CNAME**, **Alias**,
    ou **subdomínios**.

7.  Crie um novo registro CNAME e forneça um alias de subdomínio que inclui o subdomínio asverify. Por exemplo, o subdomínio especificado estará no formato **asverify.www** ou **asverify.photos**. Em seguida
    forneça um nome do host, que é o ponto de extremidade do serviço Blob, no formato **asverify.minhacontadearmazenamento.blob.core.windows.net** (em que **minhacontadearmazenamento** é o nome da sua conta de armazenamento). O nome do host que será usado é fornecido no texto da caixa de diálogo **Gerenciar Domínio personalizado**.

8.  Depois de criar o registro CNAME, volte ao diálogo **Gerenciar Domínio Personalizado** e insira o nome do seu domínio personalizado no campo **Nome de Domínio Personalizado**. Por exemplo, se o domínio for **contoso.com** e seu subdomínio for **www**, digite **www.contoso.com**; se o seu subdomínio for **fotos**, digite **photos.contoso.com**. Observe que o subdomínio é obrigatório.

9.	Clique na caixa de seleção **Avançado: Use o subdomínio 'asverify' para pré-registrar meu domínio personalizado**. 

10. Clique no botão **Registrar** para pré-registrar seu domínio personalizado. 

	Se o seu pré-registro for bem-sucedido, a mensagem **Seu domínio personalizado está ativo** será exibida. 

11. Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento. Para concluir o processo, volte ao site do registrador do DNS e crie outro registro CNAME que mapeie o subdomínio para o ponto de extremidade de serviço Blob. Por exemplo, especifique o subdomínio como **www** ou **fotos** e o nome do host como **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). Com essa etapa, o registro do seu domínio personalizado está concluído.

12. Por fim, você pode excluir o Registro CNAME criado usando **asverify**, já que ele era necessário apenas como uma etapa intermediária.

Os usuários podem agora exibir os dados do blob em seu domínio personalizado, contanto que tenham as permissões adequadas.

<a name="verify-subdomain"> </a>

<h2>Verificar se o subdomínio personalizado faz referência ao ponto de extremidade de serviço Blob</h2>

Para verificar se seu domínio personalizado está de fato mapeado ao ponto de extremidade de serviço Blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, use um URI no seguinte formato para acessar o blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por exemplo, você pode usar o seguinte URI para acessar um formulário da web por meio de um
subdomínio personalizado **photos.contoso.com** que é mapeado a um blob em seu
contêiner **myforms**:

-   http://photos.contoso.com/myforms/applicationform.htm

## Recursos adicionais

-   <a href="http://msdn.microsoft.com/library/windowsazure/gg680307.aspx">Como mapear o conteúdo da CDN para um domínio personalizado</a>
<!--HONumber=42-->
