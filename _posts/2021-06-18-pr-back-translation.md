---
layout: post
title:  "[NMT] Understanding Back-Translation at Scale"
date:   2021-06-18T14:25:52-05:00
author: Heejin Do
categories: #"paper_review"
tags:	nlp nmt back translation paper review back-translation deep-learning
---

> 논문 출처 (reference)
: Edunov, Sergey, et al. "Understanding back-translation at scale." arXiv preprint arXiv:1808.09381 (2018).
: published at EMNLP 2018

> 소스 코드 (Open Source)
: https://github.com/pytorch/fairseq

이 논문은 **Back-Translation**의 NMT에서의 적용에 대해 분석하고, 여러 방식을 비교하여 어떤 방식이 효율적인지에 대해 제시하는 논문이다. WMT'14 En-De test set에서 SOTA를 달성한 논문이라 자세히 정리해보았다.

## Abstract
target 언어 데이터를 **Back-Translation**하여 parallel corpus를 augment 하는 기법은 NMT 성능 향상을 위해 자주 사용된다. 이 논문에서는 **Back-Translation**에 대해 분석하고, 여러 방법론을 조사하여 비교했다. 이를 통해 resource가 부족한 환경에서 sampling이나 noised beam output으로 이뤄진 **Back-Translation**이 가장 효과적임을 발견했다. 또한, 합성 데이터와 본래의 bitext 데이터를 비교하여 domain 영향을 연구했다. 마지막으로, 대량의 monolingual sentence 데이터에 적용하여 새로운 SOTA(35 BLEU on WMT'14 En-De)를 달성했다. 

## Introduction
기계 번역에서 대량의 parallel corpora를 확보하는 것은 성능과 직결된다. 하지만, 현실에서는 한정되어있는 bitext는 한정되어있는 반면 monolingual 데이터는 많이 구할 수 있다. 전통적으로, 이런 monolingual 데이터를 이용해 언어 모델을 향상시킴으로써 통계적 기계번역의 유창성을 높이려는 노력이 있었다.

NMT에서도 monolingual data를 이용해 모델을 향상시키려는 노력이 있어왔다(`language model fusion`, `back-translation`, `dual learning` 등). 본 논문에서는 그 중 semi-supervised 환경에서 동작하는 **Back-Translation**에 초점을 둔다. **Back-Translation**은 parallel corpus 데이터를 augmentation 하는 방법 중 하나로, 우선 parallel 데이터를 이용해 모델을 훈련(target→source 방향)하고, 훈련된 모델로 target의 monolingual 데이터를 번역함으로써 synthetic source 데이터를 생성한다. 그렇게 생성된 synthetic 데이터는 실제 bitext에 더해져 본 모델 훈련(source→target 방향)에 사용된다. 단순하지만 효과가 좋다고 알려져있다.

이 논문에서는, 큰 규모에서 **Back-Translation**을 조사/분석 하였다. 기존 연구들을 확장하고 synthetic source 데이터를 생성하는 다양한 방법론을 분석하여 두 가지 선택(`sampling from the model distribution`, `noising beam outputs`) 중요하다는 것을 보여준다. 또한, synthetic 데이터를 추가하는 것과 실제 bitext를 추가하는 것을 비교하여 전자의 경우에도 후자와 동일한 정도의 정확도를 낼 수 있음을 발견했다. 

## Backgrounds

### Nerual Machine Translation
이 연구는 Encoder/Decoder 구조를 가지는 NMT에 기반을 둔다. Encoder는 source 문장의 연속 공간 표현을 추론하고 Decoder는 인코더 아웃풋을 조건으로 하는 언어 모델(Language model)이다. 두 모델의 파라미터들은 parallel 코퍼스의 소스 문장에 대한 타겟 문장의 가능성을 최대화하기 위해 공동으로 학습된다.

효율성과 효과성을 향상시키기 위한 다양한 NMT 기법들이 제안되어져왔다(RNN, CNN, Transformer 등). 최근에는 attention 기반 방법들이 주로 사용되며, 이 논문에서는 그 중 transformer 구조를 사용한다.

### Semi-Supervised NMT
Monolingual data는 MT 성능 향상을 위해 90년대부터 활용되어져왔다. Phrase 기반 시스템에서, target 언어의 LM(언어모델)은 디코딩 과정에서 더 자연스러운 결과를 만들어 낸다. NMT에서도 이와 비슷한 전략이 적용될 수 있는데, neural LM과 NMT의 깊은 통합(예를 들어 둘의 hidden state의 결합)은 이점을 가져온다. 신경망 기반 구조는 MT와 target 측 LM 간 multi-task learning과 파라미터 공유를 가능케 한다.

Back-Translation은 monolingual data의 횔용도를 높일 수 있는 대체 기법이다. BT에서는 추가적인 합성 parallel 데이터를 얻기 위해 target→source 시스템이 따로 필요하다. 이 합성 데이터는 기존의 bitext데이터에 더해져 목표 시스템인 source→target 시스템의 훈련에 사용된다. BT는 예전부터 phrase based system에 사용되었으며, 도메인 adaptation을 위해 monolingual data를 활용하는 부분에서 성과가 있었다. 최근에는 NMT에서의 효과가 검증되었고, 특히 parallel 데이터가 부족한 경우에 유용하다.

이 논문과 유사하게, beam search 대신 synthetic sources를 샘플링 하는 것이 더 효과적임을 증명한 논문이 있다([Imamura et al]()). 해당 논문에서는 각 target에서 여러 source들을 샘플링 했다면, 이 논문에서는 더 많은 target 문장에 적용하기 위해 단 하나의 샘플을 추출했다는 차이가 있다. 이외에도 back-translation과 final output을 연속적으로 향상시키기 위해 반복 절차를 제안하거나([Cotterell and Kreutzer]()), multilingual 모델에서 forward와 backward 전 과정을 통해 새로 합성된 데이터를 추가하며 연속적으로 훈련하는([Niu et al.]()) 기존 연구들이 있다. target이 아닌 source 측의 monolingual data를 이용하는 연구([Zhang and Zong]()), source, target 양방향으로의 훈련을 모두 하며 두 방향에서의 BT를 가능케 하는 dual learning으로의 확장([Xia et al.]()), unsepervised NMT에의 적용, GAN의 적용 등 다양한 확장이 있다. 

## Methods : Generating synthetic sources
보통 Back-translation에서는 beam search나 greedy search를 사용해 synthetic source 문장을 생성한다. 두 가지 모두 MAP 결과를 찾기 위한 근접 알고리즘에 기반을 둔다. 하지만, MAP 예측은 항상 가장 가능성이 높은 것에만 집중하기에 덜 풍부한 번역으로 이어질 수 있다. 이런 특징(beam/greedy에서 모델 분포의 head쪽에만 집중하는 것) dialog나 스토리 생성 처럼 불확실성이 높은 태스크에서 문제가 될 수 있다. 이 논문에서는 이런 특징이 back-translation에서도 문제가 된다고 주장하며 모델 분포에서의 sampling과 beam search output에 noise를 추가하는 두 가지 방법을 제안한다. 이 논문에서는 아래의 세 가지 경우를 탐색/분석했다.

1) unrestricted sampling : 가끔 높은 확률로 정답에서 벗어날 정도로, 매우 넓은 범위의 결과를 생성한다.
2) restricted sampling : 매 스텝에서 결과 분포로부터 k개의 가장 가능성 높은 토큰을 선택하고, 재-표준화(re-normalize) 한 이후, 이 restricted set으로부터 샘플링을 한다. (MAP와 unrestricted sampling의 중간에 있다고 볼 수 있다.)
3) noising beam search output : 세가지 타입의 노이즈를 통해 source sentence를 변형했다(deleting, replacing/swapping words)


## Methods

#### 2.1. Distance-wise distillation loss

#### 2.2. Angle-wise distillation loss

#### 2.3. Training with RKD

## Experiments

## Conclusion

## Insights & Opinion