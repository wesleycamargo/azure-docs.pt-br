<properties umbracoNaviHide="0" pageTitle="Integração do AD do Azure com o Google Apps" metaKeywords="Integração do Azure com o Google Apps" description="Saiba como integrar o AD do Azure com o Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Integração do AD do azure com o Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Integração do AD Azure com o Google Apps" authors="" />

# Integração do AD do Azure com o Google Apps
O objetivo deste tutorial é mostrar a integração do Azure com o Google Apps. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

- Uma assinatura válida do Azure
- Um locatário de teste no Google Apps

Se você não tiver um locatário válido no Google Apps, você poderá, por exemplo, inscrever-se para obter uma conta de avaliação no Google Apps para o site da empresa.

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

Permitindo a integração de aplicativos com o Google Apps
Configurando o logon único
Habilitando o acesso à API do Google Apps
Adicionando domínios personalizados
Configurando o provisionamento de usuários

# Habilitando a integração de aplicativos com o Google Apps #
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Google Apps.

## Para habilitar a integração de aplicativos com o Google Apps, execute as seguintes etapas: ##

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.
2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3.  Para abrir a visualização dos aplicativos,  na exibição do diretório, clique em **Aplicativos** no menu principal.
4.  Clique em **Adicionar** na parte inferior para abrir a caixa de diálogo **Adicionar Aplicativo**.
5.  Na caixa de diálogo **Integrar um aplicativo com o AD do Azure**, clique em **Gerenciar o acesso a um aplicativo**.
6.  Na página **Selecionar um aplicativo** para gerenciar, selecione **Google Apps** na lista de aplicativos.
7.  Clique no botão **Concluir** para adicionar o aplicativo e fechar a caixa de diálogo.

# Configurando o logon único #
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Google Apps com sua conta do AD do Azure usando federação baseada em protocolo SAML.

## Para configurar o logon único federado, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, selecione <strong>Active Directory</strong> no painel de navegação à esquerda para abrir a página da caixa de diálogo Active Directory.
2. Na lista diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
3. Selecione <strong>APLICATIVOS</strong> no menu de nível superior.
4. Na lista de aplicativos, selecione <strong>Google Apps</strong> para abrir a caixa de diálogo de configuração do Google Apps.
5. Para abrir a caixa de diálogo <strong>CONFIGURAR LOGON ÚNICO</strong>, clique em <strong>Configurar logon único.</strong>

	![Google_Tutorial_01](./media/integration-azure-google-apps/Google_Tutorial_01.png)

6. Na página da caixa de diálogo Selecionar o modo de logon único para este aplicativo, selecione Usuários são autenticados com sua conta no AD do Azure como MODO e clique no botão Avançar.

	![Google_Tutorial_02](./media/integration-azure-google-apps/Google_Tutorial_02.png)

7. Na página da caixa de diálogo <strong>Configurar URL do Aplicativo</strong>, na caixa de texto <strong>URL DO LOCATÁRIO DO GOOGLE APPS</strong>, digite a URL do locatário do Google Apps e, em seguida, clique no botão <strong>Avançar</strong>.

	![Google_Tutorial_03](./media/integration-azure-google-apps/Google_Tutorial_03.png)

8. Na página da caixa de diálogo <strong>Configurar logon único no Google Apps</strong>, execute as seguintes etapas e, em seguida, clique no botão <strong>Concluir</strong>.
		
	+ Clique em <strong>Baixar certificado</strong> e salve o certificado como <strong>c:\googleapps.cer</strong>.
	+ Abra a página de logon do Google Apps e, em seguida, efetue logon

	![Google_Tutorial_04](./media/integration-azure-google-apps/Google_Tutorial_04.png)

	+ No <strong>Console do administrador</strong>, clique em Segurança

	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	Se o ícone de Segurança não estiver visível, você deve clicar em Mais controles na parte inferior da página.

9. Na página <strong>Segurança</strong>, clique em configurações <strong>Avançadas</strong>.

	![Google_Tutorial_06](./media/integration-azure-google-apps/Google_Tutorial_06.png)

10. Na seção configurações <strong>Avançadas</strong> da página, selecione <strong>Configurar logon único</strong>.

	![Google_Tutorial_07](./media/integration-azure-google-apps/Google_Tutorial_07.png)

11. Na página Configurar logon único, execute as seguintes etapas:

	![Google_Tutorial_08](./media/integration-azure-google-apps/Google_Tutorial_08.png)

		+ Selecione <strong>Habilitar Logon Único</strong>.
		+ Na página <strong>Configurar logon único no Google Apps</strong> no Portal do AD do Azure, copie a <strong>URL DO LOGON ÚNICO</strong> e cole-a na caixa de texto relacionada na <strong>página Segurança</strong> no <strong>Console de Administração</strong> do Google Apps.
		+ Na página <strong>Configurar logon único no Google Apps</strong> no Portal do AD do Azure, copie a <strong>URL de serviço de saída única</strong> e cole-a na caixa de texto relacionada na página <strong>Segurança</strong> no <strong>Console de Administração</strong> do Google Apps.
		+ Na página <strong>Configurar logon único no Google Apps</strong> no Portal do AD do Azure, copie a <strong>URL de Alterar senha</strong> e cole-a na caixa de texto relacionada na página <strong>Segurança</strong> no <strong>Console de Administração</strong> do Google Apps.
		+ Clique no botão <strong>Procurar</strong> para localizar o <strong>Certificado de verificação</strong> e, em seguida, clique em Carregar.
		+ Clique em <strong>Salvar</strong> as alterações.


12. Na página da caixa de diálogo <strong>Configurar logon único no Google Apps</strong> no Portal do AD do Azure, clique no botão Concluir.

Agora, você pode ir para o [Painel de Acesso](https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633) e testar o logon único no Google Apps.

# Habilitando o acesso à API do Google Apps
Ao integrar o Active Directory do Azure com o Google Apps para provisionamento de usuários, você deve habilitar o acesso à API para seu locatário no Google Apps.

## Para habilitar o acesso à API do Google Apps

1. Efetue logon em seu locatário do Google Apps.
1. No <strong>Console do administrador</strong>, clique em <strong>Segurança</strong>.
<p></p>
	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	Se o ícone de Segurança não estiver visível, clique em Mais controles na parte inferior do Console de Administração.
1. Na página Segurança, clique em <strong>Referência à API</strong> para abrir a página da caixa de diálogo de configuração relacionada.
1. Selecione <strong>Habilitar o acesso à API</strong>.
<p></p>
	![Google_Tutorial_09](./media/integration-azure-google-apps/Google_Tutorial_09.png)


# Adicionar domínios personalizados
A configuração do provisionamento de usuários com o Google Apps requer que o domínio do AD do Azure e o domínio do Google Apps tenham o mesmo FQDN (nome de domínio totalmente qualificado). No entanto, quando você estiver, por exemplo, usando locatários de avaliação para testar o cenário deste tutorial, os FQDNs de seus locatários normalmente não coincidem. Para resolver esse problema, você pode configurar domínios personalizados no AD do Azure e no Google Apps. 
A configuração de um domínio personalizado requer o acesso ao arquivo de zona DNS do seu domínio público. 

![Google_Tutorial_10](./media/integration-azure-google-apps/Google_Tutorial_10.png)

##Para adicionar um domínio personalizado ao AD do Azure, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, selecione <strong>Active Directory</strong> no painel de navegação à esquerda para abrir a página da caixa de diálogo <strong>Active Directory</strong>.
1. Na lista Diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
1. Selecione <strong>DOMÍNIOS</strong> no menu de nível superior.
1. Para abrir a caixa de texto <strong>ADICIONAR O NOME DO DOMÍNIO</strong>, digite seu nome de domínio e, em seguida, clique em <strong>Adicionar</strong>.
1. Clique em <strong>Avançar</strong> para abrir a página da caixa de diálogo <strong>Verificar seu nome de domínio</strong>.

	![Google_Tutorial_11](./media/integration-azure-google-apps/Google_Tutorial_11.png)
1. Selecione um <strong>TIPO DE REGISTRO</strong> e, em seguida, registre o registro selecionado em seu arquivo de zona DNS.

	![Google_Tutorial_12](./media/integration-azure-google-apps/Google_Tutorial_12.png)
1. Usando o <strong>comando nslookup</strong>, você deve verificar se o registro DNS foi registrado com êxito.

	![Google_Tutorial_13](./media/integration-azure-google-apps/Google_Tutorial_13.png)

## Para adicionar um domínio personalizado no Google Apps, execute as seguintes etapas:

1. Efetue logon em seu locatário do Google Apps.
1. No **Console do administrador**, clique em **Domínios**.

	![Google_Tutorial_14](./media/integration-azure-google-apps/Google_Tutorial_14.png)

1. Clique em <strong>Adicionar um domínio personalizado</strong>.

	![Google_Tutorial_15](./media/integration-azure-google-apps/Google_Tutorial_15.png)

1. Clique em <strong>Usar um domínio que você já possui</strong>e, em seguida, clique em <strong>Continuar</strong>.

	![Google_Tutorial_16](./media/integration-azure-google-apps/Google_Tutorial_16.png)

1. Digite o nome do seu domínio personalizado e, em seguida, clique em <strong>Continuar</strong>.

	![Google_Tutorial_17](./media/integration-azure-google-apps/Google_Tutorial_17.png)

1. Conclua as etapas para verificar a propriedade do domínio.
	
	Se já tiver configurado o logon único federado, você poderá atualizar a URL de locatário do Google Apps em sua configuração de logon único federado.



# Configurando o provisionamento de usuários 
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Google Apps.

## Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, selecione <strong>Active Directory</strong> no painel de navegação à esquerda para abrir a página da caixa de diálogo <strong>Active Directory</strong>.
1. Na lista Diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
1. Selecione <strong>APLICATIVOS</strong> no menu de nível superior.
Na lista de aplicativos, selecione <strong>Google Apps</strong> para abrir a caixa de diálogo de configuração do <strong>Google Apps</strong>.
1. Para abrir a caixa de diálogo <strong>CONFIGURAR SINCRONIZAÇÃO DA CONTA</strong>, clique em <strong>Configurar sincronização da conta</strong>.
1. Na página da caixa de diálogo <strong>CONFIGURAR SINCRONIZAÇÃO DA CONTA</strong>, forneça o nome do domínio do Google Apps, o nome do usuário do Google Apps e a senha do Google Apps e clique no botão <strong>Avançar</strong>.

	![Google_Tutorial_18](./media/integration-azure-google-apps/Google_Tutorial_18.png)

1. Na página da caixa de diálogo <strong>Confirmação</strong>, clique no botão <strong>Concluir</strong> para fechar a caixa de diálogo <strong>CONFIGURAR SINCRONIZAÇÃO DA CONTA</strong>.

Agora, você pode criar uma conta de teste, aguardar 10 minutos e verificar se a conta foi sincronizada no Google Apps.

Consulte também
[Práticas recomendadas para gerenciar as melhorias de acesso ao aplicativo para acesso ao aplicativo do Active Directory do Azure](http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx)
 

