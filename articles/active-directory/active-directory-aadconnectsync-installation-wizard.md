<properties
	pageTitle="Sincronização do Azure AD Connect: executar o assistente de instalação pela segunda vez | Microsoft Azure"
	description="Explica como o assistente de instalação funciona na segunda vez que é executado."
	keywords="O assistente de instalação do Azure AD Connect permite configurar as configurações de manutenção da segunda vez que é executado"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="andkjell"/>


# Sincronização do Azure AD Connect: executar o assistente de instalação pela segunda vez
Na primeira vez que você executar o assistente de instalação do Azure AD Connect, ele explica como configurar a instalação. Se você executar o assistente de instalação novamente, ele oferecerá opções para manutenção.

Você pode encontrar o assistente de instalação no menu Iniciar chamado **Azure Connect AD**.

![Menu Iniciar](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Ao iniciar o assistente de instalação, você verá uma página com as seguintes opções:

![Página com uma lista de tarefas adicionais](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se você tiver instalado o ADFS com o Azure AD Connect, terá ainda mais opções. As opções adicionais disponíveis para ADFS estão documentadas em [Gerenciamento do ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Selecione uma das tarefas e clique em **Avançar** para continuar.

> [AZURE.IMPORTANT] Enquanto o assistente de instalação estiver aberto, todas as operações no mecanismo de sincronização serão suspensas. Lembre-se de fechar o assistente de instalação assim que concluir as alterações de configuração.

## Exibir a configuração atual
Essa opção fornece uma visão rápida das opções configuradas no momento.

![Página com uma lista de todas as opções e seus estados](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Clique em **Anterior** para voltar. Ao selecionar **Sair**, o assistente de instalação será fechado.

## Personalizar opções de sincronização
Esta opção é usada para fazer alterações na configuração de sincronização. Você verá um subconjunto das opções do caminho de instalação de configuração personalizada. Isso será mostrado mesmo se tiver usado a instalação expressa inicialmente.

- [Adicionar mais diretórios](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para remover um diretório, consulte [Excluir um Conector](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Alterar a filtragem de domínio e de unidade organizacional](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Remova a filtragem de grupo.
- [Alterar recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features).

As outras opções de instalação inicial não podem ser alteradas e não estão disponíveis. Isso inclui:

- Altere o atributo a ser usado para userPrincipalName e sourceAnchor.
- Altere o método de ingresso para objetos de uma floresta diferente.
- Habilite a filtragem baseada em grupo.

## Atualizar esquema de diretório
Essa opção é usada se você alterou o esquema em uma das suas florestas do AD DS locais. Por exemplo você pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Nesse caso, você precisa instruir o Azure AD Connect para ler o esquema novamente do AD DS e atualizar seu cache. Isso regenerará também as Regras de Sincronização. Se você adicionar o esquema do Exchange, por exemplo, as Regras de Sincronização para o Exchange serão adicionadas à configuração.

Quando você seleciona essa opção, todos os diretórios na sua configuração são listados. Você pode manter a configuração padrão e atualizar todas as florestas ou desmarcar algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## Configurar modo de preparo
Essa opção permite habilitar e desabilitar o modo de preparo no servidor. Encontre mais informações sobre o modo de preparo e como ele é usado em [Operações](active-directory-aadconnectsync-operations.md#staging-mode).

A opção mostrará se o preparo está habilitado ou desabilitado no momento: ![Opção que também está mostrando o estado atual do modo de preparo](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione essa opção e marque ou desmarque a caixa de seleção. ![Opção que também está mostrando o estado atual do modo de preparo](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## Alterar a entrada do usuário
Esta opção permite mudar da sincronização de senha para federação ou o oposto. Você não pode alterar para **não configurar**.

Para obter mais informações sobre essa opção, consulte [entrada do usuário](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).


## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0518_2016-->