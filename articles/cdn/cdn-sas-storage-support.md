---
title: Usando a CDN do Azure com SAS | Microsoft Docs
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Usando a CDN do Azure com SAS

Quando fornece conteúdo do contêiner de armazenamento de sua conta de armazenamento, talvez você queira proteger como os usuários podem acessar seus arquivos, concedendo acesso privado ao contêiner de armazenamento. Caso contrário, um contêiner de armazenamento para o qual acesso público foi concedido pode ser acessado por qualquer pessoa que souber sua URL. Para proteger uma conta de armazenamento que você permitiu que CDN (rede de distribuição de conteúdo) acessasse, você pode usar o recurso de SAS (Assinatura de Acesso Compartilhado) do armazenamento do Azure para conceder acesso limitado a contêineres de armazenamento privados.

Uma SAS é um URI que concede direitos de acesso restrito aos seus recursos do armazenamento do Azure sem expor sua chave de conta. Você pode fornecer uma SAS para clientes aos quais não confia sua chave de conta de armazenamento, mas para os quais deseja delegar acesso a determinados recursos da conta de armazenamento. Ao distribuir um URI de assinatura de acesso compartilhado para esses clientes, você concede a eles acesso a um recurso por um período especificado.
 
A SAS permite definir vários parâmetros de acesso para um blob, como horários de início e vencimento, permissões (leitura/gravação) e intervalos de IP. Este artigo descreve como usar a SAS em conjunto com a CDN do Azure. Para obter mais informações sobre a SAS, incluindo como criá-la e suas opções de parâmetro, consulte [Usando assinaturas de acesso compartilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurando a CDN do Azure para trabalhar com SAS no armazenamento
As três opções a seguir são recomendadas para usar a SAS com a CDN do Azure. Todas as opções pressupõem que você já tenha criado uma SAS que está em funcionamento (consulte os pré-requisitos). 
 
### <a name="prerequisites"></a>pré-requisitos
Para começar, crie uma conta de armazenamento e gere uma SAS para seu ativo. Você pode gerar dois tipos de assinaturas de acesso compartilhado: uma SAS de serviço ou uma SAS de conta. Para obter mais informações, confira [Tipos de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Após gerar uma SAS, você pode acessar o arquivo de armazenamento de blobs com uma URL que tem o seguinte formato:`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Por exemplo: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre a configuração dos parâmetros, consulte [Considerações sobre parâmetros de SAS](#sas-parameter-considerations) e [Parâmetros de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Configurações de SAS da CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>Opção 1: usar a SAS com passagem para o armazenamento de blobs da CDN

Esta opção é a mais simples e usa apenas um token de SAS, que é passado da CDN para o servidor de origem. Ela é compatível com a **CDN do Azure da Verizon**, para os perfis Standard e Premium, e da **CDN do Azure da Akamai**. 
 
1. Selecione um ponto de extremidade, clique em **Regras de cache** e, em seguida, selecione **Armazenar em cache todas as URLs exclusivas** na lista **Cache da cadeia de caracteres de consulta**.

    ![Regras de cache da CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar a SAS em sua conta de armazenamento, use o token de SAS com a URL da CDN para acessar o arquivo. 
   
   A URL resultante tem o seguinte formato: `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Por exemplo:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste a duração do cache usando regras de cache ou adicionando cabeçalhos `Cache-Control` na origem. Como a CDN trata o token de SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve definir a uma duração do cache que expira na mesma hora que a SAS ou antes. Caso contrário, se um arquivo for armazenado em cache por mais tempo que a SAS está ativa, o arquivo poderá ficar acessível no servidor de origem da CDN depois de decorrido o tempo de expiração da SAS. Se isso ocorrer e você desejar tornar o arquivo armazenado em cache inacessível, execute uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN, consulte [Controlar o comportamento de cache da Rede de Distribuição de Conteúdo do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>Opção 2: token de segurança da CDN oculto usando regra de regravação
 
Com essa opção, você pode proteger o armazenamento de blobs de origem sem precisar de um token SAS para o usuário da CDN. Talvez você queira usar esta opção se não precisar de restrições de acesso específicas para o arquivo, mas desejar impedir que usuários acessem a origem do armazenamento diretamente para melhorar o tempo de descarregamento da CDN. Essa opção só está disponível para perfis da **CDN Premium do Azure da Verizon**. 
 
1. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de regravação de URL. Novas regras levam cerca de 90 minutos para serem propagadas.

   ![Botão Gerenciar CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botão do mecanismo de regras da CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Essa regra de regravação de URL de exemplo tem os seguintes padrões:
   
   Origem:   
   `/test/demo.jpg`
   
   Destino:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regra de regravação de URL da CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Acesse o arquivo em sua CDN sem o token da SAS, no seguinte formato: `https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Por exemplo:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Observe que qualquer pessoa, independentemente de estar usando um token de SAS, pode acessar um ponto de extremidade da CDN. 

3. Ajuste a duração do cache usando regras de cache ou adicionando cabeçalhos `Cache-Control` na origem. Como a CDN trata o token de SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve definir a uma duração do cache que expira na mesma hora que a SAS ou antes. Caso contrário, se um arquivo for armazenado em cache por mais tempo que a SAS está ativa, o arquivo poderá ficar acessível no servidor de origem da CDN depois de decorrido o tempo de expiração da SAS. Se isso ocorrer e você desejar tornar o arquivo armazenado em cache inacessível, execute uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN, consulte [Controlar o comportamento de cache da Rede de Distribuição de Conteúdo do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: usar a autenticação de token de segurança da CDN com uma regra de regravação

Essa opção é a mais segura e personalizável. Para usar a autenticação de token de segurança da CDN, você precisa ter um perfil da **CDN Premium do Azure da Verizon**. O acesso do cliente é baseado nos parâmetros de segurança definidos no token de segurança da CDN. No entanto, se a SAS se tornar inválida, a CDN não conseguirá revalidar o conteúdo do servidor de origem.

1. [Crie um token de segurança da CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e ative-o usando o mecanismo de regras para o ponto de extremidade e o caminho da CDN na qual os usuários podem acessar o arquivo.

   Uma URL SAS tem o seguinte formato:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Por exemplo:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetro para uma autenticação de token de segurança da CDN são diferentes das opções de parâmetro para um token da SAS. Se você optar por usar uma hora de expiração quando criar um token de segurança da CDN, defina-a com o mesmo valor que a hora de expiração do token da SAS. Isso garante que a hora de expiração seja previsível. 
 
2. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de regravação de URL para permitir acesso com token a todos os blobs no contêiner. Novas regras levam cerca de 90 minutos para serem propagadas.

   Essa regra de regravação de URL de exemplo tem os seguintes padrões:
   
   Origem:   
   `/test/demo.jpg`
   
   Destino:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regra de regravação de URL da CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. Quando renovar a SAS, atualize a regra de regravação da URL para usar o novo token da SAS. 

## <a name="sas-parameter-considerations"></a>Considerações sobre parâmetros da SAS

Como parâmetros da SAS não são visíveis para a CDN, a CDN não pode alterar seu comportamento de entrega com base neles. As restrições de parâmetro definidas se aplicam apenas a solicitações que a CDN faz para o servidor de origem, não para solicitações do cliente à CDN. Essa distinção é importante de considerar quando você define os parâmetros de SAS. Se esses recursos avançados forem necessários e você estiver usando a [Opção 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), defina as restrições apropriadas no token de segurança da CDN.

| Nome do parâmetro da SAS | DESCRIÇÃO |
| --- | --- |
| Iniciar | A hora em que a CDN pode começar a acessar o arquivo de blob. Devido à distorção do relógio (quando chega um sinal de relógio em momentos diferentes para diferentes componentes), escolha um horário 15 minutos atrás se você quiser que o recurso esteja disponível imediatamente. |
| End | A hora após a qual a CDN não poderá mais acessar o arquivo de blob. Arquivos armazenados em cache anteriormente na CDN ainda são acessíveis. Para controlar a hora de expiração do arquivo, defina a hora de expiração apropriada no token de segurança da CDN ou limpe o ativo. |
| Endereços IP permitidos | Opcional. Se estiver usando a **CDN do Azure da Verizon**, você poderá definir esse parâmetro para os intervalos definidos em [Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx) (CDN do Azure de Intervalos de IP do Servidor de Borda Verizon). Se estiver usando a **CDN do Azure da Akamai**, você não poderá definir o parâmetro de intervalos de IP porque os endereços de IP não são estáticos.|
| Protocolos permitidos | Os protocolos permitidos para uma solicitação feita com a conta de SAS. A configuração HTTPS é recomendada.|

## <a name="see-also"></a>Consulte também
- [Como usar SAS (assinaturas de acesso compartilhado)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Assinaturas de Acesso Compartilhado, Parte 2: Criar e usar uma SAS com o Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Proteger ativos da Rede de Distribuição de Conteúdo do Azure com autenticação de token](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
