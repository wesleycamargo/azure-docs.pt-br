<properties 
	pageTitle="Gateway de Área de Trabalho Remota e Servidor Azure Multi-Factor Authentication usando RADIUS" 
	description="Esta é a página do Azure Multi-Factor Authentication que ajudará na implantação do Gateway de Área de Trabalho Remota e do Servidor Azure Multi-Factor Authentication usando RADIUS." 
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

# Gateway de Área de Trabalho Remota e Servidor Azure Multi-Factor Authentication usando RADIUS

Em muitos casos, o Gateway de Área de Trabalho Remota usa o NPS local para autenticar usuários. Este documento descreve como rotear a solicitação RADIUS do Gateway de Área de Trabalho Remota (por meio do NPS local) até o Servidor Multi-Factor Authentication.

O Servidor Multi-Factor Authentication deve estar instalado em um servidor separado, que enviará a solicitação RADIUS de volta ao NPS no Servidor do Gateway de Área de Trabalho Remota. Depois que o NPS validar o nome de usuário e a senha, ele retornará uma resposta ao Servidor Multi-Factor Authentication que executa o segundo fator da autenticação antes de retornar um resultado ao gateway.





## Configurar o Gateway de Área de Trabalho Remota

O Gateway de Área de Trabalho Remota deve ser configurado para enviar autenticação RADIUS a um Servidor Multi-Factor Authentication. Depois que o Gateway de Área de Trabalho Remota tiver sido instalado, configurado e estiver funcionando, acesse as propriedades do Gateway de Área de Trabalho Remota. Vá para a guia Repositório RD CAP e altere-o para usar um servidor Central executando o NPS em vez de um servidor Local executando o NPS. Adicione um ou mais Servidores Azure Multi-Factor Authentication como servidores RADIUS e especifique um segredo compartilhado para cada servidor.





## Configurar o NPS

O Gateway de Área de Trabalho Remota usa o NPS para enviar a solicitação RADIUS ao Azure Multi-Factor Authentication. Um tempo limite deve ser alterado para evitar que o tempo limite do Gateway de Área de Trabalho Remota se esgote antes da conclusão da autenticação multifator. Use o procedimento a seguir para configurar o NPS.

1. No NPS, expanda o menu Clientes e Servidor do RADIUS na coluna esquerda e clique em Grupos de Servidores Remotos RADIUS. Acesse as propriedades do GRUPO DE SERVIDORES DO GATEWAY TS. Edite os Servidores RADIUS exibidos e vá para a guia Balanceamento de Carga. Altere o "Tempo de espera sem resposta antes que uma solicitação seja ignorada (em segundos)" e o "Tempo de espera entre solicitações para identificar o servidor como não disponível (em segundos)" para 30 a 60 segundos. Clique na guia Autenticação/Conta e verifique se as portas RADIUS especificadas correspondem às portas nas quais o Servidor Multi-Factor Authentication escutará.
2. O NPS também deve ser configurado para receber autenticações RADIUS que voltam do Servidor Azure Multi-Factor Authentication. Clique em Clientes RADIUS no menu esquerdo. Adicione o Servidor Azure Multi-Factor Authentication como um cliente RADIUS. Escolha um nome amigável e especifique um segredo compartilhado.
3. Expanda a seção Políticas no painel de navegação esquerdo e clique em Políticas de Solicitação de Conexão. Você deve encontrar uma Política de Solicitação de Conexão chamada POLÍTICA DE AUTORIZAÇÃO DO GATEWAY TS que foi criada quando o Gateway de Área de Trabalho Remota foi configurado. Essa política encaminha as solicitações RADIUS para o Servidor Multi-Factor Authentication.
4. Copie essa política para criar uma nova. Na nova política, adicione uma condição que corresponda o Nome Amigável do Cliente ao Nome amigável definido na etapa 2 acima para o cliente RADIUS do Servidor Azure Multi-Factor Authentication. Altere o Provedor de Autenticação para Computador Local. Essa política garante que quando um solicitação RADIUS é recebida do Servidor Azure Multi-Factor Authentication, a autenticação ocorra localmente em vez de enviar uma solicitação RADIUS de volta ao Servidor Azure Multi-Factor Authentication, que resulta em uma condição de loop. Para evitar a condição de loop, essa nova política deve ser ordenada ACIMA da política original que é encaminhada para o Servidor Multi-Factor Authentication.

## Configurar o Azure Multi-Factor Authentication


--------------------------------------------------------------------------------



O Servidor Azure Multi-Factor Authentication é configurado como um proxy RADIUS entre o Gateway de Área de Trabalho Remota e o NPS. Ele deve ser instalado em um servidor de domínio que esteja separado do servidor do Gateway de Área de Trabalho Remota. Use o procedimento a seguir para configurar o Servidor Azure Multi-Factor Authentication.

1. Abra o Servidor Azure Multi-Factor Authentication e clique no ícone Autenticação RADIUS. Marque a caixa de seleção Habilitar autenticação RADIUS.
2. Na guia Clientes, verifique se as portas correspondem ao que foi configurado no NPS e clique no botão Adicionar. Adicione o endereço IP, o nome do aplicativo (opcional) e um segredo compartilhado do servidor do Gateway de Área de Trabalho Remota. O segredo compartilhado deve ser o mesmo no Servidor Azure Multi-Factor Authentication e no Gateway de Área de Trabalho Remota.
3. Clique na guia Destino e escolha o botão de opção Servidores RADIUS.
4. Clique no botão Adicionar... Insira o endereço IP, o segredo compartilhado e as portas do servidor NPS. A não ser que esteja usando um NPS central, o cliente RADIUS e o destino de RADIUS serão iguais. O segredo compartilhado deve corresponder ao configurado na seção do cliente RADIUS no servidor NPS. 

![Autenticação Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=AcomDC_0518_2016-->