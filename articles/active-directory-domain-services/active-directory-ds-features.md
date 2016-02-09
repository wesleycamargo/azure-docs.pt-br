<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Recursos | Microsoft Azure"
	description="Recursos dos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure* (Visualização)*

## Recursos
Os seguintes recursos estão disponíveis na versão de visualização dos Serviços de Domínio do AD do Azure.

- **Experiência de implantação simples:** com apenas alguns cliques você pode habilitar os Serviços de Domínio do AD do Azure para seu locatário do AD do Azure. Independentemente de seu locatário do AD do Azure ser um locatário de nuvem ou sincronizado com o diretório local, seu domínio gerenciado pode ser provisionado rapidamente.

- **Suporte para ingresso no domínio:** você pode facilmente ingressar computadores no domínio na rede virtual do Azure na qual os Serviços de Domínio do AD do Azure estão disponíveis. A experiência de ingresso em sistemas operacionais cliente e servidor do Windows funciona perfeitamente nos domínios atendidos pelos Serviços de Domínio do AD do Azure. Você também pode usar as ferramentas do ingresso no domínio automatizado com relação a esses domínios.

- **Uma instância de domínio por diretório do AD do Azure:** você pode criar um único domínio do Active Directory para cada diretório do AD do Azure.

- **Criar domínios com nomes personalizados:** você pode criar domínios com nomes personalizados (p. ex., contoso.local) usando os Serviços de Domínio do AD do Azure. Isso inclui nomes verificados e nomes de domínio não verificados. Opcionalmente, você também pode criar um domínio com o sufixo de domínio interno (isto é, *.onmicrosoft.com) que é oferecido pelo seu diretório do AD do Azure.

- **Integrado ao AD do Azure:** não é necessário configurar nem gerenciar a replicação para os Serviços de Domínio do AD do Azure. Contas de usuário, associações de grupo e credenciais de usuário (senhas) do seu diretório do AD do Azure estão automaticamente disponíveis nos Serviços de domínio do AD do Azure. Novos usuários, grupos ou alterações a atributos que ocorrem em seu locatário do AD do Azure ou em seu diretório local são automaticamente sincronizados com os Serviços de Domínio do AD do Azure.

- **Autenticação NTLM e Kerberos:** com suporte para autenticação NTLM e Kerberos, você pode implantar aplicativos que dependem da autenticação Integrada do Windows.

- **Usar suas credenciais corporativas/senhas:** senhas de usuários em seu locatário do AD do Azure funcionam com os Serviços de Domínio do AD do Azure. Isso significa que os usuários em sua organização podem usar suas credenciais corporativas no domínio – para computadores ingressando no domínio, fazer logon interativamente ou pela área de trabalho remota, autenticar com o controlador de domínio, etc.

- **Suporte de associação LDAP e leitura LDAP:** você pode usar aplicativos que dependem de associações LDAP para autenticar usuários em domínios atendidos pelos Serviços de Domínio do AD do Azure. Além disso, os aplicativos que usam operações de leitura LDAP para consultar atributos de usuário/computador a partir do diretório também podem funcionar nos Serviços de Domínio do AD do Azure.

- **Política de grupo:** você pode aproveitar um único objeto de política de grupo (GPO) para cada um dos usuários e contêineres de computadores, a fim de impor a conformidade com políticas de segurança necessárias para contas de usuário, bem como computadores ingressados no domínio.

- **Disponível em várias regiões do Azure:** consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do AD do Azure estão disponíveis.

- **Alta disponibilidade:** os Serviços de Domínio do AD do Azure oferecem alta disponibilidade para seu domínio. Garante maior tempo de atividade de serviço e resiliência a falhas. O monitoramento da integridade interna oferece a correção automatizada de falhas gerando novas instâncias para substituir instâncias com falha e fornecer um serviço contínuo para seu domínio.

- **Usar ferramentas de gerenciamento conhecidas:** você pode usar ferramentas de gerenciamento conhecidas do Active Directory do Windows Server, como o Centro Administrativo do Active Directory ou o Active Directory PowerShell, para administrar domínios fornecidos pelos Serviços de Domínio do AD do Azure.

<!---HONumber=AcomDC_0128_2016-->