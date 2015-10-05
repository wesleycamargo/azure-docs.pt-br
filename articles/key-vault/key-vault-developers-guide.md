<properties
   pageTitle="Guia do desenvolvedor do Cofre da Chave | Microsoft Azure"
   description="Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Guia do desenvolvedor do Cofre da Chave do Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure. O Cofre da Chave oferece suporte a vários tipos de chave e algoritmos e pode ser usado com módulos de segurança de hardware (HSM) para chaves do cliente de alto valor. Além disso, você pode usar o Cofre da Chave para armazenar com segurança os segredos que são objetos de octeto de tamanho limitado sem nenhuma semântica específica. Um cofre da chave pode conter uma combinação de chaves e segredos. O controle de acesso para os tipos de objetos é gerenciado independentemente.

Usuários, sujeitos a uma autorização bem-sucedida, podem fazer o seguinte:

- Gerenciar chaves de criptografia usando [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerenciar segredos usando [Obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Atualizar] (https://msdn.microsoft.com/library/azure/dn986818.aspx, [Excluir](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Usar chaves de criptografia com as operações [Assinar](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verificar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Criptografar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descriptografar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Operações em cofres de chave são autenticadas e autorizadas usando o Active Directory do Azure.

## Programação para cofre da chave

O sistema de gerenciamento de cofre da chave para programadores consiste em várias interfaces, com o REST como base.

### REST

A API REST é a base de toda a interação com o Cofre da Chave através de programação.

O Cofre da Chave tem seu próprio ponto de extremidade REST que é descrito na [Referência da API REST do Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903609.aspx)

### .NET

A API do .NET é um conjunto de wrappers que permite a implementação por meio do modelo de programação C# sem precisar interagir diretamente com o ponto de extremidade REST. Aqui você pode encontrar a [referência da API do cliente .NET do Cofre da Chave do Azure](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

A API do Node.js é um conjunto de wrappers que permite a implementação por meio do modelo de programação JavaScript sem precisar interagir diretamente com o ponto de extremidade REST. Aqui você pode encontrar o [SDK do Microsoft Azure para Node.js - Gerenciamento do Cofre da Chave](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

## Gerenciamento do Cofre da Chave com o PowerShell e CLI

As chaves do Cofre da Chave do Azure também podem ser gerenciadas usando o PowerShell e a CLI, conforme descrito nos seguintes artigos:

- [Criar e gerenciar Cofres das Chaves com o PowerShell](key-vault-get-started.md)
- [Criar e gerenciar Cofres das Chaves com a CLI](key-vault-manage-with-cli.md)
- [Como gerar e transferir chaves protegidas por HSM para o cofre da chave do Azure](key-vault-hsm-protected-keys.md)
- [Sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## Consulte também

- [Exemplos de código de Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->