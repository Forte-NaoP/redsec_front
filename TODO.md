클라이언트:
compiler/REDsecNetlistGenerator.xlsm 으로 모델 csv 파일 생성 
python3 compiler.py {NetworkName} {modelName}.csv 으로 REDSec Code 생성

python3 ../nets/{NetworkName}/{modelName}/{modelName}.py로 모델 트레이닝
트레이닝 데이터 수정을 위해 TODO 블럭 수정 필요

make weight_convert 로 REDsec Weight Conversion 수행 후 생성된 파일을 서버로 전송

추론할 이미지는 make encrypt-image로 암호화후 서버로 전송

서버:
클라이언트에서 받은 REDSec Weight를 make (cpu | gpu)-encrypt로 수행 
(nets/{NetworkName}/{modelName}/main.cpp를 수정하여 추론할 이미지 파일 위치 수정 필요)

생성된 결과물을 클라이언트로 전송


클라이언트:
make decrypt-image로 서버에서 받은 추론 결과 확인

파일 위치가 하드코딩된것이 많아 수정 필요
키 생성과정에서 secret.key와 eval.key (public key)생성됨 각각 위치 하드코딩되서 마찬가지로 수정 필요'


웹 페이지의 구조로는 
로그인 -> 모델 선택 -> 이미지 업로드 -> 추론 결과 확인
이고 모델 선택 페이지에서 클라이언트를 구성할 수 있도록 파일을 다운로드 가능하게 하고
생성된 파일을 서버로 업로드해서 모델을 등록하도록 함
이미지 업로드 페이지에서는 이미지를 업로드하면 추론 결과를 확인할 수 있도록 함


# frontend 구현사항
- 로그인 페이지 [V]
    - 사용자가 로그인 하지 않았으면 로그인 페이지로 이동 [V]
- 클라이언트 다운로드 페이지 [V]
- 모델 업로드 페이지
    - 모델 업로드 하면 사용자, 모델 명, 파일 위치를 DB에 저장
- 모델 선택 페이지
    - DB에서 사용자 명으로 된 모델들 리스트업    
- 모델 선텍후 이미지 업로드 페이지
    - 추론 결과 페이지도 포함 


# backend 구현사항
- 로그인 [V]
- 클라이언트 다운로드 [V]
- 모델 업로드
    - DB 스키마 수정해서 사용자, 모델 명, 파일 위치를 DB에 저장
- 모델 선택
    - DB에서 사용자 명으로 된 모델들 리스트 돌려줌
- 이미지 업로드
- 추론 결과