깃헙에서 새 리포지터리 생성하는 경우 세팅 해야 할 것들 (기존의 내 템플릿 프로젝트X)
* 리포지터리 세팅에서 정의 해야 할 것
  * secrets:
    * BASE64_KEYSTORE
    * RELEASE_KEY_ALIAS
    * RELEASE_KEY_PASSWORD
    * RELEASE_STORE_PASSWORD
    * SLACK_CHANNEL_ID : 슬랙으로 파일업로드
    * SLACK_TOKEN : 슬랙으로 파일업로드
    * SLACK_WEBHOOK_URL : 슬랙으로 결과 알려줌
  * vars:
    * APK_NAME
* 앱 build.gradle.kts에 추가 (0)
```
    val buildPropFile = rootProject.file("build.properties")
    val buildProperties = Properties().apply {
        if (buildPropFile.exists()) {
            load(FileInputStream(buildPropFile))
        }
    }
```
* 앱 build.gradle.kts에 추가 (1)
```
defaultConfig {
    versionCode = buildProperties.getProperty("versionCode").toInt()
    versionName = buildProperties.getProperty("versionName")
}
```
* 앱 build.gradle.kts에 추가 (2)
```
    signingConfigs {
        create("release") {
            val keystorePropFile = rootProject.file("keystore.properties")
            val keystoreProperties = Properties().apply {
                if (keystorePropFile.exists()) {
                    load(FileInputStream(keystorePropFile))
                }
            }
            val path = keystoreProperties.getProperty("releaseKeyStore")
            if (path != null) {
                keyAlias = keystoreProperties.getProperty("releaseKeyAlias")
                keyPassword = keystoreProperties.getProperty("releaseKeyPassword")
                storeFile = rootProject.file(keystoreProperties.getProperty("releaseKeyStore"))
                storePassword = keystoreProperties.getProperty("releaseStorePassword")
            }
        }
    }
```
* 앱 build.gradle.kts에 추가 (3)
```
    buildTypes {
        release {
            signingConfig = signingConfigs.getByName("release") // 여기만
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
```
* build.properties 파일 추가
```
versionCode=1
versionName=1.0.0
```
* README에 뱃지 추가
```
![Build Badge](https://github.com/SunChulBaek/Cokedex/actions/workflows/main.yml/badge.svg)
```
