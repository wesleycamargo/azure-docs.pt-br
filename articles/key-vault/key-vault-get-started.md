---
title: "Introdução ao Cofre de Chaves do Azure | Microsoft Docs"
description: "Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/20/2017
ms.author: barclayn
ms.openlocfilehash: 1b70802945b710059e93b54607996ccf74510d1f
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-azure-key-vault"></a>Introdução ao Cofre da Chave do Azure
Este artigo ajuda a começar a usar o Azure Key Vault usando o PowerShell e o orienta quanto às seguintes atividades:
- Como criar um contêiner protegido (um cofre) no Azure.
- Como usar o KeyVault para armazenar e gerenciar chaves de criptografia e segredos no Azure.
- Como um aplicativo pode usar essa chave ou senha.

O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Este artigo não inclui instruções sobre como gravar um aplicativo do Azure. Você pode usar o [Aplicativo de exemplo do Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) para essas etapas.

Para obter instruções de interface de linha de comando entre diferentes plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Requisitos
Antes de avançar com o artigo, confirme que você tem:

- **Uma assinatura do Azure**. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Azure PowerShell**, **versão mínima: 1.1.0**. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se você já tiver instalado o Azure PowerShell e não souber a versão, no console do Azure PowerShell, digite `(Get-Module azure -ListAvailable).Version`. Quando você tiver o Azure PowerShell versão 0.9.1 até 0.9.8 instalado, ainda poderá usar este tutorial com algumas pequenas alterações. Por exemplo, você deverá usar o comando `Switch-AzureMode AzureResourceManager` ; alguns dos comandos do Cofre da Chave do Azure foram alterados. Para obter uma lista dos cmdlets do Cofre de Chaves para as versões 0.9.1 até 0.9.8, consulte [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).
- **Um aplicativo que pode ser configurado para usar o Key Vault**. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo de acompanhamento **Leiame**.

>[!NOTE]
Este artigo pressupõe uma compreensão básica sobre o PowerShell e o Azure. Para obter mais informações sobre o PowerShell, consulte [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet **Get-Help** .

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Por exemplo, para obter ajuda para o cmdlet **AzureRmAccount Login** , digite:

```PowerShell
Get-Help Login-AzureRmAccount -Detailed
```

Você também pode ler os artigos a seguir para se familiarizar com o Modelo de implantação do Azure Resource Manager no Azure PowerShell:

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Usando o PowerShell do Azure com o Gerenciador de Recursos](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Conectar-se a suas assinaturas
Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```PowerShell
Login-AzureRmAccount
```

>[!NOTE]
 Se você estiver usando uma instância específica do Azure, use o parâmetro -Environment. Por exemplo: 
 ```powershell
 Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tem várias assinaturas e quer especificar uma a ser usada para o Cofre de Chaves do Azure, digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzureRmSubscription
```

Depois, para especificar a assinatura a ser usada, digite:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a id="resource"></a>Criar um novo grupo de recursos
Quando você usa o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados dentro de um grupo de recursos. Nós criaremos um novo grupo de recursos chamado **ContosoResourceGroup** para este tutorial:

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Criar um cofre de chave
Use o cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) para criar um cofre de chaves. Esse cmdlet tem três parâmetros obrigatórios: um **nome do grupo de recursos**, um **nome do cofre de chaves** e a **localização geográfica**.

Por exemplo, se você usar:
- Nome de cofre como **ContosoKeyVault**.
- Nome do grupo de recursos como **ContosoResourceGroup**.
- O local como **Leste dos EUA**.

você digitaria:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Saída após a conclusão do comando de criação do Key Vault](./media/key-vault-get-started/output-after-creating-keyvault.png)

A saída desse cmdlet mostra as propriedades do cofre de chaves que você criou. As duas propriedades mais importantes são:

* **Nome do Cofre**: no exemplo, isso é **ContosoKeyVault**. Você usará esse nome para outros cmdlets do Cofre da Chave.
* **URI do Cofre**: no exemplo, isso é https://contosokeyvault.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais está.

> [!NOTE]
> Ao tentar criar seu novo cofre de chaves, talvez você veja o erro **A assinatura não está registrada para usar o namespace 'Microsoft.KeyVault'**. Se essa mensagem aparecer, execute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Após a conclusão com êxito do registro, você pode executar novamente o comando New-AzureRmKeyVault. Para obter mais informações, consulte [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Adicionar uma chave ou segredo ao cofre de chave
Você pode precisar interagir com o Key Vault e chaves ou segredos de algumas maneiras diferentes.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>O Azure Key Vault gera uma chave protegida por software

Se você quiser que o Azure Key Vault crie uma chave protegida por software para você, use o cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) e digite:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
para exibir o URI dessa chave, digite:
```powershell
$key.id
```

Você pode fazer referência à chave criada ou carregada no Azure Key Vault usando seu URI. Para obter a versão atual, é possível usar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** e use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter essa versão específica.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importar um arquivo PFX existente para o Azure Key Vault

No caso de chaves existentes armazenadas em um arquivo pfx que você deseja carregar ao Azure Key Vault, as etapas são diferentes. Por exemplo:
- Se você tiver uma chave protegida por software em um arquivo .PFX
- O arquivo pfx é nomeado como softkey.pfx 
- O arquivo é armazenado na unidade C.

Você pode digitar:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Depois, digite o seguinte para importar a chave do arquivo .PFX, que protege a chave com um software no serviço de Cofre da Chave:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Para exibir o URI dessa chave, digite:

```powershell
$Key.id
```
Para exibir a chave, digite: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Se quisesse exibir as propriedades do arquivo PFX no portal, você veria algo semelhante a imagem abaixo.

![Qual a aparência de um certificado no portal](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Para adicionar um segredo ao Azure Key Vault

Para adicionar um segredo ao cofre, que é uma senha nomeada como SQLPassword e que tem o valor Pa$$w0rd no Azure Key Vault, converta o valor de Pa$$w0rd para uma cadeia de caracteres segura inserindo:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Em seguida, digite:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Agora, você pode fazer referência a essa senha que foi adicionada ao Cofre da Chave do Azure usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para sempre obter a versão atual e use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Para exibir o URI do segredo, digite:

```powershell
$secret.Id
```
Para exibir seu segredo, digite: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. Outra opção é exibir o segredo no portal.

![segredo](./media/key-vault-get-started/secret-value.png)

Para exibir o valor contido no segredo como texto sem formatação:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Agora, seu cofre de chave e a chave ou o segredo estão prontos para serem usados pelos aplicativos. Você precisa autorizar os aplicativos a usá-los.  

## <a id="register"></a>Registrar um aplicativo com o Active Directory do Azure
Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Não é específico ao Azure Key Vault. Para obter etapas detalhadas sobre como registrar um aplicativo com o Azure Active Directory, você deve analisar o artigo intitulado [Integrando aplicativos com o Active Directory do Azure](../active-directory/develop/active-directory-integrating-applications.md) ou [Usar o portal para criar um aplicativo do Azure Active Directory e entidade de serviço que pode acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md)

> [!IMPORTANT]
> Para concluir o tutorial, sua conta, o cofre e o aplicativo que você registrará nesta etapa devem todos estar no mesmo diretório do Azure.


Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo no seu Active Directory do Azure. No final do registro, o proprietário do aplicativo obtém os seguintes valores:

- Uma **ID do aplicativo** 
- Uma **chave de autenticação** (também conhecida como o segredo compartilhado). 

O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. Como o aplicativo é configurado para fazer isso depende do aplicativo. Para o [aplicativo de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário do aplicativo define esses valores no arquivo app.config.


Para registrar seu aplicativo com o Active Directory do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na esquerda, clique em **Registros do aplicativo**. Se você não vir os registros do aplicativo, clique em **mais serviços** para encontrá-los.  
>[!NOTE]
Você deve selecionar o mesmo diretório que contém a assinatura do Azure com a qual você criou o cofre de chaves. 
3. Clique em **Novo registro de aplicativo**.
4. Na folha **Criar**, especifique um nome para seu aplicativo e depois selecione **APLICATIVO WEB E/OU API WEB** (o padrão) e especifique a **URL DO LOGON** do seu aplicativo Web. Se não tiver essas informações no momento, você pode inventá-las para essa etapa (por exemplo, você pode especificar http://test1.contoso.com). Não importa se os sites existem. 

    ![Novo registro de aplicativo](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Verifique se você escolheu **APLICATIVO WEB E/OU API WEB**, caso não, você não verá a opção **chaves** nas configurações.

5. Selecione o botão **Criar** .
6. Ao concluir o registro do aplicativo, você pode ver a lista de aplicativos registrados. Encontre o aplicativo que você acabou de registrar e clique nele.
7. Clique na folha do **Aplicativo registrado** e copie a **ID do aplicativo**
8. Clique em **Todas as configurações**
9. Na folha **Configurações**, clique em **chaves**
9. Insira uma descrição na caixa **Descrição da chave** e selecione uma duração; depois, clique em **SALVAR**. A página é atualizada e passa a mostrar um valor de chave. 
10. Você usará a **ID do aplicativo** e as informações da **Chave** na próxima etapa para definir permissões em seu cofre.

## <a id="authorize"></a>Autorizar o aplicativo a usar a chave ou segredo
Para autorizar o aplicativo para acessar a chave ou o segredo no cofre, use o cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Por exemplo, se o nome do cofre for **ContosoKeyVault** e o aplicativo que você quer autorizar tiver a ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre, execute o seguinte:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se você deseja autorizar que o mesmo aplicativo leia segredos em seu cofre, execute o seguinte:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```

## <a id="HSM"></a>Trabalhando com um HSM (módulo de segurança de hardware)
Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para [Excluir o cofre de chave e chaves e segredos associados](#delete).

Para criar essas chaves protegidas por HSM, você deverá usar a [Camada de serviço Premium do Cofre de Chaves do Azure para dar suporte a chaves protegidas por HSM](https://azure.microsoft.com/pricing/free-trial/). Além disso, observe que essa funcionalidade não está disponível para o Azure China.

Ao criar o cofre de chaves, adicione o parâmetro **-SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre de chaves. Para criar uma chave protegida por HSM, defina o parâmetro **-Destination** como 'HSM':

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Você pode usar o comando a seguir para importar uma chave de um arquivo .PFX para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Esse cenário permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre de Chaves, sem que a chave deixe os limites do HSM:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Para obter instruções detalhadas sobre como gerar esse pacote BYOK, confira [Como gerar e transferir chaves protegidas por HSM para o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Excluir o cofre de chave e as chaves e segredos associados
Se você não precisar mais do cofre de chaves e da chave ou segredo que ele contém, poderá excluir o cofre de chaves usando o cmdlet [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Outros cmdlets do PowerShell do Azure
Outros comandos que podem ser úteis para gerenciar o Cofre da Chave do Azure.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: este comando obtém uma exibição em tabela de todas as chaves e propriedades selecionadas.
- `$Keys[0]`: este comando exibe uma lista completa de propriedades para a chave especificada
- `Get-AzureKeyVaultSecret`: este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: exemplo de como remover uma chave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: exemplo de como remover um segredo específico.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)
- Para ver como o cofre de chaves está sendo usado, confira [Log do Cofre de Chaves do Azure](key-vault-logging.md).
- Para um tutorial de acompanhamento que usa o Cofre da Chave do Azure em um aplicativo Web, confira [Usar o Cofre da Chave do Azure em um Aplicativo Web](key-vault-use-from-web-application.md).
- Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).
- Para obter uma lista dos cmdlets do Azure PowerShell mais recentes para o Cofre de Chaves do Azure, consulte [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).