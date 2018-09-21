---
title: Como configurar write-back de senha para SSPR do Azure AD
description: Usar o Azure AD e o Azure AD Connect para o write-back de senhas para um diretório local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 1ae74f7c43e763962224683954b28e5941136c08
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295811"
---
# <a name="how-to-configure-password-writeback"></a>Instruções: Configurar write-back de senha

É recomendável que você use o recurso de atualização automática do [Azure AD Connect](../hybrid/how-to-connect-install-express.md) ao usar write-back de senha.

As etapas a seguir pressupõem que você já tenha configurado o Azure AD Connect em seu ambiente usando as configurações [Expressas](../hybrid/how-to-connect-install-express.md) ou [Personalizadas](../hybrid/how-to-connect-install-custom.md).

1. Para configurar e habilitar o write-back de senha, entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
4. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global e escolha **Próximo**.
5. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
6. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
   ![Habilitar write-back de senha no Azure AD Connect][Writeback]
7. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
8. Quando você vir a configuração terminar, selecione **Sair**.

Para tarefas comuns de solução de problemas relacionadas ao write-back de senha, consulte a seção [Solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) em nosso artigo de solução de problemas.

## <a name="active-directory-permissions"></a>Permissões do Active Directory

A conta especificada no utilitário do Azure AD Connect deve ter os seguintes itens configurados para estar no escopo para SSPR:

* **Redefinir senha** 
* **Alterar senha** 
* **Permissões de gravação** em `lockoutTime`
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** em:
   * O objeto raiz de *cada domínio* na floresta
   * As unidades organizacionais (OUs) do usuário que você deseja que estejam no escopo para SSPR

Se não tiver certeza sobre a qual conta a conta descrita acima se refere, abra a interface do usuário da configuração do Azure Active Directory Connect e selecione a opção **Exibir configuração atual**. A conta à qual você precisa adicionar a permissão é listada em **Diretórios Sincronizados**.

Se você definir essas permissões, a conta de serviço de MA de cada floresta poderá gerenciar senhas em nome das contas de usuário nessa floresta. 

> [!IMPORTANT]
> Se você não atribuir essas permissões, mesmo que o write-back pareça estar configurado corretamente, os usuários verão erros ao tentar gerenciar suas senhas locais na nuvem.
>

> [!NOTE]
> Pode levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.
>

Para configurar as permissões apropriadas para que ocorra o write-back de senha, conclua as etapas a seguir:

1. Abra Usuários e Computadores do Active Directory com uma conta que tem as permissões de administração de domínio apropriadas.
2. No menu **Exibir**, verifique se a opção **Recursos avançados** está ativada.
3. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha **Propriedades** > **Segurança** > **Avançado**.
4. Na guia **Permissões**, selecione **Adicionar**.
5. Selecione a conta à qual as permissões estão sendo aplicadas (na configuração do Azure AD Connect).
6. Na lista suspensa **Aplica-se a**, selecione os objetos **Usuário descendente**.
7. Em **Permissões**, marque as caixas para o seguinte:
    * **Redefinir senha**
    * **Alterar senha**
    * **Gravar lockoutTime**
    * **Gravar pwdLastSet**
8. Selecione **Aplicar/OK** para aplicar as alterações e sair das caixas de diálogo abertas.

## <a name="next-steps"></a>Próximas etapas

[O que é write-back de senha?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Habilitar write-back de senha no Azure AD Connect"
