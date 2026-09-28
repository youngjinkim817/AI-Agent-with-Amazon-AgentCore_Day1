# Pre-Lab. 인프라 셋업 (개발 환경 설정)

**소요 시간**: 약 15분 (대부분 대기 시간)

| # | 위치              | 할 일                                             |
| - | --------------- | ----------------------------------------------- |
| 1 | **Console**     | SageMaker Studio 도메인 생성                         |
| 2 | **CloudShell**  | `onestop.sh` → `grant-sagemaker-permissions.sh` |
| 3 | **Code Editor** | `setup-python.sh`                               |

> ⚠️ **순서를 지켜 주세요.** 1단계에서 `AmazonSageMaker-ExecutionRole-*` 이 자동 생성되고, 2단계가 그 role 에 Bedrock 권한을 붙입니다. 도메인이 없으면 2단계가 아무 일도 하지 않고 넘어가고, Lab 실행 때 `AccessDeniedException` 이 납니다.

> ⚠️ **0단계 — Anthropic 모델 First Time Use 양식** (아직 안 했다면 지금)
>
> 이 워크샵은 Claude 모델을 씁니다. 공식 문서는 *"For Anthropic models, you must complete the First Time Use (FTU) form before invoking the model"* 라고 명시합니다 — **계정당 1회 필수**입니다. 안 하면 Lab 1 에서 `AccessDeniedException` 으로 막힙니다.
>
> Console → **Amazon Bedrock** → **Model catalog** → Anthropic 모델 선택 → use case 양식 제출 (제출 즉시 활성화됩니다. 사용 목적 + 웹사이트/GitHub URL 필요)
>
> 이미 이 계정에서 Claude 를 호출해 본 적이 있으면 건너뛰어도 됩니다. 확인:
>
> ```bash
> aws bedrock get-foundation-model-availability \
>   --model-id anthropic.claude-haiku-4-5-20251001-v1:0 --region us-east-1
> ```
>
> `agreementAvailability.status` 가 `AVAILABLE` 이면 완료 상태입니다. 자세한 내용은 [시작하기 전에](../00.md) 참고.

## 1단계. SageMaker Studio 도메인 만들기

1. Console 검색창에 **SageMaker AI** → 좌측 메뉴 **Studio**
2. **Create domain** → **Set up for single user (Quick setup)** → **Set up**

Quick setup 이면 VPC·IAM 을 직접 만질 필요가 없습니다. 기본값 그대로 두세요.

**생성은 3-5분 걸립니다. 기다리지 말고 바로 2단계로 넘어가세요** — 백그라운드에서 만들어지는 동안 CloudShell 작업을 하면 됩니다.

## 2단계. CloudShell 에서 인프라 배포

Console 우상단 **CloudShell 아이콘** → 터미널이 준비되면:

```bash
git clone https://github.com/Hannah-ireum/thewhoo-agentcore-workshop.git
cd thewhoo-agentcore-workshop
./scripts/onestop.sh w001   # 진행자가 다른 PID 를 안내했으면 그 값으로
```

10분 정도 걸립니다. 마지막 출력의 **`KB_ID` 값을 기억**해두세요 (3단계에서 씁니다).

```
 ✓ Pre-Lab 셋업 완료
  export PARTICIPANT_ID=w001
  export AWS_REGION=us-east-1
  export KB_ID=<onestop.sh 가 출력한 10자리 KB_ID>
```

이어서 SageMaker role 에 권한을 추가합니다. **1단계 도메인이 InService 가 된 뒤에** 실행하세요.

```bash
./scripts/grant-sagemaker-permissions.sh
```

`정책 적용 완료` 가 보이면 정상입니다. `[알림] ... execution role 이 없습니다` 가 나오면 도메인이 아직 안 만들어진 것이니, 조금 기다렸다가 다시 실행하세요.

> PID 를 `w001` 에서 바꿨다면 이후 모든 명령에서 같은 값을 써야 합니다 — 한 곳만 달라도 IAM 에러의 원인이 됩니다.

## 3단계. Code Editor 에서 Python 셋업

> 터미널은 열릴 때 자격증명을 캐시합니다. **2단계 권한 부여를 끝낸 뒤에** 열어 주세요. 이미 열어두셨다면 닫고 새로 여세요.

1. Console → **SageMaker AI** → **Studio** → 도메인 사용자 옆 **Open Studio**
2. 좌측 **Applications → Code Editor** → **Create Code Editor space**
3. 이름은 자유롭게 (`workshop`) → **Create space** → 기본값 그대로 **Run space**
4. 상태가 **Running** 이 되면 (2-3분) **Open** → VS Code 화면
5. 상단 메뉴 **Terminal → New Terminal**

터미널에서:

```bash
cd ~
git clone https://github.com/Hannah-ireum/thewhoo-agentcore-workshop.git
cd thewhoo-agentcore-workshop
./scripts/setup-python.sh

source .venv/bin/activate   # setup-python.sh 가 만든 venv 를 현재 터미널에 적용
which python                # .venv/bin/python 이 나와야 정상

# CloudShell 에서 복사한 값
export PARTICIPANT_ID=w001
export AWS_REGION=us-east-1
export KB_ID=<위에서 기억해둔 값>
```

> ⚠️ **`source .venv/bin/activate` 는 터미널을 새로 열 때마다 다시 해야 합니다.** 프롬프트에 `(.venv)` 가 보이면 정상입니다. 빠뜨리면 시스템에 깔린 구버전 strands 가 잡혀 `ImportError: cannot import name 'CacheConfig'` 가 납니다 (에러 경로에 `/opt/conda`).

> **Day 2 를 진행하실 분** — Lab 5 배포는 AgentCore CLI 를 씁니다. 이 CLI 는 **npm 패키지**입니다 (npm = Node.js 의 패키지 관리자, Python 의 `pip` 에 해당). **배포 도구만 Node 이고 에이전트 코드는 그대로 Python** 입니다. Code Editor 에 Node.js 가 이미 있으니 버전만 확인해 두세요 — `node --version` 이 **v20 이상**이면 됩니다. CLI 설치와 `cdk bootstrap` 은 모두 Lab 5 에서 안내합니다.

## 여기까지 됐으면 성공

* [ ] `grant-sagemaker-permissions.sh` 출력에 **`정책 적용 완료`**
* [ ] Code Editor space 가 **Running** 이고 터미널이 열림
* [ ] `which python` 이 **`.venv/bin/python`** 을 가리킴
* [ ] `echo $KB_ID` 가 값을 돌려줌

## 잘 안 될 때

| 증상                                                              | 해결                                                                                                                         |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `grant-sagemaker-permissions.sh` 가 `execution role 이 없습니다` 만 출력 | 1단계 도메인이 아직 없거나 생성 중. InService 확인 후 **다시 실행**                                                                             |
| `AccessDeniedException bedrock:InvokeModel`                     | ① `정책 적용 완료` 출력 확인 ② Code Editor 터미널을 닫고 새로 열기(자격증명 캐시) ③ 도메인을 CloudShell 작업 후에 만들었다면 `grant-sagemaker-permissions.sh` 재실행 |
| `ImportError: cannot import name 'CacheConfig'`                 | venv 미활성화. `cd ~/thewhoo-agentcore-workshop && source .venv/bin/activate`                                                  |
| `ModuleNotFoundError: No module named 'bedrock_agentcore'`      | 같은 원인. 위와 동일                                                                                                               |
| CloudShell 에서 "SageMaker execution role 로 실행 중"                 | Code Editor 가 아닌 **CloudShell** 탭에서 실행해야 함                                                                                 |
| `ResourceExistenceCheck`                                        | 이전 배포 잔해. `aws cloudformation delete-stack --stack-name thewhoo-<pid>` 후 재실행                                               |
| `Knowledge Base 생성 실패 (role propagation)`                       | IAM 전파 대기. `./scripts/onestop.sh <pid>` 한 번 더                                                                              |
| Bedrock 모델 활성화 실패 경고                                            | Console → Bedrock → Model catalog → 해당 모델 Playground 에서 메시지 1회 전송                                                          |

## 세션이 끊어졌다면

Code Editor 재접속 후 두 가지를 다시 해야 합니다.

```bash
cd ~/thewhoo-agentcore-workshop
source .venv/bin/activate               # venv 활성화
eval "$(./scripts/print-env.sh w001)"   # 환경변수 복구
echo "KB_ID=$KB_ID"                     # 값이 찍혀야 정상
```

`print-env.sh` 는 **이미 만들어진 리소스만** 출력합니다. Memory·Gateway 는 Lab 2/3 에서 만들기 전까지 비어있는 것이 정상입니다. 자세한 내용은 [환경변수 복구 가이드](../reference/env-recovery.md) 참고.

CloudShell 은 인프라가 유지되므로 재실행이 필요 없습니다.

## 다음

[**Lab 0. 데이터 준비하기**](01-lab0.md)
