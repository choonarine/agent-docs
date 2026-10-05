# agent-docs

제가 개발할 때 쓰는 스킬들과 AGENTS.md를 모아둔 공간입니다.

제가 쓰면서 수시로 내용을 바꾸기 때문에 변동이 큽니다.

## 사용 환경

저는 아래 환경들을 이용해 개발하고 있습니다.

- arch : x86_64
- OS : Arch Linux, Windows 11 VM
- lang : C/C++, TS/JS on Bun, Java, Rust, Go
- AI Agent
  - Harness: Pi Agent
  - Model : GPT 6.1 Sol (high)

## Why?

이걸 왜 만든지는 저도 모르겠습니다. bigpowers, superpowers나 oh-my로 시작하는 것들을 써보다 보니 좋기는 좋은데 토큰 비용도 많이 나오고, 제가 쓰는 모델과 하네스에는 특화되지 않았다고 생각한 게 만든 이유입니다.

## List

> [!WARN]
> `AGENTS.md`와 `idonttest` skill은 서로 충돌합니다.

- **AGENTS.md**: GPT 6.1 Sol을 위한 특별 프롬프트
- skills/yoink: 남의 코드 스멜 쌔벼와서 문서화하기 (공부용)
- skills/idonttest: 테스트 서브에이전트로 남한테 구현 문서만 주고 블라인드 테스트 진행하고 문서랑 코드 보완하기 (Orca/Paseo 같이 쓰기)
- skills/ps2decomp: PS2 게임 디컴파일용 스킬 (미공개)
