<properties
   pageTitle="Guia do desenvolvedor do Cofre da Chave | Microsoft Azure"
   description="Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Guia do desenvolvedor do Cofre da Chave do Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Usando o Cofre da Chave, você poderá acessar informações confidenciais em seus aplicativos com segurança, de modo que:

- As chaves e os segredos serão protegidos, sem que você precise escrever o código, e poderão ser usados facilmente de seus aplicativos.
- Você pode fazer com que seus clientes possuam e gerenciem suas próprias chaves e se concentrar em fornecer os principais recursos do software. Dessa forma, os aplicativos não serão responsáveis ou potencialmente responsáveis pelas chaves e segredos do locatário de seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas ainda manter o gerenciamento de chaves fora de seu aplicativo, de modo que a solução seja adequada a um aplicativo distribuído geograficamente.

Para saber mais sobre o Cofre da Chave do Azure, confira [O que é o Cofre da Chave](key-vault-whatis.md).

## Criando e gerenciando Cofres da Chave

Antes de trabalhar com o Cofre da Chave do Azure em seu código, você pode criar e gerenciar cofres por meio de REST, modelos de Gerenciador de Recursos, PowerShell ou CLI, conforme descrito nos seguintes artigos:

- [Criar e gerenciar Cofres de Chaves com a CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Criar e gerenciar Cofres das Chaves com o PowerShell](key-vault-get-started.md)
- [Criar e gerenciar Cofres das Chaves com a CLI](key-vault-manage-with-cli.md)
- [Criar um cofre de chave e adicionar um segredo usando um modelo de ARM](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operações em relação a cofres de chaves são autenticadas por meio do AAD e autorizadas por meio da própria política de acesso do Cofre da Chave, definido por cofre.

## Codificação com o Cofre da Chave

O sistema de gerenciamento de cofre da chave para programadores consiste em várias interfaces, com o REST como base, [Referência de API REST do Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Você pode, sujeito a uma autorização bem-sucedida, fazer o seguinte:

- Gerenciar chaves de criptografia usando [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerenciar segredos usando [Obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Usar chaves de criptografia com as operações [Assinar](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verificar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Criptografar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descriptografar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Os seguintes SDKs estão disponíveis para funcionar com o Cofre da Chave:

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentação do SDK do .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentação do SDK do Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacote do SDK do .NET](https://azure.microsoft.com/documentation/api/)|[Pacote do SDK do Node.js](https://www.npmjs.com/package/azure-keyvault)|


Para obter exemplos completos de como usar o Cofre da Chave com seus aplicativos, confira:

- Aplicativo de exemplo .NET *HelloKeyVault* e um exemplo de serviço Web do Azure. [Exemplos de código de Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial para ajudá-lo a saber como usar o Cofre da Chave do Azure por meio de um aplicativo Web no Azure. [Usar cofre da chave do Azure em um aplicativo Web](key-vault-use-from-web-application.md)

## Instruções

Os artigos e cenários abaixo apresentam orientação específica para a tarefa:

- [Como gerar e transferir chaves protegidas por HSM para o cofre da chave do Azure](key-vault-hsm-protected-keys.md)
- [Passe valores seguros (como senhas) durante a implantação](../resource-manager-keyvault-parameter.md).
- Para obter orientações específicas sobre a integração e o uso de Chave do Cofre no Azure, confira [Exemplos de modelo de ARM Ryan Jones para o Cofre da Chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)

## Bibliotecas de Suporte

- A [Biblioteca Principal do Cofre da Chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fornece interfaces `IKey` e `IKeyResolver` para localizar chaves com base em identificadores e realizar operações com chaves.

- [Extensões do Cofre da Chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fornece recursos estendidos para Cofre da Chave do Azure.

<!---HONumber=AcomDC_0309_2016-->