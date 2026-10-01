# Image Processing Team 7 · 재학습 시연 기록

2026-10-01에 프로젝트의 공개 원본 코드를 실행해 제작한 기능 시연입니다.

## 시연 범위

공개 저장소에 당시 학습 체크포인트가 없어, 공개 사진 64장과 해당 캡션으로 작은 모델을 새로 학습했습니다. GIF에 등장하는 두 사진도 이 학습 표본에 포함됩니다. 이미지 입력, Beam Search 캡션 생성, 단어별 Attention 시각화가 연결되는 동작을 보여 줍니다.

기존 `baseline1`·`baseline2` 실험과 데이터·학습 조건이 다릅니다. 이 GIF에서 검증 성능이나 새 사진에 대한 일반화 성능을 측정한 것은 아닙니다. [기존 평가 점수](https://github.com/Pongchi/ImageProcessing_Team7/blob/master/experiments.md)는 별도의 실험 기록입니다.

## 실행한 코드와 조건

- 원본: [`Pongchi/ImageProcessing_Team7`, `975d94a`](https://github.com/Pongchi/ImageProcessing_Team7/tree/975d94a95ae53eaf753904e70ee60fe75cc395c4).
- 원본 `EncoderCNN`, `DecoderRNN`, `generate_caption`, `beam_search`, Attention 계산을 그대로 호출했습니다. 원본 파일 해시는 [실행 데이터](image-processing-demo-data.json)에 기록했습니다.
- Encoder: ImageNet 사전 학습 ResNet-101, 가중치 고정. 같은 전처리로 얻은 특징을 캐시해 Decoder 학습에 사용했습니다.
- Decoder: embedding 256, attention 256, hidden 512, dropout 0.5. Beam size 3.
- 데이터: [COCO val2017](https://cocodataset.org/#download)의 CC BY 2.0 사진 64장과 사진별 캡션 한 문장을 재학습 표본으로 사용했습니다.
- 학습: seed 7, vocabulary 빈도 기준 1, vocabulary 280개, batch 16, Adam learning rate 0.0004, 142 epochs.
- 환경: Windows, RTX 4070 Ti, PyTorch 2.8.0+cu128, Torchvision 0.23.0.

## GIF와 사진

[GIF](../assets/image-processing-attention-demo.gif) · [정지 이미지](../assets/image-processing-attention-demo.png) · [기존 실험 결과 비교 이미지](../assets/image-processing-experiment-results.png)

1280×720, 16:9, 10개 프레임, 약 11.6초입니다. 실제 추론에서 얻은 단어와 14×14 Attention 값을 순서대로 표시했습니다. 히트맵은 원본 시각화 함수와 같은 OpenCV 확대 및 `jet` 색상·투명도로 표현했습니다. 히트맵의 색 범위는 단어별로 조정됩니다.

원본 `visualize_attention`과 `save_attention_group` 함수로 정적 결과도 생성했습니다. 저장한 Decoder를 다시 불러오고 사진을 다시 인코딩해, 두 캡션이 일치하고 Attention 값이 허용 오차 안에서 일치함을 확인했습니다. 표시한 단어와 원본 Attention 값은 [실행 데이터](image-processing-demo-data.json)에서 확인할 수 있습니다.

## 사진과 캡션 출처

사진은 COCO 메타데이터의 `license_id=4`([CC BY 2.0](https://creativecommons.org/licenses/by/2.0/))를 사용했습니다. 아래 원본 사진은 GIF에서 크기를 조정하고 Attention 색상을 겹쳐 표시했습니다. COCO 제공 메타데이터에는 촬영자 이름이 없어 원본 Flickr 이미지 링크를 함께 표기했습니다.

| 사진 | 원본 Flickr 이미지 | COCO 이미지 | 실제 생성 캡션 |
| --- | --- | --- | --- |
| 강아지 · COCO 17029 | [Flickr 원본](http://farm8.staticflickr.com/7304/8746020648_f1e2075b86_z.jpg) | [COCO 원본](http://images.cocodataset.org/val2017/000000017029.jpg) | `a dog jumping to catch a frisbee in a yard` |
| 기차 · COCO 14473 | [Flickr 원본](http://farm9.staticflickr.com/8401/8607706408_e00725a54d_z.jpg) | [COCO 원본](http://images.cocodataset.org/val2017/000000014473.jpg) | `a red and black train is coming down the tracks` |

캡션 주석의 출처는 COCO Consortium이며 [CC BY 4.0](https://cocodataset.org/#termsofuse)입니다.
