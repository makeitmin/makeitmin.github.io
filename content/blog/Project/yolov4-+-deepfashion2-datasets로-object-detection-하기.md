---
title: YOLOv4 + DeepFashion2 Datasets로 Object Detection 하기
date: 2021-05-25 07:08:06
category: Project
thumbnail: { thumbnailSrc }
draft: false
---

이 글의 Code Snippets는 Jupyter Notebook에서 Code Shell 단위로 실행됩니다. 코드 전문은 [GitHub Repository](https://github.com/makeitmin/yolov4-deepfashion2/blob/master/YOLOv4_DeepFashion2.ipynb)에 있습니다.  
  
YOLO는 Darknet이라는 프레임워크를 사용합니다.  
Darknet은 YOLOv4 뿐 아니라 다양한 버전의 YOLO 모델을 서빙하고 있습니다.

```shell
!git clone https://github.com/AlexeyAB/darknet.git
```
해당 Darknet Repository의  [README.md](https://github.com/AlexeyAB/darknet#how-to-evaluate-fps-of-yolov4-on-gpu)  에서는 GPU를 사용한 YOLOv4 를 학습하기 전 Darknet 프레임워크를 초기화(빌드)하는 방법을 설명하고 있습니다.  

## Makefile 빌드하기

다음과 같이 darknet/Makefile의 앞부분을 수정합니다.

```
GPU=1
CUDNN=1 
CUDNN_HALF=1 
OPENCV=1
LIBSO=1
```

Makefile을 빌드합니다.

```shell
%cd darknet/
!make
!mkdir DeepFashion2
%cd ../
```

## 데이터셋 가져오기

[DeepFashion2](https://github.com/switchablenorms/DeepFashion2)  를 본인의 Google Drive 에 받는 자세한 방법은  [링크](https://beausty23.tistory.com/82)를 참고하세요.  
DeepFashion2 의 데이터셋이 매우 방대하므로 이 학습에서는 Validation Set 만을 받아 Train 과 Validation으로 나누어 사용합니다.
본인의 Google Drive 를 마운트하여,

```python
from google.colab import drive
drive.mount('/content/gdrive')
```

[데이터셋](https://drive.google.com/drive/folders/125F48fsMBz2EF0Cpqk6aaHet5VH399Ok)을 복사합니다.  

```shell
!cp /content/gdrive/MyDrive/validation.zip /content/darknet/DeepFashion2/validation.zip
```

패스워드와 함께 데이터셋의 압축을 해제합니다.

```shell
!unzip -P "패스워드" /content/darknet/DeepFashion2/validation.zip -d /content/darknet/DeepFashion2/
```

## Annotation Format 변환하기

다음은 클래스를 나누어 각각의 이미지와 그에 대응하는 클래스, 어노테이션을 묶는 validation.json 파일을 생성하는 코드입니다. 이 작업이 필요한 이유는 현재 DeepFashion2 의 Annotation Format 이 YOLOv4 가 요구하는 Format 과 맞지 않기 때문입니다.  
  
다음과 같은 순서로 Format 을 맞출 예정입니다.

1.  DeepFashion2 의 Annotation Format 을 COCO Format 으로 변환
2.  COCO Format 을 YOLO Format 으로 변환

아래 셀의 코드는 1번의 과정입니다.  
DeepFashion2 의 데이터셋은 총 13개 클래스입니다.

```shell
!mkdir /content/darknet/DeepFashion2/validation/annotations
```

다음 폴더를 만든 후 코드를 실행합니다.

```python
from PIL import Image
import numpy as np
import json

dataset = {
    "info": {},
    "licenses": [],
    "images": [],
    "annotations": [],
    "categories": []
}

lst_name = ['short_sleeved_shirt', 'long_sleeved_shirt', 'short_sleeved_outwear', 'long_sleeved_outwear',
            'vest', 'sling', 'shorts', 'trousers', 'skirt', 'short_sleeved_dress',
            'long_sleeved_dress', 'vest_dress', 'sling_dress']

for idx, e  in enumerate(lst_name):
    dataset['categories'].append({
        'id': idx + 1,
        'name': e,
        'supercategory': "clothes",
        'keypoints': ['%i' % (i) for i in range(1, 295)],
        'skeleton': []
    })

num_images = 32153 #191961 
sub_index = 0  # the index of ground truth instance
for num in range(1, num_images + 1):
    json_name = '/content/DeepFashion2/validation/annos/' + str(num).zfill(6) + '.json'
    image_name = '/content/DeepFashion2/validation/image/' + str(num).zfill(6) + '.jpg'

    if (num >= 0):
        imag = Image.open(image_name)
        width, height = imag.size
        with open(json_name, 'r') as f:
            temp = json.loads(f.read())
            pair_id = temp['pair_id']

            dataset['images'].append({
                'coco_url': '',
                'date_captured': '',
                'file_name': str(num).zfill(6) + '.jpg',
                'flickr_url': '',
                'id': num,
                'license': 0,
                'width': width,
                'height': height
            })
            for i in temp:
                if i == 'source' or i == 'pair_id':
                    continue
                else:
                    points = np.zeros(294 * 3)
                    sub_index = sub_index + 1
                    box = temp[i]['bounding_box']
                    w = box[2] - box[0]
                    h = box[3] - box[1]
                    x_1 = box[0]
                    y_1 = box[1]
                    bbox = [x_1, y_1, w, h]
                    cat = temp[i]['category_id']
                    style = temp[i]['style']
                    seg = temp[i]['segmentation']
                    landmarks = temp[i]['landmarks']

                    points_x = landmarks[0::3]
                    points_y = landmarks[1::3]
                    points_v = landmarks[2::3]
                    points_x = np.array(points_x)
                    points_y = np.array(points_y)
                    points_v = np.array(points_v)
                    case = [0, 25, 58, 89, 128, 143, 158, 168, 182, 190, 219, 256, 275, 294]
                    idx_i, idx_j = case[cat - 1], case[cat]

                    for n in range(idx_i, idx_j):
                        points[3 * n] = points_x[n - idx_i]
                        points[3 * n + 1] = points_y[n - idx_i]
                        points[3 * n + 2] = points_v[n - idx_i]

                    num_points = len(np.where(points_v > 0)[0])

                    dataset['annotations'].append({
                        'area': w * h,
                        'bbox': bbox,
                        'category_id': cat,
                        'id': sub_index,
                        'pair_id': pair_id,
                        'image_id': num,
                        'iscrowd': 0,
                        'style': style,
                        'num_keypoints': num_points,
                        'keypoints': points.tolist(),
                        'segmentation': seg,
                    })

json_name = '/content/darknet/DeepFashion2/validation/validation.json'
with open(json_name, 'w') as f:
    json.dump(dataset, f)
```

YOLO Format 의 Annotations 가 저장될 폴더를 생성합니다.

```shell
%cd /content/darknet/DeepFashion2
!mkdir YOLO/
```

아래 셀의 코드는 2번의 과정입니다.  
DeepFashion2 폴더 내에 다음 2개의 파일을 추가한 후 실행해야 합니다.

1.  [example.py](https://github.com/ssaru/convert2Yolo/blob/master/example.py)
2.  [Format.py](https://github.com/ssaru/convert2Yolo/blob/master/Format.py)

```shell
!python ./example.py --datasets COCO --img_path ./validation/image/ --label ./validation/annotations/validation.json --convert_output_path ./YOLO/ --img_type ".jpg" --manifest_path ./validation/ --cls_list_file ./validation/deepfashion2.names
```

위 작업이 끝나면 다음의 것들이 생성됩니다.

-   YOLO/*.txt : 이미지별 바운딩 좌표가 Yolo Format 으로 변환되어 있습니다.
-   manifest.txt : 모든 이미지의 경로를 모아 놓은 파일입니다.

## 데이터 전처리하기

데이터를 shuffle 하는 코드입니다.

```python
import random 
txt = open('/content/darknet/DeepFashion2/validation/manifest.txt','r') 
f = open('/content/darknet/DeepFashion2/validation/manifest_shuffle.txt','w') 
tmp = [] 
while True : 
  line = txt.readline() 
  if not line: 
    break 
  tmp.append(line) 
random.shuffle(tmp) 
for i in tmp : 
  f.write(i) 
txt.close() 
f.close()
```

shuffle 된 데이터셋을 Train 과 Validation 으로 나누는 코드입니다.

```python
count = 0 
length = 37205 #total line 
txt = open('/content/darknet/DeepFashion2/validation/manifest_shuffle.txt','r') 
i = 0 
f = open('/content/darknet/custom/train.txt','w') 
f2 = open('/content/darknet/custom/validation.txt','w') 
while True : 
  if i == 0 : 
    line = txt.readline() 
    if not line : 
      break 
    count +=1 
    if count < int(length/10)*2 : 
      f2.write(line) 
    else : 
      f.write(line) 
txt.close() 
f.close() 
f2.close()
```

## YOLOv4 학습하기

첫 학습에 사용할 초기 모델로 yolov4.conv.137 을 가져옵니다.

```shell
!gdown --id 1JKF-bdIklxOOVy-2Cr5qdvjgGpmGfcbp -O /content/darknet/yolov4.conv.137
```

darknet 폴더로 이동합니다.

```shell
%cd /content/darknet
```

YOLOv4 학습을 시작합니다.

```shell
!./darknet detector train custom/custom.data cfg/yolov4.cfg yolov4.conv.137 -map -dont_show
```

## 참고자료

-   Use YOLOv4 to train the DeepFashion2 Dataset ([문서](https://www.programmersought.com/article/74557705857/))

-   Darknet 학습 준비하기 ([블로그](https://eehoeskrap.tistory.com/367))

-   YOLOv4 custom 데이터 훈련하기 ([블로그](https://keyog.tistory.com/22?category=879585))

-   Colab에서 Detectron2로 의류 인식하기 ([블로그](https://airsbigdata.tistory.com/208))

-   ssaru/convert2Yolo ([GitHub](https://github.com/ssaru/convert2Yolo))
