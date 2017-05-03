---
title: Guia do desenvolvedor do Cofre de Chaves | Microsoft Docs
description: Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure.
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: b2b1bd28-e149-4d69-b08b-97f6c50ebe30
ms.service: key-vault
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c6ae0909faf0711b1ad25df48f626630644c5df7
ms.lasthandoff: 04/20/2017


---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor do Cofre da Chave do Azure

Usando o Key Vault, você pode acessar informações confidenciais em seus aplicativos com segurança, de modo que:

* As chaves e os segredos são protegidos sem que você mesmo precise escrever código e você pode usá-los facilmente em seus aplicativos.
* Você pode fazer com que seus clientes possuam e gerenciem suas próprias chaves e se concentrar em fornecer os principais recursos do software. Dessa forma, os aplicativos não serão responsáveis ou potencialmente responsáveis pelas chaves e segredos do locatário de seus clientes.
* Seu aplicativo pode usar chaves para assinatura e criptografia, mas ainda manter o gerenciamento de chaves fora de seu aplicativo, permitindo que sua solução seja adequada a um aplicativo distribuído geograficamente.
* Com o lançamento de setembro de 2016 do Cofre de Chaves, seus aplicativos agora podem fazer uso dos certificados do Cofre de Chaves. Para obter mais informações, consulte o artigo **Sobre chaves, segredos e certificados** na [Referência REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Para obter mais informações gerais sobre o Cofre de Chaves do Azure, confira [O que é o Cofre de Chaves](key-vault-whatis.md).

## <a name="videos"></a>Vídeos

Este vídeo mostra como criar seu próprio cofre de chaves e como usá-lo por meio do aplicativo de exemplo “Hello Key Vault”.


>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player]


Links para os recursos mencionados no vídeo:

* [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
* [Código de exemplo do Cofre de Chaves do Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para saber mais, você pode seguir o [Blog do Cofre de Chaves](http://aka.ms/kvblog) e participar do [Fórum do Cofre de Chaves](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando Cofres da Chave

Antes de trabalhar com o Cofre da Chave do Azure em seu código, você pode criar e gerenciar cofres por meio de REST, modelos de Gerenciador de Recursos, PowerShell ou CLI, conforme descrito nos seguintes artigos:

* [Criar e gerenciar Cofres de Chaves com a CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
* [Criar e gerenciar Cofres das Chaves com o PowerShell](key-vault-get-started.md)
* [Criar e gerenciar Cofres das Chaves com a CLI](key-vault-manage-with-cli.md)
* [Criar um cofre de chaves e adicionar um segredo por meio de um modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Operações em relação a cofres de chaves são autenticadas por meio do AAD e autorizadas por meio da própria política de acesso do Cofre da Chave, definido por cofre.

## <a name="coding-with-key-vault"></a>Codificação com o Cofre da Chave

O sistema de gerenciamento do Cofre de Chaves para programadores consiste em várias interfaces, com o REST como base, [Referência da API REST do Cofre de Chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Você pode, se tiver autorização, realizar uma das seguintes operações.

* Gerenciar chaves de criptografia usando [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações
* Gerenciar segredos usando [Obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações
* Usar chaves de criptografia com as operações [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Os seguintes SDKs estão disponíveis para funcionar com o Cofre da Chave:

| [![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx) | [![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
|:---:|:---:|
| [Documentação do SDK do .NET](https://msdn.microsoft.com/library/mt765854.aspx) |[Documentação do SDK do Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
| [Pacote do SDK do .NET no NuGet](http://www.nuget.org/packages/Microsoft.Azure.KeyVault) |[Pacote do SDK do Node.js](https://www.npmjs.com/package/azure-keyvault) |

Para obter mais informações sobre a versão 2.x do SDK do .NET, consulte as [Notas de versão](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Código de exemplo

Para obter exemplos completos sobre como usar o Cofre de Chaves com seus aplicativos, confira:

* Aplicativo de exemplo .NET *HelloKeyVault* e um exemplo de serviço Web do Azure. [Exemplos de código de Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45343)
* Tutorial para ajudá-lo a saber como usar o Cofre da Chave do Azure por meio de um aplicativo Web no Azure. [Usar cofre da chave do Azure em um aplicativo Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Instruções

Os artigos e cenários a seguir fornecem diretrizes específicas da tarefa para trabalhar com o Azure Key Vault:

* [Alterar a ID de locatário do cofre de chaves depois de mover a assinatura](key-vault-subscription-move-fix.md) - quando você mover sua assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes não poderão ser acessados pelas entidades (usuários e aplicativos) no locatário B. Corrija isso usando este guia.
* [Acessando o Cofre de Chaves por trás do firewall](key-vault-access-behind-firewall.md) - para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
* [Como gerar e transferir chaves protegidas pelo HSM para o Cofre de Chaves do Azure](key-vault-hsm-protected-keys.md) - isso vai ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas pelo HSM a serem usadas com o Cofre de Chaves do Azure.
* [Como transmitir valores seguros (como senhas) durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.
* [Como usar o Cofre de Chaves para o gerenciamento extensível de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector do SQL Server para o Cofre de Chaves do Azure permite que o SQL Server e o SQL em uma VM utilizem o serviço do Cofre de Chaves do Azure como um provedor EKM (gerenciamento extensível de chaves) para proteger suas chaves de criptografia para o vínculo de aplicativos; Transparent Data Encryption, Criptografia de Backup e Criptografia de Nível de Coluna.
* [Como implantar certificados em VMs do Cofre de Chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
* [Configurar o Key Vault com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – Explica como configurar a auditoria e a rotação de chaves com o Azure Key Vault.
* [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Implantando o certificado de aplicativo Web do Azure por meio do Key Vault) fornece instruções passo a passo para implantar certificados armazenados no Key Vault como parte da oferta do [Certificado do Serviço de Aplicativo](https://azure.microsoft.com/en-us/blog/internals-of-app-service-certificate/).
* [Grant permission to many applications to access a key vault](key-vault-group-permissions-for-apps.md) (Conceder permissão a muitos aplicativos para acessar um Key Vault) A política de controle de acesso do Key Vault permite apenas 16 entradas. No entanto, você pode criar um grupo de segurança do Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda a esse grupo de segurança acesso ao Key Vault.

Para obter mais diretrizes específicas da tarefa sobre como integrar e usar os Cofres de Chaves com o Azure, consulte os [Exemplos de modelo do Azure Resource Manager do Ryan Jones para o Cofre de Chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Introdução ao Cofre de Chaves

Estes artigos abordam outros cenários e serviços que usam ou se integram ao Key Vault.

* A [Azure Disk Encryption](../security/azure-security-disk-encryption.md) aproveita o recurso padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.
* O [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção de criptografia dos dados que são armazenados na conta. Em relação ao gerenciamento de chaves, o Data Lake Store fornece dois modos para gerenciar suas chaves-mestras de criptografia (MEKs), que são necessárias para descriptografar os dados armazenados no Data Lake Store. Você também pode deixar o Data Lake Store gerenciar as MEKs para você ou optar por manter a propriedade das MEKs usando sua conta do Cofre de Chaves do Azure. Você pode especificar o modo de gerenciamento de chaves ao criar uma conta do Data Lake Store. 
* [Proteção de Informações do Azure](/information-protection/plan-design/plan-implement-tenant-key) permite a você gerenciar sua própria chave de locatário. Por exemplo, em vez de a Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciá-la para cumprir os regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como "traga sua própria chave", ou BYOK.


## <a name="supporting-libraries"></a>Bibliotecas de Suporte

* A [Biblioteca Principal do Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece as interfaces **IKey** e **IKeyResolver** para localizar chaves com base em identificadores e realizar operações com chaves.
* As [Extensões do Cofre de Chaves do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem recursos estendidos para o Cofre de Chaves do Azure.

## <a name="other-key-vault-resources"></a>Outros recursos do Cofre de Chaves

* [Blog do Cofre de Chaves](http://aka.ms/kvblog)
* [Fórum do Cofre de Chaves](http://aka.ms/kvforum)

