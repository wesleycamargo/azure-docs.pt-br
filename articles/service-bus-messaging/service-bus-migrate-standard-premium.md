---
title: Migrar existente do Azure Service Bus Namespaces padrão para a camada Premium | Microsoft Docs
description: Guia para permitir a migração de existente do Azure Service Namespaces do barramento de Standard para Premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896235"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrar existente do Azure Service Bus Namespaces padrão para a camada Premium

Anteriormente, o barramento de serviço do Azure oferecidos namespaces apenas na camada Standard. Elas eram as instalações de multilocatário que foram otimizadas para ambientes de desenvolvedor e baixa taxa de transferência.

Recentemente, o barramento de serviço do Azure foi expandido para oferecer a camada Premium, que oferece recursos dedicados por namespace para maior taxa de transferência a um preço fixo que é otimizado para ambientes de produção e de alta taxa de transferência e latência previsível exigir recursos adicionais do enterprise.

A seguir habilita existente namespaces da camada Standard para ser migrada para a camada Premium de ferramentas.

>[!WARNING]
> Migração destina-se para o namespace de barramento de serviço Standard para ser ***atualizado*** para a camada Premium.
>
> As ferramentas de migração ***não*** suporte a fazer downgrade.
>[!NOTE]
> Essa migração deve acontecer ***in-loco***.
>
> Isso implica que o remetente existente e aplicativos de receptor não exigem qualquer alteração de configuração ou código.
>
> A cadeia de caracteres de conexão existente vai apontar automaticamente para o novo namespace premium.
>
> Além disso, todas as entidades no namespace padrão são **copiadas** no namespace Premium durante o processo de migração.
>
>
> Damos suporte a ***1000 entidades por unidade de sistema de mensagens*** no Premium, portanto, para identificar a quantidade de unidades de mensagens, você precisa, inicie com o número de entidades que você tem em seu namespace padrão atual.

## <a name="migration-steps"></a>Etapas da migração

>[!IMPORTANT]
> Há algumas limitações associadas com o processo de migração. Solicitamos que você totalmente se familiarizar com as etapas envolvidas reduzir as possibilidades de erros.

O processo de migração de passo a passo concreta é detalhado nos guias a seguir.

As etapas envolvidas são-

1. Crie um novo namespace Premium.
2. Emparelhe o namespace Standard e Premium uns aos outros.
3. Entidades de sincronização (com cópia) de Standard para o namespace Premium
4. Confirmar a migração
5. Drenar as entidades no namespace padrão usando o nome após a migração do namespace
6. Exclua o namespace padrão

>[!NOTE]
> Depois que a migração foi confirmada, é extremamente importante acessar o namespace padrão antigo e esvaziar a filas e assinaturas.
>
> Depois que as mensagens tem sido esvaziadas podem ser enviados para o novo namespace premium a serem processadas pelos aplicativos do receptor.
>
> Depois de tem sido esvaziadas a filas e assinaturas, recomendamos que você excluir o antigo namespace padrão. Você não precisá-lo!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrar usando a CLI do Azure ou o PowerShell

Para migrar seu namespace de barramento de serviço Standard para Premium usando a ferramenta de CLI do Azure ou o PowerShell, consulte o abaixo da guia.

1. Crie um novo namespace de barramento de serviço Premium. Você pode fazer referência a [Azure Resource Manager templates](service-bus-resource-manager-namespace.md) ou [usar o portal do Azure](service-bus-create-namespace-portal.md). Certifique-se de selecionar "Premium" para o **serviceBusSku** parâmetro.

2. Defina a abaixo das variáveis de ambiente para simplificar os comandos de migração.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > O nome após a migração (post_migration_dns_name) será usado para acessar a namespace padrão após a migração antigo. Você deve usar isso para drenar as filas e as assinaturas e, em seguida, exclua o namespace.

3. **Par** os namespaces Standard e Premium e **Iniciar sincronização** usando o comando - abaixo

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Verificar o status da migração usando o comando - abaixo
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada concluída quando
    * MigrationState = "Ativo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Êxito"

    Esse comando também exibe a configuração de migração. Por favor, verifique novamente para garantir que os valores são definidos como anteriores declarado.

    Além disso, verifique também o namespace Premium no portal para garantir que todas as filas e tópicos foram criados, e se eles correspondem à que existia no namespace padrão.

5. Confirmar a migração, executando o comando completo abaixo
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrar usando o portal do Azure

Migração por meio do portal do Azure tem o mesmo fluxo lógico de migração usando os comandos. Consulte o abaixo da guia para o processo passo a passo para migrar usando o portal.

1. Escolher o **'Migrar para o Premium'** a opção de menu no menu de navegação no painel esquerdo. Clique o **'Get Started'** botão para prosseguir para a próxima página.
    ![Página de aterrissagem de migração][]

2. Concluída **instalação**.
   ![Namespace de instalação][]
   1. Crie e atribua o namespace para migrar o namespace padrão existente para Premium.
        ![Configurar o namespace – criar um namespace premium][]
   2. Escolher o **'Nome após a migração'** para acessar o namespace padrão por após a migração for concluída.
        ![Configurar o namespace – escolher o nome da migração de postagem][]
   3. Clique em **'Próximo'** para continuar.
3. **Sincronização** entidades entre o namespace Standard e Premium.
    ![Configurar o namespace - entidades de sincronização - iniciar][]

   1. Clique em **Iniciar sincronização** para iniciar a sincronização de entidades.
   2. Clique em **'Sim'** no pop-up confirmando para iniciar a sincronização.
   3. Aguarde até que o **sincronização** for concluída. O status se torna disponível na barra de status.
        ![Namespace - entidades de sincronização - progresso da instalação][]
        >[!IMPORTANT]
        > Se você precisar **anular** por algum motivo, verifique o fluxo de anular a seção de perguntas Frequentes deste documento.
   4. Depois que a sincronização seja concluída, clique no **'Próximo'** botão na parte inferior da página.

4. Examine as alterações na página de resumo.
    ![Namespace de switch - opção de menu][]

5. Clique em **'Concluir a migração'** alternar namespaces e concluir a migração.
    ![Namespace de switch - êxito][]

## <a name="faqs"></a>Perguntas frequentes

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é confirmada?

Depois que a migração for confirmada, a cadeia de caracteres de conexão apontada para o namespace padrão apontará para o namespace Premium.

Os aplicativos de remetente e destinatário serão desconectar o Namespace padrão e reconectar-se automaticamente ao namespace Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que fazer devo fazer após a conclusão do padrão para a migração de Premium?

O padrão para a migração Premium garante que os metadados da entidade (tópicos, assinaturas, filtros, entre outros) são copiados do padrão para o namespace Premium. Os dados da mensagem que foram confirmados para o namespace padrão não são copiados ao longo do padrão para o namespace Premium.

Devido a isso, o namespace padrão pode ter algumas mensagens que foram enviadas e confirmadas enquanto a migração estava em andamento. Essas mensagens devem ser extraídas do Namespace padrão e enviadas pela manualmente o Namespace Premium manualmente.

Para fazer isso, você ***devem*** usar um aplicativo de console ou um script que drenar as entidades de namespace padrão usando o **nome DNS de migração de postagem** que você especificou nos comandos de migração e, em seguida, envie essas mensagens em o Namespace Premium, para que eles podem ser processados pelos destinatários.

Depois que as mensagens tem sido esvaziadas, vá para excluir o namespace padrão.

>[!IMPORTANT]
> Observe que, depois que as mensagens do namespace padrão tem sido esvaziadas, você **deve** excluir o namespace padrão.
>
> Isso é importante porque a cadeia de caracteres de conexão que referenciada inicialmente para o namespace padrão agora na verdade faz referência ao namespace Premium. Você não precisar esse Namespace padrão mais.
>
> Excluir o namespace padrão que você migrou ajuda reduz a confusão em uma data posterior. 

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade esperado?
O processo de migração descrito acima destina-se para reduzir o tempo de inatividade esperado para os aplicativos. Isso é feito usando a cadeia de caracteres de conexão usadas pelos aplicativos de remetente e destinatário para apontar para o novo namespace Premium.

O tempo de inatividade experientes pelo aplicativo é limitado à quantidade de tempo que leva para atualizar a entrada DNS para apontar para o namespace Premium.

Isso pode ser considerado ***aproximadamente 5 minutos***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>É necessário fazer alterações de configuração ao executar a migração?
Não, não há nenhuma alteração de código/configuração necessárias para executar essa migração. A cadeia de caracteres de conexão que os aplicativos de remetente e receptor usam para acessar o Namespace padrão é mapeada automaticamente para atuar como um **alias** para o Namespace Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu anular a migração?
Migração pode ser cancelada usando o comando 'Anular' ou por meio do portal do Azure. 

#### <a name="azure-cli-or-powershell"></a>CLI do Azure ou o PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Portal do Azure

![Anular fluxo – anular sincronização][]
![anular fluxo - abort completa][]

Quando o processo de migração é anulado, ele realmente anula o processo de cópia sobre as entidades (tópicos, assinaturas e filtros) de Standard para o namespace Premium e interrompe o emparelhamento.

A cadeia de caracteres de conexão **não é** atualizado para apontar para o namespace Premium. Os aplicativos existentes continuam a funcionar como ocorria antes de iniciar a migração.

No entanto, ele **não** excluir as entidades no namespace Premium ou excluir o namespace Premium em si. Isso deve ser feito manualmente, se você tivesse decidiu não prosseguir com a migração Afinal de contas.

>[!IMPORTANT]
> Se você optar por anular a migração, exclua o Namespace Premium que você tinha provisionado para a migração, para que você não seja cobrado pelos recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter esvaziar as mensagens. O que devo fazer?

Pode haver mensagens que são enviadas pelos aplicativos do remetente e confirmadas para o armazenamento em que o Namespace padrão, enquanto a migração estiver ocorrendo, e logo antes da migração é confirmado.

Considerando que, durante a migração, a mensagem real/carga de dados não é copiada ao longo do Standard para Premium, eles devem ser descarregadas manualmente e, em seguida, enviado para o namespace Premium.

No entanto, se você pode migrar durante uma janela de manutenção do sistema/manutenção planejada e não deseja esvaziar manualmente e enviar as mensagens, siga as etapas - abaixo

1. Parar os aplicativos do remetente e permitir que os receptores processar as mensagens que estão atualmente no namespace padrão e drenar a fila.
2. Depois que as filas e assinaturas no Namespace padrão estiverem vazias, siga o procedimento descrito acima para executar a migração de Standard para o namespace Premium.
3. Depois que a migração for concluída, você pode reiniciar os aplicativos do remetente.
4. Os remetentes e receptores agora se conecta automaticamente ao namespace Premium.

    >[!NOTE]
    > O receptor não precisa ser interrompido para a migração.
    >
    > Depois que a migração for concluída, os receptores serão desconectar do namespace padrão e conecte-se automaticamente ao namespace Premium.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [diferenças entre Standard e Premium de mensagens](./service-bus-premium-messaging.md)
* Saiba mais sobre os aspectos de recuperação geográfica Diaster e de alta disponibilidade para o barramento de serviço Premium [aqui](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Página de aterrissagem de migração]: ./media/service-bus-standard-premium-migration/1.png
[Namespace de instalação]: ./media/service-bus-standard-premium-migration/2.png
[Configurar o namespace – criar um namespace premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurar o namespace – escolher o nome da migração de postagem]: ./media/service-bus-standard-premium-migration/4.png
[Configurar o namespace - entidades de sincronização - iniciar]: ./media/service-bus-standard-premium-migration/5.png
[Namespace - entidades de sincronização - progresso da instalação]: ./media/service-bus-standard-premium-migration/8.png
[Namespace de switch - opção de menu]: ./media/service-bus-standard-premium-migration/9.png
[Namespace de switch - êxito]: ./media/service-bus-standard-premium-migration/12.png

[Anular fluxo – cancelar sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Anular fluxo - abort completa]: ./media/service-bus-standard-premium-migration/abort3.png
