---
title: クラウドネイティブサービスでのコンテナーとサーバーレスアプローチの組み合わせ
description: コンテナーと Kubernetes をサーバーレスアプローチと組み合わせる
ms.date: 04/23/2020
ms.openlocfilehash: fe9e9fd5d07132971d64bc6433a762fb7bd22048
ms.sourcegitcommit: 5988e9a29cedb8757320817deda3c08c6f44a6aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82199665"
---
# <a name="combining-containers-and-serverless-approaches"></a>コンテナーとサーバーレスの手法の組み合わせ

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

クラウドネイティブアプリケーションは、通常、コンテナーとオーケストレーションを活用するサービスを実装します。 多くの場合、アプリケーションのサービスの一部を Azure Functions として公開する機会があります。 ただし、Kubernetes にデプロイされたクラウドネイティブアプリでは、この同じツールセット内で Azure Functions を活用すると便利です。 幸いにも、Docker コンテナー内で Azure Functions をラップして、Kubernetes ベースのアプリの他の部分と同じプロセスとツールを使用してデプロイすることができます。

## <a name="when-does-it-make-sense-to-use-containers-with-serverless"></a>サーバーレスでコンテナーを使用することは、どのような場合に適していますか。

Azure 関数は、デプロイされているプラットフォームについての情報を持っていません。 シナリオによっては、特定の要件があり、関数コードを実行する環境をカスタマイズする必要がある場合があります。 依存関係をサポートするカスタムイメージ、または既定のイメージでサポートされていない構成が必要です。 このような場合は、カスタム Docker コンテナーに関数をデプロイするのが理にかなっています。

## <a name="when-should-you-avoid-using-containers-with-azure-functions"></a>Azure Functions でコンテナーを使用しない場合はどうすればよいでしょうか。

従量課金制を使用する場合は、コンテナーで関数を実行することはできません。 さらに、関数を Kubernetes クラスターにデプロイした場合は、Azure Functions によって提供される組み込みのスケーリングを利用できなくなります。 この章の前半で説明した Kubernetes のスケーリング機能を使用する必要があります。

## <a name="how-to-combine-serverless-and-docker-containers"></a>サーバーレスコンテナーと Docker コンテナーを結合する方法

Docker コンテナーで Azure 関数をラップするには、 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)をインストールしてから、次のコマンドを実行します。

```console
func init ProjectName --worker-runtime dotnet --docker
```

プロジェクトが作成されると、Dockerfile とワーカーランタイムがに`dotnet`構成されます。 ここで、関数をローカルで作成してテストできます。 コマンド`docker build`と`docker run`コマンドを使用してビルドし、実行します。 Docker サポートを使用して Azure Functions の構築を開始する詳細な手順については、「[カスタムイメージを使用した Linux での関数の作成](https://docs.microsoft.com/azure/azure-functions/functions-create-function-linux-custom-image)」チュートリアルを参照してください。

## <a name="how-to-combine-serverless-and-kubernetes-with-keda"></a>サーバーレスと Kubernetes を KEDA と組み合わせる方法

Azure functions は、対象とするイベントの割合に基づいて、需要に合わせて自動的にスケールします。 いつでも AKS を利用して関数をホストし、Kubernetes ベースのイベントドリブン自動スケール (KEDA) を使用することができます。 イベントが発生していない場合、KEDA はゼロインスタンスにスケールダウンできます。 [詳細については、KEDA での Azure functions のスケーリングに関するページを参照して](https://docs.microsoft.com/azure/azure-functions/functions-kubernetes-keda)ください。

>[!div class="step-by-step"]
>[前へ](leverage-serverless-functions.md)
>[次へ](deploy-containers-azure.md)
