## <a name="set-up-azure-cli-for-azure-dns"></a>Configurar a CLI do Azure para DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Mais informações estão disponíveis em [Instalar a CLI do Azure](../articles/xplat-cli-install.md).

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Abra uma janela do console e autentique com suas credenciais. Para obter mais informações, confira [Conectar-se ao Azure a partir da CLI do Azure](../articles/xplat-cli-connect.md)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Mudar para o modo CLI

O DNS do Azure usa o Azure Resource Manager. Mude para o modo CLI para usar os comandos do Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Selecionar a assinatura

Verificar as assinaturas da conta.

```azurecli
azure account list
```

Escolha quais das suas assinaturas do Azure deseja usar.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Criar um grupos de recursos

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrar provedor de recursos

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Para que você possa usar o DNS do Azure, sua assinatura do Azure deve ser registrada para usar esse provedor de recursos. Essa operação deve ser executa apenas uma vez para cada assinatura.

```azurecli
azure provider register --namespace Microsoft.Network
```



<!--HONumber=Jan17_HO1-->


