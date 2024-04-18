---
author: Huang Chao
pubDatetime: 2023-12-16T15:58:53Z
modDatetime: 2023-12-20T13:05:56.066Z
title: "echarts pie图的一些配置项场景"
featured: true
tags:
  - echarts
description: 关于在echarts中针对饼图进行而此配置.
---

## 环境 版本

`react 18` `echarts 5.5.0`

## 基础引用

```js
import * as echarts from "echarts/core";
import {
  TooltipComponent,
  LegendComponent,
  PolarComponent,
  TitleComponent,
  GridComponent,
} from "echarts/components"; //按需
import { LabelLayout } from "echarts/features";
import { CanvasRenderer } from "echarts/renderers";
import { PieChart } from "echarts/charts";
echarts.use([PieChart]);
echarts.use([
  TooltipComponent,
  LegendComponent,
  CanvasRenderer,
  LabelLayout,
  PolarComponent,
  TitleComponent,
  GridComponent,
]); //按需
```

```js
const divRef = useRef < HTMLDivElement > null;
const chartRef = (useRef < echarts.EChartsType) | (null > null);
```

```html
<div ref="{divRef}"></div>
```

### 渲染部分

```js
useEffect(() => {
    if (divRef.current) {
      chartRef.current = echarts.init(divRef.current, undefined, {
        width,
        height,
      });
      chartRef.current!.setOption({});
    }
  }, []);
```
