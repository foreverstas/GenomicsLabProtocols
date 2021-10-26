---
title: "Tris"
author: "Romanov Stanislav"
date: "21 10 2021"
output: html_document
lang: russian
tags: ["Tris","Трис"]
---

### 1M Tris·HCl

{{% notice "info" %}}
трис(гидроксиметил)аминометан - широко используемый буферный агент с буферным интервалом 7.0-9.0. Применяется для приготовления электрофорезных буферов (ТАЕ, ТBЕ и др.), реакционных буферов, буферов для элюции и прочего. Растворы Tris имеют щелочную реакцию, так что буферы с добавлением HCl называют Tris·HCl или Tris·Cl, тогда как буферы без кислоты или с добавлением щелочи (NaOH или KOH) называют Tris Base. В любом случае указывается концентрация Tris, а не добавок.
{{% /notice %}}
{{% notice "info" %}}
Трис ингибирует некоторые клеточные ферменты, такие как аденилатциклаза, пируват дегидрогеназа, уреаза. Ингибирование происходит в т.ч. за счет связывая ионы металлов. Вероятно, поэтому в ряде случаев Tris заменяют на HEPES.
{{% /notice %}}
{{% notice "info" %}}
Производители дают развернутое описание для буферных растворов на основе Трис. В частности, хорошо задокументирована зависимость pH буфера от температуры: чем ниже температура, тем ниже pH. Как правило, изменения pH достигают 0.05 единиц на градус Цельсия. Уменьшение концентрации Трис в 10 раз приводит к уменьшению pH на 0.05 единиц.  Кроме того, производители предостерегают от использования серебряных электродов для измерения pH Трис-буферов, поскольку Трис взаимодействует с серебром с выпадением осадка, что нарушает показани pH-метра. Перед приготовлением раствора стоит убедиться, что прибор приспособлен для работы с буферами на основе Трис.
{{% /notice %}}

![](https://upload.wikimedia.org/wikipedia/commons/8/85/Tris.png?classes=shadow&width=10pc)

Стоковые растворы Tris, как правило, готовят с концентрацией 1 M.

-   Для получения желаемого объема 1М Tris нужно навесить

|                              | 50 мл     | 100 мл     | 200 мл     | 250 мл     | 500 мл     | 1 л                      |
|------------------------------|-----------|------------|------------|------------|------------|--------------------------|
| **Трис основание** (Tris bas | e), Mw 15 | 7.6 \| 7.8 | 8 г \| 15. | 8 г \| 31. | 5 г \| 39. | 4 г \| 78.8 г \| 157.6 г |

-   Добавить 80% от запланированного объема mQ

-   Довести pH до нужной величины добавлением HCl (чтобы понизить pH). Иногда pH требуется поднять, тогда используется NaOH. В процессе добавления щелочи или кислоты раствор будет нагреваться, так что измерения pH нужно проводить до тех пор, пока раствор не дойдет до комнатной температуры.

{{% notice "warning" %}}
Добавление щелочи или соляной кислоты увеличивает осмолярность буфера, что нужно будет учитывать при приготовлении клеточных сред
{{% /notice %}}

-   Довести до запланированного объема добавлением mQ

-   Стерилизовать через 0.22 мкм фильтр. Автоклавировать не стоит, так как это может изменить pH

-   Хранить при КТ

[Описание от Sigma](https://www.sigmaaldrich.com/deepweb/assets/sigmaaldrich/product/documents/301/225/106bbul.pdf)

[Описание на википедии](https://en.wikipedia.org/wiki/Tris)