<properties
   pageTitle="Guia do desenvolvedor do Cofre da Chave | Microsoft Azure"
   description="Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure. "
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
   ms.date="07/07/2016"
   ms.author="bruceper" />

# Guia do desenvolvedor do Cofre da Chave do Azure
Usando o Cofre da Chave, você poderá acessar informações confidenciais em seus aplicativos com segurança, de modo que:

- As chaves e os segredos serão protegidos, sem que você precise escrever o código e será possível utilizá-los facilmente de seus aplicativos.
- Você pode fazer com que seus clientes possuam e gerenciem suas próprias chaves e se concentrar em fornecer os principais recursos do software. Dessa forma, os aplicativos não serão responsáveis ou potencialmente responsáveis pelas chaves e segredos do locatário de seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas ainda manter o gerenciamento de chaves fora de seu aplicativo, de modo que a solução seja adequada a um aplicativo distribuído geograficamente.

Para obter mais informações gerais sobre o Cofre de Chaves do Azure, confira [O que é o Cofre de Chaves](key-vault-whatis.md).

## Vídeos
Este vídeo mostra como criar seu próprio cofre de chaves e como usá-lo por meio do aplicativo de exemplo “Hello Key Vault”.

Links para os recursos mencionados no vídeo:
- [PowerShell do Azure](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de exemplo do Cofre de Chaves do Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para saber mais, você pode seguir o [Blog do Cofre de Chaves](http://aka.ms/kvblog) e participar do [Fórum do Cofre de Chaves](http://aka.ms/kvforum).



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## Criando e gerenciando Cofres da Chave

Antes de trabalhar com o Cofre da Chave do Azure em seu código, você pode criar e gerenciar cofres por meio de REST, modelos de Gerenciador de Recursos, PowerShell ou CLI, conforme descrito nos seguintes artigos:

- [Criar e gerenciar Cofres de Chaves com a CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Criar e gerenciar Cofres das Chaves com o PowerShell](key-vault-get-started.md)
- [Criar e gerenciar Cofres das Chaves com a CLI](key-vault-manage-with-cli.md)
- [Criar um cofre de chave e adicionar um segredo usando um modelo de ARM](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operações em relação a cofres de chaves são autenticadas por meio do AAD e autorizadas por meio da própria política de acesso do Cofre da Chave, definido por cofre.

## Codificação com o Cofre da Chave

O sistema de gerenciamento do Cofre de Chaves para programadores consiste em várias interfaces, com o REST como base, [Referência da API REST do Cofre de Chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Você pode, sujeito a uma autorização bem-sucedida, fazer o seguinte:

- Gerenciar chaves de criptografia usando [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerenciar segredos usando [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Usar chaves de criptografia com as operações [Assinar](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verificar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Criptografar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descriptografar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Os seguintes SDKs estão disponíveis para funcionar com o Cofre da Chave:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt430941.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentação do SDK do .NET](https://msdn.microsoft.com/library/mt430941.aspx)|[Documentação do SDK do Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacote do SDK do .NET](https://azure.microsoft.com/documentation/api/)|[Pacote do SDK do Node.js](https://www.npmjs.com/package/azure-keyvault)|


Para obter exemplos completos de como usar o Cofre da Chave com seus aplicativos, confira:

- Aplicativo de exemplo .NET *HelloKeyVault* e um exemplo de serviço Web do Azure. [Exemplos de código de Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial para ajudá-lo a saber como usar o Cofre da Chave do Azure por meio de um aplicativo Web no Azure. [Usar cofre da chave do Azure em um aplicativo Web](key-vault-use-from-web-application.md)

## Instruções

Os artigos e cenários a seguir fornecem diretrizes específicas da tarefas para trabalhar com o Cofre de Chaves do Azure:

- [Como gerar e transferir chaves protegidas pelo HSM para o Cofre de Chaves do Azure](key-vault-hsm-protected-keys.md) - isso vai ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM a serem usadas com o Cofre de Chaves do Azure.
- [Como transmitir valores seguros (como senhas) durante a implantação](../resource-manager-keyvault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.
- [Como usar o Cofre de Chaves para o gerenciamento extensível de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector do SQL Server para o Cofre de Chaves do Azure permite que o SQL Server e o SQL em uma VM utilizem o serviço do Cofre de Chaves do Azure como um provedor EKM (gerenciamento extensível de chaves) para proteger suas chaves de criptografia para o vínculo de aplicativos; Transparent Data Encryption, Criptografia de Backup e Criptografia de Nível de Coluna.
- [Como implantar certificados em VMs do Cofre de Chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
- [Como integrar o Cofre de Chaves com o Azure Disk Encryption para VMs de IaaS do Windows e do Linux](../azure-security-disk-encryption.md) - A solução de criptografia de disco é integrada ao Cofre de Chaves do Azure para ajudar a controlar e a gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do cofre de chaves, enquanto garante que todos os dados nos discos de máquina virtual serão criptografados em repouso no armazenamento do Azure.
- [Como configurar o Cofre de Chaves com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – Explica como configurar a auditoria e a rotação de chaves com o Cofre de Chaves do Azure.

Para obter mais diretrizes específicas da tarefa sobre como integrar e usar os Cofres de Chaves com o Azure, consulte os [Exemplos de modelo ARM do Ryan Jones para o Cofre de Chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## Introdução ao Cofre de Chaves

Estes artigos abordam outros cenários e serviços que usam ou se integram ao Cofre de Chaves.

- O [Azure Disk Encryption](../azure-security-disk-encryption.md) aproveita o recurso padrão do setor [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.


## Bibliotecas de Suporte

- A [Biblioteca Principal do Cofre de Chaves do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fornece as interfaces `IKey` e `IKeyResolver` para localizar chaves com base em identificadores e realizar operações com chaves.

- [Extensões do Cofre da Chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fornece recursos estendidos para Cofre da Chave do Azure.

## Outros recursos do Cofre de Chaves
- [Blog do Cofre de Chaves](http://aka.ms/kvblog)
- [Fórum do Cofre de Chaves](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0713_2016-->