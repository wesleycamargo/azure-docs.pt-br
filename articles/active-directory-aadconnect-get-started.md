<properties 
	pageTitle="Introdução ao Azure AD Connect" 
	description="Saiba como baixar, instalar e executar o Assistente de instalação do Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Introdução ao Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/active-directory-aadconnect/" title="O que é" class="current">O que é</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-how-it-works/" title="Como funciona">Como funciona</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-get-started/" title="Introdução">Introdução</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-whats-next/" title="O que vem a seguir">O que vem a seguir</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-learn-more/" title="Saiba mais">Saiba mais</a>
</div>

A documentação a seguir o ajudará a começar com o Azure Active Directory Connect.

## Baixar o Azure AD Connect



Para começar a usar o Azure AD Connect , você pode baixar a versão mais recente usando o seguinte: [Baixar Visualização Pública do Azure AD Connect](http://connect.microsoft.com/site1164/program8612)

## Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, aqui estão algumas coisas que você precisará.

- Uma assinatura do Azure ou uma [assinatura de avaliação do Azure](http://azure.microsoft.com/pricing/free-trial/)
- O AD Premium do Azure ou uma [ avaliação do AD Premium do Azure](http://aka.ms/aadptrial)
- Uma conta de Administrador Global de AD do Azure para o locatário do AD do Azure com o qual você deseja se integrar
- Um controlador de domínio ou servidor membro do AD com o Windows Server 2008 ou posterior
- Uma conta de administrador corporativo para o Active Directory local
- Opcional: uma conta de usuário de teste para verificar a sincronização. 

Se você pretende usar o SSO com a opção AD FS, também precisará do seguinte:

- Credenciais de administrador local nos servidores de federação pretendidos.
- Credenciais de administrador local em qualquer servidor de grupo de trabalho (sem ingresso no domínio) no qual você pretende implantar a função de Proxy de aplicativo Web.
- Um servidor Windows Server 2012 R2 para o servidor de federação.
- Um servidor Windows Server 2012 R2 para o proxy de aplicativo Web.
-  Um arquivo .pfx com um certificado SSL para o nome do serviço de federação pretendido, por exemplo, fs.contoso.com.
- A máquina na qual você executa o assistente deve ser capaz de conectar a outros computadores nos quais você quer instalar o AD FS ou o Proxy de aplicativo Web por meio do Gerenciamento remoto do Windows.


## Instalando o Azure AD Connect

Depois de baixar o Azure AD Connect, use o seguinte para instalar Azure AD Connect.

1. Entre no servidor no qual deseja instalar o Azure AD Connect como um administrador corporativo.
2. Navegue até AzureADConnect.msi e clique duas vezes 
3. Percorra o assistente usando as configurações do Express ou personalizadas
4. Opcional: use sua conta de usuário de teste para entrar em um serviço de nuvem, como o Office 365, para testar.

<center>![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/aadConnect_Welcome.png)</center>

## Configurações opcionais de serviços de sincronização
Quando você instala os serviços de sincronização, pode deixar a seção de configuração opcional desmarcada e o Azure AD Connect configurará tudo automaticamente. Isso inclui a configuração de uma instância do SQL Server 2012 Express, a criação de grupos apropriados e atribuição de permissões a eles. Para alterar os padrões, use a tabela a seguir para entender as opções de configuração opcionais disponíveis.

Configuração opcional | Descrição 
------------- | ------------- |
Nome do SQL Server |Permite que você especifique o nome do SQL Server e o nome da instância. Escolha essa opção se já tiver o servidor de banco de dados do AD que você quer usar.
Conta de serviço |Por padrão, o Azure AD Connect irá criar uma conta de serviço para uso dos serviços de sincronização. O problema é que a senha é gerada automaticamente e desconhecida da pessoa que está instalando o Azure AD Connect. Na maioria dos cenários, isso é bom, mas se você quiser fazer algumas configurações avançadas, como escopo das unidades organizacionais sincronizadas, crie uma conta e escolha sua própria senha. |
Permissões | Por padrão, o Azure AD Connect irá criar quatro grupos quando os serviços de sincronização forem instalados. Esses grupos são: grupo de administradores, grupo de operadores, grupo de navegação e grupo de redefinição de senha. Se quiser especificar seus próprios grupos, você pode fazê-lo aqui.
Configurações de importação |Use esta opção se você estiver importando informações de configuração do DirSync do Azure AD Sync.|



## Instalação do Express
A seleção de configurações do Express é a opção padrão e é um dos cenários mais comuns. Ao fazer isso, o Azure AD Connect implanta a sincronização com a opção de sincronização de hash de senha. Isso é apenas para uma única floresta e permite que os usuários usem suas senhas locais para entrar na nuvem. O uso da instalação do Express inicia automaticamente uma sincronização quando instalação é concluída. Com essa opção, há apenas seis cliques curtos para estender seu diretório local para a nuvem.

<center>! [Instalação do Express] (. / media/active-directory-aadconnect-get-started/express.png)</center>

## Instalação personalizada

Com a instalação personalizada, você pode selecionar várias opções diferentes. A tabela a seguir descreve as páginas do assistente que estão disponíveis ao selecionar a opção de instalação personalizada.

Nome da página | Descrição
-------------------    | ------------- | 
Entrada do usuário|Nesta página, você pode escolher se deseja usar a sincronização de senha, federação com o AD FS ou nenhum dos dois.
Conectar aos diretórios|Nesta página, você pode adicionar um ou mais diretórios com os quais queira sincronizar.
Filtragem de sincronização| Aqui você pode determinar se deseja sincronizar todos os usuários e grupos ou se quer especificar um grupo por diretório e sincronizá-los apenas.
Identidades locais|Aqui você pode especificar que os usuários existam apenas uma vez em todos os diretórios adicionados ao Connect de sua página de diretórios ou que existam em vários diretórios. Se houver usuários em vários diretórios, você terá de escolher um atributo que identifique com exclusividade esses usuários nos diretórios. Por exemplo, os atributos de email, ObjectSID ou SAMAccountName são todos atributos comuns usados para identificar usuários com exclusividade.
Identidades do Azure|Nesta página, você especifica a âncora de origem que deseja usar para federação de identidade.
Recursos de opção|Use a tabela abaixo para obter uma descrição dos recursos opcionais que você pode selecionar.

<center>![Instalação do Express](./media/active-directory-aadconnect-get-started/of.png)</center>


Recursos opcionais | Descrição
-------------------    | ------------- | 
Implantação híbrida do Exchange  |O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Azure sincronizando um conjunto específico de atributos do AD do Azure de volta ao diretório local.
Aplicativo AD do Azure e filtragem de atributos|Ao habilitar o aplicativo AD do Azure e filtragem de atributo, o conjunto de atributos sincronizados pode ser adaptado para um conjunto específico em uma página subsequente do assistente. Isso abre duas páginas de configuração adicionais no assistente.  
Write-back de senha|Ao habilitar o write-back de senha, as alterações de senha que se originam com o AD do Azure serão gravadas no diretório local.
Write-back de usuário|Ao habilitar o write-back de usuário, os usuários criados no AD do Azure serão gravados de volta no diretório local. Isso abre uma página de configuração adicional no assistente.  
Sincronização de dispositivo|Ao habilitar a sincronização de dispositivos, a configuração do dispositivo pode ser gravada no AD do Azure.
Sincronização de atributo de extensão de diretório|Ao habilitar a sincronização de atributo de extensão de diretório, os atributos especificados serão sincronizados com o AD do Azure. Isso abre uma página de configuração adicional no assistente.  

Para opções de configuração adicionais, como alterar a configuração padrão, usando o Editor de regras de sincronização e Provisionamento declarativo, consulte [Gerenciar o Azure AD Connect](active-directory-aadconnect-whats-next.md)

## Componentes de suporte do Azure AD Connect

A seguir, uma lista de pré-requisitos e componentes de suporte que o Azure AD Connect irá instalar no servidor em que você instalou o Azure AD Connect. Esta lista é para uma instalação básica do Express. Se você optar por usar um SQL Server diferente na página de serviços de sincronização de instalação, os componentes do SQL Server 2012 listados abaixo não serão instalados.

- Forefront Identity Manager Azure Active Directory Connector
- Utilitários de linha de comando do Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- LocalDB do Microsoft SQL Server 2012 Express
- Módulo do Active Directory do Azure para Windows PowerShell
- Assistente de Conexão do Microsoft Online Services para profissionais de TI
- Pacote de redistribuição de Microsoft Visual C++ 2013


**Recursos adicionais**

* [Usar sua infraestrutura de identidade local na nuvem](active-directory-aadconnect.md)
* [Como o Azure AD Connect funciona](active-directory-aadconnect-how-it-works.md)
* [O que vem a seguir com o Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Saiba mais](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->