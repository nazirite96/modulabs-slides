# modulabs-slides 🎨

> **모두의연구소(Modulabs) 브랜드 스타일의 16:9 HTML 슬라이드 덱**을 만들어주는 [Claude Code](https://claude.com/claude-code) 스킬입니다.
> 노트·주제·영상 자막·기존 PPTX를 → 의존성 없는 단일 HTML 프레젠테이션으로 변환합니다.

<p>
  <img src="examples/example-cover.png" width="49%" alt="표지 예시">
  <img src="examples/example-content.png" width="49%" alt="본문 예시">
</p>

<sub>↑ 이 스킬로 생성한 실제 결과물 (통계 강의자료를 변환한 예시)</sub>

---

## ✨ 무엇을 해주나

- **브랜드 덱 자동 생성** — 코랄 레드 표지/섹션/클로징 + 라이트 본문(레드 헤더 룰·로고·진행바), 다이어그램은 브랜드 팔레트로 자동 리컬러
- **단일 HTML · 의존성 0** — 빌드 없이 `index.html` 하나로 동작. 키보드/휠/스와이프 내비, **발표자 노트(`N`)**, **인라인 편집(`E`, `Ctrl/Cmd+S` 저장)** 내장
- **모든 화면비에서 안 깨짐** — 1920×1080 고정 스테이지를 화면에 맞춰 균일 축소 + 레터박스 (울트라와이드·세로·노트북 어디서나 중앙 정렬)
- **PPTX → HTML 변환** — 원본 슬라이드의 그래프·수식·다이어그램 이미지를 **모두 보존**한 채 브랜드 레이아웃에 재배치
- **템플릿 라이브러리** — 기본 `modulabs-red` 외에, **참고 PPTX/PDF/이미지/URL에서 새 템플릿을 추출**해 추가 가능
- **헤드리스 검증** — 슬라이드별 오버플로·정렬을 스크린샷으로 확인하는 절차 포함

---

## 📦 설치

이 스킬은 **개인(user) 스킬** 폴더에 두면 모든 프로젝트에서 쓸 수 있습니다.

```bash
git clone https://github.com/nazirite96/modulabs-slides.git \
  ~/.claude/skills/modulabs-slides
```

특정 프로젝트에서만 쓰려면 그 프로젝트의 `.claude/skills/` 안에 클론하세요:

```bash
git clone https://github.com/nazirite96/modulabs-slides.git \
  ./.claude/skills/modulabs-slides
```

설치 후 Claude Code를 다시 시작(또는 새 세션)하면 스킬이 인식됩니다.

### 요구 사항
| 도구 | 용도 | 필수 여부 |
|---|---|---|
| **Claude Code** | 스킬 실행 | 필수 |
| **Chrome / Chromium** | 결과물 헤드리스 스크린샷 검증 | 권장 |
| **LibreOffice (`soffice`)** | PPTX → PDF 참고 렌더링 (PPTX 변환 시) | 선택 |
| 인터넷 연결 | 덱이 Google Fonts(Archivo·Noto Sans KR·JetBrains Mono) 로드 | 표시용 |

---

## 🚀 사용법

Claude Code에서 자연어로 요청하거나, 슬래시 커맨드로 호출합니다.

```
/modulabs-slides 이 노트로 발표 덱 만들어줘
```

또는 그냥 이렇게 말해도 트리거됩니다:

- “모두의연구소 스타일로 슬라이드 만들어줘”
- “이 PPTX를 HTML 덱으로 바꿔줘 (에셋 다 살려서)”
- “이 영상 자막으로 브랜드 덱 만들어줘”
- “새 템플릿 추가해줘” (참고 파일 기반)

생성된 덱은 `index.html`(+ `assets/`) 형태로 나오며, 브라우저에서 바로 열거나 GitHub Pages·Vercel로 배포할 수 있습니다.

### 만들어진 덱 단축키
| 키 | 동작 |
|---|---|
| `→` `Space` · 휠 · 스와이프 | 다음 / 이전 슬라이드 |
| `Home` / `End` | 처음 / 마지막 |
| `N` | 발표자 노트 토글 |
| `E` | 인라인 편집 (`Ctrl/Cmd+S` 저장) |

---

## 🧩 템플릿 라이브러리

템플릿은 `templates/<id>/` 폴더 단위로 관리됩니다. 각 폴더는 독립적이며 다음을 포함합니다.

```
templates/
├── index.md                # 템플릿 레지스트리 (생성 시 목록·선택에 사용)
└── modulabs-red/           # 기본 템플릿 (모두의연구소 레드)
    ├── design.md           # 팔레트·폰트·컴포넌트 등 디자인 시스템
    ├── template.html       # 스타터 덱 (테마 + 컴포넌트 + 내비/노트/편집 JS)
    └── assets/             # 로고 등 브랜드 에셋
```

**새 템플릿 추가**는 스킬이 안내합니다 — 참고 **PPTX/PDF/이미지/URL**에서 팔레트·폰트·로고·레이아웃을 추출해 `templates/<id>/`로 스캐폴딩하고 레지스트리에 등록합니다. 기본 `modulabs-red`는 변경되지 않습니다.

---

## 📂 구조

```
modulabs-slides/
├── SKILL.md          # 스킬 정의 + 워크플로우 (덱 생성 / 템플릿 추가)
├── templates/        # 템플릿 라이브러리 (위 참고)
├── examples/         # README용 결과물 예시 이미지
└── README.md
```

---

## 📄 라이선스 / 크레딧

- 코드·템플릿: 자유롭게 사용·수정하세요.
- `modulabs-red` 템플릿의 **로고·브랜드 컬러는 모두의연구소(modulabs.co.kr) 자산**입니다. 모두의연구소 자료 제작 용도로 사용하세요. 다른 브랜드 덱을 만들 땐 새 템플릿을 추가해 쓰면 됩니다.
- 폰트는 Google Fonts(오픈 라이선스)에서 로드합니다.

---

<sub>Made with 🤖 <a href="https://claude.com/claude-code">Claude Code</a></sub>
