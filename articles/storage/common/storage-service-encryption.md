---
title: Criptografia do Serviço de Armazenamento do Azure para dados em repouso | Microsoft Docs
description: Use o recurso Criptografia do Serviço de Armazenamento do Azure para criptografar o armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e descriptografá-lo ao recuperar os dados.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: 5e4df176104111f44ca95df2b2d5d1c81ed3a4e3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Criptografia do Serviço de Armazenamento do Azure para dados em repouso

A Criptografia do Serviço de Armazenamento do Azure para Dados em Repouso ajuda você a proteger seus dados para atender aos compromissos de conformidade e segurança de sua organização. Com esse recurso, o Armazenamento do Azure criptografa os dados automaticamente antes de persisti-los no Armazenamento do Azure e descriptografa os dados antes da recuperação. A manipulação de criptografia, criptografia em repouso, descriptografia e gerenciamento de chaves na Criptografia do Serviço de Armazenamento é transparente para os usuários. Todos os dados gravados no Armazenamento do Azure são criptografados por meio da [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.

A Criptografia do Serviço de Armazenamento está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar o código nem os aplicativos para aproveitar a Criptografia do Serviço de Armazenamento.

O recurso criptografa os dados automaticamente em:

- Ambos os níveis de desempenho (Standard e Premium).
- Ambos os modelos de implantação (Azure Resource Manager e clássico).
- Todos os serviços do Armazenamento do Azure (armazenamento de Blobs, armazenamento de Filas, armazenamento de Tabelas e Arquivos do Azure). 

A Criptografia do Serviço de Armazenamento não afeta o desempenho do Armazenamento do Azure.

Use chaves de criptografia gerenciadas pela Microsoft com a Criptografia do Serviço de Armazenamento ou suas próprias chaves de criptografia. Para obter mais informações sobre como usar suas próprias chaves, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Exibir as configurações de criptografia no portal do Azure

Para exibir as configurações da Criptografia do Serviço de Armazenamento, entre no [portal do Azure](https://portal.azure.com) e selecione uma conta de armazenamento. No painel **CONFIGURAÇÕES**, selecione a configuração **Criptografia**.

![Captura de tela do portal mostrando a configuração de Criptografia](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Perguntas frequentes sobre a Criptografia do Serviço de Armazenamento

**P: Tenho uma conta de armazenamento clássico. Posso habilitar a Criptografia do Serviço de Armazenamento nela?**

R: A Criptografia do Serviço de Armazenamento está habilitada por padrão para todas as contas de armazenamento (clássico e Resource Manager).

**P: Como posso criptografar os dados em minha conta de armazenamento clássica?**

R: Com a criptografia habilitada por padrão, o Armazenamento do Azure criptografa os novos dados automaticamente. 

**P: Tenho uma conta de armazenamento do Resource Manager. Posso habilitar a Criptografia do Serviço de Armazenamento nela?**

R: A Criptografia do Serviço de Armazenamento está habilitada por padrão em todas as contas de armazenamento existentes do Resource Manager. Há suporte para ela no armazenamento de Blobs, no armazenamento de Tabelas, no armazenamento de Filas e nos Arquivos do Azure. 

**P: Como fazer para criptografar os dados em uma conta de armazenamento do Resource Manager?**

R: a criptografia de serviço de armazenamento é habilitada por padrão para todas as contas de armazenamento – clássicas e o Gerenciador de Recursos, os arquivos existentes na conta de armazenamento criada antes da habilitação da criptografia serão retroativamente criptografados por um processo de criptografia de plano de fundo.

**P: Posso criar contas de armazenamento com a Criptografia do Serviço de Armazenamento habilitada usando o Azure PowerShell e a CLI do Azure?**

R: A Criptografia do Serviço de Armazenamento está habilitada por padrão no momento da criação de qualquer conta de armazenamento (clássico ou Resource Manager). Verifique as propriedades da conta usando o Azure PowerShell e a CLI do Azure.

**P: Qual é o custo adicional do Armazenamento do Azure se a Criptografia do Serviço de Armazenamento é habilitada?**

R: Não há qualquer custo adicional.

**P: Posso usar minhas próprias chaves de criptografia?**

A: Sim, você pode usar suas próprias chaves de criptografia. Para obter mais informações, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**P: Posso revogar o acesso às chaves de criptografia?**

R: Sim, se você [usar suas próprias chaves de criptografia](storage-service-encryption-customer-managed-keys.md) no Azure Key Vault.

**P: A Criptografia do Serviço de Armazenamento está habilitada por padrão quando crio uma conta de armazenamento?**

R: Sim, a Criptografia de Serviço de Armazenamento é habilitada por padrão para todas as contas de armazenamento e para todos os serviços de Armazenamento do Azure.

**P: Qual é a diferença para o Azure Disk Encryption?**

R: O Azure Disk Encryption é usado para criptografar os discos do sistema operacional e de dados em VMs IaaS. Para obter mais informações, consulte o [Guia de segurança de armazenamento](../storage-security-guide.md).

**P: E se eu habilitar o Azure Disk Encryption em meus discos de dados?**

R: Isso funcionará perfeitamente. Ambos os métodos criptografarão os dados.

**P: Minha conta de armazenamento está configurada para ser replicada de forma geograficamente redundante. Com a Criptografia do Serviço de Armazenamento, minha cópia redundante também será criptografada?**

R: Sim, todas as cópias da conta de armazenamento são criptografadas. Há suporte para todas as opções de redundância – armazenamento com redundância local, armazenamento com redundância de zona, armazenamento com redundância geográfica e armazenamento com redundância geográfica com acesso de leitura.

**P: Posso desabilitar a criptografia em minha conta de armazenamento?**

R: A criptografia está habilitada por padrão e não há nenhuma provisão para desabilitá-la em sua conta de armazenamento. 

**P: A Criptografia do Serviço de Armazenamento é permitida apenas em regiões específicas?**

R: A Criptografia do Serviço de Armazenamento está disponível em todas as regiões para todos os serviços. 

**P: Como fazer para contatar alguém caso eu tenha problemas ou deseje enviar comentários?**

R: Contate [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) em caso de problemas ou se tiver comentários relacionados à Criptografia do Serviço de Armazenamento.

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de funcionalidades de segurança que, juntas, ajudam os desenvolvedores a criar aplicativos seguros. Para obter mais informações, consulte o [Guia de segurança de armazenamento](../storage-security-guide.md).
