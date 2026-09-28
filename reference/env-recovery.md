# 환경변수 복구 (세션 끊겼을 때)

Code Editor 나 CloudShell 세션이 끊겨 환경변수(`KB_ID`, `AGENTCORE_MEMORY_ID`, `AGENTCORE_GATEWAY_URL`, `AGENT_RUNTIME_ARN`)가 사라졌을 때 복구하는 법입니다.

## 방법 1: 한 줄 복원 (권장)

`scripts/print-env.sh` 가 계정을 뒤져서 현재 존재하는 리소스의 export 구문을 출력합니다. `eval` 로 바로 적용하면 끝:

```bash
cd ~/thewhoo-agentcore-workshop
eval "$(./scripts/print-env.sh w001)"    # w001 은 할당받은 ParticipantId
echo "KB_ID=$KB_ID"
```

> **`eval` 을 치면 화면에 아무것도 안 찍히는 게 정상입니다.** `eval` 은 스크립트의 `export ...` 출력을 **환경변수로 설정**만 하고 화면에는 표시하지 않습니다. 바로 다음 줄 `echo "KB_ID=$KB_ID"` 로 값이 들어왔는지 확인하세요.
>
> 스크립트가 **무엇을 export 하는지** 직접 보고 싶으면 `eval` 없이 실행:
>
> ```bash
> ./scripts/print-env.sh w001
> ```

출력 결과를 그대로 export 하므로 KB / Memory / Gateway / Runtime 중 존재하는 것만 자동으로 세팅됩니다.

## 방법 2: 개별 조회 명령

각 값이 왜 그 값인지 이해하고 싶으면 개별 조회:

```bash
# KB_ID
aws bedrock-agent list-knowledge-bases \
  --query "knowledgeBaseSummaries[?name=='thewhoo-kb-w001'].knowledgeBaseId" \
  --output text

# AGENTCORE_MEMORY_ID
# 주의: ListMemories 응답 배열은 'memories' 이고, 각 항목에 name 필드가 없습니다.
#       (이름은 id 앞부분에 들어갑니다 → id 로 필터)
aws bedrock-agentcore-control list-memories \
  --query "memories[?starts_with(id, 'TheWhooMemory')].id" \
  --output text

# AGENTCORE_GATEWAY_URL
# 주의: ListGateways 응답에는 gatewayUrl 이 없습니다. id 를 먼저 찾고 GetGateway 로 조회.
GW_ID=$(aws bedrock-agentcore-control list-gateways \
  --query "items[?contains(name, 'thewhoo-gateway')].gatewayId | [0]" \
  --output text)
aws bedrock-agentcore-control get-gateway \
  --gateway-identifier "$GW_ID" \
  --query 'gatewayUrl' --output text

# AGENT_RUNTIME_ARN (Lab 5 이후)
aws bedrock-agentcore-control list-agent-runtimes \
  --query "agentRuntimes[?contains(agentRuntimeName, 'hewhoo')].agentRuntimeArn" \
  --output text
```

## 방법 3: 영구 저장 (선택)

매번 입력하기 귀찮으면 `~/.bashrc` 에 한 번만 추가:

```bash
cat >> ~/.bashrc <<'EOF'

# thewhoo-agentcore-workshop env
export PARTICIPANT_ID=w001
export AWS_REGION=us-east-1
eval "$($HOME/thewhoo-agentcore-workshop/scripts/print-env.sh $PARTICIPANT_ID 2>/dev/null)"
EOF
```

그 다음 새 터미널 열면 자동으로 복원됩니다.
