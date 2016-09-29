<properties
	pageTitle="Aplicativo Microsoft Authenticator para telefones celulares | Microsoft Azure"
	description="Saiba como atualizar para a versão mais recente do Azure Authenticator."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="billmath"/>

# Microsoft Authenticator

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança em sua conta do Azure (por exemplo, bsimon@contoso.onmicrosoft.com), conta de trabalho local (por exemplo, bsimon@contoso.com) ou conta da Microsoft (por exemplo, bsimon@outlook.com).

O aplicativo funciona de uma das duas maneiras:

- **Notificação**. O aplicativo pode ajudar a evitar o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Basta ver a notificação e se ela for legítima, selecione **Verificar**. Caso contrário, você pode selecionar **Negar**. Para obter informações sobre as notificações de negação, consulte Como usar o Recurso Negar e Denunciar Fraude para a Autenticação Multifator.

- **Senha com código de verificação**. O aplicativo pode ser usado como um token de software para gerar um código de verificação OAuth. Você insere o código fornecido pelo aplicativo na tela de logon, junto com o nome de usuário e senha, quando solicitado. O código de verificação oferece uma segunda forma de autenticação.

Com o lançamento do aplicativo Microsoft Authenticator, o aplicativo Microsoft Authenticator antigo está sendo substituído. O aplicativo Azure Authenticator continuará funcionando, mas se você decidir ir para o novo aplicativo Microsoft Authenticator, este artigo poderá ajudar.

## Instalar o aplicativo

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Adicionar contas ao aplicativo

Para cada conta que você deseja adicionar ao aplicativo Microsoft Authenticator, use um dos procedimentos a seguir.

### Adicionar uma conta ao aplicativo usando o scanner do código QR

1. Vá para a tela de configurações da verificação de segurança. Para obter mais informações sobre como acessar essa tela, consulte [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

	![Botão Configurar na tela de configurações da verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	Isso abre uma tela com um código QR.

	![Tela que fornece o código QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra o aplicativo Microsoft Authenticator. Na tela de **contas**, selecione **+**, em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante.

	![Tela de contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![Tela para especificar uma conta corporativa ou de estudante](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Use a câmera para verificar o código QR e selecione **Concluído** para fechar a tela do código QR.

	![Tela para verificar um código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	Se a câmera não estiver funcionando corretamente, você poderá inserir o código QR e a URL manualmente. Para obter mais informações, consulte [Adicionar manualmente uma conta ao aplicativo](#add-an-account-to-the-app-manually).

5. Aguarde enquanto a conta é ativada. Quando terminar a ativação, selecione **Contate-me**. Isso enviará uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.

	![Tela onde você pode seleciona Verificar para entrar](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Se sua empresa exigir um PIN para aprovar a verificação de entrada, digite-o.

	![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Após a conclusão da entrada do PIN, selecione **Fechar**. Nesse ponto, sua verificação deve ter sido bem-sucedida.
8. É recomendável que você insira seu número de telefone celular caso perca o acesso ao aplicativo. Especifique o seu país na lista suspensa e insira o número do celular na caixa ao lado do país. Selecione **Avançar**.
9. Neste ponto, você configurou o método de contato. Agora, é hora de configurar as senhas do aplicativo para os aplicativos sem navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, selecione **Concluído**. Caso contrário, passe para a próxima etapa.

	![Tela para criar uma senha do aplicativo](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Se você estiver usando aplicativos sem navegador, copie a senha do aplicativo fornecida e cole-a em seus aplicativos. Para ver as etapas dos aplicativos individuais, como o Outlook e Lync, consulte Como alterar a senha em seu email para a senha do aplicativo e Como alterar a senha em seu aplicativo para a senha do aplicativo.
11. Selecione **Concluído**.

Agora, você deverá ver a nova conta na tela de **contas**.

![Tela de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Adicionar manualmente uma conta ao aplicativo

1. Vá para a tela de configurações da verificação de segurança. Para obter mais informações sobre como acessar essa tela, consulte [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

	![Botão Configurar na tela de configurações da verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	Isso abre uma tela com um código QR. Observe o código e a URL.

	![Tela que fornece o código QR e a URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra o aplicativo Microsoft Authenticator. Na tela de **contas**, selecione **+**, em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante.

	![Tela de contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![Tela para especificar uma conta corporativa ou de estudante](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. No scanner, selecione **inserir o código manualmente**.

	![Tela para verificar um código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Insira o código e a URL nas caixas apropriadas no aplicativo.

	![Tela para inserir código e a URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

	![Tela para inserir código e a URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Aguarde enquanto a conta é ativada. Quando terminar a ativação, selecione **Contate-me**. Isso enviará uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.

Agora, você deverá ver a nova conta na tela de **contas**.

![Tela de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Adicionar uma conta ao aplicativo usando o Touch ID

O aplicativo Microsoft Authenticator no iOS dá suporte ao Touch ID. A Autenticação Multifator do Azure permite que as organizações exijam um PIN para os dispositivos. Com o Touch ID, os usuários do iOS não precisam inserir um PIN. Em vez disso, eles podem digitalizar sua impressão digital e selecionar **Aprovar**.

A configuração do Touch ID com o Microsoft Authenticator é simples. Você conclui um desafio de verificação normal com um PIN. Se seu dispositivo suportar o Touch ID, o Microsoft Authenticator irá configurá-lo automaticamente para essa conta.

![Verificação da configuração do Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Desse ponto em diante, quando você precisar verificar sua entrada, selecionará a notificação por push recebida e digitalizará sua impressão digital em vez de inserir seu PIN.

![Notificação por push](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Desinstalar o antigo aplicativo de Autenticação do Azure

Depois de adicionar todas as contas ao novo aplicativo, desinstale o aplicativo antigo do telefone.

## Excluir uma conta

Para remover uma conta do aplicativo Microsoft Authenticator, selecione a conta, em seguida, selecione **Excluir**.

![Botão Excluir](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0914_2016-->