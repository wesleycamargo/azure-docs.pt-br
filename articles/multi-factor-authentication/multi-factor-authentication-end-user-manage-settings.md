<properties 
	pageTitle="Problemas com a Multi-Factor Authentication do Azure | Microsoft Azure" 
	description="Este documento fornecerá informações aos usuários sobre o que fazer em caso de um problema com a Multi-Factor Authentication do Azure." 
	services="multi-factor-authentication"
	keywords = "cliente do multifactor authentication, problema de autenticação, ID de correlação"
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
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Problemas com a Multi-Factor Authentication do Azure
>[AZURE.IMPORTANT]
Ajude-nos a melhorar esta página. Se você não encontrar uma resposta para o seu problema nesta página, forneça comentários detalhados para que possamos providenciar uma solução.

As informações a seguir servem para ajudar você com alguns problemas mais comuns que podem ocorrer.


- [Erros de ID de correlação](#correlation-id-errors)
- [Perdi meu telefone ou ele foi roubado](#i-have-lost-my-phone-or-it-was-stolen)
- [Quero alterar meu número de telefone](#i-want-to-change-my-phone-number)
- [Tenho um novo telefone e preciso alterar o meu número](#i-have-a-new-phone-and-need-to-change-my-phone-number)
- [Não estou recebendo um código no meu telefone](#i-am-not-receiving-a-code-or-a-call-on-my-phone)
- [As senhas de aplicativo não estão funcionando.](#app-passwords-are-not-working)
- [Como limpar o Autenticador do Azure de meu dispositivo antigo e movê-lo para um novo?](#how-do-i-clean-up-azure-authenticator-from-my-old-device-and-move-to-a-new-one)
- [Não encontrei uma resposta para o meu problema](#i-didnt-find-an-answer-to-my-problem)

##Erros de ID de correlação
Se você tiver experimentado as etapas de solução de problemas abaixo e ainda estiver com problemas, será possível postar uma pergunta nos [Fóruns do AD do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [Pesquisar a KB (Base de Dados de Conhecimento) Microsoft](https://www.microsoft.com/pt-BR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) ou [contatar o suporte](https://support.microsoft.com/pt-BR); examinaremos o seu problema assim que possível.

Ao entrar em contato com o suporte, é recomendável incluir as seguintes informações:

 - **Descrição geral do erro**: que mensagem de erro exatamente o usuário viu? Se não havia nenhuma mensagem de erro, descreva o comportamento inesperado observado em detalhes.
 - **Página**: que página você estava quando viu o erro? (Inclua a URL.)
 - **ErrorCode**: o código de erro específico que você está recebendo.
 - **SessionId**: a ID de sessão específica que você está recebendo.
 - **ID de Correlação**: o código de ID de correlação gerado quando o usuário viu o erro.
 - **Carimbo de Data/Hora**: em que data e a hora exatas você viu o erro? (Inclua o fuso horário)
 
![ID de Correlação](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **ID de Usuário** – qual foi a ID do usuário que viu o erro (por exemplo, user@contoso.com)?
 - **Informações sobre o usuário**: o usuário era federado, a senha era sincronizada com hash, somente na nuvem? O usuário tinha uma licença do Azure AD Premium, Enterprise Mobility ou Azure AD Básico atribuída? O usuário está usando o Office 365? etc.

A inclusão dessas informações nos ajudará a resolver o problema o mais rápido possível.

## Perdi meu telefone ou ele foi roubado
Se você tiver perdido seu telefone ou se ele foi roubado, recomendamos que você solicite ao administrador que redefina suas [senhas de aplicativo](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e apague todos os [dispositivos memorizados](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user).

Para retornar à sua conta, você tem duas opções. A primeira é a seguinte: se você tiver configurado um número de telefone de autenticação alternativo, use-o para voltar à sua conta e alterar suas configurações de segurança.

Se você tiver especificado um número de telefone de autenticação secundário, você pode entrar usando ele. ![Configuração](./media/multi-factor-authentication-end-user-manage/altphone.png) Observe que, na captura de tela acima, dois números de telefone foram configurados. Um com final 67 e o segundo com final 30.
  
Para entrar usando o número de telefone alternativo, bastará entrar normalmente e escolher **Usar uma opção de verificação diferente**. ![Verificação diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Em seguida, selecione o outro número de telefone. Nesse caso, você escolheria **Ligue para o número +X XXXXXXXX30**

![Telefone alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]
É importante configurar um número de telefone de autenticação secundário. Como seu número de telefone principal e seu aplicativo móvel provavelmente estão no mesmo telefone, o número de telefone secundário é a única maneira de poder retornar à sua conta caso seu telefone seja roubado ou você o perca.

Se você não tiver configurado um número de telefone de autenticação secundário, será necessário entrar em contato com o administrador e pedir que ele apague suas configurações. Assim, na próxima vez em que você entrar, receberá uma solicitação para [configurar a autenticação multifator](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again) novamente.

## Quero alterar meu número de telefone
Dependendo de como você usa a autenticação multifator, há alguns lugares onde você pode alterar as configurações, como o número de seu telefone. Use a tabela a seguir para escolher a opção que melhor descreve você.

Como você usa a autenticação multifator|Descrição
:------------- | :------------- | 
[Eu a uso com o Office 365](#changing-your-settings-with-office-365)| Isso significa que você desejará alterar as configurações por meio do portal do Office 365.
[Não sei](#changing-your-settings-with-the-myapps-portal)|Isso significa que você desejará entrar em [http://myapps.microsoft.com](http://myapps.microsoft.com) e alterar a configuração.
[Eu a uso com o Microsoft Azure](#changing-your-settings-with-microsoft-azure)| Isso significa que você desejará alterar as configurações por meio do portal do Azure.


 
### Alterando suas configurações com o Office 365


Se você usar a autenticação multifator com o Office 365, será conveniente gerenciar as configurações da verificação de segurança adicional por meio do portal do Office 365.

#### Para alterar as configurações no portal do Office 365

1. Entre no [portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito, selecione o widget e escolha Configurações do Office 365.
3. Clique em Verificação de Segurança Adicional.
4. À direita, clique no link **Atualizar meus números de telefone usados para segurança da conta.** ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isso levará você à página que permitirá alterar as configurações. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Alterar suas configurações com o portal do Myapps

Se você não tiver certeza de como usar a multi-factor authentication, você sempre pode alterar as configurações por meio do portal do myapps.

#### Para alterar as configurações no portal do Myapps

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione Perfil.
3. Escolha Verificação de Segurança Adicional. ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isso levará você à página que permitirá alterar as configurações.

![Prova](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Alterando suas configurações com o Microsoft Azure

Se você usar uma multi-factor authentication com o Azure, talvez seja conveniente alterar suas configurações de aplicativo por meio do portal do Azure.

#### Para acessar as configurações da verificação de segurança adicional no portal do Azure


1. Faça logon no Portal do Azure.
2. Na parte superior do portal do Azure, clique no seu nome de usuário. Isso abrirá uma caixa suspensa.
3. Na caixa suspensa, selecione Verificação de segurança adicional. ![As tabelas](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. Isso levará você à página que permitirá alterar as configurações. ![Prova](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##Tenho um novo telefone e preciso alterar o meu número

Se você tiver um novo telefone e precisar alterar o número de contato principal usado pelo mfa, poderá fazer isso de duas maneiras.

>[AZURE.IMPORTANT]
É importante configurar um número de telefone de autenticação secundário. Como seu número de telefone principal e seu aplicativo móvel provavelmente estão no mesmo telefone, o número de telefone secundário é a única maneira de poder retornar à sua conta caso seu telefone seja roubado ou você o perca.

A primeira é usar um método de autenticação secundário. Se você tiver especificado um número de telefone de autenticação secundário, você pode entrar usando ele. ![Configuração](./media/multi-factor-authentication-end-user-manage/altphone.png) Observe que, na captura de tela acima, dois números de telefone foram configurados. Um com final 67 e o segundo com final 30.
  
Para entrar usando o número de telefone alternativo, bastará entrar normalmente e escolher **Usar uma opção de verificação diferente**. ![Verificação diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Em seguida, selecione o outro número de telefone. Nesse caso, você escolheria **Ligue para o número +X XXXXXXXX30**

![Telefone alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

A segunda é entrar em contato com seu administrador ou com a pessoa que configurou o mfa para você. Você só precisará fazer isso se não tiver configurado um número de telefone de autenticação secundário. Caso contrário, será necessário entrar em contato com o administrador, ou com a pessoa que configurou o mfa, e pedir que ele apague suas configurações. Assim, na próxima vez que entrar, você receberá uma solicitação para [configurar a autenticação multifator](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again) novamente.

##Não recebi um código ou uma ligação em meu telefone

Primeiro, você precisa verificar o seguinte:



- Se você tiver escolhido receber um telefonema em seu celular, verifique se o sinal é adequado. A velocidade de entrega e a disponibilidade podem variar de acordo com o local e o provedor do serviço.
- Se você selecionou a opção para receber os códigos de verificação por mensagem de texto em seu celular, verifique se o seu plano de serviço e dispositivo oferecem suporte à entrega de mensagens de texto. A velocidade de entrega e a disponibilidade podem variar de acordo com o local e o provedor do serviço. Além disso, certifique-se de que o celular esteja com um sinal adequado ao tentar receber esses códigos.
- Se você optar por receber uma verificação por meio do aplicativo móvel, verifique se o sinal do celular está bom. Lembre-se também que a velocidade de entrega e a disponibilidade podem variar de acordo com o local e o provedor do serviço.

Se você tiver um smartphone, recomendamos o uso do [aplicativo Azure Authenticator](multi-factor-authentication-azure-authenticator.md).

Você pode alternar entre o recebimento de seus códigos de verificação por mensagens de texto e por aplicativo móvel escolhendo **Usar uma opção de verificação diferente** quando entrar.

![Verificação diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Às vezes, a entrega de um desses serviços é mais confiável do que a do outro.

Lembre-se de que se você tiver recebido vários códigos de verificação, somente o mais recente deles funcionará.

Se, anteriormente, você configurou um telefone de backup, recomendamos que tente novamente selecionando esse telefone quando receber a solicitação na página de entrada. Se não houver outro método configurado, entre em contato com o administrador e peça que ele apague as configurações. Assim, na próxima vez que você entrar, receberá uma solicitação para [configurar a autenticação multifator](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again) novamente.

##As senhas de aplicativo não estão funcionando
Primeiro, verifique se você inseriu a senha de aplicativo corretamente. Se isso ainda não estiver funcionando, tente se conectar e [criar uma nova senha de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Se isso não funcionar, entre em contato com o administrador e peça que ele [exclua suas senhas de aplicativo existentes](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e crie uma nova para usá-la.

##Como limpar o Autenticador do Azure de meu dispositivo antigo e movê-lo para um novo?
Quando você desinstala o aplicativo do dispositivo ou reinicia o dispositivo, ele não remove a ativação no back-end. Você deve usar as etapas descritas em [movendo para um novo dispositivo](multi-factor-authentication-azure-authenticator.md#how-to-move-to-the-new-azure-authenticator-app).

##Não encontrei uma resposta para o meu problema
Se você não tiver encontrado uma resposta para o seu problema nesta página, poste uma pergunta nos [Fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [Pesquise na Base de Dados de Conhecimento (KB) da Microsoft](https://www.microsoft.com/pt-BR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) ou [entre em contato com o suporte](https://support.microsoft.com/pt-BR).

Você também pode contatar o administrador ou a pessoa que configurou a autenticação multifator para você e ver se ele/ela pode ajudá-lo.

Por fim, deixe alguns comentários detalhados nesta página para que possamos atualizá-la e aprimorá-la sempre com mais informações.

<!---HONumber=AcomDC_0810_2016-->