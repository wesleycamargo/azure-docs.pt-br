---
title: SQL Azure com o Azure RemoteApp | Microsoft Docs
description: Saiba como usar o SQL Azure com o Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
editor: 
ms.assetid: 35f81d75-bfd7-4980-807e-00339f2cb2a4
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cfd3da08a8c8674e686ae2933db331809fb0e34d


---
# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure com o Azure RemoteApp
> [!IMPORTANT]
> O RemoteApp do Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Geralmente, quando os clientes optam por hospedar seus aplicativos do Windows na nuvem com o Azure RemoteApp, também querem migrar seus dados, como servidores SQL, para a nuvem para uma implantação de nuvem completa. Isso possibilita a solução hospedada de nuvem completa que pode ser acessada a qualquer momento por qualquer dispositivo em qualquer lugar usando o Azure RemoteApp. A seguir, os links e as referências juntamente com as orientações para ajudar você neste processo.  

## <a name="migrate-your-sql-data"></a>Migrar os dados SQL
Comece com [Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurar o Azure RemoteApp
Hospede seu aplicativo do Windows no Azure RemoteApp. A seguir, um passo a passo de nível muito alto:

1. Crie a [VM de modelo do RemoteApp do Azure](remoteapp-imageoptions.md). 
2. Instale o aplicativo necessário na VM.
3. Configure o aplicativo para que ele se conecte ao banco de dados SQL e confirme se ele funciona.
4. Execute o sysprep e desligue a máquina virtual. Capture isso como uma imagem para usá-la com o Azure. **Observação:** você precisará garantir que o aplicativo seja capaz de manter as informações de conectividade do banco de dados por meio do processo sysprep. Se o aplicativo for incapaz de reter as informações de conexão do banco de dados, talvez seja melhor entrar em contato com o fornecedor do aplicativo para verificar como podemos especificar a cadeia de conexão.
5. Importe a imagem personalizada para a biblioteca do Azure RemoteApp selecionando a localização geográfica adequada da sua implantação do SQL Azure. 
6. Implante uma coleção do RemoteApp no mesmo data center como a implantação do SQL Azure usando o modelo acima e publique o aplicativo. A implantação do Azure RemoteApp no mesmo data center que a implantação do SQL Azure ajuda a garantir velocidades de conexão mais rápidas e a reduzir a latência. 

## <a name="app-and-sql-configuration-considerations"></a>Considerações sobre a configuração do aplicativo e do SQL:
Há alguns pontos a considerar quando você estiver usando o SQL Azure com o RemoteApp:

Saiba [como configurar um firewall do banco de dados SQL do Azure](../sql-database/sql-database-firewall-configure.md). Um trecho do artigo declara “Inicialmente, todo acesso ao seu servidor do Banco de Dados SQL do Azure é bloqueado pelo firewall. Para começar a usar seu servidor de Banco de Dados SQL do Azure, você deve acessar o Portal Clássico e especificar uma ou mais regras de firewall no nível do servidor que permitem acesso ao seu servidor de Banco de Dados SQL do Azure. Use as regras de firewall para especificar quais intervalos de endereço IP da Internet têm permissão e se os aplicativos do Azure podem tentar se conectar ao seu servidor de Banco de Dados SQL”.

Além disso, quando um computador tenta se conectar ao seu servidor de banco de dados pela Internet, o firewall verifica o endereço IP de origem da solicitação com base no conjunto completo de regras de firewall no nível do servidor e (se necessário) no nível do banco de dados: “Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida ao seu servidor de Banco de Dados SQL do Azure”. Portanto, podemos usar Intervalos de IP e não apenas os endereços IP de origem individuais.

Siga as instruções passo a passo em [Como definir as configurações de firewall no Banco de Dados SQL usando o Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar o intervalo de IPs. Quando você estiver configurando as regras do Firewall do SQL, forneça o intervalo de IP da sub-rede especificada para a coleção do Azure RemoteApp. Isso deve permitir que os servidores ARA se conectem ao banco de dados SQL, embora eles tenham Endereços IP atribuídos dinamicamente.

## <a name="troubleshooting"></a>Solucionar problemas
Se a experiência de usar um aplicativo cliente hospedado no Azure RemoteApp que se conecta a um banco de dados SQL quando hospedado no Azure ou no local estiver lenta, pode haver alguns motivos para isso.  

* A latência de rede do dispositivo até o Azure é alta. Mova para uma conexão de rede que seja melhor e mais rápida para obter o melhor desempenho. Use [azurespeed.com](http://azurespeed.com/) como uma ferramenta geral para testar a latência dos dispositivos para o data center do Azure.  
* O aplicativo cliente hospedado no Azure RemoteApp está sob carga excessiva. Escolher um plano de cobrança diferente, como a cobrança Premium, melhorará o desempenho. Outro truque é monitorar os recursos que o aplicativo está consumindo: durante uma sessão ativa, execute uma sequência de teclas ctrl-alt-end para iniciar a tela SAS, selecione o Gerenciador de Tarefas e observe a utilização de recursos para seu aplicativo.
* O servidor SQL está sob carga excessiva ou não está otimizado. Siga as diretrizes do SQL para solução de problemas. 




<!--HONumber=Nov16_HO2-->


