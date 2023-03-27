# 시작하기

Tuist는 Xcode 프로젝트의 생성, 유지 보수 및 상호 작용을 용이하게하기 위한 명령 줄 도구 (CLI)입니다. 
이것은 이진 파일로 배포되며 Ruby, Java와 같은 다른 프로그래밍 언어로 작성된 도구와 달리 종속성 관리를 위해 다른 도구에 의존하지 않고 쉽게 설치하고 사용할 수 있습니다.

## 설치

시작하기 위해 도구를 설치해야합니다. 이를 위해 터미널에서 다음 명령을 실행할 수 있습니다.
```
curl -Ls https://install.tuist.io | bash
```
툴을 설치하는 것이 아니라 실제로는 tuistenv(후에 tuist로 이름이 변경됩니다.)을 설치하기 때문에 프로세스는 상대적으로 빠릅니다.

iOS 프로젝트에서 매우 일반적인 문제는 재현 가능한 환경이 없는 것입니다. 
프로젝트가 다른 도구에 설치되어야하는 것에 따라 종종 프로젝트가 기대하는 버전의 깨끗한 Ruby 환경 및 설치해야하는 기타 도구에 의존합니다. 
이러한 것 중 어느 하나가 누락되거나 좋은 상태가 아닌 경우 예기치 않은 출력과 개발자에게 정말 나쁜 경험을 제공합니다. 
이를 피하기 위해 Tuist는 스스로 포함되며, 올바른 버전이 사용되도록 tuistenv를 제공합니다. 
환경에서 다른 버전을 관리하고 프로젝트가 고정 된 버전을 사용하도록 실행합니다. 
이를 통해 팀의 모든 사람이 Tuist의 동일한 버전을 사용할 수 있도록 보장합니다. 
고급 섹션에서는 tuistenv의 기능을 자세히 살펴보겠지만 지금은 Tuist를 직접 실행한다고 가정하고 단순한 것을 유지합니다.

### 첫 번째 프로젝트 만들기

이제 Tuist가 설치되었으므로 첫 번째 프로젝트를 만들 수 있습니다. 앱을위한 디렉토리를 만듭니다.
```
mkdir MyApp
cd MyApp
```
그런 다음 다음을 실행하십시오.
```
tuist init --platform ios
```
init 명령은 iOS 응용 프로그램을 자동으로 설정하며, Info.plist 파일, AppDelegate.swift, 테스트 파일 및 프로젝트 정의를 포함합니다.

***만약 Swift Package Manager를 사용해본 적이 있다면, Project.swift 파일은 Package.swift와 동등한 역할을 합니다.***

***SWIFTUI TEMPLATE
tuist init --platform ios --template swiftui 명령어를 사용하면 SwiftUI iOS 프로젝트를 쉽게 생성할 수 있습니다.***

```swift
import ProjectDescription

let project = Project(
    name: "MyApp",
    organizationName: "MyOrg",
    targets: [
        Target(
            name: "MyApp",
            platform: .iOS,
            product: .app,
            bundleId: "io.tuist.MyApp",
            infoPlist: "Info.plist",
            sources: ["Sources/**"],
            resources: ["Resources/**"],
            headers: .headers(
                public: ["Sources/public/A/**", "Sources/public/B/**"],
                private: "Sources/private/**",
                project: ["Sources/project/A/**", "Sources/project/B/**"]
            ),
            dependencies: [
                /* Target dependencies can be defined here */
                /* .framework(path: "framework") */
            ]
        ),
        Target(
            name: "MyAppTests",
            platform: .iOS,
            product: .unitTests,
            bundleId: "io.tuist.MyAppTests",
            infoPlist: "Info.plist",
            sources: ["Tests/**"],
            dependencies: [
                .target(name: "MyApp")
            ]
        )
    ]
)
```
우리가 Xcode 프로젝트를 정의하는 것이기 때문에 대부분의 속성은 익숙할 수 있습니다.
매니페스트에서 사용되지 않은 속성들도 사용 가능한 것이 있습니다. 
모든 공개 모델을 볼 수 있는 ProjectDescription 프레임워크의 프로젝트 참조를 확인할 수 있습니다.

### 프로젝트 편집하기

프로젝트를 편집하려면 다음 명령을 사용하여 관련 Tuist 매니페스트를 열 수 있습니다.
```
tuist edit
```
생성된 프로젝트에는 ```Project.swift``` 파일과 필요한 다른 매니페스트 파일이 포함됩니다.

### 프로젝트 생성하기

우리는 매니페스트와 프로젝트 파일이 있지만, 무언가 부족한 게 있습니다.
Xcode 프로젝트가 없으면 Xcode를 사용할 수 없습니다.
왜냐하면 Xcode가 예상하는 형식이기 때문입니다. 
우리는 다행히도 Tuist에는 매니페스트 파일에서 프로젝트와 워크스페이스를 생성하는 명령이 있습니다.
터미널에서 다음 명령을 실행하면:
```
tuist generate
```
```MyApp.xcodeproj``` 및 ```MyApp.xcworkspace``` 파일을 받게 됩니다.
의존성 섹션에서 볼 수 있듯이, 워크스페이스는 MyApp 프로젝트가 의존하는 다른 프로젝트를 추가하는 데 필요합니다. 
```MyApp.xcworkspace을``` 열고 MyApp 스키마를 실행하려고 하면 앱을 빌드하고 시뮬레이터에서 성공적으로 실행됩니다. 📱 🎉.

### 프로젝트의 README에 배지 추가하기

마지막으로, Tuist를 사용하여 프로젝트를 정의했음을 나타내는 배지를 프로젝트의 README에 포함하고 싶을 수 있습니다.
제목 아래에 다음 마크다운 코드를 복사하여 붙여넣으면 됩니다:

```
[![Tuist badge](https://img.shields.io/badge/Powered%20by-Tuist-blue)](https://tuist.io)
```
