<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Guia de Solução de Problemas | Microsoft Azure"
	description="Guia de solução de problemas para os Serviços de Domínio do AD do Azure"
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

# Serviços de Domínio do AD do Azure *(Visualização)* - Guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os Serviços de Domínio do AD (Active Directory do Azure).


### Não é possível habilitar os Serviços de Domínio do AD do Azure para seu diretório do AD do Azure.
Se você encontrar uma situação na qual tenta habilitar os Serviços de Domínio do AD do Azure para seu diretório e ocorrer uma falha, ou voltar para “Desabilitado”, execute as seguintes etapas de solução de problemas:

- Certifique-se de que você não tenha um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, vamos supor que você tenha um domínio chamado ‘contoso.com’ já disponível na rede virtual selecionada. Na sequência, você tenta habilitar um domínio gerenciado pelos Serviços de Domínio do AD do Azure com o mesmo nome de domínio (isto é, ‘contoso.com’) nessa rede virtual. Você encontrará uma falha ao tentar habilitar os Serviços de Domínio do AD do Azure. Isso ocorre devido a conflitos de nome com o nome de domínio nessa rede virtual. Nessa situação, você deve usar um nome diferente para definir o domínio gerenciado pelos Serviços de Domínio do AD do Azure. Como alternativa, você pode desprovisionar o domínio existente e, em seguida, habilitar os Serviços de Domínio do AD do Azure.

- Verifique se você tem um aplicativo com o nome ‘Sincronização de Serviços de Domínio do AD do Azure’ em seu diretório do AD do Azure. Se esse aplicativo existir, você precisará excluí-lo e, em seguida, reabilitar os Serviços de Domínio do AD do Azure. Execute as seguintes etapas para verificar a presença do aplicativo e excluí-lo, caso ele já exista:

  1. Navegue no **portal de gerenciamento do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. No painel esquerdo, selecione o nó **Active Directory**.
  3. Selecione o locatário do AD do Azure (diretório) para o qual você deseja habilitar os Serviços de Domínio do AD do Azure.
  4. Navegue até a guia **Aplicativos**.
  5. Escolha a opção **Aplicativos que minha empresa possui** na lista suspensa.
  6. Verifique se há um aplicativo chamado **Sincronização dos Serviços de Domínio do AD do Azure**. Se o aplicativo já existir, exclua-o.
  7. Após a exclusão do aplicativo, tente habilitar os Serviços de Domínio do AD do Azure novamente.


### Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure
Se você encontrar uma situação em que um ou mais usuários em seu locatário do AD do Azure não conseguem entrar no domínio gerenciado e recém-criado, execute as seguintes etapas de solução de problemas:

- Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.

- Verifique se a conta de usuário afetada não é uma conta externa no locatário do AD do Azure. Os exemplos de contas externas incluem as contas da Microsoft (por exemplo, 'joão@live.com') ou as contas de usuário de um diretório externo do AD do Azure. Como os Serviços de Domínio do AD do Azure não têm credenciais para tais contas de usuário, esses usuários não podem entrar no domínio gerenciado.

- Certifique-se de que o prefixo do UPN (ou seja, a primeira parte do UPN) da conta do usuário afetado em seu locatário do Azure AD tem menos de 20 caracteres. Por exemplo, para o UPN 'joaocomnomeusuáriorealmentelongo@contoso.com', o prefixo ('joaocomnomeusuáriorealmentelongo') excede 20 caracteres e essa conta não estará disponível no domínio gerenciado dos Serviços de Domínio do Azure AD.

- **Contas sincronizadas:** se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique o seguinte:
    - Você implantou ou atualizou para a [versão recomendada mais recente do Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).
    - Você configurou o Azure AD Connect para [executar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).
    - Dependendo do tamanho do diretório, pode levar algum tempo para as contas de usuário e os hashes de credenciais ficarem disponíveis nos Serviços de Domínio do AD do Azure. Aguarde o tempo suficiente antes de tentar novamente a autenticação (dependendo do tamanho do seu diretório, de algumas horas a um dia ou dois para diretórios grandes).

- **Contas somente em nuvem**: se a conta de usuário afetado for uma conta de usuário somente em nuvem, verifique se o usuário alterou sua senha depois que você habilitou os Serviços de Domínio do Azure AD. Essa etapa faz com que os hashes de credenciais necessários para os Serviços de Domínio do AD do Azure sejam gerados.


### Entre em contato
Se você tiver problemas com seu domínio gerenciado, verifique se as etapas descritas neste guia de solução de problemas resolverem o problema. Se você ainda tiver problemas, fique à vontade para falar conosco em:

- **Email:** você também pode nos enviar um email em [Comentários sobre os Serviços de Domínio do AD do Azure](mailto:aaddsfb@microsoft.com) Inclua a ID do locatário para o diretório do AD do Azure e o nome de domínio que você configurou para os Serviços de Domínio do AAD, para que possamos investigar o problema.
- **[Canal do Active Directory do Azure no User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/): **Coloque as palavras **"AADDS"** antes de sua pergunta para falar conosco.

<!---HONumber=AcomDC_0128_2016-->