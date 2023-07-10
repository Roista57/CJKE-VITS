# CJKE-VITS

## Runpod Guide
### 업데이트 및 필요 라이브러리 설치
```sh
apt-get update
apt-get install cmake ffmpeg unzip zip -y
```

### 아나콘다 설치
```sh
wget https://repo.anaconda.com/archive/Anaconda3-2023.03-1-Linux-x86_64.sh
bash Anaconda3-2023.03-1-Linux-x86_64.sh
```

### 아나콘다 가상환경 생성
```sh
source ~/.bashrc
conda create -n vits python=3.8
```

### 레포지토리 복사 및 라이브러리 설치
```sh
conda activate vits
git clone https://github.com/Roista57/CJKE-VITS.git
cd /workspace/CJKE-VITS/
pip install torch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 --index-url https://download.pytorch.org/whl/cu117
pip install -r requirements.txt
pip install gdown
cd ..
```

### cudnn 설치
```sh
gdown 1Ju3l5C_lmE09bbeVMLZObU6PNW0dw2gm
tar -xvf cudnn-11.5-linux-x64-v8.3.0.98.solitairetheme8
cd cuda
cp include/cudnn* /usr/local/cuda/include
cp lib64/libcudnn* /usr/local/cuda/lib64
chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
ldconfig -N -v $(sed 's/:/ /' <<< $LD_LIBRARY_PATH) 2>/dev/null | grep libcudnn
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64/
cd ..
```

### 대사 train과 val로 나눔
```
python random_pick.py --filelist path/to/filelist.txt
```

### 전처리
```
python preprocess.py --out_extension cleaned --text_index 2 --filelists path/to/filelist_train.txt path/to/filelist_val.txt --text_cleaners 'cjke_cleaners2'
```

### 학습
```
python train_ms.py -c path/to/config.json -m /workspace/CJKE-VITS/model/model_name
```

## Jupyter notebook
### 필요한 라이브러리 설치
```
!pip install tensorboard
```

### 텐서보드 열기
```
%load_ext tensorboard
%tensorboard --logdir="cd /workspace/CJKE-VITS/models/kss" --port 6006
```

### ngrok 설치
```
!rm -f ngrok
!wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
!unzip -o ngrok-stable-linux-amd64.zip
```

### 토큰 설정(회원 가입 필요)
```
https://dashboard.ngrok.com/get-started/your-authtoken
```
### copy 버튼을 누르고 your_token = "붙여넣기"
```
your_token = ""
!./ngrok authtoken {your_token}
```

### ngrok 백그라운드 실행
```
get_ipython().system_raw('./ngrok http 6006 &')
```

### 실행 중인 ngrok URL 확인
```
import json
import urllib.request

ngrok_info = urllib.request.urlopen('http://localhost:4040/api/tunnels')
tunnels = json.load(ngrok_info)
tunnel_url = tunnels['tunnels'][0]['public_url']
print(tunnel_url)
```

## 추론
### 새로운 터미널을 생성한 경우
```
conda activate vits
cd /workspace/CJKE-VITS/
python inference.py model_name global_step
```
