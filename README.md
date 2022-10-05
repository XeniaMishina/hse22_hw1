# hse22_hw1

## Обязательная часть задания 
### Работа с файлами

- Создадим в домашней папке ссылки для доступа к файлам

```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
```

- С помощью команды seqtk выбираем случайно 5 миллионов чтений типа paired-end и 1.5 миллиона чтений типа mate-pairs, установим seed = 621


```
seqtk sample -s621 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s621 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s621 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs1.fastq
seqtk sample -s621 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs2.fastq
```

### Оценка качества чтений 

- С помощью программ fastQC и multiQC оцениваем качество исходных чтений и получаем по ним общую статистику

```
mkdir fastqc
ls sub* matepairs* | xargs -tI{} fastqc -o fastqc {}

mkdir multiqc
multiqc -o multiqc fastqc
```

![Скрин_1](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/general_main_1.png)
![Скрин_2](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/quality_score_main_1.png)


- С помощью программ platanus_trim и platanus_internal_trim подрезаем чтения по качеству и удаляем адаптеры

```
platanus_trim sub*
platanus_internal_trim matepair*
```
- Удаляем исходные .fastq файлы, полученные с помощью программы seqtk
```
rm sub1.fastq
rm sub2.fastq
rm matepairs1.fastq 
rm matepairs2.fastq
```

- С помощью программ fastQC и multiQC оцениваем качество подрезанных чтений и получаем по ним общую статистику
```
mkdir fastqc_trim
ls sub* matepairs*| xargs -tI{} fastqc -o fastqc_trim {}

mkdir multiqc_trim
multiqc -o multiqc_trim fastqc_trim
```

![Скрин_3](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/general_main_2.png)
![Скрин_4](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/quality_score_main_2.png)


### Контиги и скаффолды

- С помощью программы “platanus assemble” собираем контиги из подрезанных чтений
```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
- Далее идет анализ полученных контигов

  Ссылка на Google Colab: https://colab.research.google.com/drive/1dWKTNqT-VrSV0R4vDES8CS4m_1u6RUmv?usp=sharing

- С помощью программы “platanus scaffold” собираем скаффолды из контигов, а также из подрезанных чтений
```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed matepairs2.fastq.int_trimmed 2> scaffold.log
```
- С помощью программы “platanus gap_close” уменьшаем количество гэпов с помощью подрезанных чтений

```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed  matepairs2.fastq.int_trimmed 2> gapclose.log
```

Анализ находится в ноутбуке.

- Удаляем .fastq файлы, содержащие подрезанные чтения
```
rm matepairs1.fastq.int_trimmed
rm matepairs2.fastq.int_trimmed
rm sub1.fastq.trimmed
rm sub2.fastq.trimmed
```

- Далее я перенесла все файлы основного задания в одну папку

```
mkdir hw_1_main
mv Poil* hw_1_main/
mv oil* hw_1_main/
mv *.log hw_1_main/
mv fast* hw_1_main/
mv mult* hw_1_main/
```


## Дополнительная часть
### Возьмем в 2 раза меньше чтений

- Создаю папку extra. В папке делаю то же самое, что и в основной части, но с изменением количества чтений (для paired-end 2_500_000 чтений, для mate-pairs 750_000 чтений)



 - Отчеты до подрезания:
    
    ![Скрин_5](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/general_extra_1.png)
    ![Скрин_6](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/quality_score_extra_1.png)
    
  - Отчеты после подрезания:
    
    ![Скрин_7](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/general_extra_2.png)
    ![Скрин_8](https://github.com/XeniaMishina/hse22_hw1/blob/main/screenshots/quality_score_extra_2.png)

Ссылка на Google Colab та же.
В папках data, reports лежат файлы для обязательной части, но так же они дублируются в этих же директориях в папках main. В папках extra в этих директориях лежат файлы для доп. задания. 

   
