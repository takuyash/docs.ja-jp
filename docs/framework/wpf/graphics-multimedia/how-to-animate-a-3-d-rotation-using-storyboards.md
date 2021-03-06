---
title: '方法: ストーリーボードを使用して 3D 回転をアニメーション化する'
ms.date: 03/30/2017
helpviewer_keywords:
- Storyboards [WPF]
- 3D translations [WPF], animating [WPF], with Storyboards
- animation [WPF], 3D translations [WPF], with Storyboards
ms.assetid: 1020e44e-e21e-49a8-be53-53cbc1910e83
ms.openlocfilehash: 088f1a33cfc73a706ffed55ffff6494adaf8fca4
ms.sourcegitcommit: 267d092663aba36b6b2ea853034470aea493bfae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112216"
---
# <a name="how-to-animate-a-3d-rotation-using-storyboards"></a>方法: ストーリーボードを使用して 3D 回転をアニメーション化する
次の例は、オブジェクトのプロパティと<xref:System.Windows.Media.Media3D.AxisAngleRotation3D.Angle%2A><xref:System.Windows.Media.Media3D.AxisAngleRotation3D.Axis%2A>プロパティをアニメーション化して、3D オブジェクトを回転させながら、オブジェクトを 「<xref:System.Windows.Media.Media3D.AxisAngleRotation3D>ぐらつく」 方法を示しています。 この<xref:System.Windows.Media.Media3D.AxisAngleRotation3D>オブジェクトは 3D オブジェクトの回転変換を指定するため、そのプロパティをアニメートすると、欲望の回転効果が作成されます。 ストーリーボード内では、<xref:System.Windows.Media.Animation.DoubleAnimation><xref:System.Windows.Media.Media3D.AxisAngleRotation3D.Angle%2A>プロパティをアニメーション化するために使用され、プロパティ<xref:System.Windows.Media.Animation.Vector3DAnimation>はアニメーション化に使用されます<xref:System.Windows.Media.Media3D.AxisAngleRotation3D.Axis%2A>。  
  
## <a name="example"></a>例  
 [!code-xaml[Animation3DGallery_snip#Rotate3DUsingAxisAngleRotation3DExampleWholePage](~/samples/snippets/csharp/VS_Snippets_Wpf/Animation3DGallery_snip/CS/Rotat3DUsingAxisAngleRotation3DExample.xaml#rotate3dusingaxisanglerotation3dexamplewholepage)]  
  
## <a name="see-also"></a>関連項目

- [回転 3D アニメーションを使用して 3D 回転をアニメーション化する](how-to-animate-a-3-d-rotation-using-rotation3danimation.md)
- [キー フレームを使用して 3D 回転をアニメーション化する (回転 3D アニメーションを使用してキー フレーム)](how-to-animate-a-3-d-rotation-using-key-frames.md)
- [3D グラフィックスの概要](3-d-graphics-overview.md)
- [ストーリーボードの概要](storyboards-overview.md)
