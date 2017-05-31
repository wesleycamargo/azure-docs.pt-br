---
ms.assetid: 
title: "Exclusão reversível do Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2b73fb55384cdcc8db2736557e0efef97b34fc45
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-soft-delete-feature-overview"></a>Visão geral do recurso de exclusão reversível do Azure Key Vault

>[!NOTE]
>Nesta versão prévia do Azure Key Vault, apenas o recurso **exclusão reversível** está em versão prévia. O Azure Key Vault, como um todo, é um serviço de produção completo.

Para esta versão prévia do Azure Key Vault, descrevemos a exclusão recuperável de Key Vaults e de objetos do Key Vault, conhecida como exclusão reversível. Especificamente, abordamos os seguintes cenários:

- Suporte à exclusão recuperável de cofres de chaves
- Suporte à exclusão recuperável de objetos do cofre de chaves (por exemplo, chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de exclusão reversível está inicialmente disponível por meio das interfaces da REST, do .NET/C# e do PowerShell. Consulte as referências deles para obter mais detalhes, [Referência do Key Vault](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Cenários

Os Azure Key Vaults são recursos controlados, gerenciados pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a exclusão, que exige que uma operação DELETE bem-sucedida faça com que esse recurso não esteja mais acessível. O recurso de exclusão reversível aborda a recuperação do objeto excluído, independentemente se a exclusão foi acidental ou intencional.

1. No cenário típico, um usuário pode ter acidentalmente excluído um cofre de chaves ou um objeto do cofre de chaves; se esse cofre de chaves ou objeto do cofre de chaves precisar ser recuperável por um período predeterminado, o usuário poderá desfazer a exclusão e recuperar seus dados.

2. Em um cenário diferente, um usuário mal-intencionado pode tentar excluir um cofre de chaves ou um objeto do cofre de chaves, como uma chave dentro de um cofre, para causar uma interrupção dos negócios. A separação da exclusão do cofre de chaves ou do objeto do cofre de chaves da exclusão real dos dados subjacentes pode ser usada como uma medida de segurança, por exemplo, restringindo as permissões de exclusão de dados a outra função confiável. Efetivamente, isso exige quorum para uma operação que, de outro modo, poderá resultar em uma perda de dados imediata.

### <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

Com essa versão prévia, a operação DELETE em um cofre de chaves ou objeto do cofre de chaves é uma exclusão reversível, efetivamente, mantendo os recursos por um período de retenção especificado, ao mesmo tempo que aparenta ter excluído o objeto. Além disso, o serviço fornece um mecanismo para recuperar o objeto excluído, basicamente, desfazendo a exclusão. 

A exclusão reversível é um comportamento opcional do Key Vault e **não está habilitado por padrão** nesta versão. Para obter detalhes sobre como habilitar a exclusão reversível em seu cofre de chaves, consulte as diretrizes específicas na referência da interface de sua escolha, [Referência do Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="key-vault-recovery"></a>Recuperação do cofre de chaves

Depois de excluir um cofre de chaves, o serviço criará um recurso de proxy na assinatura, adicionando metadados suficientes para recuperação. O recurso de proxy é um objeto armazenado, disponível na mesma localização do cofre de chaves excluído. 

### <a name="key-vault-object-recovery"></a>Recuperação de objetos do cofre de chaves

Depois de excluir um objeto do cofre de chaves, como uma chave, o serviço colocará o objeto em um estado excluído, tornando-o inacessível para todas as operações de recuperação. Nesse estado, o objeto do cofre de chaves só poderá ser listado, recuperado ou excluído por imposição ou permanentemente. 

Ao mesmo tempo, o Key Vault agendará a exclusão dos dados subjacentes correspondentes ao cofre de chaves ou objeto do cofre de chaves excluído para execução após um intervalo de retenção predeterminado. O registro DNS correspondente ao cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção da exclusão reversível

Os recursos excluídos de maneira reversível são mantidos por um período definido de tempo, 90 dias. 

>[!NOTE]
> Esse período de retenção da exclusão reversível não é configurável em nossa versão prévia de 10 de maio de 2017. 

Durante o intervalo de retenção da exclusão reversível, o seguinte se aplica:

- É possível listar todos os cofres de chaves e objetos do cofre de chaves no estado de exclusão reversível de sua assinatura, bem como informações de exclusão e recuperação de acesso sobre eles.
    - Somente usuários com permissões especiais podem listar os cofres excluídos. Recomendamos que nossos usuários criem uma função personalizada com essas permissões especiais para a manipulação dos cofres excluídos.
- Não é possível criar um cofre de chaves com o mesmo nome na mesma localização; do mesmo modo, não é possível criar um objeto do cofre de chaves em determinado cofre se esse cofre de chaves contém um objeto com o mesmo nome e que está no estado excluído 
- Somente um usuário com privilégios específicos pode restaurar um cofre de chaves ou objeto do cofre de chaves emitindo um comando de recuperação no recurso de proxy correspondente.
    - O usuário, membro da função personalizada, que tem o privilégio para criar um cofre de chaves no grupo de recursos pode restaurar o cofre.
- Somente um usuário com privilégios específicos pode excluir por imposição um cofre de chaves ou objeto do cofre de chaves emitindo um comando de exclusão no recurso de proxy correspondente.

A menos que um cofre de chaves ou objeto do cofre de chaves seja recuperado, ao final do intervalo de retenção, o serviço realizará uma limpeza do cofre de chaves ou do objeto do cofre de chaves excluídos de maneira reversível e de seu conteúdo. A exclusão de recursos não pode ser reagendada.

### <a name="permissioned-purge"></a>Limpeza com permissão

A exclusão permanente, limpeza, de um cofre de chaves é possível por meio de uma operação POST no recurso de proxy e exige privilégios especiais. Geralmente, apenas o proprietário da assinatura poderá limpar um cofre de chaves. A operação POST disparará a exclusão imediata e irrecuperável desse cofre. 

Uma exceção é quando a assinatura do Azure foi marcada como *não excluível*. Nesse caso, apenas o serviço pode executar a exclusão real e ele o fará como um processo agendado. 




