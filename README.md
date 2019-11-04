# ML_CRNN
영상의 단어 검출을 위한 CRNN 구현입니다.
##### 개인 공부 목적으로 제작되었으며 어떤 상업적 의지도 없음을 미리 말씀드립니다.  
##### 저작권 관련 문제가 일어날 경우 바로 내리도록 하겠습니다.  
#### data set  
##### COCO text data 2014버전을 인풋으로 사용했습니다. COCO_Text.json과 같은 API들을 사용해 얻어지는데, 다음과 같은 세가지 특성을 가집니다.
##### 1. captions > 텍스트로 된 그림에 대한 설명  
##### 2. instances > 그림에 있는 사람 / 사물에 대한 category와 영역 mask  
##### 3. person_keypoint > 사람의 자세 데이터  
##### 이중에 1번, captions를 사용해 model training / test를 진행하게 됩니다.
---
#### preporcessing  
##### datasets은 이미지에 있는 문자열을 포함하는 BOX의 점들(4개 이상의 polygon을 구성하는 점들 일수 있습니다)과 그 문자열로 구성되어 있으며 한 이미지에 다수의 문자열들이 포함되어 있을 수 있습니다.  
##### 다음과 같은 방법으로 전처리를 진행하게 됩니다.  
##### 1. polygon to rectangle  
##### polygon을 통해서 x, y값이 가장 작은 점과 x, y값이 가장 큰 점 두점을 뽑아내게 됩니다. 그후 이 두점을 통해서 사각형을 구성합니다.  
##### 2. unreadable data deletion
##### COCO data에 스페인어와 특수문자들이 존재하는 경우가 있습니다. 또한 특수문자의 경우 i와 !가 이미지가 조금만이라도 흐려진다면 구분하기 어렵기 때문에 이런 경우 데이터셋에서 제외했습니다.
##### 3. sort by the number of apperance character  
##### e, i와 같이 문자열에서 가장 많이 나타나는 문자들을 앞에 배치함으로써 더 빠르게 찾을 수 있도록 구성합니다. 적게나마 성능을 향상시키기 위해 사용합니다.
---
#### text detection : CNN  
##### 문자를 찾는 모델은 3step으로 구성되어 있습니다.  
##### 1. feature extracting
##### convolution filers로 각자의 기능들을 추출하게 됩니다. CNN에서 image를 인식하는 모델 중 performance가 높은 모델을 가져왔습니다.
##### 2. feature merging
##### max pooling과 비슷한 원리를 통해서 정보들을 합쳤습니다. 이때 candy edge detection에 있는 알고리즘 일부를 채택했습니다.  
##### 2-1. non-maximum suppression  
##### 중심 필터를 기준으로 3 X 3 8개의 픽셀이 중심 픽셀보다 값이 작으면 놔두고, 만약 큰 값이 하나라도 존재한다면 0으로 변경하는 방식입니다.  
---
#### text recognition : RNN  
##### 문자를 인식하는 모델은 RNN의 LSTM을 채택했습니다. LSTM의 경우 양방향과 Lexicon-free를 사용했습니다. Lexicon-based model의 경우 보통 높은 hit ration를 보이지만, 이미지 파일의 경우 사전에 등록되지 않은 상표들이 많이 존재하기 때문에 예측과정이 없는 것이 더 높다고 판단했습니다.  
---
### PDF파일에 CRNN관련 정리글이 존재합니다. 
