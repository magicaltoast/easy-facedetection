# Easy Face Detection
Proof of concept for including post and pre-processing steps inside onnx model.

In simple words you pass ``[width, height, 3] uint8`` tensor to the model and you get back key points, scores, and bounding boxes.

Example inference in python can be found [here](https://github.com/magicaltoast/easy-facedetection/blob/main/inference_example.ipynb), and models files [here](https://github.com/magicaltoast/easy-facedetection/tree/main/models)
# Naming convention
Model name starts with the name of the original model, if size_adjust is included in the name model will accept dynamically sized tensor and adjust the detection results to the size of the original image. 
The last number is the size on which the model performs inference.
# Upsides
- Ease of you in any language and runtime of your choice
- Reduction of duplicate code for decoding detection results
# Downsides
- Batch inference
  - Keras can't produce inputs without batch dimension. So you need to modify the graph directly if you achieve that.
  - Exported models have hardcoded batch dimensions to 1
  - Impossibility of [concatination ragged tensors with more than one dimension](https://github.com/onnx/tensorflow-onnx/issues/1817)
  - Proof of concept code can be found [here](https://github.com/magicaltoast/easy-facedetection/blob/main/batch_example.ipynb)
- Dynamic input size without resizing
    - SCRFD models can accept the image that size is dividable by 32, but I couldn't make it work.
- Image resizing could be not as efficient as a native implementation
- The outputs names produced by [tf2onnx](https://github.com/onnx/tensorflow-onnx) are dependent on the number of layers that TensorFlow created, so code won't work if you run in the wrong order or you try to generate two models in the same runtime. 
- Tools for modifying and manipulating onnx graphs have rough corners and are not really mature
# Motivation and afterthoughts
The original motivation was the need to re-write decoding steps while using another detection model trained on the coco dataset in nodejs using onnxruntime.

Also, I needed to preprocess the image - normalize it, add batch dimension, convert it to float32.

I am not arguing that including everything inside your model is the best or even good choice but changing your processing pipeline from NumPy and cv2 to your deep learning framework of choice would allow the conversion to other formats, and potentially save some redundant code when you need your model in another language and shouldn't be a lot of trouble when you have original code with original weights and are not mixing frameworks as shown in this repo.
# License
The code in this repository is released under the MIT License.

But base weights for models come from [InsightFace](https://github.com/deepinsight/insightface/tree/master/python-package) python package, and they are available for non-commercial research purposes only,
