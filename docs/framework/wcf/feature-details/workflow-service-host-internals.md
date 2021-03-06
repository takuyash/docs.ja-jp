---
title: ワークフロー サービス ホストの内部
ms.date: 03/30/2017
ms.assetid: af44596f-bf6a-4149-9f04-08d8e8f45250
ms.openlocfilehash: b95a59b0e1715b3cc18ccfea44d6c4ccd04ca5ad
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2020
ms.locfileid: "79184176"
---
# <a name="workflow-service-host-internals"></a>ワークフロー サービス ホストの内部
<xref:System.ServiceModel.WorkflowServiceHost> では、ワークフロー サービスのホストが提供されます。 これにより、受信メッセージをリッスンして、該当するワークフロー サービス インスタンスにルーティングし、アイドル状態のワークフローのアンロードおよび永続化を制御できます。 このトピックでは、WorkflowServiceHost がどのように受信メッセージを処理するかについて説明します。  
  
## <a name="workflowservicehost-overview"></a>WorkflowServiceHost の概要  

<xref:System.ServiceModel.WorkflowServiceHost> クラスは、ワークフロー サービスをホストするために使用されます。 このクラスは、受信メッセージをリッスンして、該当するサービス インスタンスにルーティングし、必要に応じて、新しいインスタンスを作成するか、永続ストレージから既存のインスタンスを読み込みます。 次の図は、動作の概要を<xref:System.ServiceModel.WorkflowServiceHost>示しています。
  
 ![ワークフロー サービス ホストの概要を示す図。](./media/workflow-service-host-internals/workflow-service-host-high-level-overview.gif)  
  
 この図では、<xref:System.ServiceModel.WorkflowServiceHost> は .xamlx ファイルからワークフロー サービス定義を読み込み、構成ファイルから構成情報を読み込みます。 また、追跡プロファイルから追跡構成を読み込みます。 <xref:System.ServiceModel.WorkflowServiceHost> によって、ワークフロー インスタンスへの制御操作の送信を可能にするワークフロー コントロール エンドポイントが公開されます。  詳細については、「[ワークフロー コントロール エンドポイントのサンプル](../../../../docs/framework/wcf/feature-details/workflow-control-endpoint.md)」を参照してください。  
  
 <xref:System.ServiceModel.WorkflowServiceHost> によって、受信アプリケーション メッセージをリッスンするアプリケーション エンドポイントも公開されます。 受信メッセージが到着すると、該当するワークフロー サービス インスタンスに送られます (現在読み込み中の場合)。 必要に応じて、新しいワークフロー インスタンスが作成されます。 既存のインスタンスが永続化されている場合は、永続ストアから読み込まれます。  
  
## <a name="workflowservicehost-details"></a>WorkflowServiceHost の詳細  
 次の図は、<xref:System.ServiceModel.WorkflowServiceHost>メッセージを処理する方法をもう少し詳しく示しています。  
  
 ![ワークフロー サービス ホストメッセージ フローを示す図。](./media/workflow-service-host-internals/workflow-service-host-message-flow.gif)  
  
 この図には、3 つの異なるエンドポイント (アプリケーション エンドポイント、ワークフロー コントロール エンドポイント、およびワークフローをホストするエンドポイント) が示されています。 アプリケーション エンドポイントは、特定のワークフロー インスタンスにバインドされたメッセージを受信します。 ワークフロー コントロール エンドポイントは、制御操作をリッスンします。 ワークフローをホストするエンドポイントは、<xref:System.ServiceModel.WorkflowServiceHost> がサービス以外のワークフローを読み込んで実行するためのメッセージをリッスンします。 この図が示すように、すべてのメッセージは、WCF ランタイム中に処理されます。  ワークフロー サービス インスタンスの調整は、<xref:System.ServiceModel.Description.ServiceThrottlingBehavior.MaxConcurrentInstances%2A> プロパティを使用して実現されます。 このプロパティは、同時ワークフロー サービス インスタンスの数を制限します。 この調整を超過すると、新しいワークフロー サービス インスタンスの追加要求または永続化されたワークフロー インスタンスのアクティブ化の要求がキューに置かれます。 キューに置かれた要求は、新規インスタンスと実行中の永続化されたインスタンスのどちらに対するものであるかにかかわらず、FIFO 順で処理されます。 未処理の例外の処理方法およびアイドル状態のワークフロー サービスのアンロードおよび永続化方法を指定するホスト ポリシー情報が読み込まれます。 これらのトピックの詳細については、「[方法 : WorkflowServiceHost を使用してワークフローハンドルされない例外動作を構成](../../../../docs/framework/wcf/feature-details/config-workflow-unhandled-exception-workflowservicehost.md)[する」および「方法: WorkflowServiceHost を使用してアイドル状態の動作を構成する](../../../../docs/framework/wcf/feature-details/how-to-configure-idle-behavior-with-workflowservicehost.md)」を参照してください。 ワークフロー インスタンスは、ホスト ポリシーに従って永続化され、必要に応じて再度読み込まれます。 ワークフローの永続性の詳細については、「[方法 : WorkflowServiceHost を使用して永続性を構成](../../../../docs/framework/wcf/feature-details/how-to-configure-persistence-with-workflowservicehost.md)する 」、[実行時間の長いワークフロー サービスの作成](../../../../docs/framework/wcf/feature-details/creating-a-long-running-workflow-service.md)、および[ワークフローの永続性](../../../../docs/framework/windows-workflow-foundation/workflow-persistence.md)を参照してください。  
  
 次の図は、ワークフロー サービスホスト.Open が呼び出されたときのフローを示しています。  
  
 ![ワークフローサービスホスト.Openが呼び出されたときのフローを示す図。](./media/workflow-service-host-internals/workflow-service-host-open.gif)  
  
 ワークフローが XAML から読み込まれ、アクティビティ ツリーが作成されます。 <xref:System.ServiceModel.WorkflowServiceHost> は、アクティビティ ツリーに従い、サービスの記述を作成します。 構成がホストに適用されます。 最後に、ホストは受信メッセージのリッスンを開始します。  
  
 次の図は<xref:System.ServiceModel.WorkflowServiceHost>、CanCreateInstance が に設定されている受信アクティビティに対してバインドされたメッセージを受信したときに`true`、 が何を行うかを示しています。  
  
 ![WFS ホストがメッセージを受信したときに使用するデシジョン ツリーであり、CanCreateInstance は true です。](./media/workflow-service-host-internals/workflow-service-host-receive-message-cancreateinstance.gif)  
  
 メッセージが到着し、WCF チャネル スタックによって処理されます。 スロットルがチェックされ、関連付けクエリが実行されます。 既存のインスタンスにバインドされているメッセージは配信されます。 新しいインスタンスを作成する必要がある場合は、Receive アクティビティの CanCreateInstance プロパティが確認されます。 true に設定されている場合、新しいインスタンスが作成され、メッセージが配信されます。  
  
 次の図は、CanCreateInstance が false に設定された場合の Receive アクティビティにバインドされたメッセージを受信したときの <xref:System.ServiceModel.WorkflowServiceHost> の動作を示しています。  
  
 ![WFS ホストがメッセージを受信したときに使用するデシジョン ツリーが False です。](./media/workflow-service-host-internals/workflow-service-host-receive-message.gif)  
  
 メッセージが到着し、WCF チャネル スタックによって処理されます。 スロットルがチェックされ、関連付けクエリが実行されます。 メッセージが既存のインスタンスにバインドされている場合は (CanCreateInstance が false に設定されているため) インスタンスが永続ストアから読み込まれ、ブックマークが再開され、ワークフローが実行されます。  
  
> [!WARNING]
> SQL Server が NamedPipe プロトコルでのみリッスンするように設定されている場合、ワークフロー サービス ホストは開きません。  
  
## <a name="see-also"></a>関連項目

- [ワークフロー サービス](../../../../docs/framework/wcf/feature-details/workflow-services.md)
- [ワークフロー サービスのホスティング](../../../../docs/framework/wcf/feature-details/hosting-workflow-services.md)
- [ワークフロー コントロール エンドポイント](../../../../docs/framework/wcf/feature-details/workflow-control-endpoint.md)
- [ワークフロー サービスの未処理の例外動作を構成する方法](../../../../docs/framework/wcf/feature-details/config-workflow-unhandled-exception-workflowservicehost.md)
- [長時間のワークフロー サービスの作成](../../../../docs/framework/wcf/feature-details/creating-a-long-running-workflow-service.md)
- [ワークフローの永続性](../../../../docs/framework/windows-workflow-foundation/workflow-persistence.md)
