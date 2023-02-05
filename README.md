# Estudo de Caso: Como uma empresa de tecnologia de bem-estar pode agir com inteligência?

##### Autor: Jailson Barros

##### Data: 23 de Janeiro de 2023

#

_O estudo de caso segue as seis etapas do processo de análise de dados:_

### ❓ [Perguntar](#perguntar)
### 💻 [Preparar](#preparar)
### 🛠 [Processar](#processar)
### 📊 [Analisar](#analisar)
### 📋 [Compartilhar](#compartilhar)
### 🧗‍♀️ [Agir](#agir)


### Cenário

Bellabeat é um fabricante de alta tecnologia de produtos focados na saúde para mulheres. Ela é uma pequena empresa de sucesso, mas tem potencial para se tornar uma das maiores empresas no mercado global de dispositivos inteligentes. Nossa equipe foi solicitada para analisar dados de dispositivos inteligentes para obter informações sobre como os consumidores estão usando seus dispositivos inteligentes. Os insights que iremos descobrir ajudarão a orientar a estratégia de marketing da empresa.


# Perguntar

Tarefa de Negócio: Analisar os dados do rastreador fitness FItBit para obter informações e ajudar a orientar a estratégia de marketing da Bellabeat. 

Partes interessadas:

- Urška Sršen - Cofundador
- Sando Mur - Cofundador
- Equipe de análise de marketing da Bellabeat

# Preparar 

Fonte de dados: FitBit Fitness Tracker Data: https://www.kaggle.com/arashnic/fitbit

O dataset contém 18 arquivos CSV. Cada arquivo representa diferentes dados quantitativos rastreados pelo Fitbit.

Os dados seguem o processo ROCCC ?
_ROCCC - Reliable, Original, Comprehensive, Current, and Cited_

* Confiável: Os dados são de apenas 30 usuários e não temos muitas informações de como ocorreu a coleta dos dados. 
* Original: Os dados foram coletados originalmente pela Amazon Mechanical Murk.
* Abrangente: Os dados abrangem informações sobre as atividades físicas, frequência cardíaca e monitoramento do sono, todos a nível de minuto. Apesar dos dados serem minuciosos na coleta de informações sobre as atividades do usuário, a amostra é pequena e não tem muitas informações sobre o usuário. 
* Atual: Os dados foram coletados em 2016, o que significa que estão desatualizados.Os dados podem não ser úteis agora, os hábitos dos usuários podem ter mudado. 
* Citado: No [link](https://zenodo.org/record/53894#.Y9Byf3bMKUn) do site original contém todas as informações sobre os autores, título, data da publicação, DOI (Digital Object Identifier), fonte e formato do arquivo.

Os dados contém algumas limitações:

* Os dados foram coletados em 2016, podem não ser relevantes para os dias atuais.
* O período de coleta foi muito curto para uma análise mais precisa.
* O tamanho da amostra é pequena para representar toda a população com exatidão.
* Existem 33 IDs únicos, alguns deles com falta de informações ou duplicados.

# Processar

[Volta ao topo](#autor-jailson-barros)

-  [Instalando e carregando os pacotes](#instalando-e-carregando-os-pacotes)
-  [Importando conjuntos de dados](#importando-conjuntos-de-dados)
-  [Visualizando conjunto de dados](#visualizando-conjunto-de-dados)
-  [Limpeza e Formatação](#limpeza-e-formatação)
-  [Junção das tabelas](#junção-das-tabelas)

Nessa fase utilizaremos o R e o R Studio para limpeza e manipulação dos dados.

## Instalando e carregando os pacotes

* Tidyverse - é uma coleção de pacotes em R com uma filosofia de design comum para manipulação, exploração e visualização de dados.
* Here - é um pacote que facilita a referência de arquivos para instalá-lo e carregalá-lo usando a biblioteca.
* Janitor - é um excelente pacote para auxiliar na limpeza de dados.
* Skimr - também é um pacote que auxilia na limpeza de dados, mas ele também ajuda na sumarização de dados de maneira fácil.
* Dplyr - ajuda na manipulação de dados, contém muitas ferramentas para manipular dados de forma simples.
* Lubridate - pacote para ajudar na manipulação de datas. 

```
install.packages("tidyverse")
library(tidyverse)
install.packages("here")
library(here)
install.packages("skimr")
library(skimr)
install.packages("janitor")
library(janitor)
install.packages("dplyr")
library(dplyr)
install.packages("lubridate")
library(lubridate)
```
## Importando conjuntos de dados

Em seguida carregamos os arquivos CSV, as tabelas necessárias. Foi selecionado 3 tabelas: ``daily_activity``, ``sleep_day``  e ``weight``.

```
daily_activity <- read_csv("dailyActivity_merged.csv")
sleep_day <- read_csv("sleepDay_merged.csv")
weight <- read_csv("weightLogInfo_merged.csv")
```
## Visualizando conjunto de dados 

Verificamos se existem valores ausentes ou nulos nos dados e verificamos também a estrutura dos dados em cada tabela.

```
skim(daily_activity)
skim(sleep_day)
skim(weight)
```

Conseguimos observar que existem 940 registros em ``daily_activity``, 413 em ``sleep_day`` e 67 em ``weight``. Não há valores nulos em nenhuma tabela. Existem valores ausentes apenas na coluna ``Fat`` da tabela ``weight``. 

## Limpeza e Formatação

### Consistência em coluna de data

Observamos também que a coluna ``ActivityDate`` da tabela ``daily_activity`` está em formato de caracteres, devemos converter para o formato de Date para ajudar na análise. 

daily_activity <- daily_activity %>% mutate( Weekdays = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
daily_activity <- daily_activity %>% mutate( ActivityDate = as.Date(ActivityDate, "%m/%d/%Y"))

Criamos também uma nova coluna na tabela ``daily_activity`` chamada de ``Weekdays`` para nos ajudar em futuras análises. 

### Verificando duplicatas

Verificamos se existem registros duplicados nas tabelas.

```
sum(duplicated(daily_activity))
sum(duplicated(sleep_day))
sum(duplicated(weight))
```

### Removendo duplicatas

Foi constatado 3 registros duplicados na tabela ``sleep_day``. Devemos removê-los.

```
sleep_day <- sleep_day[!duplicated(sleep_day), ]
```
### Usuários únicos

Agora verificamos se existem mesmo 30 IDs únicos como informa pela pesquisa. 

```
n_distinct(daily_activity$Id)
```

Existem 33 IDs únicos e não 30 como esperado. Isso pode ter ocorrido por alguns motivos, a forma como foi coletado os dados, como foi guardado eles ou até mesmo um usuário que participou da pesquisa acabou com 2 IDs diferentes em determinado momento. 

## Junção das tabelas

Por fim iremos juntas as tabelas para iniciarmos as análises nos dados. 

```
merged_activity_sleep <- merge(daily_activity, sleep_day, by = c("Id"), all=TRUE)
merged_data <- merge(merged_activity_sleep, weight, by = c("Id"), all=TRUE)
```

# Analisar 

[Volta ao topo](#autor-jailson-barros)

Vamos dar uma olhada no resumo das estatísticas dos conjuntos de dados selecionando algumas características importantes para a análise. 

Tabela ``daily_activity``:

```
daily_activity %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes, 
         VeryActiveMinutes, 
         FairlyActiveMinutes, 
         LightlyActiveMinutes
         Calories) %>%
  summary()
```

Algumas descobertas interessantes:

* O tempo médio sedentário é de 991 minutos, cerca de 16 horas.
* A média total de passos por dia é de 7.638.
* A média de tempo em exercícios pesados é de 21,16, em exercícios razoáveis é de 13,56 e em exercícios leves é de 192,8.
* A quantidade média de calorias queimadas por dia foi de cerca de 2362 kcal.

Tabela ``sleep_day``:

```
sleep_day %>%
  select(TotalSleepRecords, 
         TotalMinutesAsleep, 
         TotalTimeInBed) %>%
  summary()
```

Algumas descobertas interessantes:

* Os dormem em média uma vez por dia.
* O tempo médio de sono é de 419,5 minutos ou 7 horas.
* Em média, passam 458,6 minutos na cama, cerca de 7,64 horas. 
* Há uma diferença de cerca de 39 minutos entre o tempo na cama e o tempo de sono

Tabela ``weight``:

```
weight %>%
  select(WeightKg, 
         BMI) %>%
  summary()
```

Algumas descobertas interessantes:

* A média do IMC é de 25,19. 

Não podemos concluir muito apenas com o peso da pessoa, existem vários outros fatores necessários para uma análise mais precisa nessa questão como: altura, sexo, percentual de gordura. Mas se levarmos apenas em consideração o IMC, 25,19 está um pouquinho acima da média considerada saudável que é entre 18 e 24,9.


# Compartilhar

[Volta ao topo](#autor-jailson-barros)

## Registros durante a semana

Podemos observar que a maioria dos registros foram feitos de terça a quinta.

```
ggplot(daily_activity, aes(x=Weekdays, fill = Weekdays)) + 
  geom_bar() +
  labs(x="Dias da Semana", y="Quantidade de Registros", title="Número de registros durante a semana") +
  theme(plot.title = element_text(hjust = 0.4), legend.position="none") +
  scale_color_viridis_d()
```

![Registros](https://user-images.githubusercontent.com/20713140/215345152-d7ce1bac-e891-42bf-9010-426a59d0c098.png)

## Número de passos durante a semana

Terça é o dia em que as pessoas dão mais passos.

```
ggplot(daily_activity, aes(x=Weekdays, y=TotalSteps, fill = Weekdays)) + 
  geom_bar(stat="identity") +
  labs(x="Dias da Semana", y="Quantidade de Passos", title="Número de passos durante a semana") +
  theme(plot.title = element_text(hjust = 0.4), legend.position="none") +
  scale_color_viridis_d()
```

![semana_passos](https://user-images.githubusercontent.com/20713140/215348864-38a8043a-eabd-44f2-bd77-ad1b9a2f37de.png)


## Minutos ativos

Na maior parte do tempo os usuários são sedentários, 81,3% do tempo. Nos minutos de forma ativa os usuários são levemente ativos na maior parte do tempo 15.8%, enquanto 1.7% em razoavelmente ativos e apenas 1.1% em muito ativos. 

```
ggplot(percentage, aes(x = "", y = minutes, fill = level)) +
  geom_col(color = "black") +
  coord_polar(theta = "y") + 
  geom_label(aes(x = 1.6, label = paste(round(minutes,1), "%", sep="")), color = c(1, "white", "white", "white"),
             position = position_stack(vjust = 0.5),
             show.legend = FALSE) +
  guides(fill = guide_legend(title = "Níveis")) +
  ggtitle("Porcentagem dos minutos ativos") +
  scale_fill_viridis_d() +
  theme_void()
```

![file_show](https://user-images.githubusercontent.com/20713140/215345835-f0cd7a29-f49c-470f-829f-1f7e1b2520cc.png)

## Calorias vs Passos

Podemos observar no gráfico a correlação positiva entre os passos dados e a queima de calorias, com alguns outliers. Fica claro no gráfico que a intensidade das calorias queimadas aumenta com o número de passos dados, quanto mais ativos somos, mais calorias queimamos. É um fato óbvio, mas ainda assim interessante para a análise. 

```
ggplot(daily_activity, aes(x=TotalSteps, y=Calories, color=Calories)) + 
  geom_point() + 
  geom_smooth(formula = y ~ x, method = "loess") + 
  labs(title="Quantidade de Passos vs Calorias Queimadas", x="Total de Passos", y="Calorias Queimadas") +
  theme(plot.title = element_text(hjust = 0.4))
```

![calorias-vs-passos](https://user-images.githubusercontent.com/20713140/215346397-eebbe8c0-5e61-435e-bf7f-e561f3322a23.png)


## Tempo dormindo vs Tempo na cama

A relação entre o total de minutos dormindo e o tempo total na cama parece linear, existe uma forte correlação positiva entre eles. O que podemos observar também é uma característica dos outliers, são aquelas pessoas que passam muito tempo na cama, mas não necessariamente dormindo. 


```
ggplot(sleep_day, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) +
  geom_point() +
  geom_smooth(formula = y ~ x, method = "loess") +
  labs(title="Tempo dormindo vs Tempo na cama", x="Total de tempo dormindo (min)", y="Total de tempo na cama (min)") +
  theme(plot.title = element_text(hjust = 0.4))
```

![tempoDormindo-vs-tempoCama](https://user-images.githubusercontent.com/20713140/215347263-8d58f304-ef6a-4a39-89fc-c9440014f30b.png)



# Agir 

[Volta ao topo](#autor-jailson-barros)


Encontramos alguns insights que poderiam ajudar na estratégia de marketing da Bellabeat após analisarmos os dados do FitBit Fitness Tracker

Com base na nossa análise as conclusões foram:

* 81% dos minutos dos usuários são de forma sedentária, uma parcela bastante significativa. Menos de 30min em atividades muito ativas.
* Vemos que o maior número de passos dados é na terça, enquanto que o domingo é o dia que eles dão menos passos. 
* Também terça é o dia que se tem mais registros e vai decaindo durante a semana. 
* Os usuários passam em média 39 minutos na cama antes de adormecer. 


:sleeping: Para incentivar e melhorar os hábitos de sono, a Bellabeat deve considerar o uso de notificações por meio de aplicativos que notificam os usuários sobre a melhor hora para dormir e acordar. Pode também utilizar o Leaf Wellness Tracker como forma de notificação, vibrando para alertar a hora de dormir. 

:runner: Quanto mais exercícios, maior a perda de calorias. A criação de uma campanha com incentivos aos usuários para a prática de exercícios. Podendo utilizar lembretes para lembrar de se exercitar, notificações e utilizando o Leaf Wellness Tracker também. Incentivar os usuários por meio de "objetivos diários", com premiações para quem concluir as atividades.




