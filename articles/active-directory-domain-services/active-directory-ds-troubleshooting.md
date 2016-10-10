<properties
	pageTitle="Azure Active Directory Domain Services: Guia de Solução de Problemas | Microsoft Azure"
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
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services – Guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os Serviços de Domínio do AD (Active Directory do Azure).


### Não é possível habilitar os Serviços de Domínio do AD do Azure para seu diretório do AD do Azure.
Se você tentar habilitar os Azure AD Domain Services para seu diretório e ocorrer uma falha, ou voltar para ‘Desabilitado’, execute as seguintes etapas de solução de problemas:

- Certifique-se de que você não tenha um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, vamos supor que você tenha um domínio chamado ‘contoso.com’ já disponível na rede virtual selecionada. Posteriormente, você tenta habilitar um domínio gerenciado pelos Azure AD Domain Services com o mesmo nome de domínio (isto é, ‘contoso.com’) nessa rede virtual. Você encontra uma falha ao tentar habilitar os Serviços de Domínio do AD do Azure. Essa falha ocorre devido a conflitos de nome com o nome de domínio nessa rede virtual. Nessa situação, você deve usar um nome diferente para definir o domínio gerenciado pelos Serviços de Domínio do AD do Azure. Como alternativa, você pode desprovisionar o domínio existente e, em seguida, habilitar os Serviços de Domínio do AD do Azure.

- Verifique se você tem um aplicativo com o nome ‘Sincronização de Serviços de Domínio do AD do Azure’ em seu diretório do AD do Azure. Se esse aplicativo existir, você precisará excluí-lo e, em seguida, reabilitar os Azure AD Domain Services. Execute as seguintes etapas para verificar a presença do aplicativo e excluí-lo, caso ele já exista:

  1. Navegue até o **portal clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. No painel esquerdo, selecione o nó **Active Directory**.
  3. Selecione o locatário do AD do Azure (diretório) para o qual você deseja habilitar os Serviços de Domínio do AD do Azure.
  4. Navegue até a guia **Aplicativos**.
  5. Escolha a opção **Aplicativos que minha empresa possui** na lista suspensa.
  6. Verifique se há um aplicativo chamado **Sincronização dos Azure AD Domain Services**. Se o aplicativo já existir, exclua-o.
  7. Após a exclusão do aplicativo, tente habilitar os Serviços de Domínio do AD do Azure novamente.


### Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure
Se um ou mais usuários em seu locatário do Azure AD não conseguirem entrar no domínio gerenciado recém-criado, execute as seguintes etapas de solução de problemas:

- Tente entrar usando o formato UPN (por exemplo, "paulousuario@contoso.com") em vez do formato SAMNomeDaConta ('CONTOSO\\paulousuario'). Às vezes, o SAMAccountName pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é o mesmo de outro usuário no domínio gerenciado. O formato UPN é exclusivo, de forma garantida, dentro de um locatário do Azure AD.

> [AZURE.NOTE] Recomendamos usar o formato UPN para entrar no domínio gerenciado do Azure AD Domain Services.

- Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.

- **Contas externas:** verifique se a conta de usuário afetada não é uma conta externa no locatário do Azure AD. Os exemplos de contas externas incluem as contas da Microsoft (por exemplo, 'joão@live.com') ou as contas de usuário de um diretório externo do AD do Azure. Como os Serviços de Domínio do AD do Azure não têm credenciais para tais contas de usuário, esses usuários não podem entrar no domínio gerenciado.

- **Prefixo UPN excessivamente longo**: verifique se o prefixo do UPN (a primeira parte do UPN) da conta do usuário afetado em seu locatário do Azure AD tem menos de 20 caracteres. Por exemplo, para o UPN 'paulocomnomeusuáriorealmentelongo@contoso.com', o prefixo ('paulocomnomeusuáriorealmentelongo') excede 20 caracteres e essa conta não está disponível no domínio gerenciado.

- **Prefixo UPN duplicado:** verifique se outras contas de usuário em seu locatário do Azure AD não têm o mesmo prefixo de UPN (a primeira parte do UPN) que a conta de usuário afetada. Por exemplo, se você tiver duas contas de usuário "joeuser@finance.contoso.com" e "joeuser@engineering.contoso.com", ambos os usuários encontrarão problemas ao se conectar ao domínio gerenciado. Esse problema também poderá ocorrer se uma das contas de usuário for uma conta externa (por exemplo, "joeuser@live.com"). Estamos trabalhando para corrigir esse problema.

- **Contas sincronizadas:** se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique se:
    - Você implantou ou atualizou para a [versão recomendada mais recente do Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Você configurou o Azure AD Connect para [executar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).

    - Dependendo do tamanho do diretório, pode levar algum tempo para as contas de usuário e os hashes de credenciais ficarem disponíveis nos Serviços de Domínio do AD do Azure. Aguarde o tempo suficiente antes de tentar novamente a autenticação (dependendo do tamanho do seu diretório, de algumas horas a um dia ou dois para diretórios grandes).

    - Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o Serviço de sincronização do Microsoft Azure AD. Em seu computador de sincronização, inicie um prompt de comando e execute os seguintes comandos:
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **Contas somente em nuvem**: se a conta de usuário afetado for uma conta de usuário somente em nuvem, verifique se o usuário alterou sua senha depois que você habilitou os Serviços de Domínio do Azure AD. Essa etapa faz com que os hashes de credenciais necessários para os Serviços de Domínio do AD do Azure sejam gerados.


### Entre em contato
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).

<!---HONumber=AcomDC_0928_2016-->