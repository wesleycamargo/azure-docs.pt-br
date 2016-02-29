<properties 
	pageTitle="Proteger recursos de nuvem e locais usando o Servidor Azure MFA com o AD FS do Windows Server 2012 R2" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS no Windows Server 2012 R2." 
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
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>


# Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2

Se sua organização for federada com o Azure AD e você tiver recursos locais ou na nuvem que deseja proteger, pode fazer isso usando o Servidor Azure Multi-Factor Authentication e configurando-o para trabalhar com o AD FS, para que a autenticação multifator seja acionada pelo pontos de extremidade de alto valor.

Esta documentação aborda como usar o Servidor Azure Multi-Factor Authentication com o AD FS no Windows Server 2012 R2. Para obter informações sobre como usar o Azure Multi-Factor Authentication com o AD FS 2.0, consulte [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Protegendo o AD FS do Windows Server 2012 R2 com o Servidor Azure Multi-Factor Authentication

Ao instalar o Servidor Azure Multi-Factor Authentication, você tem as duas opções a seguir:

- Instalar o Servidor Azure Multi-Factor Authentication localmente no mesmo servidor que o AD FS 
- Instalar o Adaptador do Azure Multi-Factor Authentication localmente no Servidor do AD FS e instalar o Servidor MFA em outro computador

Antes de começar, esteja ciente das seguintes informações:

- Não é um requisito que o Servidor Azure Multi-Factor Authentication seja instalado no seu servidor de federação do AD FS. No entanto, o Adaptador do Multi-Factor Authentication para o AD FS deve ser instalado em um Windows Server 2012 R2 executando o AD FS. Você pode instalar o servidor em outro computador, desde que seja uma versão com suporte, e instalar o adaptador do AD FS separadamente no seu servidor de federação do AD FS. Consulte o procedimento abaixo para obter instruções sobre como instalar o adaptador separadamente.

- A conta conectada deve ter privilégios para criar grupos de segurança no Active Directory.

- O assistente de instalação do Adaptador do AD FS do Multi-Factor Authentication cria um grupo de segurança chamado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS do serviço de federação a esse grupo. É recomendável verificar no seu controlador de domínio se o grupo PhoneFactor Admins foi, de fato, criado e se a conta de serviço do AD FS é um membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.
  

### Para instalar o Servidor Azure Multi-Factor Authentication localmente no mesmo servidor que o AD FS

1. Baixe e instale o Servidor Azure Multi-Factor Authentication no servidor de federação do AD FS. Para saber mais sobre como instalar o Azure Multi-Factor Authentication, confira [Introdução ao servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md)
2. Na interface do usuário do Servidor Azure Multi-Factor Authentication, selecione o ícone do AD FS e as opções para Permitir registro do usuário e Permitir que usuários selecionem o método.
3. Selecione todas as opções adicionais.
4. Clique em Instalar Adaptador do AD FS
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se o computador tiver ingressado em um domínio e a configuração do Active Directory para proteger a comunicação entre o Adaptador do AD FS e o serviço do Azure Multi-Factor Authentication estiver incompleta, a etapa do Active Directory será exibida. Clique no botão Avançar para concluir automaticamente essa configuração ou marque a caixa de seleção Ignorar configuração automática do Active Directory e definir as configurações manualmente e clique em Avançar.
6. Se o computador não tiver ingressado em um domínio e a configuração do Grupo Local para proteger a comunicação entre o Adaptador do AD FS e o serviço do Azure Multi-Factor Authentication estiver incompleta, a etapa do Grupo Local será exibida. Clique no botão Avançar para concluir automaticamente essa configuração ou marque a caixa de seleção Ignorar configuração automática do Grupo Local e definir as configurações manualmente e clique em Avançar.
7. Isso iniciará o assistente de instalação. Clique em Avançar para permitir que o Servidor Azure Multi-Factor Authentication crie o grupo PhoneFactor Admins e adicione a conta de serviço do AD FS ao grupo PhoneFactor Admins.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Na etapa Iniciar Instalador, clique em Avançar.
9. No instalador do Adaptador do AD FS do Multi-Factor Authentication, clique em Avançar.
10. Clique em Fechar quando a instalação for concluída.
11. Agora que o adaptador foi instalado, ele deve ser registrado no AD FS. Abra o Windows PowerShell e execute o seguinte: C:\\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Agora, precisamos editar a Política de Autenticação Global no AD FS para usar nosso adaptador recentemente registrado. No Console de Gerenciamento do AD FS, navegue até o nó Políticas de Autenticação e, na seção Multi-factor Authentication, clique no link Editar ao lado da subseção Configurações Globais. Na janela Editar Política de Autenticação Global, selecione Multi-Factor Authentication como um método de autenticação adicional e clique em OK. O adaptador é registrado como WindowsAzureMultiFactorAuthentication. Você deve reiniciar o serviço AD FS para que o registro entre em vigor.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste ponto, o Servidor Multi-Factor Authentication está configurado para ser um provedor de autenticação adicional a ser usado com o AD FS.

## Para instalar o Adaptador do AD FS autônomo usando o SDK do serviço Web
1. Instale o SDK do Serviço Web no servidor que está executando o servidor Multi-Factor Authentication
2. Copie os arquivos MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 e MultiFactorAuthenticationAdfsAdapter.config do diretório \\Program Files\\Multi-Factor Authentication Server no servidor que você planeja instalar o Adaptador do AD FS.
3. Execute o MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do Adaptador do AD FS do Multi-Factor Authentication, clique em Avançar para executar a instalação.
5. Clique no botão Fechar quando a instalação for concluída.
6. Edite o arquivo MultiFactorAuthenticationAdfsAdapter.config fazendo o seguinte:

Etapa MultiFactorAuthenticationAdfsAdapter.config| Subetapa
:------------- | :------------- |
Defina o nó UseWebServiceSdk como verdadeiro.||
Defina WebServiceSdkUrl para a URL do SDK do Serviço Web do Multi-Factor Authentication.||
Opção 1: configurar o SDK do serviço Web com um nome de usuário e senha.|<ol><li>Defina WebServiceSdkUsername para uma conta que seja membro do grupo de segurança PhoneFactor Admins. Use o formato <domain><nome do usuário>.<li> Defina WebServiceSdkPassword para a senha da conta apropriada.</li></ol>
Opção 2: configurar o SDK do serviço Web com um certificado de cliente.|<ol><li>Obtenha um certificado de cliente de uma autoridade de certificação para o servidor que está executando o SDK do Serviço Web.</li><li>Importe o certificado do cliente para o repositório de certificado pessoal do computador local no servidor que está executando o SDK do Serviço Web. Observação: verifique se o certificado público da autoridade de certificação está em Certificados Raiz Confiáveis.</li><li>Exporte as chaves pública e privada do certificado de cliente para um arquivo .pfx.</li><li>Exporte a chave pública no formato de base 64 para um arquivo .cer.</li><li>No Gerenciador de Servidores, verifique se o recurso Servidor Web (IIS)\\Servidor Web\\Segurança\\Autenticação de Mapeamento de Certificado de Cliente está instalado.</li><li>Se não estiver instalado, escolha Adicionar Funções e Recursos para adicionar esse recurso.</li><li>No Gerenciador do IIS, clique duas vezes no Editor de Configuração no site que contém o diretório virtual do SDK do Serviço Web. Observação: é muito importante fazer isso no nível de site, e não no nível de diretório virtual.</li><li>Navegue até a seção system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Defina habilitado como verdadeiro.</li><li>Defina oneToOneCertificateMappingsEnabled como verdadeiro.</li><li>Clique no botão ... próximo a oneToOneMappings.</li><li>Clique no link Adicionar.</li><li>Abra o arquivo .cer de base 64 exportado anteriormente. Remova -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- e todas as quebras de linha. Copie a cadeia de caracteres resultante.</li><li>Defina o certificado para a cadeia de caracteres copiada na etapa anterior.</li><li>Defina habilitado como verdadeiro.</li><li>Defina userName para uma conta que seja um membro do grupo de segurança PhoneFactor Admins. Use o <domain>formato <nomedousuário>.</li><li> Defina a senha para a conta apropriada.</li><li>Feche o Editor de Coleção.</li><li>Clique no link Aplicar.</li><li>Navegue até o diretório virtual do SDK do Serviço Web.</li><li>Clique duas vezes em Autenticação.</li><li>Verifique se a Autenticação Básica e a Representação do ASP.NET estão Habilitadas e se todos os outros itens estão Desabilitados.</li><li>Navegue até o diretório virtual do SDK do Serviço Web novamente.</li><li>Clique duas vezes em Configurações de SSL.</li><li>Defina os Certificados de Cliente para Aceitar e clique em Aplicar.</li><li>Copie o arquivo .pfx exportado anteriormente para o servidor que está executando o Adaptador do AD FS.</li><li>Importe o arquivo .pfx para o repositório de certificado pessoal do computador local.</li><li>Escolha Gerenciar Chaves Privadas no menu de atalho e conceda acesso de leitura à conta a qual os Serviços de Federação do Active Directory estão conectados.</li><li>Abra o certificado de cliente e copie a impressão digital da guia Detalhes</li><li>No arquivo MultiFactorAuthenticationAdfsAdapter.config, defina WebServiceSdkCertificateThumbprint como a cadeia de caracteres copiada na etapa anterior.</li></ol>
Edite o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 adicionando -ConfigurationFilePath <path> ao fim do comando Register-AdfsAuthenticationProvider, em que <path> é o caminho completo para o arquivo MultiFactorAuthenticationAdfsAdapter.config.|


Execute o script \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 no PowerShell para registrar o adaptador. O adaptador é registrado como WindowsAzureMultiFactorAuthentication. Você deve reiniciar o serviço AD FS para que o registro entre em vigor.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=AcomDC_0218_2016-->