---
title: Problemas Conhecidos com Plugin Acústico - Serviços Cognitivos
description: Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: c19b19cab33ae868f11ded0b7ce87dac99269596
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431961"
---
# <a name="known-issues"></a>Problemas conhecidos
Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos pertencentes a essa cena não serão transferidos automaticamente para a nova cena. Eles ainda existirão no antigo arquivo de ativos, no entanto. Procure o arquivo **SceneName_AcousticParameters.asset** dentro do diretório **Editor** ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>O caminho padrão para a pasta AcousticsData na guia Probes é um caminho absoluto

Isso deve padronizar um caminho relativo para facilitar o compartilhamento de projetos entre os colaboradores. Como alternativa, altere o caminho para serem relativos ao diretório do projeto.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Os voxels de tempo de execução têm um tamanho diferente dos voxels de visualização de cena

Se você fizer um **Calcular** na aba **Sondas** e ver os voxels, então faça um assar e visualize os voxels em tempo de execução para a mesma cena, os voxels são tamanhos diferentes. Os voxels mostrados pré-cozedura são os voxels usados na simulação. Os voxels mostrados no tempo de execução são usados para interpolação entre pontos de teste. Isso pode causar uma inconsistência na qual os portais aparecerão abertos no tempo de execução que não estão realmente abertos.

## <a name="uwp-builds-not-working"></a>Compilações de UWP não está funcionando

Nas versões mais recentes do Unity (2018.2+), as compilações do UWP não estão sendo bem-sucedidas. A fase de execução da compilação será interrompida e você receberá os erros "Extensões da unidade ainda não foram inicializadas". Isso é controlado por [esse problema Unity](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Falha de Unity ao fechar projeto

Nas versões mais recentes do Unity (2018.2+), há um bug conhecido no qual o Unity travará quando você fechar o projeto. Isso é controlado por [esse problema Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problemas de implantação do Android
Para usar o Project Acoustics no Android, altere seu destino de criação para o Android. Algumas versões do Unity possuem um bug na implantação de plugins de áudio - certifique-se de não estar usando uma versão afetada por [este bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Eu recebo um erro que 'não foi possível encontrar o arquivo de metadados dll'

Assegure-se de que a Versão do Runtime do Script nas configurações do Player esteja definida como **Equivalente ao .NET 4.x** e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou tendo problemas de autenticação ao se conectar ao Azure

Verifique se você usou as credenciais corretas para sua conta do Azure, se sua conta oferece suporte ao tipo de nó solicitado no bake e se o relógio do sistema está correto.

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [integrando acústica em seu projeto do Unity](getting-started.md)

