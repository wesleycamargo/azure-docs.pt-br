---
title: "Segurança do Azure Governamental | Microsoft Docs"
description: "Fornece uma visão geral dos serviços disponíveis no Azure Governamental"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>Segurança
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Princípios para a proteção de dados do cliente no Azure Governamental
O Azure Governamental oferece uma gama de recursos e serviços que você pode usar para compilar soluções de nuvem para atender às suas necessidades de dados regulamentados/controlados. Uma solução de cliente compatível é apenas a implementação eficaz de recursos originais do Azure Governamental, juntamente com uma prática de segurança de dados sólida.

Quando você hospeda uma solução no Azure Governamental, a Microsoft lida com muitos desses requisitos no nível de infraestrutura da nuvem.

O diagrama a seguir mostra com detalhes o modelo de defesa do Azure. Por exemplo, a Microsoft fornece a infraestrutura de nuvem básica DDOS, juntamente com recursos de cliente, como dispositivos de segurança para aplicativos específicos ao cliente que precisam de DDOS.

![texto alternativo](./media/azure-government-Defenseindepth.png)

Esta página descreve os princípios fundamentais para proteger seus Serviços e Aplicativos, fornecendo orientação e práticas recomendadas sobre como aplicar esses princípios; em outras palavras, como os clientes devem usar de forma inteligente o Azure Governamental para atender às obrigações e responsabilidades necessárias para uma solução que lida com informações ITAR.

 Os princípios abrangentes para a proteção de dados do cliente são:

* Proteção de dados usando a criptografia
* Gerenciamento de segredos
* Isolamento para restringir o acesso aos dados

### <a name="protecting-customer-data-using-encryption"></a>Proteção de dados do cliente usando criptografia
Redução de riscos e atendimento às obrigações regulatórias estão aumentando o foco e a importância da criptografia de dados. Use uma implementação de criptografia eficaz para aprimorar as medições atuais de segurança de rede e de aplicativo — e diminua o risco geral de seu ambiente de nuvem.

#### <a name="encryption-at-rest"></a>Criptografia em repouso
A criptografia de dados em repouso aplica-se à proteção de conteúdo do cliente mantido no armazenamento em disco. Há várias maneiras disso acontecer:

#### <a name="storage-service-encryption"></a>Criptografia do Serviço de Armazenamento
A Criptografia do Serviço de Armazenamento é habilitada no nível da conta de armazenamento, resultando na criptografia automática em blobs de blocos e blobs de páginas quando gravados no Armazenamento do Azure. Quando você lê os dados no Armazenamento do Azure, eles são descriptografados pelo serviço de armazenamento antes de serem retornados. Use isso para proteger seus dados sem precisar modificar ou adicionar o código a algum aplicativo.

#### <a name="client-side-encryption"></a>Criptografia do cliente
A criptografia do cliente é incorporada no Java e às bibliotecas de cliente de armazenamento .NET que, por sua vez, podem usar as APIs do Cofre de Chaves do Azure, o que a torna muito fácil de implementar. Use o Cofre de Chaves do Azure para obter acesso aos segredos no Cofre de Chaves do Azure para indivíduos específicos usando o Azure Active Directory.

#### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia básica disponível para conectividade com o Azure Governamental oferece suporte ao protocolo TLS (Transport Level Security) 1.2 e aos certificados X.509. Os algoritmos de criptografia de padrão FIPS 140-2 Nível 1 também são usados para conexões de rede de infraestrutura entre os datacenters do Azure Governamental.  As VMs do Windows Server 2012 R2 e do Windows 8-plus e os Compartilhamentos de Arquivos do Azure podem usar SMB 3.0 para criptografia entre a VM e o compartilhamento de arquivos. Use Criptografia do Cliente para criptografar os dados antes que eles sejam transferidos para o armazenamento em um aplicativo cliente e para descriptografar os dados após serem transferidos para fora do armazenamento.

#### <a name="best-practices-for-encryption"></a>Práticas recomendadas para criptografia
* VMs de IaaS: usar o Azure Disk Encryption. Ative a Criptografia do Serviço de Armazenamento para criptografar os arquivos VHD usados para fazer back-up dos discos no Armazenamento do Azure, mas ela só criptografa dados recém-criados. Isso significa que se você criar uma VM e habilitar a Criptografia do Serviço de Armazenamento na conta de armazenamento que mantém o arquivo VHD, somente as alterações serão criptografadas, não o arquivo VHD original.
* Criptografia do Cliente: esse é o método mais seguro para criptografar dados, pois ele os criptografa antes da transferência, além de criptografar os dados em repouso. No entanto, ela exige a adição de código aos aplicativos usando armazenamento, o que talvez não seja conveniente. Nesses casos, é possível usar HTTPs para os dados em trânsito e a Criptografia do Serviço de Armazenamento para criptografar os dados em repouso. A Criptografia do Cliente também invoca mais carga no cliente, e você deve levar isso em conta em seus planos de escalabilidade, especialmente se estiver criptografando e transferindo muitos dados.

### <a name="protecting-customer-data-by-managing-secrets"></a>Proteção de dados do cliente por meio do gerenciamento de segredos
O gerenciamento de chaves seguro é fundamental para proteger seus dados na nuvem. Os clientes devem se esforçar para simplificar o gerenciamento de chaves e manter o controle das chaves usadas por serviços e aplicativos em nuvem para criptografar dados.

#### <a name="best-practices-for-managing-secrets"></a>Práticas recomendadas para gerenciamento de segredos
* Use o Cofre de Chaves para minimizar os riscos de exposição dos segredos por meio de arquivos de configuração embutidos, scripts ou no código-fonte. O Azure Disk Encryption criptografa as chaves (como as chaves de criptografia para Criptografia de Disco do Azure) e segredos (como senhas), armazenando-as em HSM (módulos de segurança de hardware) validados por FIPS 140-2 nível 2. Para garantia extra, você pode importar ou gerar chaves nestes HSMs.
* O código de aplicativo e os modelos devem conter somente as referências de URI aos segredos (o que significa que os segredos reais não estão nos repositórios de códigos, de configurações ou de códigos-fonte). Isso impede grandes ataques de phishing em repositórios internos ou externos, como de harvest-bots no GitHub.
* Use controles RBAC fortes no Cofre de Chaves. Se um operador confiável deixar a empresa ou for transferido para um novo grupo dentro da empresa, será necessário impedir que ele possa acessar os segredos.

Para saber mais, confira a [documentação pública do Cofre de Chaves do Azure. ](../key-vault/index.md).

### <a name="isolation-to-restrict-data-access"></a>Isolamento para restringir o acesso aos dados
Isolamento se resume ao uso de limites, segmentação e contêineres para limitar o acesso aos dados somente aos usuários, serviços e aplicativos autorizados. Por exemplo, a separação entre locatários é um mecanismo de segurança essencial para plataformas de nuvem com vários locatários, como o Microsoft Azure. O isolamento lógico ajuda a impedir que um locatário interfira com as operações de qualquer outro locatário.

#### <a name="environment-isolation"></a>Isolamento de ambiente
O ambiente do Azure Governamental é uma instância física separada do restante da rede da Microsoft. Isso é obtido por meio de uma série de controles físicos e lógicos que incluem o seguinte:

* Proteção de barreiras físicas usando câmeras e dispositivos biométricos.
* Uso de credenciais específicas e autenticação multifator por funcionários da Microsoft que precisam de acesso lógico ao ambiente de produção.
* Toda a infraestrutura de serviço para o Azure Governamental está localizada nos Estados Unidos.

#### <a name="per-customer-isolation"></a>Isolamento por cliente
O Azure implementa o controle de acesso à rede e a segregação por meio do isolamento de VLAN, ACLs, filtros IP e balanceadores de carga

Os clientes podem isolar ainda mais seus recursos nas assinaturas, grupos de recursos, redes virtuais e sub-redes.

## <a name="screening"></a>Triagem
O recém-anunciado FedRAMP High e DoD (Departamento de Defesa) afeta o credenciamento de Nível 4. Isso elevou o nível da segurança e da conformidade em todo o ambiente do Azure Governamental.

Agora, estamos fazendo uma triagem de todos os nossos operadores na NACLC (National Agency Check with Law and Credit) conforme definido na seção 5.6.2.2 do SRG (Guia de Requisitos de Segurança) de computação na nuvem do DoD:

> [!NOTE]
> A investigação mínima de antecedentes necessária para que a equipe do CSP tenha acesso às informações de Nível 4 e 5 com base em "confidencial não crítico" (por exemplo, ADP-2 do DoD) é uma NACLC (National Agency Check with Law and Credit) (para prestadores de serviços "confidenciais não críticos") ou uma MIB (Moderate Risk Background Investigation) para uma designação de cargo de "risco moderado".
> 
> 

A tabela a seguir resume a nossa triagem atual para operadores do Azure Governamental:

| Verificações de atecedentes e triagens do Azure Governamental | Descrição |
| --- | --- |
| Cidadania dos EUA |Verificação de cidadania dos EUA. |
| Verificação de antecedentes na nuvem da Microsoft (a cada dois anos) |Pesquisa de número do seguro social, verificação de antecedentes criminais, lista da OFAC (Office of Foreign Assets Control), lista BIS (Bureau of Industry and Security), lista de Pessoas impedidas do Office of Defense Trade Controls. |
| NACLC (National Agency Check with Law and Credit) (a cada cinco anos) |Adiciona a verificação de antecedentes com impressão digital em bancos de dados do FBI. Para saber mais, consulte o<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> Site do Office Personnel Management</a>. |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS"> CJIS (Criminal Justice Information Services) </a> |CJIS é uma triagem governamental estadual, local e do FBI que processa registros de impressão digital e valida históricos criminais da equipe operacional que pode receber acesso a dados de informações da justiça criminal (CJI).  Cada estado realiza sua própria verificação de antecedentes e subsequente aprovação de todos os funcionários com possível acesso a CJI. |

Para a equipe de operações do Azure, os seguintes princípios de acesso se aplicam:

* As responsabilidades são claramente definidas, com responsabilidades separadas para solicitação, aprovação e implantação das alterações.
* O acesso ocorre por meio de interfaces definidas que têm funcionalidades específicas.
* O Acesso é JIT (just-in-time) e é concedido por incidente apenas ou para um evento de manutenção específico, e sempre por um período limitado.
* O Acesso tem base em regras, com funções definidas que recebem apenas as permissões necessárias para a solução do problema.

Os padrões de triagem incluem a validação da cidadania dos EUA de toda a equipe operacional e de suporte da Microsoft, antes da concessão do acesso aos sistemas hospedados pelo Azure Governamental. A equipe de suporte que precisar transferir dados usará os recursos seguros no Azure Governamental. A transferência segura de dados exige um conjunto separado de credenciais de autenticação para obtenção do acesso. Por exemplo, para acessar os metadados do sistema, a equipe de operações usa ferramentas de gerenciamento interno específicas com base na Web, APIs somente leitura e elevação de JIT.

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>




<!--HONumber=Nov16_HO3-->


