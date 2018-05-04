---
title: Fazer backup e recuperar informações com o Microsoft Authenticator - Azure | Microsoft Docs
description: Saiba como fazer backup e recuperar as credenciais da sua conta usando o aplicativo Microsoft Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: 0b76d2debb9814188e5551252e52bd0dae38eb50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais de conta com o aplicativo Microsoft Authenticator
**Aplica-se a:**

- Dispositivos iOS

O aplicativo Microsoft Authenticator faz backup das credenciais da sua conta e das configurações de aplicativos relacionadas, como a ordem de suas contas, na nuvem. Após o backup, também será possível usar o aplicativo para recuperar as informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

>[!IMPORTANT]
> É necessário ter uma conta pessoal da Microsoft e uma conta do iCloud para cada local de armazenamento de backup. Mas, dentro desse local de armazenamento será possível fazer o backup de várias contas. Por exemplo, você pode ter uma conta pessoal, uma conta de estudante e uma conta de terceiros como Facebook, Google, e assim por diante.<br><br>Somente as credenciais da conta são armazenadas, o que inclui o nome de usuário e o código de verificação de conta de 8 dígitos necessários para provar a identidade com o aplicativo Microsoft Authenticator. Não armazenamos outras informações associadas às contas, incluindo emails ou arquivos. Também não associamos ou compartilhamos as contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, o administrador de TI não receberá informações sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup das credenciais da conta
Antes de fazer backup das credenciais, será necessário ter as duas contas a seguir:

- Uma [conta pessoal da Microsoft](https://account.microsoft.com/account) para agir como uma conta de recuperação.

- Uma [conta do iCloud](https://www.icloud.com/) para o local de armazenamento real. 

A exigência de entrar em ambas as contas conjuntamente proporciona segurança mais forte para as informações de backup.

**Para ativar o Backup na nuvem**
-   No seu dispositivo iOS, selecione **Configurações**, selecione **Backup** e, em seguida, ative **Backup automático**.

    O backup das credenciais da sua conta é feito para a conta do iCloud.

    ![Tela de configurações do iOS, mostrando o local das configurações de backup automático](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo
É possível recuperar as credenciais da conta do iCloud, usando a mesma conta de recuperação da Microsoft que você configurou ao fazer o backup das informações.

**Para recuperar as informações**
1.  No dispositivo iOS, abra o aplicativo Microsoft Authenticator e selecione **Iniciar a recuperação** na parte inferior da tela.

    ![Aplicativo Microsoft Authenticator, mostrando onde clicar em Iniciar recuperação](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Entre na conta de recuperação, usando a mesma conta pessoal da Microsoft utilizada durante o processo de backup.

    As credenciais da conta são recuperadas para o novo dispositivo.

Após concluir a recuperação, você poderá notar que os códigos de verificação de conta pessoal da Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam durante a autenticação usando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que exigem mais verificação
Se você usar notificações por push com as contas pessoais, corporativa ou de estudante, um alerta será exibido na tela informando que será necessário fornecer uma verificação adicional para poder recuperar as informações. Como as notificações por push exigem o uso de uma credencial vinculada ao dispositivo específico e nunca enviada pela rede, você deverá provar sua identidade antes que a credencial seja criada no dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo a senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deverá digitalizar um código QR fornecido pelo provedor de conta.

**Para fornecer verificação adicional para contas pessoais**
1.  Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Selecione **Entrar para recuperar**, digite a senha e confirme o endereço de email ou o número de telefone como verificação adicional.

    ![Aplicativo Microsoft Authenticator, permitindo que você insira as informações de entrada](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Para fornecer verificação adicional para contas corporativas ou de estudante**
1.  Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Selecione **Digitalizar código QR para recuperar** e, em seguida, digitalize o código QR fornecido pelo administrador.

    ![Aplicativo Microsoft Authenticator, permitindo que você digitalize o código QR](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>Solução de problemas de backup e recuperação
Há alguns motivos pelos quais o backup pode não estar disponível:

-   **Alterar sistemas operacionais.** O backup é armazenado na opção de armazenamento em nuvem fornecida pelo sistema operacional do telefone, o que significa que o backup não estará disponível se você alternar entre Android e iOS. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

-   **Problemas de rede ou senha.** Certifique-se de estar conectado a uma rede e conectado à conta do iCloud usando o mesmo AppleID utilizado no último iPhone.

-   **Exclusão acidental.** É possível que você tenha excluído a conta de backup do dispositivo anterior ou ao gerenciar a conta de armazenamento em nuvem. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

-   **Contas existentes do Microsoft Authenticator.** Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar as contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, antes de recuperar o backup você deverá remover todas as informações de conta existentes das contas existentes configuradas no aplicativo Authenticator.

## <a name="next-steps"></a>Próximas etapas
Agora que você fez backup e recuperou as credenciais da conta para o novo dispositivo, continue usando o aplicativo Microsoft Authenticator para verificar a identidade.

## <a name="related-topics"></a>Tópicos relacionados
- [Introdução ao aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md)  

- [Conectar com seu telefone](microsoft-authenticator-app-phone-signin-faq.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- [Autenticação multifator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)