Tuist를 도입하기 위해서는, 자동으로 모든 구성이 설정되는 새 프로젝트를 사용하지 않는 한, Tuist의 기본 원시 데이터를 사용하여 Xcode 프로젝트를 정의해야합니다 (예 : Project.swift 및 Workspace.swift 파일). 
이 과정이 얼마나 귀찮은지는 프로젝트가 얼마나 복잡한지에 따라 다릅니다.

아마도 알고 계시겠지만, Xcode 프로젝트는 시간이 지남에 따라 복잡해지고 혼란스러워질 수 있습니다. 
예를 들면, 디렉토리 구조와 일치하지 않는 그룹, 대상 간에 공유되는 파일 또는 존재하지 않는 파일을 가리키는 파일 참조 등이 있습니다.
모든 이러한 복잡성은 프로젝트를 신뢰성 있게 이식하는 명령을 제공하기가 어렵게 만듭니다.

또한 수동 마이그레이션은 **프로젝트를 정리하고 단순화하는 우수한 연습 방법**입니다. 
프로젝트에서 개발자뿐만 아니라 Xcode도 이러한 정리 작업을 수행함으로써 빠르게 처리 및 인덱싱할 수 있습니다. 
Tuist를 완전히 도입하면 프로젝트가 일관되게 정의되고 간단하게 유지되도록 보장합니다.

이 작업을 쉽게하기 위해, 사용자의 피드백을 기반으로 몇 가지 지침을 제공합니다.

## 지침

### ```.buildsettings```는 ```.xcconfig``` 파일로 추출하세요.

프로젝트가 가볍고 간단할수록 이식하기 쉬워집니다. 
이를 위해 빌드 설정과 같은 정보를 Xcode 프로젝트 외부에서 정의할 수 있는 ```.xcconfig``` 파일로 추출할 수 있습니다.
개발자들이 빌드 설정을 사용하여 설정을 정의하지 못하도록해야합니다.

이를 위해 다음 스니펫을 사용할 수 있습니다:
```
# Extract target build settings
tuist migration settings-to-xcconfig -p Project.xcodeproj -t MyApp -x MyApp.xcconfig

# Extract project build settings
tuist migration settings-to-xcconfig -p Project.xcodeproj -x MyAppProject.xcconfig
```

```.xcconfig``` 파일로 빌드 설정을 추출한 후, 연속적인 통합에서 프로젝트에 빌드 설정이 설정되어 있지 않은지 확인하는 것이 좋습니다:
```
tuist migration check-empty-settings -p Project.xcodeproj -t MyApp
```

### 가장 독립적인 대상을 먼저 이전하세요.

일반적으로 다른 대상보다 의존성이 적기 때문에, 가장 단순한 것들입니다.
이러한 대상으로 마이그레이션을 시작하는 것이 좋습니다.
이 명령어를 사용하여 프로젝트의 대상을 나열하고, 의존성 수에 따라 정렬할 수 있습니다. 
대상 수가 가장 적은 대상부터 시작하는 것이 좋습니다.
```
tuist migration list-targets -p Project.xcodeproj
```

### 잘못된 참조를 제거하세요

대상 소스와 리소스 빌드 단계를 확인하고, 누락된 파일에 대한 참조를 삭제하세요. 
Xcode의 빌드 시스템은 이를 무시하지만, 마이그레이션을 복잡하게 만들 수 있습니다.

### 어떤 대상에서도 사용되지 않는 파일을 제거하세요

이전에 프로젝트의 일부였지만, 더 이상 필요하지 않은 파일이 있을 수 있습니다.
이를 찾아서 제거하세요. 
그렇지 않으면, 전체 대상의 소스로서 매칭될 수 있는 전역 패턴 (예: **/*.swift**) 때문에 컴파일 문제가 발생할 수 있습니다.

### 마이그레이션 이후에는 Xcode 프로젝트 수정을 방지하세요

각 프로젝트의 마이그레이션 이후에는, Xcode 프로젝트를 직접 수정하면 CI가 실패하도록 하는 스크립트를 추가하고, 개발자들이 manifest 파일을 수정해야 한다는 것을 알리세요.
이를 위해 xcdiff와 같은 도구를 사용할 수 있습니다.

### 프로젝트 설명 도우미를 사용하세요

이러한 도우미를 사용하면 프로젝트를 정의하는 사용자 정의 추상화를 만들 수 있으며, 무엇보다도 모든 매니페스트 파일에서 내용을 재사용할 수 있습니다. 
가장 일반적인 사용 사례 중 하나는 템플릿화된 프로젝트의 팩토리로 작용하는 함수를 정의하는 것입니다. 
모든 프로젝트를 마이그레이션한 후에는, ```Project.swift``` 파일을 검토하여 공통 패턴을 식별하고 도우미로 추출하세요.

## 도구

다음은 마이그레이션 프로세스를 돕기 위해 커뮤니티에서 개발한 도구 목록입니다:

xcdiff: xcodeproj 파일을 비교할 수 있도록 도와주는 도구입니다.

Xcode Build Settings: 사용 가능한 모든 빌드 설정과 그 의미를 나열한 참조서입니다.