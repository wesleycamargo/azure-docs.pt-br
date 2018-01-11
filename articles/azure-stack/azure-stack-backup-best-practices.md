---
title: "Práticas recomendadas do serviço de Backup infraestrutura para a pilha do Azure | Microsoft Docs"
description: "Você pode seguir o conjunto de práticas recomendadas quando você implanta e gerencia a pilha do Azure em seu data center para ajudar a reduzir a perda de dados se houver uma falha catastrófica."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2017
ms.author: mabrigg
ms.openlocfilehash: b9438f3bab92f40a5c79ce7b7a572195c182be45
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>Práticas recomendadas do serviço de Backup de infraestrutura

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode seguir as práticas recomendadas quando você implanta e gerencia a pilha do Azure em seu data center para ajudar a reduzir a perda de dados no caso de uma falha catastrófica.

Você deve revisar as práticas recomendadas em intervalos regulares para verificar que a instalação ainda está em conformidade quando forem feitas alterações ao fluxo de operação. Você deve encontrar algum problema durante a implementação dessas práticas recomendadas, contate o Microsoft Support para obter ajuda.

## <a name="configuration-best-practices"></a>Práticas recomendadas de configuração

### <a name="deployment"></a>Implantação

Habilite o Backup de infraestrutura após a implantação de cada nuvem de pilha do Azure. Usando ferramentas de AzureStack você pode agendar backups de qualquer cliente ou servidor com acesso para o ponto de extremidade de API de gerenciamento do operador.

### <a name="networking"></a>Rede

A cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho deve usar um nome de domínio totalmente qualificado (FQDN). Endereço IP é possível se a resolução de nome não é possível. Uma cadeia de caracteres UNC Especifica o local de recursos, como arquivos compartilhados ou dispositivos.

### <a name="encryption"></a>Criptografia

A chave de criptografia é usada para criptografar os dados de backup que são exportados para o armazenamento externo. A chave pode ser gerada usando ferramentas de AzureStack. 

![Ferramentas de AzureStack](media\azure-stack-backup\azure-stack-backup-encryption1.png)

A chave deve ser armazenada em um local seguro (por exemplo, público segredo do Cofre de chaves do Azure). Essa chave deve ser usada durante a reimplantação da pilha do Azure. 

![Armazenado a chave de um local seguro.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Práticas recomendadas operacionais

### <a name="backups"></a>Backups

 - Controlador de infraestrutura de Backup deve ser disparado sob demanda. Recomendação é fazer o backup pelo menos duas vezes por dia.
 - Trabalhos de backup executada enquanto o sistema está em execução, portanto não há nenhum tempo de inatividade para experiências de gerenciamento ou aplicativos de usuário. Esperar que os trabalhos de backup deve levar 20 a 40 minutos para uma solução que está sob carga razoável.
 - Usando fornecido instruções do OEM, comutadores de rede manualmente backup e o host de ciclo de vida de hardware (HLH) devem ser armazenados no mesmo compartilhamento de backup onde o controle de repositórios de controlador Backup infraestrutura plano dados de backup. É recomendável armazenar o comutador e configurações de HLH na pasta de região. Se você tiver várias instâncias de pilha do Azure na mesma região, considere o uso de um identificador para cada configuração pertence a uma unidade de escala.

### <a name="folder-names"></a>Nomes de pasta

 - Infraestrutura cria a pasta MASBACKUP automaticamente. Este é um compartilhamento de gerenciada pela Microsoft. Você pode criar compartilhamentos no mesmo nível como MASBACKUP. Não é recomendável criar pastas ou armazenamento de dados dentro de MASBACKUP não criar a pilha do Azure. 
 -  FQDN do usuário e região no nome da pasta para diferenciar os dados de backup de nuvens diferentes. O nome de domínio totalmente qualificado (FQDN) de sua implantação de pilha do Azure e os pontos de extremidade é a combinação do parâmetro região e o parâmetro de nome de domínio externo. Para obter mais informações, consulte [integração de datacenter do Azure pilha - DNS](azure-stack-integrate-dns.md).

Por exemplo, o compartilhamento de backup é AzSBackups hospedado em fileserver01.contoso.com. No compartilhamento de arquivos pode ser uma pasta por implantação da pilha do Azure usando o nome de domínio externo e uma subpasta que usa o nome da região. 

FQDN: contoso.com  
Região: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Pasta MASBackup é onde a pilha do Azure armazena seus dados de backup. Você não deve usar esta pasta para armazenar seus próprios dados. OEM não deve usar esta pasta para armazenar dados de backup ou. 

Os OEMs são encorajados para armazenar dados de backup para os componentes na pasta de região. Cada comutadores de rede, host de ciclo de vida de hardware (HLH) e assim por diante podem ser armazenados em sua própria subpasta. Por exemplo: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitoramento

Os seguintes alertas são suportados pelo sistema:

| Alerta                                                   | DESCRIÇÃO                                                                                     | Correção                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Falha no backup porque o compartilhamento de arquivos estiver fora da capacidade | Compartilhamento de arquivos estiver fora da capacidade e o controlador de backup não é possível exportar arquivos de backup para o local. | Adicionar mais capacidade de armazenamento e tente fazer backup novamente. Exclua os backups existentes (a partir do mais antigo primeiro) para liberar espaço.                    |
| O backup falhou devido a problemas de conectividade.             | Rede entre a pilha do Azure e o arquivo de compartilhamento está enfrentando problemas.                          | Resolver o problema de rede e tente o backup novamente.                                                                                            |
| Falha devido a uma falha no caminho de backup                | O caminho de compartilhamento de arquivo não pode ser resolvido                                                          | Mapear o compartilhamento de um computador diferente para garantir que o compartilhamento está acessível. Talvez seja necessário atualizar o caminho se ele não é mais válido.       |
| Falha no backup devido a problemas de autenticação               | Pode haver um problema com as credenciais ou um problema de rede que afeta a autenticação.    | Mapear o compartilhamento de um computador diferente para garantir que o compartilhamento está acessível. Talvez seja necessário atualizar as credenciais se eles não são mais válidos. |
| Falha no backup devido a uma falha geral                    | A solicitação com falha pode ser devido a um problema intermitente. Tente fazer backup novamente.                    | Contate o suporte                                                                                                                               |

## <a name="next-steps"></a>Próximas etapas

 - Examine o material de referência para o [infraestrutura de serviço de Backup](azure-stack-backup-reference.md).  
 - Habilitar o [infraestrutura de serviço de Backup](azure-stack-backup-enable-backup-console.md).