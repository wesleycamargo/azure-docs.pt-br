---
title: Use InSpec para a automação de conformidade de sua infraestrutura do Azure
description: Saiba como usar o InSpec para detectar problemas em implantações do Microsoft Azure
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar, inspce
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050720"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Use InSpec para a automação de conformidade de sua infraestrutura do Azure
[Inspe](https://www.chef.io/inspec/) é uma estrutura gratuita e de código aberto para teste e seus aplicativos e infraestrutura de auditoria. O Inspe funciona comparando o estado real do seu sistema com o estado desejado que você expressa no código fácil de ler e fácil de gravar do InSpec. InSpec detecta violações e exibe os resultados na forma de um relatório, mas coloca você no controle de correção. Você pode usar InSpec para validar o estado das máquinas virtuais em execução no Azure. Você também pode usar InSpec para verificar e validar o estado de recursos e grupos de recursos dentro de uma assinatura.

Este artigo descreve os benefícios de usar o InSpec para facilitar a segurança e conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Tornar a conformidade fácil de entender e avaliar
Com o InSpec, é possível transformar seus requisitos em código com controle de versão, executável, legível. Isso permite que você organize seus testes nos perfis combináveis em que você define e personaliza exceções, conforme necessário.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detectar problemas de todo o fleet e priorizar a sua correção
O InSpec sem agente detecta o modo que permite você avaliar rapidamente - em escala - seu nível de exposição. Metadados internas para classificação de severidade/impacto ajudam a determinar quais áreas de foco para correção.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Inspecionar máquinas, dados e novas APIs de SaaS
Os recursos de conformidade de API de nuvem InSpec permitem fazer asserções grossas e refinadas sobre sua conformidade de nuvem e relatório nele continuamente.

## <a name="satisfy-audits"></a>Satisfazer auditorias
Com InSpec, você pode responder às perguntas de auditoria a qualquer momento - não apenas em intervalos predeterminados como trimestral ou anual. O InSpec permite que você insira um ciclo de auditoria sabendo sua postura de conformidade exata, em vez de ter ficado surpreso com resultados de um auditor.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Reduzir a ambiguidade e problemas de comunicações com relação a regras
Os documentos deixam as configurações e processos para abrir interpretação. O código executável remove conversas sobre o que deve ser avaliado em favor de testes tangíveis com intenção de limpar.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Acompanhar a alteração rápida de cenários de ameaça e conformidade
InSpec permite que você escreva e publique o código de detecção no mesmo dia e escreva novas regras em uma resposta rápida para novas regras. Isso significa que as alterações em ameaças ou regulamentos não são iguais em caso de emergência.

## <a name="next-steps"></a>Próximas etapas
* [Criar uma Máquina Virtual do Windows no Azure usando o Chef](/azure/virtual-machines/windows/chef-automation)