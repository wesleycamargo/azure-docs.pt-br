---
title: "Solucionar problemas das Migrações para Azure | Microsoft Docs"
description: "Fornece uma visão geral dos problemas conhecidos no serviço de Migrações para Azure e dicas de solução de erros comuns."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 1fcc9e12e63eda73d53ae2085bc2a64d31ea2067
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="troubleshoot-azure-migrate"></a>Solucionar problemas das Migrações para Azure

## <a name="troubleshoot-common-errors"></a>Solução de problemas comuns

As [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure. Use este artigo para solucionar problemas ao implementar e usar as Migrações para Azure.


**O coletor não é capaz de se conectar à internet**

Isso pode acontecer quando a máquina que você está usando estiver atrás de um proxy. Certifique-se de fornecer as credenciais de autorização se precisar de um proxy.
Se você estiver usando qualquer proxy firewall baseado em URL para controlar a conectividade de saída, certifique-se de permitir estes URLs exigidos:

**URL** | **Finalidade**  
--- | ---
*. portal.azure.com | É necessário verificar a conectividade com o serviço do Azure e validar problemas sincronização de hora.
*. oneget.org | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI.

**O coletor não consegue se conectar ao projeto usando a ID e a chave do projeto copiados do portal.**

Confira se você copiou e colou as informações corretas. Para solucionar o problema, instale o Microsoft Monitoring Agent (MMA) da seguinte maneira:

1. Na VM do coletor, baixe o [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Para iniciar a instalação, clique duas vezes no arquivo baixado.
3. Na instalação, na página **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
4. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
5. Em **Opções de Instalação do Agente**, selecione **Azure Log Analytics (OMS)** > **Avançar**.
6. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do projeto e a chave que você copiou. Em seguida, clique em **Próximo**.
7. Verifique se o agente pode se conectar ao projeto. Se não for possível, verifique as configurações. Se o agente pode se conectar, mas não o coletor, contate o suporte.


**Erro 802: recebo um erro de sincronização de data e hora.**

O relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute w32tm /tz.
3. Para sincronizar a hora, execute w32tm /resync.

**Minha chave de projeto tem símbolos “==” até o fim. Esses são codificados com outros caracteres alfanuméricos pelo coletor. Isso é esperado?**

Sim, todas as chaves de projeto terminam com "==". O coletor criptografa a chave de projeto antes de processá-la.

**Os dados de desempenho para discos e adaptadores de rede mostram como zeros**

Isso poderá ocorrer se o nível de configuração de estatísticas no servidor do vCenter estiver definido como menos de três. No nível três ou superior, o vCenter armazena o histórico de desempenho da VM para computação, armazenamento e rede. Para menos do que o nível três, o vCenter não armazena dados de armazenamento e rede, mas somente dados de CPU e memória. Nesse cenário, os dados de desempenho são exibidos como zero nas Migrações para Azure e as Migrações para Azure fornecem recomendações de tamanho para discos e redes com base nos metadados coletados nas máquinas locais.

Para habilitar a coleta de dados de desempenho de disco e rede, altere o nível de configurações de estatísticas para três. Em seguida, aguarde pelo menos um dia para descobrir o seu ambiente e avaliá-lo. 

**Eu instalei agentes e usei a visualização de dependência para criar grupos. Agora, após o failover, as máquinas mostram a ação de "Instalar o agente" em vez de "Exibir dependências"**
* O failover pós-planejado ou não planejado, as máquinas locais são desativadas e as máquinas equivalentes são criadas no Azure. Essas máquinas adquirem um endereço MAC diferente. Elas podem adquirir um endereço IP diferente com base em se o usuário optou por reter o endereço IP local ou não. Se os endereços IP e MAC forem diferentes, as Migrações para Azure não associarão as máquinas locais com os dados de dependência do Mapa do Serviço e solicitarão que o usuário instale os agentes em vez de exibir as dependências.
* Após o failover de teste, as máquinas locais permanecem ativadas conforme o esperado. As máquinas equivalentes criadas no Azure adquirem um endereço MAC diferente e podem adquirir um endereço IP diferente. A menos que o usuário bloqueie o tráfego de OMS de saída dessas máquinas, as Migrações para Azure não associarão as máquinas locais com os dados de dependência do Mapa do Serviço e solicitarão que o usuário instale os agentes em vez de exibir as dependências.


## <a name="troubleshoot-readiness-issues"></a>Solucionar problemas de preparação

**Problema** | **Correção**
--- | ---
Tipo de inicialização sem suporte | Altere para BIOS antes de executar uma migração.
A contagem de disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | Reduza discos para menos de 4 TB antes da migração. 
O disco não está disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
O disco não está disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo. 
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo adequado de VM. Reduza a memória e o número de núcleos da máquina local antes de migrar. 
Um ou mais discos inadequados. | Verifique se os discos locais têm 4 TB ou menos de executar uma migração.
Um ou mais adaptadores de rede inadequados. | Remova os adaptadores de rede não utilizados do computador antes da migração.
Não foi possível determinar a adequação da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo. 
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
A VM não foi encontrada para o desempenho de armazenamento necessário. | O desempenho do armazenamento (IOPS/taxa de transferência) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
A VM não foi encontrada para o desempenho de rede necessário. | O desempenho de rede (entrada/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede para a máquina. 
A VM não foi encontrada para a camada de preços especificada. | Verifique as configurações do tipo de preço. 
A VM não foi encontrada no local especificado. | Use um local de destino diferente antes da migração.
Problemas de suporte do SO Linux | Verifique se você está executando em 64 bits com este [sistemas operacionais](../virtual-machines/linux/endorsed-distros.md) com suporte.
Problemas de suporte do SO Windows | Verifique se você está executando um sistema operacional com suporte. [Saiba mais](concepts-assessment-calculation.md#azure-suitability-analysis)
Sistema operacional desconhecido. | Verifique se o sistema operacional especificado no vCenter está correto e repita o processo de descoberta.
Exige uma assinatura do Visual Studio. | Os sistemas operacionais do cliente Windows têm suporte apenas nas assinaturas do Visual Studio (MSDN).


## <a name="collect-logs"></a>Coletar logs

**Como faço para coletar logs na VM do coletor?**

O registro em logs é habilitado por padrão. Os logs estão localizados da seguinte maneira:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Para coletar Rastreamento de Eventos para Windows, faça o seguinte:

1. Na VM do coletor, abra uma janela de comando do PowerShell.
2. Execute **Aplicativo Get-EventLog -LogName | export-csv eventlog.csv**.

**Como faço para coletar logs de tráfego da rede do portal?**

1. Abra o navegador e navegue e faça logon [no portal](https://portal.azure.com).
2. Pressione F12 para iniciar as Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação**.
3. Clique na guia **Rede** e inicie a captura do tráfego de rede:
 - No Chrome, selecione **Preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se não for exibido, clique no círculo preto para iniciar
 - No Edge/IE, a gravação deve ser iniciada automaticamente. Se não estiver, clique no botão verde para executar.
4. Tente reproduzir o erro.
5. Depois que você tiver encontrado o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade registrada:
 - No Chrome, clique com o botão direito e clique em **Salvar como HAR com conteúdo**. Isso compacta e exporta os logs como um arquivo .har.
 - No IE/Edge, clique no ícone **Exportar tráfego capturado**. Isso compacta e exporta o log.
6. Navegue até a guia **Console** para verificar se há avisos ou erros. Para salvar o log do console:
 - No Chrome, clique com o botão direito em qualquer lugar no log do console. Selecione **Salvar como**, para exportar e compactar o log.
 - No Edge/IE, clique com o botão direito nos erros e selecione **Copiar tudo**. 
7. Fechar as Ferramentas para Desenvolvedores.
 



