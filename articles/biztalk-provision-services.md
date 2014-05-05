<properties linkid="provisioning-biztalk-service" urlDisplayName="Provisionar os Serviços BizTalk no Portal de Gerenciamento" pageTitle="Provisionar os Serviços BizTalk no Portal de Gerenciamento | Azure" metaKeywords="Introdução aos serviços biztalk Azure, provisão, dados não-estruturados Azure" description="Saiba como configurar um serviço BizTalk no Portal de Gerenciamento do Azure, bem como criar um servidor de Banco de Dados SQL opcional e uma conta de armazenamento." metaCanonical="http://www.windowsazure.com/pt-br/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />



# Serviços BizTalk: provisionar usando o Portal de Gerenciamento do Azure

<div class="dev-callout"> 
<b>Dica</b> 
<p>Para fazer logon no Portal de Gerenciamento do Azure, você precisa de uma conta e de uma assinatura do Azure. Se você não tiver uma conta, será possível criar uma conta de avaliação gratuita em questão de minutos. Para obter detalhes, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Avaliação gratuita do Azure</a>.</p> 
</div>

Um Serviço BizTalk do Azure consiste nos seguintes componentes:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Requisito</strong></td>
        <td><strong>Descrição</strong></td>
</tr>
<tr>
<td>Assinatura do Azure</td>
<td><p>A assinatura rege o acesso ao Portal de Gerenciamento do Azure e é criada pelo titular da conta do Azure em <a HREF="https://account.windowsazure.com/Subscriptions"> Assinaturas do Azure</a>.</p>
<p>A conta do Azure pode ter diversas assinaturas e ser gerenciada pelo titular da conta do Azure ou por diferentes pessoas ou grupos. Por exemplo, o titular da sua conta do Azure cria uma assinatura chamada <em>BizTalkServiceSubscription</em> e dá acesso a essa assinatura aos administradores do BizTalk em sua empresa (por exemplo, ContosoBTSAdmins@live.com). Neste cenário, os administradores do BizTalk fazem logon no Portal de Gerenciamento do Azure e têm acesso total aos serviços hospedados na assinatura, inclusive os Serviços BizTalk do Azure. Os administradores do BizTalk não são os titulares da conta do Azure e, portanto, não têm acesso às informações de cobrança.</p>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Gerenciar assinaturas e contas de armazenamento no Portal de Gerenciamento do Azure</a> oferece mais informações sobre as contas e as assinaturas do Azure.
</td>
</tr>
<tr>
<td>Banco de Dados SQL Azure</td>
<td><p>Um Banco de Dados SQL armazena as tabelas, as exibições e os procedimentos armazenados usados pelos Serviços BizTalk do Azure.</p>
<p>Quando provisiona um Serviço BizTalk, você pode usar um SQL Server do Azure, um Banco de Dados SQL Azure existentes ou criar automaticamente um novo servidor ou banco de dados. Quando você opta por criar um novo SQL Server e Banco de Dados do Azure, os Serviços do Azure são habilitados automaticamente.</p>
<p>Se você criar um novo Banco de Dados SQL Azure em um SQL Server do Azure existente, as regras do firewall do servidor não serão modificadas. Como resultado, é possível que outros Serviços do Azure não tenham permissão de acesso aos bancos de dados do servidor.</p>
Não há requisitos de escala mínimos para as configurações do Banco de Dados SQL.</td>
</tr>
<tr>
<td>Namespace do Controle de Acesso do Azure</td>
<td>O namespace do Controle de Acesso é autenticado com os Serviços BizTalk do Azure. Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. Quando você provisiona um Serviço BizTalk, o Namespace do Controle de Acesso é criado automaticamente.</td>
</tr>

<tr>
<td>Conta de Armazenamento do Azure</td>
<td><p>A Conta de Armazenamento do Azure dá acesso a tabelas, blobs e filas. Quando provisiona um Serviço BizTalk, você pode usar uma Conta de Armazenamento existente ou criar automaticamente uma nova Conta de Armazenamento. As tabelas, os blobs e as filas são usados pelo Serviços BizTalk para fazer o seguinte:</p>
<ul>
<li>Arquivos de log que monitoram o Serviço BizTalk são armazenados. A saída do monitoramento também é exibida na guia Monitoramento no Portal de Gerenciamento do Azure.</li>
<li>Ao criar um contrato X12 ou AS2 entre parceiros, você pode habilitar o recurso de Arquivamento para armazenar propriedades de mensagens. Esses dados de acompanhamento são salvos nessa Conta de Armazenamento.</li>
</ul>
</td>
</tr>

<tr>
<td>Certificado SSL privado</td>
<td><p>Ao provisionar os Serviços BizTalk do Azure, você cria uma URL que inclui o nome do seu Serviço BizTalk. Este certificado SSL privado (.pfx) é usado como o certificado da autenticação de servidor HTTPS quando são feitas solicitações para a URL do Serviço BizTalk. Quando você provisiona um Serviço BizTalk, um certificado autoassinado é criado automaticamente. </p>
<strong>Informações importantes sobre o certificado SSL</strong>

<ul>
<li>A data de expiração do certificado deve ser menor do que cinco anos.</li>
<li>Todos os certificados privados exigem uma senha. Saiba essa senha e, como uma prática recomendada, compartilhe essa senha com seus administradores.</li>
<li>Os certificados autoassinados podem ser usados em um ambiente de teste ou desenvolvimento. Ao usar certificados autoassinados, importe o certificado para o armazenamento de certificados pessoais e o armazenamento de certificados de Autoridades de Certificação Raiz Confiáveis.</li>
</ul>
<br/>Ao enviar a solicitação de certificado e produção à sua autoridade de certificação, especifique as seguintes propriedades:
<br/>

<ul>
<li><p><strong>Uso avançado da chave</strong>: autenticação do servidor
Chaves adicionais podem ser habilitadas no certificado. No mínimo, os Serviços BizTalk do Azure exigem a Autenticação de Servidor.</p></li>
<li><p><strong>Nome Comum</strong>: Insira o FQDN (nome de domínio totalmente qualificado) da URL dos Serviços BizTalk do Azure, que é criado quando você provisiona o Serviço em <a HREF="#BizTalk">Provisionar um Serviço BizTalk</a>, neste tópico.</p>
<p>Portanto, você precisa saber qual será sua URL ao enviar a solicitação de certificado à autoridade de certificação. Um certificado novo ou diferente pode ser adicionado após o Serviço BizTalk ser provisionado.</p></li>
</ul>
<br/>
</td>
</tr>
</table>


Este tópico lista as etapas para configurar os Serviços BizTalk do Azure, incluindo: 

-  [Etapa 1: Provisionar um Serviço BizTalk](#BizTalk)
-  [Etapa 2: Etapas de pós-provisionamento](#PostProv)
-  [Opcional: criar um servidor de Banco de Dados SQL](#SQLDB)
-  [Opcional: criar uma conta de armazenamento](#Storage)

##<a name="BizTalk"></a>Etapa 1: Provisionar um Serviço BizTalk

O Serviço BizTalk hospeda seus aplicativos do Serviço BizTalk do Azure. Quando você provisiona um Serviço BizTalk, o Namespace do Controle de Acesso e o certificado SSL autoassinado são criados automaticamente. Você pode optar por criar um novo Banco de Dados SQL e uma nova Conta de Armazenamento. Depois que o Serviço BizTalk estiver provisionado, alguns desses requisitos poderão ser atualizados. 

As etapas a seguir provisionam um novo Serviço BizTalk do Azure:

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).

2. Na parte inferior do painel de navegação, selecione **+NOVO**.

	![Selecionar o botão Novo][NEWButton]

3. Selecione **SERVIÇOS DE APLICATIVO**, selecione **SERVIÇO BIZTALK**e, em seguida, selecione **CRIAÇÃO PERSONALIZADA**:

	![Selecionar Serviço BizTalk e Criação Personalizada][NewBizTalkService]

4. Insira as seguintes configurações do Serviço BizTalk:

	<table border="1">
	<tr>
	<td><strong>Nome do Serviço BizTalk</strong></td>
	<td>Insira um nome para o seu Serviço BizTalk. ".biztalk.windows.net" é adicionado automaticamente ao nome que você inserir. Isso resulta em uma URL que é usada para acessar o seu Serviço BizTalk. Você pode inserir qualquer nome, mas é melhor ser específico. Alguns exemplos incluem:<br/><br/>
	<em>minha_empresa</em>. biztalk.windows.net<br/>
	<em>Meu_Aplicativo_da_Minha_Empresa</em>.biztalk.windows.net<br/>
	<em>meu_aplicativo</em>.biztalk.windows.net
	</td>
	</tr>
	<tr>
	<td><strong>URL do domínio</strong></td>
	<td><strong>Opcional</strong>. Por padrão, a URL do domínio é <em>YourBizTalkServiceName</em>.biztalk.windows.net. Um domínio personalizado também pode ser inserido. Por exemplo, se seu domínio for <em>contoso</em>, você poderá inserir: <br/><br/>
	<em>Minha_Empresa</em>.contoso.com<br/>
	<em>Meu_Aplicativo_da_Minha_Empresa</em>.contoso.com<br/>
	<em>Meu_Aplicativo</em>.contoso.com<br/>
	<em>YourBizTalkServiceName</em>.contoso.com<br/>
	</td>
	</tr>
	<tr>
	<td><strong>Edição</strong></td>
	<td>As opções incluem:
	<ul>
	<li>Developer</li>
	<li>Standard</li>
	<li>Basic:</li>
	<li>Premium</li>
	</ul>
	<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium</a> lista as diferenças entre as edições. Se você estiver em fase de teste/desenvolvimento, escolha <strong>Developer</strong>. Se estiver em fase de produção, use o gráfico para determinar se Premium, Standard ou Basic é a escolha certa para o seu cenário comercial.
	</td>
	</tr>
	<tr>
	<td><strong>Região</strong></td>
	<td>Selecione a região geográfica para hospedar seu Serviço BizTalk.</td>
	</tr>
	<tr>
	<td><strong>Banco de dados de acompanhamento</strong></td>
	<td><p>Selecione seu Banco de Dados SQL para armazenar as tabelas usadas pelo Serviço BizTalk. Escolha uma das opções a seguir:</p>
	<ul>
	<li><strong>Usar uma instância existente do Banco de Dados SQL</strong>: selecione essa opção para usar um Banco de Dados SQL Azure existente. Você poderá usar um Banco de Dados SQL Azure existente se ele não estiver sendo usado por outro Serviço BizTalk. Você precisará do nome e da senha de logon especificados na criação do Banco de Dados SQL Azure.</li>
	<li><p><strong>Criar uma nova instância do Banco de Dados SQL</strong>: selecione essa opção para criar um novo Banco de Dados SQL.</p></li>
	<p><strong>Observação</strong></p>
	<p>Quando você opta por criar um novo SQL Server e Banco de Dados do Azure, os Serviços do Azure são habilitados automaticamente no Banco de Dados SQL. O Serviço BizTalk requer que os Serviços do Azure sejam habilitados no Banco de Dados SQL Azure.</p>
	<p><strong>Dica</strong></p>
	Crie o banco de dados de acompanhamento e a Conta de Armazenamento de monitoramento/arquivamento na mesma região que o Serviço BizTalk.
	</ul>
	</td>
	</tr>
	<tr>
	<td><strong>Assinatura</strong></td>
	<td><strong>Opcional</strong>. Disponível somente quando houver mais de uma assinatura. Selecione sua assinatura para hospedar o Serviço BizTalk.</td>
	</tr>
	</table>
Selecione a seta de AVANÇO.

5. Insira as configurações do seu banco de dados:

	<table border="1">
	<tr>
	<td><strong>Assinatura</strong></td>
	<td><strong>Opcional</strong>. Disponível somente quando houver mais de uma assinatura. Selecione sua assinatura para hospedar o Banco de Dados SQL Azure.</td>
	</tr>
	<tr>
	<td><strong>Banco de dados</strong></td>
	<td><p>Disponível quando a opção <strong>Usar uma instância do Banco de Dados SQL existente</strong> foi selecionada na tela anterior.</p>
	Selecione seu Banco de Dados SQL para armazenar as tabelas usadas pelo Serviço BizTalk.
	</td>
	</tr>
	<tr>
	<td><strong>Nome</strong></td>
	<td><p>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> foi selecionada na tela anterior.</p>
	Insira o nome do Banco de Dados SQL a ser usado pelo seu Serviço BizTalk. Por padrão, o <em>YourBizTalkServiceName</em>_db é inserido.</td>
	</tr>
	<tr>
	<td><strong>Servidor</strong></td>
	<td><p>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> foi selecionada na tela anterior.</p>
	Selecione um servidor de Banco de Dados SQL existente. Ou selecione <strong>Novo servidor de Banco de Dados SQL</strong> para criar um novo servidor de Banco de Dados SQL.</td>
	</tr>
	<tr>
	<td><strong>Nome de Logon do Servidor</strong></td>
	<td>Digite o nome de usuário do logon.</td>
	</tr>
	<tr>
	<td><strong>Senha de Logon do Servidor</strong></td>
	<td>Digite a senha do logon.</td>
	</tr>
	<tr>
	<td><strong>Região</strong></td>
	<td>Disponível quando a opção <strong>Criar uma nova instância do Banco de Dados SQL</strong> está selecionada. Selecione a região geográfica para hospedar seu Banco de Dados SQL.</td>
	</tr>
	</table>
Selecione a seta de AVANÇO.

6. Insira as configurações de monitoramento do Azure:

	<table border="1">
	<tr>
	<td><strong>Monitorando/arquivando a conta de armazenamento</strong></td>
	<td>Selecione uma conta de armazenamento existente ou selecione <strong>Criar uma nova conta de armazenamento</strong>.</td>
	</tr><tr>
	<td><strong>Nome da Conta de Armazenamento</strong></td>
	<td>Disponível quando a opção <strong>Criar uma nova conta de armazenamento</strong> está selecionada. Insira um nome para a conta de armazenamento usada por seu Serviço BizTalk.</td>
	</tr>
	</table>

Selecione a marca de seleção para concluir o assistente. Ao concluir, o ícone de andamento exibe:<br/>

![O ícone de progresso é exibido após a conclusão][ProgressComplete]


Ao concluir, um Serviço BizTalk do Azure estará provisionado e pronto para seus aplicativos.

As configurações padrão são suficientes. Se você desejar modificar as configurações padrão, selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione seu Serviço BizTalk. Configurações adicionais são exibidas nas guias Painel, Monitor e Escala.

Dependendo do estado do Serviço BizTalk, existem algumas operações que não podem ser concluídas. Para obter uma lista dessas operações, consulte [Serviços BizTalk: gráfico de estado do serviço BizTalk (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870).

##<a name="PostProv"></a>Etapa 2: etapas de pós-provisionamento

Esta seção lista as seguintes etapas: 

-  [Adicionar um certificado privado](#AddCert)
-  [Recuperar o namespace do Controle de Acesso](#ACS)

####<a name="AddCert"></a>Adicionar um certificado privado
Ao provisionar os Serviços BizTalk do Azure, é criada uma URL que inclui o nome do seu Serviço BizTalk. Um certificado SSL privado (.pfx) é usado como o certificado da autenticação de servidor HTTPS quando são feitas solicitações para a sua URL do Serviço BizTalk. 

Um certificado autoassinado é criado automaticamente para seu Serviço BizTalk. Esse certificado pode ser baixado ou substituído no Painel do Serviço BizTalk. Os certificados autoassinados são usados em ambientes de desenvolvimento. 

Para adicionar um certificado pronto para produção:

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk.
3. Selecione a guia **Painel**.
4. Selecione **Atualizar Certificado SSL**:<br/><br/>
![Modificar certificado SSL][QuickGlance]

5. Procure seu certificado SSL privado (*Nome_do_Certificado*.pfx) que inclui o nome do seu Serviço BizTalk, insira a senha e selecione a caixa de seleção.


####<a name="ACS"></a>Recuperar o namespace do Controle de Acesso

O namespace do Controle de Acesso é autenticado com os Serviços BizTalk do Azure. Ao implantar um projeto do Serviço BizTalk no Visual Studio, você insere esse namespace do Controle de Acesso. 

O Namespace do Controle de Acesso é criado automaticamente para seu Serviço BizTalk. Para recuperar o namespace do Controle de Acesso, o Emissor Padrão e a Chave do Emissor, selecione o botão **Informações de Conexão** no Painel do Serviço BizTalk.

Para recuperar o namespace do Controle de Acesso:

1. Faça logon no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **SERVIÇOS BIZTALK** no painel de navegação à esquerda e selecione o seu Serviço BizTalk.
3. Na barra de tarefas, selecione **Informações de Conexão**:<br/><br/>
![Selecionar Informações de conexão][ACSConnectInfo]

Você pode copiar e colar os valores do Controle de Acesso.

Quando o namespace do Controle de Acesso é criado, os valores do Controle de Acesso podem ser usados com qualquer aplicativo. Quando os Serviços BizTalk do Azure estiverem provisionados, esse namespace do Controle de Acesso controla a autenticação com sua implantação do Serviço BizTalk. Se você desejar alterar a assinatura ou gerenciar o namespace, selecione **ACTIVE DIRECTORY** no painel de navegação à esquerda e selecione seu namespace. O painel de navegação inferior lista as opções.

Um clique em **Gerenciar** abre o Portal de Gerenciamento do Controle de Acesso. No Portal de Gerenciamento do Controle de Acesso, o Serviço BizTalk usa **Identidades de serviço**:<br/><br/>
![Identidades do serviço ACS no Portal de Gerenciamento do Controle de Acesso][ACSServiceIdentities]

A identidade do serviço do Controle de Acesso é um conjunto de credenciais que permitem que aplicativos ou clientes façam autenticação diretamente com o Controle de Acesso e recebam um token. 

**Importante**<br/>
O Serviço BizTalk usa **Proprietário** para a identidade do serviço padrão e o valor da **Senha**. Se você usar o valor de Chave Simétrica em vez do valor de Senha, pode ocorrer o seguinte erro:

*Não foi possível conectar à conta do Serviço de Gerenciamento do Controle de Acesso com as credenciais especificadas*

[Gerenciando o namespace de seu ACS (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=285670) lista algumas diretrizes e recomendações.




##<a name="SQLDB"></a>Opcional: criar um servidor de Banco de Dados SQL

Quando você provisionar os Serviços BizTalk do Azure, um novo Servidor de Banco de Dados SQL é criado automaticamente. Se você preferir criar um Servidor de Banco de Dados SQL independente do Serviço BizTalk, consulte [Como usar o Banco de Dados SQL Azure em aplicativos .NET (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=251285).

Ao concluir, haverá um novo Banco de Dados SQL Azure, no qual você pode fazer logon e criar tabelas, exibições e procedimentos armazenados. 

Por padrão, a escala do Banco de Dados SQL é configurada da seguinte maneira:

- Web Edition
- Tamanho do banco de dados de 1 GB

A configuração padrão é suficiente para um Serviço BizTalk. Se você desejar modificar os parâmetros da configuração, selecione **BANCO DE DADOS SQL** no painel de navegação à esquerda, selecione seu Banco de Dados SQL e selecione a guia **Configurar**. A modificação da escala pode afetar o preço. [Contas e cobrança no Banco de Dados SQL Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=234930) fornece informações sobre as edições e cobranças.


##<a name="Storage"></a>Opcional: criar uma conta de armazenamento

Quando você provisiona os Serviços BizTalk do Azure, uma Conta de Armazenamento do Azure é criada automaticamente. Se você preferir criar uma Conta de Armazenamento do Azure independente do Serviço BizTalk, consulte [Como criar uma conta de armazenamento (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=279823).

Ao concluir, haverá uma nova Conta de Armazenamento do Azure que dá acesso a tabelas, blobs e filas. 

As configurações padrão são suficientes para um Serviço BizTalk. Se você desejar modificar as configurações padrão, selecione **ARMAZENAMENTO** no painel de navegação à esquerda e selecione sua Conta de Armazenamento. As configurações são exibidas nas guias Painel, Monitor, Configurar e Contêineres.

Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves controlam o acesso à sua conta de armazenamento. O Serviço BizTalk usa automaticamente a chave primária.

[Armazenamento (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=285671) fornece informações sobre sua Conta de Armazenamento.



## Avançar

Agora que um Serviço BizTalk foi provisionado, você pode se familiarizar com as diferentes guias em [Serviços BizTalk: guias Painel, Monitor e Escala (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302281). Seu Serviço BizTalk está pronto para os seus aplicativos. Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Consulte também
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: gráfico de estado do Serviço BizTalk (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: limitação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços BizTalk: nome e chave do emissor (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png

