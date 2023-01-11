# Tahaluf-project

![logo](https://user-images.githubusercontent.com/61900536/211860333-ecf767e2-4521-4ead-ad2e-a292373a2e9b.png)

# VS

![splash](https://user-images.githubusercontent.com/61900536/211860109-a0f40f5b-7ede-444c-aa2b-f53288872fb1.png)

## In this notebook we are going to compare YoloX with YoloV5 and proof that YoloX achieves better results.

In trying to make the comparison as fair as possible. I used the folloing models

1. YOLOX-s:

| Model | size | mAPval_0.5:0.95 | mAPtest_0.5:0.95 | Speed V100 (ms) | Params(M) | FLOPs (G) |
| ----------- | ----------- | ----------- | ----------- | ----------- | -----------  | ----------- | 
YOLOX-s |	640	| 40.5	| 40.5	| 9.8	| 9.0	| 26.8


2. YOLOv5s:

Model	| size (pixels)	| mAPval_50-95 | Speed V100 b1 (ms)	| Speed V100 b32 (ms)	| params (M) |	FLOPs @640 (B) |
| ----------- | ----------- | ----------- | ----------- | ----------- | -----------  | ----------- | 
YOLOv5s	| 640	| 37.4	| 6.4	| 0.9	| 7.2	| 16.5
