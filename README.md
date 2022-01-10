# Easy Face Detection
Proof of concect for including post and pre processing steps inside onnx model.

In simple words you pass ``[width, height, 3] uint8`` tensor to the model and you get back keypoints, scores and bounding boxes.

Example inference in python can be found [here](https://github.com/magicaltoast/easy-facedetection/blob/main/inference_example.ipynb), and models files [here](https://github.com/magicaltoast/easy-facedetection/tree/main/models)
# Naming convention
Model name starts with name of orginal model, if size_adjust is included in the name model will accept dynamicly sized tensor and adjust the detection results to the size of orginal image. 
The last number is the size on which model perform inference.
# Upsides
- Ease of you in any language and runtime of your choice
- Reduction of duplicate code for decoding detection results
# Downsides
- Batch inference
  - Exported models have harcoded batch dimension to 1
  - Impossibility of [concatination ragged tensors with more than one dimension](https://github.com/onnx/tensorflow-onnx/issues/1817)
  - Proof of concept code can be found [here](https://github.com/magicaltoast/easy-facedetection/blob/main/batch_example.ipynb)
- Dynamic input size without resizing
    - SCRFD models can accept image that size is dividable by 32, but i couldn't make it work.
- Image resizing could be not as efficient as native implementation
- The outputs names produced by [tf2onnx](https://github.com/onnx/tensorflow-onnx) are dependend on number of layers that tensorflow created, so code won't work if you run in wrong order or you try to generate two models in same runtime. 
# Motivation and afterthoughts
The orginal motivation was need to re-write decoding steps while using another detection model trained on coco dataset in nodejs using onnxruntime.

Also I needed to preprocessing the image - normalize it, add batch dimension, convert it to float.
# License
The code in this repository is released under the MIT License.

But base weights for models come from [InsightFace](https://github.com/deepinsight/insightface/tree/master/python-package) python package, and they are available for non-commercial research purposes only,
