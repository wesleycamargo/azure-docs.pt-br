---
title: Gerenciar Azure Key Vault usando CLI | Microsoft Docs
description: Use este tutorial para automatizar tarefas comuns no Cofre da Chave usando a CLI
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>Gerenciar Cofre da Chave usando a CLI

Use este tutorial para ajudá-lo a começar a usar o Azure Key Vault para criar um contêiner de proteção avançado (um cofre) no Azure. O Azure Key Vault é usado para armazenar e gerenciar chaves de criptografia e segredos no Azure. Este artigo orienta no processo de uso da Interface de Linha de Comando de Plataforma Cruzada do Azure para criar um cofre. Você vai interagir com o cofre para executar várias operações comuns. 

O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

**Tempo estimado para conclusão:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever o aplicativo do Azure incluído em uma das etapas. Este aplicativo de exemplo é usado para mostrar como um aplicativo pode ser autorizado a usar uma chave ou segredo armazenado no cofre de chaves.
> Este artigo se concentra na configuração do Azure Key Vault usando a Interface de Linha de Comando de Plataforma Cruzada. Para obter instruções sobre o Azure PowerShell, consulte [este tutorial equivalente](key-vault-get-started.md).

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma assinatura do Microsoft Azure. Se não tiver uma assinatura, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de Linha de Comando versão 0.9.1 ou posterior. Para instalar a versão mais recente e conectá-la à sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](../cli-install-nodejs.md).
* Um aplicativo que será configurado para usar a chave ou senha que você criará neste tutorial. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtendo ajuda com a interface de linha de comando de plataforma cruzada do Azure

Este tutorial pressupõe que você esteja familiarizado com a interface de linha de comando (Bash, Terminal, Prompt de Comando)

O parâmetro --help ou -h pode ser usado para exibir a ajuda de comandos específicos. Como alternativa, o formato azure help [comando] [opções] também pode ser usado para retornar as mesmas informações. Por exemplo, todos os seguintes comandos retornam as mesmas informações:

    azure account set --help

    azure account set -h

    azure help account set

Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a Ajuda usando --help, -h ou azure help [comando].

Você também pode ler os tutoriais a seguir, para se familiarizar com o modelo de implantação do Azure Resource Manager na Interface de Linha de Comando de Plataforma Cruzada do Azure:

* [Como Instalar e configurar a interface de linha de comando de plataforma cruzada do Azure](../cli-install-nodejs.md)
* [Usando a interface de linha de comando de plataforma cruzada do Azure com o Gerenciador de Recursos do Azure](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Conectar-se às suas assinaturas

Para entrar no Azure usando o comando a seguir:

```azurecli-interactive
azure login -u username -p password
```

ou se deseja fazer logon digitando interativamente

```azurecli-interactive
azure login
```

Se você tiver várias assinaturas e quiser especificar uma a ser usada para o Cofre da Chave do Azure, digite o seguinte para ver as assinaturas da sua conta:

```azurecli-interactive
azure account list
```

Depois, para especificar a assinatura a ser usada, digite:

```azurecli-interactive
azure account set <subscription name>
```

Para obter mais informações sobre como configurar a interface de linha de comando de plataforma cruzada do Azure, consulte [Como instalar e configurar a interface de linha de comando entre plataformas do Azure](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Alternar para o Gerenciador de Recursos do Azure
O Cofre da Chave requer o Gerenciador de Recursos do Azure. Por isso, digite o seguinte para alternar para o modo do Gerenciador de Recursos do Azure:

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos
Ao usar o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados em um grupo de recursos. Criaremos um novo grupo de recursos “ContosoResourceGroup” para este tutorial.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para o local, use o comando `azure location list` para identificar como especificar um local alternativo ao deste exemplo. Se precisar de mais informações, digite: `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrar o provedor de recursos do Cofre de Chaves
Verifique se o provedor de recursos do Cofre de Chaves está registrado em sua assinatura:

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

Isso só precisa ser feito uma vez por assinatura.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Use o comando `azure keyvault create` para criar um cofre de chave. Esse script tem três parâmetros obrigatórios: um nome do grupo de recursos, um nome do cofre da chave e a localização geográfica.

Por exemplo:
- Se você usar o nome do cofre **ContosoKeyVault**.
- O nome do grupo de recursos **ContosoResourceGroup**
- O local do tipo **Ásia Oriental**:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

A saída do comando mostra as propriedades do cofre da chave que você acabou de criar. As duas propriedades mais importantes são:

* **Name**: no exemplo, é ContosoKeyVault. Você usará esse nome para outros cmdlets do Cofre da Chave.
* **vaultUri**: no exemplo, isso é https://contosokeyvault.vault.azure.net. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais está.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao cofre da chave

Se você quiser que o Cofre da Chave do Azure crie uma chave protegida por software para você, use o comando `azure key create` e digite o seguinte:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

No entanto, se você tiver uma chave existente em um arquivo PEM salvo como arquivo local em um arquivo chamado softkey.pem que você deseja carregar no Cofre da Chave do Azure, digite o seguinte para importar a chave do arquivo PEM, o qual protege a chave pelo software no serviço de Cofre da Chave:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

Agora você pode fazer referência à chave que criada ou carregada no Cofre da Chave do Azure, usando o URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para sempre obter a versão atual e use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e que tem o valor Pa$$w0rd no Cofre da Chave do Azure, digite o seguinte:

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

Agora, você pode fazer referência a essa senha que foi adicionada ao Cofre da Chave do Azure usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para sempre obter a versão atual e use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vamos exibir a chave ou o segredo que você acabou de criar:

* Para exibir a chave, digite: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Para exibir o segredo, digite: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrar um aplicativo com o Active Directory do Azure

Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ela não é específica ao Cofre da Chave do Azure, mas é incluída aqui para que as informações fiquem completas.

> [!IMPORTANT]
> Para concluir o tutorial, sua conta, o cofre e o aplicativo que você registrará nesta etapa devem todos estar no mesmo Azure Active Directory.

Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo no seu Active Directory do Azure. No final do registro, o proprietário do aplicativo obtém os seguintes valores:

- Uma **ID do Aplicativo** 
- Uma **chave de autenticação** (também conhecida como o segredo compartilhado). 

O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. Como o aplicativo é configurado para fazer isso depende do aplicativo. No [aplicativo de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário do aplicativo define esses valores no arquivo app.config.

Para obter etapas detalhadas sobre como registrar um aplicativo com o Azure Active Directory, você deve ler o artigo intitulado [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) ou [Usar o portal para criar um aplicativo do Azure Active Directory e uma entidade de serviço que possa acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) Para registrar o aplicativo no Azure Active Directory:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na esquerda, clique em **Registros de aplicativo**. Se você não vir os registros do aplicativo, clique em **mais serviços** para encontrá-los.  
    >[!NOTE]
    Você deve selecionar o mesmo diretório que contém a assinatura do Azure com a qual você criou o cofre de chaves. 
3. Clique em **Novo registro de aplicativo**.
4. Na folha **Criar**, especifique um nome para o seu aplicativo e selecione **APLICATIVO WEB E/OU API DA WEB** (o padrão) e especifique a **URL DE LOGON** do seu aplicativo Web. Se não tiver essas informações no momento, você pode inventá-las para essa etapa (pode, por exemplo, especificar http://test1.contoso.com). Não importa se os sites existem. 

   ![Novo registro de aplicativo](./media/key-vault-manage-with-cli/new-application-registration.png)

5. Selecione o botão **Criar** .
6. Ao concluir o registro do aplicativo, você pode ver a lista de aplicativos registrados. Encontre o aplicativo que você acabou de registrar e clique nele.
7. Clique na folha do **Aplicativo registrado** e copie a **ID do Aplicativo**
8. Clique em **Todas as configurações**
9. Na folha **Configurações**, clique em **chaves**
10. Digite uma descrição na caixa **Descrição da chave** e selecione uma duração; depois, clique em **SALVAR**. A página é atualizada e passa a mostrar um valor de chave. 
11. Você usará a **ID do Aplicativo** e as informações da **Chave** na próxima etapa, para definir permissões em seu cofre.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar o aplicativo a usar a chave ou o segredo
Para autorizar o aplicativo a acessar a chave ou o segredo no cofre, use:

```azurecli-interactive
azure keyvault set-policy
```

Por exemplo, se o nome do cofre for ContosoKeyVault e o aplicativo que você quer autorizar tiver a ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre. Em seguida, execute o seguinte:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Se você estiver executando no prompt de comando do Windows, será necessário substituir aspas por aspas duplas e também inserir escape nas aspas duplas internas. Por exemplo: "[\"decrypt\",\"sign\"]".
> 

Se você deseja autorizar que o mesmo aplicativo leia segredos em seu cofre, execute o seguinte:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se quiser usar um HSM (módulo de segurança de hardware)
Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para [Excluir o cofre de chave e chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar essas chaves protegidas por HSM, você deve ter uma assinatura de cofre que dê suporte a chaves protegidas por HSM.

Ao criar o keyvault, adicione o parâmetro "SKU":

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre. Para criar uma chave protegida por HSM, defina o parâmetro de Destino como “HSM”:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

Você pode usar o comando a seguir para importar uma chave de um arquivo PEM para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Isso permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre da Chave, sem que a chave deixe os limites do HSM:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

Para obter instruções mais detalhadas sobre como gerar esse pacote de BYOK, consulte [Como usar Chaves Protegidas por HSM com o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Excluir o cofre de chave e chaves e segredos associados
Se não precisar mais do cofre da chave e da chave ou do segredo contido nele, você pode excluir o cofre da chave usando o comando keyvault delete:

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos da interface de linha de comando de plataforma cruzada do Azure
Outros comandos que podem ser úteis para gerenciar o Cofre da Chave do Azure.

Este comando lista uma exibição em tabela de todas as chaves e propriedades selecionadas:

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

Este comando exibe uma lista completa de propriedades para a chave especificada:

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas:

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

Aqui está um exemplo de como remover uma chave específica:

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Aqui está um exemplo de como remover um segredo específica:

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>Próximas etapas
- Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).
- Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)
- Como trabalhar com o Azure Key Vault usando Powershell [Introdução ao Azure Key Vault](key-vault-get-started.md).


