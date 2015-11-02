<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Guia de Solução de Problemas | Microsoft Azure"
	description="Guia de solução de problemas para os Serviços de Domínio do AD do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os Serviços de Domínio do AD do Azure.


### Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure
Se você encontrar uma situação em que um ou mais usuários em seu locatário do AD do Azure não conseguem entrar no domínio gerenciado e recém-criado, execute as seguintes etapas de solução de problemas:

- Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.

- Verifique se a conta de usuário afetada não é uma conta externa no locatário do AD do Azure. Os exemplos de contas externas incluem as contas da Microsoft (por exemplo, 'joão@live.com') ou as contas de usuário de um diretório externo do AD do Azure. Como os Serviços de Domínio do AD do Azure não têm credenciais para tais contas de usuário, esses usuários não podem entrar no domínio gerenciado.

- **Contas sincronizadas:** se as contas de usuário afetadas forem sincronizadas a partir de um diretório local, verifique se as seguintes etapas são seguidas:
    - Você implantou ou atualizou para a versão GA do Azure AD Connect. As versões mais antigas não irão sincronizar os hashes de credenciais necessários para a autenticação Kerberos/NTLM.
    - Você criou a chave de registro necessária para habilitar a sincronização das credenciais herdadas para o AD do Azure.
    - Depois de criar a chave de registro acima mencionada no servidor que executa o Azure AD Connect, force o AD do Azure a executar uma sincronização completa, conforme descrito no documento.
    - Dependendo do tamanho do diretório, pode levar algum tempo para as contas de usuário e os hashes de credenciais ficarem disponíveis nos Serviços de Domínio do AD do Azure. Aguarde o tempo suficiente antes de tentar novamente a autenticação (depende do tamanho do seu diretório - de algumas horas a um dia ou dois para os diretórios grandes).

- **Contas somente em nuvem**: se a conta de usuário afetado for uma conta de usuário somente em nuvem, verifique se o usuário alterou sua senha depois que você habilitou os Serviços de Domínio do AD do Azure. Essa etapa faz com que os hashes de credenciais necessários para os Serviços de Domínio do AD do Azure sejam gerados.


### Entre em contato
Se você tiver problemas com seu domínio gerenciado, verifique se as etapas descritas neste guia de solução de problemas resolverem o problema. Se você ainda tiver problemas, fique à vontade para falar conosco em:

- [Canal de Voz do Usuário do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory). Verifique se você prefixou sua pergunta com as palavras **'AADDS'** para falar conosco.
- Você também pode enviar um email para [Comentários de Serviços de Domínio do AD do Azure](mailto:aaddsfb@microsoft.com)

<!---HONumber=Oct15_HO4-->