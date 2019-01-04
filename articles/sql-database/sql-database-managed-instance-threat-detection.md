---
title: Detecção de Ameaças – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: A Detecção de Ameaças detecta atividades anormais do banco de dados que indicam possíveis ameaças de segurança contra o banco de dados em uma Instância Gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: a456a214143f39ed9504af40129f9199b2535e46
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997133"
---
# <a name="azure-sql-database-managed-instance-threat-detection-preview"></a>Detecção de Ameaças da Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia)

A [Detecção de Ameaças](sql-database-threat-detection-overview.md) do SQL do Azure para a [Instância Gerenciada do Banco de Dados SQL](sql-database-managed-instance-index.yml) detecta atividades anormais que indicam tentativas potencialmente perigosas e incomuns de acessar ou explorar bancos de dados. A Detecção de Ameaças pode identificar **Possíveis injeções de SQL**, **Acesso de um data center ou localização incomum**, **Acesso de uma entidade de segurança desconhecida ou de um aplicativo potencialmente prejudicial** e **Credenciais SQL de força bruta** – veja mais detalhes em [Alertas da Detecção de Ameaças](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou do [Portal do Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

A [Detecção de Ameaças](sql-database-threat-detection-overview.md) faz parte da oferta [ATP](sql-advanced-threat-protection.md) (Proteção Avançada contra Ameaças) ao SQL, um pacote unificado de funcionalidades avançadas de segurança do SQL. A Detecção de Ameaças pode ser acessada e gerenciada por meio do portal central da ATP do SQL. O serviço de detecção de ameaças é cobrado US$ 15/mês por Instância Gerenciada, com os primeiros 30 dias gratuitos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurar a Detecção de Ameaças para a Instância Gerenciada no Portal do Azure
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da Instância Gerenciada que você quer proteger. Na página **Configurações**, selecione **Detecção de Ameaças**. 
3. Na página de configurações de Detecção de Ameaças 
   - **ATIVE** a Detecção de ameaças.
   - Configure a **lista de emails** para receber os alertas de segurança após a detecção das atividades de banco de dados anormais.
   - Selecione a **Conta de Armazenamento do Azure** onde os registros de auditoria de ameaças anormais são salvos. 
4.  Clique em **Salvar** para salvar a política de detecção de ameaças atualizada ou nova.

   ![detecção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [Detecção de Ameaças](sql-database-threat-detection-overview.md). 
- Para saber mais sobre Instância Gerenciada, confira [O que é uma Instância Gerenciada](sql-database-managed-instance.md). 
- Saiba mais sobre a [Detecção de ameaças para banco de dados individual](sql-database-threat-detection.md). 
- Saiba mais sobre a [Auditoria de Instância Gerenciada](https://go.microsoft.com/fwlink/?linkid=869430). 
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
