# 워크샵 소개

Amazon Bedrock AgentCore 와 Strands Agents SDK 로 **더후(The History of Whoo) AI 챗봇의 핵심 기능**을 직접 만들어 보는 실습 워크샵입니다. 이 사이트는 **Day 1 — 통합 챗봇 만들기** 범위의 자료입니다.

> **코드 블록 표기 안내**
>
> * ▶ **실행** — 워크샵 진행 중 터미널에 그대로 복사해 실행할 명령
> * 📖 **참고** — 개념 이해를 돕기 위한 예시 코드 또는 자동 생성되는 파일의 형태 (직접 실행하지 않음)
> * 라벨이 없는 일반 코드 블록은 **개념 설명용 스니펫** 입니다. 실행 단계에는 ▶ 라벨이 항상 붙습니다.

## 무엇을 만드나요?

**더후(The History of Whoo)에서 고객이 이렇게 대화할 수 있는 챗봇**을 만듭니다.

> **고객**: 건성 피부에 쓰기 좋은 보습크림 뭐가 있어?
>
> **챗봇**: 건성 피부에 맞춰 2가지 추천드려요. ① 더후 천기단 화현 크림 (195,000원) — 천기단 복합 성분으로 탄력·수분 동시 케어. ② 더후 비첩 자생 수분크림 (135,000원) — 민감 피부에도 자극 없는 보습크림. 선호하시는 가격대를 알려주시면 더 좁혀드릴게요.
>
> **(같은 세션 이어서)** 저번에 추천한 첫 번째 거 재고 있어?
>
> **챗봇**: 더후 천기단 화현 크림 말씀이시죠. 현재 재고가 있고 오늘 출고 가능합니다. 건성 피부에는 토너·에센스 다음 단계에서 사용하시면 좋습니다.

이 챗봇은 한 번의 질문만 답하는 게 아니라, **고객의 피부타입과 이전 대화 맥락을 기억**하고, **상품 검색·재고·프로모션 API 를 스스로 호출**하며, **근거 기반으로 답변을 정제**합니다.

## Day 1 에서 배우는 것

* 상품 데이터를 **검색 가능한 지식창고**(Bedrock Knowledge Base + S3 Vectors) 로 구성
* **Strands Agents SDK** 로 AI 에이전트를 가볍게 구성
* **AgentCore Memory** 로 사용자 선호·대화 맥락을 저장하고 재사용
* **AgentCore Gateway + MCP** 로 외부 API 를 에이전트가 자율적으로 호출
* 여러 전문 에이전트를 **하나의 Orchestrator 아래로 묶는** 오케스트레이션 패턴

## Day 1 Lab 구성

| Lab                                                            | 내용                                                         | 주요 리소스·스크립트                                                       |
| -------------------------------------------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------- |
| [Pre-Lab](day-1/00-pre-lab.md) | 인프라 셋업 (S3, IAM, Cognito, Mock Lambda 4종, KB + S3 Vectors) | `onestop.sh`, `grant-sagemaker-permissions.sh`, `setup-python.sh` |
| [Lab 0](day-1/01-lab0.md)      | 상품 데이터를 Knowledge Base 에 색인                                | Bedrock KB ingestion                                              |
| [Lab 1](day-1/02-lab1.md)      | 근거 기반 상품 QnA 챗봇 (RAG)                                      | Strands Agent + KB retrieve                                       |
| [Lab 2](day-1/03-lab2.md)      | AgentCore Memory 로 프로필·맥락 유지                               | `create-memory.py` (4 strategy)                                   |
| [Lab 3](day-1/04-lab3.md)      | AgentCore Gateway 에 MCP 도구 등록                              | `create-gateway.py` (4 Lambda target)                             |
| [Lab 4](day-1/05-lab4.md)      | Orchestrator 로 전문 Agent 3개 통합                              | Agents-as-Tools 패턴                                                |

## Day 1 범위 밖

아래 항목은 **Day 2 세션 (배포·운영)** 또는 실서비스 적용 영역으로, 이번 Day 1 에서는 다루지 않습니다.

* AgentCore Runtime 으로의 배포 (HTTPS 엔드포인트 노출)
* AgentCore Observability 를 통한 운영 트레이스 분석
* AgentCore Evaluations 로 답변 품질 자동 검증
* 실제 상품 DB / OMS 연동 (Day 1 은 Mock Lambda 사용)
* 다국어·브랜드 톤 가이드라인, 개인정보·의료 표현 가드레일 등 프로덕션 요건

***

준비되셨다면 [시작하기 전에](00.md) → [Pre-Lab. 인프라 셋업](day-1/00-pre-lab.md) 순서로 이동하세요.
