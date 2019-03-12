---
title: Dados com criptografia em repouso no Azure Stack
description: Saiba como a pilha do Azure protege seus dados com a criptografia em repouso
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769365"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Dados com criptografia em repouso no Azure Stack

O Azure Stack protege o usuário e infraestrutura de dados no nível do subsistema de armazenamento usando a criptografia em repouso. O subsistema de armazenamento do Azure Stack é criptografado usando o BitLocker com criptografia AES de 128 bits. As chaves do BitLocker são mantidas em um repositório secreto interno.

Dados com criptografia em repouso são um requisito comum para muitos dos padrões de conformidade principais (por exemplo, FedRAMP, PCI-DSS, HIPAA). O Azure Stack permite que você atenda a esses requisitos sem trabalho extra ou as configurações necessárias. Para obter mais informações sobre como o Azure Stack ajuda você a atender aos padrões de conformidade, consulte o [Portal de confiança do serviço de Microsoft](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Dados com criptografia em repouso protege seus dados contra sendo acessada por alguém que fisicamente roubar um ou mais discos rígidos. Dados com criptografia em repouso não protege contra os dados serem interceptados em geral, pela rede (dados em trânsito), os dados que está sendo usados atualmente (dados na memória) ou mais dados sendo exfiltrated enquanto o sistema está ligado e em execução.

## <a name="retrieving-bitlocker-recovery-keys"></a>Ao recuperar chaves de recuperação do BitLocker

Chaves do BitLocker de pilha do Azure para dados em repouso são gerenciadas internamente. Você não precisa fornecer a eles para operações regulares ou durante a inicialização do sistema. No entanto, os cenários de suporte podem exigir chaves de recuperação do BitLocker para colocar o sistema online.  

> [!WARNING]
> Recuperar as chaves de recuperação do BitLocker e armazená-los em um local seguro fora do Azure Stack. Não ter as chaves de recuperação durante determinados cenários de suporte pode resultar em perda de dados e exigem uma restauração do sistema de uma imagem de backup.

Recuperar as chaves de recuperação do BitLocker requer acesso à [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md) (PEP). Em uma sessão PEP, execute o cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Parâmetros opcionais para *Get-AzsRecoveryKeys* cmdlet:

| Parâmetro | DESCRIÇÃO | Type | Obrigatório |
|---------|---------|---------|---------|
|*raw* | retorna dados brutos de mapeamento entre a chave de recuperação, o nome do computador e a senha ID (s) de cada volume criptografado  | switch | Nenhum (desenvolvido para cenários de suporte)|


## <a name="troubleshoot-issues"></a>Solucionar problemas

Em circunstâncias extremas, o desbloqueio de um BitLocker solicitação poderia falhar resultando em um volume específico para não inicializado. Dependendo da disponibilidade de alguns dos componentes da arquitetura, essa falha pode resultar em tempo de inatividade e perda de dados se você não tiver as chaves de recuperação do BitLocker.

> [!WARNING]
> Recuperar as chaves de recuperação do BitLocker e armazená-los em um local seguro fora do Azure Stack. Não ter as chaves de recuperação durante determinados cenários de suporte pode resultar em perda de dados e exigem uma restauração do sistema de uma imagem de backup.

Se você suspeitar de que o sistema está enfrentando problemas com o BitLocker, como o Azure Stack Falha ao iniciar, contate o suporte. Suporte exige que as chaves de recuperação do BitLocker. A maioria do BitLocker relacionados a problemas podem ser resolvidos com uma operação de FRU para essa VM/host/volume específico. Para os outros casos, um procedimento de desbloqueio manual usando chaves de recuperação do BitLocker pode ser executado. Se as chaves de recuperação do BitLocker não estiverem disponíveis, a única opção é restaurar a partir de uma imagem de backup. Dependendo de quando o último backup foi executado, você poderá encontrar a perda de dados.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
- Para obter mais informações sobre como o BitLocker protege os CSVs, consulte [protegendo cluster shared volumes e redes de área de armazenamento com o BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).