# NLP기반 익스플로잇 코드 생성 자동화 시스템 설계
*Automatic Exploit Generation and Enhancement using NLP-based Techniques*

## 프로젝트 소개
최근 제로데이 취약점의 발견 및 악용 속도가 빨라지고 있으며, 해커들은 AI 및 머신러닝을 활용해 공격을 더욱 정교하게 수행하고 있습니다. 기존의 퍼저(fuzzer)나 사후 대응 방식만으로는 이러한 고도화된 공격을 방어하는 데 한계가 있습니다. 

본 프로젝트는 자연어 처리(NLP) 기술과 LLama 모델 아키텍처를 활용하여, 웹 환경의 주요 취약점인 SQL Injection과 XSS(Cross Site Scripting)를 사전에 점검할 수 있는 익스플로잇 코드 자동 생성 모델을 설계하고 구현했습니다. 

##  데이터셋 구축 및 전처리
본 모델은 정형화된 익스플로잇 코드를 효율적으로 학습하기 위해 다음과 같은 데이터 전처리 파이프라인을 거쳤습니다.

* **One-Line Code 변환**: 익스플로잇 코드를 학습시키기 위해 이스케이프 문자를 처리 가능한 데이터 형식으로 변경하거나 토크나이저를 수정하여, 모든 페이로드를 한 줄의 코드로 정형화했습니다.
* **SQL Injection 데이터셋**: SQL Injection Cheat Sheet의 다양한 구문을 정형화했습니다. 이후 HTML 태그나 JavaScript 코드 등의 특수 문자를 제거 및 대체하여 데이터를 정제된 텍스트로 생성했습니다.
* **XSS 데이터셋**: GitHub, OWASP, PortSwigger Cheat Sheet의 여러 구문을 기반으로 XSS 페이로드를 수집 및 정형화했습니다.
* **토큰화 및 어휘 크기 최적화**: 중복된 페이로드를 제거하여 데이터의 균형을 유지한 뒤, 토크나이저를 통해 데이터를 토큰 단위로 분할했습니다. 데이터셋 크기와 메모리 제약을 고려하여 어휘 크기를 8000, 10000, 12000, 14000으로 각각 설정해 모델을 학습시켰습니다.

##  모델 아키텍처 (Model Architecture)
사전 학습(pretraining)을 위해 LLama와 유사한 구조를 가진 언어 모델을 구성했습니다.

* **Tokenizer**: 악성코드의 구조적 특성이 영어 문장과 유사하다는 점을 고려하여, BPE 알고리즘을 지원하는 `sentencepiece`를 사용했습니다.
* **Attention Mechanism**: 본 연구에서 사전 학습하고자 하는 언어 모델에는 Multi-Head Attention 메커니즘을 적용했습니다.
* **Positional Embedding**: 복소수로의 변환과 회전을 통해 포지셔널 임베딩을 효과적으로 주입하여 장기적인 의존성을 유지할 수 있는 접근법을 채택했습니다.
* **Activation Function**: LLama 모델의 특성을 반영하여, 기존 4d 파라미터 대신 2/3 4d 파라미터를 적용한 변형된 SWIGLU 활성화 함수를 활용했습니다.
* **Optimizer**: LLama 아키텍처에서 사용된 AdamW 옵티마이저와 더불어 Adam 옵티마이저를 추가로 사용하여, 두 가지 방식으로 모델 트레이닝을 진행했습니다.

## 시뮬레이션 및 결과 검증
* 취약점 테스트 환경인 DVWA(Damn Vulnerable Web Application)의 SQL Injection 파트에서 low 레벨을 대상으로 검증한 결과, 익스플로잇이 성공적으로 수행됨을 확인했습니다.
