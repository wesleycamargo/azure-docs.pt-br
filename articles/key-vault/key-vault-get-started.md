<properties
	pageTitle="Introdução ao Cofre da Chave do Azure | Visão Geral"
	description="Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/22/2015"
	ms.author="cabailey"/>

# Introdução ao Cofre da Chave do Azure #
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](../../../../pricing/details/key-vault/).

## Introdução  
Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado (um cofre) no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure. Ele percorre o processo de uso do Windows PowerShell para criar um cofre que contém uma chave ou senha que você pode usar com um aplicativo do Azure. Em seguida, ele mostra como um aplicativo pode usar essa chave ou senha.

**Tempo estimado para conclusão:** 20 minutos

>[AZURE.NOTE]Este tutorial não inclui instruções de como escrever o aplicativo do Azure incluído em uma das etapas, que mostra como autorizar um aplicativo a usar uma chave ou um segredo do cofre de chave.
>
>No momento, não é possível configurar o Cofre da Chave do Azure no portal do Azure. Em vez disso, use estas instruções do PowerShell do Azure. Ou, para obter instruções de Interface de linha de comando entre diferentes plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

- Uma assinatura do Microsoft Azure. Se não tiver uma assinatura, você pode se inscrever para uma [avaliação gratuita](../../../../pricing/free-trial).
- PowerShell do Azure, versão 0.9.1 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
- Um aplicativo que será configurado para usar a chave ou senha que você criará neste tutorial. Um aplicativo de exemplo está disponível na [Centro de Download da Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame.


Este tutorial foi criado para iniciantes do Windows PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

	Get-Help Add-AzureAccount -Detailed

Leia também os tutoriais a seguir para se familiarizar com o Gerenciador de Recursos do Azure do Windows PowerShell:

- [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
- [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Conectar-se a suas assinaturas ##

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:

    Add-AzureAccount

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Windows PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas e quiser especificar uma a ser usada para o Cofre da Chave do Azure, digite o seguinte para ver as assinaturas da sua conta:

    Get-AzureSubscription

Depois, para especificar a assinatura a ser usada, digite:

    Select-AzureSubscription -SubscriptionName <subscription name>

Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

## <a id="switch"></a>Alternar para o Gerenciador de Recursos do Azure ##

Os cmdlets do Cofre da Chave demandam o Gerenciador de Recursos do Azure. Por isso, digite o seguinte para alternar para modo do Gerenciador de Recursos do Azure:

	Switch-AzureMode AzureResourceManager

## <a id="resource"></a>Criar um novo grupo de recursos ##

Quando você usa o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados dentro de um grupo de recursos. Nós criaremos um novo grupo de recursos chamado “ContosoResourceGroup” para este tutorial:

	New-AzureResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'

Para o parâmetro - Location, use o comando [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn654582.aspx) para identificar como especificar um local alternativo ao deste exemplo. Se precisar de mais informações, digite: `Get-Help Get-AzureLocation`


## <a id="vault"></a>Criar um cofre de chave ##

Use o cmdlet [New-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903602.aspx) para criar um cofre de chave. Esse cmdlet tem três parâmetros obrigatórios: um **nome do grupo de recursos**, um **nome do cofre de chave** e a **localização geográfica**.

Por exemplo, se você usar o nome de cofre **ContosoKeyVault**, o nome de grupo de recursos **ContosoResourceGroup** e o local **Ásia Oriental**, digite:

    New-AzureKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

A saída deste cmdlet mostra as propriedades do cofre de chave que você acabou de criar. As duas propriedades mais importantes são:

- **Name**: no exemplo, é ContosoKeyVault. Você usará esse nome para outros cmdlets do Cofre da Chave.
- **vaultUri**: no exemplo, é https://contosokeyvault.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais está.

## <a id="add"></a>Adicionar uma chave ou segredo ao cofre de chave ##

Se você quiser que o Cofre da Chave do Azure crie uma chave protegida por software para você, use o cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) e digite o seguinte:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'


No entanto, se você tiver uma chave protegida por software em um arquivo .PFX salvo em sua unidade C:\\, em um arquivo chamado softkey.pfx, que você deseja carregar no Cofre da Chave do Azure, digite o seguinte para definir a variável **securepfxpwd** como a senha **123** para o arquivo .PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Depois, digite o seguinte para importar a chave do arquivo .PFX, que protege a chave com um software no serviço de Cofre da Chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Agora, você pode fazer referência à chave que foi criada ou carregada no Cofre da Chave do Azure, usando seu URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual e use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica. 

Para exibir o URI dessa chave, digite:

	$Key.key.kid

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e que tem o valor Pa$$w0rd no Cofre da Chave do Azure, converta o valor Pa$$w0rd para uma cadeia de caracteres segura digitando o seguinte:

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Em seguida, digite o seguinte:

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Agora, você pode fazer referência a essa senha que foi adicionada ao Cofre da Chave do Azure usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Para exibir o URI do segredo, digite:

	$secret.Id

Vejamos a chave ou o segredo que você acabou de criar:

- Para exibir a chave, digite: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Para exibir o segredo, digite: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Agora, seu cofre de chave e a chave ou o segredo estão prontos para serem usados pelos aplicativos. Você precisa autorizar os aplicativos a usá-los.

## <a id="register"></a>Registrar um aplicativo com o Active Directory do Azure ##

Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ela não é específica ao Cofre da Chave do Azure, mas é incluída aqui para que as informações fiquem completas.


>[AZURE.IMPORTANT]Para concluir o tutorial, sua conta, o cofre e o aplicativo que você registrará nesta etapa devem todos estar no mesmo diretório do Azure.

Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo no seu Active Directory do Azure. No final do registro, o proprietário do aplicativo obtém os seguintes valores:


- Uma **ID do Aplicativo** (também conhecida como ID do Cliente) e uma **chave de autenticação** (também conhecida como o segredo compartilhado). O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. Como o aplicativo é configurado para fazer isso depende do aplicativo. Para o aplicativo de exemplo do Cofre da Chave, o proprietário do aplicativo define esses valores no arquivo app.config.



Para registrar seu aplicativo com o Active Directory do Azure:

1. Entre no Portal do Azure.
2. À esquerda, clique em **Active Directory** e selecione o diretório no qual você registrará o aplicativo. <br> <br> Observação: você deve selecionar o mesmo diretório que contém a assinatura do Azure com a qual você criou o cofre de chave. Se você não souber qual é o diretório, clique em **Configurações**, identifique a assinatura com a qual você criou o cofre de chave e anote o nome do diretório exibido na última coluna.

3. Clique em **APLICATIVOS**. Se nenhum aplicativo tiver sido adicionado ao seu diretório, essa página mostrará somente o link **Adicionar um Aplicativo**. Clique no link ou em **ADICIONAR** na barra de comandos.
4.	No assistente **ADICIONAR APLICATIVO**, na página **O que você deseja fazer?**, clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.
5.	Na página **Conte-nos sobre seu aplicativo**, especifique um nome para seu aplicativo e selecione **APLICATIVO WEB E/OU API WEB** (o padrão). Clique no ícone Avançar.
6.	Na página **Propriedades do aplicativo**, especifique o **URL DE LOGON** e o **URI DE ID DO APLICATIVO** para seu aplicativo Web. Se seu aplicativo não tiver esses valores, você pode inventá-los para esta etapa (por exemplo, você poderia especificar http://test1.contoso.com para as duas caixas). Não importa se os sites existem, o importante é que o URI de ID de cada aplicativo é diferente para todos os aplicativos no diretório. O diretório usa essa cadeia de caracteres para identificar seu aplicativo.
7.	Clique no ícone Concluído para salvar suas alterações no assistente.
8.	Na página de Início Rápido, clique em **CONFIGURAR**.
9.	Role até a seção **chaves**, selecione a duração e clique em **SALVAR**. A página é atualizada e passa a mostrar um valor de chave. Você deve configurar seu aplicativo com esse valor de chave e o valor da **ID DO CLIENTE**. (As instruções para esta configuração são específicas do aplicativo).
10.	Copie o valor da ID do cliente desta página, que você usará na próxima etapa para definir as permissões no cofre.




## <a id="authorize"></a>Autorizar o aplicativo a usar a chave ou segredo ##


Para autorizar o aplicativo a acessar a chave ou segredo no cofre, use o cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx).

Por exemplo, se o nome do cofre for ContosoKeyVault e o aplicativo que você quer autorizar tiver a ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre. Em seguida, execute o seguinte:

	Set-AzureKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign



## <a id="HSM"></a>Se quiser usar um HSM (módulo de segurança de hardware) ##

Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para [Excluir o cofre de chave e chaves e segredos associados](#delete).

Para criar essas chaves protegidas por HSM, você deve ter uma [assinatura de cofre que dê suporte a chaves protegidas por HSM](../../../pricing/free-trial).


Quando criar o cofre, adicione o parâmetro “SKU”:

	New-AzureKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'

Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre. Para criar uma chave protegida por HSM, defina o parâmetro de Destino como “HSM”:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Você pode usar o comando a seguir para importar uma chave de um arquivo .PFX para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'

O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Isso permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre da Chave, sem que a chave deixe os limites do HSM:

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obter instruções detalhadas sobre como gerar esse pacote BYOK, consulte [Como gerar e transferir chaves protegidas por HSM para o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Excluir o cofre de chave e as chaves e segredos associados ##

Se não precisar mais do cofre de chave e da chave ou segredo contido nele, você pode excluir o cofre de chave usando o cmdlet [Remove-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903603.aspx):

	Remove-AzureKeyVault -VaultName 'ContosoKeyVault'

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

	Remove-AzureResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Outros cmdlets do PowerShell do Azure ##

Outros comandos que podem ser úteis para gerenciar o Cofre da Chave do Azure:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: este comando obtém uma exibição em tabela de todas as chaves e propriedades selecionadas.
- `$Keys[0]`: este comando exibe uma lista completa de propriedades para a chave especificada
- `Get-AzureKeyVaultSecret`: este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: exemplo de como remover uma chave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: exemplo de como remover um segredo específico.






## <a id="next"></a>Próximas etapas ##

Para um tutorial de acompanhamento que usa o Cofre da Chave do Azure em um aplicativo Web, consulte [Usar o Cofre da Chave do Azure em um Aplicativo Web](key-vault-use-from-web-application.md).

Para obter uma lista dos cmdlets do Windows PowerShell para o Cofre da Chave do Azure, consulte [Cmdlets do Cofre da Chave do Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Para referências de programação, consulte [Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903625.aspx) na biblioteca de documentação do Microsoft Azure no MSDN.
 

<!---HONumber=July15_HO4-->