## 16.7 Summary

- 쿼리가 주어졌을 때, 그 답을 계산하는 다양한 방법이 있습니다.  
  시스탬은 사용자로 부터 들어온 쿼리를 더 효율적으로 변환해야 합니다.  
  더 좋은 전략을 찾는 과정을 query optimization 이라고 합니다.
- 복잡한 쿼리들의 평가는 많은 디스크 엑세스들을 포함합니다.  
  디스크로부터의 데이터의 변환은 메인 메모리에서의 속도보다 상대적으로 느리기 때문에,  
  디스크 엑세스를 최소화하는 방식을 선택하는 것이 중요합니다.
- 하나의 expression 에 대해 동일하게 변환될 수 있는 많은 equivalence rule 이 있습니다.  
  이러한 rule들을 사용하여, 체계적으로 주어진 쿼리와 동일한 모든 expression 을 생성해야 합니다.
- 각 관계대수 expression 은 연산들의 특정한 시퀀스로 표현될 수 있습니다.  
  쿼리 처리 전략을 선택하는 첫번째 단계는 주어진 표현과 동일하고 실행하는 cost 가 적다고 추정되는 하나의 관계 대수 expression 을 찾는 것 입니다.
- 데이터베이스 시스템이 연산을 평가하기 위해 선택한 전략은 각 relation의 크기와 칼럼들 안의 값들의 분포에 따라 좌우됩니다.  
  그래서 시스템은 믿을 만한 정보를 전략 선택의 기반으로 둡니다.  데이터 베이스 시스템은 각 relation r 에 대해 통계량(statistics)을 저장합니다.  
  통계량은 다음을 포함합니다. 
  - relation r 의 튜플의 수 
  - r의 한 레코드(tuple) 의 크기 (byte)
  - 하나의 특정 attribute 에 대해 r 에서 나타나는 distinct 한 값들의 수
- 대부분의 데이터베이스 시스템들은 히스토그램을 사용하여 값들의 여러 범위들에 대해 각각의 attribute 을 저장합니다.  
  히스토그램은 일반적으로 샘플링을 사용하여 계산됩니다.
- 이러한 통계량은 다양한 연산들의 결과의 크기를 추정할 수 있도록 합니다.  
  또한 연산들의 실행 코스트도 추정할 수 있게 합니다.  
  relation 에 대한 통계적 정보는 특히 쿼리 프로세싱에서 여러개의 index 를 사용할 수 있을 때 유용합니다.  
  이러한 구조들은 쿼리 프로세싱 전략의 선택한는데 중요한 영향을 미칩니다.
- 각 표현들에 대한 alternative 평가 플랜들은 동일한 rule들에 의해 만들어질 수 있습니다.  
  그리고 전체 중에서 가장 싼 플랜이 선택될 수 있습니다.  
  몇가지 최적화 기술들은 이  alternative expression 들의 수를 줄이고, 생성되어야 하는 플랜의 수를 줄입니다.
- 고려된 플랜들의 수를 줄이기 위해 휴리스틱을 사용합니다.  
  관계적 대수 쿼리를 변환하는 것에 대한 휴리스틱 rule 들은 perform selection operation as ealy as possible, perform projectios early, Avoid Cartesian products  을 포함합니다. (selection을 최대한 먼저 수행, projection을 먼저 수행, Cartesian 지양)
- Matreialized view 는 쿼리 프로세싱의 속도를 높이는데 사용될 수 있습니다.  
  incremental view 유지는 Matreialized view 를 효율적으로 업데이트하는데 필요합니다. - relation 이 수정되는 상황에서  
  연산의 미분은 연산 입력의 미분을 포함하는 대수식을 사용하여 계산할 수 있습니다.
- Matreialized view 와 관련된 다른 문제에는 사용 가능한 Matreialized view 를 사용하여 쿼리를 최적화하는 방법과 Matreialized 되도록 view 를 선택하는 방법이 포함됩니다.
- 많은 최적화 기술들이 제시되고 있습니다.  
  Top-k optimization, join minimization, optimization of updates, multiquery optimization, parametric query optimization 등이 있습니다.



구체화 된보기