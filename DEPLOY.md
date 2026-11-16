# 배포 가이드

백엔드(`beec`)는 Render, 프론트엔드는 Vercel에 배포합니다.

| 구분 | 플랫폼 | 주소 |
| --- | --- | --- |
| 프론트엔드 | Vercel | https://eco-check-ni.vercel.app |
| 백엔드 | Render (Docker) | https://ecocheck-api.onrender.com |

<br>

## 1. 백엔드 `beec` → Render

1. render.com → **New** → **Web Service** → 저장소를 연결합니다.
2. 설정
   - **Root Directory**: `beec`
   - **Runtime**: Docker (`beec/Dockerfile` 사용)
   - **Instance Type**: Free 또는 Starter
3. 배포가 끝나면 서비스 주소를 복사해 둡니다.
4. `주소/api/...` 중 아무 API 하나를 브라우저로 열어 응답이 오는지 확인합니다. 첫 기동에는 10~60초가 걸립니다.

> 포트는 Render가 `PORT` 환경변수로 넣어 주고, `application.properties`가 이를 읽습니다. 무료 인스턴스(메모리 512MB)에서도 동작하도록 Dockerfile에서 힙을 380MB로 제한합니다.

<br>

## 2. 프론트엔드 → Vercel

1. vercel.com → **Add New Project** → 저장소를 선택합니다.
2. **Environment Variables**를 등록합니다.

   | 이름 | 값 |
   | --- | --- |
   | `NEXT_PUBLIC_API_BASE_URL` | Render 백엔드 주소 (끝에 `/` 없이) |
   | `BEEC_API_BASE_URL` | 같은 주소 (서버 쪽 Route Handler용) |
   | `NEXT_PUBLIC_KAKAO_MAP_KEY` | 카카오 JavaScript 키 |
   | `KAKAO_REST_KEY` | 카카오 REST API 키 |
   | `GEMINI_API_KEY` | Gemini API 키 |
   | `GEMINI_MODEL` | 선택. 예: `gemini-2.5-flash` |
   | `BLDG_HUB_API_KEY` | 건축HUB API 키 |

   `NEXT_PUBLIC_API_BASE_URL` 대신 `NEXT_PUBLIC_BEEC_API_BASE_URL`을 써도 인식합니다.

3. **Deploy**합니다. `NEXT_PUBLIC_` 값은 빌드할 때 번들에 들어가므로, 값을 바꾸면 **Redeploy**해야 합니다.

<br>

## 3. 카카오 개발자 콘솔

**내 애플리케이션 → 플랫폼 → Web → 사이트 도메인**에 배포 주소(예: `https://eco-check-ni.vercel.app`)를 추가합니다.
등록하지 않으면 배포 주소에서 주소 검색(우편번호)과 지도가 동작하지 않습니다.

<br>

## 4. 운영 참고

- **콜드 스타트**: Render 무료 인스턴스는 15분 동안 요청이 없으면 잠듭니다. 깨어나는 데 1분 넘게 걸릴 수 있으니, 시연 전에는 사이트를 미리 한 번 열어 둡니다.
- **AI 응답 캐시**: AI 답변은 메모리에 캐시되며 서버가 재시작되면 사라집니다.
- **Gemini 할당량**: 무료 할당량이 소진되면 규칙 기반 답으로 대체됩니다. 안정적으로 운영하려면 결제를 켜거나 예비 키를 준비합니다.
