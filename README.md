# IELTS 암기장 — PWA 프로토타입

IELTS 라이팅 템플릿 암기를 위한 빈칸 채우기 웹앱.
아이폰 홈화면에 추가하면 네이티브 앱처럼 동작해.

## 기능

- **빈칸 채우기 암기**: 단어를 랜덤하게 가려서 타이핑으로 복원
- **난이도 자동 조절**: 점수에 따라 레벨 1(10% 빈칸) → 레벨 8(80% 빈칸)로 진행
- **망각곡선 스케줄링**: SM-2 알고리즘 기반 복습 간격 자동 계산
- **퍼지 매칭 채점**: 오타 허용 (Levenshtein distance 기반)
  - 🟢 정답 / 🟡 유사 (1~3자 오차) / 🔴 오답
- **로컬 저장**: 모든 데이터는 기기에 저장 (서버 없음)
- **오프라인 지원**: 서비스 워커로 인터넷 없이도 작동
- **에세이 4종 태그**: Opinion / Discussion / Problem-Solution / Adv-Disadv

## 배포 방법 (iPhone 홈화면 추가)

iOS PWA는 **HTTPS**가 필수야. 세 가지 방법:

### 방법 1: GitHub Pages (가장 추천, 무료, 5분)

1. [github.com](https://github.com)에서 새 레포지토리 생성 (예: `ielts-memo`)
   - Public으로 설정
2. 이 폴더의 모든 파일(`index.html`, `manifest.json`, `sw.js`, `icon-*.png`, `icon.svg`)을 레포에 업로드
3. 레포 **Settings → Pages**
   - Source: `Deploy from a branch`
   - Branch: `main` / `(root)`
   - Save
4. 1~2분 뒤 `https://{username}.github.io/ielts-memo/` 에서 접속 가능
5. 아이폰 Safari로 접속 → **공유 버튼 → "홈 화면에 추가"**

### 방법 2: Netlify Drop (드래그 앤 드롭)

1. [app.netlify.com/drop](https://app.netlify.com/drop) 접속
2. `ielts-memo` 폴더 전체를 드래그
3. 주어진 URL을 아이폰 Safari로 열고 홈화면 추가

### 방법 3: Vercel

1. [vercel.com](https://vercel.com)에서 Import (GitHub 연결 or 파일 업로드)
2. 배포 후 URL을 아이폰 Safari로 열고 홈화면 추가

## 사용 방법

1. **홈**: 등록된 에세이 목록 표시. 샘플 에세이 1개가 기본 포함됨.
2. **새 에세이 추가**: 우하단 `+` 버튼 → 제목, 유형, 본문 입력
3. **에세이 편집**: 홈 카드를 **길게 누르기** (0.6초)
4. **연습 시작**: 에세이 카드 **탭**
5. **빈칸 채우기**: 점선 밑줄을 탭해서 타이핑. Enter로 다음 칸 이동
6. **채점**: 하단 "채점하기" → 결과 화면에서 레벨 변화, 다음 복습일 확인

## 학습 로직

### 빈칸 생성
- 레벨 N이면 단어 중 약 N×10%를 랜덤하게 빈칸으로 전환
- 매번 다른 위치가 가려짐 (위치 암기 방지)
- 2글자 미만 단어(`a`, `is` 등)는 빈칸 대상에서 제외

### 채점 (단어 단위)
- 완전 일치 → 🟢 정답 (score 1.0)
- 오타 허용 (단어 길이에 따라 1~3자) → 🟡 유사 (score 0.6)
- 그 외 → 🔴 오답 (score 0.0)
- 대소문자 무시

### 레벨 조절
- 평균 85% 이상 → 레벨 +1
- 평균 60% 미만 → 레벨 −1
- 그 외 → 레벨 유지

### 복습 간격 (SM-2)
- 점수 기반 grade (0~5) 계산
- 실패(grade<3)시 interval 리셋
- 성공시 ease factor에 따라 간격 증가
- 첫 성공: 1일 / 두 번째: 3일 / 이후: interval × ease

## 기술 스택

- Vanilla HTML/CSS/JS (프레임워크 없음, 의존성 없음)
- LocalStorage로 영속화
- Service Worker로 오프라인 캐시
- iOS Safari PWA API 대응

## 향후 개선 아이디어

- [ ] 우선순위 태깅: `[[key phrase]]` 구문으로 특정 표현 우선 빈칸화
- [ ] 힌트 시스템 (첫 글자 보기)
- [ ] 커넥터 전용 모드 (however, furthermore 등만 가림)
- [ ] 통계 대시보드 (레벨별 추이, 정답률 그래프)
- [ ] 데이터 내보내기/가져오기 (JSON)
- [ ] 다크 모드
- [ ] 음성으로 답 입력 (Speech Recognition API)

## 데이터 초기화

Safari 주소창에서:
```
javascript:localStorage.clear();location.reload()
```
또는 iOS 설정 → Safari → 고급 → 웹 사이트 데이터에서 해당 도메인 삭제.
