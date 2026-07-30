# 퇴근셔틀 APK 배포 — 최초 1회 설정 가이드

이 저장소를 GitHub에 새로 만들고 아래 순서대로 한 번만 설정하면, 이후에는 코드를 수정해 `main` 브랜치에 push할 때마다 GitHub Actions가 자동으로 APK를 빌드해 Release에 올려줍니다. GitHub Pages 다운로드 페이지는 항상 최신 버전을 자동으로 가리킵니다.

## 1. 새 GitHub 저장소 생성 및 코드 업로드
1. GitHub에서 새 저장소 생성 (Public 권장 — Pages 무료 사용 조건)
2. 이 폴더 전체(단, `keystore/` 폴더 제외 — 아래 3번 참고)를 저장소에 push

## 2. GitHub Pages 활성화
1. 저장소 **Settings → Pages**
2. Source: `Deploy from a branch`
3. Branch: `main`, 폴더: `/docs` 선택 → Save
4. 잠시 후 `https://<본인계정>.github.io/<저장소명>/` 에서 다운로드 페이지 확인 가능

## 3. 서명 키(keystore) 등록 — Secrets
별도로 전달받은 `keystore/shuttle-release.keystore` 파일과 `keystore/keystore-passwords.txt`를 준비합니다.
**이 두 파일은 저장소에 절대 올리지 마세요** (`.gitignore`에 이미 제외 처리되어 있습니다). 분실 시 이후 앱 업데이트가 불가능하니 별도로 안전하게 백업해 두시기 바랍니다.

1. keystore 파일을 base64로 변환합니다.
   - Mac/Linux: `base64 -i shuttle-release.keystore | pbcopy` (클립보드로 복사)
   - Windows(PowerShell): `[Convert]::ToBase64String([IO.File]::ReadAllBytes("shuttle-release.keystore")) | Set-Clipboard`
2. 저장소 **Settings → Secrets and variables → Actions → New repository secret** 에서 아래 4개를 등록합니다.

| Secret 이름 | 값 |
|---|---|
| `SHUTTLE_KEYSTORE_BASE64` | 위에서 변환한 base64 문자열 전체 |
| `SHUTTLE_KEYSTORE_PASSWORD` | keystore-passwords.txt의 STOREPASS 값 |
| `SHUTTLE_KEY_ALIAS` | keystore-passwords.txt의 ALIAS 값 (shuttleboard) |
| `SHUTTLE_KEY_PASSWORD` | keystore-passwords.txt의 KEYPASS 값 |

## 4. 첫 빌드 실행
1. Secrets 등록 후 저장소 **Actions** 탭 → `Build and release APK` 워크플로 선택 → **Run workflow** 로 수동 실행
   (또는 아무 파일이나 수정해 `main`에 push하면 자동 실행됩니다)
2. 빌드가 끝나면(2~5분) 저장소 **Releases** 에 `shuttle-board.apk`가 자동 업로드됩니다.
3. GitHub Pages 다운로드 페이지(`/docs`)에서 바로 다운로드 가능합니다.

## 5. 이후 업데이트 방법
코드 수정 → `main` 브랜치에 push → GitHub Actions가 자동으로 새 APK 빌드·배포 → 이용자는 동일한 다운로드 페이지에서 새 버전을 받아 기존 앱 위에 재설치하면 됩니다 (같은 keystore로 서명되므로 데이터/설정 유지).

## 6. 이용자 안내 (Konx 등으로 공지)
다운로드 페이지 링크만 공유하면 됩니다: `https://<본인계정>.github.io/<저장소명>/`
