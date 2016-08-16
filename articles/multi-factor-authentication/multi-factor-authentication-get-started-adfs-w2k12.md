<properties
	pageTitle="Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2 | Microsoft Azure"
	description="Este artigo descreve como começar a usar o Azure Multi-Factor Authentication e o AD FS no Windows Server 2012 R2."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="billmath"/>


# Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2

Se sua organização usar os Serviços de Federação do Active Directory (AD FS) e se você quiser proteger recursos de nuvem e locais, poderá implantar e configurar o Servidor Azure Multi-Factor Authentication para trabalhar com o AD FS. Essa configuração dispara a autenticação multifator para pontos de extremidade de alto valor.

Neste artigo, discutimos o uso do Servidor Azure Multi-Factor Authentication com o AD FS no Windows Server 2012 R2. Para saber mais, leia sobre como [proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Proteger o AD FS do Windows Server 2012 R2 com o Servidor Azure Multi-Factor Authentication

Ao instalar o Servidor Azure Multi-Factor Authentication, você tem as seguintes opções:

- Instalar o Servidor Azure Multi-Factor Authentication localmente no mesmo servidor que o AD FS
- Instalar o adaptador do Azure Multi-Factor Authentication localmente no servidor do AD FS e instalar o Servidor Multi-Factor Authentication em outro computador

Antes de começar, esteja ciente das seguintes informações:

- Não é necessário instalar o Servidor Azure Multi-Factor Authentication no servidor do AD FS. No entanto, você deve instalar o adaptador do Multi-Factor Authentication para o AD FS no Windows Server 2012 R2 que está executando o AD FS. Você pode instalar o servidor em outro computador, desde que seja uma versão com suporte, e instalar o adaptador do AD FS separadamente no seu servidor de federação do AD FS. Confira os procedimentos a seguir para aprender a instalar o adaptador separadamente.
- Quando o adaptador do AD FS do Servidor Multi-Factor Authentication foi projetado, era esperado que o AD FS pudesse passar o nome da terceira parte confiável para o adaptador que poderia ser usado como um nome de aplicativo. No entanto, isso não aconteceu. Se sua organização estiver usando mensagens de texto ou de métodos de autenticação de aplicativos móveis, as cadeias de caracteres definidas em Configurações da Empresa contêm um espaço reservado <$*nome\_do\_aplicativo*$>. Esse espaço reservado não será substituído automaticamente quando você usar o adaptador do AD FS. É recomendável que você remova o espaço reservado de cadeias de caracteres apropriadas ao proteger o AD FS.

- A conta que você usa para entrar deve ter direitos de usuário para criar grupos de segurança no serviço do Active Directory.

- O assistente de instalação do adaptador AD FS do Multi-Factor Authentication cria um grupo de segurança chamado PhoneFactor Admins na sua instância do Active Directory e, em seguida, adiciona a conta de serviço do seu serviço de federação do AD FS a esse grupo. É recomendável que você verifique no controlador de domínio que o grupo PhoneFactor Admins realmente é criado e se a conta de serviço do AD FS é membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.
- Para saber mais sobre como instalar o SDK do Serviço Web com o portal do usuário, leia sobre a [implantação do portal do usuário para o Servidor Azure Multi-Factor Authentication.](multi-factor-authentication-get-started-portal.md)


### Instalar o Servidor Azure Multi-Factor Authentication localmente no mesmo servidor que o AD FS

1. Baixe e instale o Servidor Azure Multi-Factor Authentication no servidor de federação do AD FS. Para obter informações sobre instalação, leia sobre a [introdução ao Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. No console de gerenciamento do Servidor Azure Multi-Factor Authentication, clique no ícone **AD FS** e selecione as opções **Permitir registro do usuário** e **Permitir que usuários selecionem o método**.
3. Selecione as opções adicionais que você deseja especificar para a sua organização.
4. Clique em **Instalar Adaptador do AD FS**.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se o computador tiver ingressado em um domínio e a configuração do Active Directory para proteger a comunicação entre o adaptador do AD FS e o serviço do Azure Multi-Factor Authentication estiver incompleta, a janela do **Active Directory** será exibida. Clique em **Avançar** para concluir automaticamente essa configuração ou marque a caixa de seleção **Ignorar configuração automática do Active Directory e definir as configurações manualmente** e clique em **Avançar**.
6. Se o computador não tiver ingressado em um domínio e a configuração do grupo local para proteger a comunicação entre o adaptador do AD FS e o serviço do Azure Multi-Factor Authentication estiver incompleta, a janela **Grupo Local** será exibida. Clique em **Avançar** para concluir automaticamente essa configuração ou marque a caixa de seleção **Ignorar configuração automática do Grupo Local e definir as configurações manualmente** e clique em **Avançar**.
7. No Assistente de instalação, clique em **Avançar**. O Servidor Azure Multi-Factor Authentication cria o grupo PhoneFactor Admins e adiciona a conta de serviço do AD FS ao grupo PhoneFactor Admins.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Na página **Iniciar Instalador**, clique em **Avançar**.
9. No instalador do adaptador do AD FS do Multi-Factor Authentication, clique em **Avançar**.
10. Clique em **Fechar** quando a instalação for concluída.
11. Quando o adaptador tiver sido instalado, você deverá registrá-lo no AD FS. Abra PowerShell e execute o seguinte comando:<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Edite a política de autenticação global no AD FS para usar nosso adaptador recentemente registrado. No console de gerenciamento do AD FS, vá até o nó **Políticas de Autenticação**. Na seção **Multi-factor Authentication**, clique no link **Editar** próximo à seção **Configurações Globais**. Na janela **Editar Política de Autenticação Global**, selecione **Multi-Factor Authentication** como um método de autenticação adicional e clique em **OK**. O adaptador é registrado como WindowsAzureMultiFactorAuthentication. Você deve reiniciar o serviço AD FS para que o registro entre em vigor.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste ponto, o Servidor Multi-Factor Authentication está configurado para ser um provedor de autenticação adicional a ser usado com o AD FS.

## Instalar o adaptador do AD FS autônomo usando o SDK do serviço Web
1. Instale o SDK do Serviço Web no servidor que esteja executando o Servidor Multi-Factor Authentication.
2. Copie os arquivos a seguir do diretório \\Arquivos de Programa\\Multi-Factor Authentication Server para o servidor no qual você planeja instalar o adaptador do AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Execute a instalação de MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do Adaptador do AD FS do Multi-Factor Authentication, clique em **Avançar** para executar a instalação.
5. Clique em **Fechar** quando a instalação for concluída.
6. Edite o arquivo MultiFactorAuthenticationAdfsAdapter.config fazendo o seguinte:

|Etapa MultiFactorAuthenticationAdfsAdapter.config| Subetapa|
|:------------- | :------------- |
|Defina o nó **UseWebServiceSdk** como **true**.||
|Defina o valor **WebServiceSdkUrl** para a URL do SDK do Serviço Web do Multi-Factor Authentication.||
|Configure o SDK do Serviço Web.<br><br>*Opção 1*: usando um nome de usuário e uma senha|<ol type="a"><li>Defina o valor como **WebServiceSdkUsername** para uma conta que seja membro do grupo de segurança PhoneFactor Admins. Use o formato &lt;domínio&gt;&#92;&lt;nome de usuário&gt;.<li>Defina o valor como **WebServiceSdkPassword** para a senha da conta apropriada.</li></ol>
|Configurar o SDK do Serviço Web, *continuação*<br><br>*Opção 2*: usando um certificado do cliente|<ol type="a"><li>Obtenha um certificado do cliente de uma autoridade de certificação para o servidor que está executando o SDK de Serviço Web. Saiba mais sobre como [obter um certificado do cliente](https://technet.microsoft.com/library/cc770328.aspx). </li><li>Importe o certificado do cliente para o repositório de certificados pessoais no servidor que esteja executando o SDK do Serviço Web. Observação: verifique se o certificado público da autoridade de certificação está no repositório de certificados chamado Certificados Raiz Confiáveis.</li><li>Exporte as chaves pública e privada do certificado do cliente para um arquivo .pfx.</li><li>Exporte a chave pública no formato de Base 64 para um arquivo .cer.</li><li>No Gerenciador do Servidor, verifique se o recurso Web Server (IIS)\\Web Server\\Security\\IIS Client Certificate Mapping Authentication está instalado. Se não estiver instalado, selecione **Adicionar funções e recursos** para adicionar esse recurso.</li><li>No Gerenciador do IIS, clique duas vezes em **Editor de Configuração** no site que contém o diretório virtual do SDK do Serviço Web. Observação: é muito importante fazer isso no nível de site, e não no nível de diretório virtual.</li><li>Vá até a seção **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.</li><li>Defina **enabled** como **true**.</li><li>Defina **oneToOneCertificateMappingsEnabled** como **true**.</li><li>Clique no botão **...** próximo a **oneToOneMappings** e clique no link **Adicionar**.</li><li>Abra o arquivo .cer de Base 64 exportado anteriormente. Remova *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* e todas as quebras de linha. Copie a cadeia de caracteres resultante.</li><li>Defina **certificate** como a cadeia de caracteres copiada na etapa anterior.</li><li>Defina **enabled** como **true**.</li><li>Defina **userName** como uma conta que seja um membro do grupo de segurança PhoneFactor Admins. Use o formato &lt;domínio&gt;&#92;&lt;nomedousuário&gt;.</li><li>Defina a senha para a conta apropriada e feche o Editor de Configuração.</li><li>Clique no link **Aplicar**.</li><li>No diretório virtual do SDK do Serviço Web, clique duas vezes em **Autenticação**.</li><li>Verifique se a **Autenticação Básica** e a **Representação do ASP.NET** estão definidas como **Habilitado** e se todos os outros itens estão definidos como **Desabilitado**.</li><li>No diretório virtual do SDK do Serviço Web, clique duas vezes em **Configurações de SSL**.</li><li>Defina os **Certificados de Cliente** como **Aceitar** e clique em **Aplicar**.</li><li>Copie o arquivo .pfx exportado anteriormente para o servidor que está executando o adaptador do AD FS.</li><li>Importe o arquivo .pfx para o repositório de certificados pessoais do computador local.</li><li>Clique com o botão direito do mouse e selecione **Gerenciar Chaves Privadas**, então conceda acesso de leitura à conta que você usou para entrar no serviço AD FS.</li><li>Abra o certificado do cliente e copie a impressão digital da guia **Detalhes**.</li><li>No arquivo MultiFactorAuthenticationAdfsAdapter.config, defina **WebServiceSdkCertificateThumbprint** como a cadeia de caracteres copiada na etapa anterior.</li></ol>
| Edite o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 adicionando *-ConfigurationFilePath &lt;caminho&gt;* ao fim do comando `Register-AdfsAuthenticationProvider`, em que *&lt;caminho&gt;* é o caminho completo para o arquivo MultiFactorAuthenticationAdfsAdapter.config.||

Execute o script \\Arquivos de Programa\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 no PowerShell. O adaptador é registrado como WindowsAzureMultiFactorAuthentication. Você deve reiniciar o serviço AD FS para que o registro entre em vigor.

<!---HONumber=AcomDC_0810_2016-->