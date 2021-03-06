---
title: Servidores no Azure Stack de hospedagem MySQL | Microsoft Docs
description: Como adicionar instâncias do MySQL para o provisionamento por meio do provedor de recursos de adaptador do MySQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 8cffcc938a247a2b08ff53b128560e1ab5e1653a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499770"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos do MySQL

Você pode hospedar um instância de servidor em uma máquina virtual (VM) de hospedagem MySQL em [do Azure Stack](azure-stack-poc.md), ou em uma VM fora de seu ambiente do Azure Stack, desde que o provedor de recursos do MySQL pode se conectar à instância.

> [!NOTE]
> O provedor de recursos do MySQL deve ser criado na assinatura padrão do provedor, enquanto os servidores de hospedagem MySQL devem ser criados em faturáveis, assinaturas de usuário. O servidor do provedor de recursos não deve ser usado para hospedar bancos de dados do usuário.

Versões do MySQL 5.6, 5.7 e 8.0 podem ser usadas para servidores de hospedagem. O RP MySQL não dá suporte à autenticação de caching_sha2_password; que será adicionado na próxima versão. Servidores MySQL 8.0 devem ser configurados para usar mysql_native_password. Também há suporte para o MariaDB.

## <a name="connect-to-a-mysql-hosting-server"></a>Conectar-se ao servidor de hospedagem MySQL

Verifique se que você tem as credenciais para uma conta com privilégios de administrador do sistema. Para adicionar um servidor de hospedagem, siga estas etapas:

1. Entre no portal do Azure Stack de operador como um administrador de serviço.
2. Selecione **Todos os serviços**.
3. Sob o **recursos ADMINISTRATIVOS** categoria, selecione **servidores de hospedagem MySQL** > **+ adicionar**. Isso abre o **adicionar um servidor de hospedagem MySQL** caixa de diálogo, mostrada na captura de tela a seguir.

   ![Configurar um servidor de hospedagem](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Forneça os detalhes de conexão da sua instância do servidor MySQL.

   * Para **nome do servidor de hospedagem MySQL**, forneça o nome de domínio totalmente qualificado (FQDN) ou um endereço IPv4 válido. Não use o nome curto de VM.
   * O administrador padrão **nome de usuário** é para o Bitnami MySQL imagens disponíveis no marketplace do Azure Stack *raiz*. 
   * Se você não souber a raiz **senha**, consulte o [Bitnami documentação](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) para saber como obtê-lo. 
   * Uma instância do MySQL padrão não for fornecida, portanto, você precisa especificar o **tamanho de hospedagem de servidor em GB**. Insira um tamanho próximo a capacidade do servidor de banco de dados.
   * Mantenha a configuração padrão para **assinatura**.
   * Para **grupo de recursos**, crie um novo ou usar um grupo existente.

   > [!NOTE]
   > Se a instância do MySQL pode ser acessada por locatário e o administrador do Azure Resource Manager, você pode colocá-lo sob o controle do provedor de recursos. Mas, a instância do MySQL **deve** ser alocada exclusivamente para o provedor de recursos.

5. Selecione **SKUs** para abrir o **criar SKU** caixa de diálogo.

   ![Criar uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   A SKU **nome** deve refletir as propriedades do SKU para que os usuários podem implantar seus bancos de dados para a SKU adequada.

6. Selecione **Okey** para criar o SKU.
   > [!NOTE]
   > SKUs podem demorar até uma hora para ser visível no portal. Você não pode criar um banco de dados até que a SKU é implantado e em execução.

7. Sob **adicionar um servidor de hospedagem MySQL**, selecione **criar**.

Conforme você adiciona servidores, atribuí-los para um novo ou existente a SKU para diferenciar as ofertas de serviço. Por exemplo, você pode ter uma instância do MySQL empresarial que fornece o maior banco de dados e backups automáticos. Você pode reservar a este servidor de alto desempenho para diferentes departamentos na sua organização.

## <a name="security-considerations-for-mysql"></a>Considerações de segurança para MySQL

As informações a seguir se aplica a RP e servidores de hospedagem MySQL:

* Certifique-se de que todos os servidores de hospedagem são configurados para comunicação usando o TLS 1.2. Ver [Configurando o MySQL para usar conexões criptografadas](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Empregar [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* O RP MySQL não dá suporte à autenticação de caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Aumentar a capacidade do banco de dados de back-end

Você pode aumentar a capacidade de banco de dados de back-end Implantando mais servidores MySQL no portal do Azure Stack. Adicione esses servidores para um SKU de novo ou existente. Se você adicionar um servidor a uma SKU existente, certifique-se de que as características de servidor são os mesmos que os outros servidores na SKU.

## <a name="sku-notes"></a>Notas SKU
Use um nome SKU que descreve os recursos dos servidores na SKU, como capacidade e desempenho. O nome serve como um auxílio para ajudar os usuários a implantar seus bancos de dados para a SKU adequada. Por exemplo, você pode usar nomes de SKU para diferenciar as ofertas de serviço, as seguintes características:
  
* alta capacidade
* alto desempenho
* alta disponibilidade

Como prática recomendada, todos os servidores de hospedagem em um SKU devem ter as mesmas características de desempenho e recursos.

SKUs não podem ser atribuídos a usuários ou grupos específicos.

Para editar uma SKU, vá para **todos os serviços** > **adaptador do MySQL** > **SKUs**. Selecione a SKU para modificar, faça as alterações necessárias e clique em **salvar** para salvar as alterações. 

Para excluir uma SKU que não é mais necessário, vá para **todos os serviços** > **adaptador do MySQL** > **SKUs**. O nome da SKU com o botão direito e selecione **excluir** para excluí-lo.

> [!IMPORTANT]
> Pode demorar até uma hora para novas SKUs disponíveis no portal do usuário.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Disponibilizar os servidores de banco de dados MySQL para seus usuários

Crie planos e ofertas para disponibilizar os servidores de banco de dados MySQL para os usuários. Adicionar o serviço Microsoft.MySqlAdapter ao plano e crie uma nova cota. MySQL não permite limitar o tamanho dos bancos de dados.

> [!IMPORTANT]
> Pode levar até duas horas para novas cotas para estar disponível no portal do usuário ou antes de uma cota alterada é imposta.

## <a name="next-steps"></a>Próximas etapas

[Criar um banco de dados MySQL](azure-stack-mysql-resource-provider-databases.md)