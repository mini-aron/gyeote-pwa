# 곁에 (gyeote) Android TWA

[gyeote.site](https://gyeote.site)를 Google Play용 안드로이드 앱으로 감싸는 TWA(Trusted Web Activity) 프로젝트입니다. [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap)으로 생성했고, 설정의 원본은 `twa-manifest.json`입니다.

- packageId: `com.gyeote.app` (출시 후 변경 불가)

## 빌드

JDK 17과 Android SDK가 필요합니다.

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home
./gradlew assembleDebug   # 테스트용 APK: app/build/outputs/apk/debug/app-debug.apk
./gradlew bundleRelease   # 서명 없는 AAB: app/build/outputs/bundle/release/app-release.aab
```

## 웹 매니페스트를 바꿨을 때

```bash
npx @bubblewrap/cli update --skipVersionUpgrade
```

## 업로드 키

- 업로드 키스토어는 레포 밖 `../gyeote-upload.keystore`(alias `upload`)에 둡니다. 절대 커밋하지 마세요.
- 직접 만들어 쓰고 비밀번호도 레포에 남기지 않습니다.

## Digital Asset Links

Play Console의 앱 서명 키 SHA-256 지문을 웹 레포의 `public/.well-known/assetlinks.json`에 넣어야 주소창 없는 전체 화면으로 열립니다.

## 버전

출시(업로드)할 때마다 `twa-manifest.json`의 `appVersionCode`를 올리고 `update`로 다시 생성하세요.
