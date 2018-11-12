---
title: Práticas recomendadas do serviço de Backup de infraestrutura para o Azure Stack | Microsoft Docs
description: Você pode seguir o conjunto de práticas recomendadas quando você implanta e gerencia o Azure Stack em seu datacenter para ajudar a reduzir a perda de dados se não houver uma falha catastrófica.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: ec17f6923fc1c928f24fcb762daedbaea5b688ac
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035094"
---
# <a name="infrastructure-backup-service-best-practices"></a>Práticas recomendadas do serviço de Backup de infraestrutura

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode seguir as práticas recomendadas quando você implanta e gerencia o Azure Stack em seu datacenter para ajudar a reduzir a perda de dados no caso de uma falha catastrófica.

Você deve revisar as práticas recomendadas em intervalos regulares para verificar que a instalação ainda está em conformidade quando forem feitas alterações ao fluxo de operação. Você deve encontrar problemas ao implementar essas práticas recomendadas, contate o Microsoft Support para obter ajuda.

## <a name="configuration-best-practices"></a>Práticas recomendadas de configuração

### <a name="deployment"></a>Implantação

Habilite o Backup de infraestrutura após a implantação de cada nuvem do Azure Stack. Usando o PowerShell do Azure Stack, você pode agendar backups de qualquer cliente/servidor com acesso para o ponto de extremidade de API de gerenciamento do operador.

### <a name="networking"></a>Rede

A cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho deve usar um nome de domínio totalmente qualificado (FQDN). Endereço IP é possível se a resolução de nome não for possível. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos.

### <a name="encryption"></a>Criptografia

A chave de criptografia é usada para criptografar dados de backup que são exportados para o armazenamento externo. A chave é gerada como parte da [habilitação do backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

A chave deve ser armazenada em um local seguro (por exemplo, segredo do Cofre de chaves do Azure público). Essa chave deve ser usada durante a reimplantação do Azure Stack. 

![Armazenou a chave de um local seguro.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Práticas recomendadas operacionais

### <a name="backups"></a>Backups

 - Trabalhos de backup executado enquanto o sistema está em execução, portanto, não há nenhum tempo de inatividade para experiências de gerenciamento ou aplicativos de usuário. Esperar que os trabalhos de backup deve levar 20 a 40 minutos para uma solução que está sob carga razoável.
 - Usando o OEM fornecida instruções, comutadores de rede de backup manualmente e o host de ciclo de vida de hardware (HLH) devem ser armazenados no mesmo compartilhamento de backup em que o controle de repositórios de controlador de Backup de infraestrutura do plano de dados de backup. Considere armazenar o comutador e configurações de HLH na pasta de região. Se você tiver várias instâncias do Azure Stack na mesma região, considere o uso de um identificador para cada configuração que pertence a uma unidade de escala.

### <a name="folder-names"></a>Nomes de pasta

 - Infraestrutura cria a pasta MASBACKUP automaticamente. Este é um compartilhamento de gerenciada pela Microsoft. Você pode criar compartilhamentos no mesmo nível como MASBACKUP. Não é recomendável criar pastas ou armazenamento de dados dentro de MASBACKUP não criar o Azure Stack. 
 -  FQDN do usuário e a região no nome da pasta para diferenciar os dados de backup de nuvens diferentes. O nome de domínio totalmente qualificado (FQDN) de sua implantação do Azure Stack e pontos de extremidade é a combinação do parâmetro de região e o parâmetro de nome de domínio externo. Para obter mais informações, consulte [integração de datacenter do Azure Stack - DNS](azure-stack-integrate-dns.md).

Por exemplo, o compartilhamento de backup é AzSBackups hospedado em fileserver01.contoso.com. Em compartilhamento de arquivos pode ser uma pasta por implantação do Azure Stack usando o nome de domínio externo e uma subpasta que usa o nome da região. 

FQDN: contoso.com  
Região: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Pasta MASBackup é onde o Azure Stack armazena seus dados de backup. Você não deve usar essa pasta para armazenar seus próprios dados. OEM não deve usar essa pasta para armazenar os dados de backup seja. 

Os OEMs são incentivados a armazenar dados de backup para os componentes na pasta de região. Cada comutadores de rede, host de ciclo de vida do hardware (HLH) e assim por diante podem ser armazenados em sua própria subpasta. Por exemplo: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitoramento

Os alertas a seguir têm suporte pelo sistema:

| Alerta                                                   | DESCRIÇÃO                                                                                     | Correção                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| O backup falhou porque o compartilhamento de arquivos está sem capacidade | Compartilhamento de arquivos está sem capacidade e o controlador de backup não é possível exportar arquivos de backup no local. | Adicionar mais capacidade de armazenamento e tente fazer backup novamente. Exclua os backups existentes (a partir do mais antigo primeiro) para liberar espaço.                    |
| O backup falhou devido a problemas de conectividade.             | Rede entre o Azure Stack e o arquivo de compartilhamento está com problemas.                          | Resolver o problema de rede e tente o backup novamente.                                                                                            |
| O backup falhou devido a uma falha no caminho                | O caminho do compartilhamento de arquivo não pode ser resolvido                                                          | Mapeie o compartilhamento de um computador diferente para garantir que o compartilhamento está acessível. Você talvez precise atualizar o caminho se ele não é mais válido.       |
| O backup falhou devido um problema de autenticação               | Pode haver um problema com as credenciais ou um problema de rede que afeta a autenticação.    | Mapeie o compartilhamento de um computador diferente para garantir que o compartilhamento está acessível. Você talvez precise atualizar as credenciais se eles não são mais válidos. |
| O backup falhou devido a uma falha geral                    | A solicitação com falha pode ser devido a um problema intermitente. Tente fazer backup novamente.                    | Contate o suporte                                                                                                                               |

## <a name="next-steps"></a>Próximas etapas

Examine o material de referência para o [serviço de Backup de infraestrutura](azure-stack-backup-reference.md).

Habilitar o [serviço de Backup de infraestrutura](azure-stack-backup-enable-backup-console.md).
