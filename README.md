This is the code corresponding to the paper
"Audio Adversarial Examples: Targeted Attacks on Speech-to-Text"
Nicholas Carlini and David Wagner
https://arxiv.org/abs/1801.01944

To generate adversarial examples for your own files, follow the below process
and modify the arguments to attack,py. Ensure that the file is sampled at
16KHz and uses signed 16-bit ints as the data type. You may want to modify
the number of iterations that the attack algorithm is allowed to run.

WARNING: THIS IS NOT THE CODE USED IN THE PAPER. If you just want to get going
generating adversarial examples on audio then proceed as described below.

The current master branch points to code which will run on TensorFlow 1.14 and
DeepSpeech 0.4.1, an almost-recent version of the dependencies. (Large portions
of tf_logits.py will need to be re-written to run on DeepSpeech 0.5.1 which uses
a new feature extraction pipeline with TensorFlow's C++ implementation. If you
feel motivated to do that I would gladly accept a PR.)

However, IF YOU ARE TRYING TO REPRODUCE THE PAPER (or just have decided
that you enjoy pain and want to suffer through dependency hell) then you
will have to checkout commit a8d5f675ac8659072732d3de2152411f07c7aa3a and
follow the README from there.



Instructions for basic use:

## 1. Install the dependencies

pip3 install tensorflow-gpu==1.14 progressbar numpy scipy pandas==0.24.0 python_speech_features tables attrdict pyxdg

### 1a. Make sure you have installed git lfs. Otherwise later steps will mysteriously fail.

apt-get install git-lfs (if you use linux)

brew install git-lfs (if you use mac)

## 2. Clone the Mozilla DeepSpeech repository into a folder called DeepSpeech:

git clone https://github.com/mozilla/DeepSpeech.git

### 2a. install the decoder:

pip install https://taskcluster-artifacts.net/bftpNURqQkixvDjOFLSlCQ/0/public/ds_ctcdecoder-0.5.1-cp36-cp36m-manylinux1_x86_64.whl

### 2b. Checkout the correct version of the code:

(cd DeepSpeech; git checkout tags/v0.4.1)

### 2c. If you get an error with tflite_convert, comment out DeepSpeech.py Line 21
 #from tensorflow.contrib.lite.python import tflite_convert

## 3. Download the DeepSpeech model

wget https://github.com/mozilla/DeepSpeech/releases/download/v0.4.1/deepspeech-0.4.1-checkpoint.tar.gz

tar -xzf deepspeech-0.4.1-checkpoint.tar.gz

## 4. Verify that you have a file deepspeech-0.4.1-checkpoint/model.v0.4.1.data-00000-of-00001
Its MD5 sum should be

ca825ad95066b10f5e080db8cb24b165

## 5. Check that you can classify normal audio correctly

> If you want to test your own .wav files, you can record the .wav file by using the relevant software, or using the SoX to convert the format of the .wav files.

python3 classify.py --in sample-000000.wav --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1

## 6. Generate adversarial examples

python3 attack.py --in sample-000000.wav --target "this is a test" --out adv.wav --iterations 1000 --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1

## 7. Verify the attack succeeded

python3 classify.py --in adv.wav --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1