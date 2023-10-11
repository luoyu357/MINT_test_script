# MIC Tutorial

This tutorial is only tested on the macOS Ventura
The MIC resource: https://mic-cli.readthedocs.io/en/latest/installation/
---
## Creating a software component from a Jupyter Notebook (not recommand)

* Requirement:
  * Python >= 3.6
  * Docker
  * Git repository: The notebook should be available in a public GitHub repository. 
  * Binder-ready repository: Prepare your repository for Binder (requires a requirements.txt or environment.yml file). Please read Get started with Binder. 
  * A DockerHub account
  * Install MIC
  
      ```
         $ pip install mic
         $ mic version
         mic v1.3.7
      ```
  * Using a Jupyter Notebook to create a software component. Use the notebook as a wrapper to call your software

* Step 1
  * a simple model hosted in the https://github.com/luoyu357/simpleModel
* Step 2
  * use mic to convert the repository to a software component
  ```
    (base) luoyu@MacBook-Pro-452 test % mic notebook read https://github.com/luoyu357/simpleModel
    (base) luoyu@MacBook-Pro-452 test % ls
     Martin's_workflow.cwl
     simpleModelAnnotated-List-Input.cwl
     simpleModelAnnotated-Output-Wildcard.cwl
     simpleModelAnnotated.cwl
  ```
  * In this tutorial, we use the ```Martin's_workflow.ipynb``` as the sample. (The dockerImageId will be different when you run the command)
  ```
  (base) luoyu@MacBook-Pro-452 test % cat Martin\'s_workflow.cwl 
  arguments:
  - --
  baseCommand: /app/cwl/bin/Martin's_workflow
  class: CommandLineTool
  cwlVersion: v1.1
  hints:
    DockerRequirement:
      dockerImageId: r2d-2fvar-2ffolders-2fc-5f-2fgjpmq4v121x-5f1-5fxfkqjfvdn00000gn-2ft-2frepo2cwl-5f2yb4udlv-2frepo1696980739
  inputs:
    batch_size:
      inputBinding:
        prefix: --batch_size
      type: int
    classes:
      inputBinding:
        prefix: --classes
      type: int
    epochs:
      inputBinding:
        prefix: --epochs
      type: int
  outputs:
    output_file:
      outputBinding:
        glob: ./saved_model.keras
      type: File
  requirements:
    NetworkAccess:
      networkAccess: true
  ```
* Step 3: Run and Test
  * install ```cwltool```
  ```
  (base) luoyu@MacBook-Pro-452 test % pip install cwltool
  ```
  * create the ```workflow.yml``` file
  ```
  classes : 10
  epochs :  1
  batch_size :  256
  ```
  * run locally and export the model file
  ``` 
  (base) luoyu@MacBook-Pro-452 test % cwltool Martin\'s_workflow.cwl workflow.yml
  INFO /Library/Frameworks/Python.framework/Versions/3.8/bin/cwltool 3.1.20230425144158
  INFO Resolved 'Martin's_workflow.cwl' to 'file:///Users/luoyu/Desktop/MINT/model/test/Martin%27s_workflow.cwl'
  INFO [job Martin%27s_workflow.cwl] /private/tmp/docker_tmpjztmua6n$ docker \
      run \
      -i \
      --mount=type=bind,source=/private/tmp/docker_tmpjztmua6n,target=/UBIvCe \
      --mount=type=bind,source=/private/tmp/docker_tmpkupfs9o9,target=/tmp \
      --workdir=/UBIvCe \
      --read-only=true \
      --user=501:20 \
      --rm \
      --cidfile=/private/tmp/docker_tmpa29d8mlv/20231010195721-387459.cid \
      --env=TMPDIR=/tmp \
      --env=HOME=/UBIvCe \
      r2d-2fvar-2ffolders-2fc-5f-2fgjpmq4v121x-5f1-5fxfkqjfvdn00000gn-2ft-2frepo2cwl-5f2yb4udlv-2frepo1696980739 \
      '/app/cwl/bin/Martin'"'"'s_workflow' \
      -- \
      --batch_size \
      256 \
      --classes \
      10 \
      --epochs \
      1
  /srv/conda/envs/notebook/lib/python3.9/subprocess.py:941: RuntimeWarning: line buffering (buffering=1) isn't supported in binary mode, the default buffer size will be used
    self.stdout = io.open(c2pread, 'rb', bufsize)
  Downloading data from https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
  170500096/170498071 [==============================] - 25s 0us/step
  2023-10-10 23:58:12.033014: I tensorflow/compiler/jit/xla_cpu_device.cc:41] Not creating XLA devices, tf_xla_enable_xla_devices not set
  2023-10-10 23:58:12.039627: I tensorflow/core/platform/cpu_feature_guard.cc:142] This TensorFlow binary is optimized with oneAPI Deep Neural Network Library (oneDNN) to use the following CPU instructions in performance-critical operations:  SSE4.1 SSE4.2 AVX AVX2 AVX512F FMA
  To enable them in other operations, rebuild TensorFlow with the appropriate compiler flags.
  2023-10-10 23:58:12.074791: I tensorflow/core/common_runtime/process_util.cc:146] Creating new thread pool with default inter op setting: 2. Tune using inter_op_parallelism_threads for best performance.
  Model: "sequential"
  _________________________________________________________________
  Layer (type)                 Output Shape              Param #   
  =================================================================
  conv2d (Conv2D)              (None, 30, 30, 32)        896       
  _________________________________________________________________
  max_pooling2d (MaxPooling2D) (None, 15, 15, 32)        0         
  _________________________________________________________________
  conv2d_1 (Conv2D)            (None, 13, 13, 64)        18496     
  _________________________________________________________________
  max_pooling2d_1 (MaxPooling2 (None, 6, 6, 64)          0         
  _________________________________________________________________
  conv2d_2 (Conv2D)            (None, 4, 4, 64)          36928     
  _________________________________________________________________
  flatten (Flatten)            (None, 1024)              0         
  _________________________________________________________________
  dense (Dense)                (None, 64)                65600     
  _________________________________________________________________
  dense_1 (Dense)              (None, 10)                650       
  =================================================================
  Total params: 122,570
  Trainable params: 122,570
  Non-trainable params: 0
  _________________________________________________________________
  2023-10-10 23:58:21.877745: I tensorflow/compiler/mlir/mlir_graph_optimization_pass.cc:116] None of the MLIR optimization passes are enabled (registered 2)
  2023-10-10 23:58:21.885189: I tensorflow/core/platform/profile_utils/cpu_utils.cc:112] CPU Frequency: 1996810000 Hz
  196/196 - 12s - loss: 1.7474 - accuracy: 0.3638
  40/40 - 1s - loss: 1.4853 - accuracy: 0.4542
  INFO [job Martin%27s_workflow.cwl] Max memory used: 5529MiB
  INFO [job Martin%27s_workflow.cwl] completed success
  {
      "output_file": {
          "location": "file:///Users/luoyu/Desktop/MINT/model/test/saved_model.keras",
          "basename": "saved_model.keras",
          "class": "File",
          "checksum": "sha1$548c1cb1874989ff92730a18caac391bb1218b88",
          "size": 1518504,
          "path": "/Users/luoyu/Desktop/MINT/model/test/saved_model.keras"
      }
  }INFO Final process status is success
  ```
  * or you can run and test another sample (files can be found in github: https://github.com/luoyu357/simpleModel)
  ```
  $ cwltool simpleModelAnnotated.cwl simpleModelAnnotatedValues.yml
  ```
* Step 4: Upload the image to your DockerHub
  * make the docker image public
  ```
  (base) luoyu@MacBook-Pro-452 test % mic notebook upload-image Martin\'s_workflow.cwl 
  Image r2d-2fvar-2ffolders-2fc-5f-2fgjpmq4v121x-5f1-5fxfkqjfvdn00000gn-2ft-2frepo2cwl-5f2yb4udlv-2frepo1696980739 detected
  Docker username detected: luoyu357
  The push refers to repository [docker.io/luoyu357/r2d-2fvar-2ffolders-2fc-5f-2fgjpmq4v121x-5f1-5fxfkqjfvdn00000gn-2ft-2frepo2cwl-5f2yb4udlv-2frepo1696980739]
  Docker Image has been updated  luoyu357/r2d-2fvar-2ffolders-2fc-5f-2fgjpmq4v121x-5f1-5fxfkqjfvdn00000gn-2ft-2frepo2cwl-5f2yb4udlv-2frepo1696980739:20231011-151315 in the CWL specification
  ```
* Step 5: Publish the ```cwl``` and image to the Model Catalog
  * push the docker image with parameters to the server
  ```
  (base) luoyu@MacBook-Pro-452 test % mic notebook upload-component Martin\'s_workflow.cwl workflow.yml
  /Users/luoyu/Desktop/MINIT WITH ICICE/model/test/Martin's_workflow_mic.yaml created
  Added: output_file as a output
  Added: batch_size as a parameter
  Added: classes as a parameter
  Added: epochs as a parameter
  ERROR:root:Exception when calling DefaultApi->user_login_get: (404)
  Reason: Not Found
  HTTP response headers: HTTPHeaderDict({'Date': 'Wed, 11 Oct 2023 19:17:29 GMT', 'Content-Type': 'application/json', 'Content-Length': '22', 'Connection': 'keep-alive', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true', 'Access-Control-Allow-Methods': 'GET, PUT, POST, DELETE, PATCH, OPTIONS', 'Access-Control-Allow-Headers': 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization', 'Access-Control-Max-Age': '1728000'})
  HTTP response body: {"detail":"Not Found"}
  ```
  * issues: the API port is not open. The MINT team uses the MIC web and cannot find this issue 
---
## MIC Web

* Requirement:
  * MINT account

* UI: https://mic.indiana.mint.isi.edu
* tutorial: https://www.youtube.com/watch?v=JmeHIbTlT_Y 
* Potential issues: the accounts that we have may have some limitations to publish models
