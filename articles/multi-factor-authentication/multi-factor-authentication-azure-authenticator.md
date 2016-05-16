<properties 
	pageTitle="Aplicativo Azure Authenticator para telefones celulares" 
	description="Saiba como atualizar para a versão mais recente do Azure Authenticator." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/02/2016" 
	ms.author="billmath"/>



# Movendo para o novo aplicativo Azure Authenticator

Com o lançamento do aplicativo Azure Authenticator, disponível para [Windows Phone](http://www.windowsphone.com/pt-BR/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458), o antigo aplicativo Multi-Factor Authentication está sendo substituído. O aplicativo Multi-Factor Authentication continuará funcionando, mas se você decidir mover para o novo aplicativo Azure Authenticator, este artigo poderá ajudar.


## Como mover para o novo aplicativo Azure Authenticator 

**Etapa 1:** Instalar o Azure Authenticator.

![Nuvem](./media/multi-factor-authentication-azure-authenticator/home.png)

**Etapa 2:** Ativar suas contas com o novo aplicativo

Em primeiro lugar, verifique se você tem o código QR ou o código e a URL para entrada manual da conta que deseja adicionar ao aplicativo.

> [AZURE.NOTE] Não tem certeza de como obter o código QR? Entre em contato com o suporte técnico para obter assistência.
> 
> Não é possível ativar sua conta com o novo aplicativo? Entre em contato com o suporte técnico.
>


Com o código QR em mãos, inicie o aplicativo. Clique em +.


![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

Isso iniciará a câmera para verificação do código QR. Se não for possível verificar o código QR, você sempre terá a opção de entrada manual.

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

- Agora, abra o aplicativo Azure Authenticator e você será levado à página de contas. Aqui, você verá uma lista de contas que foram configuradas. Se quiser adicionar uma nova conta, clique no sinal +. Isso abrirá o scanner.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Digitalize o código de barras. 

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Aguarde enquanto a conta é ativada.

- E isso é tudo. Agora, você verá a nova conta na página de contas.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Como adicionar uma conta do Azure manualmente

Se quiser adicionar uma conta manualmente, você poderá fazer isso desta maneira:

- Primeiro, vá para a página de configurações de verificação de segurança. Para saber mais sobre como acessar essa página, confira [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

- Clique no botão Configurar.
 
![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Isso abrirá uma tela com um código de barras. Observe o código e a URL sob o código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Agora, abra o aplicativo Azure Authenticator e você será levado à página de contas. Aqui, você verá uma lista de contas que foram configuradas. Se quiser adicionar uma nova conta, clique no sinal +. Isso abrirá o scanner.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Clique no botão Inserir Manualmente na parte inferior.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Insira o código e a URL que é fornecida na mesma página que mostra o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Aguarde enquanto a conta é ativada.

- E isso é tudo. Agora, você verá a nova conta na página de contas.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Como adicionar uma conta que não seja do Azure manualmente

Se quiser adicionar manualmente uma conta que não seja do Azure, por exemplo, sua conta da Microsoft, você poderá fazer isso desta maneira:


- A adição manual de uma conta não Azure pode ser feita por meio do exame do código QR ou digitando a chave secreta.
- Se você inserir a chave secreta manualmente, obtenha a chave secreta do site ao qual a conta está associada. Por exemplo, no Outlook.com, vá para as configurações de conta, configurações de segurança e selecione a configuração de um aplicativo de autenticação. Você precisa selecionar Não posso digitalizar o código de barras para obter a chave secreta.
- 

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Abra o aplicativo Azure Authenticator e você será levado à página de contas. Aqui, você verá uma lista de contas que foram configuradas. Se quiser adicionar uma nova conta, clique no sinal +. Isso abrirá o scanner.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Examine o código QR ou clique no botão Inserir Manualmente na parte inferior. Se você examinar o código QR, ignore a próxima etapa, uma vez que a ativação será iniciada imediatamente.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Se inserir manualmente a chave secreta, insira o nome da conta e a chave secreta que é fornecida na mesma página que mostra o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Aguarde enquanto a conta é ativada. Agora, você verá a nova conta adicionada.

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- Conclua o processo inserindo o código que está abaixo de sua conta (nesse caso, é 875 756) e digite-o na caixa da página em que você recebeu a chave secreta e clique em Avançar.  

![Adicionar conta](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Como adicionar uma conta usando o TouchID
O aplicativo móvel Azure Authenticator no iOS oferece suporte a ID de Toque. O Azure Multi-Factor Authentication permite que as organizações solicitem um PIN além de terem o dispositivo registrado. Com esse novo recurso, os usuários do iOS com dispositivos compatíveis com ID de Toque não precisarão inserir mais o PIN. Uma vez configurado, os usuários digitalizam suas impressões digitais em vez de inserirem o PIN e tocam em Aprovar.

A configuração da ID de Toque com o Autenticador do Azure é muito simples. Você apenas conclui um desafio de verificação normal com um PIN, e se o dispositivo oferecer suporte à ID de Toque, nós a configuraremos automaticamente para você.

![ID de Toque](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Desse ponto em diante, quando for necessário verificar sua entrada, você tocará na notificação por push recebida e digitalizará sua impressão digital em vez de inserir o PIN.

![ID de Toque](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Como excluir uma conta

Para remover contas individuais do Aplicativo Azure Authenticator, basta tocar na conta. Você obterá uma opção para "Excluir".

![Remover conta](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0504_2016-->