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
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: impedir exclusões acidentais
Este tópico descreve o recurso de prevenção contra exclusões acidentais (que impede exclusões acidentais) no Azure AD Connect.

Ao instalar o Azure AD Connect, o recurso para impedir exclusões acidentais será habilitado por padrão e configurado para não permitir uma exportação com mais de 500 exclusões. Esse recurso destina-se a proteger você contra alterações acidentais na configuração e alterações no seu diretório local, o que poderia afetar um grande número de usuários e outros objetos.

Cenários comuns quando você vê isso incluem:

- Alterações de [filtragem](active-directory-aadconnectsync-configure-filtering.md) em que todo uma [UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) é desmarcado.
- Todos os objetos em uma UO são excluídos.
- Uma UO é renomeada e todos os objetos são considerados fora do escopo de sincronização.

O valor padrão de 500 objetos pode ser alterado com o PowerShell, usando `Enable-ADSyncExportDeletionThreshold`. Você deve configurar esse valor para ajustar o tamanho da sua organização. Como o agendador de sincronização será executado a cada 30 minutos, o valor será o número de exclusões visto em 30 minutos.

Se houver muitas exclusões preparadas para exportação no Azure AD, a exportação não continuará e você receberá um email como este:

![Impedir exclusões de email acidentais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Saudação (contato técnico). No momento, o Serviço de sincronização de identidade detectou que o número de exclusões excedeu o limite configurado de exclusões para (nome da organização). Um total de (número) objetos foram enviados para exclusão nesta execução da sincronização de Identidade. Isso atingiu ou excedeu o valor de limite de exclusão configurado de (número) objetos. Precisamos que você forneça a confirmação de que essas exclusões devem ser processadas antes de continuarmos. Veja Impedindo exclusões acidentais para obter mais informações sobre o erro listado nesta mensagem de email.*

Você também pode ver o status `stopped-deletion-threshold-exceeded` examinando a interface do **Synchronization Service Manager** para o perfil de exportação. ![Impedir exclusões acidentais na interface do usuário do Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

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

<!---HONumber=AcomDC_0629_2016-->