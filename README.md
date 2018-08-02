## 2017 Big Contest Challenge League (2017.08 ~ 2017.10)

- 주제 : **대출 연체자 예측 알고리즘 개발**
- 주최 : 한국빅데이터연합회
- 결과 : 한국정보화진흥원장상 수상 (2등)
- 최종 적용 모형 : 트리 기반 앙상블 모형 (GBM, XGB)
- 역할 : 파생변수 생성, 데이터 시각화, 파라미터 튜닝

## 0. 대회 내용 요약 

 (1) 100,000건의 고객 데이터(신용평가사, 통신사, 보험사 결합 데이터) 66개의 독립변수로 반응 변수인 **대출 연체 여부를 정확히 분류**해 내는 것이 목적
  
 (2) 연체자의 분포가 약 4,000건인 **불균형 데이터**였기 때문에 분류하는 과정에서 이에 대한 고려를 하는 것이 매우 중요한 사항이었음(샘플링, 파라미터 조절 등)
 
 (3) 최종적으로는 **XGB(Extreme Gradient Boosting)** 모형을 선택하게 되었는데, 가장 정확한 분류가 가능함과 동시에 과적합의 가능성이 적고 다양한 파라미터 조절을 통해 최적의 분류기를 생성해 낼 수 있는 장점이 있었기 때문이었다고 생각.(다른 참가 팀의 경우에도 XGB를 활용한 팀들이 대부분이었다는 점에서 현재까지 가장 뛰어난 성능을 보이는 알고리즘임을 알 수 있었음)
 
 (4) 정확한 분류를 위한 적절한 파생변수 생성, 생성한 모형의 과적합 가능성을 최대한 줄이는 것, 파라미터 서치 과정에서 효율성을 향상시키는 것이 심사에서 주된 포인트 였다고 생각 (물론 평가 기준인 F1 Measure가 어느 정도 되어야 하는 것은 기본 사항(발표에 참가한 팀들의 F1 Measure 값은 큰 차이가 없었음))

## 1. 데이터 정의

- 신용평가사, 보험사, 통신사의 결합 데이터 (비식별화) 100,000건

- 지도 학습을 통해 TARGET 변수 (대출 연체여부 : 연체(1), 비연체(0))를 정확히 맞추는 것이 주요 목표

- 데이터 셋이 크고, 변수도 매우 다양해서 이를 팀원 끼리 나누어서 의미파악, EDA를 진행한 뒤 서로에게 설명해주는 방식으로 전체 데이터 셋을 이해했음

## 2. 데이터 전처리 및 EDA 과정

- 총 세개의 데이터 셋(신평사, 보험사, 통신사)으로 나누었을 때, 각 셋의 변수들 간의 높은 상관 관계가 존재했음 -> **파생변수 생성**

- 연속형 변수의 경우 0값이 높은 Sparse 데이터인 경우가 많았음 -> **로그 변환**을 적용

- 소득을 직업 변수와 함께 그래프로 그려보았을 때, 직업 간 소득의 차이가 명확했음 -> **표준화 작업** 진행

- 그 밖에 각 독립변수의 분포 안에서 종속변수의 Proportion을 확인해 보면서 어떤 변수가 분류에 유의미하게 쓰일 지 판단했음

- 파생 변수를 생성하는 과정에서 생성에 대한 명확한 로직이나 근거를 만드는 것에 집중했음

## 3. 모델링 과정

(1) 차원 축소 : PCA(주성분 분석)
    
-> 변수 간의 상관관계가 높아서 작은 Dimension 으로 축소하는 방법을 고려했으나, 적은 주성분으로는 전체 변동을 충분히 설명할 수 없었음

(2) 샘플링 방법 : Under Sampling, Over Sampling, SMOTE

-> 불균형 데이터임을 고려하여 Sampling 방법을 적용하고자 했으나, Under Sampling 은 정보의 손실, Over Sampling은 과적합의 문제 발생,
   SMOTE는 KNN 알고리즘을 기반으로 하여 차원이 높아졌을 때 설명력이 떨어지는 문제점이 있었음

(3) 로지스틱 회귀, SVM, 의사결정나무, 랜덤포레스트 ,GBM, XGB 의 분류 기법 비교 적용

-> 분류 기법에 사용되는 다양한 알고리즘을 적용해 보았고, 로지스틱 회귀를 기본 모형으로 F-measure 값을 비교해가면서 적용해보았음
   결과적으로 트리 앙상블 모형인 GBM과 XGB가 가장 높은 성능을 가지는 것을 알 수 있었고 이를 최종 모형으로 선정하고 파라미터를 세부적으로 튜닝        하는 과정을 진행했음.
    
(4) GBM과 XGB의 하이퍼 파라미터는 다른 모형에 비해 많음 -> 이에 따라 파라미터의 최적 조합을 찾는데에 *시간 소요가 많이 되었음*

 (Grid Search로 기준값을 찾고 Heuristic한 방법으로 세부 튜닝을 진행했음 -> 대부분의 참가 팀이 이러한 방법으로 파라미터 튜닝을 했었지만,
  가장 우수한 평가를 받은 팀(1등 팀)의 경우에는 Parameter search 과정에서 **Bayesian** 을 적용한 점이 차별화 Point 였다고 생각)
    
## 4. 결론 도출

(1) 최종 적용한 부스팅 모형이 왜 최종 모형으로 가장 적합한가?에 대한 적절한 이유 설명
   
   - 불균형 데이터를 분류하는 데 가장 높은 성능을 보였음 : 이전 분류기의 오분류에 집중하여 연속적으로 보완해가는 형태의 알고리즘 이었기 때문
   - 다른 트리 앙상블 모형(ex. 랜덤포레스트)에 비해 높은 정확도 : *Bias 최소화*에 더 집중하는 알고리즘
   - 높은 정확도 + 변수 중요도 설명 가능 : 신용 평가에 관련된 모형이므로 *해석력이 필요*할 것이라고 생각(더 복잡한 모형을 선택하지 않은 이유)
    
(2) 분류 성능을 더 끌어올릴 수 있는 방법은 없을까? Stacking 방법을 적용해 보았으나 성능이 크게 상승하지는 않았음.
   -> 효율성, 설명가능성을 종합적으로 고려했을 때, XGB 단일 모형보다 나은 방법이라고 볼 수 없었음. 
