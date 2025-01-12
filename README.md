# GASDocumentation
언리얼 엔진 5의 GameplayAbilitySystem 플러그인(GAS)에 대한 제 이해를 간단한 멀티플레이어 샘플 프로젝트와 함께 설명합니다. 이것은 공식 문서가 아니며 이 프로젝트나 저는 Epic Games와 관련이 없습니다. 이 정보의 정확성에 대해 어떠한 보증도 하지 않습니다.

이 문서의 목적은 GAS의 주요 개념과 클래스들을 설명하고 제가 경험한 것을 바탕으로 추가적인 설명을 제공하는 것입니다. 커뮤니티 사용자들 사이에는 GAS에 대한 많은 '부족 지식'이 있으며, 저는 제가 아는 모든 것을 여기에서 공유하고자 합니다.

샘플 프로젝트와 문서는 **언리얼 엔진 5.3**(UE5)과 함께 최신 상태를 유지하고 있습니다. 이전 버전의 언리얼 엔진을 위한 문서 브랜치들이 있지만, 더 이상 지원되지 않으며 버그나 오래된 정보가 있을 수 있습니다. 엔진 버전과 일치하는 브랜치를 사용해주세요.

[GASShooter](https://github.com/tranek/GASShooter)는 멀티플레이어 FPS/TPS를 위한 GAS의 고급 기술을 보여주는 자매 샘플 프로젝트입니다.

가장 좋은 문서는 항상 플러그인 소스 코드입니다.

<a name="table-of-contents"></a>
## Table of Contents

> 1. [GameplayAbilitySystem 플러그인 소개](#intro)
> 1. [샘플 프로젝트](#sp)
> 1. [GAS를 사용하는 프로젝트 설정](#setup)
> 1. [GAS 개념들](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [리플리케이션 모드](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [설정 및 초기화](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Gameplay Tags 변경에 대한 응답](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [플러그인 .ini 파일에서 Gameplay Tags 불러오기](#concepts-gt-loadfromplugin)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute 정의](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Attribute 변경에 대한 응답](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set 정의](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set 설계](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [개별 Attribute가 있는 서브 컴포넌트](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [런타임에서 AttributeSet 추가 및 제거](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [아이템 Attribute (무기 탄약)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [아이템의 일반 Float 값](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [아이템의 `AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [아이템의 `ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Attribute 정의하기](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Attribute 초기화하기](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect 정의](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Gameplay Effect 적용하기](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Gameplay Effect 제거하기](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifier](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [곱하기와 나누기 Modifier](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Modifier의 Gameplay Tags](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Gameplay Effect 스택킹](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Execution Calculation에 데이터 전송](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [백업 데이터 Attribute 계산 Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [백업 데이터 임시 변수 계산 Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Cooldown Gameplay Effect의 남은 시간 가져오기](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Cooldown 시작과 종료 감지](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Cooldown 예측](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Active Gameplay Effect 지속시간 변경](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [런타임에서 Dynamic Gameplay Effect 생성](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability 정의](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [리플리케이션 정책](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [서버의 원격 Ability 취소 존중 (Server Respects Remote Ability Cancellation)](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [입력 직접 복제 (Replicate Input Directly)](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [ASC에 입력 바인딩](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Ability를 활성화하지 않고 입력에 바인딩](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [Activation Failed Tags](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [인스턴스 정책](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution 정책](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [어빌리티에 데이터 전달](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability 비용과 쿨다운](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Ability 레벨업](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability 배칭](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security 정책](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task 정의](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [커스텀 Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Ability Tasks 사용](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue 정의](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Gameplay Cue 트리거](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [로컬 Gameplay Cue](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue 매개변수](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue 매니저](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Gameplay Cue 실행 방지](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue 배칭](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [수동 RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [하나의 GE에 여러 GC](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue 이벤트](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue 신뢰성](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [어빌리티에서 새 Prediction Window 생성](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [액터의 예측적 스폰](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GAS에서 Prediction의 미래](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [네트워크 예측 플러그인 (NPP, Network Prediction Plugin)](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [일반적으로 구현되는 Abilitiy와 Effect](#cae)  
>    5.1 [기절](#cae-stun)  
>    5.2 [달리기](#cae-sprint)  
>    5.3 [조준(ADS)](#cae-ads)  
>    5.4 [생명력 흡수](#cae-ls)  
>    5.5 [클라이언트와 서버에서 난수 생성](#cae-random)  
>    5.6 [크리티컬](#cae-crit)  
>    5.7 [스택되지 않는 Gameplay Effects 중 가장 큰 수치만 대상에 영향을 미치는 경우](#cae-nonstackingge)  
>    5.8 [게임이 일시정지된 상태에서 Target Data 생성](#cae-paused)  
>    5.9 [원버튼 상호작용 시스템](#cae-onebuttoninteractionsystem)  
> 1. [GAS 디버깅](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS 로깅](#debugging-log)  
> 1. [최적화](#optimizations)  
>    7.1 [Ability 배칭](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue 배칭](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC 지연 로딩](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [Enum names are now represented by path name](#troubleshooting-enumnamesarenowpathnames)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [5.3](#changelog-5.3)  
>    * [5.2](#changelog-5.2)  
>    * [5.1](#changelog-5.1)  
>    * [5.0](#changelog-5.0)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. GameplayAbilitySystem 플러그인 소개
[공식 문서](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)에서 발췌
>게임플레이 어빌리티 시스템은 RPG나 MOBA 게임에서 볼 수 있는 어빌리티와 속성을 구축하기 위한 높은 유연성을 가진 프레임워크입니다. 게임 캐릭터가 사용할 수 있는 액션이나 패시브 어빌리티를 만들고, 이러한 액션의 결과로 다양한 속성을 증가시키거나 감소시키는 상태 효과를 구현하고, 액션 사용을 제어하기 위한 "쿨다운" 타이머나 리소스 비용을 설정하고, 어빌리티의 레벨과 각 레벨에서의 효과를 변경하고, 파티클이나 사운드 효과를 활성화하는 등의 기능을 구현할 수 있습니다. 간단히 말해서, 이 시스템은 점프와 같은 간단한 기능부터 현대 RPG나 MOBA 게임의 캐릭터가 가진 복잡한 어빌리티 세트까지 게임 내 어빌리티를 설계, 구현, 효율적으로 네트워킹하는 데 도움을 줄 수 있습니다.

GameplayAbilitySystem 플러그인은 에픽 게임즈가 개발했으며 언리얼 엔진에 포함되어 있습니다. 이는 파라곤과 포트나이트를 포함한 여러 AAA 상용 게임에서 검증되었습니다.

이 플러그인은 싱글 및 멀티플레이어 게임에서 다음과 같은 기능을 즉시 사용할 수 있는 솔루션을 제공합니다.
* 선택적 비용과 쿨다운이 있는 레벨 기반 캐릭터 어빌리티 또는 스킬 구현 ([GameplayAbilities](#concepts-ga))
* 액터에 속한 수치형 `어트리뷰트(Attribute)` 조작 ([Attributes](#concepts-a))
* 액터에 상태 효과 적용 ([GameplayEffects](#concepts-ge))
* 액터에 `GameplayTags` 적용([GameplayTags](#concepts-gt))
* 시각 또는 사운드 효과 생성 ([GameplayCues](#concepts-gc))
* 위에서 언급된 모든 것들의 리플리케이션

멀티플레이어 게임에서 GAS는 다음 항목들의 [클라이언트측 예측](#concepts-p)을 지원합니다.
* 어빌리티 활성화
* 애니메이션 몽타주 재생
* `Attributes` 변경
* `GameplayTags` 적용
* `GameplayCues` 생성
* `CharacterMovementComponent`와 연결된 `RootMotionSource` 함수를 통한 이동

**GAS는 반드시 C++에서 설정해야 합니다**, 하지만 `GameplayAbilities`와 `GameplayEffects`는 디자이너가 블루프린트에서 생성할 수 있습니다.

GAS의 현재 이슈들:
- `GameplayEffect` 지연 시간 조정(어빌리티 쿨다운을 예측할 수 없어서 지연 시간이 높은 플레이어가 낮은 플레이어에 비해 낮은 쿨다운 어빌리티의 발사율이 낮아짐)
- `GameplayEffects` 제거를 예측할 수 없음. 하지만 역효과를 가진 `GameplayEffects`를 예측적으로 추가하여 효과적으로 제거할 수는 있습니다. 이것이 항상 적절하거나 실현 가능한 것은 아니며 여전히 문제로 남아있습니다.
- 보일러플레이트 템플릿, 멀티플레이어 예제, 문서의 부족. 이 문서가 그 부분을 도와줄 수 있기를 바랍니다!

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="sp"></a>
## 2. 샘플 프로젝트
이 문서와 함께 제공되는 멀티플레이어 3인칭 슈터 샘플 프로젝트는 GameplayAbilitySystem 플러그인을 처음 접하지만 언리얼 엔진에는 익숙한 사람들을 대상으로 합니다. 사용자는 C++, 블루프린트, UMG, 리플리케이션 및 UE의 다른 중급 주제들을 알고 있어야 합니다. 이 프로젝트는 플레이어/AI가 제어하는 영웅을 위해 `PlayerState` 클래스에 `AbilitySystemComponent`(`ASC`)를 두고, AI가 제어하는 미니언을 위해 `Character` 클래스에 `ASC`를 둔 기본적인 3인칭 슈터 멀티플레이어 준비 프로젝트를 설정하는 방법의 예를 제공합니다.

이 프로젝트의 목표는 GAS 기본사항을 보여주고 잘 주석이 달린 코드로 일반적으로 요청되는 몇 가지 능력을 시연하면서도 단순함을 유지하는 것입니다. 초보자에 초점을 맞추고 있기 때문에, 이 프로젝트는 [발사체 예측](#concepts-p-spawn)과 같은 고급 주제는 다루지 않습니다.

시연되는 개념들:
* `PlayerState` vs `Character`에서의 `ASC`
* 리플리케이션된 `Attributes`
* 리플리케이션된 애니메이션 몽타주
* `GameplayTags`
* `GameplayAbilities` 내부와 외부에서 `GameplayEffects` 적용 및 제거
* 방어구에 의해 감소된 데미지를 캐릭터의 체력에 적용
* `GameplayEffectExecutionCalculations`
* 스턴 효과
* 죽음과 부활
* 서버에서 어빌리티로부터 액터(발사체) 생성
* 조준 사격과 달리기로 로컬 플레이어의 속도를 예측적으로 변경
* 달리기를 위해 지속적으로 스태미나 소모
* 어빌리티를 사용하기 위한 마나 사용
* 패시브 어빌리티
* 스택형 `GameplayEffects`
* 액터 타게팅
* 블루프린트에서 생성된 `GameplayAbilities`
* C++에서 생성된 `GameplayAbilities`
* 액터별로 인스턴스화된 `GameplayAbilities`
* 인스턴스화되지 않은 `GameplayAbilities` (점프)
* 정적 `GameplayCues` (총 발사 발사체 충격 파티클 효과)
* 액터 `GameplayCues` (달리기와 스턴 파티클 효과)

영웅 클래스는 다음과 같은 능력들을 가집니다.

| 어빌리티                    | 입력 바인딩         | 예측  | C++ / 블루프린트 | 설명                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 점프                       | 스페이스바           | 예        | C++             | 영웅이 점프합니다.                                                                                                                                                         |
| 발사                        | 좌클릭   | 아니오         | C++             | 영웅의 총에서 발사체를 발사합니다. 애니메이션은 예측되지만 발사체는 아닙니다.                                                                                |
| 조준 사격 (ADS)            | 우클릭  | 예        | 블루프린트       | 버튼을 누르고 있는 동안, 영웅은 더 천천히 걷고 카메라가 확대되어 총으로 더 정확한 사격이 가능합니다.                                                    |
| 달리기                     | 왼쪽 쉬프트키          | 예        | 블루프린트       | 버튼을 누르고 있는 동안, 영웅은 스태미나를 소모하며 더 빨리 달립니다.                                                                                                         |
| 전방 대시              | Q                   | 예        | 블루프린트       | 영웅이 스태미나를 소모하며 전방으로 대시합니다.                                                                                                                              |
| 패시브 방어구 스택       | 패시브             | 아니오         | 블루프린트       | 4초마다 영웅이 최대 4개까지 방어구 스택을 얻습니다. 데미지를 받으면 방어구 스택 하나가 제거됩니다.                                                    |
| 메테오                     | R                   | 아니오         | 블루프린트       | 플레이어가 적들에게 데미지를 주고 스턴시키는 메테오를 떨어뜨릴 위치를 타게팅합니다. 타게팅은 예측되지만 메테오 생성은 예측되지 않습니다.                     |

`GameplayAbilities`를 C++나 블루프린트 중 어디서 만드는지는 중요하지 않습니다. 여기서는 각 언어에서 어떻게 만드는지 예시를 보여주기 위해 둘 다 사용되었습니다.

미니언들은 미리 정의된 `GameplayAbilities`를 가지고 있지 않습니다. 빨간 미니언은 더 많은 체력 재생을 가지고 있고 파란 미니언은 더 높은 시작 체력을 가지고 있습니다.

`GameplayAbility` 이름 지정에서, 블루프린트에서 생성된 `GameplayAbility`의 로직을 나타내기 위해 접미사 `_BP`를 사용했습니다. 접미사가 없다면 로직이 C++에서 생성되었다는 의미입니다.

**블루프린트 에셋 이름 접두사**

| 접두사      | 에셋 타입          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="setup"></a>
## 3. GAS를 사용하는 프로젝트 설정
GAS를 사용하는 프로젝트를 설정하는 기본 단계:
1. 에디터에서 GameplayAbilitySystem 플러그인 활성화
1. `YourProjectName.Build.cs`를 수정하여 `PrivateDependencyModuleNames`에 `"GameplayAbilities", "GameplayTags", "GameplayTasks"` 추가
1. Visual Studio 프로젝트 파일 새로고침/재생성
1. 4.24부터 5.2까지는 [`TargetData`](#concepts-targeting-data)를 사용하기 위해 `UAbilitySystemGlobals::Get().InitGlobalData()`를 호출하는 것이 필수입니다. 샘플 프로젝트는 이를 `UAssetManager::StartInitialLoading()`에서 수행합니다. 5.3부터는 자동으로 호출됩니다. 자세한 정보는 [`InitGlobalData()`](#concepts-asg-initglobaldata)를 참조하세요.

GAS를 활성화하기 위해 해야 할 일은 이것이 전부입니다. 여기서부터 `Character`나 `PlayerState`에 [`ASC`](#concepts-asc)와 [`AttributeSet`](#concepts-as)를 추가하고 [`GameplayAbilities`](#concepts-ga)와 [`GameplayEffects`](#concepts-ge)를 만들기 시작하세요!

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS 개념들

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
`AbilitySystemComponent`(`ASC`)는 GAS의 핵심입니다. 이는 시스템과의 모든 상호작용을 처리하는 `UActorComponent`([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html))입니다. [`GameplayAbilities`](#concepts-ga)를 사용하거나, [`Attributes`](#concepts-a)를 가지거나, [`GameplayEffects`](#concepts-ge)를 받고자 하는 모든 `Actor`는 반드시 하나의 `ASC`가 부착되어 있어야 합니다. 이러한 객체들은 모두 `ASC` 내부에 존재하며, `ASC`에 의해 관리되고 복제됩니다([`AttributeSet`](#concepts-as)에 의해 복제되는 `Attributes`는 제외). 개발자들은 이를 서브클래싱하는 것이 권장되지만 필수는 아닙니다.

`ASC`가 부착된 `Actor`는 해당 `ASC`의 `OwnerActor`라고 불립니다. `ASC`의 물리적 표현 `Actor`는 `AvatarActor`라고 불립니다. `OwnerActor`와 `AvatarActor`는 MOBA 게임의 간단한 AI 미니언의 경우처럼 동일한 `Actor`가 될 수 있습니다. 또한 MOBA 게임의 플레이어가 조종하는 영웅의 경우처럼 서로 다른 `Actor`가 될 수도 있는데, 이때 `OwnerActor`는 `PlayerState`이고 `AvatarActor`는 영웅의 `Character` 클래스입니다. 대부분의 `Actor`들은 자신에게 `ASC`를 가지게 됩니다. 만약 여러분의 `Actor`가 리스폰되고 스폰 사이에 `Attributes`나 `GameplayEffects`의 지속성이 필요하다면(MOBA의 영웅처럼), `ASC`의 이상적인 위치는 `PlayerState`입니다.

**참고:** 만약 여러분의 `ASC`가 `PlayerState`에 있다면, `PlayerState`의 `NetUpdateFrequency`를 증가시켜야 할 것입니다. 이는 `PlayerState`에서 기본적으로 매우 낮은 값으로 설정되어 있어서 `Attributes`와 `GameplayTags` 같은 것들의 변경사항이 클라이언트에 반영되기 전에 지연이나 지각된 렉을 유발할 수 있습니다. [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)를 활성화하세요. Fortnite가 이것을 사용합니다.

`OwnerActor`와 `AvatarActor` 모두 서로 다른 `Actor`인 경우에는 `IAbilitySystemInterface`를 구현해야 합니다. 이 인터페이스는 반드시 오버라이드해야 하는 함수 하나를 가지고 있는데, 바로 자신의 `ASC`에 대한 포인터를 반환하는 `UAbilitySystemComponent* GetAbilitySystemComponent() const`입니다. `ASC`들은 이 인터페이스 함수를 찾아 시스템 내부적으로 서로 상호작용합니다.

`ASC`는 현재 활성화된 `GameplayEffects`를 `FActiveGameplayEffectsContainer ActiveGameplayEffects`에 보관합니다.

`ASC`는 부여된 `Gameplay Abilities`를 `FGameplayAbilitySpecContainer ActivatableAbilities`에 보관합니다. `ActivatableAbilities.Items`를 순회할 계획이 있다면, 반드시 루프 위에 `ABILITYLIST_SCOPE_LOCK();`을 추가하여 리스트가 변경되지 않도록(어빌리티가 제거되는 것으로 인해) 잠가야 합니다. 스코프 내의 모든 `ABILITYLIST_SCOPE_LOCK();`는 `AbilityScopeLockCount`를 증가시키고 스코프를 벗어날 때 감소시킵니다. `ABILITYLIST_SCOPE_LOCK();`의 스코프 내에서 어빌리티를 제거하려고 하지 마세요(clear ability 함수들은 리스트가 잠겨있는 경우 어빌리티 제거를 방지하기 위해 내부적으로 `AbilityScopeLockCount`를 확인합니다).

<a name="concepts-asc-rm"></a>
### 4.1.1 복제 모드
`ASC`는 `GameplayEffects`, `GameplayTags`, 그리고 `GameplayCues`를 복제하기 위한 세 가지 다른 복제 모드를 정의합니다 - `Full`, `Mixed`, 그리고 `Minimal`. `Attributes`는 자신의 `AttributeSet`에 의해 복제됩니다.

| 복제 모드 | 사용 시점 | 설명 |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full` | 싱글 플레이어 | 모든 `GameplayEffect`가 모든 클라이언트에 복제됩니다. |
| `Mixed` | 멀티플레이어, 플레이어가 제어하는 `Actor`들 | `GameplayEffects`는 소유 클라이언트에게만 복제됩니다. `GameplayTags`와 `GameplayCues`만이 모든 사람에게 복제됩니다. |
| `Minimal` | 멀티플레이어, AI가 제어하는 `Actor`들 | `GameplayEffects`는 누구에게도 복제되지 않습니다. `GameplayTags`와 `GameplayCues`만이 모든 사람에게 복제됩니다. |

**참고:** `Mixed` 복제 모드는 `OwnerActor`의 `Owner`가 `Controller`일 것으로 예상합니다. `PlayerState`의 `Owner`는 기본적으로 `Controller`이지만 `Character`의 경우는 그렇지 않습니다. `OwnerActor`가 `PlayerState`가 아닌 상태에서 `Mixed` 복제 모드를 사용한다면, 유효한 `Controller`와 함께 `OwnerActor`에서 `SetOwner()`를 호출해야 합니다.

4.24 버전부터는 `PossessedBy()`가 이제 `Pawn`의 소유자를 새로운 `Controller`로 설정합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 설정과 초기화
`ASC`는 일반적으로 `OwnerActor`의 생성자에서 생성되며 명시적으로 복제되도록 표시됩니다. **이는 반드시 C++에서 수행되어야 합니다**.

```c++
AGDPlayerState::AGDPlayerState()
{
	// 어빌리티 시스템 컴포넌트를 생성하고, 명시적으로 복제되도록 설정
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC`는 서버와 클라이언트 모두에서 `OwnerActor`와 `AvatarActor`로 초기화되어야 합니다. `Pawn`의 `Controller`가 설정된 후(소유 후)에 초기화하는 것이 좋습니다. 싱글 플레이어 게임은 서버 경로만 신경 쓰면 됩니다.

`ASC`가 `Pawn`에 있는 플레이어 제어 캐릭터의 경우, 일반적으로 서버에서는 `Pawn`의 `PossessedBy()` 함수에서 초기화하고 클라이언트에서는 `PlayerController`의 `AcknowledgePossession()` 함수에서 초기화합니다.

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode 복제는 ASC Owner의 Owner가 Controller여야 합니다.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

`ASC`가 `PlayerState`에 있는 플레이어 제어 캐릭터의 경우, 일반적으로 서버에서는 `Pawn`의 `PossessedBy()` 함수에서 초기화하고 클라이언트에서는 `Pawn`의 `OnRep_PlayerState()` 함수에서 초기화합니다. 이는 클라이언트에서 `PlayerState`가 존재하는 것을 보장합니다.

```c++
// 서버에서만
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 서버에서 ASC를 설정. 클라이언트는 OnRep_PlayerState()에서 이를 수행
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI는 PlayerController를 가지지 않으므로 여기서 다시 초기화할 수 있습니다. PlayerController를 가진 영웅들에 대해 두 번 초기화해도 해가 없습니다.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// 클라이언트에서만
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 클라이언트를 위해 ASC를 설정. 서버는 PossessedBy에서 이를 수행
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// 클라이언트를 위해 ASC Actor Info를 초기화. 서버는 새로운 Actor를 소유할 때 ASC를 초기화
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

만약 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` 오류 메시지가 나타난다면, 클라이언트에서 `ASC`를 초기화하지 않은 것입니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)는 `Parent.Child.Grandchild...` 형태의 계층적 이름으로, `GameplayTagManager`에 등록됩니다. 이러한 태그들은 객체의 상태를 분류하고 설명하는 데 매우 유용합니다. 예를 들어, 캐릭터가 기절했을 때 기절 지속 시간 동안 `State.Debuff.Stun` `GameplayTag`를 부여할 수 있습니다.

bool이나 enum으로 처리하던 것들을 `GameplayTags`로 대체하고, 객체가 특정 `GameplayTags`를 가지고 있는지에 대한 boolean 로직을 수행하게 될 것입니다.

객체에 태그를 부여할 때, 일반적으로 GAS가 이와 상호작용할 수 있도록 ASC가 있는 경우 ASC에 추가합니다. `UAbilitySystemComponent`는 `IGameplayTagAssetInterface`를 구현하여 소유한 `GameplayTags`에 접근하는 함수들을 제공합니다.

여러 `GameplayTags`는 `FGameplayTagContainer`에 저장될 수 있습니다. `GameplayTagContainers`가 일부 효율성 관련 마법을 추가하기 때문에 `TArray<FGameplayTag>`보다 `GameplayTagContainer`를 사용하는 것이 좋습니다. 태그들이 표준 `FNames`이지만, 프로젝트 설정에서 `Fast Replication`이 활성화된 경우 `FGameplayTagContainers`에서 복제를 위해 효율적으로 압축될 수 있습니다. `Fast Replication`은 서버와 클라이언트가 동일한 `GameplayTags` 목록을 가지고 있어야 합니다. 이는 일반적으로 문제가 되지 않으므로 이 옵션을 활성화해야 합니다. `GameplayTagContainers`는 또한 반복을 위해 `TArray<FGameplayTag>`를 반환할 수 있습니다.

`FGameplayTagCountContainer`에 저장된 `GameplayTags`는 해당 `GameplayTag`의 인스턴스 수를 저장하는 `TagMap`을 가지고 있습니다. `FGameplayTagCountContainer`는 여전히 `GameplayTag`를 가지고 있을 수 있지만 `TagMapCount`가 0일 수 있습니다. ASC가 여전히 `GameplayTag`를 가지고 있는지 디버깅하는 동안 이를 발견할 수 있습니다. `HasTag()` 또는 `HasMatchingTag()` 또는 유사한 함수들은 `TagMapCount`를 확인하고 `GameplayTag`가 없거나 `TagMapCount`가 0인 경우 false를 반환합니다.

`GameplayTags`는 `DefaultGameplayTags.ini`에 미리 정의되어야 합니다. 언리얼 엔진 에디터는 개발자가 `DefaultGameplayTags.ini`를 수동으로 편집할 필요 없이 `GameplayTags`를 관리할 수 있도록 프로젝트 설정에 인터페이스를 제공합니다. `GameplayTag` 에디터는 `GameplayTags`를 생성, 이름 변경, 참조 검색, 삭제할 수 있습니다.

![프로젝트 설정의 GameplayTag 에디터](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

`GameplayTag` 참조를 검색하면 에디터에서 익숙한 `Reference Viewer` 그래프가 나타나 해당 `GameplayTag`를 참조하는 모든 에셋을 보여줍니다. 하지만 이는 `GameplayTag`를 참조하는 C++ 클래스는 보여주지 않습니다.

`GameplayTags`의 이름을 변경하면 원래 `GameplayTag`를 참조하는 에셋들이 새로운 `GameplayTag`로 리디렉션할 수 있도록 리디렉트가 생성됩니다. 가능하다면 리디렉트를 만드는 것을 피하기 위해 새로운 `GameplayTag`를 만들고, 모든 참조를 새로운 `GameplayTag`로 수동으로 업데이트한 다음, 이전 `GameplayTag`를 삭제하는 것을 선호합니다.

`Fast Replication` 외에도, `GameplayTag` 에디터는 자주 복제되는 `GameplayTags`를 채워 넣어 더욱 최적화하는 옵션을 제공합니다.

`GameplayTags`는 `GameplayEffect`에서 추가된 경우 복제됩니다. ASC는 복제되지 않고 수동으로 관리해야 하는 `LooseGameplayTags`를 추가할 수 있게 해줍니다. 샘플 프로젝트는 소유 클라이언트가 체력이 0이 되었을 때 즉시 반응할 수 있도록 `State.Dead`에 대해 `LooseGameplayTag`를 사용합니다. 리스폰은 수동으로 `TagMapCount`를 0으로 설정합니다. `LooseGameplayTags`로 작업할 때만 수동으로 `TagMapCount`를 조정하세요. `TagMapCount`를 수동으로 조정하는 것보다 `UAbilitySystemComponent::AddLooseGameplayTag()`와 `UAbilitySystemComponent::RemoveLooseGameplayTag()` 함수를 사용하는 것이 좋습니다.

C++에서 `GameplayTag` 참조 얻기:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

부모나 자식 `GameplayTags`를 얻는 것과 같은 고급 `GameplayTag` 조작을 위해서는 `GameplayTagManager`가 제공하는 함수들을 살펴보세요. `GameplayTagManager`에 접근하려면 `GameplayTagManager.h`를 포함하고 `UGameplayTagManager::Get().FunctionName`으로 호출하세요. `GameplayTagManager`는 실제로 지속적인 문자열 조작과 비교보다 더 빠른 처리를 위해 `GameplayTags`를 관계형 노드(부모, 자식 등)로 저장합니다.

`GameplayTags`와 `GameplayTagContainers`는 선택적 `UPROPERTY` 지정자 `Meta = (Categories = "GameplayCue")`를 가질 수 있으며, 이는 블루프린트에서 태그를 필터링하여 부모 태그가 `GameplayCue`인 `GameplayTags`만 표시합니다. 이는 `GameplayTag` 또는 `GameplayTagContainer` 변수가 `GameplayCues`에만 사용되어야 할 때 유용합니다.

또는 `FGameplayCueTag`라는 별도의 구조체가 있는데, 이는 `FGameplayTag`를 캡슐화하고 블루프린트에서 `GameplayTags`를 자동으로 필터링하여 부모 태그가 `GameplayCue`인 태그들만 표시합니다.

함수에서 `GameplayTag` 매개변수를 필터링하려면 `UFUNCTION` 지정자 `Meta = (GameplayTagFilter = "GameplayCue")`를 사용하세요. 함수의 `GameplayTagContainer` 매개변수는 필터링할 수 없습니다. 엔진을 편집하여 이를 허용하고 싶다면, `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp`의 `SGameplayTagGraphPin::ParseDefaultValueData()`가 `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`를 호출하고 `SGameplayTagGraphPin::GetListContent()`에서 `FilterString`을 `SGameplayTagWidget`에 전달하는 방법을 살펴보세요. `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp`의 `GameplayTagContainer` 버전 함수들은 메타 필드 속성을 확인하지 않고 필터를 전달합니다.

샘플 프로젝트는 `GameplayTags`를 광범위하게 사용합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 게임플레이 태그 변경에 대한 응답
ASC는 `GameplayTags`가 추가되거나 제거될 때 델리게이트를 제공합니다. 이는 `EGameplayTagEventType`을 받아 `GameplayTag`가 추가/제거될 때만 발생하거나 `GameplayTag`의 `TagMapCount`의 모든 변경에 대해 발생하도록 지정할 수 있습니다.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

콜백 함수는 `GameplayTag`와 새로운 `TagCount`에 대한 매개변수를 가집니다.
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 플러그인 .ini 파일에서 게임플레이 태그 로딩
자체 .ini 파일에 `GameplayTags`가 있는 플러그인을 만드는 경우, 플러그인의 `StartupModule()` 함수에서 해당 플러그인의 `GameplayTag` .ini 디렉토리를 로드할 수 있습니다.

예를 들어, 언리얼 엔진과 함께 제공되는 CommonConversation 플러그인은 다음과 같이 수행합니다.

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

이는 `Plugins\CommonConversation\Config\Tags` 디렉토리를 찾아 플러그인이 활성화된 경우 엔진이 시작될 때 프로젝트에 `GameplayTags`가 있는 모든 .ini 파일을 로드합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute 정의
`Attribute`는 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 구조체로 정의된 float 값입니다. 이는 캐릭터가 가진 체력량부터 캐릭터의 레벨, 포션이 가진 충전량까지 무엇이든 표현할 수 있습니다. `Actor`에 속한 게임플레이 관련 수치 값이라면 `Attribute`를 사용하는 것을 고려해야 합니다. ASC가 변경사항을 [예측](#concepts-p)할 수 있도록 `Attribute`는 일반적으로 [`GameplayEffects`](#concepts-ge)에 의해서만 수정되어야 합니다.

`Attribute`는 [`AttributeSet`](#concepts-as)에 의해 정의되고 그 안에 존재합니다. `AttributeSet`는 복제로 표시된 `Attribute`의 복제를 담당합니다. `Attribute`를 정의하는 방법은 [`AttributeSets`](#concepts-as) 섹션을 참조하세요.

**팁:** `Attribute`가 에디터의 `Attributes` 목록에 표시되지 않기를 원한다면, `Meta = (HideInDetailsView)` `property specifier`를 사용할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
`Attribute`는 두 가지 값 - `BaseValue`와 `CurrentValue`로 구성됩니다. `BaseValue`는 `Attribute`의 영구적인 값인 반면, `CurrentValue`는 `BaseValue`에 `GameplayEffects`로부터의 일시적인 수정사항을 더한 값입니다. 예를 들어, 여러분의 `Character`가 초당 600 유닛의 `BaseValue`를 가진 이동속도 `Attribute`를 가질 수 있습니다. 아직 이동속도를 수정하는 `GameplayEffects`가 없으므로 `CurrentValue` 또한 600 u/s입니다. 만약 그녀가 일시적으로 50 u/s의 이동속도 버프를 받으면, `BaseValue`는 600 u/s로 유지되는 반면 `CurrentValue`는 이제 600 + 50으로 총 650 u/s가 됩니다. 이동속도 버프가 만료되면, `CurrentValue`는 다시 600 u/s의 `BaseValue`로 돌아갑니다.

종종 GAS 초보자들은 `BaseValue`를 `Attribute`의 최대값으로 혼동하고 그렇게 취급하려고 합니다. 이는 잘못된 접근입니다. 변경될 수 있거나 어빌리티나 UI에서 참조되는 `Attribute`의 최대값은 별도의 `Attribute`로 취급되어야 합니다. 하드코딩된 최대값과 최소값의 경우, `FAttributeMetaData`로 `DataTable`을 정의하여 최대값과 최소값을 설정하는 방법이 있지만, 구조체 위의 Epic의 주석은 이를 "진행 중인 작업"이라고 부릅니다. 자세한 정보는 `AttributeSet.h`를 참조하세요. 혼동을 피하기 위해, 어빌리티나 UI에서 참조될 수 있는 최대값은 별도의 `Attribute`로 만들고, `Attribute`를 고정하는 데만 사용되는 하드코딩된 최대값과 최소값은 `AttributeSet`에서 하드코딩된 float으로 정의하는 것을 권장합니다. `Attribute`의 고정은 `CurrentValue` 변경에 대해서는 [PreAttributeChange()](#concepts-as-preattributechange)에서, `GameplayEffects`로부터의 `BaseValue` 변경에 대해서는 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)에서 논의됩니다.

`BaseValue`의 영구적인 변경은 `Instant` `GameplayEffects`로부터 오는 반면, `Duration`과 `Infinite` `GameplayEffects`는 `CurrentValue`를 변경합니다. 주기적인(Periodic) `GameplayEffects`는 즉각적인(Instant) `GameplayEffects`처럼 취급되어 `BaseValue`를 변경합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
일부 `Attribute`는 `Attribute`와 상호작용하기 위한 임시 값의 플레이스홀더로 취급됩니다. 이를 `Meta Attributes`라고 합니다. 예를 들어, 우리는 일반적으로 데미지를 `Meta Attribute`로 정의합니다. `GameplayEffect`가 우리의 체력 `Attribute`를 직접 변경하는 대신, 데미지라는 `Meta Attribute`를 플레이스홀더로 사용합니다. 이렇게 하면 데미지 값이 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec)에서 버프와 디버프로 수정될 수 있고, 최종적으로 체력 `Attribute`에서 나머지를 빼기 전에 `AttributeSet`에서 현재 보호막 `Attribute`에서 데미지를 빼는 등 추가로 조작될 수 있습니다. 데미지 `Meta Attribute`는 `GameplayEffects` 사이에 지속성이 없으며 모든 `GameplayEffects`에 의해 덮어씌워집니다. `Meta Attributes`는 일반적으로 복제되지 않습니다.

`Meta Attributes`는 데미지와 치유 같은 것들에 대해 "얼마나 많은 데미지를 주었는가?"와 "이 데미지로 무엇을 할 것인가?" 사이에 좋은 논리적 분리를 제공합니다. 이러한 논리적 분리는 우리의 `Gameplay Effects`와 `Execution Calculations`가 대상이 데미지를 어떻게 처리하는지 알 필요가 없다는 것을 의미합니다. 데미지 예시를 계속하면, `Gameplay Effect`는 얼마나 많은 데미지를 줄지 결정하고 `AttributeSet`는 그 데미지로 무엇을 할지 결정합니다. 특히 서브클래스된 `AttributeSets`를 사용하는 경우 모든 캐릭터가 같은 `Attributes`를 가지지 않을 수 있습니다. 기본 `AttributeSet` 클래스는 체력 `Attribute`만 가질 수 있지만, 서브클래스된 `AttributeSet`는 보호막 `Attribute`를 추가할 수 있습니다. 보호막 `Attribute`가 있는 서브클래스된 `AttributeSet`는 받은 데미지를 기본 `AttributeSet` 클래스와는 다르게 분배할 것입니다.

`Meta Attributes`는 좋은 설계 패턴이지만, 필수적이지는 않습니다. 만약 모든 데미지 인스턴스에 대해 하나의 `Execution Calculation`만 사용하고 모든 캐릭터가 공유하는 하나의 `Attribute Set` 클래스만 있다면, `Execution Calculation` 내에서 체력, 보호막 등에 대한 데미지 분배를 수행하고 해당 `Attributes`를 직접 수정하는 것으로도 충분할 수 있습니다. 유연성을 희생하게 되지만, 그것이 여러분에게는 괜찮을 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Attribute 변경에 대한 응답
UI나 다른 게임플레이를 업데이트하기 위해 `Attribute`가 변경될 때를 감지하려면 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`를 사용하세요. 이 함수는 `Attribute`가 변경될 때마다 자동으로 호출되는 델리게이트를 반환하며, 이 델리게이트에 바인딩할 수 있습니다. 델리게이트는 `NewValue`, `OldValue`, `FGameplayEffectModCallbackData`를 포함하는 `FOnAttributeChangeData` 매개변수를 제공합니다. **참고:** `FGameplayEffectModCallbackData`는 서버에서만 설정됩니다.

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

샘플 프로젝트는 HUD를 업데이트하고 체력이 0이 되었을 때 플레이어 사망에 응답하기 위해 `GDPlayerState`의 `Attribute` 값 변경 델리게이트에 바인딩합니다.

이를 `ASyncTask`로 감싸는 커스텀 블루프린트 노드가 샘플 프로젝트에 포함되어 있습니다. 이는 `UI_HUD` UMG 위젯에서 체력, 마나, 스태미나 값을 업데이트하는 데 사용됩니다. 이 `AsyncTask`는 수동으로 `EndTask()`가 호출될 때까지 영원히 지속되며, 우리는 이를 UMG 위젯의 `Destruct` 이벤트에서 수행합니다. `AsyncTaskAttributeChanged.h/cpp`를 참조하세요.

![Attribute 변경 감지 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 파생 Attributes
하나 이상의 다른 `Attributes`로부터 일부 또는 전체 값이 파생되는 `Attribute`를 만들려면, 하나 이상의 `Attribute Based` 또는 [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods)가 있는 `Infinite` `GameplayEffect`를 사용하세요. `Derived Attribute`는 의존하는 `Attribute`가 업데이트될 때 자동으로 업데이트됩니다.

`Derived Attribute`의 모든 `Modifiers`에 대한 최종 공식은 `Modifier Aggregators`의 공식과 동일합니다. 계산이 특정 순서로 이루어져야 한다면, 모든 계산을 `MMC` 내부에서 수행하세요.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**참고:** PIE에서 여러 클라이언트와 함께 플레이하는 경우, 독립 `Attributes`가 첫 번째 클라이언트 이외의 클라이언트에서 업데이트될 때 `Derived  Attributes`가 업데이트되지 않으므로 에디터 환경설정에서 `Run Under One Process`를 비활성화해야 합니다.

이 예시에서, 우리는 `TestAttrB`와 `TestAttrC` `Attributes`로부터 `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)` 공식으로 `TestAttrA`의 값을 파생시키는 `Infinite` `GameplayEffect`를 가지고 있습니다. `TestAttrA`는 `Attributes` 중 어느 것이든 값이 업데이트될 때마다 자동으로 값을 재계산합니다.

![Derived Attribute 예제](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set 정의
`AttributeSet`은 `Attributes`를 정의하고, 보유하며, 변경을 관리합니다. 개발자는 [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)을 상속해야 합니다. `OwnerActor`의 생성자에서 `AttributeSet`을 생성하면 자동으로 해당 ASC에 등록됩니다. **이는 반드시 C++에서 수행되어야 합니다**.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set 설계
하나의 `ASC`는 하나 또는 여러 개의 `AttributeSets`를 가질 수 있습니다. AttributeSets는 무시할 만한 메모리 오버헤드를 가지므로 얼마나 많은 `AttributeSets`를 사용할지는 개발자의 조직적 결정에 달려있습니다.

게임의 모든 `Actor`가 공유하는 하나의 큰 단일 `AttributeSet`을 가지고, 필요한 속성만 사용하고 사용하지 않는 속성은 무시하는 것도 가능합니다.

또는 필요에 따라 `Actor`에 선택적으로 추가할 수 있는 `Attributes` 그룹을 나타내는 여러 개의 `AttributeSet`을 가질 수 있습니다. 예를 들어, 체력 관련 `Attributes`를 위한 `AttributeSet`, 마나 관련 `Attributes`를 위한 `AttributeSet` 등을 가질 수 있습니다. MOBA 게임에서 영웅들은 마나가 필요하지만 미니언들은 필요하지 않을 수 있습니다. 따라서 영웅들은 마나 `AttributeSet`을 받지만 미니언들은 받지 않을 것입니다.

추가로, `AttributeSets`는 `Actor`가 가질 `Attributes`를 선택적으로 선택하는 또 다른 수단으로 서브 클래스화될 수 있습니다. `Attributes`는 내부적으로 `AttributeSetClassName.AttributeName`으로 참조됩니다. `AttributeSet`을 서브 클래스화할 때, 부모 클래스의 모든 `Attributes`는 여전히 부모 클래스의 이름을 접두사로 가질 것입니다.

여러 개의 `AttributeSet`을 가질 수 있지만, 하나의 `ASC`에 동일한 클래스의 `AttributeSet`을 여러 개 가져서는 안 됩니다. 동일한 클래스의 `AttributeSet`을 여러 개 가지고 있다면, 어떤 `AttributeSet`을 사용할지 알 수 없어 그냥 하나를 선택할 것입니다.

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 개별 Attributes를 가진 서브 컴포넌트
개별적으로 데미지를 받을 수 있는 방어구 부위과 같이 `Pawn`에 여러 개의 데미지를 받을 수 있는 컴포넌트가 있는 시나리오에서, `Pawn`이 가질 수 있는 데미지를 받을 수 있는 컴포넌트의 최대 수를 알고 있다면 하나의 `AttributeSet`에 그만큼의 체력 `Attributes`를 만드는 것을 추천합니다 - DamageableCompHealth0, DamageableCompHealth1 등으로 데미지를 받을 수 있는 컴포넌트들의 논리적 '슬롯'을 나타냅니다. 데미지를 받을 수 있는 컴포넌트 클래스 인스턴스에서, 어떤 `Attribute`에 데미지를 적용할지 알 수 있도록 `GameplayAbilities`나 [`Executions`](#concepts-ge-ec)에서 읽을 수 있는 슬롯 번호 `Attribute`를 할당합니다. 최대 수보다 적거나 0개의 데미지를 받을 수 있는 컴포넌트를 가진 `Pawns`도 괜찮습니다. `AttributeSet`이 `Attribute`를 가지고 있다고 해서 반드시 사용할 필요는 없습니다. 사용되지 않는 `Attributes`는 무시할 만한 양의 메모리를 차지합니다.

서브 컴포넌트가 각각 많은 `Attributes`가 필요하거나, 잠재적으로 무제한의 서브 컴포넌트가 있거나, 서브 컴포넌트가 분리되어 다른 플레이어가 사용할 수 있거나(예: 무기), 또는 다른 이유로 이 접근 방식이 적합하지 않은 경우, `Attributes` 대신 컴포넌트에 일반 float를 저장하는 것을 추천합니다. [아이템 Attributes](#concepts-as-design-itemattributes)를 참조하세요.

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 런타임에 AttributeSets 추가 및 제거
`AttributeSets`는 런타임에 `ASC`에 추가되거나 제거될 수 있습니다. 하지만 `AttributeSets`를 제거하는 것은 위험할 수 있습니다. 예를 들어, `AttributeSet`이 서버보다 클라이언트에서 먼저 제거되고 `Attribute` 값 변경이 클라이언트에 복제되면, `Attribute`가 자신의 `AttributeSet`을 찾지 못하고 게임이 크래시될 것입니다.

인벤토리에 무기 추가 시:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

인벤토리에서 무기 제거 시:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 아이템 Attributes (무기 탄약)
`Attributes`(무기 탄약, 방어구 내구도 등)가 있는 장착 가능한 아이템을 구현하는 방법이 몇 가지 있습니다. 이러한 모든 접근 방식은 아이템에 직접 값을 저장합니다. 이는 수명 동안 둘 이상의 플레이어가 장착할 수 있는 아이템에 필요합니다.

> 1. 아이템에 일반 float 사용 (**권장**)
> 2. 아이템에 별도의 `AttributeSet` 사용
> 3. 아이템에 별도의 `ASC` 사용

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 아이템에 일반 Float 사용
`Attributes` 대신, 아이템 클래스 인스턴스에 일반 float 값을 저장합니다. Fortnite와 [GASShooter](https://github.com/tranek/GASShooter)는 이런 방식으로 총기 탄약을 처리합니다. 총의 경우, 최대 클립 크기, 현재 클립의 탄약, 예비 탄약 등을 총 인스턴스에 직접 복제된 float(`COND_OwnerOnly`)로 저장합니다. 무기들이 예비 탄약을 공유한다면, 예비 탄약을 공유 탄약 `AttributeSet`의 `Attribute`로 캐릭터에 이동시킬 것입니다(재장전 어빌리티는 `Cost GE`를 사용하여 예비 탄약에서 총의 float 클립 탄약으로 가져올 수 있습니다). 현재 클립 탄약에 `Attributes`를 사용하지 않으므로, 총의 float에 대한 비용을 확인하고 적용하기 위해 `UGameplayAbility`의 일부 함수를 오버라이드해야 합니다. 어빌리티를 부여할 때 총을 [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)의 `SourceObject`로 만들면 어빌리티 내부에서 어빌리티를 부여한 총에 접근할 수 있습니다.

자동 발사 중에 총이 탄약량을 다시 복제하여 로컬 탄약량을 덮어쓰는 것을 방지하기 위해, `PreReplication()`에서 플레이어가 `IsFiring` `GameplayTag`를 가지고 있을 때 복제를 비활성화합니다. 본질적으로 여기서 자체 로컬 예측을 수행하고 있는 것입니다.

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

장점:
1. `AttributeSets` 사용의 제한사항을 피할 수 있음 (하단 참고)

제한사항:
1. 기존 `GameplayEffect` 워크플로우를 사용할 수 없음(탄약 사용을 위한 `Cost GEs` 등)
1. 총의 float에 대한 탄약 비용을 확인하고 적용하기 위해 `UGameplayAbility`의 주요 함수를 오버라이드하는 작업이 필요함

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 아이템에 `AttributeSet` 사용
아이템에 별도의 `AttributeSet`을 사용하고 [플레이어의 인벤토리에 추가할 때 플레이어의 `ASC`에 추가하는](#concepts-as-design-addremoveruntime) 방식도 가능하지만, 몇 가지 주요 제한사항이 있습니다. [GASShooter](https://github.com/tranek/GASShooter)의 초기 버전에서 무기 탄약에 대해 이 방식이 작동했습니다. 무기는 최대 클립 크기, 현재 클립의 탄약, 예비 탄약 등과 같은 `Attributes`를 무기 클래스에 있는 `AttributeSet`에 저장합니다. 무기들이 예비 탄약을 공유한다면, 예비 탄약을 공유 탄약 `AttributeSet`의 캐릭터로 이동시킬 것입니다. 서버에서 무기가 플레이어의 인벤토리에 추가될 때, 무기는 자신의 `AttributeSet`을 플레이어의 `ASC::SpawnedAttributes`에 추가합니다. 그런 다음 서버는 이를 클라이언트로 복제합니다. 무기가 인벤토리에서 제거되면, `ASC::SpawnedAttributes`에서 자신의 `AttributeSet`을 제거합니다.

`AttributeSet`이 `OwnerActor`가 아닌 다른 곳(예: 무기)에 있을 때, 처음에는 `AttributeSet`에서 몇 가지 컴파일 오류가 발생할 것입니다. 해결책은 생성자 대신 `BeginPlay()`에서 `AttributeSet`을 구성하고 무기에 `IAbilitySystemInterface`를 구현하는 것입니다(플레이어 인벤토리에 무기를 추가할 때 `ASC`에 대한 포인터를 설정).

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

이를 실제로 확인하려면 [GASShooter의 이전 버전](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)을 참조하세요.

장점:
1. 기존 `GameplayAbility`와 `GameplayEffect` 워크플로우를 사용할 수 있음(탄약 사용을 위한 `Cost GEs` 등)
1. 매우 적은 수의 아이템에 대해 설정이 간단함

제한사항:
1. 모든 무기 타입에 대해 새로운 `AttributeSet` 클래스를 만들어야 함. `ASC`는 `Attribute`에 대한 변경이 `ASCs` `SpawnedAttributes` 배열에서 해당 `AttributeSet` 클래스의 첫 번째 인스턴스를 찾기 때문에 기능적으로 하나의 `AttributeSet` 인스턴스만 가질 수 있습니다. 동일한 `AttributeSet` 클래스의 추가 인스턴스는 무시됩니다.
1. 이전 이유(하나의 `AttributeSet` 클래스당 하나의 인스턴스)로 인해 플레이어의 인벤토리에 각 타입의 무기를 하나만 가질 수 있습니다.
1. `AttributeSet`을 제거하는 것은 위험합니다. GASShooter에서 플레이어가 로켓으로 자살했을 때, 플레이어는 즉시 로켓 런처를 인벤토리에서 제거했습니다(`ASC`에서 `AttributeSet` 포함). 서버가 로켓 런처의 탄약 `Attribute` 변경을 복제했을 때, `AttributeSet`이 더 이상 클라이언트의 `ASC`에 존재하지 않아 게임이 크래시되었습니다.

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
각 아이템에 전체 `AbilitySystemComponent`를 두는 것은 극단적인 접근 방식입니다. 저는 개인적으로 이것을 해본 적이 없으며 실제로도 본 적이 없습니다. 이를 작동시키기 위해서는 많은 엔지니어링이 필요할 것입니다.

> 같은 owner를 가지지만 다른 아바타를 가진 여러 AbilitySystemComponents를 가지는 것이 가능한가요(예: PlayerState를 Owner로 설정하고 폰과 무기/아이템/발사체에)?
> 
> 제가 보는 첫 번째 문제는 소유 액터에서 IGameplayTagAssetInterface와 IAbilitySystemInterface를 구현하는 것입니다. 전자는 가능할 수 있습니다: 모든 ASC의 태그를 집계하면 됩니다(하지만 주의하세요 - HasAllMatchingGameplayTags는 ASC 간 집계를 통해서만 충족될 수 있습니다. 각 ASC에 호출을 전달하고 결과를 OR 연산하는 것만으로는 충분하지 않을 것입니다). 하지만 후자는 더 까다롭습니다: 어떤 ASC가 권위 있는 것인가요? 누군가 GE를 적용하려고 할 때 - 어떤 것이 받아야 하나요? 이러한 문제들을 해결할 수 있을지 모르지만 이 측면의 문제가 가장 어려울 것입니다: owner들은 그들 아래에 여러 ASC를 가지게 될 것입니다.
> 
> 폰과 무기에 별도의 ASC를 두는 것은 그 자체로 의미가 있을 수 있습니다. 예를 들어, 무기를 설명하는 태그와 소유 폰을 설명하는 태그를 구분하는 것입니다. 아마도 무기에 부여된 태그가 owner에게도 "적용"되고 다른 것에는 적용되지 않는 것이 의미가 있을 수 있습니다(예: attributes와 GE는 독립적이지만 owner는 제가 위에서 설명한 것처럼 소유된 태그를 집계할 것입니다). 이것은 작동할 수 있을 것이라고 확신합니다. 하지만 같은 owner를 가진 여러 ASC를 가지는 것은 까다로울 수 있습니다.

*Epic의 Dave Ratti가 [커뮤니티 질문 #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)에 대한 답변*

장점:
1. 기존 `GameplayAbility`와 `GameplayEffect` 워크플로우를 사용할 수 있음(탄약 사용을 위한 `Cost GEs` 등)
1. `AttributeSet` 클래스를 재사용할 수 있음(각 무기의 ASC에 하나씩)

제한사항:
1. 알 수 없는 엔지니어링 비용
1. 가능한가?

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Attribute 정의
**`Attributes`는 `AttributeSet`의 헤더 파일에서 C++로만 정의할 수 있습니다**. 모든 `AttributeSet` 헤더 파일 상단에 다음 매크로 블록을 추가하는 것을 권장합니다. 이는 자동으로 `Attributes`에 대한 getter와 setter 함수를 생성합니다.

```c++
// AttributeSet.h의 매크로 사용
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

복제되는 체력 attribute는 다음과 같이 정의됩니다.
```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

헤더에 `OnRep` 함수도 정의합니다.
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`의 .cpp 파일은 예측 시스템에서 사용하는 `GAMEPLAYATTRIBUTE_REPNOTIFY` 매크로로 `OnRep` 함수를 채워야 합니다.
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

마지막으로, `Attribute`를 `GetLifetimeReplicatedProps`에 추가해야 합니다.
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`는 (예측으로 인해) 로컬 값이 이미 서버에서 복제되는 값과 같을 때도 `OnRep` 함수를 트리거하도록 지시합니다. 기본적으로는 로컬 값이 서버에서 복제되는 값과 같을 경우 `OnRep` 함수를 트리거하지 않습니다.

`Meta Attribute`처럼 `Attribute`가 복제되지 않는 경우, `OnRep`와 `GetLifetimeReplicatedProps` 단계는 생략할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Attribute 초기화
`Attributes`를 초기화하는 방법(초기값으로 `BaseValue`와 결과적으로 `CurrentValue`를 설정하는 것)은 여러 가지가 있습니다. Epic은 즉각적인 `GameplayEffect`를 사용하는 것을 권장합니다. 이는 샘플 프로젝트에서도 사용되는 방법입니다.

`Attributes`를 초기화하기 위한 즉각적인 `GameplayEffect`를 만드는 방법은 샘플 프로젝트의 `GE_HeroAttributes` 블루프린트를 참조하세요. 이 `GameplayEffect`의 적용은 C++에서 이루어집니다.

`Attributes`를 정의할 때 `ATTRIBUTE_ACCESSORS` 매크로를 사용했다면, 각 `Attribute`에 대해 C++에서 원하는 대로 호출할 수 있는 초기화 함수가 `AttributeSet`에 자동으로 생성됩니다.

```c++
// InitHealth(float InitialValue)는 `ATTRIBUTE_ACCESSORS` 매크로로 정의된 'Health' Attribute에 대해 자동 생성된 함수입니다.
AttributeSet->InitHealth(100.0f);
```

더 많은 `Attributes` 초기화 방법은 `AttributeSet.h`를 참조하세요.

**참고:** 4.24 이전에는 `FAttributeSetInitterDiscreteLevels`가 `FGameplayAttributeData`와 작동하지 않았습니다. 이는 `Attributes`가 raw float였을 때 생성되었으며 `FGameplayAttributeData`가 `Plain Old Data`(`POD`)가 아니라고 불평할 것입니다. 이는 4.24에서 수정되었습니다 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`는 변경이 일어나기 전에 `Attribute`의 `CurrentValue` 변경에 응답하는 `AttributeSet`의 주요 함수 중 하나입니다. 참조 매개변수 `NewValue`를 통해 `CurrentValue`에 대한 들어오는 변경을 제한하는 이상적인 위치입니다.

예를 들어 샘플 프로젝트에서는 이동속도 모디파이어를 다음과 같이 제한합니다.
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 150 units/s 미만으로 느려지거나 1000 units/s 이상으로 빨라질 수 없음
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```

`GetMoveSpeedAttribute()` 함수는 `AttributeSet.h`에 추가한 매크로 블록에 의해 생성됩니다 ([Attributes 정의](#concepts-as-attributes)).

이는 `Attribute` setter(AttributeSet.h의 매크로 블록에 의해 정의됨 [(Attributes 정의)](#concepts-as-attributes)) 사용이나 [`GameplayEffects`](#concepts-ge) 사용 등 모든 `Attributes` 변경에서 트리거됩니다.

**참고:** 여기서 일어나는 제한은 `ASC`의 모디파이어를 영구적으로 변경하지 않습니다. 모디파이어를 조회할 때 반환되는 값만 변경합니다. 이는 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)와 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)처럼 모든 모디파이어로부터 `CurrentValue`를 재계산하는 것들은 제한을 다시 구현해야 함을 의미합니다.

**참고:** Epic의 `PreAttributeChange()` 주석에는 게임플레이 이벤트에 사용하지 말고 주로 클램핑(Clamping)에 사용하라고 되어 있습니다. `Attribute` 변경에 대한 게임플레이 이벤트의 권장 위치는 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`입니다 ([Attribute 변경에 대한 응답](#concepts-a-changes)).

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`는 즉각적인 [`GameplayEffect`](#concepts-ge)로부터 `Attribute`의 `BaseValue` 변경 후에만 트리거됩니다. 이는 `GameplayEffect`로부터 변경될 때 추가적인 `Attribute` 조작을 수행하는 유효한 위치입니다.

예를 들어, 샘플 프로젝트에서는 여기서 최종 데미지 `Meta Attribute`를 체력 `Attribute`에서 차감합니다. 보호막 `Attribute`가 있다면, 체력에서 나머지를 차감하기 전에 먼저 보호막에서 데미지를 차감할 것입니다. 샘플 프로젝트는 또한 이 위치를 사용하여 타격 반응 애니메이션을 적용하고, 부동 데미지 수치를 표시하며, 킬러에게 경험치와 골드 현상금을 할당합니다. 설계상, 데미지 `Meta Attribute`는 항상 즉각적인 `GameplayEffect`를 통해 오며 절대 `Attribute` setter를 통해 오지 않습니다.

마나와 스태미나처럼 즉각적인 `GameplayEffects`로부터만 `BaseValue`가 변경되는 다른 `Attributes`도 여기서 최대값 상대 `Attributes`로 제한될 수 있습니다.

**참고:** `PostGameplayEffectExecute()`가 호출될 때, `Attribute`에 대한 변경은 이미 일어났지만 아직 클라이언트에 복제되지는 않았으므로 여기서 값을 제한해도 클라이언트에 두 번의 네트워크 업데이트가 발생하지 않습니다. 클라이언트는 클램핑 후의 업데이트만 받게 됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`는 이 세트의 `Attribute`에 대해 `Aggregator`가 생성될 때 트리거됩니다. [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)의 커스텀 설정을 허용합니다. `AggregatorEvaluateMetaData`는 `Aggregator`가 적용된 모든 [`Modifiers`](#concepts-ge-mods)를 기반으로 `Attribute`의 `CurrentValue`를 평가할 때 사용됩니다. 기본적으로 `AggregatorEvaluateMetaData`는 `Aggregator`에서 `MostNegativeMod_AllPositiveMods` 예시처럼 모든 양수 `modifier`를 허용하지만 음수 `modifier`는 가장 큰 음수 하나로만 제한하는 등 어떤 모디파이어가 해당되는지 결정하는 데만 사용됩니다. 이는 Paragon에서 한 플레이어에게 얼마나 많은 감속 효과가 동시에 적용되어 있든 가장 큰 음수 이동속도 감속 효과만 적용하면서 모든 양수 이동속도 버프는 적용하기 위해 사용되었습니다. 해당되지 않는 모디파이어도 여전히 `ASC`에 존재하지만, 최종 `CurrentValue`에는 집계되지 않습니다. 조건이 변경되면, 예를 들어 가장 큰 음수 모디파이어가 만료되면 다음으로 큰 음수 모디파이어(존재하는 경우)가 해당되는 것처럼 나중에 잠재적으로 해당될 수 있습니다.

가장 큰 음수 모디파이어와 모든 양수 모디파이어만 허용하는 예시에서 AggregatorEvaluateMetaData를 사용하는 방법:

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

해당 여부를 판단하는 커스텀 `AggregatorEvaluateMetaData`는 정적 변수로 `FAggregatorEvaluateMetaDataLibrary`에 추가되어야 합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effect 정의
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`)는 어빌리티가 자신과 다른 대상의 [`Attributes`](#concepts-a)와 [`GameplayTags`](#concepts-gt)를 변경하는 매개체입니다. 이들은 데미지나 치유와 같은 즉각적인 `Attribute` 변경을 일으키거나 이동속도 증가나 기절과 같은 장기적인 상태 버프/디버프를 적용할 수 있습니다. `UGameplayEffect` 클래스는 단일 게임플레이 이펙트를 정의하는 **데이터 전용** 클래스입니다. `GameplayEffects`에 추가적인 로직을 넣어서는 안 됩니다. 일반적으로 디자이너들은 `UGameplayEffect`의 많은 블루프린트 자식 클래스를 만들 것입니다.

`GameplayEffects`는 [`Modifiers`](#concepts-ge-mods)와 [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec)를 통해 `Attributes`를 변경합니다.

`GameplayEffects`는 세 가지 지속 시간 유형을 가집니다: `Instant`, `Duration`, 그리고 `Infinite`.

추가로, `GameplayEffects`는 [`GameplayCues`](#concepts-gc)를 추가/실행할 수 있습니다. `Instant` `GameplayEffect`는 `GameplayCue` `GameplayTags`에서 `Execute`를 호출하는 반면, `Duration`이나 `Infinite` `GameplayEffect`는 `GameplayCue` `GameplayTags`에서 `Add`와 `Remove`를 호출합니다.

| 지속 시간 유형 | GameplayCue 이벤트 | 사용 시점 |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant` | Execute | `Attribute`의 `BaseValue`를 즉시 영구적으로 변경할 때. `GameplayTags`는 한 프레임 동안도 적용되지 않습니다. |
| `Duration` | Add & Remove | `Attribute`의 `CurrentValue`를 임시로 변경하고 `GameplayEffect`가 만료되거나 수동으로 제거될 때 제거될 `GameplayTags`를 적용할 때. 지속 시간은 `UGameplayEffect` 클래스/블루프린트에서 지정됩니다. |
| `Infinite` | Add & Remove | `Attribute`의 `CurrentValue`를 임시로 변경하고 `GameplayEffect`가 제거될 때 제거될 `GameplayTags`를 적용할 때. 이들은 자체적으로 만료되지 않으며 어빌리티나 `ASC`에 의해 수동으로 제거되어야 합니다. |

`Duration`과 `Infinite` `GameplayEffects`는 `Period`로 정의된 매 `X`초마다 모디파이어와 `Executions`를 적용하는 `Periodic Effects` 옵션을 가지고 있습니다. `Periodic Effects`는 `Attribute`의 `BaseValue`를 변경하고 `GameplayCues`를 `Executing`할 때 `Instant` `GameplayEffects`로 취급됩니다. 이는 시간 경과에 따른 데미지(DOT) 유형 효과에 유용합니다. **참고:** `Periodic Effects`는 [예측](#concepts-p)될 수 없습니다.

`Duration`과 `Infinite` `GameplayEffects`는 `Ongoing Tag Requirements`가 충족되지 않거나/충족될 때([게임플레이 이펙트 태그](#concepts-ge-tags)) 적용 후 일시적으로 켜고 끌 수 있습니다. `GameplayEffect`를 끄면 모디파이어의 효과와 적용된 `GameplayTags`는 제거되지만 `GameplayEffect`는 제거되지 않습니다. `GameplayEffect`를 다시 켜면 모디파이어와 `GameplayTags`가 다시 적용됩니다.

`Duration`이나 `Infinite` `GameplayEffect`의 모디파이어를 수동으로 재계산해야 하는 경우(`Attributes`에서 오지 않는 데이터를 사용하는 `MMC`가 있는 경우), `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`을 사용하여 이미 가지고 있는 것과 같은 레벨로 `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`을 호출할 수 있습니다. 백업 `Attributes`를 기반으로 하는 모디파이어는 해당 백업 `Attributes`가 업데이트될 때 자동으로 업데이트됩니다. 모디파이어를 업데이트하는 `SetActiveGameplayEffectLevel()`의 주요 함수는 다음과 같습니다:

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// 레벨을 이미 가지고 있는 것으로 설정할 필요 없이 이 세 함수를 호출할 수 있으면 좋겠지만 private 함수입니다
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects`는 일반적으로 인스턴스화되지 않습니다. 어빌리티나 `ASC`가 `GameplayEffect`를 적용하고자 할 때, `GameplayEffect`의 `ClassDefaultObject`에서 [`GameplayEffectSpec`](#concepts-ge-spec)을 생성합니다. 성공적으로 적용된 `GameplayEffectSpecs`는 `ASC`가 `ActiveGameplayEffects`라는 특별한 컨테이너 구조체에서 추적하는 `FActiveGameplayEffect`라는 새로운 구조체에 추가됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 Gameplay Effect 적용
`GameplayEffects`는 [`GameplayAbilities`](#concepts-ga)의 함수들과 `ASC`의 함수들을 통해 여러 가지 방법으로 적용될 수 있으며, 보통 `ApplyGameplayEffectTo` 형태를 가집니다. 서로 다른 함수들은 본질적으로 결국 대상의 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`를 호출하는 편의 함수입니다.

발사체와 같이 `GameplayAbility` 외부에서 `GameplayEffects`를 적용하려면, 대상의 `ASC`를 가져와서 `ApplyGameplayEffectToSelf` 함수 중 하나를 사용해야 합니다.

`Duration`이나 `Infinite` `GameplayEffects`가 `ASC`에 적용될 때 델리게이트를 바인딩하여 감지할 수 있습니다:
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
콜백 함수:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

서버는 리플리케이션 모드와 관계없이 항상 이 함수를 호출합니다. 자율 프록시(Autonomous proxy)는 `Full`과 `Mixed` 리플리케이션 모드에서 복제된 `GameplayEffects`에 대해서만 이를 호출합니다. 시뮬레이트된 프록시(Simulated proxy)는 `Full` [리플리케이션 모드](#concepts-asc-rm)에서만 이를 호출합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Gameplay Effect 제거
`GameplayEffects`는 [`GameplayAbilities`](#concepts-ga)의 함수들과 `ASC`의 함수들을 통해 여러 가지 방법으로 제거될 수 있으며, 보통 `RemoveActiveGameplayEffect` 형태를 가집니다. 서로 다른 함수들은 본질적으로 결국 대상의 `FActiveGameplayEffectsContainer::RemoveActiveEffects()`를 호출하는 편의 함수입니다.

`GameplayAbility` 외부에서 `GameplayEffects`를 제거하려면, 대상의 `ASC`를 가져와서 `RemoveActiveGameplayEffect` 함수 중 하나를 사용해야 합니다.

`Duration`이나 `Infinite` `GameplayEffects`가 `ASC`에서 제거될 때 델리게이트를 바인딩하여 감지할 수 있습니다.
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
콜백 함수:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

서버는 리플리케이션 모드와 관계없이 항상 이 함수를 호출합니다. 자율 프록시(Autonomous proxy)는 `Full`과 `Mixed` 리플리케이션 모드에서 복제된 `GameplayEffects`에 대해서만 이를 호출합니다. 시뮬레이트된 프록시(Simulated proxy)는 `Full` [리플리케이션 모드](#concepts-asc-rm)에서만 이를 호출합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect 모디파이어
모디파이어는 `Attribute`를 변경하며, [예측적](#concepts-p)으로 `Attribute`를 변경할 수 있는 유일한 방법입니다. 하나의 `GameplayEffect`는 0개 또는 여러 개의 모디파이어를 가질 수 있습니다. 각 모디파이어는 지정된 연산을 통해 하나의 `Attribute`만 변경하는 책임을 가집니다.

| 연산 | 설명 |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add` | 결과를 모디파이어의 지정된 `Attribute`에 더합니다. 뺄셈을 위해서는 음수 값을 사용하세요. |
| `Multiply` | 결과를 모디파이어의 지정된 `Attribute`에 곱합니다. |
| `Divide` | 결과로 모디파이어의 지정된 `Attribute`를 나눕니다. |
| `Override` | 모디파이어의 지정된 `Attribute`를 결과로 덮어씁니다. |

`Attribute`의 `CurrentValue`는 모든 모디파이어의 집계 결과를 `BaseValue`에 더한 것입니다. 모디파이어가 집계되는 방식의 공식은 `GameplayEffectAggregator.cpp`의 `FAggregatorModChannel::EvaluateWithBase`에 다음과 같이 정의되어 있습니다.
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

모든 `Override` 모디파이어는 마지막으로 적용된 모디파이어가 우선권을 가지고 최종 값을 덮어씁니다.

**참고:** 퍼센트 기반 변경의 경우, 덧셈 이후에 발생하도록 `Multiply` 연산을 사용하세요.

**참고:** [예측(Prediction)](#concepts-p)은 퍼센트 변경에 문제가 있습니다.

모디파이어에는 네 가지 유형이 있습니다: Scalable Float, Attribute Based, Custom Calculation Class, Set By Caller. 이들은 모두 지정된 연산을 기반으로 모디파이어의 지정된 `Attribute`를 변경하는 데 사용되는 float 값을 생성합니다.

| 모디파이어 유형 | 설명 |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float` | `FScalableFloats`는 변수를 행으로, 레벨을 열로 가지는 데이터 테이블을 가리킬 수 있는 구조체입니다. Scalable Floats는 어빌리티의 현재 레벨(또는 [`GameplayEffectSpec`](#concepts-ge-spec)에서 재정의된 경우 다른 레벨)에서 지정된 테이블 행의 값을 자동으로 읽습니다. 이 값은 계수로 더 조작될 수 있습니다. 데이터 테이블/행이 지정되지 않으면, 값을 1로 처리하므로 계수를 사용하여 모든 레벨에서 단일 값을 하드코딩할 수 있습니다. ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png) |
| `Attribute Based` | `Attribute Based` 모디파이어는 `Source`(누가 `GameplayEffectSpec`을 생성했는지)나 `Target`(누가 `GameplayEffectSpec`을 받았는지)의 백업 `Attribute`의 `CurrentValue` 또는 `BaseValue`를 가져와서 계수와 계수 전후 추가로 더 수정합니다. `Snapshotting`은 `GameplayEffectSpec`이 생성될 때 백업 `Attribute`를 캡처하는 것을 의미하며, 스냅샷을 찍지 않는다는 것은 `GameplayEffectSpec`이 적용될 때 `Attribute`를 캡처한다는 것을 의미합니다. |
| `Custom Calculation Class` | `Custom Calculation Class`는 복잡한 모디파이어를 위한 가장 큰 유연성을 제공합니다. 이 모디파이어는 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) 클래스를 사용하고 결과 float 값을 계수와 계수 전후 추가로 더 조작할 수 있습니다. |
| `Set By Caller` | `SetByCaller` 모디파이어는 런타임에 `GameplayEffect` 외부에서 어빌리티나 `GameplayEffectSpec`을 만든 사람에 의해 `GameplayEffectSpec`에 설정되는 값입니다. 예를 들어, 플레이어가 버튼을 얼마나 오래 눌러서 어빌리티를 충전했는지에 따라 데미지를 설정하려면 `SetByCaller`를 사용할 것입니다. `SetByCallers`는 본질적으로 `GameplayEffectSpec`에 존재하는 `TMap<FGameplayTag, float>`입니다. 모디파이어는 단지 `Aggregator`에게 제공된 `GameplayTag`와 연관된 `SetByCaller` 값을 찾도록 지시하는 것입니다. 모디파이어가 사용하는 `SetByCallers`는 개념의 `GameplayTag` 버전만 사용할 수 있습니다. `FName` 버전은 여기서 비활성화됩니다. 모디파이어가 `SetByCaller`로 설정되었지만 올바른 `GameplayTag`가 있는 `SetByCaller`가 `GameplayEffectSpec`에 존재하지 않으면, 게임은 런타임 에러를 발생시키고 0 값을 반환합니다. 이는 `Divide` 연산의 경우 문제를 일으킬 수 있습니다. `SetByCallers` 사용 방법에 대한 자세한 정보는 [`SetByCallers`](#concepts-ge-spec-setbycaller)를 참조하세요. |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 곱셈과 나눗셈 모디파이어
기본적으로, 모든 `Multiply`와 `Divide` 모디파이어는 `Attribute`의 `BaseValue`에 곱하거나 나누기 전에 함께 더해집니다.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*`GameplayEffectAggregator.cpp`에서*

이 공식에서 `Multiply`와 `Divide` 모디파이어 모두 `1`의 `Bias` 값을 가집니다(`Addition`은 `0`의 `Bias`를 가짐). 따라서 다음과 같은 형태가 됩니다:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

이 공식은 몇 가지 예상치 못한 결과를 초래합니다. 첫째로, 이 공식은 모든 모디파이어를 `BaseValue`에 곱하거나 나누기 전에 함께 더합니다. 대부분의 사람들은 이들이 함께 곱해지거나 나눠질 것으로 예상할 것입니다. 예를 들어, 두 개의 `1.5` `Multiply` 모디파이어가 있다면, 대부분의 사람들은 `BaseValue`가 `1.5 x 1.5 = 2.25`로 곱해질 것을 예상할 것입니다. 대신, 이는 `1.5`들을 함께 더해서 `BaseValue`에 `2`를 곱합니다(`50% 증가 + 또 다른 50% 증가 = 100% 증가`). 이는 `GameplayPrediction.h`의 예시에서 `500` 기본 속도에 대한 `10%` 속도 버프가 `550`이 될 것이라는 예시였습니다. 또 다른 `10%` 속도 버프를 추가하면 `600`이 될 것입니다.

둘째로, 이 공식은 Paragon을 염두에 두고 설계되었기 때문에 사용할 수 있는 값에 대한 몇 가지 문서화되지 않은 규칙이 있습니다.

곱셈 덧셈 공식에 대한 `Multiply`와 `Divide` 규칙:
* `(1보다 작은 값은 하나만) 그리고 ([1, 2) 범위의 값은 몇 개든)`
* `또는 (2 이상의 값 하나)`

공식의 `Bias`는 기본적으로 `[1, 2)` 범위의 숫자에서 정수 자리를 뺍니다. 첫 번째 모디파이어의 `Bias`는 시작 `Sum` 값(루프 전에 `Bias`로 설정됨)에서 빼지기 때문에 어떤 값이든 단독으로는 작동하고 `1`보다 작은 하나의 값이 `[1, 2)` 범위의 숫자들과 함께 작동하는 이유입니다.

`Multiply`를 사용한 몇 가지 예시:  
곱셈값: `0.5`  
`1 + (0.5 - 1) = 0.5`, 정확함

곱셈값: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, 부정확함 `1`을 예상했나요? 곱셈값을 더할 때는 `1`보다 작은 여러 값은 의미가 없습니다. Paragon은 [가장 큰 음수 값만을 `Multiply` 모디파이어로 사용](#cae-nonstackingge)하도록 설계되어서 `BaseValue`에 곱해지는 `1`보다 작은 값은 최대 하나만 있을 수 있었습니다.

곱셈값: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, 정확함

곱셈값: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`, 부정확함 `10`을 예상했나요. 항상 `모디파이어의 합 - 모디파이어 개수 + 1`이 될 것입니다.

많은 게임들은 `Multiply`와 `Divide` 모디파이어가 `BaseValue`에 적용되기 전에 함께 곱해지고 나눠지기를 원할 것입니다. 이를 달성하려면, `FAggregatorModChannel::EvaluateWithBase()` 엔진 코드를 **변경**해야 합니다.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 모디파이어의 게임플레이 태그

각 [모디파이어](#concepts-ge-mods)에 대해 `SourceTags`와 `TargetTags`를 설정할 수 있습니다. 이들은 `GameplayEffect`의 [`Application Tag requirements`](#concepts-ge-tags)와 같은 방식으로 작동합니다. 따라서 태그는 이펙트가 적용될 때만 고려됩니다. 즉, 주기적이고 무한한 이펙트가 있을 때, 이들은 이펙트의 첫 적용 시에만 고려되고 각 주기적 실행 시에는 *고려되지 않습니다*.

`Attribute Based` 모디파이어는 `SourceTagFilter`와 `TargetTagFilter`도 설정할 수 있습니다. `Attribute Based` 모디파이어의 출처가 되는 attribute의 크기를 결정할 때, 이 필터들은 해당 attribute에 대한 특정 모디파이어를 제외하는 데 사용됩니다. source나 target이 필터의 모든 태그를 가지고 있지 않은 모디파이어는 제외됩니다.

자세히 말하면: ASC의 Source와 Target의 태그는 `GameplayEffects`에 의해 캡처됩니다. Source ASC 태그는 `GameplayEffectSpec`이 생성될 때 캡처되고, Target ASC 태그는 이펙트 실행 시에 캡처됩니다. 무한(Infinite) 또는 지속(Duration) 이펙트의 모디파이어가 적용될 자격이 있는지(즉, 그것의 Aggregator가 자격이 있는지) 결정할 때 이러한 필터가 설정되어 있다면, 캡처된 태그들이 필터와 비교됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 Gameplay Effect 스택킹
`GameplayEffect`는 기본적으로 적용 시 이전에 존재하는 `GameplayEffectSpec` 인스턴스에 대해 알지 못하거나 신경 쓰지 않는 새로운 `GameplayEffectSpec` 인스턴스를 적용합니다. `GameplayEffect`는 스택 가능하도록 설정할 수 있으며, 이 경우 새로운 `GameplayEffectSpec` 인스턴스가 추가되는 대신 현재 존재하는 `GameplayEffectSpec`의 스택 카운트가 변경됩니다. 스택킹은 `Duration`과 `Infinite` `GameplayEffect`에서만 작동합니다.

스택킹에는 Source 기준 집계(Aggregate by Source)와 Target 기준 집계(Aggregate by Target)의 두 가지 유형이 있습니다.

| 스택킹 유형 | 설명 |
| --- | --- |
| Source 기준 집계 (Aggregate by Source) | Target에 Source `ASC` 별로 별도의 스택 인스턴스가 있습니다. 각 Source는 X개의 스택을 적용할 수 있습니다. |
| Target 기준 집계 (Aggregate by Target) | Source에 관계없이 Target에 하나의 스택 인스턴스만 존재합니다. 각 Source는 공유된 스택 제한까지 스택을 적용할 수 있습니다. |

스택에는 또한 만료, 지속시간 갱신, 주기 재설정에 대한 정책이 있습니다. 이러한 정책들은 `GameplayEffect` 블루프린트에서 도움말 툴팁으로 확인할 수 있습니다.

샘플 프로젝트에는 `GameplayEffect` 스택 변경을 감지하는 커스텀 블루프린트 노드가 포함되어 있습니다. HUD UMG 위젯은 이를 사용하여 플레이어가 가진 패시브 방어구 스택의 수를 업데이트합니다. 이 `AsyncTask`는 수동으로 `EndTask()`가 호출될 때까지 영구적으로 존재하며, 우리는 UMG 위젯의 `Destruct` 이벤트에서 이를 수행합니다. `AsyncTaskEffectStackChanged.h/cpp`를 참조하세요.

![GameplayEffect 스택 변경 감지 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 부여된 어빌리티(Granted Abilities)
`GameplayEffect`는 `ASC`에 새로운 [`GameplayAbility`](#concepts-ga)를 부여할 수 있습니다. `Duration`과 `Infinite` `GameplayEffect`만이 어빌리티를 부여할 수 있습니다.

이의 일반적인 사용 사례는 넉백이나 당김 효과로 다른 플레이어를 강제로 이동시키고 싶을 때입니다. 이런 경우 원하는 동작을 수행하는 자동 활성화 어빌리티를 부여하는 `GameplayEffect`를 대상에게 적용합니다(어빌리티가 부여될 때 자동으로 활성화하는 방법은 [패시브 어빌리티](#concepts-ga-activating-passive) 참조).

디자이너는 `GameplayEffect`가 어떤 어빌리티를 부여할지, 어떤 레벨에서 부여할지, 어떤 [입력에 바인딩](#concepts-ga-input)할지, 그리고 부여된 어빌리티의 제거 정책을 선택할 수 있습니다.

| 제거 정책 | 설명 |
| --- | --- |
| 즉시 어빌리티 취소 (Cancel Ability Immediately) | Target에서 부여한 `GameplayEffect`가 제거되면 부여된 어빌리티가 즉시 취소되고 제거됩니다. |
| 종료 시 어빌리티 제거 (Remove Ability on End) | 부여된 어빌리티가 완료될 때까지 허용된 후 Target에서 제거됩니다. |
| 아무 동작 안 함 (Do Nothing) | 부여된 어빌리티는 부여한 `GameplayEffect`가 Target에서 제거되어도 영향을 받지 않습니다. Target은 나중에 수동으로 제거될 때까지 해당 어빌리티를 영구적으로 보유합니다. |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect 태그
`GameplayEffect`는 여러 [`GameplayTagContainer`](#concepts-gt)를 가지고 있습니다. 디자이너는 각 카테고리의 `Added`와 `Removed` `GameplayTagContainer`를 편집하며, 컴파일 시 결과가 `Combined` `GameplayTagContainer`에 표시됩니다. `Added` 태그는 이 `GameplayEffect`가 추가하는 새로운 태그이며 부모가 이전에 가지고 있지 않았던 것입니다. `Removed` 태그는 부모 클래스가 가지고 있지만 이 서브 클래스는 가지고 있지 않은 태그입니다.

| 카테고리 | 설명 |
| --- | --- |
| Gameplay Effect Asset Tags | `GameplayEffect`가 가진 태그입니다. 이들은 자체적으로는 어떤 기능도 수행하지 않으며 `GameplayEffect`를 설명하는 용도로만 사용됩니다. |
| 부여된 태그 (Granted Tags) | `GameplayEffect`에 존재하면서 `GameplayEffect`가 적용된 `ASC`에도 부여되는 태그입니다. `GameplayEffect`가 제거되면 `ASC`에서도 제거됩니다. 이는 `Duration`과 `Infinite` `GameplayEffect`에서만 작동합니다. |
| 지속 태그 요구사항 (Ongoing Tag Requirements) | 적용된 후, 이 태그들은 `GameplayEffect`가 켜져 있는지 꺼져 있는지를 결정합니다. `GameplayEffect`는 적용된 상태에서도 꺼져 있을 수 있습니다. `GameplayEffect`가 지속 태그 요구사항을 충족하지 못해 꺼져 있다가 요구사항이 충족되면, `GameplayEffect`는 다시 켜지고 모디파이어를 재적용합니다. 이는 `Duration`과 `Infinite` `GameplayEffect`에서만 작동합니다. |
| 적용 태그 요구사항 (Application Tag Requirements) | Target에 있는 태그로, `GameplayEffect`가 Target에 적용될 수 있는지를 결정합니다. 이러한 요구사항이 충족되지 않으면 `GameplayEffect`가 적용되지 않습니다. |
| 태그가 있는 GameplayEffect 제거 (Remove Gameplay Effects with Tags) | 이 `GameplayEffect`가 성공적으로 적용되면, Target에 있는 `GameplayEffect` 중 `Asset 태그`나 `부여된 태그`에 이러한 태그가 있는 것들이 Target에서 제거됩니다. |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 면역 (Immunity)
`GameplayEffect`는 [`GameplayTag`](#concepts-gt)를 기반으로 다른 `GameplayEffect`의 적용을 효과적으로 차단하는 면역을 부여할 수 있습니다. `적용 태그 요구사항`과 같은 다른 방법으로도 면역을 구현할 수 있지만, 이 시스템을 사용하면 면역으로 인해 `GameplayEffect`가 차단될 때 델리게이트(`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`)를 제공받을 수 있습니다.

`GrantedApplicationImmunityTags`는 Source `ASC`(어빌리티가 있는 경우 Source 어빌리티의 `AbilityTags`의 태그 포함)가 지정된 태그를 가지고 있는지 확인합니다. 이는 태그를 기반으로 특정 캐릭터나 Source로부터 오는 모든 `GameplayEffect`에 대한 면역을 제공하는 방법입니다.

`Granted Application Immunity Query`는 들어오는 `GameplayEffectSpec`이 쿼리와 일치하는지 확인하여 적용을 차단하거나 허용합니다.

이 쿼리들에 대한 설명은 `GameplayEffect` 블루프린트에서 도움말 툴팁으로 확인할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`)은 `GameplayEffect`의 인스턴스화로 생각할 수 있습니다. 이는 자신이 나타내는 `GameplayEffect` 클래스에 대한 참조, 생성된 레벨, 생성자를 보유합니다. 런타임 이전에 디자이너가 생성해야 하는 `GameplayEffect`와 달리, 이들은 런타임에 자유롭게 생성하고 수정할 수 있습니다. `GameplayEffect`를 적용할 때, `GameplayEffectSpec`이 `GameplayEffect`로부터 생성되어 실제로 Target에 적용됩니다.

`GameplayEffectSpec`은 `UAbilitySystemComponent::MakeOutgoingSpec()`를 사용하여 `GameplayEffect`로부터 생성되며, 이는 `BlueprintCallable`입니다. `GameplayEffectSpec`은 즉시 적용될 필요가 없습니다. 어빌리티에서 생성된 발사체에 `GameplayEffectSpec`을 전달하여 나중에 발사체가 맞춘 Target에 적용하는 것이 일반적입니다. `GameplayEffectSpec`이 성공적으로 적용되면, `FActiveGameplayEffect`라는 새로운 구조체를 반환합니다.

주요 `GameplayEffectSpec` 내용:
* 이 `GameplayEffect`가 생성된 `GameplayEffect` 클래스
* 이 `GameplayEffectSpec`의 레벨. 일반적으로 `GameplayEffectSpec`을 생성한 어빌리티의 레벨과 동일하지만 다를 수 있습니다.
* `GameplayEffectSpec`의 지속시간. 기본값은 `GameplayEffect`의 지속시간이지만 다를 수 있습니다.
* 주기적 효과를 위한 `GameplayEffectSpec`의 주기. 기본값은 `GameplayEffect`의 주기이지만 다를 수 있습니다.
* 이 `GameplayEffectSpec`의 현재 스택 카운트. 스택 제한은 `GameplayEffect`에 있습니다.
* [`GameplayEffectContextHandle`](#concepts-ge-context)은 이 `GameplayEffectSpec`을 누가 생성했는지 알려줍니다.
* 스냅샷으로 인해 `GameplayEffectSpec` 생성 시점에 캡처된 Attribute들
* Target에 부여하는 `DynamicGrantedTags`. `GameplayEffect`가 부여하는 `GameplayTags`에 추가됩니다.
* `GameplayEffect`가 가진 `AssetTags`에 추가되는 `DynamicAssetTags`
* `SetByCaller` `TMap`들

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCaller`는 `GameplayEffectSpec`이 `GameplayTag` 또는 `FName`과 연관된 float 값을 전달할 수 있게 합니다. 이들은 각각의 `TMap`에 저장됩니다: `GameplayEffectSpec`의 `TMap<FGameplayTag, float>`와 `TMap<FName, float>`입니다. 이들은 `GameplayEffect`의 모디파이어로 사용되거나 float 값을 전달하는 일반적인 수단으로 사용될 수 있습니다. 어빌리티 내에서 생성된 수치 데이터를 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec)이나 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc)에 `SetByCaller`를 통해 전달하는 것이 일반적입니다.

| `SetByCaller` 사용 | 참고사항 |
| --- | --- |
| `Modifier` | `GameplayEffect` 클래스에서 미리 정의되어야 합니다. `GameplayTag` 버전만 사용할 수 있습니다. `GameplayEffect` 클래스에 정의되어 있지만 `GameplayEffectSpec`에 해당하는 태그와 float 값 쌍이 없는 경우, `GameplayEffectSpec` 적용 시 런타임 오류가 발생하고 0을 반환합니다. 이는 `나누기` 연산에서 잠재적인 문제가 될 수 있습니다. [`모디파이어`](#concepts-ge-mods)를 참조하세요. |
| 다른 곳 | 어디에서도 미리 정의할 필요가 없습니다. `GameplayEffectSpec`에 존재하지 않는 `SetByCaller`를 읽을 때 개발자가 정의한 기본값을 선택적 경고와 함께 반환할 수 있습니다. |

블루프린트에서 `SetByCaller` 값을 할당하려면 필요한 버전(`GameplayTag` 또는 `FName`)의 블루프린트 노드를 사용하세요.

![SetByCaller 할당](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

블루프린트에서 `SetByCaller` 값을 읽으려면 블루프린트 라이브러리에 커스텀 노드를 만들어야 합니다.

C++에서 `SetByCaller` 값을 할당하려면 필요한 버전(`GameplayTag` 또는 `FName`)의 함수를 사용하세요.

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

C++에서 `SetByCaller` 값을 읽으려면 필요한 버전(`GameplayTag` 또는 `FName`)의 함수를 사용하세요.

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

블루프린트에서 철자 오류를 방지할 수 있으므로 `FName` 버전보다 `GameplayTag` 버전을 사용하는 것을 추천합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 구조체는 `GameplayEffectSpec`의 발동자(instigator)와 [`TargetData`](#concepts-targeting-data)에 대한 정보를 보유합니다. 이는 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc), [`GameplayEffectExecutionCalculation`](#concepts-ge-ec), [`AttributeSet`](#concepts-as), [`GameplayCue`](#concepts-gc) 등의 사이에서 임의의 데이터를 전달하기 위해 상속하기 좋은 구조체입니다.

`GameplayEffectContext`를 상속하는 방법:

1. `FGameplayEffectContext` 상속
1. `FGameplayEffectContext::GetScriptStruct()` 오버라이드
1. `FGameplayEffectContext::Duplicate()` 오버라이드
1. 새로운 데이터가 리플리케이션되어야 하는 경우 `FGameplayEffectContext::NetSerialize()` 오버라이드
1. 부모 구조체 `FGameplayEffectContext`처럼 상속한 클래스에 대해 `TStructOpsTypeTraits` 구현
1. [`AbilitySystemGlobals`](#concepts-asg) 클래스에서 `AllocGameplayEffectContext()`를 오버라이드하여 상속한 클래스의 새 객체를 반환하도록 함

[GASShooter](https://github.com/tranek/GASShooter)는 `GameplayEffectContext`를 상속하여 `TargetData`를 추가했으며, 이는 `GameplayCue`에서 접근할 수 있습니다. 특히 산탄총처럼 여러 적을 동시에 맞출 수 있는 경우에 사용됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculation`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` 또는 `MMC`)은 `GameplayEffect`의 [`Modifier`](#concepts-ge-mods)로 사용되는 강력한 클래스입니다. 이들은 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec)과 유사하게 작동하지만 덜 강력하며, 가장 중요한 점은 [예측](#concepts-p)이 가능하다는 것입니다. 이들의 유일한 목적은 `CalculateBaseMagnitude_Implementation()`에서 float 값을 반환하는 것입니다. 블루프린트와 C++에서 이 함수를 상속하고 오버라이드할 수 있습니다.

`MMC`는 모든 지속시간 유형의 `GameplayEffect`에서 사용할 수 있습니다 - `Instant`, `Duration`, `Infinite`, `Periodic`.

`MMC`의 강점은 `GameplayEffect`의 `Source`나 `Target`의 어떤 Attribute든 그 값을 캡처할 수 있고, `GameplayEffectSpec`에 완전히 접근하여 `GameplayTag`와 `SetByCaller`를 읽을 수 있다는 점입니다. Attribute는 스냅샷되거나 되지 않을 수 있습니다. 스냅샷된 Attribute는 `GameplayEffectSpec`이 생성될 때 캡처되는 반면, 스냅샷되지 않은 Attribute는 `GameplayEffectSpec`이 적용될 때 캡처되며 `Infinite`와 `Duration` `GameplayEffect`에서 Attribute가 변경될 때 자동으로 업데이트됩니다. Attribute를 캡처하면 `ASC`의 기존 모드에서 `CurrentValue`를 재계산합니다. 이 재계산은 AbilitySet의 [`PreAttributeChange()`](#concepts-as-preattributechange)를 실행하지 **않으므로** 여기서 다시 클램핑을 해야 합니다.

| 스냅샷 | Source 또는 Target | `GameplayEffectSpec` 캡처 시점 | `Infinite` 또는 `Duration` `GE`에서 Attribute 변경 시 자동 업데이트 |
| --- | --- | --- | --- |
| 예 | Source | 생성 | 아니오 |
| 예 | Target | 적용 | 아니오 |
| 아니오 | Source | 적용 | 예 |
| 아니오 | Target | 적용 | 예 |

`MMC`에서 나온 결과 float 값은 `GameplayEffect`의 `Modifier`에서 계수와 계수 적용 전후의 덧셈으로 추가 수정될 수 있습니다.

다음은 `Target`의 마나 Attribute를 캡처하여 독 효과로 감소시키는 `MMC` 예제입니다. 감소량은 `Target`의 마나량과 `Target`이 가질 수 있는 태그에 따라 달라집니다.
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//헤더에 정의된 ManaDef FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//헤더에 정의된 MaxManaDef FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Source와 Target의 태그를 수집하여 어떤 버프를 사용할지 결정
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // 0으로 나누기 방지

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// 대상의 마나가 절반 이상이면 효과를 2배로
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// 대상이 PoisonMana에 약하면 효과를 2배로
		Reduction *= 2;
	}
	
	return Reduction;
}
```

`MMC`의 생성자에서 `FGameplayEffectAttributeCaptureDefinition`을 `RelevantAttributesToCapture`에 추가하지 않고 Attribute를 캡처하려고 하면, 캡처 중에 누락된 Spec에 대한 오류가 발생합니다. Attribute를 캡처할 필요가 없다면 `RelevantAttributesToCapture`에 아무것도 추가하지 않아도 됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculation`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution` (플러그인 소스 코드에서 자주 볼 수 있는 용어), 또는 `ExecCalc`)은 `GameplayEffect`가 `ASC`를 변경할 수 있는 가장 강력한 방법입니다. [`ModifierMagnitudeCalculation`](#concepts-ge-mmc)처럼 Attribute를 캡처하고 선택적으로 스냅샷할 수 있습니다. `MMC`와 달리 여러 Attribute를 변경할 수 있고 프로그래머가 원하는 어떤 작업이든 수행할 수 있습니다. 이러한 유연성과 강력함의 단점은 [예측](#concepts-p)이 불가능하고 반드시 C++로 구현해야 한다는 것입니다.

`ExecutionCalculation`은 `Instant`와 `Periodic` `GameplayEffect`에서만 사용할 수 있습니다. 'Execute'라는 단어가 포함된 것은 일반적으로 이 두 가지 유형의 `GameplayEffect`를 의미합니다.

스냅샷은 `GameplayEffectSpec` 생성 시 Attribute를 캡처하는 반면, 스냅샷하지 않으면 `GameplayEffectSpec` 적용 시 Attribute를 캡처합니다. Attribute를 캡처하면 `ASC`의 기존 모드에서 `CurrentValue`를 재계산합니다. 이 재계산은 AbilitySet의 [`PreAttributeChange()`](#concepts-as-preattributechange)를 실행하지 **않으므로** 여기서 다시 클램핑을 해야 합니다.

| 스냅샷 | Source 또는 Target | `GameplayEffectSpec` 캡처 시점 |
| --- | --- | --- |
| 예 | Source | 생성 |
| 예 | Target | 적용 |
| 아니오 | Source | 적용 |
| 아니오 | Target | 적용 |

Attribute 캡처를 설정하기 위해, Epic의 ActionRPG 샘플 프로젝트가 제시한 패턴을 따릅니다. Attribute 캡처 방법을 보유하고 정의하는 구조체를 정의하고 구조체의 생성자에서 하나의 복사본을 생성합니다. 각 `ExecCalc`마다 이러한 구조체가 있을 것입니다. **주의:** 각 구조체는 동일한 네임스페이스를 공유하므로 고유한 이름이 필요합니다. 구조체에 동일한 이름을 사용하면 Attribute 캡처 시 잘못된 동작이 발생합니다(주로 잘못된 Attribute의 값을 캡처함).

`Local Predicted`, `Server Only`, `Server Initiated` [`GameplayAbility`](#concepts-ga)의 경우, `ExecCalc`는 서버에서만 호출됩니다.

`Source`와 `Target`의 여러 Attribute를 읽는 복잡한 공식을 기반으로 받은 데미지를 계산하는 것이 `ExecCalc`의 가장 일반적인 예입니다. 포함된 샘플 프로젝트에는 `GameplayEffectSpec`의 [`SetByCaller`](#concepts-ge-spec-setbycaller)에서 데미지 값을 읽고 `Target`에서 캡처한 방어구 Attribute를 기반으로 해당 값을 감소시키는 간단한 `ExecCalc`가 있습니다. `GDDamageExecCalculation.cpp/.h`를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 Execution Calculation에 데이터 전송하기
`Attribute`를 캡처하는 것 외에도 `ExecutionCalculation`에 데이터를 전송하는 몇 가지 방법이 있습니다.

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
[`GameplayEffectSpec`에 설정된 모든 `SetByCaller`](#concepts-ge-spec-setbycaller)는 `ExecutionCalculation`에서 직접 읽을 수 있습니다.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier
`GameplayEffect`에 값을 하드코딩하고 싶다면, 캡처된 Attribute 중 하나를 백업 데이터로 사용하는 `CalculationModifier`를 통해 전달할 수 있습니다.

이 스크린샷 예제에서는 캡처된 Damage `Attribute`에 50을 더하고 있습니다. 이를 `Override`로 설정하여 하드코딩된 값만 사용하도록 할 수도 있습니다.

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation`은 `Attribute`를 캡처할 때 이 값을 읽습니다.

```c++
float Damage = 0.0f;
// ExecutionCalculation 아래의 CalculationModifier로 설정된 선택적 데미지 값을 캡처
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier
`GameplayEffect`에 값을 하드코딩하고 싶다면, C++에서 'Transient Aggregator'라고 불리는 `Temporary Variable`을 사용하는 `CalculationModifier`를 통해 전달할 수 있습니다. `Temporary Variable`은 `GameplayTag`와 연관됩니다.

이 스크린샷 예제에서는 `Data.Damage` `GameplayTag`를 사용하는 `Temporary Variable`에 50을 더하고 있습니다.

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

`ExecutionCalculation`의 생성자에 백업 `Temporary Variables`를 추가합니다.

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`은 `Attribute` 캡처 함수와 유사한 특별한 캡처 함수를 사용하여 이 값을 읽습니다.

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context
[`GameplayEffectSpec`의 커스텀 `GameplayEffectContext`](#concepts-ge-context)를 통해 `ExecutionCalculation`에 데이터를 전송할 수 있습니다.

`ExecutionCalculation`에서 `FGameplayEffectCustomExecutionParameters`로부터 `EffectContext`에 접근할 수 있습니다.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

`GameplayEffectSpec`이나 `EffectContext`를 변경해야 하는 경우:

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

`ExecutionCalculation`에서 `GameplayEffectSpec`을 수정할 때는 주의해야 합니다. `GetOwningSpecForPreExecuteMod()`의 주석을 참고하세요.

```c++
/** 비상수(Non-const) 접근. 특히 Attribute를 캡처한 후 Spec을 수정할 때는 주의하세요.*/
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) 클래스는 디자이너에게 `GameplayEffect`의 단순한 `GameplayTag` 체크를 넘어서 `GameplayEffect`를 적용할 수 있는지에 대한 고급 제어 기능을 제공합니다. 블루프린트에서는 `CanApplyGameplayEffect()`를 오버라이드하고, C++에서는 `CanApplyGameplayEffect_Implementation()`을 오버라이드하여 구현할 수 있습니다.

`CAR` 사용 예시:
* `Target`이 특정 양(Amount)의 Attribute를 가져야 하는 경우
* `Target`이 특정 개수의 `GameplayEffect` 스택을 가져야 하는 경우

`CAR`은 이 `GameplayEffect`의 인스턴스가 이미 `Target`에 있는지 확인하고 새 인스턴스를 적용하는 대신 기존 인스턴스의 [지속시간을 변경](#concepts-ge-duration)하는 것과 같은 더 고급 기능을 수행할 수도 있습니다(`CanApplyGameplayEffect()`에서 false 반환).

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect
[`GameplayAbility`](#concepts-ga)는 어빌리티의 비용으로 사용하도록 특별히 설계된 선택적 `GameplayEffect`를 가집니다. 비용은 `ASC`가 `GameplayAbility`를 활성화하기 위해 필요한 Attribute의 양입니다. `GA`가 `Cost GE`를 감당할 수 없다면 활성화할 수 없습니다. 이 `Cost GE`는 하나 이상의 모디파이어를 가진 `Instant` `GameplayEffect`여야 하며, 이 모디파이어는 Attribute에서 차감합니다. 기본적으로 `Cost GE`는 예측되도록 설계되었으며, 이 기능을 유지하는 것이 권장됩니다. 즉, `ExecutionCalculation`을 사용하지 않습니다. 복잡한 비용 계산에는 `MMC`가 완벽하게 적합하며 권장됩니다.

처음에는 비용이 있는 `GA`마다 고유한 `Cost GE`를 하나씩 가질 것입니다. 더 고급 기술로는 하나의 `Cost GE`를 여러 `GA`에서 재사용하고 `Cost GE`에서 생성된 `GameplayEffectSpec`을 `GA` 특정 데이터로 수정하는 방법이 있습니다(비용 값은 `GA`에 정의됨). **이는 `Instanced` 어빌리티에서만 작동합니다.**

`Cost GE`를 재사용하는 두 가지 기술:

1. **`MMC` 사용하기.** 이것이 가장 쉬운 방법입니다. `GameplayEffectSpec`에서 얻을 수 있는 `GameplayAbility` 인스턴스에서 비용 값을 읽는 [`MMC`](#concepts-ge-mmc)를 생성합니다.

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

이 예제에서 비용 값은 추가한 `GameplayAbility` 자식 클래스의 `FScalableFloat`입니다.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **`UGameplayAbility::GetCostGameplayEffect()` 오버라이드하기.** 이 함수를 오버라이드하고 `GameplayAbility`의 비용 값을 읽는 [런타임에 `GameplayEffect` 생성](#concepts-ge-dynamic)합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect
[`GameplayAbility`](#concepts-ga)는 어빌리티의 쿨다운으로 사용하도록 특별히 설계된 선택적 `GameplayEffect`를 가집니다. 쿨다운은 활성화 후 어빌리티를 다시 활성화할 수 있을 때까지의 시간을 결정합니다. `GA`가 아직 쿨다운 중이라면 활성화할 수 없습니다. 이 `Cooldown GE`는 모디파이어가 없는 `Duration` `GameplayEffect`여야 하며, `GameplayEffect`의 `GrantedTags`("`Cooldown Tag`")에 `GameplayAbility` 별로 또는 어빌리티 슬롯 별로(게임에 쿨다운을 공유하는 슬롯에 할당된 교체 가능한 어빌리티가 있는 경우) 고유한 `GameplayTag`가 있어야 합니다. `GA`는 실제로 `Cooldown GE`의 존재가 아닌 `Cooldown Tag`의 존재를 확인합니다. 기본적으로 `Cooldown GE`는 예측되도록 설계되었으며, 이 기능을 유지하는 것이 권장됩니다. 즉, `ExecutionCalculation`을 사용하지 않습니다. 복잡한 쿨다운 계산에는 `MMC`가 완벽하게 적합하며 권장됩니다.

처음에는 쿨다운이 있는 `GA`마다 고유한 `Cooldown GE`를 하나씩 가질 것입니다. 더 고급 기술로는 하나의 `Cooldown GE`를 여러 `GA`에서 재사용하고 `Cooldown GE`에서 생성된 `GameplayEffectSpec`을 `GA` 특정 데이터로 수정하는 방법이 있습니다(쿨다운 지속시간과 `Cooldown Tag`는 `GA`에 정의됨). **이는 `Instanced` 어빌리티에서만 작동합니다.**

`Cooldown GE`를 재사용하는 두 가지 기술:

1. **[`SetByCaller`](#concepts-ge-spec-setbycaller) 사용하기.** 이것이 가장 쉬운 방법입니다. 공유 `Cooldown GE`의 지속시간을 `GameplayTag`와 함께 `SetByCaller`로 설정합니다. `GameplayAbility` 서브클래스에서 지속시간을 위한 float/`FScalableFloat`, 고유한 `Cooldown Tag`를 위한 `FGameplayTagContainer`, 그리고 우리의 `Cooldown Tag`와 `Cooldown GE`의 태그의 합집합의 반환 포인터로 사용할 임시 `FGameplayTagContainer`를 정의합니다.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags()에서 포인터를 반환할 임시 컨테이너.
// 이는 우리의 CooldownTags와 Cooldown GE의 쿨다운 태그의 합집합이 됩니다.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

그런 다음 `UGameplayAbility::GetCooldownTags()`를 오버라이드하여 우리의 `Cooldown Tag`와 기존 `Cooldown GE` 태그의 합집합을 반환하도록 합니다.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags는 CDO의 TempCooldownTags에 쓰므로 어빌리티 쿨다운 태그가 변경될 경우(다른 슬롯으로 이동) 초기화합니다

	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

마지막으로 `UGameplayAbility::ApplyCooldown()`을 오버라이드하여 우리의 `Cooldown Tag`를 주입하고 쿨다운 `GameplayEffectSpec`에 `SetByCaller`를 추가합니다.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

이 그림에서 쿨다운의 지속시간 `Modifier`는 `Data.Cooldown`이라는 `Data Tag`와 함께 `SetByCaller`로 설정됩니다. `Data.Cooldown`은 위 코드에서 `OurSetByCallerTag`가 됩니다.

![SetByCaller가 있는 Cooldown GE](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **[`MMC`](#concepts-ge-mmc) 사용하기.** 이것은 `Cooldown GE`에 지속시간으로 `SetByCaller`를 설정하는 것과 `ApplyCooldown`의 설정을 제외하고는 위와 동일한 설정을 가집니다. 대신, 지속시간을 `Custom Calculation Class`로 설정하고 우리가 만들 새로운 `MMC`를 가리키도록 합니다.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags()에서 포인터를 반환할 임시 컨테이너.
// 이는 우리의 CooldownTags와 Cooldown GE의 쿨다운 태그의 합집합이 됩니다.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

그런 다음 `UGameplayAbility::GetCooldownTags()`를 오버라이드하여 우리의 `Cooldown Tag`와 기존 `Cooldown GE` 태그의 합집합을 반환하도록 합니다.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags는 CDO의 TempCooldownTags에 쓰므로 어빌리티 쿨다운 태그가 변경될 경우(다른 슬롯으로 이동) 초기화합니다

	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

마지막으로 `UGameplayAbility::ApplyCooldown()`을 오버라이드하여 우리의 `Cooldown Tag`를 쿨다운 `GameplayEffectSpec`에 주입합니다.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![MMC가 있는 Cooldown GE](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Cooldown GameplayEffect의 남은 시간 가져오기
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**참고:** 클라이언트에서 쿨다운의 남은 시간을 조회하려면 복제된 `GameplayEffect`를 받을 수 있어야 합니다. 이는 해당 `ASC`의 [리플리케이션 모드](#concepts-asc-rm)에 따라 달라집니다.

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 쿨다운 시작과 종료 감지하기
쿨다운이 시작될 때를 감지하기 위해서는 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`에 바인딩하여 `Cooldown GE`가 적용될 때를 감지하거나, `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`에 바인딩하여 `Cooldown Tag`가 추가될 때를 감지할 수 있습니다. 적용된 `GameplayEffectSpec`에도 접근할 수 있어서 이 `Cooldown GE`가 로컬에서 예측된 것인지 서버의 수정된 것인지 판단할 수 있기 때문에 `Cooldown GE`가 추가될 때를 감지하는 것을 권장합니다.

쿨다운이 종료될 때를 감지하기 위해서는 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`에 바인딩하여 `Cooldown GE`가 제거될 때를 감지하거나, `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`에 바인딩하여 `Cooldown Tag`가 제거될 때를 감지할 수 있습니다. 서버의 수정된 `Cooldown GE`가 들어오면 로컬에서 예측된 것을 제거하면서 여전히 쿨다운 중임에도 `OnAnyGameplayEffectRemovedDelegate()`가 발생하기 때문에 `Cooldown Tag`가 제거될 때를 감지하는 것을 권장합니다. `Cooldown Tag`는 예측된 `Cooldown GE`의 제거와 서버의 수정된 `Cooldown GE`의 적용 동안 변경되지 않습니다.

**참고:** 클라이언트에서 `GameplayEffect`의 추가나 제거를 감지하려면 복제된 `GameplayEffect`를 받을 수 있어야 합니다. 이는 해당 `ASC`의 [리플리케이션 모드](#concepts-asc-rm)에 따라 달라집니다.

샘플 프로젝트는 쿨다운의 시작과 종료를 감지하는 커스텀 블루프린트 노드를 포함하고 있습니다. HUD UMG 위젯은 이를 사용하여 메테오 스킬의 남은 쿨다운 시간을 업데이트합니다. 이 `AsyncTask`는 수동으로 `EndTask()`가 호출될 때까지 영구적으로 존재하며, UMG 위젯의 `Destruct` 이벤트에서 이를 수행합니다. [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)를 참조하세요.

![쿨다운 변경 감지 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 쿨다운 예측
현재 쿨다운은 실제로 예측할 수 없습니다. 로컬에서 예측된 `Cooldown GE`가 적용될 때 UI 쿨다운 타이머를 시작할 수 있지만, `GameplayAbility`의 실제 쿨다운은 서버의 쿨다운 남은 시간과 연결되어 있습니다. 플레이어의 지연 시간에 따라 로컬에서 예측된 쿨다운은 만료될 수 있지만, 서버의 쿨다운이 만료될 때까지 `GameplayAbility`는 여전히 서버에서 쿨다운 상태이며 즉시 재활성화를 막을 것입니다.

샘플 프로젝트는 로컬에서 예측된 쿨다운이 시작될 때 메테오 어빌리티의 UI 아이콘을 회색으로 만들고, 서버의 수정된 `Cooldown GE`가 들어오면 쿨다운 타이머를 시작하는 방식으로 이를 처리합니다.

이로 인한 게임플레이 영향은 높은 지연 시간을 가진 플레이어들이 짧은 쿨다운 어빌리티에서 낮은 지연 시간을 가진 플레이어들보다 낮은 발사 속도를 가져 불이익을 받는다는 것입니다. Fortnite는 무기에 쿨다운 `GameplayEffect`를 사용하지 않는 커스텀 북키핑을 사용하여 이를 피합니다.

진정한 쿨다운 예측(로컬 쿨다운이 만료되고 서버가 여전히 쿨다운 중일 때 플레이어가 `GameplayAbility`를 활성화할 수 있음)을 허용하는 것은 Epic이 [GAS의 향후 버전](#concepts-p-future)에서 구현하고자 하는 기능입니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 활성화된 GameplayEffect의 지속시간 변경하기
`Cooldown GE`나 다른 `Duration` `GameplayEffect`의 남은 시간을 변경하려면 `GameplayEffectSpec`의 `Duration`을 변경하고, `StartServerWorldTime`, `CachedStartServerWorldTime`, `StartWorldTime`을 업데이트하고, `CheckDuration()`으로 지속시간 체크를 다시 실행해야 합니다. 이를 서버에서 수행하고 `FActiveGameplayEffect`를 더티로 표시하면 변경사항이 클라이언트에 복제됩니다.
**참고:** 이는 `const_cast`를 포함하며 Epic이 의도한 지속시간 변경 방법이 아닐 수 있지만, 지금까지는 잘 작동하는 것 같습니다.

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 런타임에 동적 GameplayEffect 생성하기
런타임에 동적 `GameplayEffect`를 생성하는 것은 고급 주제입니다. 이를 자주 사용할 필요는 없을 것입니다.

C++에서는 `Instant` `GameplayEffect`만 런타임에 처음부터 생성할 수 있습니다. `Duration`과 `Infinite` `GameplayEffect`는 복제될 때 존재하지 않는 `GameplayEffect` 클래스 정의를 찾기 때문에 런타임에 동적으로 생성할 수 없습니다. 이 기능을 구현하려면 대신 에디터에서 일반적으로 하는 것처럼 원형(archetype) `GameplayEffect` 클래스를 만들어야 합니다. 그런 다음 런타임에 필요한 대로 `GameplayEffectSpec` 인스턴스를 커스터마이즈하세요.

런타임에 생성된 `Instant` `GameplayEffect`는 [로컬 예측](#concepts-p) `GameplayAbility` 내에서도 호출될 수 있습니다. 하지만 동적 생성이 부작용을 가질 수 있는지는 아직 알려지지 않았습니다.

##### Examples

샘플 프로젝트는 캐릭터가 `AttributeSet`에서 치명타를 입었을 때 골드와 경험치를 처치자에게 돌려주기 위해 하나를 생성합니다.

```c++
// 현상금을 주기 위한 동적 인스턴트 Gameplay Effect 생성
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

두 번째 예시는 로컬 예측 `GameplayAbility` 내에서 생성된 런타임 `GameplayEffect`를 보여줍니다. 자체 책임 하에 사용하세요(코드의 주석 참조)!

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// 런타임에 GE 생성
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // 런타임 GE에서는 Instant만 작동함

		// MyAttribute를 42로 덮어쓰는 간단한 scalable float 모디파이어를 추가.
		// 실제 애플리케이션에서는 TriggerEventData를 통해 전달된 정보를 사용.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// GE 적용.

		// ASC가 GE 클래스 기본 객체로부터 GESpec을 생성하는 동작을 피하기 위해 여기서 GESpec을 생성.
		// 여기서는 동적 GE를 사용하기 때문에, 기본 GameplayEffect 클래스로 GESpec이 생성되어
		// 우리의 모디파이어들을 잃게 될 것입니다. 주의: 여기서 사용된 이 "편법"이 어떤 단점을 가질 수 있는지는 알 수 없음!
		// GE 객체는 spec의 UPROPERTY이므로 GarbageCollector에 의해 수집되는 것을 방지함.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // 수명이 핸들 내의 shared ptr에 의해 관리되므로 "new" 사용
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect Container
Epic의 [Action RPG 샘플 프로젝트](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)는 `FGameplayEffectContainer`라는 구조체를 구현합니다. 이것들은 기본 GAS에는 없지만 `GameplayEffect`와 [`TargetData`](#concepts-targeting-data)를 담는 데 매우 유용합니다. `GameplayEffect`에서 `GameplayEffectSpec`을 생성하고 `GameplayEffectContext`에 기본값을 설정하는 것과 같은 일부 작업을 자동화합니다. `GameplayAbility`에서 `GameplayEffectContainer`를 만들고 이를 생성된 발사체에 전달하는 것은 매우 쉽고 명확합니다. 포함된 샘플 프로젝트에서는 기본 GAS에서 이것 없이 어떻게 작업하는지 보여주기 위해 `GameplayEffectContainer`를 구현하지 않았지만, 이를 살펴보고 프로젝트에 추가하는 것을 적극 권장합니다.

`SetByCaller`와 같은 작업을 위해 `GameplayEffectContainer` 내의 `GESpec`에 접근하려면, `FGameplayEffectContainer`를 분해하고 `GESpec` 배열의 인덱스로 `GESpec` 참조에 접근하세요. 이는 접근하려는 `GESpec`의 인덱스를 미리 알고 있어야 합니다.

![GameplayEffectContainer와 함께 SetByCaller 사용](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainer`는 또한 [타겟팅](#concepts-targeting-containers)을 위한 선택적이고 효율적인 수단을 포함합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Ability

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability 정의
[`GameplayAbility`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`)는 게임에서 `Actor`가 수행할 수 있는 모든 행동이나 기술입니다. 달리기와 총 쏘기처럼 동시에 여러 `GameplayAbility`가 활성화될 수 있습니다. 이들은 블루프린트나 C++로 만들 수 있습니다.

`GameplayAbility` 예시:
* 점프
* 달리기
* 총 쏘기
* X초마다 공격을 자동으로 막기
* 물약 사용
* 문 열기
* 자원 수집
* 건물 건설

`GameplayAbility`로 구현하지 말아야 할 것들:
* 기본 이동 입력
* UI와의 일부 상호작용 - 상점에서 아이템을 구매하는데 `GameplayAbility`를 사용하지 마세요.

이것들은 규칙이 아닌 단순한 추천사항입니다. 여러분의 디자인과 구현은 다를 수 있습니다.

`GameplayAbility`는 Attribute 변경량을 수정하거나 `GameplayAbility`의 기능을 변경하기 위한 레벨을 가질 수 있는 기본 기능을 제공합니다.

`GameplayAbility`는 [`Net Execution Policy`](#concepts-ga-net)에 따라 소유 클라이언트 및/또는 서버에서 실행되지만 시뮬레이션된 프록시에서는 실행되지 않습니다. `Net Execution Policy`는 `GameplayAbility`가 로컬에서 [예측](#concepts-p)될지 결정합니다. 이들은 [선택적 비용과 쿨다운 `GameplayEffect`](#concepts-ga-commit)에 대한 기본 동작을 포함합니다. `GameplayAbility`는 이벤트 대기, Attribute 변경 대기, 플레이어의 대상 선택 대기, 또는 `Root Motion Source`로 `Character` 이동과 같이 시간이 걸리는 작업을 위해 [`AbilityTask`](#concepts-at)를 사용합니다. **시뮬레이션된 클라이언트는 `GameplayAbility`를 실행하지 않습니다**. 대신, 서버가 어빌리티를 실행할 때 시뮬레이션된 프록시에서 시각적으로 재생되어야 하는 것들(애니메이션 몽타주 등)은 `AbilityTask`나 사운드와 파티클 같은 시각적 효과를 위한 [`GameplayCue`](#concepts-gc)를 통해 복제되거나 RPC됩니다.

모든 `GameplayAbility`는 게임플레이 로직이 포함된 `ActivateAbility()` 함수를 오버라이드해야 합니다. `GameplayAbility`가 완료되거나 취소될 때 실행되는 추가 로직은 `EndAbility()`에 추가할 수 있습니다.

단순한 `GameplayAbility`의 플로우차트:
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

더 복잡한 `GameplayAbility`의 플로우차트:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

복잡한 어빌리티는 서로 상호작용(활성화, 취소 등)하는 여러 `GameplayAbility`를 사용하여 구현할 수 있습니다.

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy
이 옵션을 사용하지 마세요. 이름이 오해의 소지가 있으며 필요하지 않습니다. [`GameplayAbilitySpec`](#concepts-ga-spec)은 기본적으로 서버에서 소유 클라이언트로 복제됩니다. 위에서 언급했듯이, **`GameplayAbility`는 시뮬레이션된 프록시에서 실행되지 않습니다**. 이들은 시뮬레이션된 프록시에 시각적 변경을 복제하거나 RPC하기 위해 `AbilityTask`와 `GameplayCue`를 사용합니다. Epic의 Dave Ratti는 [미래에 이 옵션을 제거하고 싶다고 언급](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)했습니다.

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation
이 옵션은 도움이 되기보다는 문제를 더 자주 일으킵니다. 이는 클라이언트의 `GameplayAbility`가 취소되거나 자연스럽게 완료되면, 서버의 버전이 완료되었는지 여부와 관계없이 강제로 종료된다는 것을 의미합니다. 후자의 문제가 더 중요한데, 특히 높은 지연 시간을 가진 플레이어가 사용하는 로컬 예측 `GameplayAbility`의 경우 그렇습니다. 일반적으로 이 옵션을 비활성화하는 것이 좋습니다.

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly
이 옵션을 설정하면 입력 누름과 해제 이벤트가 항상 서버로 복제됩니다. Epic은 이를 사용하지 말고 대신 [입력이 `ASC`에 바인딩](#concepts-ga-input)되어 있다면 기존 입력 관련 [`AbilityTask`](#concepts-at)에 내장된 `Generic Replicated Events`를 사용하는 것을 권장합니다.

Epic's comment:
```c++
/** 직접 입력 상태 복제. 이것들은 어빌리티의 bReplicateInputDirectly가 true일 때 호출되며 일반적으로 사용하기에 좋지 않습니다. (대신 Generic Replicated Events를 사용하는 것을 선호합니다). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 Binding Input to the ASC
`ASC`는 입력 액션을 직접 바인딩하고 `GameplayAbility`를 부여할 때 이러한 입력을 할당할 수 있습니다. `GameplayAbility`에 할당된 입력 액션은 `GameplayTag` 요구사항이 충족되면 눌렀을 때 자동으로 해당 `GameplayAbility`를 활성화합니다. 할당된 입력 액션은 입력에 응답하는 내장 `AbilityTask`를 사용하는 데 필요합니다.

`GameplayAbility`를 활성화하는 데 할당된 입력 액션 외에도, `ASC`는 일반적인 `Confirm`과 `Cancel` 입력도 받습니다. 이러한 특수 입력은 [`Target Actors`](#concepts-targeting-actors)의 확인이나 취소와 같은 작업을 위해 `AbilityTask`에서 사용됩니다.

`ASC`에 입력을 바인딩하려면, 먼저 입력 액션 이름을 바이트로 변환하는 열거형을 만들어야 합니다. 열거형 이름은 프로젝트 설정의 입력 액션 이름과 정확히 일치해야 합니다. `DisplayName`은 상관없습니다.

샘플 프로젝트의 예시:
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

`ASC`가 `Character`에 있는 경우, `SetupPlayerInputComponent()`에 `ASC`에 바인딩하는 함수를 포함합니다:
```c++
// AbilitySystemComponent에 바인딩
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

`ASC`가 `PlayerState`에 있는 경우, `SetupPlayerInputComponent()`에서 `PlayerState`가 아직 클라이언트에 복제되지 않았을 수 있는 잠재적인 경쟁 조건(Race Condition)이 있습니다. 따라서 `SetupPlayerInputComponent()`와 `OnRep_PlayerState()` 모두에서 입력 바인딩을 시도하는 것을 권장합니다. `OnRep_PlayerState()`만으로는 충분하지 않은데, `PlayerController`가 클라이언트에게 `InputComponent`를 생성하는 `ClientRestart()`를 호출하도록 지시하기 전에 `PlayerState`가 복제되는 경우 `Actor`의 `InputComponent`가 null일 수 있기 때문입니다. 샘플 프로젝트는 두 위치 모두에서 바인딩을 시도하며 bool 값으로 프로세스를 제어하여 실제로 한 번만 바인딩하도록 합니다.

**참고:** 샘플 프로젝트에서 열거형의 `Confirm`과 `Cancel`은 프로젝트 설정의 입력 액션 이름(`ConfirmTarget`과 `CancelTarget`)과 일치하지 않지만, `BindAbilityActivationToInputComponent()`에서 매핑을 제공합니다. 이들은 특별한 경우이므로 매핑을 제공하면 일치하지 않아도 됩니다. 열거형의 다른 모든 입력은 프로젝트 설정의 입력 액션 이름과 반드시 일치해야 합니다.

하나의 입력으로만 활성화될 `GameplayAbility`의 경우(MOBA처럼 항상 같은 "슬롯"에 존재할 경우), `UGameplayAbility` 서브 클래스에 입력을 정의할 수 있는 변수를 추가하는 것을 선호합니다. 그러면 어빌리티를 부여할 때 `ClassDefaultObject`에서 이를 읽을 수 있습니다.

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 어빌리티를 활성화하지 않고 입력에 바인딩하기
입력이 눌렸을 때 `GameplayAbility`가 자동으로 활성화되는 것을 원하지 않지만 여전히 `AbilityTask`와 함께 사용하기 위해 입력에 바인딩하고 싶다면, `UGameplayAbility` 서브클래스에 기본값이 `true`인 새로운 bool 변수 `bActivateOnInput`을 추가하고 `UAbilitySystemComponent::AbilityLocalInputPressed()`를 오버라이드할 수 있습니다.

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// 이 InputID가 GenericConfirm/Cancel로 오버로딩되어 있고 GenericConfirm/Cancel 콜백이 바인딩되어 있다면 입력을 소모함
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// InputPressed 이벤트를 호출. 이 이벤트는 여기서 복제되지 않음. 누군가가 이 이벤트를 수신 중이라면 InputPressed 이벤트를 서버로 복제할 수 있음
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability가 활성화되지 않았으므로 활성화를 시도함
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 어빌리티 부여하기
`ASC`에 `GameplayAbility`를 부여하면 이를 `ASC`의 `ActivatableAbilities` 목록에 추가하여 [`GameplayTag` 요구사항](#concepts-ga-tags)을 충족할 때 언제든지 `GameplayAbility`를 활성화할 수 있게 합니다.

우리는 서버에서 `GameplayAbility`를 부여하며, 이는 자동으로 [`GameplayAbilitySpec`](#concepts-ga-spec)을 소유 클라이언트에 복제합니다. 다른 클라이언트/시뮬레이션된 프록시는 `GameplayAbilitySpec`을 받지 않습니다.

샘플 프로젝트는 게임 시작 시 읽고 부여하는 `TArray<TSubclassOf<UGDGameplayAbility>>`를 `Character` 클래스에 저장합니다:
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// 서버일 때만 어빌리티를 부여함
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

이러한 `GameplayAbility`를 부여할 때, `UGameplayAbility` 클래스, 어빌리티 레벨, 바인딩된 입력, 그리고 이 `GameplayAbility`를 이 `ASC`에 부여한 `SourceObject`로 `GameplayAbilitySpec`을 생성합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 어빌리티 활성화하기
`GameplayAbility`에 입력 액션이 할당되면, 입력이 눌리고 `GameplayTag` 요구사항을 충족할 때 자동으로 활성화됩니다. 이것이 항상 `GameplayAbility`를 활성화하는 바람직한 방법은 아닐 수 있습니다. `ASC`는 `GameplayAbility`를 활성화하는 다른 네 가지 방법을 제공합니다: `GameplayTag`로, `GameplayAbility` 클래스로, `GameplayAbilitySpec` 핸들로, 그리고 이벤트로. 이벤트로 `GameplayAbility`를 활성화하면 [이벤트와 함께 데이터 페이로드를 전달](#concepts-ga-data)할 수 있습니다.

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```

이벤트로 `GameplayAbility`를 활성화하려면, `GameplayAbility`에 `Triggers`가 설정되어 있어야 합니다. `GameplayTag`를 할당하고 `GameplayEvent`에 대한 옵션을 선택하세요. 이벤트를 보내려면 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)` 함수를 사용하세요. 이벤트로 `GameplayAbility`를 활성화하면 데이터가 있는 페이로드를 전달할 수 있습니다.

`GameplayAbility` `Triggers`는 또한 `GameplayTag`가 추가되거나 제거될 때 `GameplayAbility`를 활성화할 수 있게 합니다.

**참고:** 블루프린트에서 이벤트로 `GameplayAbility`를 활성화할 때는 반드시 `ActivateAbilityFromEvent` 노드를 사용해야 합니다.

**참고:** 패시브 어빌리티처럼 항상 실행되는 `GameplayAbility`가 아니라면, `GameplayAbility`가 종료되어야 할 때 `EndAbility()`를 호출하는 것을 잊지 마세요.

**로컬 예측** `GameplayAbility`의 활성화 순서:
1. **소유 클라이언트**가 `TryActivateAbility()` 호출
1. `InternalTryActivateAbility()` 호출
1. `CanActivateAbility()` 호출하고 `GameplayTag` 요구사항 충족 여부, `ASC`가 비용을 감당할 수 있는지, `GameplayAbility`가 쿨다운 중이 아닌지, 다른 인스턴스가 현재 활성화되지 않았는지 반환
1. `CallServerTryActivateAbility()` 호출하고 생성한 `Prediction Key` 전달
1. `CallActivateAbility()` 호출
1. `PreActivate()` 호출 - Epic은 이를 "boilerplate init stuff"라고 부름
1. 마지막으로 어빌리티를 활성화하는 `ActivateAbility()` 호출

**서버**가 `CallServerTryActivateAbility()` 받음:
1. `ServerTryActivateAbility()` 호출
1. `InternalServerTryActivateAbility()` 호출
1. `InternalTryActivateAbility()` 호출
1. `CanActivateAbility()` 호출하고 `GameplayTag` 요구사항 충족 여부, `ASC`가 비용을 감당할 수 있는지, `GameplayAbility`가 쿨다운 중이 아닌지, 다른 인스턴스가 현재 활성화되지 않았는지 반환
1. 성공하면 `ClientActivateAbilitySucceed()`를 호출하여 활성화가 서버에 의해 확인되었음을 알리고 `OnConfirmDelegate` 델리게이트를 브로드캐스트. 이는 입력 확인과는 다릅니다.
1. `CallActivateAbility()` 호출
1. `PreActivate()` 호출 - Epic은 이를 "boilerplate init stuff"라고 부름
1. 마지막으로 어빌리티를 활성화하는 `ActivateAbility()` 호출

서버가 활성화에 실패하면 언제든지 `ClientActivateAbilityFailed()`를 호출하여 클라이언트의 `GameplayAbility`를 즉시 종료하고 예측된 변경사항을 되돌립니다.

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 패시브 어빌리티(Passive Abilities)
자동으로 활성화되어 지속적으로 실행되는 패시브 `GameplayAbility`를 구현하려면, `UGameplayAbility::OnAvatarSet()`을 오버라이드하고 `TryActivateAbility()`를 호출하세요. 이 함수는 `GameplayAbility`가 부여되고 `AvatarActor`가 설정될 때 자동으로 호출됩니다.

커스텀 `UGameplayAbility` 클래스에 `GameplayAbility`가 부여될 때 활성화되어야 하는지를 지정하는 `bool` 값을 추가하는 것을 권장합니다. 샘플 프로젝트에서는 패시브 방어구 스택 어빌리티에 이를 적용하고 있습니다.

패시브 `GameplayAbility`는 일반적으로 [`Net Execution Policy`](#concepts-ga-net)가 `Server Only`로 설정됩니다.

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic은 이 함수를 패시브 어빌리티를 초기화하고 `BeginPlay` 유형의 작업을 수행하는 올바른 위치로 설명하고 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 활성화 실패 태그(Activation Failed Tags)

어빌리티에는 어빌리티 활성화가 실패한 이유를 알려주는 기본 로직이 있습니다. 이를 활용하려면 기본 실패 케이스에 해당하는 GameplayTag를 설정해야 합니다.

프로젝트에 다음 태그들을 추가하세요(또는 자신만의 명명 규칙 사용):
```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

그런 다음 [`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13)에 다음을 추가하세요:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

이제 어빌리티 활성화가 실패할 때마다 해당하는 GameplayTag가 출력 로그 메시지에 포함되거나 `showdebug AbilitySystem` HUD에 표시됩니다.
```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![showdebug AbilitySystem에 표시되는 활성화 실패 태그](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 어빌리티 취소하기(Canceling Abilities)
내부에서 `GameplayAbility`를 취소하려면 `CancelAbility()`를 호출하면 됩니다. 이는 `EndAbility()`를 호출하고 `WasCancelled` 매개변수를 true로 설정합니다.

외부에서 `GameplayAbility`를 취소하기 위해 `ASC`는 다음과 같은 함수들을 제공합니다:

```c++
/** 지정된 어빌리티 CDO를 취소합니다. */
void CancelAbility(UGameplayAbility* Ability);	

/** 전달된 spec 핸들이 가리키는 어빌리티를 취소합니다. 핸들을 재활성화된 어빌리티에서 찾을 수 없으면 아무 일도 일어나지 않습니다. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** 지정된 태그를 가진 모든 어빌리티를 취소합니다. Ignore 인스턴스는 취소하지 않습니다 */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** 태그와 관계없이 모든 어빌리티를 취소합니다. Ignore 인스턴스는 취소하지 않습니다 */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** 모든 어빌리티를 취소하고 남아있는 인스턴스화된 어빌리티들을 제거합니다 */
virtual void DestroyActiveState();
```

**참고:** `Non-Instanced` `GameplayAbility`가 있는 경우 `CancelAllAbilities`가 제대로 작동하지 않는 것으로 확인되었습니다. `Non-Instanced` `GameplayAbility`를 만나면 중단되는 것으로 보입니다. `CancelAbilities`는 `Non-Instanced` `GameplayAbility`를 더 잘 처리할 수 있으며, 샘플 프로젝트에서는 이를 사용합니다(Jump는 non-instanced `GameplayAbility`입니다). 상황에 따라 다를 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 활성화된 어빌리티 가져오기 (Getting Active Abilities)
초보자들은 종종 "어떻게 활성화된 어빌리티를 가져올 수 있나요?"라고 질문합니다. 아마도 변수를 설정하거나 취소하기 위해서일 것입니다. 한 번에 여러 개의 `GameplayAbility`가 활성화될 수 있으므로 하나의 "활성화된 어빌리티"는 없습니다. 대신, `ASC`의 `ActivatableAbilities` 목록(ASC가 소유한 부여된 `GameplayAbility`들)을 검색하여 찾고자 하는 [`Asset` 또는 `Granted` `GameplayTag`](#concepts-ga-tags)와 일치하는 것을 찾아야 합니다.

`UAbilitySystemComponent::GetActivatableAbilities()`는 반복 작업이 가능한 `TArray<FGameplayAbilitySpec>`를 반환합니다.

`ASC`는 또한 수동으로 `GameplayAbilitySpecs` 목록을 반복하는 대신 매개변수로 `GameplayTagContainer`를 받아 검색을 도와주는 헬퍼 함수를 제공합니다. `bOnlyAbilitiesThatSatisfyTagRequirements` 매개변수는 현재 활성화될 수 있는 GameplayTag 요구사항을 만족하는 `GameplayAbilitySpecs`만 반환합니다. 예를 들어, 무기를 든 기본 공격과 맨손 기본 공격, 이렇게 두 개의 `GameplayAbility`가 있을 수 있고, 무기 장착 여부에 따라 GameplayTag 요구사항이 설정되어 올바른 것이 활성화됩니다. 자세한 내용은 함수에 대한 Epic의 주석을 참조하세요.

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

찾고자 하는 `FGameplayAbilitySpec`을 얻으면 이에 대해 `IsActive()`를 호출할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 인스턴싱 정책 (Instancing Policy)
`GameplayAbility`의 `Instancing Policy`는 활성화될 때 `GameplayAbility`가 인스턴스화되는 방식을 결정합니다.

| `Instancing Policy`     | 설명                                                                                           | 사용 예시                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 각 `ASC`는 활성화 사이에 재사용되는 하나의 `GameplayAbility` 인스턴스만 가집니다.             | 가장 많이 사용하게 될 `Instancing Policy`입니다. 모든 어빌리티에 사용할 수 있으며 활성화 사이에 지속성을 제공합니다. 디자이너는 필요한 경우 활성화 사이에 변수를 수동으로 재설정해야 할 책임이 있습니다.                                                                                                                                                               |
| Instanced Per Execution | `GameplayAbility`가 활성화될 때마다 새로운 인스턴스가 생성됩니다.                             | 이러한 `GameplayAbility`의 장점은 활성화할 때마다 변수가 재설정된다는 것입니다. 활성화할 때마다 새로운 `GameplayAbility`를 생성하므로 `Instanced Per Actor`보다 성능이 떨어집니다. 샘플 프로젝트에서는 이를 사용하지 않습니다.                                                                                                                                                 |
| Non-Instanced           | `GameplayAbility`는 `ClassDefaultObject`에서 작동합니다. 인스턴스가 생성되지 않습니다.        | 세 가지 중 가장 좋은 성능을 보이지만 수행할 수 있는 작업이 가장 제한적입니다. `Non-Instanced` `GameplayAbility`는 상태를 저장할 수 없으며, 이는 동적 변수와 `AbilityTask` 대리자에 바인딩할 수 없음을 의미합니다. MOBA나 RTS의 미니언 기본 공격과 같이 자주 사용되는 단순한 어빌리티에 가장 적합합니다. 샘플 프로젝트의 Jump `GameplayAbility`는 `Non-Instanced`입니다. |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 네트워크 실행 정책 (Net Execution Policy)
`GameplayAbility`의 `Net Execution Policy`는 누가, 어떤 순서로 `GameplayAbility`를 실행할지 결정합니다.

| `Net Execution Policy` | 설명                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility`는 소유 클라이언트에서만 실행됩니다. 이는 로컬 시각적 변경만 하는 어빌리티에 유용할 수 있습니다. 싱글 플레이어 게임은 `Server Only`를 사용해야 합니다.                                     |
| `Local Predicted`      | `Local Predicted` `GameplayAbility`는 먼저 소유 클라이언트에서 실행된 다음 서버에서 실행됩니다. 서버 버전은 클라이언트가 잘못 예측한 것을 수정합니다. [Prediction](#concepts-p)을 참조하세요. |
| `Server Only`          | `GameplayAbility`는 서버에서만 실행됩니다. 패시브 `GameplayAbility`는 일반적으로 `Server Only`가 될 것입니다. 싱글 플레이어 게임은 이것을 사용해야 합니다.                                                  |
| `Server Initiated`     | `Server Initiated` `GameplayAbility`는 먼저 서버에서 실행된 다음 소유 클라이언트에서 실행됩니다. 개인적으로는 이것을 거의 사용하지 않았습니다.                                                     |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 어빌리티 태그 (Ability Tags)
`GameplayAbility`에는 내장된 로직을 가진 `GameplayTagContainer`가 함께 제공됩니다. 이러한 `GameplayTag`들은 복제되지 않습니다.

| `GameplayTag Container`     | 설명                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility`가 소유한 `GameplayTag`입니다. 이는 단순히 `GameplayAbility`를 설명하는 `GameplayTag`입니다.                                                                              |
| `Cancel Abilities with Tag` | 이 `GameplayAbility`가 활성화될 때 `Ability Tags`에 이러한 `GameplayTag`를 가진 다른 `GameplayAbility`들이 취소됩니다.                                                   |
| `Block Abilities with Tag`  | 이 `GameplayAbility`가 활성화되어 있는 동안 `Ability Tags`에 이러한 `GameplayTag`를 가진 다른 `GameplayAbility`들의 활성화가 차단됩니다.                                          |
| `Activation Owned Tags`     | 이 `GameplayAbility`가 활성화되어 있는 동안 이러한 `GameplayTag`들이 `GameplayAbility`의 소유자에게 부여됩니다. 이들은 복제되지 않는다는 점을 기억하세요.                                    |
| `Activation Required Tags`  | 이 `GameplayAbility`는 소유자가 이러한 `GameplayTag`를 **모두** 가지고 있을 때만 활성화될 수 있습니다.                                                                                |
| `Activation Blocked Tags`   | 소유자가 이러한 `GameplayTag` 중 **하나라도** 가지고 있으면 이 `GameplayAbility`는 활성화될 수 없습니다.                                                                                  |
| `Source Required Tags`      | 이 `GameplayAbility`는 `Source`가 이러한 `GameplayTag`를 **모두** 가지고 있을 때만 활성화될 수 있습니다. `Source` `GameplayTag`는 `GameplayAbility`가 이벤트에 의해 트리거될 때만 설정됩니다. |
| `Source Blocked Tags`       | `Source`가 이러한 `GameplayTag` 중 **하나라도** 가지고 있으면 이 `GameplayAbility`는 활성화될 수 없습니다. `Source` `GameplayTag`는 `GameplayAbility`가 이벤트에 의해 트리거될 때만 설정됩니다.   |
| `Target Required Tags`      | 이 `GameplayAbility`는 `Target`이 이러한 `GameplayTag`를 **모두** 가지고 있을 때만 활성화될 수 있습니다. `Target` `GameplayTag`는 `GameplayAbility`가 이벤트에 의해 트리거될 때만 설정됩니다. |
| `Target Blocked Tags`       | `Target`이 이러한 `GameplayTag` 중 **하나라도** 가지고 있으면 이 `GameplayAbility`는 활성화될 수 없습니다. `Target` `GameplayTag`는 `GameplayAbility`가 이벤트에 의해 트리거될 때만 설정됩니다.   |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 게임플레이 어빌리티 스펙 (Gameplay Ability Spec)
`GameplayAbilitySpec`은 `GameplayAbility`가 부여된 후 `ASC`에 존재하며, 활성화 가능한 `GameplayAbility`를 정의합니다 - `GameplayAbility` 클래스, 레벨, 입력 바인딩, 그리고 `GameplayAbility` 클래스와 별도로 유지되어야 하는 런타임 상태를 포함합니다.

서버에서 `GameplayAbility`가 부여되면, 서버는 `GameplayAbilitySpec`을 소유 클라이언트에 복제하여 클라이언트가 이를 활성화할 수 있게 합니다.

`GameplayAbilitySpec`을 활성화하면 `Instancing Policy`에 따라 `GameplayAbility`의 인스턴스가 생성됩니다(`Non-Instanced` `GameplayAbility`의 경우 생성되지 않음).

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 어빌리티에 데이터 전달하기
`GameplayAbility`의 일반적인 패러다임은 `활성화->데이터 생성->적용->종료`입니다. 때로는 기존 데이터에 대해 작업해야 할 필요가 있습니다. GAS는 `GameplayAbility`에 외부 데이터를 전달하기 위한 몇 가지 옵션을 제공합니다:

| 방법                                           | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 이벤트로 `GameplayAbility` 활성화              | 데이터 페이로드를 포함한 이벤트로 `GameplayAbility`를 활성화합니다. 이벤트의 페이로드는 local predicted `GameplayAbility`의 경우 클라이언트에서 서버로 복제됩니다. 기존 변수에 맞지 않는 임의의 데이터에는 두 개의 `Optional Object` 또는 [`TargetData`](#concepts-targeting-data) 변수를 사용하세요. 이 방법의 단점은 입력 바인딩으로 어빌리티를 활성화할 수 없다는 것입니다. 이벤트로 `GameplayAbility`를 활성화하려면 `GameplayAbility`에 `Triggers`가 설정되어 있어야 합니다. `GameplayTag`를 할당하고 `GameplayEvent`에 대한 옵션을 선택하세요. 이벤트를 보내려면 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)` 함수를 사용하세요. |
| `WaitGameplayEvent` `AbilityTask` 사용         | `WaitGameplayEvent` `AbilityTask`를 사용하여 활성화된 후 페이로드 데이터가 있는 이벤트를 수신하도록 `GameplayAbility`에 지시합니다. 이벤트 페이로드와 전송 프로세스는 이벤트로 `GameplayAbility`를 활성화하는 것과 동일합니다. 이 방법의 단점은 이벤트가 `AbilityTask`에 의해 복제되지 않으며 `Local Only`와 `Server Only` `GameplayAbility`에만 사용해야 한다는 것입니다. 잠재적으로 이벤트 페이로드를 복제할 수 있는 자체 `AbilityTask`를 작성할 수 있습니다.                                                                                                                                                                                                               |
| `TargetData` 사용                              | 커스텀 `TargetData` 구조체는 클라이언트와 서버 간에 임의의 데이터를 전달하는 좋은 방법입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `OwnerActor` 또는 `AvatarActor`에 데이터 저장 | `OwnerActor`, `AvatarActor` 또는 참조할 수 있는 다른 객체에 저장된 복제된 변수를 사용합니다. 이 방법은 가장 유연하며 입력 바인딩으로 활성화된 `GameplayAbility`와 함께 작동합니다. 그러나 사용 시점에 복제로부터 데이터가 동기화될 것이라는 보장은 없습니다. 이를 미리 보장해야 합니다 - 즉, 복제된 변수를 설정한 다음 즉시 `GameplayAbility`를 활성화하면 패킷 손실로 인해 수신자 측에서 발생하는 순서가 보장되지 않습니다.                                                                                                                                                                                                                                   |


**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 어빌리티 비용과 쿨다운 (Ability Cost and Cooldown)
`GameplayAbility`에는 선택적 비용과 쿨다운 기능이 함께 제공됩니다. 비용은 `GameplayAbility`를 활성화하기 위해 `ASC`가 가지고 있어야 하는 사전 정의된 `Attribute` 양으로, `Instant` `GameplayEffect`([`Cost GE`](#concepts-ge-cost))로 구현됩니다. 쿨다운은 만료될 때까지 `GameplayAbility`의 재활성화를 방지하는 타이머로, `Duration` `GameplayEffect`([`Cooldown GE`](#concepts-ge-cooldown))로 구현됩니다.

`GameplayAbility`가 `UGameplayAbility::Activate()`를 호출하기 전에 `UGameplayAbility::CanActivateAbility()`를 호출합니다. 이 함수는 소유한 `ASC`가 비용을 감당할 수 있는지(`UGameplayAbility::CheckCost()`) 확인하고 `GameplayAbility`가 쿨다운 상태가 아닌지(`UGameplayAbility::CheckCooldown()`) 확인합니다.

`GameplayAbility`가 `Activate()`를 호출한 후에는 `UGameplayAbility::CommitAbility()`를 사용하여 언제든지 선택적으로 비용과 쿨다운을 확정할 수 있으며, 이는 `UGameplayAbility::CommitCost()`와 `UGameplayAbility::CommitCooldown()`을 호출합니다. 디자이너는 동시에 확정되지 않아야 하는 경우 `CommitCost()` 또는 `CommitCooldown()`을 별도로 호출하도록 선택할 수 있습니다. 비용과 쿨다운을 확정하면 `CheckCost()`와 `CheckCooldown()`을 한 번 더 호출하며, 이는 이와 관련된 `GameplayAbility`가 실패할 수 있는 마지막 기회입니다. `GameplayAbility`가 활성화된 후에 소유한 `ASC`의 `Attribute`가 잠재적으로 변경되어 확정 시점에 비용 요구사항을 충족하지 못할 수 있습니다. 비용과 쿨다운의 확정은 확정 시점에 [prediction key](#concepts-p-key)가 유효한 경우 [locally predicted](#concepts-p)될 수 있습니다.

구현 세부사항은 [`CostGE`](#concepts-ge-cost)와 [`CooldownGE`](#concepts-ge-cooldown)를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 어빌리티 레벨 올리기 (Leveling Up Abilities)
어빌리티의 레벨을 올리는 두 가지 일반적인 방법이 있습니다:

| 레벨 업 방법                            | 설명                                                                                                                                                                    |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 해제 후 새 레벨로 재부여       | 서버에서 `ASC`로부터 `GameplayAbility`를 해제(제거)하고 다음 레벨로 다시 부여합니다. 당시에 활성화되어 있었다면 `GameplayAbility`를 종료합니다.                                   |
| `GameplayAbilitySpec`의 레벨 증가 | 서버에서 `GameplayAbilitySpec`을 찾아 레벨을 증가시키고, 소유 클라이언트에 복제되도록 더티 마크를 설정합니다. 이 방법은 당시에 활성화되어 있었더라도 `GameplayAbility`를 종료하지 않습니다. |

두 방법의 주요 차이점은 레벨 업 시점에 활성화된 `GameplayAbility`를 취소할지 여부입니다. `GameplayAbility`에 따라 두 방법을 모두 사용하게 될 것입니다. `UGameplayAbility` 서브 클래스에 어떤 방법을 사용할지 지정하는 `bool` 값을 추가하는 것을 추천합니다.


**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 어빌리티 세트 (Ability Sets)
`GameplayAbilitySet`은 캐릭터의 입력 바인딩과 시작 `GameplayAbility` 목록을 보관하고 `GameplayAbility`를 부여하는 로직을 포함하는 편의성 `UDataAsset` 클래스입니다. 서브 클래스에는 추가 로직이나 속성도 포함될 수 있습니다. Paragon에서는 각 영웅마다 그들에게 주어진 모든 `GameplayAbility`를 포함하는 `GameplayAbilitySet`이 있었습니다.

지금까지 본 바로는 이 클래스가 불필요하다고 생각합니다. 샘플 프로젝트는 `GameplayAbilitySet`의 모든 기능을 `GDCharacterBase`와 그 서브클래스 내에서 처리합니다.


**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 어빌리티 배칭 (Ability Batching)
전통적인 `Gameplay Ability` 생명주기는 클라이언트에서 서버로 최소 2-3개의 RPC를 포함합니다.

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (선택적)
1. `ServerEndAbility()`

만약 `GameplayAbility`가 한 프레임 내에서 이 모든 작업을 하나의 원자적 그룹으로 수행한다면, 우리는 이 워크플로우를 최적화하여 2-3개의 RPC를 하나의 RPC로 배치(결합)할 수 있습니다. `GAS`는 이러한 RPC 최적화를 `Ability Batching`이라고 합니다. `Ability Batching`을 사용하는 일반적인 예는 히트스캔 총입니다. 히트스캔 총은 한 프레임 내에서 하나의 원자적 그룹으로 활성화되고, 라인 트레이스를 수행하며, [`TargetData`](#concepts-targeting-data)를 서버로 보내고, 어빌리티를 종료합니다. [GASShooter](https://github.com/tranek/GASShooter) 샘플 프로젝트는 히트스캔 총에 이 기법을 보여줍니다.

반자동(Semi-Automatic) 총은 가장 좋은 시나리오로, 3개의 RPC 대신 `CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()` (총알 히트 결과), `ServerEndAbility()`를 하나의 RPC로 배치합니다.

완전 자동/버스트(Full-Automatic/Burst) 총은 첫 번째 총알에 대해 두 개의 RPC 대신 `CallServerTryActivateAbility()`와 `ServerSetReplicatedTargetData()`를 하나의 RPC로 배치합니다. 각각의 후속 총알은 자체 `ServerSetReplicatedTargetData()` RPC입니다. 마지막으로, 총 발사가 멈추면 `ServerEndAbility()`가 별도의 RPC로 전송됩니다. 이는 첫 번째 총알에서만 두 개 대신 하나의 RPC를 절약하는 최악의 시나리오입니다. 이 시나리오는 [`Gameplay Event`](#concepts-ga-data)를 통해 어빌리티를 활성화하는 방식으로도 구현할 수 있었으며, 이 경우 총알의 `TargetData`를 `EventPayload`와 함께 클라이언트에서 서버로 보냈을 것입니다. 후자 접근방식의 단점은 배칭 접근방식이 어빌리티 내부에서 `TargetData`를 생성하는 반면, `TargetData`를 어빌리티 외부에서 생성해야 한다는 것입니다.

`Ability Batching`은 기본적으로 [`ASC`](#concepts-asc)에서 비활성화되어 있습니다. `Ability Batching`을 활성화하려면 `ShouldDoServerAbilityRPCBatch()`를 오버라이드하여 true를 반환하도록 하세요:

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

이제 `Ability Batching`이 활성화되었으므로, 배치하고자 하는 어빌리티를 활성화하기 전에 `FScopedServerAbilityRPCBatcher` 구조체를 먼저 생성해야 합니다. 이 특별한 구조체는 해당 범위 내에서 그 뒤에 오는 모든 어빌리티를 배치하려고 시도합니다. `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면, 활성화된 어빌리티는 배치를 시도하지 않습니다. `FScopedServerAbilityRPCBatcher`는 배치될 수 있는 각 함수에서 RPC를 보내는 호출을 가로채고 대신 메시지를 배치 구조체에 패킹하는 특별한 코드로 작동합니다. `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면 `UAbilitySystemComponent::EndServerAbilityRPCBatch()`에서 자동으로 이 배치 구조체를 서버에 RPC합니다. 서버는 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`에서 배치 RPC를 받습니다. `BatchInfo` 매개변수는 어빌리티가 종료되어야 하는지, 활성화 시점에 입력이 눌렸는지에 대한 플래그와, 포함된 경우 `TargetData`를 포함합니다. 이는 배칭이 제대로 작동하는지 확인하기 위해 브레이크포인트를 설정하기 좋은 함수입니다. 또는 cvar `AbilitySystem.ServerRPCBatching.Log 1`을 사용하여 특별한 어빌리티 배칭 로깅을 활성화할 수 있습니다.

이 메커니즘은 C++에서만 수행할 수 있으며, `FGameplayAbilitySpecHandle`로만 어빌리티를 활성화할 수 있습니다.

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter는 반자동과 완전 자동 총에 대해 동일한 배치된 `GameplayAbility`를 재사용하며, 이는 직접 `EndAbility()`를 호출하지 않습니다(현재 발사 모드에 따라 배치된 어빌리티에 대한 호출을 관리하는 로컬 전용 어빌리티에 의해 외부에서 처리됨). 모든 RPC가 `FScopedServerAbilityRPCBatcher`의 범위 내에서 발생해야 하므로, 컨트롤/관리하는 로컬 전용이 이 어빌리티가 `EndAbility()` 호출을 배치해야 하는지(반자동), 또는 `EndAbility()` 호출을 배치하지 않고(완전 자동) 나중에 자체 RPC에서 `EndAbility()` 호출이 발생할지를 지정할 수 있도록 `EndAbilityImmediately` 매개변수를 제공합니다.

GASShooter는 앞서 언급한 로컬 전용 어빌리티가 배치된 어빌리티를 트리거하는 데 사용하는 Blueprint 노드를 노출합니다.

![배치된 어빌리티 활성화](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 네트워크 보안 정책 (Net Security Policy)
`GameplayAbility`의 `NetSecurityPolicy`는 네트워크상에서 어빌리티가 어디서 실행되어야 하는지를 결정합니다. 이는 클라이언트가 제한된 어빌리티를 실행하려는 시도로부터 보호합니다.

| `NetSecurityPolicy`     | 설명                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 보안 요구사항이 없습니다. 클라이언트나 서버가 이 어빌리티의 실행과 종료를 자유롭게 트리거할 수 있습니다.                                           |
| `ServerOnlyExecution`   | 이 어빌리티의 실행을 요청하는 클라이언트는 서버에서 무시됩니다. 클라이언트는 여전히 서버에 이 어빌리티의 취소나 종료를 요청할 수 있습니다. |
| `ServerOnlyTermination` | 이 어빌리티의 취소나 종료를 요청하는 클라이언트는 서버에서 무시됩니다. 클라이언트는 여전히 어빌리티의 실행을 요청할 수 있습니다.      |
| `ServerOnly`            | 서버가 이 어빌리티의 실행과 종료를 모두 제어합니다. 클라이언트가 하는 모든 요청은 무시됩니다.                                      |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 어빌리티 태스크 (Ability Tasks)

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task 정의
`GameplayAbility`는 한 프레임에서만 실행됩니다. 이것만으로는 유연성이 많이 부족합니다. 시간에 걸쳐 발생하거나 나중에 특정 시점에 발생하는 대리자(delegate)에 응답해야 하는 작업을 수행하기 위해 우리는 `AbilityTasks`라고 하는 지연 작업을 사용합니다.

GAS는 많은 `AbilityTask`를 기본 제공합니다:
* `RootMotionSource`로 캐릭터를 이동시키는 태스크
* 애니메이션 몽타주를 재생하는 태스크
* `Attribute` 변경에 응답하는 태스크
* `GameplayEffect` 변경에 응답하는 태스크
* 플레이어 입력에 응답하는 태스크
* 그 외 다수

`UAbilityTask` 생성자는 동시에 실행될 수 있는 최대 1000개의 `AbilityTask`라는 하드코딩된 게임 전역 제한을 강제합니다. RTS 게임처럼 동시에 수백 개의 캐릭터가 세계에 존재할 수 있는 게임을 위한 `GameplayAbility`를 설계할 때 이 점을 유의하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 커스텀 어빌리티 태스크 (Custom Ability Tasks)
종종 여러분은 자신만의 커스텀 `AbilityTask`를 만들게 될 것입니다(C++에서). 샘플 프로젝트에는 두 개의 커스텀 `AbilityTask`가 포함되어 있습니다:
1. `PlayMontageAndWaitForEvent`는 기본 `PlayMontageAndWait`와 `WaitGameplayEvent` `AbilityTask`를 결합한 것입니다. 이를 통해 애니메이션 몽타주가 `AnimNotifies`에서 그것을 시작한 `GameplayAbility`로 게임플레이 이벤트를 보낼 수 있습니다. 이를 사용하여 애니메이션 몽타주 중 특정 시점에서 액션을 트리거하세요.
1. `WaitReceiveDamage`는 `OwnerActor`가 데미지를 받는 것을 감시합니다. 패시브 방어구 스택 `GameplayAbility`는 영웅이 데미지를 받을 때 방어구 스택을 하나 제거합니다.

`AbilityTask`는 다음으로 구성됩니다:
* `AbilityTask`의 새 인스턴스를 생성하는 정적 함수
* `AbilityTask`가 목적을 완수했을 때 브로드캐스트되는 델리게이트
* 주요 작업을 시작하고, 외부 델리게이트에 바인딩하는 등의 작업을 하는 `Activate()` 함수
* 바인딩된 외부 델리게이트를 포함한 정리를 위한 `OnDestroy()` 함수
* 바인딩된 외부 델리게이트를 위한 콜백 함수
* 멤버 변수와 내부 헬퍼 함수

**참고:** `AbilityTask`는 한 가지 유형의 출력 델리게이트만 선언할 수 있습니다. 매개변수를 사용하는지 여부와 관계없이 모든 출력 델리게이트가 이 유형이어야 합니다. 사용하지 않는 델리게이트 매개변수에는 기본값을 전달하세요.

`AbilityTask`는 소유한 `GameplayAbility`를 실행하는 클라이언트나 서버에서만 실행됩니다. 하지만 `AbilityTask` 생성자에서 `bSimulatedTask = true;`를 설정하고, `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`를 오버라이드하고, 모든 멤버 변수가 복제되도록 설정함으로써 시뮬레이션된 클라이언트에서 실행되도록 할 수 있습니다. 이는 모든 이동 변경을 복제하지 않고 대신 전체 이동 `AbilityTask`를 시뮬레이션하고 싶은 이동 `AbilityTask`와 같은 드문 상황에서만 유용합니다. 모든 `RootMotionSource` `AbilityTask`가 이렇게 합니다. 예시로 `AbilityTask_MoveToLocation.h/.cpp`를 참조하세요.

`AbilityTask`는 `AbilityTask` 생성자에서 `bTickingTask = true;`를 설정하고 `virtual void TickTask(float DeltaTime);`를 오버라이드하면 `Tick`할 수 있습니다. 이는 프레임 간에 값을 부드럽게 보간해야 할 때 유용합니다. 예시로 `AbilityTask_MoveToLocation.h/.cpp`를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 어빌리티 태스크 사용하기 (Using Ability Tasks)
C++에서 `AbilityTask`를 생성하고 활성화하기 (`GDGA_FireGun.cpp`에서):
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

블루프린트에서는 `AbilityTask`를 위해 생성한 블루프린트 노드를 사용합니다. `ReadyForActivation()`을 호출할 필요가 없습니다. 이는 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`에 의해 자동으로 호출됩니다. `K2Node_LatentGameplayTaskCall`은 또한 `AbilityTask` 클래스에 존재한다면 `BeginSpawningActor()`와 `FinishSpawningActor()`를 자동으로 호출합니다(`AbilityTask_WaitTargetData` 참조). 다시 말하지만, `K2Node_LatentGameplayTaskCall`은 블루프린트에서만 자동으로 처리됩니다. C++에서는 `ReadyForActivation()`, `BeginSpawningActor()`, `FinishSpawningActor()`를 수동으로 호출해야 합니다.

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

`AbilityTask`를 수동으로 취소하려면 블루프린트(여기서는 `Async Task Proxy`라고 불림)나 C++에서 `AbilityTask` 객체에 대해 `EndTask()`를 호출하면 됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 루트 모션 소스 어빌리티 태스크 (Root Motion Source Ability Tasks)
GAS는 넉백, 복잡한 점프, 당기기, 대시와 같은 것을 위해 `CharacterMovementComponent`에 연결된 `Root Motion Sources`를 사용하여 `Character`를 시간에 걸쳐 이동시키는 `AbilityTask`를 제공합니다.

**참고:** `RootMotionSource` `AbilityTask`의 예측은 엔진 버전 4.19와 4.25+ 에서 작동합니다. 예측은 엔진 버전 4.20-4.24에서 버그가 있습니다. 하지만 `AbilityTask`는 여전히 멀티플레이어에서 약간의 네트워크 보정과 함께 기능을 수행하며 싱글 플레이어에서는 완벽하게 작동합니다. 4.25의 [예측 수정](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)을 커스텀 4.20-4.24 엔진에 가져오는 것이 가능합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 게임플레이 큐 (Gameplay Cues)

<a name="concepts-gc-definition"></a>
#### 4.8.1 게임플레이 큐 정의 (Gameplay Cue Definition)
`GameplayCue`(`GC`)는 사운드 이펙트, 파티클 이펙트, 카메라 쉐이크 등과 같은 게임 플레이와 관련없는 것들을 실행합니다. `GameplayCue`는 일반적으로 복제되며(명시적으로 로컬에서 `Execute`, `Add`, `Remove`되지 않는 한) 예측됩니다.

우리는 **필수 부모 이름 `GameplayCue.`**를 가진 해당하는 `GameplayTag`와 이벤트 타입(`Execute`, `Add`, 또는 `Remove`)을 `ASC`를 통해 `GameplayCueManager`에 전송하여 `GameplayCue`를 트리거합니다. `GameplayCueNotify` 객체와 `IGameplayCueInterface`를 구현하는 다른 `Actor`들은 `GameplayCue`의 `GameplayTag`(`GameplayCueTag`)를 기반으로 이러한 이벤트를 구독할 수 있습니다.

**참고:** 다시 강조하자면, `GameplayCue` `GameplayTag`는 반드시 부모 `GameplayTag`인 `GameplayCue`로 시작해야 합니다. 예를 들어, 유효한 `GameplayCue` `GameplayTag`는 `GameplayCue.A.B.C`가 될 수 있습니다.

`GameplayCueNotify`에는 `Static`과 `Actor` 두 가지 클래스가 있습니다. 이들은 서로 다른 이벤트에 응답하며 서로 다른 유형의 `GameplayEffect`가 이들을 트리거할 수 있습니다. 해당하는 이벤트를 여러분의 로직으로 오버라이드하세요.

| `GameplayCue` 클래스                                                                                                                  | 이벤트            | `GameplayEffect` 유형    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` 또는 `Periodic`  | Static `GameplayCueNotify`는 `ClassDefaultObject`에서 작동하며(인스턴스 없음) 히트 임팩트와 같은 일회성 효과에 완벽합니다.                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` 또는 `Remove` | `Duration` 또는 `Infinite` | Actor `GameplayCueNotify`는 `Add`될 때 새 인스턴스를 생성합니다. 인스턴스화되므로 `Remove`될 때까지 시간에 걸쳐 작업을 수행할 수 있습니다. 이는 기반이 되는 `Duration` 또는 `Infinite` `GameplayEffect`가 제거되거나 수동으로 remove를 호출할 때 제거될 반복 사운드와 파티클 이펙트에 적합합니다. 또한 동시에 `Add`될 수 있는 수를 관리하는 옵션이 제공되어 동일한 효과를 여러 번 적용해도 사운드나 파티클이 한 번만 시작되도록 할 수 있습니다. |

`GameplayCueNotify`는 기술적으로 모든 이벤트에 응답할 수 있지만, 일반적으로 이렇게 사용합니다.

**참고:** `GameplayCueNotify_Actor`를 사용할 때는 `Auto Destroy on Remove`를 체크하세요. 그렇지 않으면 해당 `GameplayCueTag`에 대한 후속 `Add` 호출이 작동하지 않습니다.

`Full`이 아닌 `ASC` [리플리케이션 모드](#concepts-asc-rm)를 사용할 때, `Add`와 `Remove` `GC` 이벤트는 서버 플레이어(리슨 서버)에서 두 번 발생합니다 - 한 번은 `GE`를 적용할 때, 다시 한 번은 클라이언트로의 "Minimal" `NetMultiCast`에서 발생합니다. 하지만 `WhileActive` 이벤트는 여전히 한 번만 발생합니다. 클라이언트에서는 모든 이벤트가 한 번만 발생합니다.

샘플 프로젝트에는 스턴과 스프린트 효과를 위한 `GameplayCueNotify_Actor`가 포함되어 있습니다. 또한 FireGun의 발사체 임팩트를 위한 `GameplayCueNotify_Static`도 있습니다. 이러한 `GC`들은 `GE`를 통해 복제하는 대신 [로컬에서 트리거](#concepts-gc-local)함으로써 더욱 최적화할 수 있습니다. 샘플 프로젝트에서는 초보자용 사용법을 보여주기 위해 이 방식을 선택했습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 게임플레이 큐 트리거하기 (Triggering Gameplay Cues)

`GameplayEffect` 내에서 성공적으로 적용될 때(태그나 면역에 의해 차단되지 않은 경우), 트리거되어야 할 모든 `GameplayCue`의 `GameplayTag`를 채웁니다.

![GameplayEffect에서 트리거되는 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility`는 `GameplayCue`를 `Execute`, `Add`, 또는 `Remove`하는 블루프린트 노드를 제공합니다.

![GameplayAbility에서 트리거되는 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

C++에서는 `ASC`에서 직접 함수를 호출할 수 있습니다(또는 `ASC` 서브 클래스에서 블루프린트에 노출할 수 있습니다):

```c++
/** GameplayCue는 독립적으로 올 수도 있습니다. 히트 결과 등을 전달하기 위한 선택적 effect context를 받습니다 */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 지속적인 gameplay cue 추가 */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 지속적인 gameplay cue 제거 */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** 독립적으로 추가된, 즉 GameplayEffect의 일부가 아닌 GameplayCue를 모두 제거합니다. */
void RemoveAllGameplayCues();
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 로컬 게임플레이 큐 (Local Gameplay Cues)
`GameplayAbility`와 `ASC`에서 노출된 `GameplayCue` 실행 함수들은 기본적으로 복제됩니다. 각 `GameplayCue` 이벤트는 멀티캐스트 RPC입니다. 이는 많은 RPC를 발생시킬 수 있습니다. GAS는 또한 net update당 동일한 `GameplayCue` RPC를 최대 2개로 제한합니다. 가능한 경우 로컬 `GameplayCue`를 사용하여 이를 피할 수 있습니다. 로컬 `GameplayCue`는 개별 클라이언트에서만 `Execute`, `Add`, 또는 `Remove`됩니다.

로컬 `GameplayCue`를 사용할 수 있는 시나리오:
* 발사체 임팩트
* 근접 전투 충돌 임팩트
* 애니메이션 몽타주에서 발생하는 `GameplayCue`

`ASC` 서브 클래스에 추가해야 하는 로컬 `GameplayCue` 함수들:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

`GameplayCue`가 로컬에서 `Added`되었다면, 로컬에서 `Remove`되어야 합니다. 복제를 통해 `Add`되었다면, 복제를 통해 `Remove`되어야 합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 게임플레이 큐 매개변수 (Gameplay Cue Parameters)
`GameplayCue`는 추가 정보를 포함한 `FGameplayCueParameters` 구조체를 매개변수로 받습니다. `GameplayAbility`나 `ASC`의 함수에서 수동으로 `GameplayCue`를 트리거하는 경우, `GameplayCue`에 전달되는 `GameplayCueParameters` 구조체를 수동으로 채워야 합니다. `GameplayCue`가 `GameplayEffect`에 의해 트리거되는 경우, 다음 변수들이 `GameplayCueParameters` 구조체에 자동으로 채워집니다:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (`GameplayEffect`가 `GameplayCue` 태그 컨테이너 위의 드롭다운에서 magnitude를 위한 `Attribute`를 선택했고, 해당 `Attribute`에 영향을 주는 해당 `Modifier`가 있는 경우)

`GameplayCueParameters` 구조체의 `SourceObject` 변수는 `GameplayCue`를 수동으로 트리거할 때 임의의 데이터를 `GameplayCue`에 전달하기에 적절한 장소가 될 수 있습니다.

**참고:** 매개변수 구조체의 `Instigator`와 같은 일부 변수들은 이미 `EffectContext`에 존재할 수 있습니다. `EffectContext`는 또한 월드에서 `GameplayCue`를 생성할 위치에 대한 `FHitResult`를 포함할 수 있습니다. `EffectContext`를 상속받는 것은 특히 `GameplayEffect`에 의해 트리거되는 것들에 대해 `GameplayCue`에 더 많은 데이터를 전달하는 좋은 방법이 될 수 있습니다.

자세한 내용은 [`UAbilitySystemGlobals`](#concepts-asg)의 `GameplayCueParameters` 구조체를 채우는 3개의 함수를 참조하세요. 이들은 가상 함수이므로 더 많은 정보를 자동으로 채우도록 오버라이드할 수 있습니다.

```c++
/** GameplayCue Parameters 초기화 */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 게임플레이 큐 매니저 (Gameplay Cue Manager)
기본적으로 `GameplayCueManager`는 전체 게임 디렉토리에서 `GameplayCueNotify`를 검색하고 플레이 시 이를 메모리에 로드합니다. `DefaultGame.ini`에서 `GameplayCueManager`가 검색하는 경로를 변경할 수 있습니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

우리는 `GameplayCueManager`가 모든 `GameplayCueNotify`를 검색하고 찾기를 원하지만, 플레이 시 모든 것을 비동기적으로 로드하는 것은 원하지 않습니다. 이는 레벨에서 실제로 사용되는지 여부와 관계없이 모든 `GameplayCueNotify`와 그들이 참조하는 모든 사운드와 파티클을 메모리에 로드할 것입니다. Paragon과 같은 대형 게임에서는 이로 인해 수백 메가바이트의 불필요한 에셋이 메모리에 올라가고 시작 시 끊김이나 게임 프리징이 발생할 수 있습니다.

시작 시 모든 `GameplayCue`를 비동기 로드하는 대신, 게임 내에서 트리거될 때만 `GameplayCue`를 비동기 로드하는 대안이 있습니다. 이는 불필요한 메모리 사용과 모든 `GameplayCue`를 비동기 로드하는 동안 발생할 수 있는 게임 프리징을 완화하는 대신, 특정 `GameplayCue`가 플레이 중 처음 트리거될 때 잠재적인 지연이 발생할 수 있습니다. 이 잠재적 지연은 SSD에서는 존재하지 않습니다. HDD에서는 테스트해보지 않았습니다. UE 에디터에서 이 옵션을 사용하는 경우, 에디터가 파티클 시스템을 컴파일해야 한다면 GameplayCue를 처음 로드할 때 약간의 끊김이나 프리징이 있을 수 있습니다. 이는 빌드에서는 문제가 되지 않습니다. 파티클 시스템이 이미 컴파일되어 있기 때문입니다.

먼저 `UGameplayCueManager`를 상속받고 `DefaultGame.ini`에서 `AbilitySystemGlobals` 클래스가 우리의 `UGameplayCueManager` 서브클래스를 사용하도록 지시해야 합니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

우리의 `UGameplayCueManager` 서브클래스에서 `ShouldAsyncLoadRuntimeObjectLibraries()`를 오버라이드합니다.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 게임플레이 큐 실행 방지하기 (Prevent Gameplay Cues from Firing)
때로는 `GameplayCue`가 실행되는 것을 원하지 않을 수 있습니다. 예를 들어 공격을 막았을 때, 데미지 `GameplayEffect`에 붙은 히트 임팩트를 재생하지 않거나 대신 커스텀 임팩트를 재생하고 싶을 수 있습니다. 이는 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 내에서 `OutExecutionOutput.MarkGameplayCuesHandledManually()`를 호출한 다음, 수동으로 `Target`이나 `Source`의 `ASC`에 `GameplayCue` 이벤트를 보내는 방식으로 할 수 있습니다.

특정 `ASC`에서 어떤 `GameplayCue`도 실행되지 않기를 원한다면, `AbilitySystemComponent->bSuppressGameplayCues = true;`로 설정할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 게임플레이 큐 배칭 (Gameplay Cue Batching)
트리거되는 각 `GameplayCue`는 신뢰할 수 없는(unreliable) NetMulticast RPC입니다. 동시에 여러 `GC`를 실행하는 상황에서는, 이들을 하나의 RPC로 압축하거나 더 적은 데이터를 전송하여 대역폭을 절약하는 몇 가지 최적화 방법이 있습니다.

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 수동 RPC (Manual RPC)
산탄총이 8개의 탄환을 발사한다고 가정해봅시다. 이는 8개의 트레이스와 임팩트 `GameplayCue`가 필요합니다. [GASShooter](https://github.com/tranek/GASShooter)는 모든 트레이스 정보를 [`TargetData`](#concepts-targeting-data)로서 [`EffectContext`](#concepts-ge-ec)에 저장하여 하나의 RPC로 결합하는 간단한 접근방식을 취합니다. 이는 RPC를 8개에서 1개로 줄이지만, 여전히 하나의 RPC에서 많은 데이터(~500 바이트)를 네트워크로 전송합니다. 더 최적화된 접근방식은 히트 위치를 효율적으로 인코딩하거나 수신 측에서 임팩트 위치를 재생성/근사하기 위한 랜덤 시드 번호를 제공하는 커스텀 구조체를 RPC로 보내는 것입니다. 클라이언트는 이 커스텀 구조체를 풀어서 [로컬에서 실행되는 `GameplayCue`](#concepts-gc-local)로 변환합니다.

작동 방식:
1. `FScopedGameplayCueSendContext`를 선언합니다. 이는 범위를 벗어날 때까지 `UGameplayCueManager::FlushPendingCues()`를 억제하여, 모든 `GameplayCue`가 `FScopedGameplayCueSendContext`가 범위를 벗어날 때까지 큐에 쌓이게 됩니다.
1. `UGameplayCueManager::FlushPendingCues()`를 오버라이드하여 커스텀 `GameplayTag`를 기반으로 배칭될 수 있는 `GameplayCue`들을 커스텀 구조체로 병합하고 클라이언트에 RPC로 전송합니다.
1. 클라이언트는 커스텀 구조체를 받아서 로컬에서 실행되는 `GameplayCue`로 풀어냅니다.

이 방법은 또한 데미지 수치, 크리티컬 표시기, 방패 파괴 표시기, 치명타 표시기 등과 같이 `GameplayCueParameters`가 제공하는 것과 맞지 않고 `EffectContext`에 추가하고 싶지 않은 특정 매개변수가 필요한 `GameplayCue`에도 사용할 수 있습니다.

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 하나의 GE에서 여러 GC 사용 (Multiple GCs on one GE)
하나의 `GameplayEffect`의 모든 `GameplayCue`는 이미 하나의 RPC로 전송됩니다. 기본적으로, `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`은 `ASC`의 `Replication Mode`와 관계없이 전체 `GameplayEffectSpec`을 (`FGameplayEffectSpecForRPC`로 변환하여) 신뢰할 수 없는 NetMulticast로 전송합니다. `GameplayEffectSpec`에 포함된 내용에 따라 이는 많은 대역폭을 사용할 수 있습니다. cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`을 설정하여 이를 최적화할 수 있습니다. 이렇게 하면 `GameplayEffectSpec`을 `FGameplayCueParameter` 구조체로 변환하여 전체 `FGameplayEffectSpecForRPC` 대신 이를 RPC로 전송합니다. `GESpec`이 `GameplayCueParameters`로 어떻게 변환되는지와 `GC`가 알아야 할 내용에 따라 대역폭을 절약할 수 있지만 정보는 줄어들 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 게임플레이 큐 이벤트 (Gameplay Cue Events)
`GameplayCue`는 특정 `EGameplayCueEvent`에 응답합니다:

| `EGameplayCueEvent` | 설명                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | `GameplayCue`가 활성화(추가)될 때 호출됩니다.                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | 실제로 방금 적용되지 않았더라도(진행 중인 게임 참여 등) `GameplayCue`가 활성화되어 있을 때 호출됩니다. 이는 `Tick`이 아닙니다! `GameplayCueNotify_Actor`가 추가되거나 관련성을 가질 때 `OnActive`처럼 한 번만 호출됩니다. `Tick()`이 필요하다면, `GameplayCueNotify_Actor`의 `Tick()`을 사용하세요. 결국 이는 `AActor`입니다. |
| `Removed`           | `GameplayCue`가 제거될 때 호출됩니다. 이 이벤트에 응답하는 블루프린트 `GameplayCue` 함수는 `OnRemove`입니다.                                                                                                                                                                                                             |
| `Executed`          | `GameplayCue`가 실행될 때 호출됩니다: 즉각적인 효과나 주기적인 `Tick()`입니다. 이 이벤트에 응답하는 블루프린트 `GameplayCue` 함수는 `OnExecute`입니다.                                                                                                                                                                     |

`OnActive`는 `GameplayCue` 시작 시 발생하는 모든 것에 사용하되, 늦게 참여하는 플레이어가 놓쳐도 괜찮은 것에 사용하세요. `WhileActive`는 늦게 참여하는 플레이어도 볼 수 있기를 원하는 `GameplayCue`의 지속적인 효과에 사용하세요. 예를 들어, MOBA에서 타워 구조물이 폭발하는 `GameplayCue`가 있다면, 초기 폭발 파티클 시스템과 폭발 사운드는 `OnActive`에 넣고, 잔여 지속되는 화염 파티클이나 사운드는 `WhileActive`에 넣을 것입니다. 이 시나리오에서는 늦게 참여하는 플레이어가 `OnActive`의 초기 폭발을 다시 재생하는 것은 의미가 없지만, `WhileActive`의 폭발 후 지속되는 반복적인 화염 효과는 보여주고 싶을 것입니다. `OnRemove`는 `OnActive`와 `WhileActive`에서 추가된 모든 것을 정리해야 합니다. `WhileActive`는 Actor가 `GameplayCueNotify_Actor`의 관련 범위에 들어올 때마다 호출됩니다. `OnRemove`는 Actor가 `GameplayCueNotify_Actor`의 관련 범위를 벗어날 때마다 호출됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 게임플레이 큐 신뢰성 (Gameplay Cue Reliability)

일반적으로 `GameplayCue`는 신뢰할 수 없는 것으로 간주되어야 하며, 따라서 게임플레이에 직접적인 영향을 미치는 것에는 적합하지 않습니다.

**실행된 `GameplayCue`:** 이러한 `GameplayCue`는 신뢰할 수 없는 멀티캐스트를 통해 적용되며 항상 신뢰할 수 없습니다.

**`GameplayEffect`로부터 적용된 `GameplayCue`:**
* Autonomous proxy는 `OnActive`, `WhileActive`, `OnRemove`를 신뢰성 있게 받습니다.  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()`는 `OnActive`와 `WhileActive`를 호출하기 위해 `UAbilitySystemComponent::HandleDeferredGameplayCues()`를 호출합니다. `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()`는 `OnRemoved`를 호출합니다.
* Simulated proxy는 `WhileActive`와 `OnRemove`를 신뢰성 있게 받습니다.  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`의 복제가 `WhileActive`와 `OnRemove`를 호출합니다. `OnActive` 이벤트는 신뢰할 수 없는 멀티캐스트로 호출됩니다.

**`GameplayEffect` 없이 적용된 `GameplayCue`:**
* Autonomous proxy는 `OnRemove`를 신뢰성 있게 받습니다.  
`OnActive`와 `WhileActive` 이벤트는 신뢰할 수 없는 멀티캐스트로 호출됩니다.
* Simulated proxy는 `WhileActive`와 `OnRemove`를 신뢰성 있게 받습니다.  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`의 복제가 `WhileActive`와 `OnRemove`를 호출합니다. `OnActive` 이벤트는 신뢰할 수 없는 멀티캐스트로 호출됩니다.

`GameplayCue`에서 '신뢰성 있는(reliable)' 무언가가 필요하다면, `GameplayEffect`에서 적용하고 FX를 추가하기 위해 `WhileActive`를 사용하고 FX를 제거하기 위해 `OnRemove`를 사용하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 어빌리티 시스템 글로벌 (Ability System Globals)
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 클래스는 GAS에 대한 전역 정보를 보관합니다. 대부분의 변수는 `DefaultGame.ini`에서 설정할 수 있습니다. 일반적으로 이 클래스와 직접 상호작용할 필요는 없지만, 그 존재를 알고 있어야 합니다. [`GameplayCueManager`](#concepts-gc-manager)나 [`GameplayEffectContext`](#concepts-ge-context)와 같은 것들을 상속받아야 할 경우, `AbilitySystemGlobals`를 통해 해야 합니다.

`AbilitySystemGlobals`를 상속받으려면 `DefaultGame.ini`에 클래스 이름을 설정하세요:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
UE 4.24와 5.2 사이에서는 [`TargetData`](#concepts-targeting-data)를 사용하기 위해 `UAbilitySystemGlobals::Get().InitGlobalData()`를 호출해야 합니다. 그렇지 않으면 `ScriptStructCache`와 관련된 오류가 발생하고 클라이언트가 서버에서 연결 해제됩니다. 이 함수는 프로젝트에서 한 번만 호출하면 됩니다. Fortnite는 이를 `UAssetManager::StartInitialLoading()`에서 호출하고 Paragon은 `UEngine::Init()`에서 호출했습니다. 샘플 프로젝트에서 보여주는 것처럼 `UAssetManager::StartInitialLoading()`에 넣는 것이 좋은 위치라고 생각합니다. 이는 `TargetData` 관련 문제를 피하기 위해 프로젝트에 복사해야 할 상용구 코드라고 생각하시면 됩니다. 5.3부터는 자동으로 호출됩니다.

`AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`를 사용할 때 크래시가 발생하면, Fortnite처럼 `AssetManager`나 `GameInstance`에서 `UAbilitySystemGlobals::Get().InitGlobalData()`를 나중에 호출해야 할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 예측 (Prediction)
GAS는 기본적으로 클라이언트 측 예측을 지원합니다. 하지만 모든 것을 예측하지는 않습니다. GAS의 클라이언트 측 예측은 클라이언트가 `GameplayAbility`를 활성화하고 `GameplayEffect`를 적용하기 위해 서버의 허가를 기다릴 필요가 없다는 것을 의미합니다. 클라이언트는 서버가 이를 허가할 것과 `GameplayEffect`를 적용할 대상을 "예측"할 수 있습니다. 그런 다음 서버는 클라이언트가 활성화한 후 네트워크 지연 시간만큼 지난 뒤 `GameplayAbility`를 실행하고 클라이언트의 예측이 맞았는지 여부를 알려줍니다. 클라이언트의 예측이 잘못된 경우, "잘못된 예측"으로 인한 변경사항을 "롤백"하여 서버와 일치시킵니다.

GAS 관련 예측에 대한 최종 소스는 플러그인 소스 코드의 `GameplayPrediction.h`입니다.

Epic의 사고방식은 "해볼 만한" 것들만 예측하는 것입니다. 예를 들어, Paragon과 Fortnite는 데미지를 예측하지 않습니다. 아마도 어차피 예측할 수 없는 [`ExecutionCalculations`](#concepts-ge-ec)를 데미지에 사용하기 때문일 것입니다. 이는 데미지와 같은 특정 요소를 예측할 수 없다는 의미는 아닙니다. 물론 여러분이 시도해서 잘 작동한다면 그것은 좋은 일입니다.

> ... 우리는 또한 "모든 것을 자동으로 완벽하게 예측"하는 솔루션을 전적으로 지지하지는 않습니다. 우리는 여전히 플레이어 예측은 최소한으로 유지하는 것이 가장 좋다고 생각합니다(즉, 해볼 만한 최소한의 것만 예측하라는 의미입니다).

*새로운 [Network Prediction Plugin](#concepts-p-npp)에 대한 Epic의 Dave Ratti의 코멘트*

**예측되는 것:**
> * 어빌리티 활성화
> * 트리거된 이벤트
> * GameplayEffect 적용:
>    * Attribute 수정(예외: Execution은 현재 예측되지 않음, Attribute 모디파이어만 예측)
>    * GameplayTag 수정
> * 게임플레이 큐 이벤트(예측 가능한 gameplay effect 내부와 단독 모두)
> * 몽타주
> * 이동(UE의 UCharacterMovement에 내장)

**예측되지 않는 것:**
> * GameplayEffect 제거
> * GameplayEffect 주기적 효과(도트 데미지 틱)

*`GameplayPrediction.h`에서 발췌*

우리는 `GameplayEffect` 적용을 예측할 수 있지만, `GameplayEffect` 제거는 예측할 수 없습니다. 이 제한을 우회하는 한 가지 방법은 `GameplayEffect`를 제거하고 싶을 때 역효과를 예측하는 것입니다. 예를 들어 40%의 이동 속도 감소를 예측했다고 가정해봅시다. 40%의 이동 속도 버프를 적용하여 예측적으로 제거할 수 있습니다. 그런 다음 두 `GameplayEffect`를 동시에 제거합니다. 이는 모든 시나리오에 적합하지는 않으며, `GameplayEffect` 제거 예측에 대한 지원은 여전히 필요합니다. Epic의 Dave Ratti는 [GAS의 향후 버전](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)에서 이를 추가하고 싶다는 의견을 표명했습니다.

`GameplayEffect` 제거를 예측할 수 없기 때문에, `GameplayAbility` 쿨다운을 완전히 예측할 수 없으며 이에 대한 역효과 `GameplayEffect` 우회 방법도 없습니다. 서버의 복제된 `Cooldown GE`는 클라이언트에 존재하며, 이를 우회하려는 시도(예: `Minimal` 리플리케이션 모드 사용)는 서버에서 거부됩니다. 이는 지연 시간이 높은 클라이언트가 서버에게 쿨다운을 시작하라고 알리고 서버의 `Cooldown GE` 제거를 받는 데 더 오래 걸린다는 것을 의미합니다. 이로 인해 지연 시간이 높은 플레이어는 지연 시간이 낮은 플레이어보다 발사 속도가 낮아져 불이익을 받게 됩니다. Fortnite는 `Cooldown GE` 대신 커스텀 북키핑을 사용하여 이 문제를 피합니다.

데미지 예측과 관련하여, GAS를 시작할 때 대부분의 사람들이 가장 먼저 시도하는 것 중 하나임에도 불구하고 개인적으로는 추천하지 않습니다. 특히 죽음을 예측하는 것은 추천하지 않습니다. 데미지 예측이 가능하긴 하지만, 그렇게 하는 것은 까다롭습니다. 데미지 적용을 잘못 예측하면 플레이어는 적의 체력이 다시 올라가는 것을 보게 됩니다. 특히 죽음을 예측하려고 하면 매우 어색하고 좌절스러울 수 있습니다. 예를 들어 `Character`의 죽음을 잘못 예측하여 래그돌이 시작되었다가, 서버가 수정했을 때 래그돌이 멈추고 당신을 향해 계속 발사하는 상황을 생각해보세요.

**참고:** (`Cost GE`와 같은) `Instant` `GameplayEffect`가 `Attribute`를 변경하는 것은 자신에게는 매끄럽게 예측될 수 있지만, 다른 캐릭터에 대한 `Instant` `Attribute` 변경을 예측하면 그들의 `Attribute`에 짧은 이상이나 "깜빡임"이 표시됩니다. 예측된 `Instant` `GameplayEffect`는 실제로 잘못 예측된 경우 롤백할 수 있도록 `Infinite` `GameplayEffect`처럼 취급됩니다. 서버의 `GameplayEffect`가 적용되면, 잠재적으로 동일한 `GameplayEffect`가 두 개 존재하여 `Modifier`가 잠시 동안 두 번 적용되거나 전혀 적용되지 않을 수 있습니다. 결국에는 수정되지만 때로는 깜빡임이 플레이어들에게 눈에 띌 수 있습니다.

GAS의 예측 구현이 해결하려는 문제들:
> 1. "이걸 할 수 있나요?" 예측을 위한 기본 프로토콜.
> 2. "실행 취소" 예측이 실패했을 때 부작용을 실행 취소하는 방법.
> 3. "다시 실행" 로컬에서 예측했지만 서버에서도 복제되는 부작용을 재생하지 않는 방법.
> 4. "완전성" 모든 부작용을 /정말로/ 예측했는지 확인하는 방법.
> 5. "의존성" 의존적 예측과 예측된 이벤트의 체인을 관리하는 방법.
> 6. "오버라이드" 서버가 소유하거나 복제하는 상태를 예측적으로 오버라이드하는 방법.

*`GameplayPrediction.h`에서 발췌*

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 예측 키(Prediction Key)
GAS의 예측은 클라이언트가 `GameplayAbility`를 활성화할 때 생성하는 정수 식별자인 `Prediction Key` 개념을 기반으로 작동합니다.

* 클라이언트는 `GameplayAbility`를 활성화할 때 예측 키를 생성합니다. 이것이 `Activation Prediction Key`입니다.
* 클라이언트는 이 예측 키를 `CallServerTryActivateAbility()`와 함께 서버로 보냅니다.
* 클라이언트는 예측 키가 유효한 동안 적용하는 모든 `GameplayEffect`에 이 예측 키를 추가합니다.
* 클라이언트의 예측 키가 범위를 벗어납니다. 동일한 `GameplayAbility`에서 추가로 예측되는 효과는 새로운 [Scoped Prediction Window](#concepts-p-windows)가 필요합니다.

* 서버는 클라이언트로부터 예측 키를 받습니다.
* 서버는 적용하는 모든 `GameplayEffect`에 이 예측 키를 추가합니다.
* 서버는 예측 키를 클라이언트에게 다시 복제합니다.

* 클라이언트는 서버로부터 적용에 사용된 예측 키와 함께 복제된 `GameplayEffect`를 받습니다. 복제된 `GameplayEffect` 중 클라이언트가 동일한 예측 키로 적용한 `GameplayEffect`와 일치하는 것이 있다면, 그것은 올바르게 예측된 것입니다. 클라이언트가 예측한 것을 제거할 때까지 대상에 `GameplayEffect`의 복사본이 두 개 일시적으로 존재할 것입니다.
* 클라이언트는 서버로부터 예측 키를 다시 받습니다. 이것이 `Replicated Prediction Key`입니다. 이 예측 키는 이제 오래된 것으로 표시됩니다.
* 클라이언트는 이제 오래된 복제된 예측 키로 생성한 **모든** `GameplayEffect`를 제거합니다. 서버가 복제한 `GameplayEffect`는 유지됩니다. 클라이언트가 추가했지만 서버로부터 일치하는 복제 버전을 받지 못한 `GameplayEffect`는 잘못 예측된 것입니다.

예측 키는 `GameplayAbility` 내에서 `Activation` 예측 키로 시작하는 원자적 명령 그룹 "윈도우" 동안 유효성이 보장됩니다. 이는 한 프레임 동안만 유효한 것으로 생각할 수 있습니다. 지연 작업 `AbilityTask`의 콜백은 `AbilityTask`에 새로운 [Scoped Prediction Window](#concepts-p-windows)를 생성하는 내장된 동기화 지점이 없는 한 더 이상 유효한 예측 키를 가지지 않습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 어빌리티에서 새로운 예측 윈도우 생성하기 (Creating New Prediction Windows in Abilities)
`AbilityTask`의 콜백에서 더 많은 액션을 예측하기 위해서는 새로운 Scoped Prediction Key와 함께 새로운 Scoped Prediction Window를 생성해야 합니다. 이는 때때로 클라이언트와 서버 간의 동기화 지점이라고도 합니다. 입력 관련 `AbilityTask`들은 모두 새로운 scoped prediction window를 생성하는 내장 기능이 있어서, `AbilityTask` 콜백의 원자적 코드가 사용할 수 있는 유효한 scoped prediction key를 가집니다. `WaitDelay` 태스크와 같은 다른 태스크들은 콜백을 위한 새로운 scoped prediction window를 생성하는 내장 코드가 없습니다. `WaitDelay`와 같이 scoped prediction window를 생성하는 내장 코드가 없는 `AbilityTask` 이후에 액션을 예측해야 하는 경우, `OnlyServerWait` 옵션과 함께 `WaitNetSync` `AbilityTask`를 사용하여 수동으로 이를 수행해야 합니다. 클라이언트가 `OnlyServerWait`와 함께 `WaitNetSync`에 도달하면, `GameplayAbility`의 activation prediction key를 기반으로 새로운 scoped prediction key를 생성하고, 이를 서버에 RPC로 보내며, 적용하는 모든 새로운 `GameplayEffect`에 추가합니다. 서버가 `OnlyServerWait`와 함께 `WaitNetSync`에 도달하면, 계속하기 전에 클라이언트로부터 새로운 scoped prediction key를 받을 때까지 기다립니다. 이 scoped prediction key는 activation prediction key와 동일한 방식으로 작동합니다 - `GameplayEffect`에 적용되고 클라이언트에게 다시 복제되어 오래된 것으로 표시됩니다. scoped prediction key는 범위를 벗어날 때까지, 즉 scoped prediction window가 닫힐 때까지 유효합니다. 따라서 다시 말하지만, 원자적 연산만 가능하며, 지연된 것은 새로운 scoped prediction key를 사용할 수 없습니다.

필요한 만큼 많은 scoped prediction window를 생성할 수 있습니다.

자신의 커스텀 `AbilityTask`에 동기화 지점 기능을 추가하고 싶다면, 입력 관련 태스크들이 어떻게 `WaitNetSync` `AbilityTask` 코드를 주입하는지 살펴보세요.

**참고:** `WaitNetSync`를 사용할 때, 이는 서버의 `GameplayAbility`가 클라이언트로부터 응답을 받을 때까지 실행을 계속하지 못하도록 차단합니다. 이는 게임을 해킹하고 의도적으로 새로운 scoped prediction key 전송을 지연시키는 악의적인 사용자들에 의해 잠재적으로 악용될 수 있습니다. Epic은 `WaitNetSync`를 제한적으로 사용하지만, 이것이 우려된다면 클라이언트 없이도 자동으로 계속되는 지연이 있는 새로운 버전의 `AbilityTask`를 만드는 것을 추천합니다.

샘플 프로젝트는 Sprint `GameplayAbility`에서 `WaitNetSync`를 사용하여 스태미나 비용을 적용할 때마다 새로운 scoped prediction window를 생성하여 이를 예측할 수 있게 합니다. 이상적으로는 비용과 쿨다운을 적용할 때 유효한 예측 키를 가지고 싶습니다.

소유 클라이언트에서 예측된 `GameplayEffect`가 두 번 재생된다면, 예측 키가 오래되었고 "redo" 문제를 겪고 있는 것입니다. 일반적으로 `GameplayEffect`를 적용하기 직전에 `OnlyServerWait`와 함께 `WaitNetSync` `AbilityTask`를 배치하여 새로운 scoped prediction key를 생성함으로써 해결할 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 액터를 예측적으로 스폰하기 (Predictively Spawning Actors)
클라이언트에서 예측적으로 `Actor`를 스폰하는 것은 고급 주제입니다. GAS는 이를 기본적으로 처리하는 기능을 제공하지 않습니다(`SpawnActor` `AbilityTask`는 서버에서만 `Actor`를 스폰합니다). 핵심 개념은 클라이언트와 서버 모두에서 복제된 `Actor`를 스폰하는 것입니다.

`Actor`가 단순히 시각적이거나 게임플레이 목적이 없다면, 간단한 해결책은 `Actor`의 `IsNetRelevantFor()` 함수를 오버라이드하여 서버가 소유 클라이언트에게 복제하는 것을 제한하는 것입니다. 소유 클라이언트는 자신의 로컬에서 스폰된 버전을 가지고, 서버와 다른 클라이언트들은 서버의 복제된 버전을 가지게 됩니다.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

스폰된 `Actor`가 데미지를 예측해야 하는 발사체처럼 게임플레이에 영향을 미친다면, 이 문서의 범위를 벗어나는 고급 로직이 필요합니다. Epic Games의 GitHub에서 UnrealTournament가 어떻게 발사체를 예측적으로 스폰하는지 살펴보세요. 그들은 서버의 복제된 발사체와 동기화되는 더미 발사체를 소유 클라이언트에서만 스폰합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 GAS에서 예측의 미래 (Future of Prediction in GAS)
`GameplayPrediction.h`는 향후 `GameplayEffect` 제거와 주기적 `GameplayEffect`를 예측하는 기능을 추가할 수 있다고 명시합니다.

Epic의 Dave Ratti는 쿨다운 예측을 위한 `지연 시간 조정` 문제를 해결하는 데 [관심을 표명](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)했습니다. 이는 지연 시간이 높은 플레이어가 지연 시간이 낮은 플레이어에 비해 불이익을 받는 문제입니다.

Epic의 새로운 [`Network Prediction` 플러그인](#concepts-p-npp)은 이전의 `CharacterMovementComponent`처럼 GAS와 완전히 상호 운용될 것으로 예상됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 네트워크 예측 플러그인 (NPP, Network Prediction Plugin)
Epic은 최근 `CharacterMovementComponent`를 새로운 `Network Prediction` 플러그인으로 대체하는 계획을 시작했습니다. 이 플러그인은 아직 매우 초기 단계이지만 Unreal Engine GitHub에서 매우 조기 접근이 가능합니다. 어떤 향후 엔진 버전에서 실험적 베타로 데뷔할지는 아직 알 수 없습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 타겟팅 (Targeting)

<a name="concepts-targeting-data"></a>
#### 4.11.1 타겟 데이터 (Target Data)
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html)는 네트워크를 통해 전달되는 타겟팅 데이터를 위한 일반적인 구조체입니다. `TargetData`는 일반적으로 `AActor`/`UObject` 참조, `FHitResults`, 그리고 기타 일반적인 위치/방향/원점 정보를 포함합니다. 하지만 이를 상속받아 [`GameplayAbilities`에서 클라이언트와 서버 간에 데이터를 전달하는](#concepts-ga-data) 간단한 수단으로 원하는 어떤 것이든 넣을 수 있습니다. 기본 구조체인 `FGameplayAbilityTargetData`는 직접 사용하지 않고 상속받아 사용해야 합니다. `GAS`는 `GameplayAbilityTargetTypes.h`에 몇 가지 미리 만들어진 `FGameplayAbilityTargetData` 구조체를 제공합니다.

`TargetData`는 일반적으로 [`Target Actor`](#concepts-targeting-actors)에 의해 생성되거나 **수동으로 생성**되며, [`EffectContext`](#concepts-ge-context)를 통해 [`AbilityTask`](#concepts-at)와 [`GameplayEffect`](#concepts-ge)에서 사용됩니다. `EffectContext`에 포함되어 있기 때문에, [`Execution`](#concepts-ge-ec), [`MMC`](#concepts-ge-mmc), [`GameplayCue`](#concepts-gc), 그리고 [`AttributeSet`](#concepts-as)의 백엔드 함수들이 `TargetData`에 접근할 수 있습니다.

일반적으로 `FGameplayAbilityTargetData`를 직접 전달하지 않고, 대신 `FGameplayAbilityTargetData`에 대한 포인터들의 TArray를 내부적으로 가지고 있는 [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)을 사용합니다. 이 중간 구조체는 `TargetData`의 다형성을 지원합니다.

`FGameplayAbilityTargetData`를 상속받는 예시:
```c++
USTRUCT(BlueprintType)
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData
{
    GENERATED_BODY()
public:

    FGameplayAbilityTargetData_CustomData()
    { }

    UPROPERTY()
    FName CoolName = NAME_None;

    UPROPERTY()
    FPredictionKey MyCoolPredictionKey;

    // FGameplayAbilityTargetData의 모든 자식 구조체에 필요함
    virtual UScriptStruct* GetScriptStruct() const override
    {
    	return FGameplayAbilityTargetData_CustomData::StaticStruct();
    }

	// FGameplayAbilityTargetData의 모든 자식 구조체에 필요함
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
    {
	    // 엔진에서 이미 FName과 FPredictionKey에 대한 NetSerialize를 정의했습니다, Epic에 감사!
        CoolName.NetSerialize(Ar, Map, bOutSuccess);
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);
        bOutSuccess = true;
        return true;
    }
}

template<>
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>
{
	enum
	{
        WithNetSerializer = true // FGameplayAbilityTargetDataHandle의 네트워크 직렬화가 작동하기 위해 필요함
	};
};
```
핸들에 타겟 데이터를 추가하는 방법:
```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
    // 타겟 데이터 타입 생성
    // 핸들이 자동으로 정리되고 핸들이 소멸될 때 이 데이터를 삭제합니다
    // 핸들에 추가하지 않는다면 메모리 관리와 메모리 누수에 주의해야 하므로 프레임 내에서 항상 핸들에 추가하는 것이 안전합니다!
	FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
    // 입력된 이름과 기타 변경하고 싶은 사항을 구조체에 설정
	MyCustomData->CoolName = CustomName;
	
    // 블루프린트 사용을 위한 핸들 래퍼 생성
	FGameplayAbilityTargetDataHandle Handle;
    // 핸들에 타겟 데이터 추가
	Handle.Add(MyCustomData);
    // 블루프린트로 핸들 출력
	return Handle
}
```

값을 가져오기 위해서는 타입 안전성 검사가 필요합니다. 핸들의 타겟 데이터에서 값을 가져오는 유일한 방법은 일반 C/C++ 캐스팅을 사용하는 것인데, 이는 타입 안전성이 *없어* 객체 슬라이싱과 크래시를 일으킬 수 있기 때문입니다. 타입 검사에는 여러 가지 방법이 있습니다(원하는 대로 구현 가능). 일반적인 두 가지 방법은 다음과 같습니다:
- GameplayTag: 특정 코드 아키텍처의 기능이 발생할 때마다 기본 부모 타입으로 캐스팅하여 GameplayTag를 가져와서 상속된 클래스에 대한 캐스팅을 위해 비교할 수 있습니다.
- Script Struct & Static Structs: 대신 직접적인 클래스 비교를 할 수 있습니다(이는 많은 IF문이나 템플릿 함수를 만드는 것을 포함할 수 있음). 아래는 이를 사용하는 예시입니다. 기본적으로 모든 `FGameplayAbilityTargetData`에서 script struct를 가져올 수 있고(`USTRUCT`이며 상속된 모든 클래스가 `GetScriptStruct`에서 구조체 타입을 지정해야 하는 장점) 원하는 타입인지 비교할 수 있습니다. 아래는 이러한 타입 검사 함수를 사용하는 예시입니다:
```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{   
    // 참고: '::Get(int32 Index)' 함수에는 두 가지 버전이 있습니다:
    // 1) const 버전으로 'const FGameplayAbilityTargetData*'를 반환, 타겟 데이터 값 읽기에 좋음
    // 2) non-const 버전으로 'FGameplayAbilityTargetData*'를 반환, 타겟 데이터 값 수정에 좋음
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // 인덱스의 유효성을 자동으로 검사함
    
    // 사용할 데이터가 있는지 유효성 검사, null 데이터는 캐스팅할 것이 없음을 의미
    if(Data == nullptr)
    {
       	return NAME_None;
    }
    // 이것이 기본적인 타입 검사입니다. static_cast는 타입 안전성이 없으므로 이 검사를 수행합니다.
    // 이를 수행하지 않으면 구조체가 객체 슬라이싱되어 해당 타입인지 확인할 방법이 없습니다.
    if(Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
    {
        // 이미 올바른 타입임을 알고 있으므로 여기서 캐스팅을 수행합니다
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);    
        return CustomData->CoolName;
    }
    return NAME_None;
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 타겟 액터 (Target Actor)
 `GameplayAbilities`는 `WaitTargetData` `AbilityTask`를 통해 [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)를 스폰하여 월드에서 타겟팅 정보를 시각화하고 캡처합니다. `TargetActors`는 선택적으로 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles)를 사용하여 현재 타겟을 표시할 수 있습니다. 확인 시, 타겟팅 정보는 [`TargetData`](#concepts-targeting-data)로 반환되어 `GameplayEffects`에 전달될 수 있습니다.

`TargetActor`는 `AActor`를 기반으로 하므로 스태틱 메시나 데칼과 같이 타겟팅하는 **위치**와 **방법**을 나타내는 어떤 종류의 가시적인 컴포넌트도 가질 수 있습니다. 스태틱 메시는 캐릭터가 건설할 오브젝트의 배치를 시각화하는 데 사용될 수 있습니다. 데칼은 지면의 효과 범위를 보여주는 데 사용될 수 있습니다. 샘플 프로젝트에서는 메테오 어빌리티의 데미지 효과 범위를 나타내기 위해 데칼이 있는 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html)를 사용합니다. 아무것도 표시하지 않을 수도 있습니다. 예를 들어, [GASShooter](https://github.com/tranek/GASShooter)에서 사용되는 것처럼 즉시 타겟까지 선을 추적하는 히트스캔 총에는 아무것도 표시할 필요가 없습니다.

이들은 기본적인 트레이스나 콜리전 오버랩을 사용하여 타겟팅 정보를 캡처하고, `TargetActor` 구현에 따라 결과를 `FHitResults`나 `AActor` 배열로 `TargetData`로 변환합니다. `WaitTargetData` `AbilityTask`는 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 매개변수를 통해 타겟이 언제 확인되는지 결정합니다. `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`를 사용하지 **않을** 때, `TargetActor`는 일반적으로 `Tick()`에서 트레이스/오버랩을 수행하고 구현에 따라 위치를 `FHitResult`로 업데이트합니다. 이는 `Tick()`에서 트레이스/오버랩을 수행하지만, 복제되지 않고 일반적으로 한 번에 하나의 `TargetActor`만 실행되므로(더 많이 가질 수도 있지만) 그렇게 나쁘지 않습니다. 단, GASShooter의 로켓 런처의 보조 어빌리티처럼 복잡한 `TargetActor`는 `Tick()`에서 많은 작업을 수행할 수 있다는 점을 유의하세요. `Tick()`에서의 트레이스가 클라이언트에 매우 반응적이지만, 성능 저하가 너무 크다면 `TargetActor`의 틱 레이트를 낮추는 것을 고려할 수 있습니다. `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`의 경우, `TargetActor`는 즉시 스폰되어 `TargetData`를 생성하고 파괴됩니다. `Tick()`은 호출되지 않습니다.

| `EGameplayTargetingConfirmation::Type` | 타겟이 확인되는 시기 |
| -------------------------------------- | --------------------- |
| `Instant` | 특별한 로직이나 사용자 입력 없이 즉시 타겟팅이 발생합니다. |
| `UserConfirmed` | [어빌리티가 `Confirm` 입력에 바인딩되어 있거나](#concepts-ga-input) `UAbilitySystemComponent::TargetConfirm()`을 호출할 때 사용자가 타겟팅을 확인하면 타겟팅이 발생합니다. `TargetActor`는 또한 바인딩된 `Cancel` 입력이나 `UAbilitySystemComponent::TargetCancel()` 호출에 응답하여 타겟팅을 취소합니다. |
| `Custom` | GameplayTargeting Ability가 `UGameplayAbility::ConfirmTaskByInstanceName()`을 호출하여 타겟팅 데이터가 준비되었는지 결정할 책임이 있습니다. `TargetActor`는 또한 `UGameplayAbility::CancelTaskByInstanceName()`에 응답하여 타겟팅을 취소합니다. |
| `CustomMulti` | GameplayTargeting Ability가 `UGameplayAbility::ConfirmTaskByInstanceName()`을 호출하여 타겟팅 데이터가 준비되었는지 결정할 책임이 있습니다. `TargetActor`는 또한 `UGameplayAbility::CancelTaskByInstanceName()`에 응답하여 타겟팅을 취소합니다. 데이터 생성 시 `AbilityTask`를 종료하지 않아야 합니다. |

모든 EGameplayTargetingConfirmation::Type이 모든 `TargetActor`에서 지원되는 것은 아닙니다. 예를 들어, `AGameplayAbilityTargetActor_GroundTrace`는 `Instant` 확인을 지원하지 않습니다.

`WaitTargetData` `AbilityTask`는 매개변수로 `AGameplayAbilityTargetActor` 클래스를 받아 `AbilityTask`가 활성화될 때마다 인스턴스를 스폰하고 `AbilityTask`가 종료될 때 `TargetActor`를 파괴합니다. `WaitTargetDataUsingActor` `AbilityTask`는 이미 스폰된 `TargetActor`를 받지만, 여전히 `AbilityTask`가 종료될 때 이를 파괴합니다. 이 두 `AbilityTasks`는 매 사용마다 새로운 `TargetActor`를 스폰하거나 요구하기 때문에 비효율적입니다. 프로토타입에는 좋지만, 자동 소총처럼 지속적으로 `TargetData`를 생성하는 경우에는 프로덕션에서 최적화를 고려해야 할 수 있습니다. GASShooter는 [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h)의 사용자 정의 서브 클래스와 새로운 [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask`를 처음부터 작성하여 `TargetActor`를 파괴하지 않고 재사용할 수 있게 했습니다.

`TargetActors`는 기본적으로 복제되지 않습니다. 하지만 게임에서 다른 플레이어에게 로컬 플레이어가 어디를 타겟팅하고 있는지 보여주는 것이 의미가 있다면 복제되도록 만들 수 있습니다. `WaitTargetData` `AbilityTask`에서 RPCs를 통해 서버와 통신하는 기본 기능을 포함하고 있습니다. `TargetActor`의 `ShouldProduceTargetDataOnServer` 속성이 `false`로 설정되어 있으면, 클라이언트는 확인 시 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`의 `CallServerSetReplicatedTargetData()`를 통해 `TargetData`를 서버로 RPC합니다. `ShouldProduceTargetDataOnServer`가 `true`이면, 클라이언트는 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`에서 일반 확인 이벤트인 `EAbilityGenericReplicatedEvent::GenericConfirm` RPC를 서버로 보내고, 서버는 RPC를 받으면 서버에서 데이터를 생성하기 위해 트레이스나 오버랩 체크를 수행합니다. 클라이언트가 타겟팅을 취소하면, `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`에서 일반 취소 이벤트인 `EAbilityGenericReplicatedEvent::GenericCancel` RPC를 서버로 보냅니다. 보시다시피, `TargetActor`와 `WaitTargetData` `AbilityTask` 모두에 많은 델리게이트가 있습니다. `TargetActor`는 입력에 응답하여 `TargetData` ready, confirm, 또는 cancel 델리게이트를 생성하고 브로드캐스트합니다. `WaitTargetData`는 `TargetActor`의 `TargetData` ready, confirm, cancel 델리게이트를 수신하여 그 정보를 `GameplayAbility`와 서버로 전달합니다. 서버로 `TargetData`를 보내는 경우, 치팅을 방지하기 위해 서버에서 `TargetData`가 타당한지 검증하는 것을 고려할 수 있습니다. 서버에서 직접 `TargetData`를 생성하면 이 문제를 완전히 피할 수 있지만, 소유 클라이언트의 예측 오류로 이어질 수 있습니다.

사용하는 `AGameplayAbilityTargetActor`의 특정 서브 클래스에 따라, `WaitTargetData` `AbilityTask` 노드에서 다른 `ExposeOnSpawn` 매개변수가 노출됩니다. 일반적인 매개변수는 다음과 같습니다:

| 일반적인 `TargetActor` 매개변수 | 정의 |
| ------------------------------- | ---- |
| Debug | `true`이면, 비 배포 빌드에서 `TargetActor`가 트레이스를 수행할 때마다 디버그 트레이싱/오버랩 정보를 그립니다. 비-`Instant` `TargetActor`는 `Tick()`에서 트레이스를 수행하므로 이러한 디버그 드로우 호출도 `Tick()`에서 발생한다는 점을 기억하세요. |
| Filter | [선택사항] 트레이스/오버랩이 발생할 때 타겟에서 `Actors`를 필터링(제거)하기 위한 특별한 구조체입니다. 일반적인 사용 사례는 플레이어의 `Pawn`을 필터링하거나, 특정 클래스의 타겟만 요구하는 것입니다. 더 고급 사용 사례는 [Target Data Filters](#concepts-target-data-filters)를 참조하세요. |
| Reticle Class | [선택사항] `TargetActor`가 스폰할 `AGameplayAbilityWorldReticle`의 서브클래스입니다. |
| Reticle Parameters | [선택사항] 레티클을 구성합니다. [Reticles](#concepts-targeting-reticles)를 참조하세요. |
| Start Location | 트레이싱이 시작되어야 하는 위치를 위한 특별한 구조체입니다. 일반적으로 플레이어의 시점, 무기 총구, 또는 `Pawn`의 위치가 될 것입니다. |

기본 `TargetActor` 클래스에서, `Actor`는 트레이스/오버랩에 직접적으로 있을 때만 유효한 타겟입니다. 트레이스/오버랩에서 벗어나면(이동하거나 시선을 돌리면) 더 이상 유효하지 않습니다. `TargetActor`가 마지막 유효한 타겟(들)을 기억하게 하려면, 사용자 정의 `TargetActor` 클래스에 이 기능을 추가해야 합니다. 이를 지속적인 타겟이라고 부르는데, `TargetActor`가 확인이나 취소를 받거나, `TargetActor`가 트레이스/오버랩에서 새로운 유효한 타겟을 찾거나, 타겟이 더 이상 유효하지 않을 때(파괴됨)까지 지속됩니다. GASShooter는 로켓 런처의 보조 어빌리티의 유도 로켓 타겟팅을 위해 지속적인 타겟을 사용합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 타겟 데이터 필터 (Target Data Filter)
`Make GameplayTargetDataFilter`와 `Make Filter Handle` 노드를 사용하여 플레이어의 `Pawn`을 필터링하거나 특정 클래스만 선택할 수 있습니다. 더 고급 필터링이 필요한 경우, `FGameplayTargetDataFilter`를 상속받고 `FilterPassesForActor` 함수를 재정의할 수 있습니다.

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** 액터가 필터를 통과하고 타겟이 될 수 있으면 true를 반환합니다 */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

하지만 이는 `FGameplayTargetDataFilterHandle`을 필요로 하는 `Wait Target Data` 노드에서 직접 작동하지 않습니다. 서브클래스를 받아들이는 새로운 사용자 정의 `Make Filter Handle`을 만들어야 합니다:
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 게임플레이 어빌리티 월드 조준점 (Gameplay Ability World Reticle)
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticles`)는 비-`Instant` 확인 [`TargetActor`](#concepts-targeting-actors)로 타겟팅할 때 **누구**를 타겟팅하고 있는지 시각화합니다. `TargetActor`는 모든 `Reticle`의 스폰과 파괴 수명 주기를 담당합니다. `Reticle`는 `AActor`이므로 표현을 위해 어떤 종류의 시각적 컴포넌트든 사용할 수 있습니다. [GASShooter](https://github.com/tranek/GASShooter)에서 볼 수 있는 일반적인 구현은 화면 공간(항상 플레이어의 카메라를 향함)에 UMG 위젯을 표시하기 위해 `WidgetComponent`를 사용하는 것입니다. `Reticle`는 자신이 어떤 `AActor` 위에 있는지 모르지만, 사용자 정의 `TargetActor`에서 이 기능을 상속받아 추가할 수 있습니다. `TargetActor`는 일반적으로 매 `Tick()`마다 `Reticle`의 위치를 타겟의 위치로 업데이트합니다.

GASShooter는 로켓 런처의 보조 어빌리티의 유도 로켓을 위해 록온된(Lock-on) 타겟을 보여주는 데 `Reticle`를 사용합니다. 적에 표시되는 빨간 표시가 `Reticle`입니다. 비슷한 흰색 이미지는 로켓 런처의 크로스헤어입니다.
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticle`는 디자이너를 위한 몇 가지 `BlueprintImplementableEvent`를 제공합니다(블루프린트에서 개발되도록 의도됨):

```c++
/** bIsTargetValid 값이 변경될 때마다 호출됩니다. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** bIsTargetAnActor 값이 변경될 때마다 호출됩니다. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticle`는 선택적으로 `TargetActor`가 제공하는 [`FWorldReticleParameter`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html)를 구성에 사용할 수 있습니다. 기본 구조체는 하나의 변수 `FVector AOEScale`만 제공합니다. 이 구조체를 기술적으로 상속받을 수 있지만, `TargetActor`는 기본 구조체만 받아들입니다. 기본 `TargetActor`에서 이를 상속받을 수 없게 한 것은 다소 근시안적으로 보입니다. 하지만 자신만의 사용자 정의 `TargetActor`를 만든다면, 자신만의 사용자 정의 레티클 매개변수 구조체를 제공하고 `AGameplayAbilityWorldReticle`의 서브 클래스를 스폰할 때 수동으로 전달할 수 있습니다.

`Reticle`는 기본적으로 복제되지 않지만, 게임에서 다른 플레이어가 로컬 플레이어가 누구를 타겟팅하고 있는지 보여주는 것이 의미가 있다면 복제되도록 만들 수 있습니다.

기본 `TargetActor`에서 `Reticle`는 현재 유효한 타겟에만 표시됩니다. 예를 들어, `AGameplayAbilityTargetActor_SingleLineTrace`를 사용하여 타겟을 추적하는 경우, `Reticle`은 적이 추적 경로에 직접 있을 때만 나타납니다. 시선을 돌리면 적은 더 이상 유효한 타겟이 아니며 `Reticle`은 사라집니다. 마지막 유효한 타겟에 `Reticle`이 유지되기를 원한다면, `TargetActor`를 커스터마이즈하여 마지막 유효한 타겟을 기억하고 그들에게 `Reticle`을 유지하도록 해야 합니다. 이를 지속적인 타겟이라고 부르는데, `TargetActor`가 확인이나 취소를 받거나, `TargetActor`가 트레이스/오버랩에서 새로운 유효한 타겟을 찾거나, 타겟이 더 이상 유효하지 않을 때(파괴됨)까지 지속됩니다. GASShooter는 로켓 런처의 보조 어빌리티의 유도 로켓 타겟팅을 위해 지속적인 타겟을 사용합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 게임플레이 이펙트 컨테이너 타겟팅 (Gameplay Effect Container Targeting)
[`GameplayEffectContainers`](#concepts-ge-containers)는 [`TargetData`](#concepts-targeting-data)를 생성하는 선택적이고 효율적인 방법을 제공합니다. 이 타겟팅은 클라이언트와 서버에서 `EffectContainer`가 적용될 때 즉시 발생합니다. 타겟팅 객체의 CDO에서 실행되기 때문에(액터의 스폰과 파괴가 없음) [`TargetActors`](#concepts-targeting-actors)보다 더 효율적이지만, 플레이어 입력이 없고, 확인이 필요 없이 즉시 발생하며, 취소할 수 없고, 클라이언트에서 서버로 데이터를 보낼 수 없습니다(양쪽 모두에서 데이터 생성). 즉각적인 트레이스와 콜리전 오버랩에 잘 작동합니다. Epic의 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)는 컨테이너와 함께 두 가지 예시 타겟팅 타입을 포함합니다 - 어빌리티 소유자를 타겟팅하고 이벤트에서 `TargetData`를 가져옵니다. 또한 플레이어로부터 일정 오프셋(자식 블루프린트 클래스에서 설정)에서 즉각적인 구체 트레이스를 수행하는 블루프린트도 구현합니다. C++나 블루프린트에서 `URPGTargetType`을 상속받아 자신만의 타겟팅 타입을 만들 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae"></a>
## 5. 일반적으로 구현되는 어빌리티와 이펙트

<a name="cae-stun"></a>
### 5.1 스턴
일반적으로 스턴에서는 `Character`의 모든 활성 `GameplayAbilities`를 취소하고, 새로운 `GameplayAbility` 활성화를 방지하며, 스턴 지속시간 동안 이동을 막고 싶습니다. 샘플 프로젝트의 메테오 `GameplayAbility`는 타격된 대상에게 스턴을 적용합니다.

대상의 활성 `GameplayAbilities`를 취소하기 위해, 스턴 [`GameplayTag`가 추가될 때](#concepts-gt-change) `AbilitySystemComponent->CancelAbilities()`를 호출합니다.

스턴 상태에서 새로운 `GameplayAbilities`가 활성화되는 것을 방지하기 위해, `GameplayAbilities`의 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags)에 스턴 `GameplayTag`가 주어집니다.

스턴 상태에서 이동을 방지하기 위해, 소유자가 스턴 `GameplayTag`를 가질 때 0을 반환하도록 `CharacterMovementComponent`의 `GetMaxSpeed()` 함수를 재정의합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 달리기
샘플 프로젝트는 달리기 구현 예시를 제공합니다 - `Left Shift`를 누르고 있는 동안 더 빠르게 달립니다.

더 빠른 이동은 네트워크를 통해 서버로 플래그를 전송하여 `CharacterMovementComponent`에서 예측적으로 처리됩니다. 자세한 내용은 `GDCharacterMovementComponent.h/cpp`를 참조하세요.

`GA`는 `Left Shift` 입력에 응답하고, `CMC`에 달리기 시작과 중지를 알리며, `Left Shift`가 눌려있는 동안 예측적으로 스태미나를 소모합니다. 자세한 내용은 `GA_Sprint_BP`를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 조준 모드(ADS, Aim Down Sight)
샘플 프로젝트는 이를 달리기와 정확히 같은 방식으로 처리하지만 이동 속도를 증가시키는 대신 감소시킵니다.

이동 속도를 예측적으로 감소시키는 방법에 대한 자세한 내용은 `GDCharacterMovementComponent.h/cpp`를 참조하세요.

입력 처리에 대한 자세한 내용은 `GA_AimDownSight_BP`를 참조하세요. 조준 모드에는 스태미나 소모가 없습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 생명력 흡수
생명력 흡수는 데미지 [`ExecutionCalculation`](#concepts-ge-ec) 내에서 처리합니다. `GameplayEffect`는 `Effect.CanLifesteal`과 같은 `GameplayTag`를 가집니다. `ExecutionCalculation`은 `GameplayEffectSpec`이 해당 `Effect.CanLifesteal` `GameplayTag`를 가지고 있는지 확인합니다. `GameplayTag`가 존재하면, `ExecutionCalculation`은 회복할 체력량을 모디파이어로 하는 [Dynamic `Instant` `GameplayEffect`를 생성](#concepts-ge-dynamic)하고 이를 `Source`의 `ASC`에 적용합니다.

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 클라이언트와 서버에서 난수 생성하기
때때로 총기 반동이나 탄 퍼짐과 같은 것들을 위해 `GameplayAbility` 내에서 "난수"를 생성해야 할 필요가 있습니다. 클라이언트와 서버 모두 동일한 난수를 생성해야 합니다. 이를 위해서는 `GameplayAbility` 활성화 시점에 `random seed`를 동일하게 설정해야 합니다. 클라이언트가 활성화를 잘못 예측하여 난수 시퀀스가 서버와 동기화되지 않는 경우에 대비하여 `GameplayAbility`를 활성화할 때마다 `random seed`를 설정해야 합니다.

| Seed 설정 방법 | 설명 |
|----------------|------|
| 활성화 예측 키 사용 (Use the activation prediction key) | `GameplayAbility` 활성화 예측 키는 `Activation()` 내에서 클라이언트와 서버 모두에서 동기화되고 사용 가능한 것이 보장된 int16입니다. 이를 클라이언트와 서버 모두에서 `random seed`로 설정할 수 있습니다. 이 방법의 단점은 예측 키가 게임이 시작될 때마다 항상 0에서 시작하고 키 생성 사이에 사용할 값을 일관되게 증가시킨다는 것입니다. 이는 각 매치가 정확히 같은 난수 시퀀스를 가지게 된다는 것을 의미합니다. 이는 당신의 필요에 따라 충분히 무작위적일 수도 있고 아닐 수도 있습니다. |
| `GameplayAbility` 활성화 시 이벤트 페이로드를 통해 시드 전송 | 이벤트로 `GameplayAbility`를 활성화하고 복제된 이벤트 페이로드를 통해 클라이언트에서 서버로 무작위로 생성된 시드를 전송합니다. 이는 더 많은 무작위성을 허용하지만 클라이언트가 쉽게 게임을 해킹하여 매번 같은 시드 값만 보낼 수 있습니다. 또한 이벤트로 `GameplayAbilities`를 활성화하면 입력 바인딩으로부터 활성화되는 것을 막습니다. |

만약 무작위 편차가 작다면, 대부분의 플레이어들은 시퀀스가 매 게임마다 같다는 것을 알아채지 못할 것이고 활성화 예측 키를 `random seed`로 사용하는 것이 적절할 것입니다. 해커로부터 보호해야 하는 더 복잡한 작업을 하고 있다면, 서버가 예측 키를 생성하거나 이벤트 페이로드를 통해 `random seed`를 생성할 수 있는 `Server Initiated` `GameplayAbility`를 사용하는 것이 더 좋을 수 있습니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 치명타
치명타는 데미지 [`ExecutionCalculation`](#concepts-ge-ec) 내에서 처리합니다. `GameplayEffect`는 `Effect.CanCrit`과 같은 `GameplayTag`를 가집니다. `ExecutionCalculation`은 `GameplayEffectSpec`이 해당 `Effect.CanCrit` `GameplayTag`를 가지고 있는지 확인합니다. `GameplayTag`가 존재하면, `ExecutionCalculation`은 치명타 확률(`Source`에서 캡처된 `Attribute`)에 해당하는 난수를 생성하고 성공했다면 치명타 데미지(역시 `Source`에서 캡처된 `Attribute`)를 추가합니다. 데미지를 예측하지 않기 때문에, `ExecutionCalculation`은 서버에서만 실행되므로 클라이언트와 서버의 난수 생성기를 동기화할 필요가 없습니다. 데미지 계산을 위해 `MMC`를 사용하여 이를 예측적으로 수행하려고 했다면, `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`에서 `random seed`의 참조를 가져와야 했을 것입니다.

[GASShooter](https://github.com/tranek/GASShooter)가 헤드샷을 어떻게 처리하는지 참조하세요. 확률을 위한 난수에 의존하지 않고 대신 `FHitResult` 본 이름을 확인한다는 점을 제외하면 같은 개념입니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 중첩되지 않는 Gameplay Effect지만 가장 큰 크기만 실제로 대상에 영향을 미치는 경우 (Non-Stacking Gameplay Effects but Only the 
Paragon의 감속 효과는 중첩되지 않았습니다. 각 감속 인스턴스는 정상적으로 적용되고 지속시간을 추적했지만, 가장 큰 크기의 감속 효과만 실제로 `Character`에 영향을 미쳤습니다. GAS는 `AggregatorEvaluateMetaData`로 이 시나리오를 기본적으로 제공합니다. 자세한 내용과 구현은 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 게임이 일시정지된 상태에서 Target Data 생성
플레이어로부터 `WaitTargetData` `AbilityTask`에서 [`TargetData`](#concepts-targeting-data)를 생성하기를 기다리는 동안 게임을 일시정지해야 한다면, 일시정지 대신 `slomo 0`을 사용하는 것을 추천합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 단일 버튼 상호작용 시스템 (One Button Interaction System)
[GASShooter](https://github.com/tranek/GASShooter)는 플레이어가 'E'를 누르거나 길게 눌러 플레이어 부활, 무기 상자 열기, 미닫이문 열고 닫기와 같은 상호작용 가능한 오브젝트와 상호작용할 수 있는 단일 버튼 상호작용 시스템을 구현합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="debugging"></a>
## 6. GAS 디버깅
GAS 관련 문제를 디버깅할 때, 다음과 같은 것들을 알고 싶을 것입니다:
> * "내 Attributes의 값은 무엇인가?"
> * "어떤 GameplayTag들을 가지고 있는가?"
> * "현재 어떤 GameplayEffect들을 가지고 있는가?"
> * "어떤 어빌리티들이 부여되었고, 어떤 것들이 실행 중이며, 어떤 것들이 활성화되지 못하도록 막혀있는가?"

GAS comes with two techniques for answering these questions at runtime - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).
GAS는 이러한 질문들에 대한 답을 런타임에 확인할 수 있는 두 가지 기술을 제공합니다 - [`showdebug abilitysystem`](#debugging-sd)과 [`GameplayDebugger`](#debugging-gd)의 후크들입니다.

**팁:** 언리얼 엔진은 C++ 코드를 최적화하는 것을 좋아하는데, 이는 일부 함수들을 디버깅하기 어렵게 만듭니다. 코드를 깊이 추적할 때 드물게 이런 상황을 만날 것입니다. Visual Studio 솔루션 구성을 `DebugGame Editor`로 설정해도 코드 추적이나 변수 검사가 안 된다면, `UE_DISABLE_OPTIMIZATION`과 `UE_ENABLE_OPTIMIZATION` 매크로나 CoreMiscDefines.h에 정의된 ship 변형을 사용하여 최적화된 함수를 감싸서 모든 최적화를 비활성화할 수 있습니다. 소스로부터 플러그인을 재빌드하지 않는 한 플러그인 코드에는 사용할 수 없습니다. 인라인 함수의 경우 그들이 하는 일과 위치에 따라 작동할 수도 있고 안 할 수도 있습니다. 디버깅이 끝나면 매크로를 제거하는 것을 잊지 마세요!

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// 내 코드
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
게임 내 콘솔에 `showdebug abilitysystem`을 입력하세요. 이 기능은 세 개의 "페이지"로 나뉘어 있습니다. 세 페이지 모두 현재 가지고 있는 `GameplayTag`를 보여줍니다. 페이지 간 전환을 위해 콘솔에 `AbilitySystem.Debug.NextCategory`를 입력하세요.

첫 번째 페이지는 모든 `Attribute`의 `CurrentValue`를 보여줍니다:
![showdebug abilitysystem의 첫 번째 페이지](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

두 번째 페이지는 당신에게 적용된 모든 `Duration`과 `Infinite` `GameplayEffect`, 그들의 스택 수, 그들이 주는 `GameplayTag`, 그리고 그들이 주는 `Modifier`를 보여줍니다.
![showdebug abilitysystem의 두 번째 페이지](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

세 번째 페이지는 당신에게 부여된 모든 `GameplayAbility`, 현재 실행 중인지 여부, 활성화가 차단되었는지 여부, 그리고 현재 실행 중인 `AbilityTask`의 상태를 보여줍니다.
![showdebug abilitysystem의 세 번째 페이지](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

타겟들 사이를 전환하기 위해(Actor 주변의 녹색 직육면체로 표시됨), `PageUp` 키나 `NextDebugTarget` 콘솔 명령어를 사용하여 다음 타겟으로, `PageDown` 키나 `PreviousDebugTarget` 콘솔 명령어를 사용하여 이전 타겟으로 이동할 수 있습니다.

**참고:** 어빌리티 시스템 정보가 현재 선택된 디버그 Actor를 기반으로 업데이트되려면, `DefaultGame.ini`에서 다음과 같이 `AbilitySystemGlobals`에 `bUseDebugTargetFromHud=true`를 설정해야 합니다:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**참고:** `showdebug abilitysystem`이 작동하려면 GameMode에서 실제 HUD 클래스가 선택되어 있어야 합니다. 그렇지 않으면 명령어를 찾을 수 없어 "Unknown Command"가 반환됩니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 게임 플레이 디버거 (Gameplay Debugger)
GAS는 Gameplay Debugger에 기능을 추가합니다. Gameplay Debugger는 아포스트로피(') 키로 접근할 수 있습니다. 숫자패드의 3을 눌러 Abilities 카테고리를 활성화하세요. 플러그인에 따라 카테고리가 다를 수 있습니다. 노트북처럼 숫자패드가 없는 키보드라면, 프로젝트 설정에서 키 바인딩을 변경할 수 있습니다.

**다른** `Characters`의 `GameplayTag`, `GameplayEffect`, 그리고 `GameplayAbility`를 보고 싶을 때 Gameplay Debugger를 사용하세요. 안타깝게도 타겟의 `Attributes`의 `CurrentValue`는 보여주지 않습니다. 화면 중앙에 있는 `Character`를 타겟팅할 것입니다. 에디터의 World Outliner에서 선택하거나 다른 `Character`를 보고 아포스트로피(') 키를 다시 눌러 타겟을 변경할 수 있습니다. 현재 검사 중인 `Character` 위에 가장 큰 빨간 원이 표시됩니다.

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS 로그
GAS 소스 코드는 다양한 상세도 수준에서 생성되는 많은 로깅 구문을 포함하고 있습니다. 이러한 것들을 `ABILITY_LOG()` 구문으로 볼 수 있을 것입니다. 기본 상세도 수준은 `Display`입니다. 그 이상의 상세도는 기본적으로 콘솔에 표시되지 않습니다.

로그 카테고리의 상세도 수준을 변경하려면 콘솔에 다음을 입력하세요:

```
log [category] [verbosity]
```

예를 들어, `ABILITY_LOG()` 구문을 켜려면 콘솔에 다음을 입력하면 됩니다:
```
log LogAbilitySystem VeryVerbose
```

기본값으로 되돌리려면 다음을 입력하세요:
```
log LogAbilitySystem Display
```

모든 로그 카테고리를 표시하려면 다음을 입력하세요:
```
log list
```

주목할 만한 GAS 관련 로깅 카테고리:

| 로깅 카테고리 | 기본 상세도 수준 |
| ------------- | --------------- |
| LogAbilitySystem | Display |
| LogAbilitySystemComponent | Log |
| LogGameplayCueDetails | Log |
| LogGameplayCueTranslator | Display |
| LogGameplayEffectDetails | Log |
| LogGameplayEffects | Display |
| LogGameplayTags | Log |
| LogGameplayTasks | Log |
| VLogAbilitySystem | Display |

더 자세한 정보는 [로그에 대한 위키](https://unrealcommunity.wiki/logging-lgpidy6i)를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="optimizations"></a>
## 7. 최적화

<a name="optimizations-abilitybatching"></a>
### 7.1 어빌리티 배칭 (Ability Batching)
한 프레임에 활성화되고, 선택적으로 서버에 `TargetData`를 보내고, 종료되는 [`GameplayAbility`](#concepts-ga)는 [2-3개의 RPC를 하나의 RPC로 묶을 수 있습니다](#concepts-ga-batching). 이러한 유형의 어빌리티는 일반적으로 히트스캔 총에 사용됩니다.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 게임플레이 큐 배칭 (Gameplay Cue Batching)
동시에 많은 [`GameplayCue`](#concepts-gc)를 보내는 경우, [하나의 RPC로 배칭하는 것](#concepts-gc-batching)을 고려하세요. 목표는 RPC의 수를 줄이고(`GameplayCue`는 신뢰할 수 없는 NetMulticast임) 가능한 한 적은 데이터를 보내는 것입니다.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 어빌리티 시스템 컴포넌트 리플리케이션 모드 (AbilitySystemComponent Replication Mode)
기본적으로, [`ASC`](#concepts-asc)는 [`Full Replication Mode`](#concepts-asc-rm)입니다. 이는 모든 [`GameplayEffect`](#concepts-ge)를 모든 클라이언트에 복제합니다(싱글 플레이어 게임에서는 괜찮습니다). 멀티플레이어 게임에서는 플레이어가 소유한 `ASC`를 `Mixed Replication Mode`로, AI가 제어하는 캐릭터를 `Minimal Replication Mode`로 설정하세요. 이렇게 하면 플레이어 캐릭터에 적용된 `GE`는 해당 캐릭터의 소유자에게만 복제되고, AI가 제어하는 캐릭터에 적용된 `GE`는 클라이언트에 절대 복제되지 않습니다. [`GameplayTag`](#concepts-gt)는 여전히 복제되고 [`GameplayCue`](#concepts-gc)는 여전히 `Replication Mode`와 관계없이 모든 클라이언트에 신뢰할 수 없는 NetMulticast로 전송됩니다. 이는 모든 클라이언트가 볼 필요가 없는 `GE`가 복제되는 네트워크 데이터를 줄일 것입니다.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 어트리뷰트 프록시 리플리케이션 (Attribute Proxy Replication)
Fortnite Battle Royale(FNBR)과 같이 많은 플레이어가 있는 대규모 게임에서는 항상 관련이 있는 `PlayerState`에 많은 [`ASC`](#concepts-asc)가 있어 많은 [`Attribute`](#concepts-a)를 복제할 것입니다. 이 병목 현상을 최적화하기 위해, Fortnite는 `PlayerState::ReplicateSubobjects()`에서 **시뮬레이트된 플레이어 제어 프록시(Simulated player-controlled proxy)**에서 `ASC`와 그의 [`AttributeSet`](#concepts-as)의 복제를 완전히 비활성화합니다. 자율 프록시와 AI가 제어하는 `Pawn`는 여전히 그들의 [`Replication Mode`](#concepts-asc-rm)에 따라 완전히 복제됩니다. 항상 관련이 있는 `PlayerState`의 `ASC`에서 `Attribute`를 복제하는 대신, FNBR은 플레이어의 `Pawn`에서 복제된 프록시 구조체를 사용합니다. 서버의 `ASC`에서 `Attribute`가 변경되면, 프록시 구조체에서도 변경됩니다. 클라이언트는 프록시 구조체에서 복제된 `Attribute`를 받아 그 변경사항을 로컬 `ASC`로 다시 푸시합니다. 이를 통해 `Attribute` 복제가 `Pawn`의 관련성과 `NetUpdateFrequency`를 사용할 수 있습니다. 이 프록시 구조체는 또한 비트마스크에서 작은 화이트리스트 `GameplayTag` 세트를 복제합니다. 이 최적화는 네트워크 상의 데이터 양을 줄이고 pawn 관련성을 활용할 수 있게 해줍니다. AI가 제어하는 `Pawn`는 그들의 `ASC`가 이미 그들의 관련성을 사용하는 `Pawn`에 있으므로 이 최적화가 필요하지 않습니다.

> 그 이후로 수행된 다른 서버 측 최적화들(Replication Graph 등)로 인해 여전히 필요한지는 확실하지 않으며, 가장 유지보수하기 좋은 패턴은 아닙니다.

*Epic의 Dave Ratti가 [커뮤니티 질문 #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)에 답변한 내용*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC 지연 로딩 (ASC Lazy Loading)
Fortnite Battle Royale(FNBR)에는 각각 [`ASC`](#concepts-asc)를 가진 많은 데미지를 받을 수 있는 `AActor`(나무, 건물 등)가 월드에 있습니다. 이는 메모리 비용이 증가할 수 있습니다. FNBR은 필요할 때만(플레이어에 의해 처음 데미지를 받을 때) `ASC`를 지연 로딩하여 이를 최적화합니다. 일부 `AActor`는 매치에서 절대 데미지를 받지 않을 수 있으므로 전체 메모리 사용량이 줄어듭니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="qol"></a>
## 8. 품질 개선 제안

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 게임 플레이 이펙트 컨테이너 (Gameplay Effect Container)
[GameplayEffectContainer](#concepts-ge-containers)는 [`GameplayEffectSpec`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [간단한 타겟팅](#concepts-targeting-containers), 그리고 관련 기능들을 사용하기 쉬운 구조체로 결합합니다. 이는 어빌리티에서 생성된 발사체에 `GameplayEffectSpec`를 전달하여 나중에 충돌 시 적용하는 데 매우 유용합니다.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 ASC 델리게이트에 바인딩하기 위한 블루프린트 AsyncTask (Blueprint AsyncTasks to Bind to ASC Delegates)
특히 UI를 위한 UMG 위젯을 디자인할 때 디자이너 친화적인 반복 시간을 늘리기 위해, UMG 블루프린트 그래프에서 직접 `ASC`의 일반적인 변경 델리게이트에 바인딩하기 위한 블루프린트 AsyncTask(C++에서)를 생성하세요. 유일한 주의사항은 (위젯이 파괴될 때처럼) 수동으로 파괴해야 한다는 것입니다. 그렇지 않으면 메모리에 영원히 남아있게 됩니다. 샘플 프로젝트는 세 가지 블루프린트 AsyncTask를 포함합니다.

`Attribute` 변경 수신:

![Attributes 변경 수신 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

쿨다운 변경 수신:

![쿨다운 변경 수신 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

`GE` 스택 변경 수신:

![GameplayEffect 스택 변경 수신 BP 노드](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. 트러블슈팅

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
[클라이언트에서 `ASC`를 초기화](#concepts-asc-setup)해야 합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` 오류
[`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata)를 호출해야 합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 애니메이션 몽타주가 클라이언트에 복제되지 않음
[GameplayAbilities](#concepts-ga)에서 `PlayMontage` 대신 `PlayMontageAndWait` 블루프린트 노드를 사용하고 있는지 확인하세요. 이 [AbilityTask](#concepts-at)는 `PlayMontage` 노드와 달리 몽타주를 `ASC`를 통해 자동으로 복제합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 블루프린트 액터 복제 시 AttributeSet이 nullptr로 설정됨
기존 블루프린트 액터 클래스에서 복제된 블루프린트 액터 클래스의 경우 클래스의 `AttributeSet` 포인터를 nullptr로 설정하는 [언리얼 엔진의 버그](https://issues.unrealengine.com/issue/UE-81109)가 있습니다. 이에 대한 몇 가지 해결 방법이 있습니다. 클래스에 맞춤형 `AttributeSet` 포인터를 만들지 않고(.h에 포인터 없음, 생성자에서 `CreateDefaultSubobject` 호출하지 않음) 대신 `PostInitializeComponents()`에서 `AttributeSets`를 직접 `ASC`에 추가하는 방법이 효과적이었습니다(샘플 프로젝트에는 표시되지 않음). 복제된 `AttributeSet`는 여전히 `ASC`의 `SpawnedAttribute` 배열에 있을 것입니다. 다음과 같이 보일 것입니다:

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... 가지고 있을 수 있는 다른 AttributeSet들
	}
}
```

이 시나리오에서는 [매크로로 만든 `AttributeSet`의 함수를 호출](#concepts-as-attributes)하는 대신 `ASC`의 함수를 사용하여 `AttributeSet`의 값을 읽고 설정합니다.

```c++
/** Attribute의 현재(최종) 값을 반환합니다 */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Attribute의 기본 값을 설정합니다. 기존의 Active Modifier는 제거되지 않고 새로운 기본 값에 적용됩니다. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

따라서 `GetHealth()`는 다음과 같이 보일 것입니다:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

체력 `Attribute` 설정(초기화)는 다음과 같이 보일 것입니다:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

참고로, `ASC`는 `AttributeSet` 클래스당 최대 하나의 `AttributeSet` 객체만 기대합니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 Unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)

다음과 같은 컴파일러 에러가 발생하는 경우:

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

이는 `FFastArraySerializer`에서 `MarkItemDirty()`를 호출하려고 할 때 발생합니다. 쿨다운 지속시간을 업데이트할 때처럼 `ActiveGameplayEffect`를 업데이트하면서 이 문제를 만났습니다.

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

이는 `WITH_PUSH_MODEL`이 여러 곳에서 정의되어 있기 때문에 발생합니다. `PushModelMacros.h`는 이를 0으로 정의하고 있지만 여러 곳에서 1로 정의되어 있습니다. `PushModel.h`는 이를 1로 보고 있지만 `PushModel.cpp`는 0으로 보고 있습니다.

해결책은 프로젝트의 `Build.cs`의 `PublicDependencyModuleNames`에 `NetCore`를 추가하는 것입니다.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>
### 9.6 열거형 이름이 이제 경로 이름으로 표현됨 (Enum names are now represented by path name)

다음과 같은 컴파일러 경고가 발생하는 경우:

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE 5.1에서는 `BindAbilityActivationToInputComponent()`의 생성자에서 `FString` 사용이 deprecated되었습니다. 대신 `FTopLevelAssetPath`를 전달해야 합니다.

이전의 deprecated된 방식:
```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

새로운 방식:
```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

자세한 내용은 `Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h`를 참조하세요.

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="acronyms"></a>
## 10. 일반적인 GAS 약어

| 이름 | 약어 |
|------|------|
| AbilitySystemComponent | ASC |
| AbilityTask | AT |
| [Epic의 Action RPG 샘플 프로젝트](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample |
| CharacterMovementComponent | CMC |
| GameplayAbility | GA |
| GameplayAbilitySystem | GAS |
| GameplayCue | GC |
| GameplayEffect | GE |
| GameplayEffectExecutionCalculation | ExecCalc, Execution |
| GameplayTag | Tag, GT |
| ModifierMagnitudeCalculation | ModMagCalc, MMC |

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="resources"></a>
## 11. 다른 자료들
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Lyra Sample Project by Epic](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Epic Games의 Dave Ratti와의 Q&A

<a name="resources-daveratti-community1"></a>
#### 11.1.1 커뮤니티 질문 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. `GameplayAbilities` 외부에서 또는 관계없이 필요할 때 범위가 지정된 예측 윈도우(Scoped prediction window)를 어떻게 생성할 수 있나요? 예를 들어, 발사 후 잊어버리는 발사체가 적에게 맞았을 때 데미지 `GameplayEffect`를 로컬에서 어떻게 예측할 수 있나요?

> PredictionKey 시스템은 이를 위한 것이 아닙니다. 근본적으로 이 시스템은 클라이언트가 예측적 행동을 시작하고, 서버에 키와 함께 이를 알리며, 그런 다음 클라이언트와 서버 모두 동일한 것을 실행하고 예측적 부작용을 주어진 예측 키와 연결하는 방식으로 작동합니다. 예를 들어, "나는 예측적으로 어빌리티를 활성화하고 있다" 또는 "나는 타겟 데이터를 생성했고 WaitTargetData 태스크 이후의 어빌리티 그래프 부분을 예측적으로 실행할 것이다"와 같습니다.
>
> 이 패턴에서, PredictionKey는 서버에서 "튕겨" 클라이언트로 UAbilitySystemComponent::ReplicatedPredictionKeyMap(복제된 속성)을 통해 돌아옵니다. 키가 서버로부터 다시 복제되면, 클라이언트는 모든 로컬 예측적 부작용(GameplayCues, GameplayEffects)을 취소할 수 있습니다: 복제된 버전이 *있을 것이고* 없다면 잘못된 예측이었던 것입니다. 예측적 부작용을 정확히 언제 취소할지 아는 것이 여기서 중요합니다: 너무 일찍하면 간격이 보이고, 너무 늦으면 "두 배"가 됩니다. (이는 지속시간 기반 Gameplay Effect의 반복되는 GameplayCue와 같은 상태 있는 예측을 말합니다. "버스트" GameplayCues와 즉각적인 Gameplay Effects는 절대 "취소"되거나 롤백되지 않습니다. 예측 키가 연결되어 있으면 클라이언트에서 건너뛰기만 합니다).
>
> 핵심을 더 강조하자면: 예측적 행동은 서버가 스스로 하는 것이 아니라 클라이언트가 지시할 때만 수행하는 것이어야 합니다. 따라서 "필요할 때 키를 생성하고 서버에 알려서 무언가를 실행할 수 있게 하는" 일반적인 방식은 그 "무언가"가 서버가 클라이언트의 지시가 있을 때만 수행하는 것이 아니라면 작동하지 않습니다.
>
> 원래 질문으로 돌아가서: 발사 후 잊어버리는 발사체와 같은 것입니다. Paragon과 Fornite 모두 GameplayCue를 사용하는 발사체 액터 클래스를 가지고 있습니다. 하지만 우리는 이를 위해 Prediction Key 시스템을 사용하지 않습니다. 대신 비복제 GameplayCue 개념을 가지고 있습니다. GameplayCue는 로컬에서만 발사되고 서버에서는 완전히 건너뛰어집니다. 근본적으로 이들은 모두 UGameplayCueManager::HandleGameplayCue에 대한 직접 호출입니다. UAbilitySystemComponent를 통해 라우팅되지 않으므로 예측 키 검사/조기 반환이 이루어지지 않습니다.
>
> 비복제 GameplayCue의 단점은, 말 그대로, 복제되지 않는다는 것입니다. 따라서 이러한 함수를 호출하는 코드 경로가 모든 사람에게서 실행되도록 하는 것은 발사체 클래스/블루프린트의 몫입니다. 우리는 시작(BeginPlay에서 호출됨), 폭발, 벽/캐릭터 충돌 등에 대한 큐를 가지고 있습니다.
>
> 이러한 유형의 이벤트는 이미 클라이언트 측에서 생성되므로, 비복제 gameplay cue를 호출하는 것은 큰 문제가 아니었습니다. 복잡한 블루프린트는 까다로울 수 있으며, 어디에서 무엇이 실행되는지 이해하는 것은 작성자의 몫입니다.

2. 로컬에서 예측된 `GameplayAbility`에서 `OnlyServerWait`로 `WaitNetSync` `AbilityTask`를 사용할 때, 서버가 그들의 예측 키를 가진 RPC를 기다리고 있으므로 플레이어가 패킷을 서버로 지연시켜 `GameplayAbility` 타이밍을 제어할 수 있나요? 이것이 Paragon이나 Fortnite에서 문제가 된 적이 있나요? 만약 있었다면, Epic은 이를 어떻게 해결했나요?

> 네, 이는 타당한 우려입니다. 서버에서 실행되는 어빌리티 블루프린트가 클라이언트의 "신호"를 기다리는 것은 잠재적으로 랙 스위치 같은 악용에 취약할 수 있습니다.
>
> Paragon은 UAbilityTask_WaitTargetData와 유사한 커스텀 타겟팅 태스크를 가지고 있었습니다. 이 태스크에서 우리는 즉각적인 타겟팅 모드에 대해 클라이언트가 기다릴 수 있는 타임아웃 또는 "최대 지연"을 가지고 있었습니다. 타겟팅 모드가 사용자 확인(버튼 누름)을 기다리고 있다면 사용자가 시간을 가질 수 있으므로 무시되었습니다. 하지만 즉시 타겟팅을 확인하는 어빌리티의 경우 A) 서버 측에서 타겟 데이터를 생성하거나 B) 어빌리티를 취소하기 전에 특정 시간만 기다렸습니다.
>
> 우리는 아주 드물게 사용했던 WaitNetSync에 대해서는 그런 메커니즘을 가지고 있지 않았습니다.
>
> Fortnite는 이와 같은 것을 사용하지 않는 것으로 알고 있습니다. Fortnite의 무기 어빌리티는 fortnite 특유의 단일 RPC로 배치 처리됩니다: 어빌리티를 활성화하고, 타겟 데이터를 제공하고, 어빌리티를 종료하는 하나의 RPC입니다. 따라서 Battle Royale에서 무기 어빌리티는 본질적으로 이런 취약점이 없습니다.
>
> 제 생각으로는 이것이 시스템 전반적으로 해결될 수 있지만, 우리가 곧 이 변경을 할 것 같지는 않습니다. 당신이 언급한 경우에 대해 최대 지연을 포함하도록 WaitNetSync를 부분적으로 수정하는 것은 아마도 합리적인 작업이겠지만, 다시 말하지만 - 우리 쪽에서 가까운 미래에 이를 할 가능성은 낮습니다.

3. Paragon과 Fortnite는 어떤 `EGameplayEffectReplicationMode`를 사용했고, Epic은 각각을 언제 사용할 것을 추천하나요?

> 두 게임 모두 기본적으로 플레이어가 제어하는 캐릭터에는 Mixed 모드를, AI가 제어하는 캐릭터(AI 미니언, 정글 크립, AI 허스크 등)에는 Minimal을 사용합니다. 이것이 멀티플레이어 게임에서 시스템을 사용하는 대부분의 사람들에게 추천하는 방식입니다. 프로젝트 초기에 이를 설정할수록 좋습니다.
>
> Fortnite는 최적화를 몇 단계 더 진행했습니다. 실제로 시뮬레이트된 프록시에 대해서는 UAbilitySystemComponent를 전혀 복제하지 않습니다. 컴포넌트와 어트리뷰트 서브오브젝트는 포트나이트 플레이어 스테이트 클래스의 ::ReplicateSubobjects() 내에서 건너뜁니다. 우리는 어빌리티 시스템 컴포넌트에서 최소한의 복제 데이터를 폰 자체의 구조체로 푸시합니다(기본적으로 어트리뷰트 값의 서브셋과 비트마스크로 복제되는 화이트리스트 태그의 서브셋입니다). 우리는 이를 "프록시"라고 부릅니다. 수신 측에서 우리는 폰에서 복제된 프록시 데이터를 가져와 플레이어 스테이트의 어빌리티 시스템 컴포넌트로 다시 푸시합니다. 따라서 FNBR의 각 플레이어에 대한 ASC는 있지만, 직접 복제되지는 않습니다: 대신 폰의 최소한의 프록시 구조체를 통해 데이터를 복제하고 수신 측에서 ASC로 다시 라우팅됩니다. 이는 A) 더 적은 데이터 세트이고 B) 폰 관련성을 활용할 수 있다는 점에서 이점이 있습니다.
>
> 이후에 수행된 다른 서버 측 최적화(Replication Graph 등)로 인해 여전히 필요한지는 확실하지 않으며 가장 유지보수하기 좋은 패턴은 아닙니다.

4. `GameplayPrediction.h`에 따르면 `GameplayEffect`의 제거를 예측할 수 없는데, `GameplayEffect` 제거에 대한 지연 시간의 영향을 완화할 전략이 있나요? 예를 들어, 이동 속도 감속을 제거할 때, 현재는 서버가 `GameplayEffect` 제거를 복제하기를 기다려야 하므로 플레이어의 캐릭터 위치가 순간이동하게 됩니다.

> 이는 어려운 문제이고 좋은 답이 없습니다. 우리는 일반적으로 허용 오차와 스무딩으로 이러한 문제를 피해갔습니다. 어빌리티 시스템과 캐릭터 이동 시스템의 정확한 동기화가 좋지 않은 상태이며 수정이 필요하다는 점에 전적으로 동의합니다.
>
> GE의 예측적 제거를 허용하는 작업을 했지만 다른 일로 넘어가기 전에 모든 엣지 케이스를 해결할 수 없었습니다. 하지만 이것도 모든 것을 해결하지는 못합니다. 캐릭터 이동은 여전히 어빌리티 시스템과 가능한 이동 속도 모디파이어 등에 대해 아무것도 모르는 내부 저장된 이동 버퍼를 가지고 있기 때문입니다. GE의 제거를 예측할 수 없는 것 외에도 수정 피드백 루프에 빠질 수 있습니다.
>
> 정말 절박한 경우라고 생각한다면, 이동 속도 GE를 억제할 GE를 예측적으로 추가할 수 있습니다. 나는 이를 직접 해본 적은 없지만 이전에 이론적으로 생각해본 적이 있습니다. 특정 종류의 문제에는 도움이 될 수 있을 것입니다.

5. 우리는 Paragon과 Fortnite에서는 `AbilitySystemComponent`가 `PlayerState`에 있고 Action RPG 샘플에서는 `Character`에 있다는 것을 알고 있습니다. AbilitySystemComponent가 어디에 있어야 하고, 그것의 `Owner`가 무엇이어야 하는지에 대한 Epic의 내부 규칙, 지침 또는 권장사항은 무엇인가요?

> 일반적으로 리스폰할 필요가 없는 것들은 Owner와 Avatar 액터가 같아야 합니다. AI 적, 건물, 월드 소품 등과 같은 것들이 이에 해당합니다.
>
> 리스폰이 필요한 것들은 리스폰 후에 Ability System Component를 저장/재생성/복원할 필요가 없도록 Owner와 Avatar가 달라야 합니다. PlayerState는 모든 클라이언트에 복제되므로(PlayerController는 그렇지 않음) 논리적 선택입니다. 단점은 PlayerStates가 항상 관련이 있어서 100명의 플레이어가 있는 게임에서 문제가 될 수 있다는 것입니다(질문 #3에서 FN이 한 것에 대한 노트 참조).

6. 같은 owner를 가지지만 다른 avatar를 가진 여러 개의 `AbilitySystemComponents`를 가지는 것이 가능한가요? (예: pawn과 weapon/items/projectiles에서 `Owner`를 `PlayerState`로 설정)

> 제가 보는 첫 번째 문제는 IGameplayTagAssetInterface와 IAbilitySystemInterface를 소유 액터에서 구현하는 것입니다. 전자는 가능할 수 있습니다: 모든 ASC의 태그를 집계하면 됩니다(하지만 주의하세요 - HasAllMatchingGameplayTags는 ASC 간 집계를 통해서만 충족될 수 있습니다. 각 ASC에 호출을 전달하고 결과를 OR 연산하는 것만으로는 충분하지 않습니다). 하지만 후자는 더 까다롭습니다: 어떤 ASC가 권위 있는 것인가요? 누군가 GE를 적용하려고 할 때 - 어떤 것이 받아야 하나요? 이러한 문제를 해결할 수 있을 수도 있지만 이 문제의 이 부분이 가장 어려울 것입니다: 여러 ASC를 가진 owners.
>
> 폰과 무기에 별도의 ASC를 두는 것은 그 자체로 의미가 있을 수 있습니다. 예를 들어, 무기를 설명하는 태그와 소유 폰을 설명하는 태그를 구분하는 것입니다. 아마도 무기에 부여된 태그가 소유자에게도 "적용"되고 다른 것은 적용되지 않는 것이 의미가 있을 수 있습니다(예: 어트리뷰트와 GE는 독립적이지만 소유자는 위에서 설명한 대로 소유한 태그를 집계합니다). 이는 잘 작동할 수 있다고 확신합니다. 하지만 같은 소유자를 가진 여러 ASC는 까다로워질 수 있습니다.

7. 로컬에서 예측된 어빌리티의 쿨다운 지속시간을 서버가 Owning Client에서 덮어쓰는 것을 막을 방법이 있나요? 높은 지연 시간의 시나리오에서, 이는 Owning Client가 로컬 쿨다운이 만료되었을 때 서버에서는 여전히 쿨다운 중이더라도 어빌리티를 다시 활성화하려고 "시도"하게 할 수 있습니다. Owning Client의 활성화 요청이 네트워크를 통해 서버에 도달할 때쯤이면, 서버는 쿨다운이 끝났을 수 있거나 서버가 남은 밀리초 동안 활성화 요청을 큐에 넣을 수 있습니다. 지금처럼 지연 시간이 더 높은 클라이언트가 지연 시간이 더 낮은 클라이언트보다 어빌리티를 재활성화할 수 있을 때까지 더 오래 기다려야 하는 것과는 다릅니다. 이는 기본 공격과 같이 쿨다운이 1초 미만인 매우 낮은 쿨다운 어빌리티에서 가장 명확합니다. 로컬에서 예측된 어빌리티의 쿨다운 지속시간을 서버가 덮어쓰는 것을 막을 방법이 없다면, 어빌리티 재활성화에 대한 높은 지연 시간의 영향을 완화하기 위한 Epic의 전략은 무엇인가요? 다른 예시로 설명하자면, Epic은 높은 지연 시간을 가진 플레이어가 로컬 예측으로 낮은 지연 시간 플레이어와 같은 속도로 공격하거나 활성화할 수 있도록 Paragon의 기본 공격과 다른 어빌리티를 어떻게 설계했나요?

> 짧은 답변은 이를 방지할 방법이 없으며 Paragon은 확실히 이 문제를 가지고 있었습니다. 더 높은 지연 시간 연결은 기본 공격에서 더 낮은 ROF(발사 속도)를 가졌습니다.
>
> 저는 GE 지속시간을 계산할 때 지연 시간을 고려하는 "GE 조정"을 추가하여 이를 해결하려고 했습니다. 근본적으로 서버가 총 GE 시간의 일부를 소비하도록 허용하여 클라이언트 측의 효과적인 GE 시간이 어떤 지연 시간에서도 100% 일관되도록 하는 것입니다(그러나 변동은 여전히 문제를 일으킬 수 있음). 하지만 이를 배포할 수 있는 상태로 만들지 못했고 프로젝트가 빠르게 진행되어 우리는 이를 완전히 해결하지 못했습니다.
>
> Fortnite는 무기 발사 속도에 대해 자체적인 북키핑을 수행합니다: 무기의 쿨다운에 GE를 사용하지 않습니다. 이것이 당신의 게임에서 중요한 문제라면 이 방법을 추천합니다.

8. GameplayAbilitySystem 플러그인에 대한 Epic의 로드맵은 무엇인가요? Epic은 2019년과 그 이후에 어떤 기능들을 추가할 계획인가요?

> 우리는 전반적으로 시스템이 꽤 안정적이라고 생각하며 현재 주요 새 기능을 작업하는 사람이 없습니다. Fortnite나 UDN/pull 요청에서 버그 수정과 작은 개선이 가끔 이루어지지만, 현재로서는 그게 전부입니다.
>
> 장기적으로, 저는 우리가 결국 "V2"나 큰 변화를 할 것이라고 생각합니다. 우리는 이 시스템을 작성하면서 많은 것을 배웠고 많은 것을 잘했지만 많은 것을 잘못했다고 느낍니다. 저는 위에서 지적된 치명적인 결함들을 수정하고 개선할 기회를 갖고 싶습니다.
>
> V2가 나온다면, 업그레이드 경로를 제공하는 것이 가장 중요할 것입니다. 우리는 절대 V2를 만들고 Fortnite를 영원히 V1에 남겨두지 않을 것입니다: 가능한 한 많은 것을 자동으로 마이그레이션하는 경로나 절차가 있을 것이지만, 여전히 거의 확실히 수동으로 다시 만들어야 하는 것들이 있을 것입니다.
>
> 높은 우선순위 수정사항들은 다음과 같습니다:
> * 캐릭터 이동 시스템과의 더 나은 상호운용성. 클라이언트 예측 통합.
> * GE 제거 예측 (질문 #4)
> * GE 지연 시간 조정 (질문 #7)
> * RPC 배치 처리와 프록시 구조와 같은 일반화된 네트워크 최적화. 주로 Fortnite를 위해 했던 것들이지만 적어도 게임들이 자신만의 게임 특화 최적화를 더 쉽게 작성할 수 있도록 더 일반화된 형태로 분해하는 방법을 찾는 것.
>
> 더 일반적인 리팩토링 유형의 변경사항들을 고려하면:
> * GE가 스프레드시트 값을 직접 참조하는 것에서 근본적으로 벗어나고 싶습니다. 대신 매개변수를 방출할 수 있고 이러한 매개변수는 스프레드시트 값에 바인딩된 더 높은 수준의 객체에 의해 채워질 수 있습니다. 현재 모델의 문제는 GE가 커브 테이블 행과의 밀접한 결합으로 인해 공유할 수 없게 된다는 것입니다. 매개변수화를 위한 일반화된 시스템이 작성되어 V2 시스템의 기초가 될 수 있다고 생각합니다.
> * UGameplayAbility의 "정책" 수를 줄입니다. ReplicationPolicy와 InstancingPolicy를 제거하고 싶습니다. 복제는, 제 생각에는, 거의 실제로 필요하지 않으며 혼란을 야기합니다. InstancingPolicy는 대신 FGameplayAbilitySpec를 서브클래스화할 수 있는 UObject로 만드는 것으로 대체되어야 합니다. 이는 이벤트를 가지고 블루프린트 가능한 "비인스턴스화된 어빌리티 객체"가 되어야 했습니다. UGameplayAbility는 "실행당 인스턴스화된" 객체여야 합니다. 실제로 인스턴스화가 필요한지 여부는 선택적일 수 있습니다: 대신 "비인스턴스화된" 어빌리티는 새로운 UGameplayAbilitySpec 객체를 통해 구현될 것입니다.
> * 시스템은 "필터링된 GE 적용 컨테이너"(더 높은 수준의 게임플레이 로직으로 어떤 GE를 어떤 액터에 적용할지 데이터 주도), "오버랩 볼륨 지원"(충돌 프리미티브 오버랩 이벤트를 기반으로 "필터링된 GE 적용 컨테이너" 적용) 등과 같은 더 많은 "중간 수준" 구조를 제공해야 합니다. 이들은 모든 프로젝트가 결국 자신만의 방식으로 구현하게 되는 구성 요소입니다. 이를 올바르게 구현하는 것은 사소한 일이 아니므로 우리가 몇 가지 기본 구현을 제공하는 것이 더 나을 것 같습니다.
> * 일반적으로, 프로젝트를 시작하고 실행하는 데 필요한 상용구를 줄입니다. 아마도 기본적으로 패시브 어빌리티나 기본 히트스캔 무기를 제공하는 별도의 모듈 "Ex library" 등이 될 수 있습니다. 이 모듈은 선택적이지만 빠르게 시작하고 실행할 수 있게 해줄 것입니다.
> * GameplayCues를 어빌리티 시스템과 결합되지 않은 별도의 모듈로 이동하고 싶습니다. 여기에서 많은 개선이 이루어질 수 있다고 생각합니다.
>
> 이는 단지 제 개인적인 의견이며 누구의 약속도 아닙니다. 제가 생각하기에 가장 현실적인 행동 과정은 새로운 엔진 기술 이니셔티브가 나올 때 어빌리티 시스템을 업데이트해야 할 것이고 그때가 이러한 종류의 일을 할 시기가 될 것입니다. 이러한 이니셔티브는 스크립팅, 네트워킹 또는 물리/캐릭터 이동과 관련될 수 있습니다. 하지만 이는 모두 매우 멀리 내다보는 것이므로 일정에 대한 약속이나 추정을 할 수 없습니다.


**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 커뮤니티 질문 2
커뮤니티 멤버 [iniside](https://github.com/iniside)와 Dave Ratti의 Q&A:

1. 분리된 고정 틱킹에 대한 지원이 계획되어 있나요? 게임 스레드는 고정(30/60fps처럼)하고 렌더링 스레드는 자유롭게 실행되게 하고 싶습니다. 이것이 미래에 기대할 수 있는 것인지 아닌지 물어보는 이유는 게임플레이가 어떻게 작동해야 할지에 대한 몇 가지 가정을 하기 위해서입니다. 현재 물리에 대한 고정 비동기 틱이 있기 때문에 주로 물어보는데, 이는 나머지 시스템이 미래에 어떻게 작동할지에 대한 의문을 제기합니다. 엔진의 나머지 부분의 틱 속도를 고정하지 않고도 고정된 틱 게임 스레드를 가질 수 있는 능력이 있다면 정말 엄청날 것 같아서 숨기지 않고 말씀드립니다.

> 렌더링 프레임 레이트와 게임 스레드 틱 프레임 레이트를 분리할 계획은 없습니다. 이러한 시스템의 복잡성과 이전 엔진 버전과의 하위 호환성을 유지해야 하는 요구사항 때문에 이것이 실현될 가능성은 없다고 생각합니다.
>
> 대신, 우리가 선택한 방향은 게임 스레드와 독립적으로 고정된 틱 레이트로 실행되는 비동기 "물리 스레드"를 갖는 것입니다. 고정 속도로 실행되어야 하는 것들은 여기서 실행될 수 있고 게임 스레드/렌더링은 항상 그래왔던 대로 작동할 수 있습니다.
>
> Network Prediction이 Independent Ticking과 Fixed Ticking 모드를 지원한다는 점을 명확히 하는 것이 좋겠습니다. 제 장기 계획은 Independent Ticking을 Network Prediction에서 현재와 비슷하게 유지하여 변동 프레임 레이트로 게임 스레드에서 실행되고 "그룹/월드" 예측이 없이, 단지 고전적인 "클라이언트가 자신의 폰과 소유한 액터를 예측하는" 모델을 유지하는 것입니다. 그리고 Fixed Ticking은 비동기 물리를 사용하고 물리 오브젝트와 다른 클라이언트/폰/차량 등과 같은 클라이언트가 제어/소유하지 않는 액터를 예측할 수 있게 하는 것이 될 것입니다.

2. Ability System과 Network Prediction의 통합이 어떻게 보일지에 대한 계획이 있나요? 예를 들어, 고정 프레임 어빌리티 활성화(서버가 예측 키 대신 어빌리티가 활성화되고 태스크가 실행된 프레임을 받는 것)와 같은 것 말입니다.

> 네, Ability System의 예측 키를 재작성/제거하고 Network Prediction 구조로 대체하는 것이 계획입니다. NetworkPredictionExtras의 MockAbility 예제는 이것이 어떻게 작동할 수 있는지 보여주지만 GAS가 요구하는 것보다 더 "하드코딩" 되어 있습니다.
>
> 주요 아이디어는 ASC의 RPC에서 명시적인 클라이언트->서버 예측 키 교환을 제거하는 것입니다. 더 이상 예측 윈도우나 범위가 지정된 예측 키가 없을 것입니다. 대신 모든 것이 NetworkPrediction 프레임을 중심으로 고정될 것입니다. 중요한 것은 클라이언트와 서버가 언제 일이 일어나는지에 대해 동의하는 것입니다. 예를 들면:
>
> * 어빌리티가 언제 활성화/종료/취소되었는지
> * GameplayEffect가 언제 적용/제거되었는지
> * Attribute 값(프레임 X에서 attribute의 값이 무엇이었는지)
>
> 이것은 어빌리티 시스템 수준에서 일반적으로 수행될 수 있다고 생각합니다. 하지만 UGameplayAbility 내의 사용자 정의 로직을 완전히 롤백 가능하게 만드는 것은 여전히 더 많은 작업이 필요할 것입니다. 우리는 결국 완전히 롤백 가능하고 더 제한된 기능 세트나 롤백 친화적으로 표시된 Ability Task만 접근할 수 있는 UGameplayAbility의 서브클래스를 만들게 될 수 있습니다. 그런 식으로요. 애니메이션 이벤트와 루트 모션, 그리고 이들이 어떻게 처리되는지에 대한 많은 의미도 있습니다.
>
> 더 명확한 답변을 드리고 싶지만 GAS를 다시 건드리기 전에 기초를 제대로 갖추는 것이 정말 중요합니다. 더 높은 수준의 시스템을 변경하기 전에 이동과 물리가 견고해야 합니다.


3. Network Prediction 개발을 메인 브랜치로 이동할 계획이 있나요? 솔직히 말씀드리면, 최신 코드를 확인해보고 싶습니다. 상태와 관계없이요.

> 우리는 그것을 향해 작업하고 있습니다. 시스템 작업은 여전히 모두 NetworkPrediction에서 수행되고 있으며(NetworkPhysics.h 참조) 기본적인 비동기 물리 관련 내용은 모두 사용 가능해야 합니다(RewindData.h 등). 하지만 우리는 또한 공개할 수 없는 Fortnite의 사용 사례에 집중해 왔습니다. 우리는 버그, 성능 최적화 등을 해결하고 있습니다.
>
> 더 많은 맥락을 제공하자면: 이 시스템의 초기 버전을 작업할 때, 우리는 "프론트엔드" - 상태와 시뮬레이션이 어떻게 정의되고 작성되는지에 매우 집중했습니다. 거기서 많이 배웠습니다. 하지만 비동기 물리가 온라인으로 오면서, 우리는 초기 추상화의 일부를 버리는 대가로 이 시스템에서 실제로 작동하는 것을 만드는 데 훨씬 더 집중해 왔습니다. 여기서의 목표는 실제 작동하는 것이 있을 때 다시 돌아와서 것들을 재통합하는 것입니다. 예를 들어, "프론트엔드"로 돌아가서 현재 작업 중인 핵심 기술 조각들 위에 최종 버전을 만드는 것입니다.


4. 메인 브랜치에 한동안 Gameplay Messages를 보내기 위한 플러그인(이벤트/메시지 버스처럼 보이던)이 있었는데 제거되었습니다. 이를 복원할 계획이 있나요? Game Features/Modular Gameplay 플러그인과 함께, 일반적인 이벤트 버스 디스패처가 있으면 매우 유용할 것 같습니다.

> GameplayMessages 플러그인을 말씀하시는 것 같네요. 이는 아마 언젠가는 다시 돌아올 것입니다 - API가 아직 완전히 확정되지 않았고 작성자가 아직 공개하려고 하지 않았습니다. 모듈식 게임플레이 설계에 유용할 것이라는 데 동의합니다. 하지만 이는 제 영역이 아니라서 더 많은 정보를 드릴 수 없습니다.


5. 최근에 비동기 고정 물리를 가지고 실험해봤는데 결과가 유망해 보입니다. 하지만 향후 NP 업데이트가 있을 예정이라면 아마 그냥 좀 더 실험해보고 기다릴 것 같습니다. 왜냐하면 작동하게 하려면 여전히 전체 엔진을 고정 틱으로 만들어야 하고, 다른 한편으로는 물리를 33ms로 유지하려고 하는데, 모든 것이 30fps라면 좋은 경험이 되지 않기 때문입니다.

비동기 CharacterMovementComponent에 대한 작업이 있었던 것을 봤는데, 이것이 Network Prediction을 사용할지 아니면 별도의 노력인지는 확실하지 않습니다.

이것을 보고 나서, 고정 틱 레이트에서 커스텀 비동기 이동을 구현해보려고 했고, 잘 작동했지만, 그 위에 보간을 위한 별도의 업데이트도 추가해야 했습니다. 설정은 고정 33ms 업데이트로 별도의 워커 스레드에서 시뮬레이션 틱을 실행하고, 계산을 하고, 결과를 저장한 다음, 현재 프레임 레이트에 맞게 게임 스레드에서 보간하는 것이었습니다. 완벽하지는 않았지만, 일은 해냈습니다.

제 질문은 이것이 미래에는 더 쉽게 설정할 수 있는 것이 될지입니다. 상당한 양의 보일러플레이트 코드를 작성해야 하고(보간 부분) 각 이동 객체를 개별적으로 보간하는 것이 특별히 효율적이지 않기 때문입니다.

비동기 기능은 정말 흥미롭습니다. 게임 시뮬레이션을 고정 업데이트 속도로 실행할 수 있게 해주고(이는 고정 스레드를 불필요하게 만들 것입니다) 더 예측 가능한 결과를 얻을 수 있기 때문입니다. 이것이 앞으로 의도된 것인가요, 아니면 선택된 시스템에 대한 혜택인가요? 제가 기억하기로는 액터 트랜스폼은 비동기적으로 업데이트되지 않고 블루프린트가 완전히 스레드 안전하지 않습니다. 다시 말해서, 이것이 더 프레임워크 수준에서 지원될 계획인가요, 아니면 각 게임이 자체적으로 해결해야 하는 것인가요?

> 비동기 CharacterMovementComponent
>
> 이는 기본적으로 CMC를 있는 그대로 물리 스레드로 포팅하는 초기 프로토타입/실험입니다. 아직은 CMC의 미래로 보지 않지만, 그렇게 발전할 수 있습니다. 현재는 네트워킹 지원이 없어서 정말로 따라할 만한 것은 아닙니다. 이를 작업하는 사람들은 주로 이 시스템이 추가할 입력 지연 시간을 측정하고 그것을 어떻게 완화할 수 있는지에 관심이 있습니다.
>
> 예. 비동기 물리가 활성화된 상태에서 엔진은 가변 틱 레이트로 실행하면서 물리와 "핵심" 게임플레이 시뮬레이션(캐릭터 이동, 차량, GAS 등)은 고정 레이트로 실행할 수 있습니다.
>
> 이를 지금 활성화하는 데 필요한 cvar들은 다음과 같습니다: (이미 알고 계신 것 같습니다)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos는 물리 상태에 대한 보간을 제공합니다(예: UPrimitiveComponent로 다시 푸시되고 게임 코드에서 볼 수 있는 트랜스폼). 현재 `p.AsyncInterpolationMultiplier`라는 cvar가 있어서, 살펴보고 싶으시다면 이를 제어할 수 있습니다. 추가 코드를 작성하지 않아도 물리 바디의 부드럽고 연속적인 움직임을 볼 수 있을 것입니다.
>
> 물리가 아닌 상태를 보간하고 싶다면, 현재로서는 여전히 직접 해야 합니다. 예를 들어 비동기 물리 스레드에서 업데이트(틱)하고 싶지만 게임 스레드에서는 부드럽고 연속적인 보간을 보고 싶어서 매 렌더 프레임마다 쿨다운 시각화가 업데이트되는 것과 같은 경우입니다. 우리는 결국 이것도 다룰 것이지만 아직 예제는 없습니다.
>
> 네, 그래서 그것이 지금까지 시스템의 큰 일반적인 문제였습니다. 우리는 경험 있는 프로그래머들이 성능과 안전성을 최대화할 수 있는 인터페이스를 제공하고 싶습니다(수많은 위험요소와 할-수는-있지만-하지-않는-것이-좋은 것들 없이 예측적으로 "그냥 작동하는" 게임플레이 코드를 작성하는 능력). 따라서 CharacterMovement와 같은 것은 성능을 최대화하기 위해 많은 커스텀 작업을 할 수 있습니다 - 예를 들어, 템플릿 코드 작성과 배치 업데이트 수행, 넓게 확장, 업데이트 루프를 구분된 단계로 나누는 등입니다. 우리는 이 사용 사례를 위해 비동기 스레드와 롤백 시스템에 대한 좋은 "로우 레벨" 인터페이스를 제공하고 싶습니다. 그리고 이 경우에도 - 캐릭터 이동 시스템 자체가 자체적인 방식으로 확장 가능한 것이 여전히 합리적입니다. 예를 들어 커스텀 이동 모드를 블루프린트로 만들고 스레드 안전한 블루프린트 API를 제공하는 것입니다.
>
> 하지만 우리는 이것이 자체 "시스템"이 정말로 필요하지 않은 더 단순한 게임플레이 오브젝트에는 받아들일 수 없다는 것을 인식하고 있습니다. Unreal과 더 일치하는 무언가가 필요합니다. 예를 들어, 리플렉션 시스템 사용, 일반적인 블루프린트 지원 등입니다. 다른 스레드에서 블루프린트를 사용하는 예제들이 있습니다(BlueprintThreadSafe 키워드와 애니메이션 시스템이 작업해온 것을 보세요). 그래서 언젠가는 이런 형태가 있을 것이라고 생각합니다. 하지만 다시 말하지만, 우리는 아직 거기까지 가지 못했습니다.
>
> 단지 보간에 대해 물어보신 것을 알지만 일반적인 답변은 이렇습니다: 현재는 NetSerialize, ShouldReconcile, Interpolate 등 모든 것을 수동으로 하게 하지만 결국에는 "리플렉션 시스템을 사용하고 싶다면 이런 것들을 수동으로 작성할 필요가 없다"와 같은 방법이 있을 것입니다. 우리는 단지 모든 사람에게 리플렉션 시스템 사용을 강제하고 싶지 않습니다. 왜냐하면 그것은 시스템의 가장 낮은 수준에서 우리가 받아들이고 싶지 않은 다른 제한사항들을 부과하기 때문입니다.
>
> 그리고 앞서 말씀드린 것과 연결하자면 - 현재 우리는 몇 가지 매우 특정한 예제들이 작동하고 성능이 좋게 만드는 데 정말 집중하고 있으며, 그 다음에 프론트엔드로 관심을 돌려 다른 모든 사람들이 사용하기 쉽고 반복 작업할 수 있게 만들고, 보일러플레이트를 줄이는 등의 작업을 할 것입니다. 

**[⬆ 상단으로 돌아가기](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS 변경사항

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-5.3"></a>
### 5.3
* Crash Fix: Fixed a crash when trying to apply Gameplay Cues after a seamless travel.
* Crash Fix: Fixed a crash caused by GlobalAbilityTaskCount when using Live Coding.
* Crash Fix: Fixed UAbilityTask::OnDestroy to not crash if called recursively for cases like UAbilityTask_StartAbilityState.
* Bug Fix: It is now safe to call `Super::ActivateAbility` in a child class. Previously, it would call `CommitAbility`.
* Bug Fix: Added support for properly replicating different types of FGameplayEffectContext.
* Bug Fix: FGameplayEffectContextHandle will now check if data is valid before retrieving "Actors".
* Bug Fix: Retain rotation for Gameplay Ability System Target Data LocationInfo.
* Bug Fix: Gameplay Ability System now stops searching for PC only if a valid PC is found.
* Bug Fix: Use existing GameplayCueParameters if it exists instead of default parameters object in RemoveGameplayCue_Internal.
* Bug Fix: GameplayAbilityWorldReticle now faces towards the source Actor instead of the TargetingActor.
* Bug Fix: Cache trigger event data if it was passed in with GiveAbilityAndActivateOnce and the ability list was locked.
* Bug Fix: Support has been added for the FInheritedGameplayTags to update its CombinedTags immediately rather than waiting until a Save.
* Bug Fix: Moved ShouldAbilityRespondToEvent from client-only code path to both server and client.
* Bug Fix: Fixed FAttributeSetInitterDiscreteLevels from not working in Cooked Builds due to Curve Simplification.
* Bug Fix: Set CurrentEventData in GameplayAbility.
* Bug Fix: Ensure MinimalReplicationTags are set up correctly before potentially executing callbacks.
* Bug Fix: Fixed ShouldAbilityRespondToEvent from not getting called on the instanced GameplayAbility.
* Bug Fix: Gameplay Cue Notify Actors executing on Child Actors no longer leak memory when gc.PendingKill is disabled.
* Bug Fix: Fixed an issue in GameplayCueManager where GameplayCueNotify_Actors could be 'lost' due to hash collisions.
* Bug Fix: WaitGameplayTagQuery will now respect its Query even if we have no Gameplay Tags on the Actor.
* Bug Fix: PostAttributeChange and AttributeValueChangeDelegates will now have the correct OldValue.
* Bug Fix: Fixed FGameplayTagQuery from not showing a proper Query Description if the struct was created by native code.
* Bug Fix: Ensure that the UAbilitySystemGlobals::InitGlobalData is called if the Ability System is in use. Previously if the user did not call it, the Gameplay Ability System did not function correctly.
* Bug Fix: Fixed issue when linking/unlinking anim layers from UGameplayAbility::EndAbility.
* Bug Fix: Updated Ability System Component function to check the Spec's ability pointer before use.
* New: Added a GameplayTagQuery field to FGameplayTagRequirements to enable more complex requirements to be specified.
* New: Introduced FGameplayEffectQuery::SourceAggregateTagQuery to augment SourceTagQuery.
* New: Extended the functonality to execute and cancel Gameplay Abilities & Gameplay Effects from a console command.
* New: Added the ability to perform an "Audit" on Gameplay Ability Blueprints that will show information on how they're developed and intended to be used.
* Change: OnAvatarSet is now called on the primary instance instead of the CDO for instanced per Actor Gameplay Abilities.
* Change: Allow both Activate Ability and Activate Ability From Event in the same Gameplay Ability Graph.
* Change: AnimTask_PlayMontageAndWait now has a toggle to allow Completed and Interrupted after a BlendOut event.
* Change: ModMagnitudeCalc wrapper functions have been declared const.
* Change: FGameplayTagQuery::Matches now returns false for empty queries.
* Change: Updated FGameplayAttribute::PostSerialize to mark the contained attribute as a searchable name.
* Change: Updated GetAbilitySystemComponent to default parameter to Self.
* Change: Marked functions as virtual in AbilityTask_WaitTargetData.
* Change: Removed unused function FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters.
* Change: Removed vestigial GameplayAbility::SetMovementSyncPoint.
* Change: Removed unused replication flag from Gameplay tasks & Ability system components.
* Change: Moved some gameplay effect functionality into optional components. All existing content will automatically update to use components during PostCDOCompiled, if necessary.

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* Bug Fix: Fixed a crash in the `UAbilitySystemBlueprintLibrary::MakeSpecHandle` function.
* Bug Fix: Fixed logic in the Gameplay Ability System where a non-Controlled Pawn would be considered remote, even if it was spawned locally on the server (e.g. Vehicles).
* Bug Fix: Correctly set activation info on predicted instanced abilities that were rejected by the server.
* Bug Fix: Fixed a bug that would cause GameplayCues to get stuck on remote instances.
* Bug Fix: Fixed a memory stomp when chaining calls to WaitGameplayEvent.
* Bug Fix: Calling the AbilitySystemComponent `GetOwnedGameplayTags()` function in Blueprint no longer retains the previous call's return values when the same node is executed multiple times.
* Bug Fix: Fixed an issue with GameplayEffectContext replicating a reference to a dynamic object that would never be replicated.
  * This prevented GameplayEffect from calling `Owner->HandleDeferredGameplayCues(this)` as `bHasMoreUnmappedReferences` would always be true.
* New: The [Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) is a way to create data-driven targeting requests.
* New: Added custom serialization support for GameplayTag Queries.
* New: Added support for replicating derived FGameplayEffectContext types.
* New: Gameplay Attributes in assets are now registered as searchable names on save, allowing for references to attributes to be seen in the reference viewer.
* New: Added some basic unit tests for the AbilitySystemComponent.
* New: Gameplay Ability System Attributes now respect Core Redirects. This means you can now rename Attribute Sets and their Attributes in code and have them load properly in assets saved with the old names by adding redirect entries to DefaultEngine.ini.
* Change: Allow changing the evaluation channel of a Gameplay Effect Modifier from code.
* Change: Removed previously unused variable `FGameplayModifierInfo::Magnitude` from the Gameplay Abilities Plugin.
* Change: Removed the synchronization logic between the ability system component and Smart Object instance tags.

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* Bug Fix: Fixed issue where replicated loose gameplay tags were not replicating to the owner.
* Bug Fix: Fixed AbilityTask bug where abilities could be blocked from timely garbage-collection.
* Bug Fix: Fixed an issue when a gameplay ability listening to activate based on a tag would fail to be activated. This would happen if there were more than one Gameplay Ability listening to this tag, and the first one in the list was invalid or didn't have authority to activate.
* Bug Fix: Fixed GameplayEffects that use Data Registries correctly from warning on load and improved the warning text.
* Bug Fix: Removed code from UGameplayAbility that was incorrectly only registering the last instanced ability with the Blueprint debugger for breakpoints.
* Bug Fix: Fixed Gameplay Ability System Ability getting stuck if EndAbility was called during the lock inside ApplyGameplayEffectSpecToTarget.
* New: Added support for Gameplay Effects to add blocked ability tags.
* New: Added WaitGameplayTagQuery nodes. One is based off of the UAbilityTask and the other is of UAbilityAsync. This node specifies a TagQuery, and will trigger its output pin when the query becomes true or false, based on configuration.
* New: Modified AbilityTask debugging in Console Variables to enable debug recording and printing to log by default in non-shipping builds (with ability to hotfix on/off as needed).
* New: You can now set AbilitySystem.AbilityTask.Debug.RecordingEnabled to 0 to disable, 1 to enable in non-shipping builds, and 2 to enable all builds (including shipping).
* New: You can use AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults to only print the top N results in log (to avoid log spam).
* New: STAT_AbilityTaskDebugRecording can be used to test perf impact from these on-by-default debugging changes.
* New: Added a debug command to filter GameplayCue events.
* New: Added new debug commandsAbilitySystem.DebugAbilityTags, AbilitySystem.DebugBlockedTags, andAbilitySystem.DebugAttribute to the Gameplay Ability System.
* New: Added a Blueprint function to get a debug string representation of a Gameplay Attribute.
* New: Added a new Gameplay Task resource overlap policy to cancel existing tasks.
* Change: Now Ability Tasks should make sure to call Super::OnDestroy only after they do anything needed to the Ability pointer, as it will be nulled out after calling it.
* Change: Converted FGameplayAbilitySpec/Def::SourceObject to be a weak reference.
* Change: Made a Ability System Component reference in the Ability Task a weak pointer so Garbage Collection can delete it.
* Change: Removed redundant enum EWaitGameplayTagQueryAsyncTriggerCondition.
* Change: GameplayTasksComponent and AbilitySystemComponent now support the registered subobject API.
* Change: Added better logging to indicate why Gameplay Abilities failed to be activated.
* Change: Removed AbilitySystem.Debug.NextTarget and PrevTarget commands in favor of global HUD NextDebugTarget and PrevDebugTarget commands.

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Updated `GiveAbilityAndActivateOnce` to pass in FGameplayEventData parameter.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
  * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
  * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ 상단으로 돌아가기](#table-of-contents)**
