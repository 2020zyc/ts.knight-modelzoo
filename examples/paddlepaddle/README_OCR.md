### OCR_DET 检测网络

- __量化__

```
# 创建模型目录
mkdir -p /my_project/quant/ocrv3_det
mkdir -p /my_project/quant/to_compiler/ocrv3_det

# 获取飞桨模型
examples/paddlepaddle/ocrv3_det/
（说明：限于编译器对规格的要求，我们下载了paddle官方ch_PP-OCRv3_det模型（链接见文末）并将模型input shape设置为[1,3,512,896]；这与官方推荐的shape=[1,3,746,1312]有所差别）

# 执行量化命令,完成paddle2onnx转换，并对模型进行量化定点
Knight --chip TX5368A quant onnx -f paddle -m examples/paddlepaddle/ocrv3_det/ocrv3_det.pdmodel -if infer_ocr_det_model -w examples/paddlepaddle/ocrv3_det/ocrv3_det.pdiparams -s /my_project/quant/ocrv3_det/ -bs 1 -i 100 -qm kl -is 1 3 512 896

# 拷贝模型到指定目录
cp /my_project/quant/ocrv3_det/ocrv3_det_quantize.onnx /my_project/quant/to_compiler/ocrv3_det/
cp /my_project/quant/ocrv3_det/dump/float/0001:input/batch_0.npy /my_project/quant/to_compiler/ocrv3_det/input.npy
```


- __编译__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/compile_dir

# 编译网络
Knight --chip TX5368A rne-compile --onnx /my_project/quant/to_compiler/ocrv3_det/ocrv3_det_quantize.onnx --outpath /my_project/quant/to_compiler/ocrv3_det/compile_dir/ --general-process 1
```

- __模拟__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/simulator_dir

# 输入数据转换成模拟器运行需要的二级制文件
python3 /TS-Knight-software/tools/npy2bin.py --input /my_project/quant/to_compiler/ocrv3_det/input.npy --output /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin

#模拟网络
Knight --chip TX5368A rne-sim --input /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/simulator_dir/

#性能分析器
mkdir -p /my_project/quant/to_compiler/ocrv3_det/profiling_dir

#分析网络
Knight --chip TX5368A rne-profiling --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/profiling_dir/
```

### OCR_CLS 分类网络

- __量化__

```
# 创建模型目录
mkdir -p /my_project/quant/ocrv3_det
mkdir -p /my_project/quant/to_compiler/ocrv3_det

# 获取飞桨模型
examples/paddlepaddle/ocrv3_det/
（说明：限于编译器对规格的要求，我们下载了paddle官方ch_PP-OCRv3_det模型（链接见文末）并将模型input shape设置为[1,3,512,896]；这与官方推荐的shape=[1,3,746,1312]有所差别）

# 执行量化命令,完成paddle2onnx转换，并对模型进行量化定点
Knight --chip TX5368A quant onnx -f paddle -m examples/paddlepaddle/ocrv3_det/ocrv3_det.pdmodel -if infer_ocr_det_model -w examples/paddlepaddle/ocrv3_det/ocrv3_det.pdiparams -s /my_project/quant/ocrv3_det/ -bs 1 -i 100 -qm kl -is 1 3 512 896

# 拷贝模型到指定目录
cp /my_project/quant/ocrv3_det/ocrv3_det_quantize.onnx /my_project/quant/to_compiler/ocrv3_det/
cp /my_project/quant/ocrv3_det/dump/float/0001:input/batch_0.npy /my_project/quant/to_compiler/ocrv3_det/input.npy
```


- __编译__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/compile_dir

# 编译网络
Knight --chip TX5368A rne-compile --onnx /my_project/quant/to_compiler/ocrv3_det/ocrv3_det_quantize.onnx --outpath /my_project/quant/to_compiler/ocrv3_det/compile_dir/ --general-process 1
```

- __模拟__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/simulator_dir

# 输入数据转换成模拟器运行需要的二级制文件
python3 /TS-Knight-software/tools/npy2bin.py --input /my_project/quant/to_compiler/ocrv3_det/input.npy --output /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin

#模拟网络
Knight --chip TX5368A rne-sim --input /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/simulator_dir/

#性能分析器
mkdir -p /my_project/quant/to_compiler/ocrv3_det/profiling_dir

#分析网络
Knight --chip TX5368A rne-profiling --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/profiling_dir/
```

### OCR_REC 识别网络

- __量化__

```
# 创建模型目录
mkdir -p /my_project/quant/ocrv3_det
mkdir -p /my_project/quant/to_compiler/ocrv3_det

# 获取飞桨模型
examples/paddlepaddle/ocrv3_det/
（说明：限于编译器对规格的要求，我们下载了paddle官方ch_PP-OCRv3_det模型（链接见文末）并将模型input shape设置为[1,3,512,896]；这与官方推荐的shape=[1,3,746,1312]有所差别）

# 执行量化命令,完成paddle2onnx转换，并对模型进行量化定点
Knight --chip TX5368A quant onnx -f paddle -m examples/paddlepaddle/ocrv3_det/ocrv3_det.pdmodel -if infer_ocr_det_model -w examples/paddlepaddle/ocrv3_det/ocrv3_det.pdiparams -s /my_project/quant/ocrv3_det/ -bs 1 -i 100 -qm kl -is 1 3 512 896

# 拷贝模型到指定目录
cp /my_project/quant/ocrv3_det/ocrv3_det_quantize.onnx /my_project/quant/to_compiler/ocrv3_det/
cp /my_project/quant/ocrv3_det/dump/float/0001:input/batch_0.npy /my_project/quant/to_compiler/ocrv3_det/input.npy
```


- __编译__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/compile_dir

# 编译网络
Knight --chip TX5368A rne-compile --onnx /my_project/quant/to_compiler/ocrv3_det/ocrv3_det_quantize.onnx --outpath /my_project/quant/to_compiler/ocrv3_det/compile_dir/ --general-process 1
```

- __模拟__

```
mkdir -p /my_project/quant/to_compiler/ocrv3_det/simulator_dir

# 输入数据转换成模拟器运行需要的二级制文件
python3 /TS-Knight-software/tools/npy2bin.py --input /my_project/quant/to_compiler/ocrv3_det/input.npy --output /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin

#模拟网络
Knight --chip TX5368A rne-sim --input /my_project/quant/to_compiler/ocrv3_det/simulator_dir/input.bin --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/simulator_dir/

#性能分析器
mkdir -p /my_project/quant/to_compiler/ocrv3_det/profiling_dir

#分析网络
Knight --chip TX5368A rne-profiling --weight /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.weight --config /my_project/quant/to_compiler/ocrv3_det/compile_dir/ocrv3_det_quantize_r.cfg --outpath /my_project/quant/to_compiler/ocrv3_det/profiling_dir/
```


## Benchmark(ImageNet)

|  模型名称     | 浮点精度  | 量化精度  | 数据量   | 推理速度(单张图片)   |
|--------------|-----------|----------|----------|--------------------|
| OCR_DET     | 69.4      | 69.2     | 500     |    3.2911ms        |
| OCR_CLS     | 71.8      | 72.1     | 30     |    2.3425ms        |
| OCR_REC     | 76.6      | 76.6     | 2077     |    10.4567ms       |

## 其他说明
上文提到的所有官方模型均可在 [官方OCRv3模型](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/doc/doc_ch/models_list.md) 找到
