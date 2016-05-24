<properties 
	pageTitle="Azure Multi-Factor Authentication e autenticação IIS" 
	description="Esta é a página do Azure Multi-Factor Authentication que o auxiliará na implantação de autenticação IIS e do servidor Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/12/2016" 
	ms.author="billmath"/>

# Autenticação IIS

A seção autenticação IIS do servidor Azure Multi-Factor Authentication permite habilitar e configurar a autenticação IIS para integração com aplicativos Web Microsoft IIS. O servidor Azure Multi-Factor Authentication instala um plug-in que pode filtrar solicitações sendo feitas ao servidor Web IIS para o Azure Multi-Factor Authentication. O plug-in do IIS dá suporte para autenticação baseada em formulário e para autenticação integrada do Windows HTTP. As IPs confiáveis também podem ser configuradas para isentar os endereços IP de autenticação de dois fatores.


![Autenticação IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Usando a autenticação ISS baseada em formulário com o servidor Azure Multi-Factor Authentication

Para proteger um aplicativo Web do IIS que usa autenticação baseada em formulário, instale o servidor Azure Multi-Factor Authentication no servidor Web IIS e configure o servidor de acordo com o procedimento a seguir.

1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.
2. Clique na guia Baseado em Formulário.
3. Clique no botão Adicionar...
4. Para detectar o nome do usuário, a senha e as variáveis de domínio automaticamente, insira a URL de Logon (por ex., https://localhost/contoso/auth/login.aspx) na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário e clique em OK.
5. Marque a caixa correspondente do usuário Exigir Multi-Factor Authentication se todos os usuários tiverem sido ou vierem a ser importados para o servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis de página não puderem ser detectadas automaticamente, clique no botão Especificar Manualmente... na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.
7. Na caixa de diálogo Adicionar Site Baseado em Formulário, digite a URL para a página de logon no campo URL de envio e digite um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel. Consulte o arquivo de ajuda para obter mais informações sobre a URL de Envio. 
8. Selecione o formato correto da solicitação. Isso é definido como "POST ou GET" para a maioria dos aplicativos Web.
9. Insira a variável de nome de usuário, a variável de senha e a variável de domínio (se aparecer na página de logon). Talvez seja necessário navegar até a página de logon em um navegador da Web, clicar com o botão direito do mouse na página e selecionar "Exibir Fonte" para localizar os nomes das caixas de entrada na página de logon.
10. Marque a caixa correspondente do usuário Exigir Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados no Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.
11.  Clique no botão Avançado... para revisar as configurações avançadas, incluindo a capacidade de selecionar um arquivo de página de negação personalizado, de armazenar em cache autenticações bem-sucedidas no site por um período usando cookies e de selecionar se deve autenticar as credenciais principais com o Domínio do Windows, diretório LDAP ou servidor RADIUS. Ao concluir, clique no botão OK para retornar à caixa de diálogo Adicionar Site Baseado em Formulário. Consulte o arquivo de ajuda para obter mais informações sobre as configurações avançadas.
12. Clique no botão OK.
13. Depois que as variáveis de URL e página forem detectadas ou inseridas, os dados do site serão exibidos no painel Baseado em Formulário.
14. Consulte a seção Habilitar Plug-ins IIS para servidor Azure Multi-Factor Authentication diretamente abaixo para concluir a configuração de autenticação IIS. 

## Usando a autenticação integrada do Windows com o servidor Azure Multi-Factor Authentication

Para proteger um aplicativo Web do IIS que usa a autenticação HTTP integrada do Windows, instale o servidor Azure Multi-Factor Authentication no servidor Web IIS e configure o servidor de acordo com o procedimento a seguir.

1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação IIS no menu esquerdo.
2. Clique na guia HTTP. Clique na guia Baseado em Formulário.
3. Clique no botão Adicionar...
4. Na caixa de diálogo Adicionar URL Base, insira a URL para o site onde a autenticação HTTP é executada (por exemplo, http://localhost/owa) para a URL de base de dados de campo e digite um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
5. Ajuste o tempo limite de ociosidade e o tempo máximo de sessão se o padrão não for suficiente.
6. Marque a caixa correspondente do usuário Exigir Multi-Factor Authentication se todos os usuários tiverem sido ou vierem a ser importados para o servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso. 
7. Marque a caixa de cache de cookie se desejado.
8. Clique no botão OK.
9. Consulte a seção [Habilitar Plug-ins IIS para servidor Azure Multi-Factor Authentication](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) diretamente abaixo para concluir a configuração de autenticação IIS. 


## Habilitar Plug-ins IIS para o servidor Azure Multi-Factor Authentication

Depois de ter configurado as URLs e configurações de autenticação HTTP ou baseadas em formulário, você deve selecionar os locais onde os plug-ins IIS do Azure Multi-Factor Authentication devem ser carregados e habilitados no IIS. Use este procedimento:

1. Se executado no IIS 6, clique na guia ISAPI e selecione o site em que o aplicativo Web está sendo executado (por exemplo, Site padrão) para habilitar o filtro ISAPI do Azure Multi-Factor Authentication plug-in para o site.
2. Se estiver executando o IIS 7 ou posterior, clique na guia Módulo Nativo e selecione os servidores, sites ou aplicativos para habilitar o plug-in IIS nos níveis desejados.
3. Clique na caixa de autenticação Habilitar IIS na parte superior da tela. O Azure Multi-Factor Authentication agora está protegendo o aplicativo IIS selecionado. Verifique se os usuários foram importados para o servidor. Consulte a seção abaixo, IPs Confiáveis, se desejar colocar os endereços IP internos na lista banca para que a autenticação multifator não seja exigida na conexão com o site a partir desses locais. 


## IPs Confiáveis

Os IPs Confiáveis permitem que os usuários ignorem o Azure Multi-Factor Authentication para solicitações de site que se originam de sub-redes ou endereços IP específicos. Por exemplo, convém isentar os usuários do Azure Multi-Factor Authentication durante o logon feito no escritório. Para isso, você deve especificar a sub-rede do escritório como uma entrada de IPs Confiáveis. Para configurar IPs confiáveis, use o seguinte procedimento:

1. Na seção Autenticação IIS, clique na guia de IPs Confiáveis. 
2. Clique no botão Adicionar...
3. Quando a caixa de diálogo Adicionar IPs Confiáveis aparecer, selecione o botão de opção IP Único, Intervalo de IP ou Sub-rede.
4. Insira o endereço IP, o intervalo dos endereços IP ou a sub-rede que devem ser colocados na lista branca. Se inserir uma sub-rede, selecione a Máscara de Rede apropriada e clique no botão OK. A lista branca agora foi adicionada.

<!---HONumber=AcomDC_0518_2016-->