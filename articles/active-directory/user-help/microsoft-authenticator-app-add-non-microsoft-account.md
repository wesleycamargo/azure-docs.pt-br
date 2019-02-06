---
title: Adicione suas contas não Microsoft pessoais ao aplicativo Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Como adicionar suas contas não Microsoft pessoais, como Google, Facebook ou GitHub ao aplicativo Microsoft Authenticator para a verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: db59d3d2795b8cfa3fc7682f4486d968f39ab6ab
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459138"
---
# <a name="add-your-personal-non-microsoft-accounts"></a>Adicionar suas contas pessoais não Microsoft
Adicione suas contas não Microsoft pessoais, como Google, Facebook ou GitHub ao aplicativo Microsoft Authenticator para a verificação de dois fatores.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](microsoft-authenticator-app-how-to.md).

## <a name="add-personal-accounts"></a>Adicionar contas pessoais
Em geral, para todas as suas contas pessoais, você deverá:

1. Entrar na sua conta e, em seguida, ativar a verificação de dois fatores usando seu dispositivo ou computador.

2. Adicionar a conta ao aplicativo Microsoft Authenticator.

Estamos fornecendo o processo aqui para suas contas do Facebook, do Google, do GitHub e da Amazon, mas esse processo é o mesmo para qualquer outro aplicativo, como Adobe, Netflix ou Instagram.

## <a name="add-your-google-account"></a>Adicionar sua conta do Google
Adicione sua conta do Google ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No computador, vá para https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, selecione **Começar** e verifique sua identidade.

2. Siga as etapas na página para ativar a verificação em duas etapas para sua conta pessoal do Google.

### <a name="add-your-google-account-to-the-app"></a>Adicionar sua conta do Google ao aplicativo

1. Na página do Google no computador, vá para a seção **Configurar a segunda etapa alternativa**, escolha **Configurar** na seção **Aplicativo autenticador**.

2. Na página **Obter códigos do aplicativo Microsoft Authenticator**, selecione **Android** ou **iPhone** com base no seu tipo de telefone e selecione **Avançar**.

    Você recebe um código QR que pode usar para associar automaticamente sua conta ao aplicativo Microsoft Authenticator. Não feche esta janela.

3. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** na parte superior direita e selecione **Outra conta (Google, Facebook etc.)**.

4. Use a câmera do seu dispositivo para ler o código QR da página **Configurar o Autenticador** no computador.

    >[!Note]
    >Se sua câmera não estiver funcionando corretamente, você poderá [inserir o código QR e o URL manualmente](#add-an-account-to-the-app-manually).

5. Examine a página **Contas** do aplicativo Microsoft Authenticator em seu dispositivo para garantir que as informações da sua conta estão corretas e que há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

6. Selecione **Avançar** na página **Configurar o Authenticator** no computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Google e, em seguida, selecione **Verificar**.

7. Sua conta é verificada e você pode selecionar **Concluído** para fechar a página **Configurar o Autenticador**.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a conta do Google, confira [Ativar a verificação em duas etapas](https://support.google.com/accounts/answer/185839) e [Saiba mais sobre a verificação em duas etapas](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adicionar sua conta do Facebook
Adicione sua conta do Facebook ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No computador, abra o Facebook, selecione o menu suspenso no canto superior direito e vá para **Configurações** > **Segurança e Logon**.

    A página **Segurança e Logon** é exibida.

2. Vá até a opção **Usar autenticação de dois fatores** na seção **Autenticação de Dois Fatores** e selecione **Editar**.

    A página **Autenticação de Dois Fatores** é exibida.

3. Selecione **Ativar**.

### <a name="add-your-facebook-account-to-the-app"></a>Adicionar sua conta do Facebook ao aplicativo

1. Na página do Facebook no computador, vá para a seção **Adicionar um backup** e escolha **Configuração** na área **Aplicativo de autenticação**.

    Você recebe um código QR que pode usar para associar automaticamente sua conta ao aplicativo Microsoft Authenticator. Não feche esta janela.

2. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** na parte superior direita e selecione **Outra conta (Google, Facebook etc.)**.

3. Use a câmera do seu dispositivo para ler o código QR da página **Autenticação de dois fatores** no computador.

    >[!Note]
    >Se sua câmera não estiver funcionando corretamente, você poderá [inserir o código QR e o URL manualmente](#add-an-account-to-the-app-manually).

4. Examine a página **Contas** do aplicativo Microsoft Authenticator em seu dispositivo para garantir que as informações da sua conta estão corretas e que há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

5. Selecione **Avançar** na página **Autenticação de dois fatores** no computador e digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Facebook.

    Sua conta foi verificada e agora você pode usar o aplicativo para verificar sua conta.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do Facebook, confira [O que é a autenticação de dois fatores e como ela funciona?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Adicionar sua conta do GitHub
Adicione sua conta do GitHub ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No computador, abra o GitHub, selecione a imagem do canto superior direito e selecione **Configurações**.

    A página **Autenticação de dois fatores** é exibida.

2. Selecione **Segurança** na barra lateral **Configurações pessoais** e selecione **Habilitar a autenticação de dois fatores** na área **Autenticação de dois fatores**.

### <a name="add-your-github-account-to-the-app"></a>Adicionar sua conta do GitHub ao aplicativo

1. Na página **Autenticação de dois fatores** no computador, selecione **Configurar usando um aplicativo**.

2. Salve seus códigos de recuperação para que possa entrar novamente em sua conta caso perca o acesso e selecione **Avançar**. 

    Você pode salvar seus códigos baixando-os em seu dispositivo, imprimindo uma cópia física ou copiando-os em uma ferramenta de gerenciamento de senhas.

3. Na página **Autenticação de dois fatores**, selecione **Configurar usando um aplicativo**.

    As página muda para mostrar a você um código QR. Não feche esta página.

4. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Outra conta (Google, Facebook, etc.)** e, em seguida, selecione **inserir este código de texto** do texto na parte superior da página.

    O aplicativo Microsoft Authenticator não consegue ler o código QR, portanto, você precisa inserir o código manualmente.

5. Insira um **Nome da conta** (por exemplo, GitHub) e digite a **chave secreta** da Etapa 4 e, em seguida, selecione **Concluir**.

4. Na página **Autenticador de dois fatores** no computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do GitHub e, em seguida, selecione **Habilitar**.

    A página **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do GitHub, confira [Sobre a autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Adicionar sua conta da Amazon
Adicione sua conta da Amazon ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No computador, abra a Amazon, selecione o menu suspenso **Conta e Listas** e selecione **Sua Conta**.

2. Selecione **Logon e segurança**, entre na sua conta da Amazon e selecione **Editar** na área **Configurações de Segurança Avançadas**.

    A página **Configurações de Segurança Avançadas** é exibida.

3. Selecione **Introdução**.

4. Selecione **Aplicativo Autenticador** na página **Escolha como você vai receber códigos**.

    As página muda para mostrar a você um código QR. Não feche esta página.

5. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** na parte superior direita e selecione **Outra conta (Google, Facebook etc.)**.

6. Use a câmera do seu dispositivo para ler o código QR da página **Escolha como você vai receber códigos** no computador.

    >[!Note]
    >Se sua câmera não estiver funcionando corretamente, você poderá [inserir o código QR e o URL manualmente](#add-an-account-to-the-app-manually).

5. Examine a página **Contas** do aplicativo Microsoft Authenticator em seu dispositivo para garantir que as informações da sua conta estão corretas e que há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

6. Na página **Escolha como você vai receber códigos** no computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta da Amazon e selecione **Verificar código e continuar**.

7. Conclua o restante do processo de inscrição, incluindo a adição de um método de verificação de backup, tal como uma mensagem de texto. Em seguida, selecione **Enviar código**.

8. Na página **Adicionar um método de verificação de backup** em seu computador, digite o código de verificação de seis dígitos fornecido pelo seu método de verificação de backup para sua conta da Amazon e, em seguida, selecione **Verificar código e continuar**.

9. Na página Quase concluído, decida se deseja tornar seu PC um dispositivo confiável e, em seguida, selecione **Entendi. Ative a verificação em duas etapas**.

    A página **Configurações de Segurança Avançadas** é exibida, mostrando os detalhes da sua verificação de dois fatores atualizada.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a conta da Amazon, confira [Sobre a verificação em duas etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) e [Entrando com a verificação em duas etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](microsoft-authenticator-app-phone-signin-faq.md).

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md).
