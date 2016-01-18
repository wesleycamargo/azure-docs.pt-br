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
   ms.date="11/06/2015"
   ms.author="bruceper" />

# Guia do desenvolvedor do Cofre da Chave do Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure. O Cofre da Chave oferece suporte a vários tipos de chave e algoritmos e pode ser usado com módulos de segurança de hardware (HSM) para chaves do cliente de alto valor. Além disso, você pode usar o Cofre da Chave para armazenar com segurança os segredos que são objetos de octeto de tamanho limitado sem nenhuma semântica específica. Um cofre da chave pode conter uma combinação de chaves e segredos. O controle de acesso para os tipos de objetos é gerenciado independentemente.

Usuários, sujeitos a uma autorização bem-sucedida, podem fazer o seguinte:

- Gerenciar chaves de criptografia usando [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerenciar segredos usando [Obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Atualizar] (https://msdn.microsoft.com/library/azure/dn986818.aspx, [Excluir](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Usar chaves de criptografia com as operações [Assinar](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verificar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Criptografar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descriptografar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Operações em cofres de chave são autenticadas e autorizadas usando o Active Directory do Azure.

## Programação para cofre da chave

O sistema de gerenciamento de cofre da chave para programadores consiste em várias interfaces, com o REST como base. [Referência de API REST do Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)

## Gerenciado os cofres da chave

Os contêineres do Cofre da Chave do Azure (cofres) podem ser gerenciados com a REST, o PowerShell ou a CLI, como descrito nos seguintes artigos:

- [Criar e gerenciar Cofres de Chaves com a CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Criar e gerenciar Cofres das Chaves com o PowerShell](key-vault-get-started.md)
- [Criar e gerenciar Cofres das Chaves com a CLI](key-vault-manage-with-cli.md)


## Instruções

Os artigos a seguir apresentam orientação específica da tarefa:

- [Como gerar e transferir chaves protegidas por HSM para o cofre da chave do Azure](key-vault-hsm-protected-keys.md)

## Exemplos

- Este download contém o aplicativo de exemplo HelloKeyVault e um exemplo de serviço Web do Azure. [Exemplos de código de Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web no Azure. [Usar cofre da chave do Azure em um aplicativo Web](key-vault-use-from-web-application.md)

## Bibliotecas de Suporte

- A [Biblioteca Principal do Cofre da Chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fornece interfaces IKey e IKeyResolver para localizar chaves com base em identificadores e realizar operações com chaves.

- [Extensões do Cofre da Chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fornece recursos estendidos para Cofre da Chave do Azure.

<!---HONumber=Nov15_HO3-->