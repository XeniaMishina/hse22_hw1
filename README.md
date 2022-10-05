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

<!---  Или блокнот вы можете найти здесь: https://github.com/Laitielly/hse22_hw1/blob/main/src/HW22_1.ipynb
--->

- С помощью программы “platanus scaffold” собираем скаффолды из контигов, а также из подрезанных чтений
```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed matepairs2.fastq.int_trimmed 2> scaffold.log
```
- С помощью программы “platanus gap_close” уменьшаем количество гэпов с помощью подрезанных чтений

```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed  matepairs2.fastq.int_trimmed 2> gapclose.log
```

<!--- Ссылка на ноутбук та же. --->

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

<!--- 
В папке MainTask создала папку platanus и перенесла туда файлы, созданные программой platanus.

```
mkdir platanus
mv Poil* platanus/
mv *.log platanus/
```

## Дополнительная часть
### Дополнительная сборка 1. Чтений в 2 раза меньше.

- Создаю папку extratask, в ней seq1. В папке seq1 выполняем ровно те же действия, но с изменением кол-ва чтений (для paired-end 2_500_000 чтений, для mate-pairs 750_000 чтений.

- Ссылка на Google Colab та же.

 - Статистики:
    - До ![Скрин5](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/total3.png)
    - После ![Скрин6](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/total4.png)
    - До ![Скрин7](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/quality%20scores3.png)
    - После ![Скрин8](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/quality%20scores4.png)

### Дополнительная сборка 2. Чтений в 4 раза меньше.

- В папке extratask папка seq2. В папке seq2 выполняем ровно те же действия, но с изменением кол-ва чтений (для paired-end 1_250_000 чтений, для mate-pairs 375_000 чтений.

- Ссылка на Google Colab та же.

 - Статистики:
    - До 
    ![Скрин9](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/total5.png)
    - После 
    ![Скрин10](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/total6.png)
    - До 
    ![Скрин11](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/quality%20scores5.png)
    - После 
    ![Скрин12](https://github.com/Laitielly/hse22_hw1/blob/main/screenshots/quality%20scores6.png)
    
    
    
 ### Выводы
 - В блокноте выведены графики, выводы как раз под ними.
 --->
