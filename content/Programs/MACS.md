---
title: "MACS"
author: "Romanov Stanislav"
date: "19 10 2022"
output: html_document
lang: russian
tags: ["MACS","MACS2","MACS3","ChIP-seq","Peal Calling", "Программы"]
editor_options: 
  markdown: 
    wrap: 72
    toc: true
---

Один из ключевых этапов ChIP-seq - поиск сайтов связывания (*peak calling*). Алгоритм MACS (*Model-based Analysis of ChIP-Seq*) является одним из наиболее популярных инструменов анализа данных высокопроизводительного секвенирования. Он предназначен для поиска в
геноме районов с достоверным обогащением выравненных прочтений. Хотя изначально алгоритм MACS предназначался для поиска сайтов связывания транскрипционных факторов в данных ChIP-seq, его современная версия тлично подходит для анализа других экспериментов, включая ATAC-seq, DNAse-seq, STARR-seq, MeDIP-seq, MBD-seq и пр.

{{% notice "info" %}}
Программа MACS была представлена в [2008 году](https://doi.org/10.1186/gb-2008-9-9-r137) и с тех пор пережил несколько модификаций. К настоящему времени вышло три поколения программы: `macs`, `macs2` (с 2011 года) и `macs3` (c июня 2022 года). Последняя версия находится в стадии альфа-тестирования и может содержать баги. Отличия между пололениями касаются не только производительности и скорости выполнения, но и внутренней схемы алгоритмов и общей эффективности программы. К сожалению, после 2008 года авторы программы не публиковали статьи с описанием модифицированного алгоритма поиска пиков, так что выяснить особенности алгоритма в современных весриях MACS весьма непросто. Внимательный поиск обсуждений на форумах, лекционных материалов и книг привел меня к выводу, что кроме создателей мало кто понимает, как работают последние версии алгоритма MACS.
{{% /notice %}}

MACS использует информацию о длине фрагментов ДНК, полученных в результате иммунопреципитации, чтобы лучше предсказывать локализацию пиков на профиле, благодаря чему достигается высокая разрешающая способность. В случае если библиотека фрагментов ChIP была отсеквенирована в режимие односторонних прочтений, MACS эмпирически моделирует длину фрагментов ChIP. С помощью распределения Пуассона P<sub>λ</sub>(X) MACS определяет достоверность обогащения выравненных прочтений внутри пиков. Чтобы определить обогащение, MACS может сравнивать образец IP с контролем (либо Input, либо другой IP для определения дифферецниального связывания) либо с рановерным покрытием. В качестве контроля также можно использовать профили ChIP-seq, что позволяет использовать MACS для анализа дифференциального связывания. В момент создания MACS был предназначен для поиска “узких пиков” (*narrow peaks*) - коротких участков генома, с которыми связываются транскрипционные факторы. В последующих ревизиях алгоритм был расширен и теперь дает возможность обнаруживать широкие области распределения (*broad peaks*), характерные для гистоновых модификаций.

{{% notice "warnings" %}}
Установить macs можно только на ОС Linux и OSX
{{% /notice %}}

### Установка MACS2

{{% notice "warning" %}}
По какой-то причине установка macs2 в контейнерах Docker успешно завершается только в Debian, но не в Ubuntu. Потенциально это означает, что на ОС Ubuntu установка невозможна.
{{% /notice %}}

Через Anaconda:

    conda install -c bioconda macs2

Через pip (Python\>3.6):

    pip install macs2

### Установка MACS3

{{% notice "info" %}}
Установка macs3 в контейнерах Docker успешно завершается и в Debian и в Ubuntu.
{{% /notice %}}

Через Anaconda:

    conda install -c maximinio macs3

Через pip (Python\>3.6):

    pip install macs3

### Алгоритм поиска пиков MACS

{{% notice "warnings" %}}
Во время создания алгоритма MACS термин **прочтение** (*read*, *рид*) использовался редко, а технология NGS не давала возможности получать двусторонние прочтения фрагментов ДНК. В документации к MACS вместо "прочтений" используется термин **тег** (*tag*) - то есть любое правильно выравненное на референсный геном прочтение. **Размер тега** (*tag size*) - это длина рида. Если фрагменты ДНК были прочтены с двух сторон, и файл с выравниванием содержит коодинаты выравнивания каждого из спаренных ридов,то под тегом подразумевается первый рид, то есть 5'-конец выравненного фрагмента.
{{% /notice %}}

Основным инструментом MACS является функция callpeak, задачей которой является поиск сайтов связывания. Вызов функции осуществляется из командной строки. В версии macs2 команда выглядит следующим образом:

    macs2 callpeak -t TFILE [необязательные параметры]

А в версии macs3

    macs3 callpeak -t TFILE [необязательные_параметры]

Обязательный параметр `-t` (или синонимичный ему `--treatment`) указывает на путь к файлу файлу вырвавниваний (здесь указан как `TFILE`), который содержит координаты варавнивненных прочтений библиотеки IP. MACS поддерживает форматы файлов `.bam`, `.sam`, `.bed` и файлы выравниваний программы `ELAND` (от разработчиков из Illumina).

{{% notice "tip" %}}
Если выравнивания сохранены в нескольких файлах, то можно указать из одним списком через пробел: `-t TFILE1 TFILE2 TFILE3`. Обратите внимание, повторы обрабатываются отдельно, чтобы обнаруживать воспроизводимые пики.
{{% /notice %}}

По умолчанию программа самостоятельно распознает формат файлов. Однако программа не умеет определять, получен ли файл выравниваний на основе двусторонних или односторонних прочтений. Поэтому но его можно уточнить с помощью опции `-f` (или `--format`). Формат указывается заглавными бугвами.

Значение `-f AUTO` заставляет программу самостоятельно распознавать формат файлов. Опция принимает несколько аргументов, которые сооветствуют форматам файлов:

`-f {AUTO,BAM,SAM,BED,ELAND,ELANDMULTI,ELANDEXPORT,BOWTIE,BAMPE,BEDPE}}`

Поиск пиков в алгоритме MACS происходит в несколько этапов.

#### 1. Устранение избыточности (Remove redundancy)

Последним этапом пробоподготовки библиотек перед секвенированием является ПЦР-обогащение (*library enrichment*). Это не только позволяет увеличить концентрацию библиотек, но так же необходимо для окончательного присоединения секвенирующих адаптеров. Однако в результате ПЦР амплификации фрагменты ДНК неизбежно дуплицируются. Как результат - на карте выравниваний некоторые локусы оказываются перепредставлены, что делает профиль ChIP-seq зашумленным, а также повышает вероятность ложного обнаружени пиков. Чтобы решить эту проблему, выравнивания с одинаковым направлением относительно референса и совпадающими координатами сливаются в одно. Эта процедура обычно называется **удаление ПЦР-дупликатов**.

MACS предоставляет различные возможности для работы с дуплицированными тегами. По умолчанию в каждом локусе сохраняется только одно прочтение. Опция `--auto`, которая очень часто используется, указывает MACS рассчитать максимальное количество тегов в одном и том же месте на основе биномиального распределения с использованием 1e-5 в качестве отсечки pvalue. Альтернативой является установка allопции, которая сохраняет каждый тег. Если integerуказано a, то в одном и том же месте будет храниться не более указанного количества тегов. Эта избыточность последовательно применяется как для чипа, так и для входных отсчетов.

#### Поиск районов с достоверным обогащением

Чтобы определить достоверность обогащения прочтениями внутри потенциальных пиков (P-value), MACS использует распределение Пуассона P<sub>λ</sub>(X), где λ - это ожидаемое число прочтений. Чтобы определить λ, MACS вычисляет покрытие прочтениями в области внутри потенциального пика исходя из случайного распределения прочтений по геному. MACS также оценивает чило прочтений в области вокруг потенциального пика исходя из случайного распределения прочтений по геному или с использованием контрольного образца, если таковой имеется. Далее MACS выбирает наибольшую оценку λ=λ<sup>local</sup> и вычисляет вероятность случайно получить наблюдаемое (k) или большее покрытие внутри потенциального пика P-value=P<sub>λ</sub>(X\>k), . который является ожидаемым число прочтений в рассматриваемой области который зависитиспользуется информация о покрытии в контрольном образце (если такой имеется) или в Алгоритм MACS можно использовать для поиска пиков в экспериментах с контрольными образцами или без них.

#### Поиск широких пиков (*broad peaks*)

Профиль ChIP-seq канонических транскрипционных факторов в большинстве случаев содержат хорошо различимые узкие пики связывания. Однако гистоновые модификаци (H3K27me3, H3K9me3 и пр.) зачастую демонстрируют широкий паттерн распределения. По умолчанию MACS (вплоть до версии 2.0.8) рассматривает пики как обогащенные области генома с шириной 2×d, что удобно для поиска “узких пиков” транскрипционных факторов, но не годится для обнаружения широких доменов.

Начиная с версии 2.0.8 в функции `callpeak` реализована возможность поиска широких пиков, которая включается, если указать опцию `--broad`. В этом случае MACS объединит узкие пики, если они разделены промежутками, в которых достоверность обогащения выше заданного слабого порога `--broad-cutoff`.

{{% notice "info" %}}
Алгоритм поиска широких пиков реализован следующим образом. Сначала MACS стандартным способом ищет "узкие пики": ширина окна 2×d, плюс высокий уровень отсечки по достоверности (указан в параметрах `-p` или `-q`). Узкие пики, которые лежат на расстоянии меньше числа, указанного в параметре `--max-gap` (по умолчанию 50 bp), сливаются. В результате получаются "пики первого уровня". На втором этапе MACS ищет вокруг пиков первого уровня "пики второго уровня" - длинные области обогащения со слабой достоверностью: ширина окна 4×d, уровень отсечки по достоверности понижен и задается параметром `--broad-cutoff` (по умолчанию равен 0.1). Пики второго уровня, которые лежат на расстоянии меньше `4 × max-gap` друг от друга, объединяются. Далее MACS объединяет все пики первого уровня, кторые лежат внутри одного пика второго уровня, в один "широкий пик".
{{% /notice %}}
{{% notice "note" %}}
Важно обратить внимание, что уровень отсечки по достоверности для узких пиков задается либо через P-value (параметр `-p`), либо через FDR (параметр `-q`). Но уровень осечки по достоверности для пиков второго уровня вводится только через один параметр `--broad-cutoff`. Дело в том, что значение, указанное в `--broad-cutoff`, интерпретируется программой в зависимости от того, как происходит выбор достоверных узких пиков. Если для узких пиков уровень отсечки по достоверности определен через значение FDR (использован параметр `-q`), то и `--broad-cutoff` будет интерпретироваться как FDR (отсечка пиков второго уровня будет происходить по значению FDR). Аналогично, если узкие пики отсекаются по значению P-value (параметр `-p`), то `--broad-cutoff` будет интерпретироваться как P-value.
{{% /notice %}}

В резульате включения опции `--broad` MACS вместо одного файла с расширением `.narrowPeak` выводит файлы `.broadPeak` и `.gappedPeak`. Файл `.broadPeak` содержит координаты пиков, уровень обогащения и достоверность. Файл `.gappedPeak` нужен для более подробной визуализации данных в геномном браузере, он представляет широкие пики в виде блоков, на которых можно различить рамположение узких пиков, то есть областей с достоверно высоким обогащением. Файл `.gappedPeak` использует тот же формат визуализации, что и модели генов в файлах `.gtf`, но вместо экзонов демонстрирует координаты узких пиков, а вместо интронов - области слабого обогащения внутри широкого пика. Вершины пиков при этом не изображаются.

### Краткая документация MACS2 (+ отличия в MACS3)

Ниже будет дано краткое описание команд MACS2, представлено сравнение с
MACS3.

{{% notice "info" %}}
Необязательные параметры указаны в квадратных скобках, обязательные без скобок.
{{% /notice %}}

#### Поиск сайтов связывания при помощи `macs2 callpeaks`

Вызов программы выглядит следующим образом:

    macs2 callpeak [-h] -t TFILE [TFILE ...] [-c [CFILE ...]]

Ниже даны стандартные примеры использования:

{{% expand "Поиск сайтов связывания для стандартных ТФ" %}}

В листинге ниже callpeak принимает BAM-файлы выравниваний ондносторонних
прочтений (опция `-f BAM`). Для построения модели Пуассоновского
распределения используется эффективная длина генома человека (опция
`-g hs`). Для поиска достоверных пиков используется предельное значение
FDR=0.01 (`опция -q 0.01`). Названия выходных файлов начинаются со слова
“test” (опция `-n test`).

    macs2 callpeak -t ChIP.bam -c Control.bam -f BAM -g hs -n test -B -q 0.01

С использованием macs3 команда будет выглядеть аналогично (команда будет
начинаться с `macs3 callpeaks`).

{{% /expand %}}

{{% expand "Поиск широких областей обогащения" %}}

В листинге ниже callpeak принимает BAM-файлы выравниваний ондносторонних
прочтений (опция `-f BAM`). Для построения модели Пуассоновского
распределения используется эффективная длина генома человека (опция
`-g hs`). Для поиска достоверных пиков используется предельное значение
FDR=0.01 (`опция -q 0.01`). Названия выходных файлов начинаются со слова
“test” (опция `-n test`).

    macs2 callpeak -t ChIP.bam -c Control.bam --broad -g hs --broad-cutoff 0.1

С использованием macs3 команда будет выглядеть аналогично (команда будет
начинаться с `macs3 callpeaks`).

{{% /expand %}}

    macs2 callpeak -t ChIP.bam -c Control.bam --broad -g hs --broad-cutoff 0.1

### Дополнительные источники

[Zhang et al. - 2008 - Model-based Analysis of ChIP-Seq (MACS)](https://doi.org/10.1186/gb-2008-9-9-r137)

[Feng et al. - 2012 - Identifying ChIP-seq enrichment using MACS](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3868217/)

[Официальный репозиторий GitHub](https://github.com/macs3-project/MACS)

[Документация от разработциков](https://macs3-project.github.io/MACS/)
