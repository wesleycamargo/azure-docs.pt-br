---
title: Ajuda com o aplicativo Microsoft Authenticator - Azure AD | Microsoft Docs
description: Fornece uma lista de perguntas frequentes e respostas relacionadas ao aplicativo Microsoft Authentication e ao Autenticação Multifator do Azure.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 9027b09c186dfb7661fc63f200f4d2e5da96a70a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130094"
---
# <a name="microsoft-authenticator-app-faq"></a>Perguntas frequentes sobre o aplicativo Microsoft Authenticator

Este artigo responde a perguntas comuns sobre o aplicativo Microsoft Authenticator. Caso não encontre uma resposta para sua pergunta, vá para o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Além disso, é possível analisar outras perguntas frequentes sobre um recurso específico no aplicativo, [Entrar com seu telefone - perguntas frequentes](microsoft-authenticator-app-phone-signin-faq.md).

O aplicativo Microsoft Authenticator substituiu o aplicativo Azure Authenticator, e é o aplicativo recomendado ao usar a Autenticação Multifator do Azure. O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Quais dados o Authenticator armazena em meu nome e como posso excluí-lo?

O Microsoft Authenticator armazena as informações de conta que você cria quando adiciona uma conta. Quando você usa o Authenticator, um log de diagnóstico é criado para fins de depuração e armazena dados úteis para ajudar a Microsoft a diagnosticar problemas imprevistos. Você pode acessar os dados de log, abrindo **Ajuda** > **Enviar Logs** > **Exibir logs**.

Você pode excluir os dados excluindo o bloco da conta. A exclusão do bloco da conta também exclui todas as informações da conta que estão sendo usadas pelo aplicativo, incluindo os logs. 

Para obter mais informações sobre como a Microsoft usa os dados, acesse: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Para que servem os códigos no aplicativo? Por que a contagem diminui?

Ao abrir o aplicativo Microsoft Authenticator, você verá as contas que adicionou e um número de seis ou oito dígitos para cada uma delas. Você pode ver um temporizador de 30 segundos realizando uma contagem regressiva.

Esses códigos são usados quando você entra em sua conta. Depois de inserir seu nome de usuário e senha, talvez receba uma solicitação para inserir um código de verificação. Abra o aplicativo Microsoft Authenticator e copie o código que está sendo exibido no momento. Insira o código na página de entrada para concluir.

Os códigos mudam a cada 30 segundos para que você nunca use o mesmo código duas vezes. Não é como uma senha a qual você precisa se lembrar. A ideia é que somente alguém com acesso ao seu telefone conhece seu código de verificação.

Os códigos não exigem internet ou dados e, portanto, você não precisa se preocupar em ter um serviço de telefone para entrar. Quando você fecha o aplicativo, ele não fica em execução em segundo plano e não consome sua bateria. Você pode fechar o aplicativo e ignorá-lo até a próxima vez que entrar.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Apenas recebo notificações quando o aplicativo está aberto. Se o aplicativo não está aberto, não recebo nenhuma notificação.

Se você receber notificações, mas elas não fizerem nenhum ruído ou vibrarem apesar de a campainha estar ativada, primeiro verifique as configurações do aplicativo. Permita que o aplicativo use sons ou vibre com suas notificações.

Se você não receber nenhuma notificação, verifique os seguintes casos:

- Seu telefone está no modo Silencioso ou Não Incomodar? Esse modo pode impedir que os aplicativos recebam notificações.
- Você consegue receber notificações em outros aplicativos? Se esse não for o caso, poderá haver um problema com as conexões de rede do telefone ou com o canal de notificações do Android ou da Apple. Você pode conseguir definir a primeira opção nas configurações do telefone, mas talvez seja necessário falar com o provedor de serviços para obter ajuda com a segunda opção.
- Você consegue receber notificações para algumas contas no aplicativo, mas não para outras? Nesse casso, remova a conta com problemas do aplicativo e adicione-a novamente para habilitar as notificações por push outra vez.

Se você tentou essas sugestões de solução de problemas, mas ainda está com problemas, poderá enviar seus registros para diagnóstico. Vá para as configurações de aplicativo, selecione **Ajuda e comentários** e **Enviar logs**. Em seguida, vá para o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) e informe o problema que você está vendo e quais etapas tentou até o momento.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Já estou usando o aplicativo Microsoft Authenticator para códigos de segurança. Como alterno para notificações por push de um clique?
Aprovar uma entrada por meio da notificação por push só está disponível para contas pessoais da Microsoft ou para contas corporativas e de estudante da Microsoft, não para contas de terceiros como Google ou Facebook. Se você tiver uma conta corporativa ou de estudante da Microsoft, sua organização poderá optar por desabilitar essa opção.

Se você usa uma conta da Microsoft como sua conta pessoal e quer alternar para notificações por push, você precisa adicionar sua conta novamente. Registre novamente o dispositivo com sua conta e configure as notificações por push.  

Se você usar o Microsoft Authenticator para sua conta corporativa ou de estudante, sua organização decide se deseja permitir as notificações de um clique.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Notificações por push de um clique funcionam para contas que não são da Microsoft?
Não, as notificações por push só funcionam com contas da Microsoft e contas do Azure Active Directory. Se sua empresa ou escola usar as contas do Azure AD, elas poderão desabilitar esse recurso.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Eu obtenho um novo dispositivo ou restauro meu dispositivo de um backup. Como configurar minhas contas no aplicativo Microsoft Authenticator novamente?
Se você estiver executando um dispositivo iOS, ativou o  **Backup Automático** e criou um backup das contas no dispositivo antigo, você poderá usar esse backup para recuperar as credenciais da conta no novo dispositivo. Para obter mais informações, consulte o artigo [Fazer backup e recuperar credenciais de conta com o aplicativo Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-backup-and-recovery.md). 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>Perdi meu dispositivo ou mudei para um novo dispositivo. Como garantir que as notificações não continuem indo para o dispositivo antigo?  
Adicionar o aplicativo Microsoft Authenticator ao novo dispositivo iOS não removerá automaticamente o aplicativo do dispositivo antigo. Até mesmo excluir o aplicativo do dispositivo antigo não é suficiente. Você deve excluir o aplicativo do dispositivo antigo e informar a Microsoft ou a sua organização para esquecer o dispositivo antigo e cancelar o registro da conta.
- **Para remover o aplicativo de um dispositivo usando uma conta pessoal da Microsoft.** Vá para a área de verificação em duas etapas da página [Segurança da Conta](https://account.microsoft.com/security)  e escolha desativar a confirmação do dispositivo antigo.  
- **Para remover o aplicativo de um dispositivo usando uma conta corporativa ou de estudante da Microsoft.** Vá para a área de verificação em duas etapas da página [MyApps](https://myapps.microsoft.com/) ou para o portal personalizado da sua organização e escolha desativar a verificação do dispositivo antigo. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Como remover uma conta do aplicativo?
* iOS: no menu principal, passe o dedo para esquerda em um bloco de conta. Selecione **Excluir**.
* Windows Phone: No menu principal, selecione o botão de menu, em seguida, **Editar contas**. Tocar o **X** ao lado do nome de conta.
* Android: No menu principal, selecione o botão de menu, em seguida, **Editar contas**. Tocar o **X** ao lado do nome de conta.

Caso tenha um dispositivo registrado em sua organização, você precisará concluir uma etapa extra para remover a conta. Nesses dispositivos, o aplicativo Microsoft Authenticator é registrado automaticamente como um administrador do dispositivo. Se você quiser desinstalar completamente o aplicativo, você precisa primeiro cancelar o registro do aplicativo nas configurações do aplicativo.

### <a name="why-does-the-app-request-so-many-permissions"></a>Por que o aplicativo solicita tantas permissões?
Aqui está uma lista completa de permissões que podem ser solicitadas e como são utilizadas no aplicativo. As permissões específicas que você vê dependem do tipo de telefone que você tem.

* **Câmera**: utilizada para digitalizar códigos QR quando você adiciona uma conta corporativa, de estudante ou não-Microsoft.
* **Contatos e telefone**: utilizado para simplificar o processo, localizando contas existentes no telefone ao entrar com sua conta pessoal da Microsoft.
* **SMS**: utilizado para certificar-se de que o número de telefone corresponde ao número registrado. Ao entrar com sua conta pessoal da Microsoft pela primeira vez.  Enviamos uma mensagem de texto para o telefone em que você baixou o aplicativo, que inclui um código de verificação de 6 a 8 dígitos. Em vez de solicitar para que você localize esse código e insira-o no aplicativo, ele será localizado na mensagem de texto para você.
* **Emitir em outros aplicativos**: a notificação que você recebe e verifica se sua identidade também é exibida em qualquer outro aplicativo que possa estar em exibição.
* **Receber dados da internet**: essa permissão é necessária para enviar notificações.
* **Impedir que o telefone entre em espera**: se você registar o dispositivo na sua organização, a organização poderá alterar essa política no seu telefone.
* **Controle de vibração**: você pode escolher se deseja uma vibração sempre que você receber uma notificação para verificar sua identidade.
* **Usar o hardware de impressão digital**: algumas contas corporativas e de estudantes exigem um PIN adicional sempre que você verifica sua identidade. PARA facilitar o processo, permitimos que você use sua impressão digital em vez da inserção do PIN.
* **Exibir conexões de rede**: quando você adiciona uma conta da Microsoft, o aplicativo requer conexão de rede/internet.
* **Ler o conteúdo de seu armazenamento**: essa permissão é usada apenas quando você relata um problema técnico através das configurações do aplicativo. Algumas informações de seu armazenamento são coletadas para diagnosticar o problema.
* **Acesso de rede completo**: essa permissão é necessária para enviar notificações para verificar sua identidade.
* **Executar na inicialização**: se você reiniciar seu telefone, essa permissão garante que você continue a receber notificações para verificar sua identidade.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Por que o aplicativo Microsoft Authenticator permite que você aprove uma solicitação sem desbloquear o dispositivo?

Não é necessário desbloquear o dispositivo para aprovar solicitações de verificação porque a única prova necessária é a de que seu telefone está com você. A verificação em duas etapas exige a comprovação de duas coisas — algo que você sabe e algo que você tem. Algo que você sabe é a sua senha. Algo que você tem é seu telefone (configurado com o Microsoft Authenticator e registrado como uma prova de MFA). Portanto, ter o telefone e aprovar a solicitação atende aos critérios para o segundo fator de autenticação.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>O que significa o ícone de cadeado na lista de contas?

O ícone de cadeado indica que o dispositivo está registrado no Azure AD e está registrado para a conta. O registro de dispositivo para o iOS ocorre durante o registro do Microsoft Intune.

## <a name="next-steps"></a>Próximas etapas

### <a name="contact-us"></a>Fale conosco
Se sua pergunta não tiver sido respondida aqui, queremos ouvir sua opinião. Acesse o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) para postar a pergunta e obter ajuda da comunidade ou deixe um comentário nesta página.


### <a name="related-topics"></a>Tópicos relacionados
* [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) da conta da Microsoft
* [Está com dificuldades com a verificação de duas etapas](multi-factor-authentication-end-user-troubleshoot.md) para sua conta corporativa ou de estudante?
* [Usar o Microsoft Authenticator para entrar usando seu telefone](microsoft-authenticator-app-phone-signin-faq.md)
