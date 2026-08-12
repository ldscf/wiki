---
source_title: Helm ABC
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2024-07-26T03:17:54Z'
---
yaml 中的语法、函数。

### 控制结构
 ```

# values.yaml
favorite:
  drink: coffee
  food: pizza
pizzaToppings:
  - mushrooms
  - cheese
  - peppers
  - onions

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: <!-- template:  $.Release.Name  -->-configmap
data:
  myvalue: "Hello World"
  <!-- template: - with .Values.favorite  -->
  drink: <!-- template:  .drink ,  default "tea" ,  quote  -->
  food: <!-- template:  .food ,  upper ,  quote  -->
  <!-- template:  if eq .drink "coffee"  -->mug: "true"<!-- template:  end  -->
  <!-- template: - end  -->
  toppings: |-
    <!-- template: - range .Values.pizzaToppings  -->
    - <!-- template:  . ,  title ,  quote  -->
    <!-- template: - end  -->

# Output
apiVersion: v1
kind: ConfigMap
metadata:
  name: edgy-dragonfly-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"
  mug: "true"
  toppings: |-
    - "Mushrooms"
    - "Cheese"
    - "Peppers"
    - "Onions"
```

#### if
 ```
<!-- template: - if PIPELINE  -->

# Do something
<!-- template: - else if OTHER PIPELINE  -->

# Do something else
<!-- template: - else  -->

# Default case
<!-- template: - end  -->
  # if 写成换行，就需要用 <!-- template: - 格式删除空格/回车
  <!-- template: - if eq $.Values.favorite.drink "coffee"  -->
  mug: "true"
  <!-- template: - end  -->
  # 要确保 <!-- template: - 和其他命令之间有一个空格。{{- 3  --> 表示“删除左边空格并打印3”，而 <!-- template: -3  --> 表示“打印 -3 ”。
```

#### with

用来指定范围，PIPELINE 值不为空时执行
 ```
<!-- template: - with PIPELINE  -->

# restricted scope
<!-- template: - end  -->

# 修改配置映射中的 . 的作用域指向 .Values.favorite

# .drink --> .Values.favorite.drink

# $.Release --> .Release
```

#### range

range 方法遍历列表(list)、元组(tuple )、map、dict 等多值结构，若不指定变量(如：列表、元组)，则 . 为当前的值。
- range 内部会改变变量路径，相当于 with。解决办法是预先定义变量，或者使用 $. 绝对路径
- 在 Helm 模板中，$index 内置变量会在 range 循环内自动生成，代表着循环中当前迭代的索引
 ```
  ## 1
  toppings: , -
    <!-- template: - range $index, $topping := .Values.pizzaToppings  -->
    <!-- template:  $index  -->: <!-- template:  $topping  -->
    <!-- template: - end  -->
  ## 1 - output
  toppings: , -
    0: mushrooms
    1: cheese
    2: peppers
    3: onions 
  ## 2
  sizes: , -
    <!-- template: - range tuple "small" "medium" "large"  -->
    - <!-- template:  .  -->
    <!-- template: - end  -->
  ## 2 - output
  sizes: , -
    - small
    - medium
    - large
```

#### Sample

需要说明的是默认值：如果值是 true/false，{{ ...| default false }} 显然是正确的(default true 不合适)，否则当原值不存在，或者是 false，都会触发 default。
 ```

# values.yaml
loop:
  - 1
  - 2
enable:
  pizza: true
  baozi: false
favorite:
  drink: coffee
  food: pizza
pizzaToppings:
  - mushrooms
  - cheese
  - peppers
  - onions

# templates/configmap.yaml
<!-- template: - $Release        := .Release.Name  -->
{{- $enable         := .Values.enable.pizza ,  default false  -->
<!-- template: - $favorite       := .Values.favorite  -->
<!-- template: - $pizzaToppings  := .Values.pizzaToppings  -->
<!-- template: - if $enable  -->
<!-- template: - range $loop1 := .Values.loop  -->
apiVersion: v1
kind: ConfigMap
metadata:
  name: <!-- template:  $Release  -->-configmap-<!-- template:  $loop1  -->
  myvalue: "Hello World"
  <!-- template: - range $ky, $val := $favorite  -->
  <!-- template:  $ky  -->: <!-- template:  $val  -->
  <!-- template: - end  -->
toppings: |-
  <!-- template: - range $pizzaToppings  -->
  - <!-- template:  . ,  title  -->
  <!-- template: - end  -->
---
<!-- template: - end  -->
<!-- template: - end  -->
```

### 数据

#### Dict
 ```

# values.yaml
podLabels: 
  a: 1
  b: 2
-.OR.-
podLabels: {a: 1, b: 2}

# deployment.yaml
      labels:
        <!-- template: - include "dftrd.labels" . ,  nindent 8  -->
        <!-- template: - with .Values.podLabels  -->
        <!-- template: - toYaml . ,  nindent 8  -->
        <!-- template: - end  -->

# Output
      labels:
        helm.sh/chart: dftrd-0.1.0
        app.kubernetes.io/name: dftrd
        app.kubernetes.io/instance: dftrd
        app.kubernetes.io/version: "1.16.0"
        app.kubernetes.io/managed-by: Helm
        a: 1
        b: 2
```

### 函数

Helm 函数的一般建议使用方法是类似于管道的方法，如：
- .Values.pizza | default false
- .Values.pizza | quote

| 分类 | 名称 | 功能 | 备注 |
|:---|:---|:---|:---|
| + |
| 字符串 | quote | 转为字符串 |
| default | 默认值 | 空、0、false 等均被判定 |

### Error
- Error: YAML parse error on ??.yaml: error converting YAML to JSON: yaml: line 8: did not find expected key

一般此种情况，多是缩进格式问题
