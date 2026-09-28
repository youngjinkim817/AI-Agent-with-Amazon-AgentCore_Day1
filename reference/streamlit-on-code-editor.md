# Streamlit UI 여는 법 (Code Editor)

각 Lab 의 `streamlit_labN.py` 를 실제로 브라우저에서 보려면 Code Editor 의 **포트 포워딩** 을 써야 합니다. 로컬 PC 가 아닌 SageMaker Studio 안이라 `localhost:8501` 가 브라우저에서 자동으로 열리지 않습니다.

## 1단계. venv 활성화 후 Streamlit 실행

항상 **프로젝트 루트**(`~/thewhoo-agentcore-workshop`) 에서 실행하세요.

```bash
cd ~/thewhoo-agentcore-workshop
source .venv/bin/activate

# Lab 번호에 맞게 파일만 바꿔 실행
streamlit run src/streamlit_lab1.py --server.port 8501
```

> `source .venv/bin/activate` 가 누락되면 `streamlit: command not found` 가 납니다. 새 터미널마다 1회 필요 (`~/.bashrc` 에 pyenv 가 등록돼 있어 Python 자체는 문제없지만 venv 는 직접 activate 필요).

## 2단계. 포트 포워딩으로 브라우저 열기

Code Editor (VS Code) 화면 하단에서:

1. **Ports** 탭 클릭 (없으면 `View → Ports` 메뉴)
2. **Forward a Port** 또는 **Add Port** → `8501` 입력 → Enter
3. 방금 추가된 8501 행의 **지구본 아이콘** 또는 **Open in Browser** 클릭
4. 새 탭에서 `https://*.studio.<region>.sagemaker.aws/...` URL 로 Streamlit UI 가 열림

## 끌 때

* 터미널에서 `Ctrl + C` 로 streamlit 종료
* Ports 탭에서 8501 우클릭 → **Stop Forwarding Port** (선택)

## 포트 충돌 시

이미 다른 Lab 의 Streamlit 이 8501 에 떠 있으면 포트를 바꿔 실행:

```bash
streamlit run src/streamlit_lab2.py --server.port 8502
```

Ports 탭에서 새 포트를 다시 Forward 해야 합니다.
