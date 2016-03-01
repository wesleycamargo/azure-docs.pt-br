<properties 
	pageTitle="Práticas recomendadas de segurança para o uso do Azure MFA" 
	description="Este documento fornece as práticas recomendadas sobre o uso do Azure MFA com contas do Azure" 
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

# Práticas recomendadas de segurança para o uso do Azure Multi-Factor Authentication com contas do AD do Azure

Quando se trata de aprimorar o processo de autenticação, a autenticação multifator é a opção preferencial para a maioria das organizações. O MFA (Azure Multi-Factor Authentication) habilita as empresas a atender aos seus requisitos de segurança e conformidade, ao mesmo tempo que fornece uma experiência de conexão simples para seus usuários. Este artigo abordará algumas práticas recomendadas que você deve considerar ao planejar a adoção do Azure MFA.

## Práticas recomendadas para o Azure Multi-Factor Authentication na nuvem
Para fornecer a todos os usuários a autenticação multifator e aproveitar as vantagens dos recursos estendidos oferecidas pelo Azure Multi-Factor Authentication, você precisará habilitar o Azure Multi-Factor Authentication em todos os seus usuários. Isso é feito usando um dos seguintes:

- Azure AD Premium ou Enterprise Mobility Suite 
- Provedor de Multi-Factor Authentication

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

A primeira etapa recomendada para a adoção do Azure MFA na nuvem com o uso do Azure AD Premium ou do Enterprise Mobility Suite é atribuir licenças aos usuários. O Azure Multi-Factor Authentication faz parte desses pacotes e, assim, sua organização não precisa de mais nada para estender a funcionalidade de autenticação multifator para todos os usuários.
 
Ao configurar o Multi-Factor Authentication, considere o seguinte:

- Você não precisa criar um Provedor de Multi-Factor Authentication. O Azure AD Premium e o Enterprise Mobility Suite são incluídos no Azure Multi-Factor Authentication. Se você criar um Provedor de Autenticação, você pode ser cobrado duas vezes.
- O Azure AD Connect é apenas um requisito se você estiver sincronizando o ambiente do Active Directory local com um diretório do AD do Azure. Se você usar apenas um diretório do AD do Azure que não está sincronizado com uma instância local do Active Directory, você não precisará do Azure AD Connect.


### Provedor de Multi-Factor Authentication

![Provedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se você não tiver o Azure AD Premium ou o Enterprise Mobility Suite, a primeira etapa recomendada para a adoção do Azure MFA na nuvem é criar um Provedor de Autenticação de MFA. Embora o MFA esteja disponível por padrão para os administradores globais que têm o Active Directory do Azure, quando estiver implantando o MFA para sua organização, você precisará estender a capacidade de autenticação multifator para todos os usuários; para fazer isso, você precisa de um Provedor de Multi-Factor Authentication. Ao selecionar o Provedor de Autenticação, você precisa selecionar um diretório e considerar o seguinte:

- Não é necessário um diretório do AD do Azure para criar um Provedor do Multi-Factor Auth. 
- Você precisará associar o Provedor de Multi-Factor Authentication a um diretório do AD do Azure se desejar estender a autenticação multifator a todos os seus usuários e/ou desejar que seus administradores globais possam aproveitar os recursos, como o portal de gerenciamento, as saudações personalizadas e os relatórios.
- O DirSync ou AAD Sync são apenas um requisito se você estiver sincronizando o ambiente do Active Directory local com um diretório do AD do Azure. Se você usa apenas um diretório do AD do Azure que não está sincronizado com uma instância local do Active Directory, não haverá necessidade do DirSync ou AAD Sync.
- Se tiver o Azure AD Premium ou o Enterprise Mobility Suite, você não precisará criar um Provedor do Multi-Factor Auth. Você só precisa atribuir uma licença a um usuário e começar a ativar o MFA para os usuários.
- Certifique-se de escolher o modelo de uso correto para a sua empresa (por autenticação ou por usuário habilitado); depois de selecionar o modelo de uso, não é possível alterá-lo.

### Conta de usuário
Ao habilitar o MFA para seus usuários, as contas de usuário podem estar em um dos três estados principais: desabilitado, habilitado ou imposto. Use as diretrizes abaixo para garantir que você está usando a opção mais apropriada para a sua implantação:

- Quando o estado do usuário é definido como desabilitado, esse usuário não usa a autenticação multifator. Esse é o valor padrão.
- Quando o estado do usuário é definido como habilitado, isso significa que o usuário está habilitado, mas não concluiu o processo de registro. Eles serão solicitados a concluir o processo na próxima vez que se conectarem. Essa configuração não afeta os aplicativos sem navegador. Todos os aplicativos continuarão funcionando até que o processo de registro seja concluído.
- Quando o estado do usuário é definido como imposto, isso significa que o usuário pode ou não ter concluído o registro. Se tiver concluído o processo de registro, significa que ele está usando o Multi-Factor Authentication. Caso contrário, o usuário será solicitado a concluir o processo de registro na próxima vez que se conectar. Essa configuração afeta os aplicativos sem navegador. Esses aplicativos não funcionarão até que as senhas de aplicativo sejam criadas e usadas.
- Use o Modelo de Notificação do Usuário disponível no artigo [Introdução ao Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-get-started-cloud.md) para enviar um email aos usuários sobre a adoção do MFA.

### Capacidade de suporte

Como a maioria dos usuários estão acostumados a usar apenas senhas para a autenticação, é importante que a sua empresa conscientize todos os usuários sobre esse processo. Essa conscientização pode reduzir a probabilidade de que os usuários chamarão seu suporte técnico para resolver pequenos problemas relacionados ao MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

- Certifique-se de que sua equipe de suporte técnico é treinada para lidar com cenários em que o aplicativo móvel ou o telefone não recebe uma notificação ou chamada telefônica, impossibilitando a conexão do usuário. Ela pode habilitar uma opção de desvio único para permitir que um usuário faça a autenticação uma única vez ao “desviar” a autenticação multifator. O desvio é temporário e expira após o número especificado de segundos. 
- Se necessário, você pode aproveitar a funcionalidade de IPs Confiáveis no Azure MFA. Este recurso permite que os administradores de um locatário gerenciado ou federado ignorem a autenticação multifator para usuários que entram pela intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças do Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.


## Práticas recomendadas para o Azure Multi-Factor Authentication local
Se a sua empresa decidiu aproveitar sua própria infraestrutura para habilitar o MFA, será necessário implantar um Servidor Azure Multi-Factor Authentication localmente. Os componentes do Servidor MFA são mostrados no seguinte diagrama:

![Provedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/server.png) *Não instalado por padrão **Instalado, mas não habilitado por padrão


O Servidor Azure Multi-Factor Authentication pode ser usado para proteger recursos de nuvem e recursos locais que são acessados por contas do AD do Azure. No entanto, isso só pode realizado por meio da federação. Ou seja, você deve ter o AD FS e torná-lo federado com seu locatário do AD do Azure. Ao configurar o Servidor Multi-Factor Authentication, considere o seguinte:

- Se estiver protegendo os recursos do AD do Azure usando os Serviços de Federação do Active Directory, o 1º fator de autenticação é executado localmente usando o AD FS e o 2º fator é executado localmente respeitando a declaração.
- Não é um requisito que o Servidor Azure Multi-Factor Authentication seja instalado no seu servidor de federação do AD FS. No entanto, o Adaptador do Multi-Factor Authentication para o AD FS deve ser instalado em um Windows Server 2012 R2 executando o AD FS. Você pode instalar o servidor em outro computador, desde que seja uma versão com suporte, e instalar o adaptador do AD FS separadamente no seu servidor de federação do AD FS. Consulte o procedimento abaixo para obter instruções sobre como instalar o adaptador separadamente.
- O assistente de instalação do Adaptador do AD FS do Multi-Factor Authentication cria um grupo de segurança chamado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS do serviço de federação a esse grupo. É recomendável verificar no seu controlador de domínio se o grupo PhoneFactor Admins foi, de fato, criado e se a conta de serviço do AD FS é um membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.

### Portal do Usuário
Esse portal é executado em um site do IIS (Internet Information Server), que permite recursos de autoatendimento e que fornece um conjunto completo de recursos de administração de usuários. Use as diretrizes abaixo para configurar este componente:

- É necessário o IIS 6 ou posterior
- O ASP.NET v2.0.507207 precisa ser instalado e registrado
- Esse servidor pode ser implantado em uma rede de perímetro.



### Senhas de aplicativo
Se a sua organização for federada usando o SSO com o AD do Azure e você pretender usar o Azure MFA, lembre-se do seguinte ao usar as senhas do aplicativo (lembre-se de que isso só se aplica se o SSO federado for usado):

- A Senha de Aplicativo é verificada pelo AD do Azure e, portanto, não utiliza a federação. A federação é usada apenas durante a configuração da Senha de Aplicativo.
- Para os usuários federados (SSO), as senhas serão armazenadas na ID organizacional. Se o usuário sair da empresa, essa informação deve ir para a ID organizacional usando o DirSync em tempo real. A desabilitação/exclusão da conta pode levar até 3 horas para sincronizar, atrasando a desabilitação/exclusão da Senha de aplicativo no Azure AD.
- As configurações do Controle de Acesso do Cliente local não são consideradas pela Senha de aplicativo
- Nenhum recurso de registro de autenticação local/auditoria recurso está disponível para a Senha de aplicativo
- É preciso que haja mais treinamento do usuário final é necessário para o cliente Microsoft Lync 2013. 
- Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a autenticação multifator com os clientes, dependendo de onde eles se autenticam. Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.
- Por padrão, os usuários não podem criar senhas de aplicativo; se a sua empresa exigir isso ou se você precisar permitir que os usuários criem senhas de aplicativo em alguns cenários, verifique se a opção Permitir que os usuários criem senhas de aplicativo para se conectar a aplicativos sem navegador está marcada.

## Considerações adicionais
Use a lista abaixo para entender algumas considerações adicionais e práticas recomendadas para cada componente que será implantado localmente:

Método|Descrição
:------------- | :------------- | 
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre como configurar o Azure Multi-Factor Authentication com o AD FS.
[Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md)| Informações sobre instalação e como configurar o Servidor Azure MFA com o RADIUS.
[Autenticação IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre instalação e como configurar o Servidor Azure MFA com o IIS.
[Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md)| Informações sobre instalação e como configurar o Servidor Azure MFA com a Autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre instalação e como configurar o Servidor Azure MFA com a Autenticação LDAP.
[Gateway de Área de Trabalho Remota e Servidor Azure Multi-Factor Authentication usando RADIUS](multi-factor-authentication-get-started-server-rdg.md)| Informações sobre instalação e como configurar o Servidor Azure MFA com o Gateway de Área de Trabalho Remota usando RADIUS.
[Sincronizar com o Active Directory do Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre instalação e como configurar a sincronização entre o Active Directory e o Servidor Azure MFA.
[Implantando o Serviço Web do aplicativo móvel do Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre instalação e como configurar o serviço Web do Servidor Azure MFA.
[Configurações de VPN avançadas com o Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md)|Informações sobre como configurar dispositivos de VPN Cisco ASA, Citrix Netscaler e Juniper/Pulse Secure usando o LDAP ou o RADIUS. 


## Recursos adicionais
Embora este artigo destaque algumas práticas recomendadas para o Azure MFA, há outros recursos que você também pode usar ao planejar sua implantação do MFA. A lista abaixo contém alguns artigos principais que podem ajudá-lo durante esse processo:

- [Relatórios no Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [Experiência de configuração do Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
- [Perguntas frequentes sobre o Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0218_2016-->