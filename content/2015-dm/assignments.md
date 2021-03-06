Title: Assignments
Date: 2015-03-06 09:00
Tags: assignments
Courseid: 2015-dm
Toc: True

For assignment guidelines, visit the class <a href="http://eclass.seoultech.ac.kr">e-class</a>.

## Assignment 0: 자기소개서 쓰기

- 마감일: 2015-03-11 23:59
- 제출처: e-class

A4 용지 1페이지 이내로 자기소개서를 작성해봅시다.
형식은 자유이지만 아래의 내용은 꼭 포함시켜주시기 바랍니다.

1. 이름, 전화번호, 메일주소, 사진
2. 성격, 취미, 특기, 동아리 활동 등
3. 프로그래밍 경력 (사용 가능한 언어, 상중하 수준, 언어를 이용해 진행한 일)
4. 데이터마이닝 수업을 듣게 된 이유, 수업을 통해 얻고 싶은 것
5. 졸업 후 계획, 가고 싶은 학교 또는 회사 

## Project proposal

- 마감일: 2015-03-18 23:59
- 제출처: e-class

팀프로젝트에서 진행할 데이터마이닝 아이디어 한 가지를 제안해주세요.
제안서는 A4 용지 1페이지 이내로 작성하고 PDF로 변환하여 올려주시기 바랍니다.

프로젝트에 대한 자세한 사항은 [이 링크](http://www.lucypark.kr/courses/2015-dm/course-introduction.html#term-project-40)를 참고해주세요.

## Assignment 1: Classification

- 마감일: 2015-04-02 23:59
- 제출처: e-class

Assignment 1은 수업 시간에 사용한 MNIST 데이터셋을 이용한 학습을 실제로 해보는 것입니다.
아래의 다섯 가지 문항에 대한 답을 문서로 작성하고 PDF로 변환한 후 올려주시기 바랍니다.

[수업 시간에 배운 logistic regression](logistic-regression.html) 알고리즘을 학습해보자.
단, 이번에는 1) Binary가 아니라 전체 10개의 범주(class)에 대해 분류해보고, 2) 성능과 학습 시간을 모두 측정해보는 것이 목표이다.

먼저 scikit-learn을 통해 MNIST dataset을 loading하고, partitioning해보자.

    :::python
    from sklearn import datasets, cross_validation
    d = datasets.fetch_mldata('MNIST original', data_home='.')
    X, y = d['data'], d['target']
    X_train, X_test, y_train, y_test = cross_validation.train_test_split(X, y, test_size=0.4, random_state=1234)

> **참고**: `d = datasets.fetch_mldata('MNIST original', data_home='.')`에서 `HTTP Error 500: Internal Server Error`를 내면서 죽는다면 mldata.org 사이트의 접속량 폭주 때문일 수 있다. 이 때 해결법은 두 가지가 있다.<br>
> 1. 접속이 원활해질 때까지 기다렸다가 잠시 후에 다시 시도해보거나<br>
> 2. [이 파이썬 파일](https://gist.github.com/e9t/736b5410c0937091166b)을 현재 디렉토리에서 실행해보자.<br>
> 현재 디렉토리에 `mldata`라는 폴더가 생기고, 그 안에 `mnist-original.mat`이라는 이름으로 약 54MB 크기의 파일이 저장되었다면 성공이다.

학습시간을 측정하기 위해 다음과 같이 [time](https://docs.python.org/3/library/time.html) 모듈을 사용해보자.
다만, 코드가 무슨 말인지는 꼭 이해하고 가자!
이해가 안 된다면 이 참에 구글링도 하고 scikit-learn 공식 문서도 뒤져보자.
우리의 목표는 이 숙제를 완성하는 것이 아니라 실제로 데이터를 가지고 놀 수 있는 능력을 키우는 것이다.

시간이 오래걸릴 것이니 밥을 먹고 와도 좋다.

    :::python
    from time import time
    from sklearn.linear_model import LogisticRegression
    lr = LogisticRegression(random_state=1234)
    s = time(); lr.fit(X_train, y_train); t = time() - s
    print(t)

> 1: 로지스틱 회귀분석으로 MNIST 데이터를 학습하는데 시간이 얼마나 걸렸는지 기록하자.

학습이 다 됐는가? Training하느라 우리 컴퓨터가 수고 많았다.
매번 그 과정을 거치면 우리 컴퓨터가 너무 힘드니까 모델을 저장해보자.
이 때 모델의 이름은 길더라도 의미있게 짓는 것이 좋다.
(여기서는 random_state라는 파라미터를 입력했으니 그것을 변수명에 넣어보겠다.)
여기서 파일 확장자로 사용한 `pkl`은 [pickle](https://docs.python.org/3/library/pickle.html) 파일이라는 것을 나타낸다.
pickle은 파이썬에서 오브젝트(object)를 binary로 저장하는 방식 중 하나이다.

    :::python
    from sklearn.externals import joblib
    joblib.dump(lr, 'lr_randomstate_1234.pkl')

위 코드를 입력하면 내 컴퓨터에 한 개(혹은 그 이상)의 파일이 생성되는 것을 확인할 수 있다.
(참고자료: [Model persistence](http://scikit-learn.org/stable/modules/model_persistence.html))

이렇게 모델을 파일로 저장하고나면 다음부터는 컴퓨터를 재부팅하고도 다시 많은 시간을 투자해서 모델을 학습하지 않고 아래와 같이 모델을 파일로부터 로딩할 수 있다.

    :::python
    lr = joblib.load('lr_randomstate_1234.pkl')

> 2: 생성된 학습 모델 파일명과 파일의 크기가 몇 KB인지 기록하자.
파일이 여러 개 생성되었다면 각 파일의 이름과 크기를 모두 기록하자.

우리가 LogisticRegression 클래스의 instance를 생성할 때 사용한 파라미터는 아래의 명령어로 찾을 수 있다.

    :::python
    p = lr.get_params()

> 3: 어떤 값들이 출력되는가? 각 파라미터는 어떤 의미를 가지는지 설명해보자. 잘 모르겠다면 [이 문서](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)를 확인해보자.

> 4: 이 파라미터들을 사용했을 때 test set에 대한 정확도(accuracy), 정밀도(precision), 재현율(recall), F-score는 각각 얼마인지 계산해보자.

    :::python
    print(lr.score(X_test, y_test))

> 5: 분류 알고리즘은 logistic regression 뿐 아니라 k-NN, Decision trees, SVM, Neural Networks 등 다양하게 있다. Logistic regression을 제외하고 다른 분류 알고리즘을 적어도 하나 택해서 Logistic regression으로 MNIST dataset에 대한 학습 시간과 정확도를 비교해보자.

로지스틱 회귀분석 모형의 학습 시간이 엄청 길었을 것이다.
그런데 왜 그랬을까?
[공식 문서](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)에도 나와있듯 multi-class, 즉 다범주 문제에 대해서 scikit-learn의 구현체가 multinomial logistic regression을 구현하지 않았기 때문이다.
즉, 우리가 수업 시간에 다룬 [1-against-all](http://localhost:8000/2015-dm/logistic-regression.html#6-1범주-mnist-1-against-all) 모델을 모든 범주, 그러니까 총 10개의 범주에 대해서 다 시행한 것이다.
따라서 원래 모델 하나를 학습하는 시간보다 약 10배의 시간이 걸렸을 것이다.

> Extra credit: 학습 속도를 높이면서 성능은 유지하거나, 학습 속도를 유지하면서 성능을 높일 수 있는가? 어떻게 하면 되는가? 이 때 어떤 파라미터를 썼는가?

## Assignment 2: Dimensionality reduction

- 마감일: 2015-05-07 23:59
- 제출처: e-class

Assignment 2은 Assignment 1에서 다룬 MNIST 데이터셋을 이용하여 PCA를 이용한 차원축소를 하는 것입니다.
아래의 세 가지 문항에 대한 답을 문서로 작성하고 PDF로 변환한 후 올려주시기 바랍니다.

이번에는 Assignment 1에서처럼 `sklearn.datasets.fetch_mldata()`를 사용하지 말고,
`sklearn.datasets.load_digits()`를 사용해서 약식의 MNIST 데이터를 loading해보자.

    :::python
    d = datasets.load_digits()      # sklearn의 datasets에 완비된 mnist 데이터 로딩
    X, y = d['data'], d['target']   # X, y 변수를 생성하여 각각 독립변수와 종속변수를 넣음

> 1: 변수 X는 몇 개의 record(row)와 몇 개의 attribute(column)로 구성되어 있는가? 첫 다섯 개의 record가 각각 어떤 X값을 가지는지 기록해보자.

> 2: 파이썬의 scikit-learn 패키지에서 PCA를 할 수 있는 함수를 찾아 X를 2개의 차원으로 전사하여 Z라는 변수에 저장하자. Z는 몇 개의 record(row)와 몇 개의 attribute(column)로 구성되어 있는가? 또, 첫 다섯 개의 record는 각각 어떤 X값을 가지게 되었는가?

> 3: 1, 2번을 수행한 코드를 붙여넣고, 첫째줄부터 마지막줄까지, 작성한 코드의 각 줄이 어떤 동작을 하는지 설명해보자.

> Extra credit: PCA를 이용하여 MNIST 데이터를 두 개의 차원으로 전사한 결과값 Z를 2차원 평면의 그래프로 그려보자. 파이썬을 이용해도 좋고, 전사한 데이터를 파일로 저장하여 다른 언어를 이용해 시각화해도 좋다.
참고로, PCA를 이용하여 [iris 데이터셋](http://en.wikipedia.org/wiki/Iris_flower_data_set)을 3차원으로 전사시켜 시각화한 예시는 [이 링크](http://scikit-learn.org/stable/auto_examples/decomposition/plot_pca_iris.html)에서 볼 수 있다.

<!--
> 5: 분류문제의 성능을 측정할 때는 위의 정확도(accuracy) 뿐 아니라 precision, recall, f-measure 등도 사용된다. 1) 각각은 어떤 의미를 가지며 어떻게 계산하는가? 2) 실제로 값도 구해보고, 3) 도출된 네 가지 지표 accuracy, precision, recall, f-measure을 통해 우리 모델이 잘 학습되었는지 판단해보자.
1. 아래의 코드로 logistic regression의 coefficient들을 그림으로 그려보자.
어떤 모양을 가지는가? 이것이 뜻하는 바는 무엇일까?

        :::python
        plt.matshow(lr.coef_.reshape(28, 28))
        plt.colorbar()
        plt.savefig('lr_coef.png')

## Assignment 3: 분류기 비교

- 마감일: 2015-04-02 23:59
- 제출처: e-class

지금까지 우리는 logistic regression, decision trees, k-NN, ANN, SVM 등의 분류 알고리즘에 대해 배웠거나 배울 것이다.
이들의 특성은 어떻게 다른가?
각각의 알고리즘은 어떤 데이터셋과 상황에 적합하다고 볼 수 있는가?
[iris](http://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_iris.html) 데이터셋을 이용해 각 분류기의 성능을 비교해보자.
    - 참고: [Scikit-learn classifier comparison](http://scikit-learn.org/stable/auto_examples/plot_classifier_comparison.html)
-->
