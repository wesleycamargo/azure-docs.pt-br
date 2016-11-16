---
title: "Alertas de segurança por Tipo na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a compreender os tipos de alertas de segurança disponíveis na Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4ddc691f4f3d53dbff2e55b7a97446427b93a8a4


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Alertas de Segurança por Tipo na Central de Segurança do Azure
Este documento ajuda-o a compreender os diferentes tipos de alertas de segurança disponíveis na Central de Segurança do Azure. Leia [Gerenciamento e resposta aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como gerenciar os alertas.

> [!NOTE]
> Para habilitar as detecções avançadas, atualize para o Padrão da Central de Segurança do Azure. Há uma avaliação gratuita de 90 dias disponível. Para atualizar, selecione a Camada de Preços na [Política de Segurança](security-center-policies.md). Consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) para saber mais.
> 
> 

## <a name="what-type-of-alerts-are-available"></a>Que tipo de alerta está disponível?
A Central de Segurança do Azure fornece vários alertas que se alinham com os estágios da cadeia cyber kill. A figura a seguir fornece alguns exemplos de vários alertas quando eles se relacionam com alguns desses estágios.

![Eliminar cadeia](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**Destino e Ataque**

* Ataques RDP/SSH de entrada
* Ataques de DDoS e aplicativo (parceiros WAF)
* Detecção de intrusão (parceiros NG Firewall)

**Instalar e Explorar**

* Assinaturas de malware conhecidas (parceiros AM)
* Tentativas de malware e exploração na memória
* Execução de processo suspeita
* Manobras evasivas para evitar a descoberta
* Movimento lateral
* Reconhecimento interno
* Atividade suspeita do PowerShell

**Violação de Postagem**  

* Comunicação com um IP mal-intencionado conhecido (extração de dados ou comando e controle)
* Usando recursos comprometidos para montar ataques adicionais (ataques de força bruta RDP/SSH para verificar porta de saída e spam)

Diferentes tipos de ataques são associados a cada estágio e eles se destinam a diferentes subsistemas. Para endereçar os ataques durante os estágios, a Central de Segurança tem três categorias de alertas:

* Análise de Comportamento da Máquina Virtual (VMBA)
* Análise de Rede
* Análise de Recursos

## <a name="virtual-machine-behavioral-analysis"></a>Análise de comportamento da máquina virtual
A Central de Segurança do Azure pode usar a análise de comportamento para identificar os recursos comprometidos com base na análise dos logs de evento da máquina virtual, por exemplo: Eventos de Criação do Processos, Eventos de Logon etc.). Além disso, há uma correlação com outros sinais para verificar se há suporte a evidências de uma campanha generalizada.

> [!NOTE]
> Para saber mais sobre como funciona os recursos de detecção da Central de Segurança, leia [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).
> 
> 

### <a name="crash-analysis"></a>Análise de falha
A análise do despejo de memória é um método usado para detectar um malware sofisticado capaz de escapar das soluções de segurança tradicionais. Várias formas de malware tentam reduzir a chance de serem detectadas por produtos antivírus nunca gravando no disco ou criptografando os componentes de software gravados no disco. Isso torna o malware difícil de detectar usando as abordagens tradicionais antimalware. No entanto, tal malware pode ser detectado usando a análise de memória, pois o malware deve deixar rastreamentos na memória para funcionar.

Quando o software falha, um despejo de memória captura uma parte da memória no momento da falha. A falha pode ser causada por malware, problemas gerais do aplicativo ou do sistema. Analisando a memória no despejo de falha, a Central de Segurança pode detectar as técnicas usadas para explorar as vulnerabilidades no software, acessar os dados confidenciais e manter-se de maneira furtiva em uma máquina comprometida. Isso é feito com um mínimo de impacto no desempenho para os hosts quando a análise é executada pelo back-end da Central de Segurança.

Os campos abaixo são comuns a todos os alertas de análise do despejo de falha listados:

* DUMPFILE: nome do arquivo de despejo
* PROCESSNAME: nome do processo de falha
* PROCESSVERSION: versão do processo de falha

### <a name="shellcode-discovered"></a>Shellcode descoberto
Shellcode é a carga executada depois que o malware explorou uma vulnerabilidade do software. Esse alerta indica que a análise do despejo de memória detectou código executável com comportamento normalmente executado por cargas mal-intencionadas. Embora software que não seja mal-intencionado possa executar esse comportamento, não é comum às práticas de desenvolvimento de software normal.

Esse alerta fornece o seguinte campo adicional:

* ADDRESS: o local do shellcode na memória

Eis um exemplo desse tipo de alerta:

![Alerta de shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png) 

### <a name="module-hijacking-discovered"></a>Descoberta de sequestro do módulo
O Windows se baseia em DLLs (bibliotecas de vínculo dinâmico) para permitir que o software use funcionalidades comuns de sistema do Windows. O sequestro de DLL ocorre quando o malware altera a ordem de carregamento de DLL para carregar cargas maliciosas na memória, onde o código arbitrário pode ser executado. O alerta indica que a análise de despejo de memória detectou um módulo de nome semelhante carregado de dois caminhos diferentes, e que um dos caminhos carregados vem de um local de binários de sistema comum do Windows.

Os desenvolvedores de software legítimos ocasionalmente alteram a ordem de carregamento de DLL por motivos lícitos, por exemplo, para instrumentação, extensão do sistema operacional Windows ou de aplicativos do Windows. Para ajudar a diferenciar as alterações mal-intencionadas das bem-intencionadas na ordem de carregamento de DLL, a Central de Segurança do Azure verifica se um módulo carregado se encaixa em um perfil suspeito. O resultado dessa verificação é indicado pelo campo "ASSINATURA" do alerta e refletido na gravidade, na descrição e nas etapas de solução do alerta. A análise da cópia em disco no módulo de sequestro, como a verificação da assinatura digital dos arquivos ou a execução de uma verificação antivírus, pode fornecer mais informações sobre a natureza legítima ou mal-intencionada desse módulo.

Além dos campos comuns descritos na seção "Código descobertos" acima, esse alerta fornece os seguintes campos:

* SIGNATURE: indica se o módulo de sequestro se encaixa em um perfil de comportamento suspeito
* HIJACKEDMODULE: o nome do módulo do sistema Windows sequestrado
* HIJACKEDMODULEPATH: caminho do módulo do sistema Windows sequestrado
* HIJACKINGMODULEPATH: o caminho do módulo de sequestro

Eis um exemplo desse tipo de alerta:

![Alerta de sequestro de módulo](./media/security-center-alerts-type/security-center-alerts-type-fig3.png) 

### <a name="masquerading-windows-module-detected"></a>Módulo do Windows simulado detectado
Malware pode usar nomes comuns de binários (por exemplo, SVCHOST.EXE) ou módulos (por exemplo, NTDLL. DLL) do sistema Windows para "se misturar" e ocultar a natureza de software mal-intencionado dos administradores de sistema. O alerta indica que a análise de despejo de memória detectou que o arquivo de despejo de memória contém módulos que usam nomes de módulo do sistema Windows, mas não atendem a outros critérios típicos de módulos do Windows. A análise da cópia em disco do módulo simulado pode fornecer mais informações sobre a natureza legítima e mal-intencionada do módulo. A análise pode incluir:

* A confirmação de que o arquivo em questão é enviado como parte de um pacote de software legítimo
* A verificação da assinatura digital do arquivo
* A execução de uma varredura antivírus no arquivo

Além dos campos comuns descritos na seção "Shellcode descoberto" acima, o alerta fornece os seguintes campos adicionais:

* DETAILS: descreve se os metadados dos módulos são válidos e se o módulo foi carregado de um caminho do sistema.
* NAME: o nome do módulo do Windows simulado
* PATH: o caminho do módulo do Windows simulado.

Esse alerta também extrai e exibe determinados campos do cabeçalho do módulo, como "CHECKSUM" e "TIMESTAMP". Esses campos serão exibidos somente se os campos estiverem presentes no módulo. Confira a [Especificação Microsoft PE e COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) para obter detalhes sobre esses campos.

Eis um exemplo desse tipo de alerta:

![Alerta do Windows simulado](./media/security-center-alerts-type/security-center-alerts-type-fig4.png) 

### <a name="modified-system-binary-discovered"></a>Binário do sistema modificado descoberto
Malware pode modificar os principais binários do sistema para acessar dados secretamente ou persistir furtivamente em um sistema comprometido. O alerta indica que a análise de despejo de memória detectou binários principais do sistema operacional Windows que foram modificados na memória ou no disco.
Os desenvolvedores de software legítimos ocasionalmente modificam módulos do sistema na memória por motivos lícitos, por exemplo, para desvios ou para compatibilidade de aplicativos. Para ajudar a diferenciar módulos bem-intencionadas de mal-intencionados, a Central de Segurança do Azure verifica se um módulo alterado se encaixa em um perfil suspeito. O resultado dessa verificação é indicado pela severidade, pela descrição e pelas etapas de solução do alerta.

Além dos campos comuns descritos na seção "Shellcode descoberto" acima, o alerta fornece os seguintes campos adicionais:

* MODULENAME: nome do binário do sistema modificado
* MODULEVERSION: versão do binário do sistema modificado

Eis um exemplo desse tipo de alerta:

![Alerta de binário do sistema](./media/security-center-alerts-type/security-center-alerts-type-fig5.png) 

### <a name="suspicious-process-executed"></a>Processo suspeito executado
A Central de Segurança identifica um processo suspeito em execução na máquina virtual de destino e dispara um alerta. A detecção não procura o nome específico, mas seu parâmetro, portanto, mesmo que o invasor renomeia o executável, a Central de Segurança ainda será capaz de detectar.

Eis um exemplo desse tipo de alerta:

![Alerta de processo suspeito](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Várias contas de domínio consultadas
A Central de Segurança pode detectar várias tentativas para consultar as contas de domínio, sendo algo que normalmente é feito pelo invasores durante o reconhecimento da rede. Os invasores podem aproveitar essa técnica para consultar o domínio para identificar quem são os usuários, quais são as contas do administrador de domínio, quais computadores são os Controladores de Domínio e também a relação de confiança de domínio potencial com outros domínios.

Eis um exemplo desse tipo de alerta:

![Alerta de conta de vários domínios](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>Análise de Rede
A detecção de ameaças da rede da Central de Segurança funciona coletando automaticamente as informações de segurança de seu tráfego do Azure IPFIX (Internet Protocol Flow Information Export). Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças.

### <a name="suspicious-outgoing-traffic-detected"></a>Tráfego de saída suspeito detectado
Os dispositivos de rede podem ser descobertos e analisados da mesma forma que outros tipos de sistemas. Os invasores normalmente começam com verificação de porta/varredura de porta. No exemplo a seguir, há um tráfego suspeito de SSH de uma VM, que pode estar executando um ataque de força bruta SSH ou de varredura de porta contra um recurso externo.

![Alerta de tráfego de saída suspeito](./media/security-center-alerts-type/security-center-alerts-type-fig8.png) 

Esse alerta fornece informações que permitem a identificação do recurso usado para iniciar esse ataque, a máquina comprometida, o tempo de detecção, o protocolo e a porta usada. Essa folha também fornece uma lista de etapas de correção que podem ser usadas para atenuar esse problema.

### <a name="network-communication-with-a-malicious-machine"></a>Comunicação de rede com uma máquina mal-intencionada
Aproveitando os feeds de inteligência de ameaças da Microsoft, a Central de Segurança do Azure pode detectar máquinas comprometidas que estão se comunicando com endereços IP mal-intencionados, em muitos casos, um centro de comando e controle. Nesse caso, a Central de Segurança do Azure detectou que a comunicação foi feita usando o malware Pony Loader (também conhecido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![alerta de comunicação de rede](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Esse alerta fornece informações que permitem a identificação do recurso usado para iniciar esse ataque, o recurso atacado, o IP da vítima, o IP do invasor e o tempo de detecção.

[AZURE.NOTE] Endereços IP ativos foram removidos nesta captura de tela por fins de privacidade.

### <a name="possible-outgoing-denialofservice-attack-detected"></a>Possível saída do ataque de negação de serviço detectada
O tráfego de rede anormal proveniente de uma máquina virtual pode levar a Central de Segurança a disparar um tipo de ataque potencial de negação de serviço.

Eis um exemplo desse tipo de alerta:

![DOS de Saída](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Análise de Recursos
A análise de recursos da Central de Segurança foca nos serviços de PaaS, como a integração com o recurso [Detecção de Ameaças do Azure SQL Db](../sql-database/sql-database-threat-detection-get-started.md) . Com base nos resultados da análise dessas áreas, a Central de Segurança dispara um alerta relacionado aos recursos.

### <a name="potential-sql-injection"></a>Potencial injeção de SQL
A injeção de SQL é um ataque em que o código mal-intencionado é inserido em cadeias de caracteres, passadas posteriormente para uma instância do SQL Server para análise e execução. Qualquer procedimento que constrói instruções SQL deve ser revisado em busca de vulnerabilidades de injeção, pois o SQL Server executa todas as consultas sintaticamente válidas que recebe. A Detecção de Ameaças SQL usa o aprendizado de máquina, análise de comportamento e detecção de anomalias para determinar os eventos suspeitos que podem estar ocorrendo em seus Bancos de Dados SQL do Azure. Por exemplo:

* Tentativa de acesso do banco de dados por um antigo funcionário 
* Ataques de injeção de SQL 
* Acesso incomum ao banco de dados de produção de um usuário em casa

![Alerta de Potencial Injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Esse alerta fornece informações que permitem a identificação do recurso atacado, o tempo de detecção, o estado do ataque e também fornece um link para outras etapas de investigação.

### <a name="vulnerability-to-sql-injection"></a>Vulnerabilidade à Injeção de SQL
Este alerta é disparado quando um erro de aplicativo é disparado em um banco de dados que pode indicar uma possível vulnerabilidade a ataques de injeção de SQL.

![Alerta de Potencial Injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Acesso incomum de um local desconhecido
Este alerta é disparado quando um acesso de um endereço IP desconhecido foi detectado no servidor, que não foi visto no último período.

![Alerta de acesso incomum](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Confira também
Neste documento, você aprendeu sobre os diferentes tipos de alertas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Manipulação de incidente de segurança na Central de Segurança do Azure](security-center-incident.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.




<!--HONumber=Nov16_HO2-->


