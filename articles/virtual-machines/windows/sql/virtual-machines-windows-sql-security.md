---
title: "Considerações de segurança para o SQL Server no Azure | Microsoft Docs"
description: "Este tópico refere-se a recursos criados com o modelo de implantação clássica e fornece orientação geral para proteger o SQL Server em execução em uma Máquina Virtual do Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 12df603e22c8c735bf5c3f892575e79725f7313e


---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure
Este tópico inclui diretrizes gerais de segurança que ajudam a estabelecer acesso seguro a instâncias do SQL Server em uma VM do Azure. No entanto, para garantir melhor proteção às instâncias de banco de dados do SQL Server no Azure, recomendamos que você implemente as práticas de segurança tradicionais locais, além das práticas recomendadas de segurança do Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Para obter mais informações sobre as práticas de segurança do SQL Server, consulte [Práticas Recomendadas de Segurança do SQL Server 2008 R2 - Tarefas Operacionais e Administrativas](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

O Azure está em conformidade com várias normas e padrões do setor que permitem criar uma solução compatível com o SQL Server em execução em uma máquina virtual. Para obter informações sobre conformidade regulamentar com o Azure, consulte a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).

Esta é uma lista de recomendações de segurança que deve ser considerada ao configurar e conectar-se à instância do SQL Server em uma VM do Azure.

## <a name="considerations-for-managing-accounts"></a>Considerações para gerenciamento de contas:
* Crie uma conta de administrador local exclusiva que não esteja nomeada como **Administrador**.
* Use senhas fortes e complexas para todas as suas contas. Para saber mais sobre como criar uma senha forte, veja o artigo [Dicas para criar senhas fortes](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password).
* Por padrão, o Azure seleciona a autenticação do Windows durante a instalação da máquina Virtual do SQL Server. Portanto, o logon **SA** é desabilitado e uma senha é atribuída pela instalação. É recomendável que o logon **SA** não seja usado nem habilitado. A seguir, algumas estratégias alternativas caso você queira um logon do SQL:
  
  * Crie uma conta do SQL que tenha associação a sysadmin.
  * Se você precisar usar um logon **SA** , habilite o logon, renomeie-o e atribua uma nova senha.
  * As opções mencionadas anteriormente exigem uma alteração do modo de autenticação para **Modo de Autenticação do SQL Server e do Windows**. Para obter mais informações, consulte [Alterar Modo de Autenticação do Servidor](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considerações sobre conexões seguras à máquina Virtual do Azure:
* Considere o uso da [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) para administrar máquinas virtuais, em vez de portas RDP públicas.
* Use um [NSG](../../../virtual-network/virtual-networks-nsg.md) (grupo de segurança de rede) para permitir ou negar tráfego de rede à sua máquina virtual. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para saber mais sobre como fazer isso, confira [Gerenciando listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Se você está usando pontos de extremidade, remova quaisquer pontos de extremidade da máquina virtual, se não for usá-los. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](../../virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).
* Habilite uma opção de conexão criptografada para uma instância do mecanismo de banco de dados do SQL Server em máquinas virtuais do Azure. Configure a instância do SQL Server com um certificado assinado. Para saber mais, veja [Enable Encrypted Connections to the Database Engine](https://msdn.microsoft.com/library/ms191192.aspx) (Habilitar conexões criptografadas para o mecanismo de banco de dados) e [Sintaxe da cadeia de conexão](https://msdn.microsoft.com/library/ms254500.aspx).
* Se as máquinas virtuais forem acessadas somente de uma rede específica, use o Firewall do Windows para restringir o acesso a determinados endereços IP ou sub-redes da rede.

## <a name="next-steps"></a>Próximas etapas
Se você também estiver interessado em práticas recomendadas sobre desempenho, veja [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para ver outros tópicos relacionados à execução do SQL Server em VMs do Azure, confira [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).




<!--HONumber=Jan17_HO2-->


