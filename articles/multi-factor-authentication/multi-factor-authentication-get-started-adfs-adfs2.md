<properties 
	pageTitle="Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS 2.0." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>
# Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0

Se sua organização for federada com o Active Directory do Azure e você tiver recursos locais ou na nuvem que deseja proteger, é possível fazer isso usando o Servidor Azure Multi-Factor Authentication e configurando-o para trabalhar com o AD FS para que a autenticação multifator seja acionada pelo pontos de extremidade de alto valor.

Esta documentação aborda como usar o Servidor Azure Multi-Factor Authentication com o AD FS 2.0. Para obter informações sobre como usar o Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2, consulte [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy do AD FS 2.0
Para proteger o AD FS 2.0 com um proxy, instale o Servidor Azure Multi-Factor Authentication no servidor proxy do ADFS e configure o Servidor de acordo com as etapas a seguir.

### Para proteger o AD FS 2.0 com um proxy
<ol>
<li>No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.</li>
<li>Clique na guia Baseado em Formulário.</li>
<li>Clique no botão Adicionar...</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>Para detectar o nome do usuário, a senha e as variáveis de domínio automaticamente, insira a URL de Logon (por ex., https://sso.contoso.com/adfs/ls) na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário e clique em OK.</li>
<li>Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.</li>
<li>Se as variáveis de página não puderem ser detectadas automaticamente, clique no botão Especificar Manualmente... na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.</li>
<li>Na caixa de diálogo Adicionar Site Baseado em Formulário, insira a URL à página de logon do ADFS no campo URL de Envio (por ex., https://sso.contoso.com/adfs/ls) e insira um nome para o aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel. Consulte o arquivo de ajuda para obter mais informações sobre a URL de Envio.</li>
<li>Defina o formato da solicitação como “POST ou GET”.</li>
<li>Insira a variável do Nome do usuário (ctl00$ContentPlaceHolder1$UsernameTextBox) e a variável da Senha (ctl00$ContentPlaceHolder1$PasswordTextBox). Se a página de logon baseada em formulário exibir uma caixa de texto de domínio, insira a variável do domínio também. Talvez seja necessário navegar até a página de logon em um navegador da Web, clicar com o botão direito do mouse na página e selecionar "Exibir Fonte" para localizar os nomes das caixas de entrada na página de logon.</li>
<li>Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>Clique no botão Avançado... para revisar as configurações avançadas, incluindo a capacidade de selecionar um arquivo de página de negação personalizado, armazenar em cache autenticações bem-sucedidas no site por um período usando cookies e selecionar como autenticar as credenciais principais.</li>
<li>Como não há probabilidade de o servidor proxy do ADFS se unir ao domínio, você provavelmente usará LDAP para se conectar ao seu controlador de domínio para importação de usuário e pré-autenticação. Na caixa de diálogo Site Avançado Baseado em Formulário, clique na guia Autenticação Primária e selecione "Associação LDAP" para o tipo de autenticação Pré-autenticação.</li>
<li>Ao concluir, clique no botão OK para retornar à caixa de diálogo Adicionar Site Baseado em Formulário. Consulte o arquivo de ajuda para obter mais informações sobre as configurações avançadas.</li>
<li>Clique no botão OK para fechar a caixa de diálogo.</li>
<li>Depois que as variáveis de URL e página forem detectadas ou inseridas, os dados do site serão exibidos no painel Baseado em Formulário.</li>
<li>Clique na guia Módulo Nativo e selecione o servidor, o site em que proxy do ADFS está em execução (por ex., “Site Padrão”) ou o aplicativo de proxy do ADFS (por ex., “Está” em “adfs”) para habilitar o plug-in do IIS no nível desejado.</li>
<li>Clique na caixa de autenticação Habilitar IIS na parte superior da tela.</li>
<li>Agora, a autenticação do IIS está habilitada. No entanto, para executar a pré-autenticação no AD (Active Directory) via LDAP, você deve configurar a conexão LDAP com o controlador de domínio. Para fazer isso, clique no ícone de Integração de Diretórios.</li>
<li>Na guia Configurações, selecione o botão de opção Usar configuração de LDAP específica.</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>Clique no botão Editar...</li>
<li>Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao controlador de domínio do AD. As descrições dos campos estão incluídas na tabela abaixo. Observação: essas informações também estão incluídas no arquivo de ajuda do Servidor Azure Multi-Factor Authentication.</li>

Campo| Descrição
:------------- | :-------------
Servidor|Insira o nome do host ou o endereço IP do servidor que está executando o diretório LDAP. Um servidor de backup também pode ser especificado separado por ponto-e-vírgula.<br> **Observação:** quando o Tipo de Associação for SSL, normalmente, um nome de host totalmente qualificado será exigido e ele deverá corresponder ao nome no certificado SSL instalado no servidor de diretório LDAP. 
DN base|Insira o nome diferenciado do objeto de diretório base do qual todas as consultas de diretório serão iniciadas. Por exemplo, dc=contoso,dc=com.
Tipo de associação|Selecione o tipo de associação apropriado a ser usado na associação do diretório LDAP à pesquisa. Ele é usado para importações, sincronização e resolução de nome de usuário.<ul><li>**Anônimo** — uma associação anônima será realizada. O DN de Associação e a Senha de Associação não serão usados. Isso funcionará apenas se o diretório LDAP permitir associação anônima e as permissões permitirem a consulta dos registros e atributos apropriados.</li><li>**Simples** — O DN de Associação e a Senha de Associação serão transferidos como texto sem formatação para associação ao diretório LDAP. Isso deve ser usado apenas para testes, a fim de verificar se o servidor pode ser acessado e se a conta de associação tem o acesso apropriado. Nesse caso, é recomendável usar o SSL depois que o certificado apropriado tiver sido instalado.</li><li>**SSL** — O DN de Associação e a Senha de Associação serão criptografados usando SSL para associação ao diretório LDAP. Isso exige que um certificado seja instalado no servidor do diretório LDAP em que o Servidor Azure Multi-Factor Authentication confia.</li><li>**Windows** — O Nome de Usuário de Associação e a Senha de Associação serão usados para conexão com segurança a um controlador de domínio do Active Directory ou diretório ADAM. Se Nome de Usuário de Associação for deixado em branco, a conta do usuário conectado será usada na associação.</li></ul> 
Nome de Usuário de Associação|Insira o nome diferenciado do registro do usuário para a conta a ser usada na associação ao diretório LDAP. O nome diferenciado de associação é usado apenas quando Tipo de Associação é Simples ou SSL.    
Senha de associação|Insira a senha de associação para o DN ou nome de usuário de associação que está sendo usado para associação ao diretório LDAP. Para configurar a senha do Serviço AdSync do Servidor Multi-Factor Auth, a sincronização deve ser habilitada e o serviço deve estar em execução no computador local. A senha será salva em Nomes de Usuário e Senhas Armazenados no Windows na conta em que o Serviço AdSync do Servidor Azure Multi-Factor Authentication está em execução. A senha também será salva na conta em que a interface de usuário do Servidor Multi-Factor Auth está em execução e na conta em que o Serviço do Servidor Azure Multi-Factor Authentication está em execução. Observação: uma vez que a senha é armazenada somente em Nomes de Usuário e Senhas Armazenados do Windows do servidor local, essa etapa precisará ser executada em cada Servidor Multi-Factor Auth que precise de acesso à senha. 
Limite de tamanho de consulta|Especifique o limite de tamanho para o número máximo de usuários que uma pesquisa de diretório retornará. Esse limite deve corresponder à configuração no diretório LDAP. Em pesquisas grandes, em que a paginação não tem suporte, a importação e a sincronização tentarão recuperar os usuários em lotes. Se o limite de tamanho especificado aqui for maior que o limite configurado no diretório LDAP, alguns usuários poderão ser perdidos. 



<li>Teste a conexão LDAP clicando no botão Testar.</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>Se o teste de conexão LDAP foi bem-sucedido, clique no botão OK.</li>
<li>Em seguida, clique no ícone de Configurações da Empresa e selecione a guia Resolução de Nome de Usuário.</li>
<li>Selecione o botão de opção Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário.</li>
<li>Se os usuários inserirem o respectivo nome de usuário no formulário de logon do proxy do ADFS no formato “domínio\nome de usuário”, o Servidor precisará estar apto a revelar o domínio do nome de usuário quando ele criar a consulta LDAP. Isso pode ser feito por meio de uma configuração do registro.</li>
<li>Abra o editor de registro e vá para HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor em um servidor de 64 bits. Em um servidor de 32 bits, retire “Wow6432Node” do caminho. Crie uma chave de registro DWORD chamada “UsernameCxz_stripPrefixDomain” e defina o valor para 1. Agora o Azure Multi-Factor Authentication está protegendo o proxy do ADFS. Verifique se os usuários foram importados do Active Directory no Servidor. Consulte a seção abaixo, IPs Confiáveis, se desejar colocar os endereços IP internos na lista banca para que a autenticação multifator não seja exigida na conexão com o site a partir desses locais.</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct sem um proxy

Para proteger o AD FS quando o proxy do AD FS não for usado, instale o Servidor Azure Multi-Factor Authentication no servidor AD FS e configure o Servidor de acordo com as etapas a seguir.

### Para proteger o AD FS 2.0 sem um proxy
<ol>
<li>No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.</li>
<li>Clique na guia HTTP.</li>
<li>Clique no botão Adicionar...</li>
<li>Na caixa de diálogo Adicionar URL Base, insira a URL para o site do ADFS onde a autenticação HTTP é realizada (por ex., https://sso.domain.com/adfs/ls/auth/integrated) no campo URL Base e insira um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.</li>
<li>Se desejar, ajuste o Tempo limite de ociosidade e o Tempo máximo da sessão.</li>
<li>Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.</li>
<li>Marque a caixa de cache de cookie se desejado.</li>

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>Clique no botão OK.</li>
<li>Clique na guia Módulo Nativo e selecione o servidor, o site em que o ADFS está em execução (por ex., “Site Padrão”) ou o aplicativo do ADFS (por ex., “Está” em “adfs”) para habilitar o plug-in do IIS no nível desejado.</li>
<li>Clique na caixa de autenticação Habilitar IIS na parte superior da tela. Agora, o Azure Multi-Factor Authentication está protegendo o ADFS. Verifique se os usuários foram importados do Active Directory no Servidor. Consulte a seção abaixo, IPs Confiáveis, se desejar colocar os endereços IP internos na lista banca para que a autenticação multifator não seja exigida na conexão com o site a partir desses locais.</li>

## IPs Confiáveis
Os IPs Confiáveis permitem que os usuários ignorem o Azure Multi-Factor Authentication para solicitações de site que se originam de sub-redes ou endereços IP específicos. Por exemplo, convém isentar os usuários do Azure Multi-Factor Authentication durante o logon feito no escritório. Para isso, você deve especificar a sub-rede do escritório como uma entrada de IPs Confiáveis.

### Para configurar IPs confiáveis

<ol>
<li>Na seção Autenticação IIS, clique na guia de IPs Confiáveis.</li>
<li>Clique no botão Adicionar...</li>
<li>Quando a caixa de diálogo Adicionar IPs Confiáveis aparecer, selecione o botão de opção IP Único, Intervalo de IP ou Sub-rede.</li>
<li>Insira o endereço IP, o intervalo dos endereços IP ou a sub-rede que devem ser colocados na lista branca. Se inserir uma sub-rede, selecione a Máscara de Rede apropriada e clique no botão OK. A lista branca agora foi adicionada.</li>


<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->