<properties
   pageTitle="Sincronização do Azure AD Connect: impedir exclusões acidentais | Microsoft Azure"
   description="Este tópico descreve o recurso de prevenção contra exclusões acidentais (que impede exclusões acidentais) no Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: impedir exclusões acidentais
Este tópico descreve o recurso de prevenção contra exclusões acidentais (que impede exclusões acidentais) no Azure AD Connect.

Ao instalar o Azure AD Connect, o recurso para impedir exclusões acidentais será habilitado por padrão e configurado para não permitir uma exportação com mais de 500 exclusões. Esse recurso destina-se a proteger você contra alterações acidentais na configuração e alterações no seu diretório local, o que poderia afetar um grande número de usuários.

O valor padrão de 500 objetos pode ser alterado com o PowerShell, usando `Enable-ADSyncExportDeletionThreshold`. Você deve configurar esse valor para ajustar o tamanho da sua organização. Como o agendador de sincronização será executado a cada 3 horas, o valor será o número de exclusões visto em 3 horas.

Com esse recurso habilitado, se houver muitas exclusões preparadas para exportação no Azure AD, a exportação não continuará e você receberá um email como este:

![Email de exclusões acidentais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

Caso isso não seja esperado, investigue e tome medidas corretivas. Para ver quais objetos estão prestes a ser excluídos, siga este procedimento:

1. Inicie o **Serviço de Sincronização** no Menu Iniciar.
2. Acesse **Conectores**.
3. Selecione o Conector com o tipo **Active Directory do Azure**.
4. Em **Ações**, à direita, selecione **Pesquisar Espaço do Conector**.
5. No pop-up em **Escopo**, selecione **Desconectado desde** e escolha um horário no passado. Clique em **Pesquisar**. Isso fornecerá uma exibição de todos os objetos a serem excluídos. Ao clicar em cada item, você poderá obter informações adicionais sobre o objeto. Você também pode clicar em **Configuração da Coluna** para adicionar outros atributos para exibição na grade.

![Pesquisar Espaço do Conector](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se todas as exclusões forem desejadas, siga este procedimento:

1. Para desabilitar temporariamente essa proteção e permitir que essas exclusões ocorram, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold` Quando solicitado, forneça uma conta e senha de Administrador Global do AD do Azure. ![Credenciais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Com o Conector do Active Directory do Azure ainda selecionado, selecione a ação **Executar** e, em seguida, **Exportar**.
3. Para reabilitar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold`

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->