# Geumcheon-Meat 장바구니 분석
![image](https://user-images.githubusercontent.com/71205453/109490613-cd861b80-7acb-11eb-8ef0-0bbd0bcc5fc5.png)

## 1. 장바구니 분석(연관규칙 분석)이란?
두 개 이상의 거래나 사건에 포함되어 있는 항목(아이템)들 간의 관련성을 파악하는 방법입니다.   
이를 고객이 구매한 상품 항목에 적용할 경우 장바구니 분석이라 부르며 다음과 같이 표현 할 수 있습니다.

**"품목 A를 구매하는 경우는 품목 B도 구매한다."**   
장바구니 분석의 경우 비교적 간단한 계산 방법으로 보이지 않는 구매패턴까지 탐색할 수 있다.   
유통업에서 교차판매 전략, 상품진열 레이아웃, 패키지 상품 기획 등 다용한 용도에 사용되며    
분석 대상의 항목을 상품이 아니라 소비자의 행동이나 이벤트 등으로 확장하면 다양한 서비스 영역에서 응용할 수 있다.

-장바구니 분석의 실제 적용 예-   
맥주는 왜 기저귀 옆에 있을까? 출처: 이데일리   
https://www.edaily.co.kr/news/read?newsid=01410406615800016

## 2. 금천미트 구매후기를 데이터셋으로 선정한 이유

구매후기로 부터 구매이력을 얻어내기 위해서는 재구매율이 상당히 높은 상품이어야 한다.   
단순 1회성 구매 회원으로 부터는 구매이력을 얻어 낼 수 없기 때문이다.   
금천미트는 육가공 B2B 유통 1위 온라인몰으로써 B2B 업계 특성상 고객이신 사장님들의 재구매율이 상당히 높다.   
따라서 구매후기를 바탕으로 구매이력을 추정할 수 있었고 이를 바탕으로 장바구니분석(상품연관분석)을 진행하였다.   
![image](https://user-images.githubusercontent.com/71205453/109499868-c74a6c00-7ad8-11eb-9359-22cc16f9debc.png)


## 3. 장바구니 분석을 위한 데이터 전처리
장바구니 분석에서는 한가지 주의할점이 있다. 분석 항목이 증가 할수록 연산 부하는 급격하게 늘어나다는 점이다.   
따라서 구매빈도가 높은 상위 50여가지 부위의 구매 데이터로만 장바구니 분석을 진행했다.
<p align="center"> 금천미트에서 구매빈도가 높은 상위 50 부위</p>
  
![image](https://user-images.githubusercontent.com/71205453/109490889-366d9380-7acc-11eb-8495-595c6f442f4f.png)
<div class="output_subarea output_html rendered_html output_result" dir="auto"><div>
<p align="center"> 상위 50가지 부위만 나타낸 구매 후기</p>   
  
 초기 데이테셋은 10000개의 데이테셋이었다.   
 상위 50가지 부위와 중복 데이터를 제외한 후 5000여개 가량의 데이테 셋이 되었다.
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>번호</th>
      <th>상품</th>
      <th>만족도</th>
      <th>날짜</th>
      <th>내용</th>
      <th>작성자</th>
      <th>부위</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>110763</td>
      <td>보리돈(Lethbridge)/냉장/삼겹살</td>
      <td>만족도5.0</td>
      <td>2021-02-28</td>
      <td>배송잘받았 습니다</td>
      <td>eli******</td>
      <td>삼겹살</td>
    </tr>
    <tr>
      <th>7</th>
      <td>110758</td>
      <td>금천한우거세/냉장/앞다리살/1++</td>
      <td>만족도5.0</td>
      <td>2021-02-28</td>
      <td>조아요</td>
      <td>545********</td>
      <td>앞다리살</td>
    </tr>
    <tr>
      <th>8</th>
      <td>110757</td>
      <td>엑셀(Excel)/냉장/알목심/PR</td>
      <td>만족도5.0</td>
      <td>2021-02-28</td>
      <td>마블도 괜찮고 좋아요~</td>
      <td>par*****</td>
      <td>알목심</td>
    </tr>
    <tr>
      <th>9</th>
      <td>110756</td>
      <td>금천한돈/냉장/미박앞다리(암)</td>
      <td>만족도5.0</td>
      <td>2021-02-28</td>
      <td>잘썻습니다~~~~~~~~</td>
      <td>ssp******</td>
      <td>미박앞다리(암)</td>
    </tr>
    <tr>
      <th>10</th>
      <td>110755</td>
      <td>금천한우암소/냉동/사골/2</td>
      <td>만족도5.0</td>
      <td>2021-02-28</td>
      <td>굿</td>
      <td>jin*****</td>
      <td>사골</td>
    </tr>
  </tbody>
</table>
<p>5412 rows × 7 columns</p>
</div></div>

## 4. GROUPBY 메소드와 엑셀을 이용한 더미데이터 변환

groupby 메소드를 이용해 작성자들의 구매 항목들을 합쳐 주었다.
<pre><code>feel = df.groupby('작성자')['부위'].apply(set)
df1 = pd.DataFrame(feel)
df1.head(10)</code></pre>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>부위</th>
    </tr>
    <tr>
      <th>작성자</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>024******</th>
      <td>{홍두깨, 삼겹(암), 우빽립, 삼겹양지}</td>
    </tr>
    <tr>
      <th>029******</th>
      <td>{양지머리외, 홍두깨, 채끝, 등심, 미박삼겹(암)}</td>
    </tr>
    <tr>
      <th>073**</th>
      <td>{미박뒷다리}</td>
    </tr>
    <tr>
      <th>100********</th>
      <td>{삼겹}</td>
    </tr>
    <tr>
      <th>102*******</th>
      <td>{사태}</td>
    </tr>
    <tr>
      <th>126*</th>
      <td>{목심, 삼겹살, 목살, 등심}</td>
    </tr>
    <tr>
      <th>133*</th>
      <td>{목심(암), 잡뼈, 사태, 앞다리살, 차돌박이, 미박삼겹(암)}</td>
    </tr>
    <tr>
      <th>145*</th>
      <td>{삼겹양지, 늑간살, 꼬리, LA갈비, 우목심}</td>
    </tr>
    <tr>
      <th>151*******</th>
      <td>{앞다리, 앞다리(암)}</td>
    </tr>
    <tr>
      <th>158*****</th>
      <td>{삼겹살}</td>
    </tr>
  </tbody>
</table>
</div></div>

엑셀을 이용해 1회성 이용자들의 구매이력을 제거해주고   
장바구니분석에 쓰이는 알고리즘인 apriori알고리즘을 적용할 수 있도록 데이터셋 형태를 바꾸어 주었다. 
![image](https://user-images.githubusercontent.com/71205453/109664856-efa59980-7bb0-11eb-9c91-511c14fc3f76.png)


각 항목간의 상관성을 파악하기 위해 행렬화(더미데이터화)하였다.

<pre><code>
df = df.iloc[:,1:]
df_dummy = pd.get_dummies(df)
df_dummy.head()</code></pre>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>부위1_LA갈비</th>
      <th>부위1_갈매기</th>
      <th>부위1_갈비</th>
      <th>부위1_꼬리</th>
      <th>부위1_늑간살</th>
      <th>부위1_등갈비</th>
      <th>부위1_등뼈</th>
      <th>부위1_등심</th>
      <th>부위1_로인립</th>
      <th>부위1_목살</th>
      <th>...</th>
      <th>부위14_미박뒷다리</th>
      <th>부위14_부채살</th>
      <th>부위14_앞다리(암)</th>
      <th>부위14_항정</th>
      <th>부위15_LA갈비</th>
      <th>부위15_갈비</th>
      <th>부위15_등갈비</th>
      <th>부위15_등심</th>
      <th>부위15_미박뒷다리</th>
      <th>부위15_삼겹살(암)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 415 columns</p>

## 5. 결과 및 해석
<pre><code>from mlxtend.frequent_patterns import apriori, association_rules
frequent_items = apriori(df_dummy, min_support=0.01, use_colnames=True)
frequent_items.head()
rules = association_rules(frequent_items, metric='confidence', min_threshold=0.6)
rules[['antecedents', 'consequents', 'support', 'confidence', 'lift']].sort_values(by='lift', ascending=False)</code></pre>

**지지도(support)란?**
특정 품목에 대한 지지도는 전체 거래중에서   
해당 품목을 포함하는 거래의 비율을 의미합니다.

**신뢰도(confidence)란?**
신뢰도(confidence)는 품목 A가 포함된 거래 중에서 품목 B가 포함될 확률을 의미합니다.

**향상도(lift)란?**
두 품목 (A 와 B)이 동시에 구매되는 확률과 후행조건(품목B)이 독립적으로 구매된 확률을 비해 봄으로써 도출된 규칙이 우연에 의해 나타난 것인지 실제 연광성이 존재하여 나타난 것인지를 판단하는 것입니다.

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>antecedents</th>
      <th>consequents</th>
      <th>support</th>
      <th>confidence</th>
      <th>lift</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>(부위3_양지(치마)</td>
      <td>(부위4_업진))</td>
      <td>0.013245</td>
      <td>1.000000</td>
      <td>75.500000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>(부위4_업진))</td>
      <td>(부위3_양지(치마)</td>
      <td>0.013245</td>
      <td>1.000000</td>
      <td>75.500000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>(부위2_양지(치마)</td>
      <td>(부위3_업진))</td>
      <td>0.016556</td>
      <td>1.000000</td>
      <td>60.400000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>(부위3_업진))</td>
      <td>(부위2_양지(치마)</td>
      <td>0.016556</td>
      <td>1.000000</td>
      <td>60.400000</td>
    </tr>
    <tr>
      <th>16</th>
      <td>(부위14_갈비)</td>
      <td>(부위13_사태)</td>
      <td>0.013245</td>
      <td>0.800000</td>
      <td>48.320000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>(부위13_사태)</td>
      <td>(부위14_갈비)</td>
      <td>0.013245</td>
      <td>0.800000</td>
      <td>48.320000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>(부위2_업진))</td>
      <td>(부위1_양지(치마)</td>
      <td>0.039735</td>
      <td>1.000000</td>
      <td>25.166667</td>
    </tr>
    <tr>
      <th>6</th>
      <td>(부위1_양지(치마)</td>
      <td>(부위2_업진))</td>
      <td>0.039735</td>
      <td>1.000000</td>
      <td>25.166667</td>
    </tr>
    <tr>
      <th>13</th>
      <td>(부위6_삼겹살)</td>
      <td>(부위5_삼겹)</td>
      <td>0.013245</td>
      <td>0.666667</td>
      <td>25.166667</td>
    </tr>
    <tr>
      <th>14</th>
      <td>(부위9_항정)</td>
      <td>(부위5_안심)</td>
      <td>0.013245</td>
      <td>0.800000</td>
      <td>21.963636</td>
    </tr>
    <tr>
      <th>20</th>
      <td>(부위3_목심(암))</td>
      <td>(부위1_미박삼겹(암), 부위2_목심)</td>
      <td>0.019868</td>
      <td>0.666667</td>
      <td>20.133333</td>
    </tr>
    <tr>
      <th>19</th>
      <td>(부위1_미박삼겹(암), 부위2_목심)</td>
      <td>(부위3_목심(암))</td>
      <td>0.019868</td>
      <td>0.600000</td>
      <td>20.133333</td>
    </tr>
  </tbody>
</table>
</div></div>

