---
title: Azure Notebooks 内で Azure Machine Learning を使用する
description: Azure Notebooks で使用できる Azure Machine Learning 用のサンプル ノートブックの概要。
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496634"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>ノートブック内で Azure Machine Learning を使用する

Azure Notebooks は、[Azure Machine Learning](/azure/machine-learning/service/) と連動するために必要な環境で事前構築されています。 サンプル プロジェクトを自分の Notebooks アカウントに複製し、さまざまな機械学習シナリオを試すことが簡単にできます。

## <a name="clone-the-sample-into-your-account"></a>サンプルを自分のアカウントに複製する

1. [Azure Notebooks](https://notebooks.azure.com/) にサインインします。
1. **[マイ プロジェクト]** を選択し、プロジェクト ダッシュボードに移動します。
1. **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** (上向き矢印) ボタンを選択し、 **[Upload GitHub Repository]\(GitHub リポジトリのアップロード\)** ポップアップを開きます。
1. このポップアップで、 **[GitHub リポジトリ]** に「`Azure/MachineLearningNotebooks`」と入力し、 **[プロジェクト名]** でプロジェクトの名前を指定し ("Azure Machine Learning" など)、 **[プロジェクト ID]** で識別子を指定し、必要であれば **[公開]** を選択解除して、 **[インポート]** を選択します。

    ![Azure Machine Learning Notebook サンプルを自分の Notebooks アカウントにインポートする](media/azureml-import-project.png)

1. 数分後、Azure Notebooks から新しいプロジェクトのダッシュボードに自動的に移動します。

## <a name="run-a-sample-notebook"></a>サンプル ノートブックを実行する

1. **[00 - configuration.ipynb]** を選択し、ノートブックの構成セクションを開始し、ノートブックの構成セクションを開始し、その指示に従って Azure Machine Learning ワークスペースを作成します。

    - Azure Notebooks には必要な Python パッケージが既に含まれているため、前提条件の手順 2 でコード スニペットを実行するだけで Azure ML SDK のバージョンを確認できます。

1. 構成の完了後、 **[01.getting-started]** を選択し、13 個の異なるサンプル ノートブックが含まれるフォルダーを開きます。このサンプル ノートブックはいずれも、見れば内容がわかるようになっています。

## <a name="next-steps"></a>次の手順

Azure Machine Learning ドキュメントにはその他の各種リソースが含まれています。このリソースによって、ノートブック内で Machine Learning を段階的に進めることができます。

- [クイック スタート:Python を使用して Azure Machine Learning の利用を開始する](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [チュートリアル 1: Azure Machine Learning でイメージ分類モデルをトレーニングする](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)」を参照してください
- [チュートリアル #2: Azure Container Instances (ACI) に画像分類モデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [チュートリアル:Azure Machine Learning において、自動機械学習で分類モデルをトレーニングする](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 用のドキュメントも参照してください。
