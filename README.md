깃헙에서 새 리포지터리 생성하는 경우 세팅 해야 할 것들 (기존의 내 템플릿 프로젝트X)
* 리포지터리 세팅에서 정의 해야 할 것
  * secrets:
    * APP_ID
    * BASE64_KEYSTORE
    * RELEASE_KEY_ALIAS
    * RELEASE_KEY_PASSWORD
    * RELEASE_STORE_PASSWORD
    * SLACK_CHANNEL_ID
    * SLACK_TOKEN
    * SLACK_WEBHOOK_URL
  * vars:
    * APK_NAME
* 글로벌 build.gradle.kts에 추가
```
plugins {
    id("com.github.ben-manes.versions") version "0.51.0"
}
```
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
compileSdk = buildProperties.getProperty("compileSdk").toInt()
defaultConfig {
    applicationId = buildProperties.getProperty("applicationId")
    minSdk = buildProperties.getProperty("minSdk").toInt()
    targetSdk = buildProperties.getProperty("targetSdk").toInt()
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