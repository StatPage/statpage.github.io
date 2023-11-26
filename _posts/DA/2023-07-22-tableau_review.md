---
layout: single
title:  "태블로 Certified Data Analyst 시험 후기"
toc: true
toc_sticky: true
# categories: tableau
# tags: [tableau, certified data analyst, 자격증]
---

# 들어가며

데이터 분석에 관심이 있을 만한 분들은 어쩌다가 한 번씩 `태블로(Tableau)`라는 단어를 듣게 된다.

태블로는 시각화 도구로 개인 작업에서는 시간 절약을/ 팀 차원에서는 신속한 의사 결정을/ 회사 및 기관에서는 데이터 기반 기업 문화 정착을 돕는 것으로 활용된다고 한다. (회사의 말에 따르면)

그와 관련된 자격증으로 여러 개가 있지만 나는 개인적인 사정으로 `Tableau Certified Data Analyst`라는 자격증을 따야 하는 상황이다. 그래서 그와 관련된 정보와 자료를 정리하고 시험 후기까지 다른 사람들에게 공유할 수 있도록 블로그 포스팅으로 남길 생각이다.

2주 안에 개인적으로 공부해서 시험을 치르는 만큼 내가 잘 배웠는지 알기 위해 누군가에게 설명하는 글을 쓰다보면서 제대로 이해했는지 확인을 해야 겠다.

![Untitled](https://github.com/StatPage/AI-programming-class/assets/61931924/4cbc7b1b-ca7c-4516-b8b3-a2864b289895){: .align-center}


# Certified Data Analyst 공식 가이드

링크를 따라 들어가면 Tableau Certified Data Analyst 시험 공식 가이드 내용이 있다.

총 16페이지 정도 되는 내용인데 읽어보고 액기스를 정리하도록 하겠다.

(참고로 자격증 유효 기간은 2년이라고 한다.)

## 타겟층

>  A Tableau Data Analyst enables stakeholders to make business decisions by understanding the business problem, identifying data to explore for analysis, and delivering actionable insights.


Tableau Data Analyst는 데이터를 탐구해서 비즈니스 문제 이해, 분석을 위한 데이터 식별, 인사이트 제공을 가능하게 만들어준다고 한다.

사용하는 툴은 총 네 가지다. (시험에서 다루는 도구들이라고 생각하자.)

- Tableau Desktop
- Tableau Prep
- Tableau Server
- Tableau Online

그리고 이 도구들로 다음과 같은 작업을 한다고 한다. (이게 시험 범위일 것이다.)

- Connect to data sources
- Perform data transformations
- Explore and analyze data
- Create meaningful visualizations that answer key business questions

마지막으로 이 시험에서 응시자의 다음과 같은 요건들을 본다고 한다. (시험 공부의 방향성일 것이다.)

- **Knowledge** of the capabilities of Tableau Desktop, Tableau Prep, and either Tableau Server or Tableau Online
- **Ability** to share content and keep the content current by publishing, scheduling, and maintaining it on the web

결국은 **위의 툴들 어떻게 사용하는지 아는 것**이랑 그렇게 **만든 결과물을 어떻게 다루는지** 중점적으로 보려는 것 같다.

![Untitled 1](https://github.com/StatPage/AI-programming-class/assets/61931924/744ef49d-629d-4e65-8e3b-04981219cbbb){: .align-center}


## 시험 방식

- 시간: 2시간 (비밀유지계약서(NDA) 검토 3분이랑 튜토리얼 5분 포함)
- 체크인: 시험 30분 전부터 가능
- 질문 유형
    - 지식 기반(문항 30개, 핸즈온 1개 with 10-11 tasks): Multiple choice, Multiple selection, and Active screen (includes Build list, Drag and drop, and Hot area)
    - 실습 기반(문항+핸즈온 15개): Hands-on lab
- **합격 점수: 750점**
- 점수 확인: 이틀 안에 메일로 결과가 나온다.
    1. Log-in
    2. GO TO PEARSON
    3. Click my Exam History
- 언어: 영어
- 응시 방법: Online Delivery (Testing Center도 있다)

## 시험 전 확인 사항

아무런 이슈 없이 시험을 잘 치기 위해서 컴퓨터, 네트워크, 세팅이 잘 됐는지 파악하자. [Technical Requirements](https://home.pearsonvue.com/op/OnVUE-technical-requirements)도 한 번씩 확인해주고 핸즈온 문제도 원활하게 치기 위해서 큰 화면 사용도 추천하고 있다. 핸즈온 문제를 어떻게 푸는지 튜토리얼 비디오가 있는데 [링크](https://www.tableau.com/sites/default/files/2022-03/PBT%20tutorial.mp4)에서 한 번씩 확인하고 넘어가면 좋을 것 같다.

## Content Outline

말 그대로 시험 비중을 의미한다. 비중은 아래와 같다.

1. Connect to and Transform Data: **24%**
2. Explore and Analyze Data: **41%**
3. Create Content: **26%**
4. Publish and Manage Content on Tableau Server and Tableau Online: **9%**

## 연습 문제

실제 태블로 측에서 응시자들이 감을 잡으라고 연습 문제도 제공하고 있다. 실제로 응시했을 때 문제들도 보면 거의 비슷해서 한 번씩 훑어보면 시험 문제 때 금방 감 잡고 풀 것 같다.

### Practice Question 1

You have a dataset that contains a list of gym equipment and the exercises that use them.

![Untitled 2](https://github.com/StatPage/AI-programming-class/assets/61931924/b7dc0556-31a6-45a6-b079-fce6702a2029){: .align-center}

Which formula should you use to get the number of items in the equipment list?

a) MAX ( \[Equipment\] )

b) COUNT ( \[Equipment\] )

c) COUNTD ( \[Equipment\] )

d) COVAR ( \[Equipment\] )

e) ATTR ( \[Equipment\] )

### Practice Question 2

You have the following dataset:

![Untitled 3](https://github.com/StatPage/AI-programming-class/assets/61931924/586ca727-021a-46b1-9433-1d2c560b667f){: .align-center}

You want to create the following pie chart.

![Untitled 4](https://github.com/StatPage/AI-programming-class/assets/61931924/ed9af051-c629-44da-8557-1fe2bc213276){: .align-center}

How should you configure the marks for the pie chart?

a) Drag Revenue to Color, Item to Label, and Item to Angle.

b) Drag Item to Color, Item to Label, and Revenue to Angle.

c) Drag Item to Size, Item to Label, and Revenue to Detail.

d) Drag Revenue to Color, Revenue to Label, and Item to Detail.

## 도메인 정리

pdf에도 나와있듯이 이 리스트는 단순 나열이 아니라 시험 범위에 관해 뭘 알아야하는지 정확히 알려주는 리스트라고 생각하면 좋을 것 같다.

### Domain 1: Connect to and Transform Data

1. Connect to data sources
    1. [Choose an appropriate data source](https://blog.statbrand.com/entry/Tableau-111-Choose-an-appropriate-data-source)
    2. Choose between live connection or extract
    3. Connect to extracts
    4. Connect to spreadsheets
    5. Connect to .hyper files (or .tde files)
    6. Connect to relational databases
    7. Pull data from relational databases by using custom SQL queries
    8. Connect to a data source on Tableau Server
    9. Replace the connected data source with another data source for an existing chart or sheet
2. Prepare data for analysis
    1. Assess data quality (completeness, consistency, accuracy)
    2. Perform cleaning operations
    3. Organize data into folders Tableau Certified Data Analyst
    4. Use multiple data sources (establish relationships, create joins, union tables, blend data)
    5. Prepare data by using Data Interpreter, pivot, and split
    6. Create extract filters
3. Perform data transformation in Tableau Prep
    1. Choose which data transformation to perform based on a business scenario
    2. Combine data by using unions
    3. Combine data by using joins
    4. Shape data by using aggregations
    5. Perform filtering
    6. Shape data by using pivots
4. Customize fields
    1. Change default field properties (types, sorting, etc.)
    2. Rename columns
    3. Choose when to convert between discrete and continuous
    4. Choose when to convert between dimension and measure
    5. Create aliases

### Domain 2: Explore and Analyze Data

1. Create calculated fields
    1. Write date calculations (DATEPARSE, DATENAME…)
    2. Write string functions
    3. Write logical and Boolean expressions (If, case, nested, etc.)
    4. Write number functions
    5. Write type conversion functions
    6. Write aggregate functions
    7. Write FIXED LOD calculations
2. Create quick table calculations
    1. Moving average
    2. Percent of total
    3. Running total Tableau Certified Data Analyst
    4. Difference and percent of difference
    5. Percentile
    6. Compound growth rate
3. Create custom table calculations
    1. Year to date
    2. Month to date
    3. Year over year
    4. Index
    5. Ranking
    6. First-last
4. Create and use filters
    1. Apply filters to dimensions and measures
    2. Configure filter settings including Top N, Bottom N, include, exclude, wildcard, and conditional
    3. Add filters to context
    4. Apply filters to multiple sheets and data sources
5. Create parameters to enable interactivity
    1. In calculations
    2. With filters
    3. With reference lines
6. . Structure the data
    1. Sets
    2. Bins
    3. Hierarchies
    4. Groups
7. Map data geographically
    1. Create symbol maps
    2. Create heat maps
    3. Create density maps
    4. Create choropleth maps (filled maps)
8. Summarize, model, and customize data by using the Analytics feature
    1. Totals and subtotals
    2. Reference lines
    3. Reference bands
    4. Average lines
    5. Trend lines
    6. Distribution bands
    7. Forecast by using default settings
    8. Customize a data forecasting model
    9. Create a predictive model

### Domain 3: Create Content

1. Create charts
    1. Create basic charts from scratch (bar, line, pie, highlight table, scatter plot, histogram, tree map, bubbles, data tables, Gantt, box plots, area, dual axis, combo)
    2. Sort data (including custom sort)
2. Create dashboards and stories
    1. Combine sheets into a dashboard by using containers and layout options
    2. Add objects
    3. Create stories
3. Add interactivity to dashboards
    1. Apply a filter to a view
    2. Add filter, URL, and highlight actions
    3. Swap sheets by using parameters or sheet selector
    4. Add navigation buttons
    5. Implement user guiding sentences (click…, hover…, menu options)
4. Format dashboards
    1. Apply color, font, shapes, styling
    2. Add custom shapes and color palettes
    3. Add annotations
    4. Add tooltips
    5. Apply padding
    6. Remove gridlines, row-level and column-level bands, and shading
    7. Apply responsive design for specific device layouts

### Domain 4: Publish and Manage Content on Tableau Server and Tableau Online

1. Publish Content
    1. Publish a workbook
    2. Publish a data source
    3. Print content
    4. Export content
2. Schedule data updates
    1. Schedule data extract refreshes
    2. Schedule a Tableau Prep workflow
3. Manage Published workbooks
    1. Create alerts
    2. Create subscriptions
    

# 시험 준비

그래서 공식 자료 태블로 시험 링크([Link](https://www.tableau.com/learn/certification/certified-data-analyst))에 들어가면, `PREPARE FOR EXAM` 이라고 시험 합격을 위한 리소스를 제공해주는 곳이 있다. 그런데 백 만원이 넘기 때문에 너무 비싸다. 그래서 **인터넷에서 무료로 활용할 수 있는 정보들을 활용**했다.

## 학습 자료

나는 우선 다음 링크들의 정보들을 활용했다.

- [E-learning: Analyst Learning Path](https://elearning.tableau.com/): 원래는 이것도 돈을 내고 사용해야하지만 학생은 1년간 무료로 사용할 수 있어서 이걸 중심으로 학습했다(태블로 제품도 마찬가지로 학생은 1년간 무료로 사용이 가능하다.)
- [Tableau Certified Data Analyst Study Guide](https://learningtableau.com/data-analyst-study-guide/): 백과사전이라고 생각하자. 처음 다루는 tool이기 때문에 익숙치 않은 용어가 있거나 특히 기능이 떠오르지 않을 때 목차를 한 번 쭉 훑어보면 필요한 내용을 금방 찾을 수 있다.
- 구글 검색과 [Tableau community](https://www.community.tableau.com/s/): 가끔씩 자료에는 없는 내용들, 응용 방법들을 구글에 검색해야 할 때가 있다. 그 때 태블로 커뮤니티에서 많은 질문과 해결 방법을 볼 수 있다. 물론 다 정답만 있는 것은 아니지만 ‘아 이렇게 할 수 있구나. 다른 방법은 없을까?’ 등의 생각을 할 수 있어서 생각을 환기시킬 수 있다.

## 공부 방법

우선 E-learning을 하나씩 들어보면서 태블로에 대해 익숙해지는 시간을 가졌다. 중간중간 나오는 내용을 직접 해보고 연습 문제도 풀어봤다. 이걸 완벽하게 이해하겠다는 것보다 체험한다는 생각으로 가볍게 쭉 보고 시험에 응시했다.

# 시험 후기

![Untitled 5](https://github.com/StatPage/AI-programming-class/assets/61931924/f1791935-7479-441b-961e-32401ebe06fc){: .align-center}

말하기 좀 그렇지만 사실 나는 한 번 시험에 떨어졌었다ㅠㅠ 

합격은 일주일 뒤에 재시험을 보고 받았다. 휴~

계획대로 2주 정도 공부하고 태블로가 어떤 기능이 있는지, 어떤 데이터에서 어떤 그래프를 만드는지 직접 익혀보면서 시간을 많이 들였었다. 그래서 합격할 줄 알았는데 못 해서 좀 충격이었다;;

(결과적으로 3주만에 자격증을 땄다.)

시험에 떨어진 원인을 생각해보니까 **실습에서 바로바로 그래프와 대시보드를 어떻게 만들지, 데이터 추출은 어떻게 할지 익숙한 상태가 아니었다**. 그래서 실습을 많이 틀렸을 것 같았다.

시험 문제를 머릿 속으로 복기해보면서 실습 문제를 푸는 데 필요한 내용들을 다시 복습했다. 특히 자료를 안 보고도 어떻게 해야 하는지 몸이 기억하도록 세, 네 번 복습을 했다.

(개념 부분은 그냥 시험에서 헷갈렸던 문제에 나왔던 개념들을 다시 복기했다.)

그렇게 하니까 합격했다. 

다시 한 번 떨어질 수 있지 않을까 걱정했는데 운이 좋게 합격했다.

## 문제 스타일과 유의사항

객관식:

- **영어 표현**에 익숙해야 한다.
- 기본 개념을 잘 숙지해놓으면 충분. 심하게 응용하거나 심화한 내용은 없다.

실습:

- **문제를 꼼꼼히 읽어야 한다.** 예를 들어 label 색도 bar 색깔과 통일시킨다던가…
- **태블로가 손에 익숙한 상태면 대부분의 문제를 풀 수 있다.** 한 문제 정도 모르는 내용을 실행하라고 해도 대부분 비슷하게 실행할 수 있어서 어느 부분에서 어떻게 하면 되겠다는 짐작이 된다.

![Untitled 6](https://github.com/StatPage/AI-programming-class/assets/61931924/82696a81-08fc-4adc-8fc5-c38ada7694af){: .align-center}


## 추천하는 공부 방식

처음 시험을 준비하려고 정보를 찾아보니까 한글로 관련된 자료는 많이 찾아볼 수가 없었다. 하지만 Tableau Desktop Specialist 자격증 관련된 내용들로도 충분할 수 있다. 

내가 생각하기에 범위 비율이 거의 67%는 겹치는 부분이고 실습에서는 View랑 Dashboard 만드는 내용이 대부분이기 때문이다. (Extract가 나오기도 하지만 거의 기본적인 내용이다)

그래서 어떻게든 공부를 시작하는 게 중요하다. 

**태블로 개념과 실습에 익숙해지는 시간을 충분히 가지면 합격할 수 있을 것**이다.

만약 [E-learning](https://elearning.tableau.com/) 과정으로 공부할 생각이 있는 사람들이라면 **문제 푸는 데 시간을 많이 들이지 말자.** 그냥 이런 내용이 있구나 하면서 훑어보면서 지나가자. 실습도 못 풀겠으면 밑에 힌트가 있으니까 너무 안 보고 풀려하지 말자. 

혹은 **Udemy에 2만원도 안 하는 저렴하고 퀄리티 좋은 강의**들이 있다. 모든 과정이 끝나고 보니까 개인적으로는 이 [강의](https://www.udemy.com/course/tableau-accelerate-your-career-and-get-certified/)가 가장 도움이 될 것 같다. ([Link](https://www.udemy.com/course/tableau-accelerate-your-career-and-get-certified/))

# 마치며

한국에서 태블로는 익숙치 않은 tool이다. 심지어 자격증은 더하다. 그러기 때문에 이 자격증을 따야하는 상황이 되면 영어로 정보를 구해야 하는 귀찮은 상황이 된다.

그래도 진도를 한 번 쭉 나가면 시험 문제가 어렵게 나오지 않기 때문에 더 이상 공부할 필요도 없다. 그러니까 너무 걱정하지 않아도 된다. 쭉 시험 준비만 하자.

시험을 준비하시는 분들이 있다면 이 글이 도움이 됐으면 좋겠다.
