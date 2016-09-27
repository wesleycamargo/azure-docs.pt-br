<properties 
	pageTitle="Servidor Azure Multi-Factor Authentication e autenticação LDAP"
	description="Essa é a página Azure Multi-Factor Authentication que auxiliará na implantação de autenticação LDAP e do servidor Azure Multi-Factor Authentication."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Servidor Azure Multi-Factor Authentication e autenticação LDAP


Por padrão, o Servidor do Azure Multi-Factor Authentication é configurado para importar ou sincronizar usuários do Active Directory. No entanto, ele pode ser configurado para associar a diferentes diretórios LDAP, como um diretório ADAM ou controlador de domínio específico do Active Directory. Quando configurado para se conectar a um diretório via LDAP, o servidor Azure Multi-Factor Authentication pode ser configurado para atuar como um proxy LDAP e realizar autenticações. Ele também permite o uso da associação LDAP como um destino RADIUS para pré-autenticação dos usuários ao usar a autenticação IIS ou autenticação principal no Portal de usuário do Azure Multi-Factor Authentication.

Ao usar autenticação multifator do Azure como um proxy LDAP, o servidor Azure Multi-Factor Authentication é inserido entre o cliente LDAP (por exemplo, o dispositivo ou aplicativo VPN) e o servidor do diretório LDAP para adicionar autenticação multifator. Para o Azure multi-Factor Authentication funcionar, o servidor Azure multi-Factor Authentication deve ser configurado para se comunicar com servidores do cliente e com o diretório LDAP. Nessa configuração, o servidor Azure Multi-Factor Authentication aceita solicitações LDAP dos aplicativos e servidores clientes e os encaminha para o servidor de diretório LDAP de destino para validar as credenciais principais. Se a resposta do diretório LDAP mostrar que as credenciais primárias são válidas, o Azure Multi-Factor Authentication executa a autenticação de dois fatores e envia uma resposta ao cliente LDAP. A autenticação completa só terá êxito se a autenticação para o servidor LDAP e a autenticação multifator tiverem êxito.





## Configuração de autenticação LDAP


Para configurar a autenticação LDAP, instale o servidor Azure Multi-Factor Authentication em um servidor Windows. Use este procedimento:

1. No servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação LDAP no menu esquerdo.
2. Marque a caixa de seleção Habilitar autenticação LDAP. ![Autenticação LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Na guia Clientes, altere a porta TCP e a porta SSL se o serviço LDAP do Azure Multi-Factor Authentication tiver que se associar às portas não padrão para escutar solicitações LDAP dos clientes que serão configurados.
4. Se você planeja usar LDAPS do cliente para o servidor Azure Multi-Factor Authentication, um certificado SSL deve ser instalado no servidor que está executando o servidor. Clique no botão Procurar... ao lado da caixa do certificado SSL e selecione o certificado instalado que será usado para a conexão segura.
5. Clique no botão Adicionar...
6. Na caixa de diálogo Adicionar cliente LDAP, digite o endereço IP do dispositivo, servidor ou aplicativo que fará a autenticação no servidor e um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
7. Marque a caixa correspondente a Requer usuário Azure Multi-Factor Authentication se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado para o servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.
8. Você pode repetir as etapas 5 a 7 para adicionar mais clientes LDAP.
9. Quando o Azure multi-Factor Authentication é configurado para receber autenticações LDAP, ele deve fazer o proxy dessas autenticações para o diretório LDAP. Portanto, o guia Destino exibe apenas uma única opção esmaecida para usar um destino LDAP. Para configurar a conexão do diretório LDAP, clique no ícone de integração de diretórios.
10. Na guia Configurações, selecione o botão de opção Usar configuração de LDAP específica.
11. Clique no botão Editar...
12. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao diretório LDAP. As descrições dos campos estão incluídas na tabela abaixo. Observação: essas informações também estão incluídas no arquivo de ajuda do servidor Azure Multi-Factor Authentication. ![Integração de diretórios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Teste a conexão LDAP clicando no botão Testar.
14. Se o teste de conexão LDAP foi bem-sucedido, clique no botão OK.
15. Clique na guia Filtros. O servidor está pré-configurado para carregar contêineres, usuários e grupos de segurança do Active Directory. Se estiver se associando a um diretório LDAP diferente, provavelmente será necessário editar os filtros exibidos. Clique no link Ajuda para saber mais sobre filtros.
16. Clique na guia Atributos. O servidor está pré-configurado para mapear os atributos do Active Directory.
17. Se estiver se associando a um diretório LDAP diferente ou para alterar os mapeamentos de atributos pré-configurados, clique no botão Editar....
18. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para seu diretório. Os nomes de atributo podem ser digitados ou selecionados clicando no botão ... ao lado de cada campo.
19. Clique no link Ajuda para saber mais sobre atributos.
20. Clique no botão OK.
21. Em seguida, clique no ícone de Configurações da Empresa e selecione a guia Resolução de Nome de Usuário. 22. Se estiver se conectando ao Active Directory de um servidor ingressado em domínio, você deve poder deixar o botão Usar os identificadores de segurança do Windows (SIDs) para nomes de usuário correspondentes selecionado. Selecione o botão de opção Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário. Quando selecionado, o servidor Azure Multi-Factor Authentication tentará resolver cada nome de usuário para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP será executada nos atributos de nome de usuário definidos na guia Integração do Diretório -> Atributos. Quando um usuário é autenticado, o nome de usuário será resolvido para o identificador exclusivo no diretório LDAP e o identificador exclusivo será usado para corresponder ao usuário no arquivo de dados do Azure Multi-Factor Authentication. Isso permite comparações de maiúsculas e minúsculas, bem como formatos de nome de usuário curtos e longos. Isso conclui a configuração do servidor Azure Multi-Factor Authentication. O servidor agora está escutando nas portas configuradas para solicitações de acesso LDAP dos clientes configurados e definido como proxy dessas solicitações ao diretório LDAP para autenticação.


## Configuração do cliente LDAP

Para configurar o cliente LDAP, use as diretrizes:

- Configure seu dispositivo, servidor ou aplicativo para autenticar via LDAP para o servidor Azure Multi-Factor Authentication como se fosse seu diretório LDAP. Você deve usar as mesmas configurações que normalmente usa para se conectar diretamente ao diretório LDAP, exceto o nome do servidor ou endereço IP que será o servidor Azure Multi-Factor Authentication.
- Configure o tempo limite LDAP para 30 a 60 segundos, de forma que haja tempo para validar as credenciais do usuário com o diretório LDAP, executar a autenticação de dois fatores, receber a resposta e responder à solicitação de acesso LDAP.
- Se estiver usando LDAPS, o dispositivo ou o servidor que faz as consultas LDAP deve confiar no certificado SSL instalado no servidor Azure Multi-Factor Authentication.

<!---HONumber=AcomDC_0921_2016-->