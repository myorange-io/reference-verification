## 6단계 — 리포트 산출

**판단만 적고 나머지는 조립기가 만든다.** 리포트를 통째로 받아쓰면 60,000자를 쓰게 되는데,
그중 모델만 알 수 있는 것은 절반이 안 된다. 주장 원문과 서지는 0단계가 이미 가지고 있고,
판정과 오류 유형은 슬롯 표에서 규칙대로 나오며, 근거 인용문은 쪽·행만 알면 원문에서
뽑힌다. 받아쓰면 시간만 드는 것이 아니라 **틀릴 자리가 생긴다.**

```bash
python3 -m refver assemble judgment.json --citations citations.json \
    --corpus <출처폴더> --document <원문서> -o report.json
```

판단 파일의 계약은 바로 앞 절에 있다.

### 사람이 읽는 리포트

```python
from refver.report import load, validate, render
rep = load("report.json"); assert not validate(rep)
open("참고문헌_검증리포트.md","w",encoding="utf-8").write(render(rep))
```

마크다운을 손으로 쓰지 않는다. 두 벌을 따로 쓰면 반드시 어긋나고, 어긋난 순간
어느 쪽이 진실인지 알 수 없게 된다.

리포트 구성은 렌더러가 정한다: 검증 개요 · 종합 판정 · 1단계 표 · 2단계 표 ·
문제 인용 상세(슬롯 표·근거 인용·대체 출처) · 사람이 확인할 항목.

표기 규칙.
- 기관은 **현행 명칭 + 도메인 병기** — 예: `성평등가족부(mogef.go.kr)`
- 1·2단계는 **같은 번호**를 쓴다
- (FAIL + MISMATCH + NOT_SUPPORTED + PARTIAL) ÷ 전체 > 30%면 근거 전면 재작성을 권고한다

### 건네는 리포트 — HTML

마크다운은 저장소에 두고 읽기 좋고, HTML은 **문서를 쓴 사람에게 그대로 건네기** 좋다.
받는 쪽이 판독기를 따로 열 필요가 없고, 브라우저에서 그대로 인쇄하거나 PDF로
내보낼 수 있다. 마지막에 한 벌 더 낸다.

```bash
python3 -m refver render report.json -o 참고문헌_검증리포트.html
```

`-o`가 `.html`로 끝나면 HTML로 낸다. 못박고 싶으면 `--html`을 붙인다.
셸이 없는 환경에서는 파이썬으로 같은 것을 낸다.

```python
from refver.report import load, validate, render_html
rep = load("report.json"); assert not validate(rep)
open("참고문헌_검증리포트.html","w",encoding="utf-8").write(render_html(rep))
```

파일 하나에 스타일까지 들어 있어 딸린 것이 없다. 밖으로 아무것도 불러오지 않으므로
망이 막힌 자리에서도 그대로 열린다. 판정에는 색이 붙고(일치·부분적·뒷받침 안 됨),
'사람이 확인해야 할 항목'은 읽으며 실제로 체크할 수 있다.

**HTML은 마크다운을 옮긴 것이다.** 판정도 근거도 여기서 다시 만들지 않는다.
`report.json → 마크다운 → HTML` 한 줄기뿐이라 세 벌이 어긋날 자리가 없다.
세 파일을 함께 건넨다 — 기계가 읽는 정본, 저장소에 남길 것, 사람에게 보낼 것.
