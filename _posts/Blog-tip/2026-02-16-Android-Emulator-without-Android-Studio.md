---
title:  "Android Studio 없이 Android Emulator 실행하기"

categories:
  - Android

date: 2026-02-16
last_modified_at: 2026-02-16
---

출처: [Run Android Emulator without Android Studio (gist)](https://gist.github.com/giansalex/92cb633c50ab31f855d895dd95ee920c)

Android Studio를 설치하지 않고, Android SDK Command line tools만으로 에뮬레이터를 실행하는 방법을 정리한다.

## 1) Command line tools 다운로드

- Android Developers에서 **Command line tools only**를 다운로드한다.

## 2) SDK 루트 디렉터리 구성

아래와 같이 SDK 루트를 만들고, 다운로드한 압축 파일을 `cmdline-tools/latest` 아래에 풀어준다.

```text
$ANDROID_SDK_ROOT/
├── cmdline-tools/
│   └── latest/
│       └── bin/
│           ├── avdmanager
│           └── sdkmanager
```

## 3) 환경 변수 설정

- `ANDROID_SDK_ROOT`를 SDK 루트 경로로 설정
- `PATH`에 아래 경로 추가

```text
$ANDROID_SDK_ROOT/cmdline-tools/latest/bin
$ANDROID_SDK_ROOT/emulator
$ANDROID_SDK_ROOT/platform-tools
```

## 4) Java 설치/설정 (필수)

`sdkmanager`와 `avdmanager`는 Java가 필요하다.

Windows PowerShell 예시:

```powershell
winget install -e --id Microsoft.OpenJDK.17
```

설치 확인:

```powershell
java -version
where.exe java
```

## 5) SDK 패키지 설치

API 23(마시멜로) 기준 예시:

```bash
sdkmanager --sdk_root="$ANDROID_SDK_ROOT" "system-images;android-23;google_apis;x86" "platforms;android-23"
sdkmanager --sdk_root="$ANDROID_SDK_ROOT" "emulator" "platform-tools"
```

## 6) AVD(가상 디바이스) 생성

```bash
avdmanager --verbose create avd --force --name "pixel_6.0" --device "pixel" --package "system-images;android-23;google_apis;x86" --tag "google_apis" --abi "x86"
```

## 7) 에뮬레이터 실행

```bash
emulator @pixel_6.0
```

## 전체 구조 예시

```text
$ANDROID_SDK_ROOT/
├── cmdline-tools/
│   └── latest/
│       ├── bin/
│       │   ├── avdmanager
│       │   ├── sdkmanager
│       │   └── ...
│       └── source.properties
├── emulator/
├── platforms/
│   └── android-23/
├── platform-tools/
│   ├── adb
│   ├── fastboot
│   └── ...
└── system-images/
    └── android-23/
```

## 참고

- gist 작성 시점 기준(Last active: 2024-06-18)으로 API 23/x86 예시를 사용하고 있다.
- 최신 환경에서는 x86_64 이미지 또는 더 높은 API 레벨을 선택하면 호환성이 더 좋을 수 있다.
- `Could not determine SDK root` 오류가 나면 `--sdk_root` 옵션을 명시하거나 `cmdline-tools/latest` 디렉터리 구조를 다시 확인한다.
