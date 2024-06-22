# CS와 연관된 개발 경험 정리 글

자체적인 스케줄링 알고리즘을 개발하여 최적의 항만 화물차 입항 스케줄링 시스템에 대한 프로젝트를 진행했었다. 이때 ML 서버에 연동하는 과정을 경험했는데 이 과정에 대해 서술하려고 한다. 

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled.png)

## 시스템 소개

항만 혼잡은 항만 근처의 교통 혼잡을 야기하고 이는 경제적, 환경적인 문제로 이어진다. 이를 개선하기 위한 방법으로 트럭 예약 시스템을 생각해 볼 수 있다. 트럭 예약 시스템은 효율적인 운송 관리를 위한 시스템으로, 이를 통해 트럭의 배송 스케줄을 최적화하여 비용과 시간을 절약할 수 있다. 기존의 스케줄링 시스템은 트럭이 특정 시간대에 몰리는 문제들이 빈번하게 발생하였다. 따라서 지금의 스케줄링보다  더욱 세밀한 스케줄링을 통해 문제를 개선하기 위하여 중장기 스케줄링과 실시간 스케줄링 알고리즘을 활용한 트럭 예약 시스템을 제안하고자 했다.

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%201.png)

우리의 스케줄링 시스템은 다음과 같다. 

먼저, 운수 회사가 각 날짜에 배정할 화물의 양을 사전에 고지해준다. 중장기 스케줄링은 사전에 고지 받은 각 날짜에 배정된 화물의 양들을 기준으로 스케줄링 되고, 10분 마다 최대 몇 대의 화물차들이 수용될 수 있을지 최대 수용량을 배정해준다. 화물차 운전 기사는 화물이 배정된 날을 기준으로 최대 일주일 전부터 당일까지 그 화물의 운송 시간을 예약할 수 있다. 이때, 예약하고자 하는 시간대가 중장기 스케줄링을 통해 배정된 화물차 최대 수용량을 넘긴다면, 운전 기사는 그 시간대를 예약할 수 없다. 예약에 성공하고 운송 당일이 되면 운전 기사가 출발 버튼을 누를 때 실시간 스케줄링이 시작되어 현재 항만 상황을 반영하여 권고 출발 시간을 안내해준다.

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%202.png)

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%203.png)

## GCP란?

GCP란 Google Cloud Platform으로 구글 클라우드에서 제공하는 머신러닝 엔진이다.

GCP에서 ML 모델을 배포하고 이를 애플리케이션에 연동하는 과정은 머신러닝 모델을 클라우드에서 운영하고, 실시간으로 예측 서비스를 제공할 수 있다. 

다음은 ML 워크플로 단계 개요이다.

1. ML 개발
2. 데이터 처리
3. 운영 학습
4. 모델 배포 및 서빙
5. ML 워크플로 조정
6. 아티팩트 구성
7. 모델 모니터링

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%204.png)

**모델 준비 및 훈련**

머신러닝 모델의 훈련은 GCP의 AI Platform Notebooks나 로컬 환경에서 진행할 수 있다. TensorFlow, PyTorch, Scikit-learn 등 다양한 프레임워크를 사용하여 모델을 훈련시킨 후, 모델을 SavedModel, .pt 파일, .joblib 파일 등의 형식으로 저장한다. 우리 프로젝트에서는 PyTorch를 사용했었다.

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%205.png)

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%206.png)

### PyTorch 모델 배포를 위한 GCP 설정

### 1. 모델 훈련 및 저장

먼저 PyTorch를 사용하여 로컬 환경이나 GCP의 AI Platform Notebooks에서 모델을 훈련한다. 모델을 `.pt` 파일 형식으로 저장하여 배포 준비한다.

```python

# 모델 훈련 후 저장 예시
torch.save(model.state_dict(), 'model.pth')
```

![Untitled](CS%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%AA%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A5%E1%86%B7%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B3%E1%86%AF%20f85ccd5f8b604719ba663098bdb7a28a/Untitled%207.png)

### 2. Google Cloud Storage에 모델 업로드

훈련된 모델 파일을 Google Cloud Storage에 업로드한다. 이를 위해 먼저 GCP의 콘솔이나 Cloud SDK를 사용하여 스토리지 버킷을 생성한다.

```bash

# 버킷 생성
gsutil mb gs://your-bucket-name/

# 모델 파일 업로드
gsutil cp model.pth gs://your-bucket-name/model/

```

### 3. AI Platform에 모델과 버전 생성

훈련된 PyTorch 모델을 AI Platform에 배포하기 위해 모델과 버전을 생성한다. PyTorch 모델을 서빙하기 위해서는 커스텀 예측 루틴을 사용하는 것이 일반적이다. 이 예제에서는 기본 설정으로 모델을 배포하지만, 실제로는 PyTorch 모델을 로드하고 예측을 수행하는 커스텀 클래스를 구현할 필요가 있다.

```bash

# 모델 생성
gcloud ai-platform models create "your_model_name" --regions "us-central1"

# 모델 버전 생성
gcloud beta ai-platform versions create "your_version_name" \
    --model "your_model_name" \
    --origin "gs://your-bucket-name/model/" \
    --runtime-version "2.1" \
    --python-version "3.7" \
    --machine-type "n1-standard-4" \
    --package-uris "gs://your-bucket-name/custom-prediction-routine/my_custom_code-0.1.tar.gz" \
    --prediction-class "predictor.MyPredictor"

```

### 4. 예측 요청

AI Platform에 배포된 모델을 사용하여 예측을 요청한다. Python 클라이언트 라이브러리를 사용하여 예측 요청을 보낼 수 있다.

```python

import googleapiclient.discovery

service = googleapiclient.discovery.build('ml', 'v1')
name = 'projects/{}/models/{}/versions/{}'.format(project_id, model_name, version_name)

response = service.projects().predict(
    name=name,
    body={'instances': [your_input_data]}
).execute()

print(response['predictions'])

```