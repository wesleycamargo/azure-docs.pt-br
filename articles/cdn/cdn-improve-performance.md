---
title: Melhorar o desempenho ao compactar arquivos na CDN do Azure | Microsoft Docs
description: Saiba como melhorar a velocidade de transferência do arquivo e aumentar o desempenho de carregamento da página compactando os arquivos na CDN do Azure.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper

---
# Melhorar o desempenho ao compactar arquivos
A compactação é um método simples e eficiente para melhorar a velocidade de transferência de arquivos e aumentar o desempenho de carregamento de páginas, reduzindo o tamanho de arquivos antes de serem enviados do servidor. Ela reduz os custos de largura de banda e oferece uma experiência mais responsiva para os seus usuários.

Há duas maneiras de habilitar a compactação:

* Você pode habilitar a compactação no seu servidor de origem. Nesse caso, a CDN passará os arquivos compactados e entregará arquivos compactados para os clientes que os solicitem.
* Você pode habilitar a compactação diretamente nos servidores de borda CDN; nesse caso, o CDN compactará os arquivos e os fornecerá aos usuários finais mesmo se eles não forem compactados pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração de CDN levam algum tempo para se propagarem pela rede. Para perfis <b>CDN do Azure do Akamai</b>, a propagação normalmente é concluída em menos de um minuto. Para perfis <b>CDN do Azure da Verizon</b>, você geralmente verá suas alterações serem aplicadas em 90 minutos. Se esta for a primeira vez que você configura a compactação do seu ponto de extremidade CDN, será necessário considerar uma espera de 1 a 2 horas para garantir que as configurações de compactação sejam propagadas para os POPs antes de solucionar problemas
> 
> 

## Habilitando a compactação
> [!NOTE]
> As camadas CDN Standard e Premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário varia. Para saber mais sobre as diferenças entre as camadas CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).
> 
> 

### Camada padrão
> [!NOTE]
> Esta seção aplica-se aos **CDN do Azure Standard da Verizon** e aos perfis **CDN do Azure da Akamai**.
> 
> 

1. Na folha Perfil CDN, clique no ponto de extremidade da CDN que deseja gerenciar.
   
    ![Pontos de extremidade da folha Perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    A folha do ponto de extremidade da CDN se abre.
2. Clique no botão **Configurar**.
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    A folha Configuração da CDN se abre.
3. Habilitar **Compactação**.
   
    ![Opções de compactação da CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Use os tipos padrão ou modifique a lista removendo ou adicionando tipos de arquivo.
   
   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados, como ZIP, MP3, MP4, JPG, etc.
   > 
   > 
5. Após fazer suas alterações, clique no botão **Salvar**.

### Camada premium
> [!NOTE]
> Esta seção aplica-se aos perfis **CDN do Azure Premium da Verizon**.
> 
> 

1. Na folha do perfil CDN, clique no botão **Gerenciar**.
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o ponteiro do mouse sobre a guia **HTTP grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Compactação**.
   
    As opções de compactação são exibidas.
   
    ![Compactação de arquivos](./media/cdn-file-compression/cdn-compress-files.png)
3. Habilite a compactação clicando no botão de opção **Compactação Habilitada**. Insira os tipos MIME que você deseja compactar como uma lista delimitada por vírgula (sem espaços) na caixa de texto **Tipos de Arquivo**.
   
   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados, como ZIP, MP3, MP4, JPG, etc.
   > 
   > 
4. Depois de fazer as alterações, clique no botão **Atualizar**.

## Regras de compactação
Essas tabelas descrevem o comportamento de compactação CDN do Azure para cada cenário.

> [!IMPORTANT]
> Para o **CDN do Azure da Verizon** (Standard e Premium), só os arquivos elegíveis são compactados. Para se qualificar para a compactação, um arquivo deve:
> 
> * Ser maior que 128 bytes.
> * Ser menor que 1 MB.
> 
> Para o **CDN do Azure do Akamai**, todos os arquivos estão qualificados para compactação.
> 
> Para todos os produtos de CDN do Azure, um arquivo deve ser um tipo MIME que foi [configurado para compactação](#enabling-compression).
> 
> Os perfis do **CDN do Azure da Verizon** (Standard e Premium) dão suporte a codificações **gzip**, **deflate**, ou **bzip2**. Os perfis do **CDN do Azure do Akamai** somente dão suporte à codificação **gzip**.
> 
> Pontos de extremidade do **CDN do Azure da Akamai** sempre solicitam arquivos codificados **gzip** na origem, independentemente da solicitação do cliente.
> 
> 

### Compactação desabilitada ou arquivo não qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | Resposta CDN para o cliente | Observações |
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado | |
| Compactado |Não compactado |Não compactada | |
| Compactado |Não armazenado em cache |Compactada ou descompactada |Depende da resposta de origem |
| Não compactado |Compactado |Não compactado | |
| Não compactada |Não compactada |Não compactada | |
| Não compactado |Não armazenado em cache |Não compactada | |

### Compactação habilitada ou arquivo qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | Resposta CDN para o cliente | Observações |
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado |CDN transcodifica entre os formatos com suporte |
| Compactado |Não compactado |Compactado |CDN executa compactação |
| Compactado |Não armazenado em cache |Compactado |O CDN executará compactação se a origem for retornada descompactada. O **Azure CDN da Verizon** passará o arquivo descompactado na primeira solicitação e, em seguida, compactará e armazenará em cache o arquivo para solicitações subsequentes. Os arquivos com o cabeçalho `Cache-Control: no-cache` nunca serão compactados. |
| Não compactada |Compactado |Não compactada |CDN executa descompactação |
| Não compactado |Não compactada |Não compactada | |
| Não compactado |Não armazenado em cache |Não compactada | |

## Compactação de CDN dos Serviços de Mídia
Para pontos de extremidade de streaming habilitado para a CDN de Serviços de Mídia, a compactação está habilitada por padrão para os seguintes tipos de conteúdo: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Você não pode habilitar/desabilitar a compactação para os tipos mencionados usando o portal do Azure.

## Consulte também
* [Solucionando problemas de compactação de arquivo CDN](cdn-troubleshoot-compression.md)

<!---HONumber=AcomDC_0803_2016-->