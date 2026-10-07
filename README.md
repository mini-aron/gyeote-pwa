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

처음 한 번 키를 만듭니다. 비밀번호를 물어보니 직접 입력하고, 잃어버리지 않게 따로 보관하세요.

```bash
/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home/bin/keytool -genkeypair \
  -keystore ../gyeote-upload.keystore -alias upload \
  -keyalg RSA -keysize 2048 -validity 10000
```

`keystore.properties.example`을 `keystore.properties`로 복사하고 비밀번호를 채웁니다(이 파일은 gitignore 되어 있습니다). 이 파일이 있으면 `./gradlew bundleRelease`가 서명된 AAB를 만듭니다.

## Play 스토어 출시

1. `./gradlew bundleRelease`로 서명된 `app/build/outputs/bundle/release/app-release.aab`를 만듭니다.
2. Play Console에서 앱을 만들고 Play 앱 서명을 켠 채로 AAB를 업로드합니다(처음에는 내부 테스트 트랙 추천).
3. Play Console > 설정 > 앱 무결성에서 **앱 서명 키**의 SHA-256 지문을 복사해 아래 Digital Asset Links에 넣습니다.

## Digital Asset Links

Play Console의 앱 서명 키 SHA-256 지문을 웹 레포의 `public/.well-known/assetlinks.json`에 넣어야 주소창 없는 전체 화면으로 열립니다. 직접 빌드한 AAB/APK도 전체 화면으로 확인하려면 업로드 키 지문도 함께 넣습니다.

```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.gyeote.app",
    "sha256_cert_fingerprints": ["<앱 서명 키 SHA-256>", "<업로드 키 SHA-256>"]
  }
}]
```

업로드 키 지문은 `keytool -list -v -keystore ../gyeote-upload.keystore -alias upload`로 확인합니다. 배포 후 `https://gyeote.site/.well-known/assetlinks.json`이 열리는지 확인하세요.

## 버전

출시(업로드)할 때마다 `twa-manifest.json`의 `appVersionCode`를 올리고 `update`로 다시 생성하세요.
