<properties 
	pageTitle="Servidor Azure Multi-Factor Authentication e autenticação RADIUS" 
	description="Esta é a página do Azure Multi-Factor Authentication que auxiliará na implantação de autenticação RADIUS e do servidor Azure Multi-Factor Authentication." 
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



# Servidor Azure Multi-Factor Authentication e autenticação RADIUS

A seção Autenticação RADIUS permite habilitar e configurar a autenticação RADIUS para o servidor Azure Multi-Factor Authentication. RADIUS é um protocolo padrão para aceitar solicitações de autenticação e processar essas solicitações. O servidor Multi-Factor Authentication do Azure atua como um servidor RADIUS e é inserido entre o cliente RADIUS (por exemplo, o dispositivo VPN) e o destino de autenticação, que pode ser o Active Directory (AD), um diretório LDAP ou outro servidor RADIUS, para adicionar o Azure Multi-Factor Authentication. Para o Azure Multi-Factor Authentication funcionar, você deve configurar o servidor Azure Multi-Factor Authentication para que ele possa se comunicar com os servidores clientes e o destino de autenticação. O servidor Azure Multi-Factor Authentication aceita solicitações de um cliente RADIUS, valida as credenciais usando o destino de autenticação, adiciona o Azure Multi-Factor Authentication e envia uma resposta ao cliente RADIUS. A autenticação completa só terá êxito se a autenticação primária e o Azure Multi-Factor Authentication tiverem êxito.

>[AZURE.NOTE]
O servidor MFA só dá suporte a PAP (protocolo de autenticação de senha) e o MSCHAPv2 (Microsoft’s Challenge Handshake Authentication Protocol) de protocolos de RADIUS agindo como um servidor RADIUS. Outros protocolos, como o EAP (protocolo de autenticação extensível) podem ser usados quando o servidor MFA atua como um proxy RADIUS para outro servidor RADIUS que dá suporte a esse protocolo, como Microsoft NPS. </br> Ao usar outros protocolos nessa configuração, os tokens SMS e OATH unidirecionais não funcionarão, pois o servidor MFA não é capaz de iniciar uma resposta bem-sucedida de Challenge RADIUS usando esse protocolo.


![Autenticação Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Configuração da autenticação RADIUS

Para configurar a autenticação RADIUS, instale o servidor Azure Multi-Factor Authentication em um servidor Windows. Se você tiver um ambiente do Active Directory, o servidor deve ser associado ao domínio dentro da rede. Use o procedimento a seguir para configurar o servidor Azure Multi-Factor Authentication:

1. No servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação RADIUS no menu esquerdo.
2. Marque a caixa de seleção Habilitar autenticação RADIUS.
3. Na guia Clientes, altere as portas de autenticação e de contabilização se o serviço RADIUS do Azure Multi-Factor Authentication tiver que se associar às portas não padrão para escutar as solicitações RADIUS dos clientes que serão configurados.
4. Clique no botão Adicionar...
5. Na caixa de diálogo Adicionar cliente RADIUS, digite o endereço IP do dispositivo/servidor que fará a autenticação para o servidor Azure Multi-Factor Authentication, um nome de aplicativo (opcional) e um segredo compartilhado. O segredo compartilhado deve ser o mesmo tanto no servidor Azure Multi-Factor Authentication quanto no dispositivo/servidor. O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
6. Marque a caixa correspondente do usuário Exigir Multi-Factor Authentication se todos os usuários tiverem sido ou vierem a ser importados para o servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.
7. Marque a caixa Habilitar token OATH de fallback se os usuários forem usar o aplicativo móvel Azure Multi-Factor Authentication e você desejar usar senhas OATH como autenticação de fallback para a notificação de chamada, SMS ou push fora da banda.
8. Clique no botão OK.
9. Você pode repetir as etapas de 4 a 8 para adicionar clientes RADIUS adicionais.
10. Clique na guia Destino.
11. Se o servidor Azure Multi-Factor Authentication estiver instalado em um servidor ingressado em um domínio em ambiente do Active Directory, selecione o domínio do Windows.
12. Se os usuários forem autenticados em um diretório LDAP, selecione Associação LDAP. Ao usar a associação LDAP, clique no ícone de integração de diretórios e edite a configuração de LDAP na guia Configurações para que o servidor possa se associar ao diretório. As instruções para configurar o LDAP podem ser encontradas no guia de configuração de Proxy LDAP. 
13. Se os usuários forem autenticados em outro servidor RADIUS, selecione servidores RADIUS.
14. Configure o servidor para o qual o servidor irá enviar as solicitações RADIUS por proxy clicando no botão Adicionar....
15. Na caixa de diálogo Adicionar servidor RADIUS, digite o endereço IP do servidor RADIUS e um segredo compartilhado. O segredo compartilhado deve ser o mesmo tanto no servidor Azure Multi-Factor Authentication quanto no servidor RADIUS. Altere a porta de autenticação e a porta de contabilização se forem usadas portas diferentes pelo servidor RADIUS.
16. Clique no botão OK. 
17. Você deve adicionar o Azure Multi-Factor Authentication como um cliente RADIUS no outro servidor RADIUS para que ele processe as solicitações de acesso enviadas do servidor Azure Multi-Factor Authentication. Você deve usar o mesmo segredo compartilhado configurado no servidor Azure Multi-Factor Authentication.
18. Você pode repetir esta etapa para adicionar servidores RADIUS adicionais e configurar a ordem em que o servidor deve chamá-los com os botões Mover para Cima e Mover para Baixo. Isso conclui a configuração do servidor Azure Multi-Factor Authentication. O servidor agora está escutando nas portas configuradas solicitações de acesso RADIUS dos clientes configurados.   


## Configuração do cliente RADIUS

Para configurar o cliente RADIUS, use as diretrizes:

- Configure o dispositivo/servidor para autenticar via RADIUS para o endereço IP do servidor Azure Multi-Factor Authentication, que atuará como o servidor RADIUS. 
- Use o mesmo segredo compartilhado que foi configurado acima. 
- Configure o tempo limite RADIUS para 30 a 60 segundos, de forma que haja tempo para validar as credenciais do usuário, executar a autenticação multifator, receber a resposta e responder à solicitação de acesso RADIUS.

<!---HONumber=AcomDC_0518_2016-->