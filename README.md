# ml_conformer_ctc
conformer_ctc

<!-- code_chunk_output -->

- [Conformer_ctc with NeMo](#conformer_ctc-with-NeMo)
- [Requirements](#requirements)
- [How to install](#how-to-install)
- [Data](#data)
- [Result](#result)
- [Competition](#competition)
- [Reference](#reference)

<!-- /code_chunk_output -->

---

## Conformer_ctc with NeMo

NVIDIA NeMo Toolkit 기반 Conformer_ctc를 사용하여 한국어 음성인식 학습을 진행하였습니다.

## Requirements

- python==3.9.2

## How to install

### in colab

```python
!pip install nemo_toolkit['all']
!pip install nlptutti
```

### in vscode

```sh
python -m venv .venv
```

Ctrl+Shift+P</br>
Python:Select Interpreter</br>
Python 3.9.2('.venv':venv)

```sh
pip install --upgrade pip
pip install nemo_toolkit['all']
pip install nlptutti
pip install -r requirements.txt
```

## Data

### Source

1. [AIHub 자유대화 음성(일반남여)](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=109)
2. [AIHub 인공지능 학습을 위한 외국인 한국어 발화 음성 데이터](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=505)

### Dataset 구성

|  | 데이터 요약데이터 구분 | 제공 방식 |
| --- | --- | --- |
| 자유대화
(일반남여) | 10대에서 50대 사이의 일반인 남녀의 발화 데이터
녹음 인원 2,000명 이상, 4,000시간 음성 데이터 | 원천데이터: PCM(WAV) 파일 포맷
메타데이터: JSON 파일 포맷 |

### 어노테이션

- Studio에서 녹음DB 이외의 실 환경에서 수집 받은 음성데이터에는 외부 잡음 등이 포함되어 있
으니, 외부 잡음 Filler Noise를 철자 전사 정보에 추가
    - (NO:) 내일 오후 세시에 예약해 주세요
- 자유 발화인 경우, 녹음자도 모르는 사이에 발성하는 어’,‘음’, ‘글세’ 등도 음성인식 학습에 필요한
정보이기 때문에 간투어 Filler Pause 정보를 철자 전사 정보에 추가
    - (FP:음) 내일 오후 세시에 예약해 주세요
- 유아, 노인들이 발성할 경우, 전사자가 들었을 때 명확하게 철자전사 작업을 하지 못할 음성 전
사는 발성오류 정보를 추가
    - 내일 오후 (SP:세시에) 예약해 주세요
    : 세시, 네시 가 명확하게 들리지 않는 경우
- 마이크로폰 근접에서 발성하는 경우, 녹음자의 들숨, 날숨, 웃음 소리 등이 녹음되는 음원의 경우
는 화자 잡음 Speaker Noise를 철자 전사 정보에 추가
    - (SN:) 내일 오후 세시에 예약해 주세요 (SN:)


### Data dir
```
30G     ./[원천]1.AI챗봇_2
30G     ./[원천]1.AI챗봇_3
31G     ./[원천]1.AI챗봇_4
30G     ./[원천]1.AI챗봇_5
30G     ./[원천]1.AI챗봇_6
14G     ./[원천]1.AI챗봇_7
30G     ./[원천]2.음성수집도구_1
29G     ./[원천]2.음성수집도구_2
31G     ./[원천]2.음성수집도구_3
24G     ./[원천]2.음성수집도구_4
8.2G    ./[원천]2.음성수집도구_5
28G     ./[원천]3.스튜디오_1
11G     ./[원천]3.스튜디오_2
5.3G    ./[라벨]1.AI챗봇
2.7G    ./[라벨]2.음성수집도구
840M    ./[라벨]3.스튜디오
30G     ./[원천]1.AI챗봇_1
714G    .
```


### Training set

- 전체 데이터

    |  |  |
    | --- | --- |
    | PCM 파일수 | 489 |
    | WAV 파일수 | 2,278,494 |
    | 용량 | 356GB |
    | 시간 | 3213 시간 |

- 디렉토리별

    |  | # of PCM | # of WAV | 시간(hh:mm:ss) | 용량 |
    | --- | --- | --- | --- | --- |
    | [원천]1.AI챗 봇_1 | 0 | 211,761 | 273:11:41 | 30GB |
    | [원천]1.AI챗 봇_2 | 0 | 204692 | 274:57:14 | 30GB |
    | [원천]1.AI챗 봇_3 | 0 | 221786 | 273:26:17 | 30GB |
    | [원천]1.AI챗 봇_4 | 0 | 227032 | 275:13:48 | 31GB |
    | [원천]1.AI챗 봇_5 | 0 | 219714 | 273:29:40 | 30GB |
    | [원천]1.AI챗 봇_6 | 0 | 218792 | 271:01:58 | 30GB |
    | [원천]1.AI챗 봇_7 | 0 | 60510 | 123:51:46 | 14GB |
    | [원천]2.음성수집도구_1 | 0 | 179231 | 274:23:33 | 30GB |
    | [원천]2.음성수집도구_2 | 0 | 155957 | 258:27:10 | 29GB |
    | [원천]2.음성수집도구_3 | 0 | 173654 | 274:42:47 | 31GB |
    | [원천]2.음성수집도구_4 | 0 | 137107 | 220:08:21 | 24GB |
    | [원천]2.음성수집도구_5 | 0 | 47937 | 74:44:41 | 8.2GB |
    | [원천]3.스튜디오_1 | 489 | 156090 | 251:41:26 | 28GB |
    | [원천]3.스튜디오_2 | 0 | 58210 | 93:52:29 | 11GB |
    | 계 | 489 | 2,272,473 | 3213:12:51 | 356GB |

### Validation set (사실상 Test set)

- 전체 데이터

    |  |  |
    | --- | --- |
    | PCM 파일수 | 0 |
    | WAV 파일수 | 26만 개 |
    | 용량 | 89 GB(레이블포함) |
    | 시간 | 366 시간 |

- 디렉토리별

    |  | # of PCM | # of WAV | 시간(hh:mm:ss) | 용량 |
    | --- | --- | --- | --- | --- |
    | [원천]1.AI챗 봇 | 0 | 149,008 | 200:09:34 | 24GB |
    | [원천]2.음성수집도구 | 0 | 87,273 | 125:57:53 | 15GB |
    | [원천]3.스튜디오 | 0 | 27,093 | 39:55:41 | 4.7GB |
    | 계 | 0 | 26,374 | 366:03:08 | 43.7GB |

### Preprocessed Data Set

- 전처리 및 정제가 끝난 Trainset의 정보
- 총 2800시간, 약 200만개의 음성파일

- Train set

    | # of wav files  | 1,806,842 |
    | --- | --- |
    | Duration(hh:mm:ss) | 2514:18:16 |

- Valid set

    | # of wav files  | 200,761 |
    | --- | --- |
    | Duration(hh:mm:ss) | 279:50:52 

## Result

### 학습환경
![GPU](/ml_conformer_ctc/images/ctc학습_gpu.png)

### Training
*기존 WER을 기준으로 학습을 진행했으나 이후 CER을 기준으로 다시 학습을 진행함 아래 이미지는 CER기준으로 학습 시 진행과정을 모니터링 하기 위한 그래프
![GRAPH](/ml_conformer_ctc/images/ctc학습_graph.png)

### 평가지표 : CER(Character Error Rate)

![CER](/ml_conformer_ctc/images/CER.png)
- Substitution (S): 추론된 텍스트 중 정답 텍스트와 비교해 잘못 대체된 음절 수

- Deletion (D): 추론된 텍스트 중 정답 텍스트와 비교해 잘못 삭제된 음절 수

- Insertion (I): 추론된 텍스트 중 정답 텍스트와 비교해 잘못 추가된 음절 수

- N: 정답 텍스트의 음절 수

### Accuracy

|  model  |   CER(%)   | data |
| :---: | :---: | :---: |
| conformer_ctc | 8.3 | AIHub 자유대화 음성(일반남여) |
| KoSpeech | 10.31 | AIHub KsponSpeech |
| conformer_ctc | 19.2 | AIHub 인공지능 학습을 위한 외국인 한국어 발화 음성(한국일반-발화능력 상) |
| conformer_ctc | 23 | AIHub 인공지능 학습을 위한 외국인 한국어 발화 음성(한국일반-발화능력 중) |
| conformer_ctc | 25.7 | AIHub 인공지능 학습을 위한 외국인 한국어 발화 음성(한국일반-발화능력 하) |

### Analysis

- 기존에는 Librispeech로 pre-training된 모델을 fine-tuning하여 학습을 진행했으나 한국어 음성데이터가 충분히 많아 fine-tuning방식이 아니라 처음부터 2500시간 가량의 데이터를 학습시키는 방식을 사용하여 성능을 향상시킴
- 이 방식을 선택하여서 성능은 향상시킬 수 있었지만 학습하는데 소요되는 시간이 매우 늘어남 기존 fine-tuning은 마지막 layer만 학습시키면 되어서 여러번 테스트를 할 수 있었지만 전체를 학습시키는 방식은 2500시간을 학습시켜야하기 때문에 많은 테스트와 수정이 힘듦
- 하지만 초기 빠르게 개발을 시작하여 후자의 방식을 선택하였고 한 번 학습에 총 10일 정도가 소요됨
- 한국어 교육 어플리케이션에 사용할 한국어 음성인식기이므로 일반 한국인 남녀의 한국어 발화 데이터로 학습시킴
- 따라서 외국인의 한국어 발화 데이터에 대한 평가 결과는 한국인에 비해 낮을 것
- 외국인이 한국인처럼 말하게 하는 것을 목표로 하는 어플리케이션이기 때문에 외국인의 테스트 결과를 참고하기 위해 한국어 발화능력에 따라 상, 중, 하로 나눠진 데이터를 테스트함
- 그 결과 발화능력에 따라 CER이 다르게 측정 되었으며 한국어 발화능력 상에 해당하는 외국인들의 평균이 19.2 정도로 나옴
- 따라서 CER 10% 이내로 결과가 나온다면 great, CER 20%이내로 결과가 나온다면 good을 return해주고 CER이 그 이상이라면 bad를 return해주어 다시 녹음하도록 함


### In App

<!-- ![In App](./in_app.png) -->
- 추후 업데이트

## Competition
- [2022 한국어 AI 경진대회](https://competition.aihub.or.kr/hackathon/scheduleDetail/10000)
- 한국어 음성인식 성능향상 Track 01 참가 (팀명: 서강프렌즈)
- [데이터 품질리뷰상(특별상) 수상](https://www.sogang.ac.kr/gopage/goboard2.jsp?bbsConfigFK=58&pkid=535170)

---

## Reference

- [NVIDIA NeMo Toolkit](https://github.com/NVIDIA/NeMo)
- [Conformer: Convolution-augmented Transformer for Speech Recognition](https://arxiv.org/abs/2005.08100)
- [KoSpeech: Open-Source Toolkit for End-to-End Korean Speech Recognition](https://arxiv.org/ftp/arxiv/papers/2009/2009.03092.pdf)
- [Google Sentencepiece Tokenizer](https://github.com/google/sentencepiece)
- [AIHub](https://aihub.or.kr)