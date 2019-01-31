---
title: Problemas conhecidos com o plug-in de Projeto Acústico
titlesuffix: Azure Cognitive Services
description: Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 02f56306110e904f41c1b94346c58b33ce0b575c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156174"
---
# <a name="known-issues"></a>Problemas conhecidos
Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos pertencentes a essa cena não serão transferidos automaticamente para a nova cena. Eles ainda existirão no antigo arquivo de ativos, no entanto. Procure o arquivo **SceneName_AcousticParameters.asset** dentro do diretório **Editor** ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Os voxels de tempo de execução têm um tamanho diferente dos voxels de visualização de cena

Se você fizer um **Calcular** na aba **Sondas** e ver os voxels, então faça um assar e visualize os voxels em tempo de execução para a mesma cena, os voxels são tamanhos diferentes. Os voxels mostrados pré-cozedura são os voxels usados na simulação. Os voxels mostrados no tempo de execução são usados para interpolação entre pontos de teste. Isso pode causar uma inconsistência na qual os portais aparecerão abertos no tempo de execução que não estão realmente abertos.

## <a name="unity-crashes-when-closing-project"></a>Falha de Unity ao fechar projeto

Nas versões mais recentes do Unity (2018.2+), há um bug conhecido no qual o Unity travará quando você fechar o projeto. Isso é controlado por [esse problema Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problemas de implantação do Android
Para usar o Project Acoustics no Android, altere seu destino de criação para o Android. Algumas versões do Unity possuem um bug na implantação de plugins de áudio - certifique-se de não estar usando uma versão afetada por [este bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Eu recebo um erro que 'não foi possível encontrar o arquivo de metadados dll'

Assegure-se de que a Versão do Runtime do Script nas configurações do Player esteja definida como **Equivalente ao .NET 4.x** e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou tendo problemas de autenticação ao se conectar ao Azure

Verifique se você usou as credenciais corretas para sua conta do Azure, se sua conta oferece suporte ao tipo de nó solicitado no bake e se o relógio do sistema está correto.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Cancelar um bake deixa a guia Bake em estado de "exclusão"
O Projeto Acústico limpará todos os recursos do Azure para um trabalho após a conclusão com êxito ou o cancelamento, o que pode levar até cinco minutos.

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [integrando acústica em seu projeto do Unity](getting-started.md)

