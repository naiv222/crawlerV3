## 다나와 카테고리 크롤러 (crawlerV2)

Playwright(Python) 기반으로 **다나와 카테고리 페이지의 상품 정보**를 자동 수집해 CSV로 저장하는 크롤러입니다.  
최신 버전에서는 **페이지 이동(pagination) 처리 로직이 개선**되어, 스크롤 또는 `movePage()` 호출 기반 페이지에서도 안정적으로 동작합니다.

### 사전 준비

- Python **3.10 이상** 권장 (Windows 10/11 테스트 완료)
- `pip` 사용 가능 환경
- Playwright가 브라우저(Chromium 등)를 자동 설치하므로 별도 설치 불필요

### 설치 방법

```bash
# (선택) 가상 환경 생성
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# 필수 패키지 설치
pip install -r requirements.txt

# Playwright 브라우저 설치
python -m playwright install
```

### 실행 예시

```bash
python danawa_crawler.py   --category-url "<카테고리_URL>"   --output result.csv   --items-per-page 20   --max-total-items 100   --delay-ms 800   --headless
```

### 주요 옵션

`--category-url` 크롤링 대상 다나와 카테고리 URL (필수)
`--output` 결과 파일 이름 `danawa_output.csv`
`--items-per-page` 한 페이지당 최대 수집 상품 수
`--max-total-items` 전체 수집 상품 수 제한
`--delay-ms` 요청 사이 대기 시간(ms) (권장: 800ms)
`--headless` 브라우저 창 없이 실행


### 페이징 처리 (v3 개선 사항)

이전 버전에서는 일부 카테고리의 `onclick` 기반 페이지 이동이 감지되지 않는 문제가 있었습니다.  
새 버전에서는 다음 방식을 통해 **모든 형태의 페이지네이션에 대응**합니다:

1. `movePage(N)` 함수를 직접 호출해 페이지 이동  
2. 페이지 로드 완료 후 `networkidle` 상태까지 대기  
3. 비정상 로드 시 **재시도 로직** 적용 (타임아웃 방지)

이를 통해 **SPA(단일 페이지) 구조의 카테고리 페이지**에서도 안정적인 연속 크롤링이 가능합니다.

### 출력 형식

생성된 CSV에는 다음 정보가 포함됩니다:

- `상품명`
- `URL`
- `상세정보` (사양, 인증, 등록일 등)

### 💡 사용 팁

- **속도보다 안정성 우선:** `--delay-ms` 값을 600~1200 사이로 조정 
- **테스트 시:** `--headless` 옵션을 제거하면 동작 확인 가능
- **수집량 제한:** `--max-total-items`으로 개발 단계에서 데이터 크기 제어  

### 🧰 문제 해결

결과가 비거나 타임아웃 발생: `--delay-ms` 증가, `--items-per-page` 축소
브라우저 오류: `python -m playwright install` 재실행
PowerShell 정책 문제: 관리자 권한으로 `Set-ExecutionPolicy RemoteSigned`

### ⚠️ 주의사항

- 크롤링은 사이트의 이용약관 및 로봇(/robot.txt) 정책을 준수해야 합니다.  
- 무분별한 요청으로 인한 차단이나 법적 책임은 사용자에게 있습니다.
