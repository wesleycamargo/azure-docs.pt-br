<properties 
	pageTitle="Autenticação do Windows e Servidor Azure Multi-Factor Authentication"
	description="Esta é a página do Azure Multi-Factor Authentication que auxiliará na implantação da Autenticação do Windows e do Servidor Azure Multi-Factor Authentication."
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

# Autenticação do Windows e Servidor Azure Multi-Factor Authentication

A seção Autenticação do Windows permite que o administrador habilite e configure a autenticação do Windows para um ou mais aplicativos. Veja a seguir uma lista a ser lembrada antes de configurar a Autenticação do Windows.

-  A reinicialização é necessária antes que o Azure Multi-Factor Authentication para Serviços de Terminal entre em vigor.
-  Se a opção 'Exigir correspondência de usuário do Azure Multi-Factor Authentication' for marcada e você não estiver na lista de usuários, não será possível fazer logon no computador após a reinicialização.
-  IPs confiáveis dependem de o aplicativo poder fornecer o IP do cliente com a autenticação. Atualmente, apenas os Serviços de Terminal têm suporte.







>[AZURE.NOTE]Esse recurso não tem suporte para proteger os Serviços de Terminal no Windows Server 2012 R2.




## Para proteger um aplicativo com a Autenticação do Windows, use o procedimento a seguir.

1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação do Windows. ![Autenticação do Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Marque a caixa de seleção Habilitar Autenticação do Windows. Por padrão, essa caixa está desmarcada.
3. A guia Aplicativos permite que o administrador configure um ou mais aplicativos para Autenticação do Windows.
4. Selecione um servidor ou aplicativo — especifique se o servidor/aplicativo está habilitado. Clique em OK.
5. Clique no botão Adicionar...
6. A guia IPs Confiáveis permite que você ignore as sessões do Azure Multi-Factor Authentication para Windows originadas de IPs específicos. Por exemplo, se os funcionários usarem o aplicativo no escritório e em casa, você pode decidir que não deseja que seus telefones toquem para o Azure Multi-Factor Authentication enquanto estiverem no escritório. Para isso, você especifica a sub-rede do escritório como entrada de IPs Confiáveis.
7. Clique no botão Adicionar...
8. Selecione IP Único se desejar ignorar um único endereço IP.
9. Selecione o Intervalo de IP se desejar ignorar um intervalo inteiro de IP. Exemplo 10.63.193.1-10.63.193.100.
10. Selecione Sub-rede se desejar especificar um intervalo de IPs usando a notação de sub-rede. Insira o IP inicial da sub-rede e escolha a máscara de rede adequada na lista suspensa.
11. Clique no botão OK.

<!---HONumber=AcomDC_0921_2016-->