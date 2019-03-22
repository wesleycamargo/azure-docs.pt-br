---
title: 'Tutorial:  Configurando o PHS como backup para o AD FS no Azure AD Connect | Microsoft Docs'
description: Demonstra como ativar a sincronização de hash de senha como um backup e para o AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281bc451a5acf9e4e634a124161a3e8b0734deb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090501"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial:  Configurando o PHS como backup para o AD FS no Azure AD Connect

O tutorial a seguir orientará você pela configuração da sincronização de hash de senha como um backup e failover para o AD FS.  Este documento também demonstrará como habilitar a sincronização de hash de senha como o método de autenticação primário em caso de falha ou de não disponibilidade do AD FS.

## <a name="prerequisites"></a>Pré-requisitos
Esse tutorial é baseado no [Tutorial: Federar um ambiente com uma única floresta do AD na nuvem](tutorial-federation.md) e é um pré-requisito antes de tentar realizar este tutorial.  Se você não tiver concluído este tutorial, faça isso antes de tentar realizar as etapas neste documento.

## <a name="enable-phs-in-azure-ad-connect"></a>Habilitar o PHS no Azure AD Connect
A primeira etapa, agora que temos um ambiente do Azure AD Connect que está usando a federação, é ativar a sincronização de hash de senha e permitir que o Azure AD Connect sincronize os hashes.

Faça o seguinte:

1.  Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Na página Tarefas adicionais, selecione **Personalizar opções de sincronização** e clique em **Avançar**.
4.  Insira o nome de usuário e a senha do seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na tela **Conectar seus diretórios**, clique em **Avançar**.
6.  Na tela **Filtragem de Domínio e UO**, clique em **Avançar**.
7.  Na tela de **Recursos opcionais**, marque a **Sincronização de hash de senha** e clique em **Avançar**.
![Selecionar](media/tutorial-phs-backup/backup1.png)</br>
8.  Na tela **Pronto para configurar**, clique em **Configurar**.
9.  Após a configuração ser concluída, clique em **Sair**.
10. É isso!  Pronto.  A sincronização de hash de senha ocorrerá agora e poderá ser usada como backup em caso de não disponibilidade do AD FS.

## <a name="switch-to-password-hash-synchronization"></a>Alternar para a sincronização de hash de senha
Agora, mostraremos a você como alternar para a sincronização de hash de senha. Antes de começar, considere em quais circunstâncias você deve fazer essa mudança. Não faça a mudança por motivos temporários, como uma interrupção de rede, um pequeno problema do AD FS ou um problema que afeta um subconjunto de seus usuários. Se decidir fazer a mudança porque a correção do problema será muito demorada, faça o seguinte:

1. Clique duas vezes no ícone do Azure AD Connect que foi criado na área de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Alterar a entrada do usuário** e clique em **Avançar**.
![Alteração](media/tutorial-phs-backup/backup2.png)</br>
4.  Insira o nome de usuário e a senha do seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na tela **Entrada do usuário**, selecione **Sincronização de hash de senha** e marque a caixa **Não converter contas de usuário**.  
6.  Deixe a opção padrão **Habilitar o logon único** selecionada e clique em **Avançar**.
7.  Na tela **Habilitar o logon único**, clique em **Avançar**.
8.  Na tela **Pronto para configurar**, clique em **Configurar**.
9.  Quando a configuração estiver concluída, clique em **Sair**.
10. Os usuários agora podem usar suas senhas para entrar no Azure e nos serviços do Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Testar entrando com um dos usuários

1. Navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Entre com uma conta de usuário que foi criada no novo locatário.  Será necessário entrar usando o formato a seguir: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para entrar localmente.</br>
   ![Verificar](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|
