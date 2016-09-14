<properties 
	pageTitle="Aplicativo Microsoft Authenticator para telefones celulares" 
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

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança que pode ser usado na conta do Azure (ex: bsimon@contoso.onmicrosoft.com), na conta de trabalho local (ex: bsimon@contoso.com) ou na conta da Microsoft (ex: bsimon@outlook.com).

## Baixe o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Como funciona o aplicativo Microsoft Authenticator
O aplicativo funciona enviando uma notificação para o aplicativo Microsoft Authenticator no seu smartphone ou tablet. Basta tocar em "Verificar" no aplicativo para autenticar.

Como alternativa, o aplicativo também pode ser usado com um código de verificação. Basta inserir o código fornecido pelo aplicativo na tela de entrada quando solicitado.

Esses dois modos diferentes são os seguintes:

**Notificação** – Nesse modo, o aplicativo Microsoft Authenticator impede o acesso não autorizado a contas e impede transações fraudulentas. Isso é feito usando uma notificação por push para seu telefone ou dispositivo registrado. Basta ver a notificação e, se ela for legítima, selecione Autenticar. Caso contrário, você pode escolher Negar ou optar por negar e reportar a notificação fraudulenta. Para obter informações sobre relatórios de notificações fraudulentas, confira Como usar o recurso Negar e Denunciar fraude para Multi-Factor Authentication.

**Senha de uso único** – Nesse modo, o aplicativo Microsoft Authenticator pode ser usado como um token de software para gerar um código de verificação OATH. Esse código de verificação pode ser inserido juntamente com o nome de usuário e a senha, a fim de fornecer a segunda forma de autenticação.

## Adicionar uma conta no aplicativo Microsoft Authenticator usando o scanner de código QR

- No telefone com o aplicativo Microsoft Authenticator instalado, inicie o aplicativo e clique em “+” para adicionar uma nova conta. Em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante, a qual iniciará o scanner de código QR. Se a câmera não estiver funcionando corretamente, você poderá optar por inserir as informações da sua empresa manualmente. [Adicionando uma conta manualmente](#adding-an-account-manually).

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Digitalize a imagem do código QR que aparece com a tela configurar aplicativo móvel. Clique em Concluído para fechar a tela de código QR.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Se não for possível digitalizar o código QR, você poderá inserir as informações manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- No telefone, ele começará a ser ativado; depois de concluído, clique em Contate-me. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Algumas empresas podem exigir um PIN durante a verificação.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Quando isso estiver concluído, você poderá clicar em Fechar. Nesse ponto, sua verificação deve ter sido bem-sucedida.
- Agora é recomendável inserir o número do celular, caso você perca o acesso ao aplicativo móvel.
- Especifique o seu país na lista suspensa e insira o número do celular na caixa próxima ao país. Clique em Próximo.
- Até aqui, você configurou o método de contato e agora é hora de configurar as senhas de aplicativo para aplicativos que não usam navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, clique em **Concluído**. Caso contrário, passe para a próxima etapa.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo que não usa navegador. Para ver as etapas de aplicativos individuais, como Outlook e Lync, consulte Como alterar a senha no seu email para a senha de aplicativo e Como alterar a senha no seu aplicativo para a senha de aplicativo.
- Clique em Concluído.


## Adicionar uma conta ao aplicativo Microsoft Authenticator manualmente
Se você deseja adicionar uma conta manualmente, selecione o botão Inserir conta manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Agora, se você tiver uma conta que tenha o Azure MFA, insira o código e a URL que é fornecida na mesma página que exibe o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Quando isso for concluído, clique em Contato. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.

## Movendo para o novo aplicativo Microsoft Authenticator

Com o lançamento do aplicativo Microsoft Authenticator, o aplicativo Microsoft Authenticator antigo está sendo substituído. O aplicativo Azure Authenticator continuará funcionando, mas se você decidir mover para o novo aplicativo Microsoft Authenticator, este artigo poderá ajudar.


### Como mover para o novo aplicativo Microsoft Authenticator 

**Etapa 1:** instalar o Microsoft Authenticator.


**Etapa 2:** Ativar suas contas com o novo aplicativo

Em primeiro lugar, verifique se você tem o código QR ou o código e a URL para entrada manual da conta que deseja adicionar ao aplicativo.

> [AZURE.NOTE] Não tem certeza de como obter o código QR? Entre em contato com o suporte técnico para obter assistência.
> 
> Não é possível ativar sua conta com o novo aplicativo? Entre em contato com o suporte técnico.
>


Com o código QR em mãos, inicie o aplicativo. Clique em +.

Especifique que você deseja adicionar uma conta corporativa ou de estudante. Isso iniciará a câmera para verificação do código QR. Se não for possível verificar o código QR, você sempre terá a opção de entrada manual.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

Para confirmar que a conta foi ativada com êxito, verifique se a nova conta aparece nas páginas de contas.


Siga esta etapa para todas as contas que deseja migrar para o novo aplicativo.



**Etapa 3:** Desinstalar o aplicativo Multi-Factor Authentication antigo do seu telefone.

Depois de adicionar todas as contas ao novo aplicativo, desinstale o aplicativo antigo do telefone.



## Como adicionar uma conta usando o scanner de código de barras



- Primeiro, vá para a página de configurações de verificação de segurança. Para saber mais sobre como acessar essa página, confira [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

- Clique no botão Configurar.
 
![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Isso abrirá uma tela com um código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Agora, abra o aplicativo Microsoft Authenticator e você será levado à página de contas. Aqui, você verá uma lista de contas que foram configuradas. Se você quiser adicionar uma nova conta, deverá clicar no símbolo + e especificar que você gostaria de adicionar uma conta corporativa ou de estudante. Isso abrirá o scanner.

- Digitalize o código de barras.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Aguarde enquanto a conta é ativada.

- E isso é tudo. Agora, você verá a nova conta na página de contas.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## Como adicionar uma conta do Azure manualmente

Se quiser adicionar uma conta manualmente, você poderá fazer isso desta maneira:

- Primeiro, vá para a página de configurações de verificação de segurança. Para saber mais sobre como acessar essa página, confira [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

- Clique no botão Configurar.
 
![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Isso abrirá uma tela com um código de barras. Observe o código e a URL sob o código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Agora, abra o aplicativo Microsoft Authenticator e você será levado à página de contas. Aqui, você verá uma lista de contas que foram configuradas. Se você quiser adicionar uma nova conta, deverá clicar no símbolo + e especificar que você gostaria de adicionar uma conta corporativa ou de estudante. Isso abrirá o scanner. Isso abrirá o scanner.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Clique em inserir código manualmente na parte inferior.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Insira o código e a URL que é fornecida na mesma página que mostra o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/manual.png)

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- Aguarde enquanto a conta é ativada.

- E isso é tudo. Agora, você verá a nova conta na página de contas.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## Como adicionar uma conta usando o TouchID
O aplicativo móvel Microsoft Authenticator no iOS dá suporte para a ID de Toque. O Azure Multi-Factor Authentication permite que as organizações solicitem um PIN além de terem o dispositivo registrado. Com esse novo recurso, os usuários do iOS com dispositivos compatíveis com ID de Toque não precisarão inserir mais o PIN. Uma vez configurado, os usuários digitalizam suas impressões digitais em vez de inserirem o PIN e tocam em Aprovar.

A configuração da ID de Toque com o Microsoft Authenticator é muito simples. Você apenas conclui um desafio de verificação normal com um PIN, e se o dispositivo oferecer suporte à ID de Toque, nós a configuraremos automaticamente para você.

![ID de Toque](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Desse ponto em diante, quando for necessário verificar sua entrada, você tocará na notificação por push recebida e digitalizará sua impressão digital em vez de inserir o PIN.

![ID de Toque](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Como excluir uma conta

Para remover contas individuais do Aplicativo Microsoft Authenticator, basta tocar na conta. Você obterá uma opção para "Excluir".

![Remover conta](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->