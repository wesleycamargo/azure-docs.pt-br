<properties 
	pageTitle="Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS 2.0." 
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
	ms.date="11/17/2015" 
	ms.author="billmath"/>
# Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0

Se sua organização for federada com o Active Directory do Azure e você tiver recursos locais ou na nuvem que deseja proteger, é possível fazer isso usando o Servidor Azure Multi-Factor Authentication e configurando-o para trabalhar com o AD FS para que a autenticação multifator seja acionada pelo pontos de extremidade de alto valor.

Esta documentação aborda como usar o Servidor Azure Multi-Factor Authentication com o AD FS 2.0. Para obter informações sobre como usar o Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2, consulte [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy do AD FS 2.0
Para proteger o AD FS 2.0 com um proxy, instale o Servidor Azure Multi-Factor Authentication no servidor proxy do ADFS e configure o Servidor de acordo com as etapas a seguir.

### Para proteger o AD FS 2.0 com um proxy

1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.
2. Clique na guia Baseado em Formulário.
3. Clique no botão Adicionar...
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Para detectar o nome do usuário, a senha e as variáveis de domínio automaticamente, insira a URL de Logon (por ex., https://sso.contoso.com/adfs/ls) na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário e clique em OK.
5. Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.
6. Se as variáveis de página não puderem ser detectadas automaticamente, clique no botão Especificar Manualmente... na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.
7. Na caixa de diálogo Adicionar Site Baseado em Formulário, insira a URL para a página de logon do AD FS no campo URL de Envio (por ex., https://sso.contoso.com/adfs/ls) e insira um nome para o aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel. Consulte o arquivo de ajuda para obter mais informações sobre a URL de Envio.
8. Defina o formato da solicitação como “POST ou GET”.
9. Insira a variável do Nome do usuário (ctl00$ContentPlaceHolder1$UsernameTextBox) e a variável da Senha (ctl00$ContentPlaceHolder1$PasswordTextBox). Se a página de logon baseada em formulário exibir uma caixa de texto de domínio, insira a variável do domínio também. Talvez seja necessário navegar até a página de logon em um navegador da Web, clicar com o botão direito do mouse na página e selecionar "Exibir Fonte" para localizar os nomes das caixas de entrada na página de logon.
10. Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Clique no botão Avançado... para revisar as configurações avançadas, incluindo a capacidade de selecionar um arquivo de página de negação personalizado, armazenar em cache autenticações bem-sucedidas no site por um período usando cookies e selecionar como autenticar as credenciais principais.
12. Como não há probabilidade de o servidor proxy do ADFS se unir ao domínio, você provavelmente usará LDAP para se conectar ao seu controlador de domínio para importação de usuário e pré-autenticação. Na caixa de diálogo Site Avançado Baseado em Formulário, clique na guia Autenticação Primária e selecione "Associação LDAP" para o tipo de autenticação Pré-autenticação.
13. Ao concluir, clique no botão OK para retornar à caixa de diálogo Adicionar Site Baseado em Formulário. Consulte o arquivo de ajuda para obter mais informações sobre as configurações avançadas.
14. Clique no botão OK para fechar a caixa de diálogo.
15. Depois que as variáveis de URL e página forem detectadas ou inseridas, os dados do site serão exibidos no painel Baseado em Formulário.
16. Clique na guia Módulo Nativo e selecione o servidor, o site em que proxy do ADFS está em execução (por ex., “Site Padrão”) ou o aplicativo de proxy do ADFS (por ex., “Está” em “adfs”) para habilitar o plug-in do IIS no nível desejado.
17. Clique na caixa de autenticação Habilitar IIS na parte superior da tela.
18. Agora, a autenticação do IIS está habilitada. No entanto, para executar a pré-autenticação no AD (Active Directory) via LDAP, você deve configurar a conexão LDAP com o controlador de domínio. Para fazer isso, clique no ícone de Integração de Diretórios.
19. Na guia Configurações, selecione o botão de opção Usar configuração de LDAP específica.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Clique no botão Editar...
21. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao controlador de domínio do AD. As descrições dos campos estão incluídas na tabela abaixo. Observação: essas informações também estão incluídas no arquivo de ajuda do Servidor Azure Multi-Factor Authentication.
22. Teste a conexão LDAP clicando no botão Testar.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Se o teste de conexão LDAP foi bem-sucedido, clique no botão OK.
24. Em seguida, clique no ícone de Configurações da Empresa e selecione a guia Resolução de Nome de Usuário.
25. Selecione o botão de opção Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário.
26. Se os usuários inserirem o respectivo nome de usuário no formulário de logon do proxy do ADFS no formato “domínio\\nome de usuário”, o Servidor precisará estar apto a revelar o domínio do nome de usuário quando ele criar a consulta LDAP. Isso pode ser feito por meio de uma configuração do registro.
27. Abra o editor de registro e vá para HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor em um servidor de 64 bits. Em um servidor de 32 bits, retire “Wow6432Node” do caminho. Crie uma chave de registro DWORD chamada “UsernameCxz\_stripPrefixDomain” e defina o valor para 1. Agora o Azure Multi-Factor Authentication está protegendo o proxy do ADFS. Verifique se os usuários foram importados do Active Directory no Servidor. Consulte a seção abaixo, IPs Confiáveis, se desejar colocar os endereços IP internos na lista banca para que a autenticação multifator não seja exigida na conexão com o site a partir desses locais.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct sem um proxy

Para proteger o AD FS quando o proxy do AD FS não for usado, instale o Servidor Azure Multi-Factor Authentication no servidor AD FS e configure o Servidor de acordo com as etapas a seguir.

### Para proteger o AD FS 2.0 sem um proxy
1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.
2. Clique na guia HTTP.
3. Clique no botão Adicionar...
4. Na caixa de diálogo Adicionar URL Base, insira a URL para o site do AD FS onde a autenticação HTTP é realizada (por ex., https://sso.domain.com/adfs/ls/auth/integrated) no campo URL Base e insira um nome para o aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
5. Se desejar, ajuste o Tempo limite de ociosidade e o Tempo máximo da sessão.
6. Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.
7. Marque a caixa de cache de cookie se desejado.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Clique no botão OK.
9. Clique na guia Módulo Nativo e selecione o servidor, o site em que o ADFS está em execução (por ex., “Site Padrão”) ou o aplicativo do ADFS (por ex., “Está” em “adfs”) para habilitar o plug-in do IIS no nível desejado.
10. Clique na caixa de autenticação Habilitar IIS na parte superior da tela. Agora, o Azure Multi-Factor Authentication está protegendo o ADFS. Verifique se os usuários foram importados do Active Directory no Servidor. Consulte a seção abaixo, IPs Confiáveis, se desejar colocar os endereços IP internos na lista banca para que a autenticação multifator não seja exigida na conexão com o site a partir desses locais.


## IPs Confiáveis
Os IPs Confiáveis permitem que os usuários ignorem o Azure Multi-Factor Authentication para solicitações de site que se originam de sub-redes ou endereços IP específicos. Por exemplo, convém isentar os usuários do Azure Multi-Factor Authentication durante o logon feito no escritório. Para isso, você deve especificar a sub-rede do escritório como uma entrada de IPs Confiáveis.

### Para configurar IPs confiáveis


1. Na seção Autenticação IIS, clique na guia de IPs Confiáveis.
1. Clique no botão Adicionar...
1. Quando a caixa de diálogo Adicionar IPs Confiáveis aparecer, selecione o botão de opção IP Único, Intervalo de IP ou Sub-rede.
1. Insira o endereço IP, o intervalo dos endereços IP ou a sub-rede que devem ser colocados na lista branca. Se inserir uma sub-rede, selecione a Máscara de Rede apropriada e clique no botão OK. O IP confiável foi adicionado.


<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=Nov15_HO4-->