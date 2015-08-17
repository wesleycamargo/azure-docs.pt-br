<properties 
	pageTitle="O que são senhas de aplicativo no Azure MFA?" 
	description="Esta página ajudará os usuários a entender o que são senhas de aplicativo e para que elas são usadas em relação ao Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# O que são as senhas de aplicativo no Azure Multi-Factor Authentication?

Determinados aplicativos que não usam navegador, como o cliente de email nativo da Apple, que usa o Exchange Active Sync, atualmente, não oferecem suporte à autenticação multifator. O Multi-Factor Authentication é habilitado por usuário. Isso significa que se um usuário tiver sido habilitado para autenticação multifator e tentar usar aplicativos sem navegador, ele não conseguirá usá-lo. Uma senha de aplicativo permite que isso ocorra.

>[AZURE.NOTE]Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication. Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013. Para obter mais informações, consulte [Anúncio do preview público da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Como usar senhas de aplicativo

Veja a seguir do que se lembrar sobre como usar senhas de aplicativo.

- A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
- Atualmente, há um limite de 40 senhas por usuário. Se você tentar criar uma depois de ter atingido o limite, será solicitado que você exclua uma de suas senhas de aplicativo existentes para criar uma nova.
- É recomendável que as senhas de aplicativo sejam criadas por dispositivo, e não por aplicativo. Por exemplo, você pode criar uma senha de aplicativo para seu laptop e usá-la para todos os aplicativos nesse laptop.
- Você recebe uma senha de aplicativo na primeira vez que se conecta. Se precisar de mais, é possível criá-las.
 
<center>![Configuração](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

Quando tiver uma senha de aplicativo, você poderá usá-la no lugar da senha original com esses aplicativos que não usam navegador. Desse modo, se você estiver usando a autenticação multifator, por exemplo, e o cliente de email nativo da Apple no seu telefone, use a senha de aplicativo para que a autenticação multifator seja ignorada e ele continue funcionando.

### Criando senhas de aplicativo
Durante a conexão inicial, você recebe uma senha de aplicativo que pode usar. Além disso, você também pode criar senhas de aplicativo posteriormente. Como fazer isso depende de como você usa a autenticação multifator. Escolha a forma que se aplica a você.

Como você usa a autenticação multifator|Descrição
:------------- | :------------- | 
[Eu a uso com o Office 365](multi-factor-authentication-end-user-manage-o365.md)| Isso significa que você desejará criar senhas de aplicativo por meio do portal do Office 365.
[Eu a uso com o Microsoft Azure](multi-factor-authentication-end-user-manage-azure.md)| Isso significa que você desejará criar senhas de aplicativo por meio do portal do Azure.
[Não sei](multi-factor-authentication-end-user-manage-myapps.md)|Isso significa que você desejará criar senhas de aplicativo por meio de [https://myapps.microsoft.com](https://myapps.microsoft.com)




 

<!---HONumber=August15_HO6-->