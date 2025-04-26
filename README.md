# GASDocumentation
Unreal Engine 5のGameplayAbilitySystemプラグイン（GAS）についての私の理解を、シンプルなマルチプレイヤーサンプルプロジェクトと共にまとめました。このドキュメントは公式のものではなく、このプロジェクトや私自身がEpic Gamesと提携しているわけではありません。この情報の正確性について保証するものではありません。

このドキュメントの目的は、GASの主要な概念やクラスを説明し、私の経験に基づいた追加のコメントを提供することです。GASに関する「部族的知識」がコミュニティ内で広まっており、ここで私の知識をすべて共有することを目指しています。

サンプルプロジェクトとドキュメントは、**Unreal Engine 5.3**（UE5）に対応しています。古いバージョンのUnreal Engine用のドキュメントもありますが、それらはサポートされておらず、バグや情報の古さがある可能性があります。エンジンのバージョンに一致するブランチを使用してください。

[GASShooter](https://github.com/tranek/GASShooter)は、GASを使用したマルチプレイヤーFPS/TPSの高度な技術を示す姉妹プロジェクトです。

最良のドキュメントは常にプラグインのソースコードです。

<a name="table-of-contents"></a>
## 目次

> 1. [GameplayAbilitySystemプラグインの概要](#intro)
> 1. [サンプルプロジェクト](#sp)
> 1. [GASを使用したプロジェクトのセットアップ](#setup)
> 1. [概念](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [レプリケーションモード](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [セットアップと初期化](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Gameplay Tagsの変更に応答する](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [プラグイン.iniファイルからGameplay Tagsを読み込む](#concepts-gt-loadfromplugin)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attributeの定義](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValueとCurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Attributeの変更に応答する](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Setの定義](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Setの設計](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [個別のAttributesを持つサブコンポーネント](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [ランタイムでのAttributeSetsの追加と削除](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [アイテムAttributes（武器の弾薬）](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [アイテム上の単純な浮動小数点数](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [アイテム上の`AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [アイテム上の`ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Attributeの定義](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Attributeの初期化](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [ゲームプレイエフェクト](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [ゲームプレイエフェクトの定義](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [ゲームプレイエフェクトの適用](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [ゲームプレイエフェクトの削除](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [ゲームプレイエフェクトの修飾子](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乗算および除算の修飾子](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [修飾子におけるGameplay Tags](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Gameplay Effectのスタッキング](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [付与されたアビリティ](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effectのタグ](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [免疫](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [修飾子の大きさ計算](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [ゲームプレイエフェクト実行計算](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [実行計算へのデータ送信](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [バックデータAttribute計算修飾子](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [バックデータ一時変数計算修飾子](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [ゲームプレイエフェクトコンテキスト](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [カスタム適用要件](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [コストゲームプレイエフェクト](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [クールダウンゲームプレイエフェクト](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [クールダウンゲームプレイエフェクトの残り時間を取得する](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [クールダウンの開始と終了をリッスンする](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [クールダウンの予測](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [アクティブなゲームプレイエフェクトの持続時間を変更する](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [ランタイムで動的なゲームプレイエフェクトを作成する](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [ゲームプレイエフェクトコンテナ](#concepts-ge-containers)  
>    4.6 [ゲームプレイアビリティ](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [ゲームプレイアビリティの定義](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [レプリケーションポリシー](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [リモートアビリティキャンセルのサーバー尊重](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [入力を直接レプリケートする](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [ASCへの入力バインディング](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [アビリティをアクティブ化せずに入力をバインドする](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [アビリティの付与](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [アビリティのアクティブ化](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [パッシブアビリティ](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [アクティベーション失敗タグ](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [アビリティのキャンセル](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [アクティブなアビリティの取得](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [インスタンス化ポリシー](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [ネット実行ポリシー](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [アビリティタグ](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [ゲームプレイアビリティスペック](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [アビリティへのデータの渡し方](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [アビリティのコストとクールダウン](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [アビリティのレベルアップ](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [アビリティセット](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [アビリティのバッチ処理](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [ネットセキュリティポリシー](#concepts-ga-netsecuritypolicy)  
>    4.7 [アビリティタスク](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [アビリティタスクの定義](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [カスタムアビリティタスク](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [アビリティタスクの使用方法](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [ルートモーションソースアビリティタスク](#concepts-at-rms)  
>    4.8 [ゲームプレイキュー](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [ゲームプレイキューの定義](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [ゲームプレイキューのトリガー](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [ローカルゲームプレイキュー](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [ゲームプレイキューパラメータ](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [ゲームプレイキューマネージャー](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [ゲームプレイキューの発火防止](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [ゲームプレイキューのバッチ処理](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [手動RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [1つのゲームプレイエフェクトに複数のゲームプレイキュー](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [ゲームプレイキューイベント](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [ゲームプレイキューの信頼性](#concepts-gc-reliability)  
>    4.9 [アビリティシステムグローバル](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [予測](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [予測キー](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [アビリティ内での新しい予測ウィンドウの作成](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [アクターの予測的スポーン](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GASにおける予測の将来](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [ネットワーク予測プラグイン](#concepts-p-npp)  
>    4.11 [ターゲティング](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [ターゲットデータ](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [ターゲットアクター](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [ターゲットデータフィルタ](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [ゲームプレイアビリティワールドレティクル](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [ゲームプレイエフェクトコンテナのターゲティング](#concepts-targeting-containers)  
> 1. [よく実装されるアビリティとエフェクト](#cae)  
>    5.1 [スタン](#cae-stun)  
>    5.2 [スプリント](#cae-sprint)  
>    5.3 [エイムダウンサイト](#cae-ads)  
>    5.4 [ライフスティール](#cae-ls)  
>    5.5 [クライアントとサーバーでランダムな数値を生成する](#cae-random)  
>    5.6 [クリティカルヒット](#cae-crit)  
>    5.7 [スタックしないゲームプレイエフェクト（ただし最大の効果のみがターゲットに影響する）](#cae-nonstackingge)  
>    5.8 [ゲームが一時停止中にターゲットデータを生成する](#cae-paused)  
>    5.9 [ワンボタンインタラクションシステム](#cae-onebuttoninteractionsystem)  
> 1. [GASのデバッグ](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [ゲームプレイデバッガー](#debugging-gd)  
>    6.3 [GASのログ出力](#debugging-log)  
> 1. [最適化](#optimizations)  
>    7.1 [アビリティのバッチ処理](#optimizations-abilitybatching)  
>    7.2 [ゲームプレイキューのバッチ処理](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponentのレプリケーションモード](#optimizations-ascreplicationmode)  
>    7.4 [Attributeプロキシのレプリケーション](#optimizations-attributeproxyreplication)  
>    7.5 [ASCの遅延読み込み](#optimizations-asclazyloading)  
> 1. [品質向上の提案](#qol)  
>    8.1 [ゲームプレイエフェクトコンテナ](#qol-gameplayeffectcontainers)  
>    8.2 [ASCデリゲートにバインドするためのBlueprint AsyncTasks](#qol-asynctasksascdelegates)  
> 1. [トラブルシューティング](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` エラー](#troubleshooting-scriptstructcache)  
>    9.3 [アニメーションモンタージュがクライアントにレプリケートされない](#troubleshooting-replicatinganimmontages)  
>    9.4 [Blueprintアクターを複製するとAttributeSetsがnullptrになる](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [列挙型名がパス名で表現されるようになった](#troubleshooting-enumnamesarenowpathnames)  
> 1. [一般的なGASの略語](#acronyms)
> 1. [その他のリソース](#resources)  
>    11.1 [Epic GamesのDave RattiとのQ&A](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [コミュニティ質問 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [コミュニティ質問 2](#resources-daveratti-community2)  
> 1. [GASの変更履歴](#changelog)  
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
## 1. GameplayAbilitySystemプラグインの概要
[公式ドキュメント](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)より引用:
>Gameplay Ability Systemは、RPGやMOBAタイトルで見られるようなアビリティやAttributeを構築するための非常に柔軟なフレームワークです。このシステムを使用すると、ゲーム内のキャラクターが使用するアクションやパッシブアビリティ、これらのアクションの結果として蓄積または減少するさまざまなAttributeに影響を与えるステータス効果、使用を調整するための「クールダウン」タイマーやリソースコスト、アビリティのレベルやその効果を各レベルで変更する機能、パーティクルやサウンドエフェクトをアクティブ化する機能などを構築できます。簡単に言えば、このシステムは、ジャンプのようなシンプルなアビリティから、現代のRPGやMOBAタイトルにおけるお気に入りのキャラクターのアビリティセットのような複雑なものまで、ゲーム内アビリティを設計、実装、効率的にネットワーク化するのに役立ちます。

GameplayAbilitySystemプラグインはEpic Gamesによって開発され、Unreal Engineに付属しています。このプラグインは、ParagonやFortniteなどのAAA商業ゲームで実際に使用され、実績があります。

このプラグインは、シングルプレイヤーおよびマルチプレイヤーゲームにおいて以下を実現するための即時利用可能なソリューションを提供します:
* レベルベースのキャラクターアビリティやスキルの実装（コストやクールダウンのオプション付き）([GameplayAbilities](#concepts-ga))
* アクターに属する数値的な`Attribute`の操作 ([Attributes](#concepts-a))
* アクターへのステータス効果の適用 ([GameplayEffects](#concepts-ge))
* アクターへの`GameplayTags`の適用 ([GameplayTags](#concepts-gt))
* 視覚エフェクトやサウンドエフェクトの生成 ([GameplayCues](#concepts-gc))
* 上記すべての要素のレプリケーション

マルチプレイヤーゲームでは、GASは以下の[クライアントサイド予測](#concepts-p)をサポートします:
* アビリティのアクティベーション
* アニメーションモンタージュの再生
* `Attribute`の変更
* `GameplayTags`の適用
* `GameplayCues`の生成
* `CharacterMovementComponent`に接続された`RootMotionSource`機能を介した移動

**GASのセットアップはC++で行う必要があります**が、`GameplayAbilities`や`GameplayEffects`はデザイナーがBlueprintで作成することが可能です。

GASの現在の課題:
* `GameplayEffect`の遅延補正（アビリティのクールダウンを予測できないため、高遅延のプレイヤーは低遅延のプレイヤーに比べてクールダウンが短いアビリティの発動頻度が低くなる）。
* `GameplayEffects`の削除を予測できない。ただし、逆の効果を持つ`GameplayEffects`を追加することで削除を予測することは可能。しかし、これは常に適切または実現可能ではなく、依然として課題が残る。
* テンプレート、マルチプレイヤーの例、およびドキュメントの不足。このドキュメントがその助けになれば幸いです！

**[⬆ トップに戻る](#table-of-contents)**


<a name="sp"></a>
## 2. サンプルプロジェクト
このドキュメントには、GameplayAbilitySystemプラグインに不慣れな人向けのマルチプレイヤー対応の三人称シューティングゲームのサンプルプロジェクトが含まれています。ただし、Unreal EngineのC++、Blueprint、UMG、レプリケーション、その他中級レベルのトピックについての知識があることが前提です。このプロジェクトでは、`AbilitySystemComponent`（`ASC`）をプレイヤー/AI制御のヒーローでは`PlayerState`クラスに、AI制御のミニオンでは`Character`クラスに設定する方法を例示しています。

このプロジェクトの目的は、GASの基本を示し、よく求められるアビリティをコメント付きのコードで実演しながら、シンプルさを保つことです。初心者向けの内容であるため、[プロジェクタイルの予測](#concepts-p-spawn)のような高度なトピックは含まれていません。

実演されるコンセプト:
* `PlayerState`と`Character`における`ASC`の使用
* レプリケーションされた`Attributes`
* レプリケーションされたアニメーションモンタージュ
* `GameplayTags`
* `GameplayAbilities`内および外部からの`GameplayEffects`の適用と削除
* アーマーで軽減されたダメージを適用してキャラクターのヘルスを変更
* `GameplayEffectExecutionCalculations`
* スタン効果
* 死亡とリスポーン
* サーバー上でアクター（プロジェクタイル）をスポーン
* エイムダウンサイトやスプリントでローカルプレイヤーの速度を予測的に変更
* スプリントのためにスタミナを継続的に消費
* マナを使用してアビリティを発動
* パッシブアビリティ
* スタックする`GameplayEffects`
* アクターのターゲティング
* Blueprintで作成された`GameplayAbilities`
* C++で作成された`GameplayAbilities`
* アクターごとにインスタンス化された`GameplayAbilities`
* 非インスタンス化された`GameplayAbilities`（ジャンプ）
* 静的な`GameplayCues`（FireGunプロジェクタイルのインパクトパーティクルエフェクト）
* アクター`GameplayCues`（スプリントとスタンのパーティクルエフェクト）
ヒーロークラスには以下のアビリティがあります:

| アビリティ                | 入力バインド         | 予測可能 | C++ / Blueprint | 説明                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ジャンプ                  | スペースバー         | はい       | C++             | ヒーローがジャンプします。                                                                                                                                           |
| ガン                      | 左クリック           | いいえ     | C++             | ヒーローの銃からプロジェクタイルを発射します。アニメーションは予測可能ですが、プロジェクタイルは予測されません。                                                   |
| エイムダウンサイト        | 右クリック           | はい       | Blueprint       | ボタンを押している間、ヒーローは歩行速度が遅くなり、カメラがズームインして銃でのより正確な射撃が可能になります。                                                   |
| スプリント                | 左シフト            | はい       | Blueprint       | ボタンを押している間、ヒーローはスタミナを消費して走行速度が速くなります。                                                                                           |
| 前方ダッシュ              | Q                   | はい       | Blueprint       | ヒーローがスタミナを消費して前方にダッシュします。                                                                                                                     |
| パッシブアーマースタック  | パッシブ            | いいえ     | Blueprint       | 4秒ごとにヒーローはアーマースタックを1つ獲得し、最大4スタックまで保持します。ダメージを受けるとアーマースタックが1つ減少します。                                     |
| メテオ                    | R                   | いいえ     | Blueprint       | プレイヤーがターゲット地点を指定し、敵にメテオを落としてダメージを与え、スタンさせます。ターゲティングは予測可能ですが、メテオの生成は予測されません。               |

`GameplayAbilities`がC++またはBlueprintで作成されているかは重要ではありません。ここでは、それぞれの方法で作成する例を示すために混在させています。

ミニオンには事前定義された`GameplayAbilities`はありません。赤いミニオンはより高いヘルス再生能力を持ち、青いミニオンはより高い初期ヘルスを持っています。

`GameplayAbility`の命名には、ロジックがBlueprintで作成された場合は接尾辞`_BP`を使用しています。接尾辞がない場合は、ロジックがC++で作成されたことを意味します。

**Blueprint アセット命名プレフィックス**

| プレフィックス | アセットタイプ          |
| ------------- | ----------------------- |
| GA_           | GameplayAbility         |
| GC_           | GameplayCue             |
| GE_           | GameplayEffect          |
**[⬆ トップに戻る](#table-of-contents)**

<a name="setup"></a>
## 3. GASを使用したプロジェクトのセットアップ
GASを使用するプロジェクトをセットアップする基本手順:
1. エディタでGameplayAbilitySystemプラグインを有効化する
1. `YourProjectName.Build.cs`を編集し、`"GameplayAbilities"`, `"GameplayTags"`, `"GameplayTasks"`を`PrivateDependencyModuleNames`に追加する
1. Visual Studioのプロジェクトファイルをリフレッシュ/再生成する
1. バージョン4.24から5.2までの間では、[`TargetData`](#concepts-targeting-data)を使用するために`UAbilitySystemGlobals::Get().InitGlobalData()`を呼び出す必要があります。サンプルプロジェクトでは、`UAssetManager::StartInitialLoading()`内でこれを行っています。バージョン5.3以降では自動的に呼び出されます。詳細は[`InitGlobalData()`](#concepts-asg-initglobaldata)を参照してください。

これでGASを有効化する準備は完了です。ここから、[`ASC`](#concepts-asc)や[`AttributeSet`](#concepts-as)を`Character`や`PlayerState`に追加し、[`GameplayAbilities`](#concepts-ga)や[`GameplayEffects`](#concepts-ge)を作成していきましょう！

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts"></a>
## 4. GASの概念

#### セクション

> 4.1 [アビリティシステムコンポーネント](#concepts-asc)  
> 4.2 [ゲームプレイタグ](#concepts-gt)  
> 4.3 [Attribute](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [ゲームプレイエフェクト](#concepts-ge)  
> 4.6 [ゲームプレイアビリティ](#concepts-ga)  
> 4.7 [アビリティタスク](#concepts-at)  
> 4.8 [ゲームプレイキュー](#concepts-gc)  
> 4.9 [アビリティシステムグローバル](#concepts-asg)  
> 4.10 [予測](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 アビリティシステムコンポーネント
`アビリティシステムコンポーネント`（`ASC`）はGASの中心です。これは`UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)）であり、システムとのすべてのやり取りを処理します。[`GameplayAbilities`](#concepts-ga)を使用したり、[`Attribute`](#concepts-a)を持ったり、[`GameplayEffects`](#concepts-ge)を受け取る`アクター`は、1つの`ASC`をアタッチする必要があります。これらのオブジェクトはすべて`ASC`内に存在し、管理され、レプリケートされます（ただし、`Attribute`は[`Attribute Set`](#concepts-as)によってレプリケートされます）。開発者はこれをサブクラス化することが推奨されていますが、必須ではありません。

`ASC`がアタッチされている`アクター`は、`ASC`の`OwnerActor`と呼ばれます。`ASC`の物理的な表現である`アクター`は`AvatarActor`と呼ばれます。`OwnerActor`と`AvatarActor`は、MOBAゲームの単純なAIミニオンのように同じ`アクター`である場合もあります。また、MOBAゲームのプレイヤーが操作するヒーローのように、`OwnerActor`が`PlayerState`で、`AvatarActor`がヒーローの`Character`クラスである場合もあります。ほとんどの`アクター`は、自身に`ASC`を持ちます。もし`アクター`がリスポーンし、リスポーン間で`Attribute`や`GameplayEffects`の永続性が必要な場合（MOBAのヒーローのように）、理想的な`ASC`の場所は`PlayerState`です。

**注意:** `ASC`が`PlayerState`にある場合、`PlayerState`の`NetUpdateFrequency`を増やす必要があります。デフォルトでは非常に低い値に設定されており、クライアントで`Attribute`や`GameplayTags`の変更が遅延またはラグのように見える原因となる可能性があります。[`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)を有効にすることを忘れないでください。Fortniteではこれが使用されています。

`OwnerActor`と`AvatarActor`の両方が異なる`アクター`である場合、`IAbilitySystemInterface`を実装する必要があります。このインターフェースには1つの関数`UAbilitySystemComponent* GetAbilitySystemComponent() const`があり、`ASC`へのポインタを返します。`ASC`は、このインターフェース関数を探して内部的に相互作用します。

`ASC`は現在アクティブな`GameplayEffects`を`FActiveGameplayEffectsContainer ActiveGameplayEffects`に保持します。

`ASC`は付与された`Gameplay Abilities`を`FGameplayAbilitySpecContainer ActivatableAbilities`に保持します。`ActivatableAbilities.Items`を反復処理する場合は、必ずループの上に`ABILITYLIST_SCOPE_LOCK();`を追加してリストが変更されないようにロックしてください（アビリティの削除による変更を防ぐため）。スコープ内のすべての`ABILITYLIST_SCOPE_LOCK();`は`AbilityScopeLockCount`をインクリメントし、スコープ外になるとデクリメントします。`ABILITYLIST_SCOPE_LOCK();`のスコープ内でアビリティを削除しようとしないでください（クリアアビリティ関数は内部的に`AbilityScopeLockCount`をチェックしてリストがロックされている場合にアビリティを削除しないようにします）。

<a name="concepts-asc-rm"></a>
### 4.1.1 レプリケーションモード
`ASC`は、`GameplayEffects`、`GameplayTags`、`GameplayCues`をレプリケートするための3つの異なるモードを定義しています - `Full`、`Mixed`、`Minimal`。`Attributes`はそれぞれの`AttributeSet`によってレプリケートされます。

| レプリケーションモード | 使用する場面                           | 説明                                                                                     |
| ---------------------- | ------------------------------------- | --------------------------------------------------------------------------------------- |
| `Full`                | シングルプレイヤー                   | すべての`GameplayEffect`がすべてのクライアントにレプリケートされます。                   |
| `Mixed`               | マルチプレイヤー、プレイヤー制御のアクター | `GameplayEffects`は所有クライアントにのみレプリケートされます。他のクライアントには`GameplayTags`と`GameplayCues`のみがレプリケートされます。 |
| `Minimal`             | マルチプレイヤー、AI制御のアクター     | `GameplayEffects`は誰にもレプリケートされません。他のクライアントには`GameplayTags`と`GameplayCues`のみがレプリケートされます。 |

**注意:** `Mixed`レプリケーションモードでは、`OwnerActor`の`Owner`が`Controller`であることを期待します。`PlayerState`の`Owner`はデフォルトで`Controller`ですが、`Character`の`Owner`はそうではありません。`Mixed`レプリケーションモードを`PlayerState`以外の`OwnerActor`で使用する場合、有効な`Controller`を指定して`SetOwner()`を呼び出す必要があります。

バージョン4.24以降、`PossessedBy()`は`Pawn`の`Owner`を新しい`Controller`に設定するようになりました。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 セットアップと初期化
`ASC`は通常、`OwnerActor`のコンストラクタ内で作成され、明示的にレプリケートされるように設定されます。**これはC++で行う必要があります**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// アビリティシステムコンポーネントを作成し、明示的にレプリケートされるように設定
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC`は、サーバーとクライアントの両方で`OwnerActor`と`AvatarActor`で初期化する必要があります。初期化は、`Pawn`の`Controller`が設定された後（ポゼッション後）に行う必要があります。シングルプレイヤーゲームではサーバー側の処理のみを考慮すれば十分です。

プレイヤーが操作するキャラクターで`ASC`が`Pawn`に存在する場合、通常サーバーでは`Pawn`の`PossessedBy()`関数内で初期化し、クライアントでは`PlayerController`の`AcknowledgePossession()`関数内で初期化します。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASCのMixedModeレプリケーションでは、ASCのOwnerのOwnerがControllerである必要があります。
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

プレイヤーが操作するキャラクターで`ASC`が`PlayerState`に存在する場合、通常サーバーでは`Pawn`の`PossessedBy()`関数内で初期化し、クライアントでは`Pawn`の`OnRep_PlayerState()`関数内で初期化します。これにより、クライアント側で`PlayerState`が存在することが保証されます。

```c++
// サーバーのみ
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// サーバー側でASCを設定。クライアントはOnRep_PlayerState()でこれを行います。
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AIにはPlayerControllerがないため、ここでもう一度初期化しても問題ありません。PlayerControllerを持つヒーローに対して2回初期化しても害はありません。
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// クライアントのみ
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// クライアント側でASCを設定。サーバーはPossessedByでこれを行います。
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// クライアント側でASCのアクター情報を初期化。サーバーは新しいアクターをポゼッションするときにASCを初期化します。
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

もしエラーメッセージ `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` が表示された場合、クライアント側でASCを初期化していない可能性があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 ゲームプレイタグ
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)は、`Parent.Child.Grandchild...`の形式で階層的に名前付けされたタグで、`GameplayTagManager`に登録されます。これらのタグは、オブジェクトの状態を分類し記述するのに非常に便利です。例えば、キャラクターがスタン状態の場合、その期間中に`State.Debuff.Stun`という`GameplayTag`を付与することができます。

以前はブール値や列挙型で処理していたものを、`GameplayTags`に置き換え、オブジェクトが特定の`GameplayTags`を持っているかどうかのブールロジックで処理することが多くなります。

タグをオブジェクトに付与する際、通常はそのオブジェクトが`ASC`を持っている場合に`ASC`に追加します。これにより、GASがそれらと連携できます。`UAbilitySystemComponent`は`IGameplayTagAssetInterface`を実装しており、所有する`GameplayTags`にアクセスするための関数を提供します。

複数の`GameplayTags`は`FGameplayTagContainer`に格納できます。`GameplayTagContainer`は、`TArray<FGameplayTag>`よりも効率的な処理が可能であるため、こちらを使用することが推奨されます。タグは標準の`FName`ですが、`GameplayTagContainer`に効率的にパックされることで、プロジェクト設定で`Fast Replication`が有効になっている場合にレプリケーションが最適化されます。`Fast Replication`を使用するには、サーバーとクライアントが同じ`GameplayTags`リストを持っている必要がありますが、通常これは問題にならないため、このオプションを有効にするべきです。`GameplayTagContainer`は`TArray<FGameplayTag>`を返して反復処理することも可能です。

`GameplayTags`が`FGameplayTagCountContainer`に格納されている場合、その`TagMap`にはその`GameplayTag`のインスタンス数が格納されます。`FGameplayTagCountContainer`に`GameplayTag`が含まれていても、その`TagMapCount`がゼロの場合があります。このような状況は、デバッグ中に`ASC`がまだ`GameplayTag`を保持している場合に遭遇することがあります。`HasTag()`や`HasMatchingTag()`などの関数は`TagMapCount`をチェックし、`GameplayTag`が存在しないか`TagMapCount`がゼロの場合は`false`を返します。

`GameplayTags`は、`DefaultGameplayTags.ini`で事前に定義する必要があります。Unreal Engine Editorには、`DefaultGameplayTags.ini`を手動で編集することなく、開発者が`GameplayTags`を管理できるプロジェクト設定のインターフェースが用意されています。この`GameplayTag`エディタでは、タグの作成、名前変更、参照の検索、削除が可能です。

![プロジェクト設定内のGameplayTagエディタ](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

`GameplayTag`の参照を検索すると、エディタ内でおなじみの`Reference Viewer`グラフが表示され、その`GameplayTag`を参照しているすべてのアセットが表示されます。ただし、C++クラスで参照されている`GameplayTag`は表示されません。

`GameplayTags`の名前を変更すると、リダイレクトが作成され、元の`GameplayTag`を参照しているアセットが新しい`GameplayTag`にリダイレクトされるようになります。ただし、可能であれば、新しい`GameplayTag`を作成し、すべての参照を手動で新しい`GameplayTag`に更新してから、古い`GameplayTag`を削除することで、リダイレクトの作成を避けることをお勧めします。

`Fast Replication`に加えて、`GameplayTag`エディタには、よくレプリケートされる`GameplayTags`をさらに最適化するオプションがあります。

`GameplayTags`は、`GameplayEffect`から追加された場合にレプリケートされます。`ASC`では、レプリケートされない`LooseGameplayTags`を追加することができ、これらは手動で管理する必要があります。サンプルプロジェクトでは、`State.Dead`に`LooseGameplayTag`を使用しており、所有クライアントが即座にヘルスがゼロになったことに反応できるようにしています。リスポーン時には手動で`TagMapCount`をゼロに戻します。`LooseGameplayTags`を使用する場合は、`TagMapCount`を手動で調整する必要がありますが、`UAbilitySystemComponent::AddLooseGameplayTag()`および`UAbilitySystemComponent::RemoveLooseGameplayTag()`関数を使用する方が望ましいです。

C++で`GameplayTag`への参照を取得する方法:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

親タグや子タグを取得するなど、高度な`GameplayTag`操作を行う場合は、`GameplayTagManager`が提供する関数を確認してください。`GameplayTagManager`にアクセスするには、`GameplayTagManager.h`をインクルードし、`UGameplayTagManager::Get().FunctionName`を呼び出します。`GameplayTagManager`は、`GameplayTags`を関係ノード（親、子など）として格納しており、文字列操作や比較よりも高速に処理できます。

`GameplayTags`や`GameplayTagContainer`には、オプションで`UPROPERTY`指定子`Meta = (Categories = "GameplayCue")`を付けることができ、Blueprintで`GameplayCue`の親タグを持つ`GameplayTags`のみを表示するようにフィルタリングできます。これは、変数が`GameplayCues`専用であるべき場合に便利です。

また、`FGameplayCueTag`という別の構造体があり、`FGameplayTag`をカプセル化し、Blueprintで`GameplayCue`の親タグを持つタグのみを自動的にフィルタリングします。

関数のパラメータとして`GameplayTag`をフィルタリングしたい場合は、`UFUNCTION`指定子`Meta = (GameplayTagFilter = "GameplayCue")`を使用します。ただし、関数の`GameplayTagContainer`パラメータはフィルタリングできません。これを可能にするためにエンジンを編集したい場合は、`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp`の`SGameplayTagGraphPin::ParseDefaultValueData()`が`FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`を呼び出し、`FilterString`を`SGameplayTagWidget`に渡している方法を確認してください。これに対し、`GameplayTagContainer`バージョンのこれらの関数は、メタフィールドプロパティをチェックせず、フィルタを渡しません。

サンプルプロジェクトでは、`GameplayTags`が広く使用されています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 Gameplay Tagsの変更に応答する
`ASC`は、`GameplayTags`が追加または削除されたときに発火するデリゲートを提供します。このデリゲートは、`GameplayTag`が追加/削除された場合のみ、またはその`TagMapCount`に変更があった場合に発火するように指定できます。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

コールバック関数には、`GameplayTag`と新しい`TagCount`のパラメータがあります。
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 プラグインの.iniファイルからGameplay Tagsを読み込む
プラグインに独自の.iniファイルで`GameplayTags`を定義した場合、そのプラグインの`GameplayTag` .iniディレクトリをプラグインの`StartupModule()`関数内で読み込むことができます。

例えば、Unreal Engineに付属するCommonConversationプラグインでは以下のようにしています:

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

これにより、`Plugins\CommonConversation\Config\Tags`ディレクトリを検索し、そこにある`GameplayTags`を含む.iniファイルをエンジン起動時にプロジェクトに読み込みます（プラグインが有効な場合）。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attribute

<a name="concepts-a-definition"></a>
#### 4.3.1 Attributeの定義
`Attribute`は、構造体[`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html)で定義される浮動小数点値です。これらはキャラクターのヘルス量やレベル、ポーションのチャージ数など、ゲームプレイに関連する数値を表すことができます。`Attribute`は通常、[`GameplayEffects`](#concepts-ge)によってのみ変更されるべきで、これによりASCが変更を[予測](#concepts-p)できるようになります。

`Attribute`は[`AttributeSet`](#concepts-as)によって定義され、そこに格納されます。`AttributeSet`は、レプリケーションが必要な`Attribute`をレプリケートする責任を持ちます。`Attribute`の定義方法については、[`AttributeSets`](#concepts-as)のセクションを参照してください。

**ヒント:** `Attribute`をエディタの`Attribute`リストに表示させたくない場合、`Meta = (HideInDetailsView)`プロパティ指定子を使用できます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValueとCurrentValue
`Attribute`は、`BaseValue`と`CurrentValue`の2つの値で構成されます。`BaseValue`は`Attribute`の永続的な値であり、`CurrentValue`は`BaseValue`に`GameplayEffects`による一時的な変更を加えた値です。例えば、キャラクターの移動速度`Attribute`が600単位/秒の`BaseValue`を持つ場合、まだ`GameplayEffects`による変更がないため、`CurrentValue`も600単位/秒です。もし一時的に50単位/秒の移動速度バフを受けた場合、`BaseValue`は600単位/秒のままですが、`CurrentValue`は600 + 50で合計650単位/秒になります。バフが終了すると、`CurrentValue`は`BaseValue`の600単位/秒に戻ります。

GAS初心者はしばしば`BaseValue`を`Attribute`の最大値と混同し、それをそのように扱おうとしますが、これは誤ったアプローチです。変更可能でアビリティやUIで参照される最大値は、別の`Attribute`として扱うべきです。ハードコードされた最大値と最小値については、`FAttributeMetaData`を使用して`DataTable`を定義する方法がありますが、Epicのコメントによるとこれは「進行中の作業」とされています。詳細は`AttributeSet.h`を参照してください。混乱を避けるため、アビリティやUIで参照される変更可能な最大値は別の`Attribute`として作成し、`Attribute`のクランプにのみ使用されるハードコードされた最大値と最小値は`AttributeSet`内のハードコードされた浮動小数点値として定義することをお勧めします。`Attribute`のクランプについては、`CurrentValue`の変更に対する[PreAttributeChange()](#concepts-as-preattributechange)と、`GameplayEffects`による`BaseValue`の変更に対する[PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)で説明されています。

`BaseValue`への永続的な変更は`Instant`型の`GameplayEffects`から発生し、`Duration`型および`Infinite`型の`GameplayEffects`は`CurrentValue`を変更します。`Periodic`型の`GameplayEffects`は`Instant`型の`GameplayEffects`として扱われ、`BaseValue`を変更します。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attribute
一部の`Attribute`は、一時的な値を保持するためのプレースホルダーとして扱われ、他の`Attribute`と相互作用することを目的としています。これらは`Meta Attribute`と呼ばれます。例えば、ダメージを`Meta Attribute`として定義することが一般的です。`GameplayEffect`が直接ヘルス`Attribute`を変更するのではなく、ダメージという`Meta Attribute`をプレースホルダーとして使用します。この方法では、ダメージ値をバフやデバフで修正し、[`GameplayEffectExecutionCalculation`](#concepts-ge-ec)内でさらに操作できます。例えば、現在のシールド`Attribute`からダメージを差し引き、残りをヘルス`Attribute`から差し引くことができます。ダメージ`Meta Attribute`は`GameplayEffects`間で永続性を持たず、毎回上書きされます。`Meta Attribute`は通常レプリケートされません。

`Meta Attribute`は、ダメージや回復のようなものに対して「どれだけのダメージを与えたか」と「そのダメージをどう処理するか」を分離するための良い論理的な区分を提供します。この論理的な分離により、`GameplayEffects`や`Execution Calculations`がターゲットがダメージをどのように処理するかを知る必要がなくなります。ダメージの例を続けると、`GameplayEffect`がダメージ量を決定し、`AttributeSet`がそのダメージをどう処理するかを決定します。すべてのキャラクターが同じ`Attribute`を持つわけではないため、特に`AttributeSets`をサブクラス化している場合、この分離は重要です。基本の`AttributeSet`クラスにはヘルス`Attribute`しかないかもしれませんが、サブクラス化された`AttributeSet`にはシールド`Attribute`が追加されるかもしれません。シールド`Attribute`を持つサブクラス化された`AttributeSet`は、受け取ったダメージを基本の`AttributeSet`クラスとは異なる方法で分配します。

`Meta Attribute`は良い設計パターンですが、必須ではありません。すべてのダメージインスタンスに対して1つの`Execution Calculation`を使用し、すべてのキャラクターが共有する1つの`AttributeSet`クラスを使用する場合、`Execution Calculation`内でヘルスやシールドなどにダメージを直接分配して`Attribute`を直接変更する方法でも問題ありません。この場合、柔軟性を犠牲にすることになりますが、それが許容範囲であれば問題ありません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Attributeの変更に応答する
UIの更新や他のゲームプレイのために`Attribute`が変更されたときにリッスンするには、`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`を使用します。この関数はデリゲートを返し、`Attribute`が変更されるたびに自動的に呼び出されます。デリゲートは`FOnAttributeChangeData`パラメータを提供し、`NewValue`、`OldValue`、および`FGameplayEffectModCallbackData`を含みます。**注意:** `FGameplayEffectModCallbackData`はサーバーでのみ設定されます。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

サンプルプロジェクトでは、`GDPlayerState`で`Attribute`値変更デリゲートにバインドしてHUDを更新し、ヘルスがゼロになったときにプレイヤーの死亡に応答します。

この処理を`ASyncTask`にラップしたカスタムBlueprintノードがサンプルプロジェクトに含まれています。これは`UI_HUD`のUMGウィジェットでヘルス、マナ、スタミナの値を更新するために使用されています。この`AsyncTask`は手動で`EndTask()`を呼び出すまで永続します。UMGウィジェットの`Destruct`イベントでこれを行います。詳細は`AsyncTaskAttributeChanged.h/cpp`を参照してください。

![Attribute変更をリッスンするBPノード](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attribute
1つまたは複数の他の`Attribute`から値の一部または全部が派生する`Attribute`を作成するには、1つ以上の`Attribute Base`または[`MMC`](#concepts-ge-mmc) [`Derived`](#concepts-ge-mods)を持つ`Infinite`型の`GameplayEffect`を使用します。`Derived Attribute`は、それに依存する`Attribute`が更新されるたびに自動的に更新されます。

`Derived Attribute`のすべての`Derived`に対する最終的な計算式は、`Modifier Aggregators`の計算式と同じです。特定の順序で計算を行う必要がある場合は、すべてを`MMC`内で行います。

```
((CurrentValue + Additive) * Multiplicative) / Division
```

**注意:** PIEで複数のクライアントを使用する場合、エディタ設定で`Run Under One Process`を無効にする必要があります。そうしないと、最初のクライアント以外で独立した`Attribute`が更新されても`Derived Attribute`が更新されません。

この例では、`Infinite`型の`GameplayEffect`を使用して、`TestAttrA`の値を`TestAttrB`と`TestAttrC`という`Attribute`から`TestAttrA = (TestAttrA + TestAttrB) * (2 * TestAttrC)`という式で派生させています。`TestAttrA`は、これらの`Attribute`が値を更新するたびに自動的に再計算されます。

![Derived Attributeの例](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Setの定義
`Attribute Set`は、`Attribute`を定義し、保持し、変更を管理します。開発者は[`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)からサブクラス化する必要があります。`OwnerActor`のコンストラクタ内で`Attribute Set`を作成すると、自動的にその`ASC`に登録されます。**これはC++で行う必要があります**。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Setの設計
`ASC`には1つまたは複数の`Attribute Set`を持たせることができます。`Attribute Set`はメモリのオーバーヘッドがほとんどないため、いくつの`Attribute Set`を使用するかは開発者の判断に委ねられます。

すべての`アクター`で共有される1つの大きなモノリシックな`Attribute Set`を使用し、必要に応じて`Attribute`を使用し、未使用の`Attribute`は無視する方法も許容されます。

また、`Attribute`をグループ化した複数の`Attribute Set`を作成し、必要に応じて`アクター`に選択的に追加する方法もあります。例えば、ヘルス関連の`Attribute`用の`Attribute Set`、マナ関連の`Attribute`用の`Attribute Set`などを作成できます。MOBAゲームでは、ヒーローはマナが必要ですが、ミニオンは必要ない場合があります。そのため、ヒーローにはマナの`Attribute Set`を追加し、ミニオンには追加しないという選択が可能です。

さらに、`Attribute Set`をサブクラス化することで、`アクター`が持つ`Attribute`を選択的に決定することもできます。`Attribute`は内部的に`AttributeSetClassName.AttributeName`として参照されます。`Attribute Set`をサブクラス化すると、親クラスのすべての`Attribute`は引き続き親クラスの名前をプレフィックスとして持ちます。

複数の`Attribute Set`を持つことは可能ですが、同じクラスの`Attribute Set`を1つの`ASC`に複数持たせるべきではありません。同じクラスの`Attribute Set`が複数ある場合、どの`Attribute Set`を使用するかが不明確になり、1つだけが選択されます。

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 個別のAttributeを持つサブコンポーネント
`Pawn`に複数のダメージ可能なコンポーネント（個別にダメージを受けるアーマーピースなど）がある場合、`Pawn`が持つ最大数のダメージ可能なコンポーネントを想定して、それに対応する数のヘルス`Attribute`を1つの`Attribute Set`に作成することをお勧めします。例えば、DamageableCompHealth0、DamageableCompHealth1などのように、これらのコンポーネントを論理的な「スロット」として表現します。ダメージ可能なコンポーネントクラスのインスタンスでは、どのスロット番号の`Attribute`を使用するかを割り当て、`GameplayAbilities`や[`Execution`](#concepts-ge-ec)でどの`Attribute`にダメージを適用するかを判断できるようにします。`Pawn`が最大数より少ない、またはゼロのダメージ可能なコンポーネントを持つ場合でも問題ありません。`Attribute Set`に`Attribute`が含まれていても、使用しない限りメモリへの影響はほとんどありません。

サブコンポーネントが多くの`Attribute`を必要とする場合や、サブコンポーネントの数が無制限である場合、またはサブコンポーネントがデタッチされて他のプレイヤーに使用される可能性がある場合（例: 武器）、このアプローチが適さない場合は、`Attribute`の代わりに通常の浮動小数点値をコンポーネントに格納する方法を検討してください。[アイテムAttribute](#concepts-as-design-itemattributes)を参照してください。

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 実行時にAttribute Setを追加・削除する
`Attribute Set`は実行時に`ASC`に追加および削除することができます。ただし、`Attribute Set`の削除は危険を伴います。例えば、クライアントで`Attribute Set`がサーバーよりも先に削除され、`Attribute`値の変更がクライアントにレプリケートされると、`Attribute`がその`Attribute Set`を見つけられず、ゲームがクラッシュする可能性があります。

インベントリに武器を追加する際のコード例:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

インベントリから武器を削除する際のコード例:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 アイテムAttribute（武器の弾薬）
装備可能なアイテムに`Attribute`（武器の弾薬、アーマーの耐久性など）を実装する方法はいくつかあります。これらのアプローチはすべて、値をアイテムに直接格納します。これは、アイテムがそのライフサイクル中に複数のプレイヤーに装備される可能性がある場合に必要です。

> 1. アイテムに通常の浮動小数点値を使用する（**推奨**）
> 2. アイテムに個別の`Attribute Set`を使用する
> 3. アイテムに個別の`ASC`を使用する

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 アイテムに通常の浮動小数点値を使用する
`Attribute`の代わりに、アイテムクラスのインスタンスに通常の浮動小数点値を格納します。Fortniteや[GASShooter](https://github.com/tranek/GASShooter)では、武器の弾薬をこの方法で管理しています。例えば、武器には最大クリップサイズ、現在のクリップ内の弾薬、予備弾薬などを直接レプリケートされた浮動小数点値（`COND_OwnerOnly`）として格納します。武器が予備弾薬を共有する場合、予備弾薬をキャラクターの共有弾薬`Attribute Set`の`Attribute`として移動します（リロードアビリティは`Cost GE`を使用して予備弾薬から武器のクリップ弾薬に移動できます）。現在のクリップ弾薬に`Attribute`を使用しないため、`UGameplayAbility`のいくつかの関数をオーバーライドして、武器の浮動小数点値に対してコストをチェックおよび適用する必要があります。アビリティを付与する際に[`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)の`SourceObject`として武器を設定すると、アビリティ内でその武器にアクセスできます。

自動射撃中に弾薬量がレプリケートされてローカルの弾薬量を上書きしないようにするため、`PreReplication()`でプレイヤーが`IsFiring`の`GameplayTag`を持っている間はレプリケーションを無効にします。この方法では、独自のローカル予測を行っています。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

**利点:**
1. `Attribute Set`を使用する際の制約を回避できる（以下参照）

**制約:**
1. 既存の`GameplayEffect`ワークフロー（弾薬使用の`Cost GE`など）を使用できない
2. 武器の浮動小数点値に対してコストをチェックおよび適用するために、`UGameplayAbility`の主要な関数をオーバーライドする必要がある

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

利点:
1. `AttributeSets`を使用する際の制約を回避できる（以下参照）

制約:
1. 既存の`GameplayEffect`ワークフロー（弾薬使用の`Cost GEs`など）を使用できない
1. 武器の浮動小数点値に対してコストをチェックおよび適用するために、`UGameplayAbility`の主要な関数をオーバーライドする必要がある

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 アイテムに`AttributeSet`を使用する
アイテムに個別の`AttributeSet`を使用し、それを[プレイヤーの`ASC`に追加する](#concepts-as-design-addremoveruntime)方法は機能しますが、いくつかの大きな制約があります。これは[GASShooter](https://github.com/tranek/GASShooter)の初期バージョンで武器の弾薬管理に使用されていました。武器は、最大クリップサイズ、現在のクリップ内の弾薬、予備弾薬などの`Attribute`を武器クラスに属する`AttributeSet`に格納します。武器が予備弾薬を共有する場合、予備弾薬をキャラクターの共有弾薬`AttributeSet`に移動します。サーバーで武器がプレイヤーのインベントリに追加されると、武器はその`AttributeSet`をプレイヤーの`ASC::SpawnedAttributes`に追加します。その後、サーバーはこれをクライアントにレプリケートします。武器がインベントリから削除されると、その`AttributeSet`も`ASC::SpawnedAttributes`から削除されます。

`AttributeSet`が`OwnerActor`以外の場所（例えば武器）に存在する場合、`AttributeSet`でいくつかのコンパイルエラーが発生することがあります。この問題を解決するには、`AttributeSet`をコンストラクタではなく`BeginPlay()`で構築し、武器に`IAbilitySystemInterface`を実装して（武器をプレイヤーのインベントリに追加する際に`ASC`へのポインタを設定）、エラーを回避します。

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

この[古いバージョンのGASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)を確認することで実際の動作を確認できます。

**利点:**
1. 既存の`GameplayAbility`および`GameplayEffect`のワークフロー（弾薬使用の`Cost GEs`など）を使用可能
2. 非常に少数のアイテムに対して簡単にセットアップ可能

**制約:**
1. 武器タイプごとに新しい`AttributeSet`クラスを作成する必要がある。`ASC`は、`AttributeSet`クラスのインスタンスを1つしか機能的に持つことができないため、`Attribute`の変更は`ASC`の`SpawnedAttributes`配列内で最初に見つかった`AttributeSet`クラスのインスタンスを探します。同じ`AttributeSet`クラスの追加インスタンスは無視されます。
2. プレイヤーのインベントリ内に同じタイプの武器を複数持つことができない（前述の理由により、`AttributeSet`クラスごとに1つのインスタンスしか持てないため）。
3. `AttributeSet`の削除は危険を伴う。GASShooterでは、プレイヤーがロケットで自爆した場合、プレイヤーは即座にロケットランチャーをインベントリから削除し（`ASC`からその`AttributeSet`も削除）、サーバーがロケットランチャーの弾薬`Attribute`の変更をレプリケートした際、クライアントの`ASC`にその`AttributeSet`が存在しなくなり、ゲームがクラッシュすることがありました。

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 アイテムに`ASC`を持たせる
各アイテムに個別の`AbilitySystemComponent`を持たせるのは極端なアプローチです。個人的にはこの方法を試したことはなく、実際に使用されている例も見たことがありません。これを機能させるには多くのエンジニアリングが必要です。

> 同じ所有者を持ちつつ異なるアバター（例: プレイヤーの状態に設定されたポーンや武器/アイテム/プロジェクタイル）を持つ複数の`AbilitySystemComponent`を持つことは可能ですか？
> 
> 最初の問題は、所有するアクターに`IGameplayTagAssetInterface`と`IAbilitySystemInterface`を実装することです。前者は可能かもしれません: すべての`ASC`からタグを集約する（ただし注意が必要です - `HasAllMatchingGameplayTags`はクロスASC集約によってのみ満たされる可能性があります。各ASCへの呼び出しを転送して結果をORするだけでは不十分です）。しかし、後者はさらに難しいです: どのASCが権限を持つものと見なされるべきでしょうか？誰かがGEを適用したい場合、どのASCがそれを受け取るべきでしょうか？これらの問題を解決できるかもしれませんが、この側面が最も難しい部分になるでしょう: 所有者が複数のASCを持つことになります。
> 
> ポーンと武器に別々のASCを持たせること自体は理にかなっています。例えば、武器を説明するタグと所有するポーンを説明するタグを区別することができます。武器に付与されたタグが所有者にも「適用」されるが、それ以外には適用されない（例: AttributeやGEは独立しているが、所有者は所有するタグを集約する）というのは理にかなっているかもしれません。これが機能する可能性は十分にあります。しかし、同じ所有者を持つ複数のASCを持つことはやや危険です。

*EpicのDave Rattiによる[コミュニティ質問#6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)への回答*

**利点:**
1. 既存の`GameplayAbility`および`GameplayEffect`のワークフロー（弾薬使用の`Cost GEs`など）を使用可能
2. `AttributeSet`クラスを再利用可能（各武器のASCに1つずつ）

**制約:**
1. エンジニアリングコストが不明
2. 実現可能かどうかも不明

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Attributeの定義
**`Attribute`はC++でのみ定義可能**であり、`AttributeSet`のヘッダーファイル内で定義します。以下のマクロブロックをすべての`AttributeSet`ヘッダーファイルの先頭に追加することを推奨します。このマクロは、`Attribute`のためのゲッターおよびセッター関数を自動的に生成します。

```c++
// AttributeSet.hからのマクロを使用
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

レプリケーションされるヘルスAttributeは以下のように定義します:

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

また、ヘッダーファイル内で`OnRep`関数を定義します:
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`の.cppファイルでは、予測システムで使用される`GAMEPLAYATTRIBUTE_REPNOTIFY`マクロを使用して`OnRep`関数を実装します:
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最後に、`Attribute`を`GetLifetimeReplicatedProps`に追加します:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`は、ローカル値がサーバーからレプリケートされた値とすでに等しい場合でも`OnRep`関数をトリガーするように指示します（予測のため）。デフォルトでは、ローカル値がサーバーからレプリケートされた値と同じ場合、`OnRep`関数はトリガーされません。

もし`Attribute`が`Meta Attribute`のようにレプリケートされない場合、`OnRep`および`GetLifetimeReplicatedProps`の手順をスキップできます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Attributeの初期化
`Attribute`（`BaseValue`およびそれに伴う`CurrentValue`）を初期化する方法はいくつかあります。Epicはインスタント型の`GameplayEffect`を使用することを推奨しています。この方法はサンプルプロジェクトでも使用されています。

サンプルプロジェクトの`GE_HeroAttributes` Blueprintを参照すると、`Attribute`を初期化するためのインスタント型`GameplayEffect`の作成方法がわかります。この`GameplayEffect`の適用はC++で行われます。

もし`ATTRIBUTE_ACCESSORS`マクロを使用して`Attribute`を定義している場合、各`Attribute`に対して自動的に初期化関数が生成されます。この関数をC++内で任意のタイミングで呼び出すことができます。

```c++
// `ATTRIBUTE_ACCESSORS`マクロで定義された`Health`Attributeに対して自動生成された初期化関数
AttributeSet->InitHealth(100.0f);
```

`AttributeSet.h`には、他の`Attribute`を初期化する方法も記載されています。

**注意:** バージョン4.24以前では、`FAttributeSetInitterDiscreteLevels`は`FGameplayAttributeData`と互換性がありませんでした。この仕組みは、`Attribute`が単なる浮動小数点値だった時代に作られたもので、`FGameplayAttributeData`が`Plain Old Data`（`POD`）ではないとエラーが発生します。この問題はバージョン4.24で修正されています（https://issues.unrealengine.com/issue/UE-76557）。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`は、`AttributeSet`内で`Attribute`の`CurrentValue`が変更される前に呼び出される主要な関数の1つです。この関数は、参照パラメータ`NewValue`を使用して`CurrentValue`の変更をクランプ（制限）するのに最適な場所です。

例えば、移動速度の修正値をクランプする場合、サンプルプロジェクトでは以下のように実装されています:
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 移動速度を150～1000の範囲内に制限
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()`関数は、`AttributeSet.h`に追加したマクロブロックによって生成されます（[Attributeの定義](#concepts-as-attributes)を参照）。

この関数は、`Attribute`の変更が`Attribute`セッター（`AttributeSet.h`のマクロブロックで定義）や[`GameplayEffects`](#concepts-ge)を使用して行われる場合にトリガーされます。

**注意:** この関数内で行われるクランプ処理は、`ASC`上の修飾子を永続的に変更するものではありません。クランプ処理は、修飾子をクエリした際に返される値にのみ影響します。そのため、[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)や[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)など、`CurrentValue`を再計算する処理では、再度クランプ処理を実装する必要があります。

**注意:** Epicのコメントによると、`PreAttributeChange()`はゲームプレイイベントには使用せず、主にクランプ処理に使用することが推奨されています。`Attribute`の変更に対するゲームプレイイベントは、`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（[Attribute変更への応答](#concepts-a-changes)）を使用することが推奨されています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`は、インスタント型[`GameplayEffect`](#concepts-ge)による`Attribute`の`BaseValue`の変更後にのみトリガーされます。この関数は、`GameplayEffect`による`Attribute`の変更後にさらなる操作を行うのに適した場所です。

例えば、サンプルプロジェクトでは、最終的なダメージ`Meta Attribute`をヘルス`Attribute`から差し引く処理をここで行っています。もしシールド`Attribute`が存在する場合、ダメージをまずシールドから差し引き、残りをヘルスから差し引くことができます。また、サンプルプロジェクトでは、この関数を使用してヒットリアクションアニメーションの適用、ダメージ数値の表示、キラーへの経験値やゴールド報酬の割り当てを行っています。設計上、ダメージ`Meta Attribute`は常にインスタント型`GameplayEffect`を通じて処理され、`Attribute`セッターを使用することはありません。

また、マナやスタミナのようにインスタント型`GameplayEffect`によってのみ`BaseValue`が変更される`Attribute`についても、ここで最大値に対応する`Attribute`にクランプすることができます。

**注意:** `PostGameplayEffectExecute()`が呼び出された時点で、`Attribute`の変更はすでに行われていますが、まだクライアントにレプリケートされていません。そのため、ここで値をクランプしても、クライアントへのネットワーク更新が2回発生することはありません。クライアントはクランプ後の値のみを受け取ります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`は、このセット内の`Attribute`に対して`Aggregator`が作成されたときにトリガーされます。これにより、[`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)のカスタムセットアップが可能になります。`AggregatorEvaluateMetaData`は、`Aggregator`がすべての[`Modifiers`](#concepts-ge-mods)を基に`Attribute`の`CurrentValue`を評価する際に使用されます。デフォルトでは、`AggregatorEvaluateMetaData`は`Aggregator`がどの`Modifiers`を適用するかを決定するために使用されます。例えば、`MostNegativeMod_AllPositiveMods`はすべての正の`Modifiers`を許可し、負の`Modifiers`は最も負のものだけを適用するように制限します。これは、Paragonでプレイヤーに適用される移動速度低下効果を、最も強いものだけに制限しつつ、すべての移動速度バフを適用するために使用されました。適用されない`Modifiers`は`ASC`に存在し続けますが、最終的な`CurrentValue`には集約されません。条件が変化した場合（例えば、最も負の`Modifier`が期限切れになった場合）、次に負の値が大きい`Modifier`が適用される可能性があります。

以下は、最も負の`Modifier`とすべての正の`Modifiers`を許可する例です:

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

カスタムの`AggregatorEvaluateMetaData`は、`FAggregatorEvaluateMetaDataLibrary`に静的変数として追加する必要があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effectの定義
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html)（`GE`）は、アビリティが自身や他者の[`Attributes`](#concepts-a)や[`GameplayTags`](#concepts-gt)を変更するための手段です。これにより、ダメージや回復のような即時の`Attribute`変更や、移動速度の増加やスタンのような長期的なバフ/デバフを適用できます。`UGameplayEffect`クラスは、単一のゲームプレイ効果を定義する**データ専用**クラスとして設計されています。`GameplayEffects`に追加のロジックを追加するべきではありません。通常、デザイナーは`UGameplayEffect`のBlueprint子クラスを多数作成します。

`GameplayEffects`は、[`Modifiers`](#concepts-ge-mods)や[`Executions`（`GameplayEffectExecutionCalculation`）](#concepts-ge-ec)を通じて`Attributes`を変更します。

`GameplayEffects`には、`Instant`、`Duration`、`Infinite`の3つの期間タイプがあります。

さらに、`GameplayEffects`は[`GameplayCues`](#concepts-gc)を追加/実行することができます。`Instant`型の`GameplayEffect`は`GameplayCue`の`GameplayTags`に対して`Execute`を呼び出し、`Duration`または`Infinite`型の`GameplayEffect`は`Add`と`Remove`を呼び出します。

| 期間タイプ   | GameplayCueイベント | 使用する場面                                                                                                                                                                                                 |
| ------------ | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`    | Execute             | `Attribute`の`BaseValue`に即時かつ永続的な変更を加える場合に使用します。`GameplayTags`は適用されません（1フレームも適用されません）。                                                                 |
| `Duration`   | Add & Remove        | `Attribute`の`CurrentValue`に一時的な変更を加え、`GameplayTags`を適用します。これらは`GameplayEffect`が期限切れになるか手動で削除されると解除されます。期間は`UGameplayEffect`クラス/Blueprintで指定されます。 |
| `Infinite`   | Add & Remove        | `Attribute`の`CurrentValue`に一時的な変更を加え、`GameplayTags`を適用します。これらは自動的には期限切れにならず、アビリティや`ASC`によって手動で削除される必要があります。                              |

`Duration`および`Infinite`型の`GameplayEffects`は、`Period`で定義された間隔ごとにその`Modifiers`や`Executions`を適用する`Periodic Effects`を適用するオプションがあります。`Periodic Effects`は、`Attribute`の`BaseValue`を変更し、`GameplayCues`を実行する際には`Instant`型の`GameplayEffects`として扱われます。これらは、継続的なダメージ（DOT）タイプの効果に便利です。**注意:** `Periodic Effects`は[予測](#concepts-p)できません。

`Duration`および`Infinite`型の`GameplayEffects`は、`Ongoing Tag Requirements`が満たされない場合、一時的に無効化され、満たされた場合に再度有効化されます。無効化された`GameplayEffect`は、その`Modifiers`や適用された`GameplayTags`の効果を削除しますが、`GameplayEffect`自体は削除されません。有効化されると、再びその`Modifiers`や`GameplayTags`が適用されます。

もし`Duration`または`Infinite`型の`GameplayEffect`の`Modifiers`を手動で再計算する必要がある場合（例えば、`MMC`が`Attributes`以外のデータを使用している場合）、`UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`を呼び出して、現在のレベルを再設定することができます。この際、`UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`を使用して現在のレベルを取得します。`Attributes`に基づく`Modifiers`は、それらの`Attributes`が更新されると自動的に更新されます。`SetActiveGameplayEffectLevel()`の主要な関数は以下の通りです:

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects`は通常インスタンス化されません。アビリティや`ASC`が`GameplayEffect`を適用したい場合、その`GameplayEffect`の`ClassDefaultObject`から[`GameplayEffectSpec`](#concepts-ge-spec)を作成します。適用に成功した`GameplayEffectSpecs`は、新しい構造体`FActiveGameplayEffect`に追加され、`ASC`はこれを特別なコンテナ構造体`ActiveGameplayEffects`内で管理します。

**[⬆ トップに戻る](#table-of-contents)**


<a name="concepts-ge-applying"></a>
#### 4.5.2 Gameplay Effectの適用
`GameplayEffects`は、[`GameplayAbilities`](#concepts-ga)の関数や`ASC`の関数を通じて適用することができ、通常は`ApplyGameplayEffectTo`の形式を取ります。これらの異なる関数は、最終的には`UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`を`Target`に対して呼び出す便利な関数です。

例えば、プロジェクタイルから`GameplayEffects`を適用する場合、`Target`の`ASC`を取得し、その関数の1つを使用して`ApplyGameplayEffectToSelf`を呼び出す必要があります。

`Duration`または`Infinite`型の`GameplayEffects`が`ASC`に適用されたときにリッスンするには、以下のようにデリゲートにバインドします:
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
コールバック関数:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

サーバーは常にこの関数を呼び出しますが、レプリケーションモードに応じて動作が異なります。自律プロキシは、`Full`および`Mixed`レプリケーションモードでレプリケートされた`GameplayEffects`に対してのみこの関数を呼び出します。シミュレートされたプロキシは、`Full`[レプリケーションモード](#concepts-asc-rm)でのみこの関数を呼び出します。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Gameplay Effectの削除
`GameplayEffects`は、[`GameplayAbilities`](#concepts-ga)の関数や`ASC`の関数を通じて削除することができ、通常は`RemoveActiveGameplayEffect`の形式を取ります。これらの異なる関数は、最終的には`FActiveGameplayEffectsContainer::RemoveActiveEffects()`を`Target`に対して呼び出す便利な関数です。

例えば、`GameplayAbility`の外で`GameplayEffects`を削除する場合、`Target`の`ASC`を取得し、その関数の1つを使用して`RemoveActiveGameplayEffect`を呼び出す必要があります。

`Duration`または`Infinite`型の`GameplayEffects`が`ASC`から削除されたときにリッスンするには、以下のようにデリゲートにバインドします:
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
コールバック関数:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

サーバーは常にこの関数を呼び出しますが、レプリケーションモードに応じて動作が異なります。自律プロキシは、`Full`および`Mixed`レプリケーションモードでレプリケートされた`GameplayEffects`に対してのみこの関数を呼び出します。シミュレートされたプロキシは、`Full`[レプリケーションモード](#concepts-asc-rm)でのみこの関数を呼び出します。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers
`Modifiers`は`Attribute`を変更し、[予測的に](#concepts-p)変更する唯一の方法です。`GameplayEffect`は0個または複数の`Modifiers`を持つことができます。各`Modifier`は、指定された操作を通じて1つの`Attribute`を変更する役割を担います。

| 操作       | 説明                                                                                     |
| ---------- | ---------------------------------------------------------------------------------------- |
| `Add`      | 結果を`Modifier`で指定された`Attribute`に加算します。減算する場合は負の値を使用します。    |
| `Multiply` | 結果を`Modifier`で指定された`Attribute`に乗算します。                                    |
| `Divide`   | 結果を`Modifier`で指定された`Attribute`に対して除算します。                              |
| `Override` | 結果で`Modifier`で指定された`Attribute`を上書きします。                                  |

`Attribute`の`CurrentValue`は、すべての`Modifiers`が`BaseValue`に加算された集約結果です。`Modifiers`がどのように集約されるかの計算式は、`GameplayEffectAggregator.cpp`内の`FAggregatorModChannel::EvaluateWithBase`で以下のように定義されています:
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

`Override`の`Modifiers`は、最後に適用された`Modifier`が優先されて最終値を上書きします。

**注意:** パーセンテージベースの変更の場合、加算の後に処理されるように`Multiply`操作を使用してください。

**注意:** [予測](#concepts-p)はパーセンテージ変更に問題を抱えることがあります。

`Modifiers`には4つのタイプがあります: Scalable Float、Attribute Based、Custom Calculation Class、Set By Caller。それぞれが浮動小数点値を生成し、その値を使用して指定された`Modifier`の操作に基づいて`Attribute`を変更します。

| `Modifier`タイプ            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats`は、変数を行、レベルを列として持つデータテーブルを指す構造体です。Scalable Floatsは、アビリティの現在のレベル（または`GameplayEffectSpec`で上書きされた場合は異なるレベル）で指定されたテーブル行の値を自動的に読み取ります。この値は係数によってさらに操作できます。データテーブル/行が指定されていない場合、値は1として扱われ、係数を使用してすべてのレベルで単一の値をハードコードできます。 ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Attribute Based`          | `Attribute Based`の`Modifiers`は、`Source`（`GameplayEffectSpec`を作成した者）または`Target`（`GameplayEffectSpec`を受け取った者）のバックアップ`Attribute`の`CurrentValue`または`BaseValue`を取得し、係数および係数前後の加算でさらに操作します。`Snapshotting`は、`GameplayEffectSpec`が作成された時点でバックアップ`Attribute`をキャプチャすることを意味し、スナップショットを使用しない場合は、`GameplayEffectSpec`が適用された時点で`Attribute`をキャプチャします。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `Custom Calculation Class`は、複雑な`Modifiers`に対して最も柔軟性を提供します。この`Modifier`は[`ModifierMagnitudeCalculation`](#concepts-ge-mmc)クラスを使用し、さらに係数および係数前後の加算で結果の浮動小数点値を操作できます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller`の`Modifiers`は、アビリティや`GameplayEffectSpec`を作成した者がランタイムで`GameplayEffectSpec`上で設定する値です。例えば、プレイヤーがボタンを押し続けてアビリティをチャージした時間に基づいてダメージを設定したい場合に使用します。`SetByCallers`は`GameplayEffectSpec`上に存在する`TMap<FGameplayTag, float>`です。この`Modifier`は、指定された`GameplayTag`に関連付けられた`SetByCaller`値を探すように`Aggregator`に指示します。`Modifiers`で使用される`SetByCallers`は、`GameplayTag`バージョンのみ使用可能です。`FName`バージョンはここでは無効です。`Modifier`が`SetByCaller`に設定されているが、正しい`GameplayTag`を持つ`SetByCaller`が`GameplayEffectSpec`上に存在しない場合、ゲームはランタイムエラーをスローし、値を0として返します。この場合、`Divide`操作で問題が発生する可能性があります。`SetByCallers`の使用方法については、[`SetByCallers`](#concepts-ge-spec-setbycaller)を参照してください。 |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 MultiplyとDivideの修飾子
デフォルトでは、すべての`Multiply`および`Divide`の`Derived`は、`Attribute`の`BaseValue`に乗算または除算される前に加算されます。

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
*from `GameplayEffectAggregator.cpp`*

`Multiply` と `Divide` の `Modifiers` は、この式において `Bias` 値が `1` となっています（`Addition` の場合、`Bias` は `0` です）。そのため、次のような式になります:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

この式は、いくつかの予期しない結果をもたらします。まず、この式はすべての修飾子を加算してから、それを `BaseValue` に乗算または除算します。多くの人は、これらを乗算または除算してから適用することを期待します。例えば、`1.5` の `Multiply` 修飾子が2つある場合、多くの人は `BaseValue` が `1.5 x 1.5 = 2.25` 倍になることを期待します。しかし、この式では `1.5` を加算して `BaseValue` を `2` 倍にします（`50%` の増加 + さらに `50%` の増加 = `100%` の増加）。これは、`GameplayPrediction.h` の例である `500` の基本速度に対する `10%` の速度バフが `550` になるというケースに基づいています。さらに別の `10%` の速度バフを加えると、`600` になります。

次に、この式には、Paragon を念頭に設計されたため、使用できる値に関する未記載のルールがあります。

`Multiply` と `Divide` の乗算加算式のルール:
* `(1未満の値は1つまで) AND (1以上2未満の値は任意の数)`
* `または (1つの値が2以上)`

この式の `Bias` は、基本的に `[1, 2)` の範囲にある数値の整数部分を差し引きます。最初の修飾子の `Bias` は、ループの前に設定された開始値（`Bias`）から差し引かれるため、単一の値が動作する理由や、`1未満` の値が `[1, 2)` の範囲内の数値と組み合わせて動作する理由が説明されます。

以下は `Multiply` の例です:  
修飾子: `0.5`  
`1 + (0.5 - 1) = 0.5`、正しい

修飾子: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`、誤り。期待値は `1`？複数の `1未満` の値を加算することは、乗算の観点から意味をなしません。Paragon は [最大の負の値のみを使用する](#cae-nonstackingge) ように設計されていたため、`BaseValue` に乗算される `1未満` の値は最大でも1つだけでした。

修飾子: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`、正しい

修飾子: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`、誤り。期待値は `10`。常に `修飾子の合計 - 修飾子の数 + 1` になります。

多くのゲームでは、`Multiply` と `Divide` の `Modifiers` を `BaseValue` に適用する前に乗算または除算することを望むでしょう。これを実現するには、`FAggregatorModChannel::EvaluateWithBase()` のエンジンコードを **変更する必要があります**。

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

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 修飾子におけるゲームプレイタグ

各[修飾子](#concepts-ge-mods)に対して`SourceTags`と`TargetTags`を設定できます。これらは`GameplayEffect`の[`Application Tag requirements`](#concepts-ge-tags)と同様に機能します。そのため、タグは効果が適用される際にのみ考慮されます。例えば、周期的または無限の効果を持つ場合、これらのタグは効果が最初に適用される際にのみ考慮され、各周期的な実行時には考慮されません。

`Attribute Based`修飾子は、`SourceTagFilter`と`TargetTagFilter`も設定できます。この修飾子の元となるAttributeの大きさを決定する際に、これらのフィルターを使用してそのAttributeに対する特定の修飾子を除外します。フィルターのタグをすべて持たないソースまたはターゲットの修飾子は除外されます。

具体的には次のようになります: ソースASCとターゲットASCのタグは`GameplayEffects`によってキャプチャされます。ソースASCのタグは`GameplayEffectSpec`が作成された時点でキャプチャされ、ターゲットASCのタグは効果が実行される際にキャプチャされます。無限または期間型の効果の修飾子が「適用資格がある」と判断される際（つまり、そのアグリゲーターが資格を持つ）にこれらのフィルターが設定されている場合、キャプチャされたタグがフィルターと比較されます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 スタッキングGameplay Effects
`GameplayEffects`はデフォルトで、新しい`GameplayEffectSpec`のインスタンスを適用し、既存のインスタンスを気にしません。ただし、`GameplayEffects`をスタックするように設定すると、新しいインスタンスを追加する代わりに、現在の`GameplayEffectSpec`のスタック数が変更されます。スタッキングは`Duration`および`Infinite`型の`GameplayEffects`でのみ機能します。

スタッキングには、ソースごとに集約する方法とターゲットごとに集約する方法の2種類があります。

| スタッキングタイプ       | 説明                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| ソースごとに集約 | ターゲット上のソース`ASC`ごとにスタックのインスタンスが分かれます。各ソースはX個のスタックを適用できます。                     |
| ターゲットごとに集約 | ソースに関係なく、ターゲット上には1つのスタックインスタンスのみが存在します。各ソースは共有スタック制限までスタックを適用できます。 |

スタックには、期限切れ、期間のリフレッシュ、周期のリセットに関するポリシーもあります。これらは`GameplayEffect`のBlueprint内でホバーツールチップとして説明されています。

サンプルプロジェクトには、`GameplayEffect`のスタック変更をリッスンするカスタムBlueprintノードが含まれています。HUDのUMGウィジェットはこれを使用して、プレイヤーが持つパッシブアーマースタックの量を更新します。この`AsyncTask`は手動で`EndTask()`を呼び出すまで永続します。UMGウィジェットの`Destruct`イベントでこれを行います。詳細は`AsyncTaskEffectStackChanged.h/cpp`を参照してください。

![GameplayEffectスタック変更をリッスンするBPノード](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 付与されたアビリティ
`GameplayEffects`は新しい`GameplayAbilities`を`ASCs`に付与することができます。`Duration`および`Infinite`型の`GameplayEffects`のみがアビリティを付与できます。

この機能の一般的な使用例として、他のプレイヤーに特定の行動を強制する場合があります。例えば、ノックバックや引き寄せを行う場合、対象に自動的にアクティブ化されるアビリティ（付与されたアビリティを自動的にアクティブ化する方法については[パッシブアビリティ](#concepts-ga-activating-passive)を参照）を付与する`GameplayEffect`を適用します。

デザイナーは、`GameplayEffect`が付与するアビリティ、付与するレベル、[入力にバインド](#concepts-ga-input)する方法、および付与されたアビリティの削除ポリシーを選択できます。

| Removal Policy             | 説明                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | `GameplayEffect`が削除されると、付与されたアビリティは即座にキャンセルされ、ターゲットから削除されます。                                                   |
| Remove Ability on End      | 付与されたアビリティは終了するまで許可され、その後ターゲットから削除されます。                                                                                                   |
| Do Nothing                 | 付与されたアビリティは、付与した`GameplayEffect`がターゲットから削除されても影響を受けません。アビリティは手動で削除されるまでターゲットに永続的に残ります。 |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect Tags
`GameplayEffects`は複数の[`GameplayTagContainers`](#concepts-gt)を持ちます。デザイナーは各カテゴリの`Added`および`Removed`の`GameplayTagContainers`を編集し、その結果がコンパイル時に`Combined`の`GameplayTagContainer`に表示されます。`Added`タグは、この`GameplayEffect`が追加する新しいタグで、親が以前持っていなかったものです。`Removed`タグは、親クラスが持っているが、このサブクラスにはないタグです。

| カテゴリ                          | 説明                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect`が持つタグです。これらは単独では機能を持たず、`GameplayEffect`を説明する目的でのみ使用されます。                                                                                                                                                                                                                                        |
| Granted Tags                      | `GameplayEffect`に存在し、さらに適用先の`ASC`にも付与されるタグです。このタグは`GameplayEffect`が削除されると`ASC`からも削除されます。これは`Duration`および`Infinite`型の`GameplayEffects`でのみ機能します。                                                                                                                             |
| Ongoing Tag Requirements          | 適用後、これらのタグは`GameplayEffect`がオンかオフかを決定します。`GameplayEffect`がオフであっても適用されている状態であることがあります。`Ongoing Tag Requirements`を満たさないために`GameplayEffect`がオフになっている場合でも、要件が満たされると`GameplayEffect`は再びオンになり、その修飾子を再適用します。これは`Duration`および`Infinite`型の`GameplayEffects`でのみ機能します。 |
| Application Tag Requirements | ターゲットに`GameplayEffect`を適用できるかどうかを決定するタグです。これらの要件が満たされない場合、`GameplayEffect`は適用されません。 |
| Remove Gameplay Effects with Tags | この`GameplayEffect`が正常に適用された場合、ターゲット上の`Asset Tags`または`Granted Tags`にこれらのタグのいずれかを持つ`GameplayEffects`はターゲットから削除されます。                                                                                                                                                                                            |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 免疫
`GameplayEffects`は、[`GameplayTags`](#concepts-gt)に基づいて他の`GameplayEffects`の適用をブロックする免疫を付与できます。免疫は、`Application Tag Requirements`のような他の方法を使用して効果的に実現できますが、このシステムを使用することで、免疫によって`GameplayEffects`がブロックされた場合に`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`デリゲートを利用できます。

`GrantedApplicationImmunityTags`は、ソース`ASC`（ソースアビリティの`AbilityTags`からのタグを含む）が指定されたタグのいずれかを持っているかどうかを確認します。これにより、特定のキャラクターやソースからのすべての`GameplayEffects`に対する免疫をタグに基づいて提供できます。

`Granted Application Immunity Query`は、受信した`GameplayEffectSpec`が指定されたクエリのいずれかに一致するかどうかを確認し、その適用をブロックまたは許可します。

クエリには、`GameplayEffect` Blueprint内で役立つホバーツールチップが用意されています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html)（`GESpec`）は、`GameplayEffects`のインスタンス化と考えることができます。これには、表現する`GameplayEffect`クラス、それが作成されたレベル、およびそれを作成した人物が含まれます。これらはランタイム中に自由に作成および変更でき、ターゲットに適用される前に調整可能です。一方、`GameplayEffects`はランタイム前にデザイナーによって作成されるべきです。`GameplayEffect`を適用する際には、`GameplayEffect`から`GameplayEffectSpec`が作成され、それがターゲットに適用されます。

`GameplayEffectSpecs`は、`UAbilitySystemComponent::MakeOutgoingSpec()`を使用して`GameplayEffects`から作成されます。この関数は`BlueprintCallable`です。`GameplayEffectSpecs`はすぐに適用する必要はありません。アビリティから作成されたプロジェクタイルに`GameplayEffectSpec`を渡し、後でプロジェクタイルがヒットしたターゲットに適用することが一般的です。`GameplayEffectSpecs`が正常に適用されると、`FActiveGameplayEffect`という新しい構造体が返されます。

`GameplayEffectSpec`の主な内容:
- この`GameplayEffectSpec`が作成された`GameplayEffect`クラス。
- この`GameplayEffectSpec`のレベル。通常はこれを作成したアビリティのレベルと同じですが、異なる場合もあります。
- この`GameplayEffectSpec`の期間。デフォルトでは`GameplayEffect`の期間と同じですが、異なる場合もあります。
- 周期的効果のための`GameplayEffectSpec`の周期。デフォルトでは`GameplayEffect`の周期と同じですが、異なる場合もあります。
- この`GameplayEffectSpec`の現在のスタック数。スタック制限は`GameplayEffect`にあります。
- この`GameplayEffectSpec`を作成した人物を示す[`GameplayEffectContextHandle`](#concepts-ge-context)。
- スナップショットによって作成時にキャプチャされた`Attributes`。
- ターゲットに付与される`DynamicGrantedTags`（`GameplayEffect`が付与する`GameplayTags`に加えて）。
- `DynamicAssetTags`（`GameplayEffect`が持つ`AssetTags`に加えて）。
- `SetByCaller`の`TMaps`。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers`は、`GameplayEffectSpec`が`GameplayTag`または`FName`に関連付けられた浮動小数点値を保持することを可能にします。これらはそれぞれの`TMap`（`TMap<FGameplayTag, float>`および`TMap<FName, float>`）に格納されます。これらは`GameplayEffect`の`Modifiers`として使用することも、汎用的な浮動小数点値の受け渡し手段として使用することもできます。アビリティ内で生成された数値データを[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)や[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)に渡す際に、`SetByCallers`を使用することが一般的です。

| `SetByCaller` Use | Notes                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | Must be defined ahead of time in the `GameplayEffect` class. Can only use the `GameplayTag` version. If one is defined on the `GameplayEffect` class but the `GameplayEffectSpec` does not have the corresponding tag and float value pair, the game will have a runtime error on application of the `GameplayEffectSpec` and return 0. This is a potential problem for a `Divide` operation. See [`Modifiers`](#concepts-ge-mods). |
| Elsewhere         | Does not need to be defined ahead of time anywhere. Reading a `SetByCaller` that does not exist on a `GameplayEffectSpec` can return a developer defined default value with optional warnings.                                                                                                                                                                                                                                      |

`SetByCaller`の値をBlueprintで割り当てるには、必要なバージョン（`GameplayTag`または`FName`）のBlueprintノードを使用します:

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

Blueprintで`SetByCaller`の値を読み取るには、Blueprintライブラリ内でカスタムノードを作成する必要があります。

C++で`SetByCaller`の値を割り当てるには、必要なバージョン（`GameplayTag`または`FName`）の関数を使用します:

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

C++で`SetByCaller`の値を読み取るには、必要なバージョン（`GameplayTag`または`FName`）の関数を使用します:

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

`GameplayTag`バージョンを使用することをお勧めします。これにより、Blueprintでのスペルミスを防ぐことができます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)構造体は、`GameplayEffectSpec`の発動者および[`TargetData`](#concepts-targeting-data)に関する情報を保持します。また、[`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、[`AttributeSets`](#concepts-as)、および[`GameplayCues`](#concepts-gc)間で任意のデータを渡すための構造体としてサブクラス化するのに適しています。

`GameplayEffectContext`をサブクラス化する手順:

1. `FGameplayEffectContext`をサブクラス化する
2. `FGameplayEffectContext::GetScriptStruct()`をオーバーライドする
3. `FGameplayEffectContext::Duplicate()`をオーバーライドする
4. 新しいデータをレプリケートする必要がある場合は、`FGameplayEffectContext::NetSerialize()`をオーバーライドする
5. 親構造体`FGameplayEffectContext`と同様に、サブクラス用に`TStructOpsTypeTraits`を実装する
6. [`AbilitySystemGlobals`](#concepts-asg)クラス内で`AllocGameplayEffectContext()`をオーバーライドし、サブクラスの新しいオブジェクトを返す

[GASShooter](https://github.com/tranek/GASShooter)では、`GameplayEffectContext`をサブクラス化して`TargetData`を追加し、特にショットガンのように複数の敵にヒットする場合に`GameplayCues`でアクセスできるようにしています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html)（`ModMagCalc`または`MMC`）は、`GameplayEffects`内の[`Modifiers`](#concepts-ge-mods)として使用される強力なクラスです。これらは[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)と似ていますが、機能が制限されており、最も重要なのは[予測可能](#concepts-p)であることです。その唯一の目的は、`CalculateBaseMagnitude_Implementation()`から浮動小数点値を返すことです。この関数をBlueprintやC++でサブクラス化してオーバーライドできます。

`MMCs`は、`Instant`、`Duration`、`Infinite`、または`Periodic`のいずれの期間の`GameplayEffects`でも使用できます。

`MMCs`の強みは、`GameplayEffect`の発動者またはターゲットの任意の数の`Attributes`の値をキャプチャし、`GameplayEffectSpec`に完全にアクセスして`GameplayTags`や`SetByCallers`を読み取る能力にあります。`Attributes`はスナップショット化することも、しないこともできます。スナップショット化された`Attributes`は`GameplayEffectSpec`が作成されたときにキャプチャされ、スナップショット化されていない`Attributes`は`GameplayEffectSpec`が適用されたときにキャプチャされ、`Infinite`および`Duration`の`GameplayEffects`では`Attribute`が変更されると自動的に更新されます。`Attributes`をキャプチャすると、`ASC`上の既存の修飾子からその`CurrentValue`が再計算されます。この再計算では、`AbilitySet`内の[`PreAttributeChange()`](#concepts-as-preattributechange)は実行されないため、再度クランプ処理を行う必要があります。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` | Automatically updates when `Attribute` changes for `Infinite` or `Duration` `GE` |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

`MMC`から得られる浮動小数点値は、`GameplayEffect`の`Modifier`内で係数や係数前後の加算によってさらに修正することができます。

以下は、`Target`のマナ`Attribute`をキャプチャし、毒効果によって減少させる`MMC`の例です。この減少量は、`Target`が持つマナの量や、`Target`が持つ可能性のあるタグによって変化します:
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef はヘッダーで定義された FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef はヘッダーで定義された FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// ソースとターゲットのタグを収集し、それがどのバフを使用するべきかに影響を与える可能性がある
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
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // ゼロ除算を回避

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// ターゲットがマナの半分以上を持っている場合、効果を2倍にする
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// ターゲットが "PoisonMana" に弱い場合、効果を2倍にする
		Reduction *= 2;
	}
	
	return Reduction;
}
```

`MMC` のコンストラクタで `FGameplayEffectAttributeCaptureDefinition` を `RelevantAttributesToCapture` に追加しないで `Attributes` をキャプチャしようとすると、キャプチャ中に Spec が不足しているというエラーが発生します。`Attributes` をキャプチャする必要がない場合は、`RelevantAttributesToCapture` に何も追加する必要はありません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)（`ExecutionCalculation`、`Execution`（プラグインのソースコード内でよく見られる用語）、または`ExecCalc`）は、`GameplayEffects`が`ASC`に変更を加えるための最も強力な方法です。[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)と同様に、`Attributes`をキャプチャし、必要に応じてスナップショットを作成することができます。`MMCs`とは異なり、複数の`Attribute`を変更したり、プログラマーが望む他の操作を実行することができます。このパワーと柔軟性の代償として、[予測](#concepts-p)することはできず、C++で実装する必要があります。

`ExecutionCalculations`は、`Instant`および`Periodic`の`GameplayEffects`でのみ使用できます。「Execute」という言葉が含まれるものは通常、これら2種類の`GameplayEffects`を指します。

スナップショットは、`GameplayEffectSpec`が作成された時点で`Attribute`をキャプチャします。一方、スナップショットを使用しない場合は、`GameplayEffectSpec`が適用された時点で`Attribute`をキャプチャします。`Attributes`をキャプチャすると、`ASC`上の既存の修飾子からその`CurrentValue`が再計算されます。この再計算では、`AbilitySet`内の[`PreAttributeChange()`](#concepts-as-preattributechange)は実行されないため、再度クランプ処理を行う必要があります。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

`Attribute`キャプチャを設定するには、EpicのActionRPGサンプルプロジェクトで設定されたパターンに従い、`Attributes`をキャプチャする方法を定義する構造体を作成し、その構造体のコンストラクタ内でそのコピーを1つ作成します。各`ExecCalc`に対してこのような構造体を作成します。**注意:** 各構造体は一意の名前を持つ必要があります。同じ名前を使用すると、`Attributes`のキャプチャが正しく動作せず、主に間違った`Attributes`の値をキャプチャする原因となります。

`Local Predicted`、`Server Only`、および`Server Initiated`の[`GameplayAbilities`](#concepts-ga)では、`ExecCalc`はサーバー上でのみ呼び出されます。

`Source`および`Target`の多くのAttributeを読み取る複雑な計算式に基づいて受け取るダメージを計算することが、`ExecCalc`の最も一般的な例です。付属のサンプルプロジェクトには、`GameplayEffectSpec`の[`SetByCaller`](#concepts-ge-spec-setbycaller)からダメージ値を読み取り、その値を`Target`からキャプチャしたアーマー`Attribute`に基づいて軽減する単純な`ExecCalc`が含まれています。詳細は`GDDamageExecCalculation.cpp/.h`を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 実行計算へのデータ送信
`Attributes`をキャプチャする以外にも、`ExecutionCalculation`にデータを送信するいくつかの方法があります。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
[`GameplayEffectSpec`](#concepts-ge-spec-setbycaller)に設定された任意の`SetByCallers`は、`ExecutionCalculation`内で直接読み取ることができます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 バッキングデータAttribute計算修飾子
`GameplayEffect`に値をハードコードしたい場合、キャプチャされた`Attributes`の1つをバッキングデータとして使用する`CalculationModifier`を使用してそれらを渡すことができます。

このスクリーンショットの例では、キャプチャされたダメージ`Attribute`に50を加えています。また、これを`Override`に設定して、ハードコードされた値のみを受け取るようにすることもできます。

![バッキングデータAttribute計算修飾子](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation`は、`Attribute`をキャプチャする際にこの値を読み取ります。

```c++
float Damage = 0.0f;
// ダメージGEに設定されたオプションのダメージ値をExecutionCalculationのCalculationModifierとしてキャプチャ
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 バッキングデータ一時変数計算修飾子
値を`GameplayEffect`にハードコードしたい場合、`Temporary Variable`またはC++で呼ばれる`Transient Aggregator`を使用する`CalculationModifier`を使用してそれらを渡すことができます。この`Temporary Variable`は`GameplayTag`に関連付けられています。

このスクリーンショットの例では、`Data.Damage`という`GameplayTag`を使用して`Temporary Variable`に50を加えています。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

`ExecutionCalculation`のコンストラクタにバッキング`Temporary Variables`を追加します:

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`は、この値を`Attribute`キャプチャ関数に似た特別なキャプチャ関数を使用して読み取ります。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context
カスタム[`GameplayEffectContext`](#concepts-ge-context)を`GameplayEffectSpec`に設定することで、`ExecutionCalculation`にデータを送信できます。

`ExecutionCalculation`内では、`FGameplayEffectCustomExecutionParameters`から`EffectContext`にアクセスできます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

`GameplayEffectSpec`または`EffectContext`に何か変更を加える必要がある場合:

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

`ExecutionCalculation`内で`GameplayEffectSpec`を変更する場合は注意してください。`GetOwningSpecForPreExecuteMod()`のコメントを参照してください。

```c++
/** 非constアクセス。特にAttributeキャプチャ後にSpecを変更する場合は注意してください。 */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 カスタム適用要件
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)（`CAR`）クラスは、`GameplayEffect`の単純な`GameplayTag`チェックに対して、デザイナーに適用可能かどうかを高度に制御する機能を提供します。これらはBlueprintでは`CanApplyGameplayEffect()`をオーバーライドし、C++では`CanApplyGameplayEffect_Implementation()`をオーバーライドすることで実装できます。

`CAR`を使用する例:
* `Target`が特定の量の`Attribute`を持っている必要がある場合
* `Target`が特定の数の`GameplayEffect`スタックを持っている必要がある場合

`CAR`は、`Target`にこの`GameplayEffect`のインスタンスが既に存在するかを確認し、既存のインスタンスの[期間を変更](#concepts-ge-duration)する代わりに新しいインスタンスを適用しない（`CanApplyGameplayEffect()`でfalseを返す）といった高度な処理も可能です。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 コストGameplay Effect
[`GameplayAbilities`](#concepts-ga)には、アビリティのコストとして使用するために特別に設計された`GameplayEffect`をオプションで設定できます。コストは、`ASC`が`GameplayAbility`をアクティブ化するために必要な`Attribute`の量を決定します。`GA`が`Cost GE`を負担できない場合、アクティブ化できません。この`Cost GE`は、1つ以上の`Modifiers`を持つ`Instant`型の`GameplayEffect`であり、`Attributes`から値を減算します。デフォルトでは、`Cost GE`は予測可能であることを意図しており、その能力を維持することが推奨されます。そのため、`ExecutionCalculations`を使用せず、複雑なコスト計算には`MMC`を使用することが推奨されます。

初めは、コストを持つ`GA`ごとに1つのユニークな`Cost GE`を持つことが一般的です。より高度な手法として、1つの`Cost GE`を複数の`GA`で再利用し、`Cost GE`から作成された`GameplayEffectSpec`を`GA`固有のデータ（コスト値は`GA`で定義）で変更する方法があります。**これは`Instanced`アビリティでのみ機能します。**

`Cost GE`を再利用する2つの方法:

1. **`MMC`を使用する。** これが最も簡単な方法です。`GameplayEffectSpec`から`GameplayAbility`インスタンスを読み取り、コスト値を取得する[`MMC`](#concepts-ge-mmc)を作成します。

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

この例では、コスト値は`GameplayAbility`子クラスに追加された`FScalableFloat`です。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![MMCを使用したCost GE](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **`UGameplayAbility::GetCostGameplayEffect()`をオーバーライドする。** この関数をオーバーライドし、[ランタイムで`GameplayEffect`を作成](#concepts-ge-dynamic)して`GameplayAbility`上のコスト値を読み取ります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 クールダウンGameplay Effect
[`GameplayAbilities`](#concepts-ga)には、アビリティのクールダウンとして使用するために特別に設計されたオプションの`GameplayEffect`があります。クールダウンは、アクティベーション後にアビリティが再度アクティベート可能になるまでの時間を決定します。`GA`がまだクールダウン中の場合、アクティベートできません。この`Cooldown GE`は、`Duration`型の`GameplayEffect`であり、`Modifiers`を持たず、`GameplayEffect`の`GrantedTags`にアビリティごとまたはスロットごとに一意の`GameplayTag`（"`Cooldown Tag`"）を設定する必要があります。`GA`は実際には`Cooldown GE`の存在ではなく、`Cooldown Tag`の存在をチェックします。デフォルトでは、`Cooldown GEs`は予測可能であることを意図しており、その能力を維持することが推奨されます。そのため、`ExecutionCalculations`を使用せず、複雑なクールダウン計算には`MMCs`を使用することが推奨されます。

初めは、クールダウンを持つ`GA`ごとに1つのユニークな`Cooldown GE`を持つことが一般的です。より高度な手法として、1つの`Cooldown GE`を複数の`GA`で再利用し、`Cooldown GE`から作成された`GameplayEffectSpec`を`GA`固有のデータ（クールダウン時間と`Cooldown Tag`は`GA`で定義）で変更する方法があります。**これは`Instanced`アビリティでのみ機能します。**

`Cooldown GE`を再利用する2つの方法:

1. **[`SetByCaller`](#concepts-ge-spec-setbycaller)を使用する。** これが最も簡単な方法です。共有`Cooldown GE`の期間を`SetByCaller`で`GameplayTag`に設定します。`GameplayAbility`サブクラスで、期間用のfloat / `FScalableFloat`、一意の`Cooldown Tag`用の`FGameplayTagContainer`、および`Cooldown Tag`と`Cooldown GE`のタグの和集合を返すための一時的な`FGameplayTagContainer`を定義します。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

次に、`UGameplayAbility::GetCooldownTags()`をオーバーライドして、`Cooldown Tags`と既存の`Cooldown GE`タグの和集合を返します。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最後に、`UGameplayAbility::ApplyCooldown()`をオーバーライドして、`Cooldown Tags`を注入し、`SetByCaller`をクールダウン`GameplayEffectSpec`に追加します。
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

この画像では、クールダウンの期間`Modifier`が`SetByCaller`に設定され、`Data Tag`として`Data.Cooldown`が使用されています。`Data.Cooldown`は上記コード内の`OurSetByCallerTag`に相当します。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **[`MMC`](#concepts-ge-mmc)を使用する。** これは上記と同じ設定ですが、`Cooldown GE`の期間を`SetByCaller`として設定する代わりに、期間を`Custom Calculation Class`として設定し、新しい`MMC`を指すようにします。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags()でポインタを返すための一時的なコンテナ。
// これは、CooldownTagsとCooldown GEのクールダウンタグの和集合になります。
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

次に、`UGameplayAbility::GetCooldownTags()`をオーバーライドして、`Cooldown Tags`と既存の`Cooldown GE`タグの和集合を返します。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTagsはCDO上のTempCooldownTagsに書き込むため、アビリティのクールダウンタグが変更された場合（別のスロットに移動した場合）にクリアします。
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最後に、`UGameplayAbility::ApplyCooldown()` をオーバーライドして、クールダウン `GameplayEffectSpec` に `Cooldown Tags` を注入します。
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

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 クールダウンGameplay Effectの残り時間を取得する
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
**注意:** クールダウンの残り時間をクライアントでクエリするには、複製された`GameplayEffects`を受信できる必要があります。これは`ASC`の[複製モード](#concepts-asc-rm)に依存します。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 クールダウンの開始と終了をリッスンする
クールダウンの開始をリッスンするには、`Cooldown GE`が適用されたときに`AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`にバインドするか、`Cooldown Tag`が追加されたときに`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`にバインドする方法があります。`Cooldown GE`が追加されたときにリッスンすることをお勧めします。これにより、適用された`GameplayEffectSpec`にアクセスでき、ローカルで予測されたものかサーバーの修正されたものかを判断できます。

クールダウンの終了をリッスンするには、`Cooldown GE`が削除されたときに`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`にバインドするか、`Cooldown Tag`が削除されたときに`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`にバインドする方法があります。`Cooldown Tag`が削除されたときにリッスンすることをお勧めします。サーバーの修正された`Cooldown GE`が到着すると、ローカルで予測されたものが削除され、`OnAnyGameplayEffectRemovedDelegate()`が発火しますが、まだクールダウン中である場合があります。一方、`Cooldown Tag`は予測された`Cooldown GE`の削除とサーバーの修正された`Cooldown GE`の適用中に変更されません。

**注意:** クライアントで`GameplayEffect`が追加または削除されるのをリッスンするには、複製された`GameplayEffects`を受信できる必要があります。これは`ASC`の[複製モード](#concepts-asc-rm)に依存します。

サンプルプロジェクトには、クールダウンの開始と終了をリッスンするカスタムBlueprintノードが含まれています。HUDのUMGウィジェットはこれを使用して、Meteorのクールダウンの残り時間を更新します。この`AsyncTask`は手動で`EndTask()`を呼び出すまで永続します。UMGウィジェットの`Destruct`イベントでこれを行います。詳細は[`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)を参照してください。

![クールダウン変更をリッスンするBPノード](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 クールダウンの予測
現在、クールダウンを完全に予測することはできません。ローカルで予測された`Cooldown GE`が適用されたときにUIのクールダウンタイマーを開始できますが、`GameplayAbility`の実際のクールダウンはサーバーのクールダウンの残り時間に依存します。プレイヤーのレイテンシーに応じて、ローカルで予測されたクールダウンが終了しても、サーバー上でまだクールダウン中である場合、サーバーのクールダウンが終了するまで`GameplayAbility`の再アクティベーションが防止されます。

サンプルプロジェクトでは、ローカルで予測されたクールダウンが開始されたときにMeteorアビリティのUIアイコンをグレーアウトし、サーバーの修正された`Cooldown GE`が到着したときにクールダウンタイマーを開始するようにしています。

この結果として、短いクールダウンを持つアビリティでは、高いレイテンシーを持つプレイヤーが低いレイテンシーを持つプレイヤーよりも発射速度が遅くなり、不利になります。Fortniteでは、武器がカスタムの管理を持ち、クールダウン`GameplayEffects`を使用しないことでこれを回避しています。

真の予測クールダウン（ローカルのクールダウンが終了したときにプレイヤーが`GameplayAbility`をアクティベートできるが、サーバーはまだクールダウン中である）を可能にすることは、Epicが[将来のGASのバージョン](#concepts-p-future)で実装したいと考えているものです。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 アクティブなGameplay Effectの期間を変更する
`Cooldown GE`や任意の`Duration`型`GameplayEffect`の残り時間を変更するには、`GameplayEffectSpec`の`Duration`を変更し、`StartServerWorldTime`を更新し、`CachedStartServerWorldTime`を更新し、`StartWorldTime`を更新し、`CheckDuration()`で期間のチェックを再実行する必要があります。これをサーバー上で行い、`FActiveGameplayEffect`をマークして変更をクライアントに複製します。
**注意:** これは`const_cast`を伴うため、Epicが意図した方法ではない可能性がありますが、現在のところ問題なく動作しています。

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

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 ランタイムで動的なGameplay Effectを作成する
ランタイムで動的な`GameplayEffects`を作成することは高度なトピックです。これを頻繁に行う必要はないでしょう。

`Instant`型の`GameplayEffects`のみがC++でランタイムにゼロから作成可能です。`Duration`型や`Infinite`型の`GameplayEffects`は、複製時に存在しない`GameplayEffect`クラス定義を探すため、ランタイムで動的に作成することはできません。この機能を実現するには、通常エディタで行うようにアーキタイプの`GameplayEffect`クラスを作成し、ランタイムで必要な内容に`GameplayEffectSpec`インスタンスをカスタマイズしてください。

ランタイムで作成された`Instant`型の`GameplayEffects`は、[ローカル予測](#concepts-p)された`GameplayAbility`内からも呼び出すことができます。ただし、動的作成が副作用を引き起こす可能性があるかどうかはまだ不明です。

##### 例

サンプルプロジェクトでは、キャラクターが致命的な一撃を受けた際に、そのキャラクターを倒したプレイヤーにゴールドと経験値を送るために、`AttributeSet`内でこれを作成しています。

```c++
```c++
// 動的なインスタントGameplay Effectを作成して報酬を与える
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

2つ目の例では、ローカルで予測された`GameplayAbility`内で作成されたランタイム`GameplayEffect`を示しています。使用は自己責任で行ってください（コード内のコメントを参照してください）！

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

		// ランタイムでGameplay Effectを作成
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // ランタイムGEではインスタントのみが機能します。

		// シンプルなスケーラブルフロート修飾子を追加。MyAttributeを42に上書きします。
		// 実際のアプリケーションでは、TriggerEventDataを介して渡された情報を使用します。
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Gameplay Effectを適用

		// GESpecをここで作成することで、ASCがGEクラスのデフォルトオブジェクトからGESpecを作成する挙動を回避します。
		// ランタイムGEの場合、これにより基本GameplayEffectクラスからGESpecが作成され、修飾子が失われます。
		// 注意: ここで行われている「ハック」が副作用を引き起こす可能性があるかどうかは不明です！
		// SpecはGEオブジェクトがGarbageCollectorによって収集されるのを防ぎます。Spec内でGEはUPROPERTYとして保持されます。
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new"を使用。ライフタイムはハンドル内の共有ポインタによって管理されます。
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect コンテナ
Epicの[Action RPG サンプルプロジェクト](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)では、`FGameplayEffectContainer`という構造体が実装されています。これは標準のGASには含まれていませんが、`GameplayEffects`や[`TargetData`](#concepts-targeting-data)を含むのに非常に便利です。これにより、`GameplayEffects`から`GameplayEffectSpecs`を作成したり、`GameplayEffectContext`にデフォルト値を設定したりする作業が自動化されます。`GameplayEffectContainer`を`GameplayAbility`内で作成し、それを生成されたプロジェクタイルに渡すのは非常に簡単で直感的です。サンプルプロジェクトには`GameplayEffectContainers`を実装していませんが、標準のGASでの作業方法を示すためです。ただし、これをプロジェクトに追加することを検討することを強くお勧めします。

`GameplayEffectContainers`内の`GESpecs`にアクセスして`SetByCallers`を追加するには、`FGameplayEffectContainer`を分解し、`GESpecs`配列内のインデックスを指定して`GESpec`参照にアクセスします。これは、アクセスしたい`GESpec`のインデックスを事前に知っている必要があります。

![GameplayEffectContainerを使用したSetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers`には、[ターゲティング](#concepts-targeting-containers)を効率的に行うためのオプションも含まれています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability の定義
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)（`GA`）は、ゲーム内で`Actor`が実行できるアクションやスキルを指します。例えば、スプリントしながら銃を撃つなど、複数の`GameplayAbility`を同時にアクティブにすることができます。これらはBlueprintまたはC++で作成できます。

`GameplayAbilities`の例:
* ジャンプ
* スプリント
* 銃を撃つ
* 一定時間ごとに攻撃を受け流す
* ポーションを使用する
* ドアを開ける
* 資源を収集する
* 建物を建設する

`GameplayAbilities`で実装すべきでないもの:
* 基本的な移動入力
* 一部のUIとのインタラクション - 例えば、ストアでアイテムを購入するために`GameplayAbility`を使用しないでください。

これらはルールではなく、あくまで推奨事項です。設計や実装によって異なる場合があります。

`GameplayAbilities`には、Attributeの変化量を修正したり、`GameplayAbility`の機能を変更したりするためのレベルがデフォルトで備わっています。

`GameplayAbilities`は[`Net Execution Policy`](#concepts-ga-net)に応じて、所有するクライアントおよび/またはサーバー上で実行されますが、シミュレートされたプロキシでは実行されません。`Net Execution Policy`は、`GameplayAbility`がローカルで[予測](#concepts-p)されるかどうかを決定します。これらには、[オプションのコストとクールダウン`GameplayEffects`](#concepts-ga-commit)のデフォルト動作が含まれています。`GameplayAbilities`は、[`AbilityTasks`](#concepts-at)を使用して、イベントの待機、Attributeの変化の待機、プレイヤーがターゲットを選択するのを待機、または`Root Motion Source`を使用してキャラクターを移動させるなど、時間を伴うアクションを実行します。**シミュレートされたクライアントは`GameplayAbilities`を実行しません**。代わりに、サーバーがアビリティを実行するとき、シミュレートされたプロキシで視覚的に再生する必要があるもの（アニメーションモンタージュなど）は、`AbilityTasks`または[`GameplayCues`](#concepts-gc)を通じて複製またはRPCされます。

すべての`GameplayAbilities`は、`ActivateAbility()`関数をオーバーライドしてゲームプレイロジックを実装します。`GameplayAbility`が完了またはキャンセルされたときに実行される追加のロジックを`EndAbility()`に追加することもできます。

シンプルな`GameplayAbility`のフローチャート:
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

より複雑な`GameplayAbility`のフローチャート:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

複雑なアビリティは、複数の`GameplayAbilities`を使用して実装することができ、これらは互いに（アクティベート、キャンセルなど）相互作用します。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 レプリケーションポリシー
このオプションは使用しないでください。この名前は誤解を招きやすく、必要ありません。`GameplayAbilitySpecs`はデフォルトでサーバーから所有するクライアントにレプリケートされます。前述のように、**`GameplayAbilities`はシミュレートされたプロキシでは実行されません**。これらは`AbilityTasks`や`GameplayCues`を使用して、シミュレートされたプロキシに視覚的な変更をレプリケートまたはRPCします。EpicのDave Rattiは、このオプションを将来的に削除したいと述べています。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 サーバーがリモートアビリティキャンセルを尊重する
このオプションは、問題を引き起こすことが多いです。クライアントの`GameplayAbility`がキャンセルまたは自然に終了した場合、サーバー側のバージョンも終了することを強制します。特に高いレイテンシーを持つプレイヤーが使用するローカル予測された`GameplayAbilities`では、この問題が重要です。一般的に、このオプションは無効にすることをお勧めします。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 入力を直接レプリケートする
このオプションを設定すると、入力の押下および解放イベントが常にサーバーにレプリケートされます。Epicはこれを使用せず、既存の入力関連`AbilityTasks`に組み込まれている`Generic Replicated Events`を使用することを推奨しています。

Epicのコメント:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 ASCへの入力のバインド
`ASC`は、入力アクションを直接バインドし、それらの入力を`GameplayAbilities`に割り当てることができます。入力アクションが`GameplayAbilities`に割り当てられると、`GameplayTag`の要件が満たされている場合に、入力が押されたときにそれらの`GameplayAbilities`が自動的にアクティベートされます。割り当てられた入力アクションは、入力に応答する組み込みの`AbilityTasks`を使用するために必要です。

`GameplayAbilities`をアクティベートするために割り当てられた入力アクションに加えて、`ASC`は`Confirm`および`Cancel`という汎用的な入力も受け付けます。これらの特別な入力は、`AbilityTasks`で[`Target Actors`](#concepts-targeting-actors)の確認やキャンセルなどに使用されます。

入力を`ASC`にバインドするには、まず入力アクション名をバイトに変換する列挙型を作成する必要があります。この列挙型の名前は、プロジェクト設定で使用される入力アクション名と正確に一致する必要があります。`DisplayName`は関係ありません。

サンプルプロジェクトから:
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

`ASC`が`Character`に存在する場合、`SetupPlayerInputComponent()`内で`ASC`へのバインド関数を含めます:
```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

`ASC`が`PlayerState`に存在する場合、`SetupPlayerInputComponent()`内で`PlayerState`がクライアントにまだ複製されていない可能性があるため、競合状態が発生する可能性があります。そのため、`SetupPlayerInputComponent()`と`OnRep_PlayerState()`の両方で入力をバインドすることをお勧めします。`OnRep_PlayerState()`だけでは不十分です。なぜなら、`PlayerState`が複製される前に`Actor`の`InputComponent`がnullである場合があり、`PlayerController`がクライアントに`ClientRestart()`を呼び出すよう指示して`InputComponent`を作成するまで待つ必要があるからです。サンプルプロジェクトでは、両方の場所でバインドを試み、プロセスを一度だけ実行するようにするブール値で制御しています。

**注意:** サンプルプロジェクトでは、列挙型内の`Confirm`と`Cancel`はプロジェクト設定内の入力アクション名（`ConfirmTarget`と`CancelTarget`）と一致していませんが、`BindAbilityActivationToInputComponent()`内でそれらのマッピングを提供しています。これらは特別なケースであり、マッピングを提供するため一致する必要はありませんが、一致させることも可能です。他のすべての列挙型内の入力は、プロジェクト設定内の入力アクション名と一致する必要があります。

特定の入力によってのみアクティブ化される`GameplayAbilities`（MOBAのように常に同じ「スロット」に存在するもの）については、`UGameplayAbility`サブクラスに変数を追加し、入力を定義できるようにすることをお勧めします。その後、アビリティを付与する際に`ClassDefaultObject`からこれを読み取ることができます。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 入力にバインドしてアビリティをアクティブ化しない
入力が押されたときに`GameplayAbilities`が自動的にアクティブ化されないようにしつつ、`AbilityTasks`で使用するために入力にバインドしたい場合、`UGameplayAbility`サブクラスに新しいブール変数`bActivateOnInput`を追加し、デフォルトで`true`に設定します。そして、`UAbilitySystemComponent::AbilityLocalInputPressed()`をオーバーライドします。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// GenericConfirm/CancelがこのInputIDに関連付けられており、GenericConfirm/Cancelのコールバックがバインドされている場合、入力を消費する
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
					// このInputIDが直接レプリケートされるべきであり、オーナーアクターが権限を持っていない場合、サーバーに入力が押されたことを通知する
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// InputPressedイベントを呼び出す。ここではレプリケートされない。リスナーがいる場合、InputPressedイベントをサーバーにレプリケートする可能性がある。
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// アビリティがアクティブでない場合、アクティブ化を試みる
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 アビリティの付与
`GameplayAbility`を`ASC`に付与することで、`ASC`の`ActivatableAbilities`リストに追加され、`GameplayTag`の要件を満たしていれば、自由に`GameplayAbility`をアクティブ化できるようになります。

`GameplayAbilities`はサーバー上で付与され、その後、自動的に[`GameplayAbilitySpec`](#concepts-ga-spec)が所有するクライアントに複製されます。他のクライアントやシミュレートされたプロキシには`GameplayAbilitySpec`は複製されません。

サンプルプロジェクトでは、`Character`クラスに`TArray<TSubclassOf<UGDGameplayAbility>>`を格納しており、ゲーム開始時にこれを読み取り、アビリティを付与しています。
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// アビリティを付与しますが、サーバー上でのみ実行します
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

これらの`GameplayAbilities`を付与する際、`UGameplayAbility`クラス、アビリティのレベル、バインドされる入力、そしてこの`GameplayAbility`をこの`ASC`に付与した`SourceObject`を使用して`GameplayAbilitySpecs`を作成しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 アビリティのアクティベーション
もし`GameplayAbility`が入力アクションに割り当てられている場合、入力が押され、その`GameplayTag`の要件を満たしていれば、自動的にアクティベートされます。しかし、これは常に望ましい方法ではない場合があります。`ASC`は他にも4つの方法で`GameplayAbilities`をアクティベートすることができます：`GameplayTag`、`GameplayAbility`クラス、`GameplayAbilitySpec`ハンドル、そしてイベントによるアクティベーションです。イベントによる`GameplayAbility`のアクティベーションでは、[イベントと共にデータのペイロードを渡す](#concepts-ga-data)ことが可能です。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```
イベントによって`GameplayAbility`をアクティブ化するには、`GameplayAbility`内でその`Triggers`を設定する必要があります。`GameplayTag`を割り当て、`GameplayEvent`のオプションを選択します。イベントを送信するには、関数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`を使用します。イベントによる`GameplayAbility`のアクティブ化では、データを含むペイロードを渡すことが可能です。

`GameplayAbility`の`Triggers`を使用すると、`GameplayTag`が追加または削除された際に`GameplayAbility`をアクティブ化することもできます。

**注意:** Blueprintでイベントから`GameplayAbility`をアクティブ化する場合は、`ActivateAbilityFromEvent`ノードを使用する必要があります。

**注意:** `GameplayAbility`が終了すべきタイミングで`EndAbility()`を呼び出すことを忘れないでください。ただし、常に動作し続けるパッシブなアビリティの場合は例外です。

**ローカル予測**された`GameplayAbilities`のアクティベーションシーケンス:
1. **所有クライアント**が`TryActivateAbility()`を呼び出す
2. `InternalTryActivateAbility()`を呼び出す
3. `CanActivateAbility()`を呼び出し、`GameplayTag`の要件が満たされているか、`ASC`がコストを負担できるか、`GameplayAbility`がクールダウン中でないか、他のインスタンスが現在アクティブでないかを確認して結果を返す
4. `CallServerTryActivateAbility()`を呼び出し、生成した`Prediction Key`を渡す
5. `CallActivateAbility()`を呼び出す
6. `PreActivate()`を呼び出す（Epicはこれを「定型的な初期化作業」と呼んでいます）
7. 最終的に`ActivateAbility()`を呼び出してアビリティをアクティブ化する

**サーバー**が`CallServerTryActivateAbility()`を受信:
1. `ServerTryActivateAbility()`を呼び出す
2. `InternalServerTryActivateAbility()`を呼び出す
3. `InternalTryActivateAbility()`を呼び出す
4. `CanActivateAbility()`を呼び出し、`GameplayTag`の要件が満たされているか、`ASC`がコストを負担できるか、`GameplayAbility`がクールダウン中でないか、他のインスタンスが現在アクティブでないかを確認して結果を返す
5. 成功した場合、`ClientActivateAbilitySucceed()`を呼び出し、サーバーによるアクティベーションが確認されたことをクライアントに通知し、`ActivationInfo`を更新し、`OnConfirmDelegate`デリゲートをブロードキャストする（これは入力確認とは異なります）
6. `CallActivateAbility()`を呼び出す
7. `PreActivate()`を呼び出す（Epicはこれを「定型的な初期化作業」と呼んでいます）
8. 最終的に`ActivateAbility()`を呼び出してアビリティをアクティブ化する

サーバーがアクティベーションに失敗した場合、`ClientActivateAbilityFailed()`を呼び出し、クライアントの`GameplayAbility`を即座に終了させ、予測された変更を元に戻します。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 パッシブアビリティ
自動的にアクティブ化され、継続的に動作するパッシブな`GameplayAbilities`を実装するには、`UGameplayAbility::OnAvatarSet()`をオーバーライドします。この関数は`GameplayAbility`が付与され、`AvatarActor`が設定されたときに自動的に呼び出されます。そして`TryActivateAbility()`を呼び出します。

`GameplayAbility`が付与された際にアクティブ化すべきかどうかを指定するために、カスタム`UGameplayAbility`クラスに`bool`型の変数を追加することをお勧めします。サンプルプロジェクトでは、この方法を使用してパッシブなアーマースタッキングアビリティを実装しています。

パッシブな`GameplayAbilities`は通常、[`Net Execution Policy`](#concepts-ga-net)として`Server Only`を持ちます。

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

エピックは、この関数をパッシブアビリティを開始し、`BeginPlay`タイプの処理を行う正しい場所として説明しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 アクティベーション失敗タグ

アビリティには、アクティベーションが失敗した理由を示すデフォルトのロジックがあります。これを有効にするには、デフォルトの失敗ケースに対応する`GameplayTags`を設定する必要があります。

以下のタグ（または独自の命名規則）をプロジェクトに追加してください:
```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

その後、これらを[`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13)に追加します:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

これで、アビリティのアクティベーションが失敗するたびに、対応する`GameplayTag`が出力ログメッセージに含まれるか、`showdebug AbilitySystem` HUDに表示されるようになります。
```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![アクティベーション失敗タグがshowdebug AbilitySystemに表示される](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 アビリティのキャンセル
アビリティ内からキャンセルするには、`CancelAbility()`を呼び出します。これにより、`EndAbility()`が呼び出され、その`WasCancelled`パラメータがtrueに設定されます。

外部からアビリティをキャンセルするには、`ASC`がいくつかの関数を提供しています:
```c++
/** 指定されたアビリティのCDOをキャンセルします。 */
void CancelAbility(UGameplayAbility* Ability);	

/** 指定されたスペックハンドルに対応するアビリティをキャンセルします。ハンドルが再アクティブ化されたアビリティに見つからない場合は何も起こりません。 */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** 指定されたタグを持つすべてのアビリティをキャンセルします。Ignoreインスタンスはキャンセルされません。 */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** タグに関係なくすべてのアビリティをキャンセルします。Ignoreインスタンスはキャンセルされません。 */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** すべてのアビリティをキャンセルし、残っているインスタンス化されたアビリティをすべて終了します。 */
virtual void DestroyActiveState();
```

**注意:** `CancelAllAbilities` は、`Non-Instanced` な `GameplayAbilities` を持つ場合、正しく動作しないようです。`Non-Instanced` な `GameplayAbility` にヒットして処理を中断するように見えます。`CancelAbilities` は `Non-Instanced` な `GameplayAbilities` をより適切に処理でき、サンプルプロジェクトではこれを使用しています（ジャンプは `Non-Instanced` な `GameplayAbility` です）。結果は環境によって異なる場合があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 アクティブなアビリティを取得する
初心者がよく「アクティブなアビリティを取得するにはどうすればいいですか？」と尋ねます。これは、変数を設定したり、キャンセルしたりするためです。複数の `GameplayAbility` が同時にアクティブになる可能性があるため、単一の「アクティブなアビリティ」というものは存在しません。代わりに、`ASC` の `ActivatableAbilities`（`ASC` が所有する付与された `GameplayAbilities`）リストを検索し、探している [`Asset` または `Granted` `GameplayTag`](#concepts-ga-tags) に一致するものを見つける必要があります。

`UAbilitySystemComponent::GetActivatableAbilities()` は、`TArray<FGameplayAbilitySpec>` を返し、それを反復処理できます。

`ASC` には、`GameplayTagContainer` をパラメータとして受け取り、リスト内の `GameplayAbilitySpecs` を手動で検索する代わりに支援するヘルパー関数もあります。`bOnlyAbilitiesThatSatisfyTagRequirements` パラメータは、`GameplayTag` 要件を満たし、現在アクティブ化可能な `GameplayAbilitySpecs` のみを返します。例えば、武器を持っている場合と素手の場合で異なる基本攻撃の `GameplayAbilities` があり、武器が装備されているかどうかに応じて正しいものがアクティブ化される場合に役立ちます。この関数に関する詳細は、Epic のコメントを参照してください。
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

目的の `FGameplayAbilitySpec` を取得したら、`IsActive()` を呼び出すことができます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 インスタンス化ポリシー
`GameplayAbility` の `Instancing Policy` は、アクティブ化時に `GameplayAbility` がインスタンス化されるかどうか、またどのようにインスタンス化されるかを決定します。

| `Instancing Policy`     | 説明                                                                                              | 使用例                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 各`ASC`は、アクティベーション間で再利用される`GameplayAbility`のインスタンスを1つだけ持ちます。    | これがおそらく最もよく使用する`Instancing Policy`です。どのアビリティにも使用でき、アクティベーション間での持続性を提供します。デザイナーは、必要に応じてアクティベーション間でリセットする変数を手動でリセットする責任があります。                                                                                                                                                               |
| Instanced Per Execution | `GameplayAbility`がアクティベートされるたびに、新しいインスタンスが作成されます。                  | これらの`GameplayAbilities`の利点は、アクティベートするたびに変数がリセットされることです。これらは、アクティベーションごとに新しい`GameplayAbilities`を生成するため、`Instanced Per Actor`よりもパフォーマンスが劣ります。サンプルプロジェクトではこれらは使用されていません。                                                                                                                                 |
| Non-Instanced           | `GameplayAbility`は`ClassDefaultObject`上で動作します。インスタンスは作成されません。              | これは3つの中で最もパフォーマンスが良いですが、制約が最も多いです。`Non-Instanced`の`GameplayAbilities`は状態を保持できないため、動的な変数や`AbilityTask`デリゲートへのバインドができません。最適な使用例は、MOBAやRTSでのミニオンの基本攻撃など、頻繁に使用されるシンプルなアビリティです。サンプルプロジェクトのジャンプ`GameplayAbility`は`Non-Instanced`です。 |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 ネット実行ポリシー
`GameplayAbility`の`Net Execution Policy`は、誰が`GameplayAbility`を実行するか、そしてどの順序で実行するかを決定します。

| `Net Execution Policy` | 説明                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility` は所有するクライアント上でのみ実行されます。これはローカルの見た目に関する変更のみを行うアビリティに役立つ場合があります。シングルプレイヤーゲームでは `Server Only` を使用するべきです。                                     |
| `Local Predicted`      | `Local Predicted` の `GameplayAbilities` は最初に所有するクライアント上でアクティブ化され、その後サーバー上で実行されます。サーバーのバージョンはクライアントが予測を誤った場合に修正を行います。[予測](#concepts-p) を参照してください。 |
| `Server Only`          | `GameplayAbility` はサーバー上でのみ実行されます。パッシブな `GameplayAbilities` は通常 `Server Only` になります。シングルプレイヤーゲームではこれを使用するべきです。                                                                  |
| `Server Initiated`     | `Server Initiated` の `GameplayAbilities` は最初にサーバー上でアクティブ化され、その後所有するクライアント上で実行されます。個人的にはこれをほとんど使用したことがありません。                                                                     |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 アビリティタグ
`GameplayAbilities` には組み込みのロジックを持つ `GameplayTagContainers` が付属しています。これらの `GameplayTags` はいずれも複製されません。

| `GameplayTag Container`     | 説明                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility` が所有する `GameplayTags`。これらは `GameplayAbility` を記述するための単なる `GameplayTags` です。                                                                              |
| `Cancel Abilities with Tag` | この `GameplayAbility` がアクティブ化されたときに、`Ability Tags` にこれらの `GameplayTags` を持つ他の `GameplayAbilities` がキャンセルされます。                                                   |
| `Block Abilities with Tag`  | この `GameplayAbility` がアクティブな間、`Ability Tags` にこれらの `GameplayTags` を持つ他の `GameplayAbilities` はアクティブ化をブロックされます。                                          |
| `Activation Owned Tags`     | この `GameplayAbility` がアクティブな間、これらの `GameplayTags` が `GameplayAbility` の所有者に付与されます。これらは複製されないことに注意してください。                                                    |
| `Activation Required Tags`  | 所有者がこれらの `GameplayTags` を**すべて**持っている場合にのみ、この `GameplayAbility` をアクティブ化できます。                                                                                                |
| `Activation Blocked Tags`   | 所有者がこれらの `GameplayTags` を**いずれか**持っている場合、この `GameplayAbility` をアクティブ化できません。                                                                                                  |
| `Source Required Tags`      | `Source` がこれらの `GameplayTags` を**すべて**持っている場合にのみ、この `GameplayAbility` をアクティブ化できます。`Source` の `GameplayTags` は、この `GameplayAbility` がイベントによってトリガーされた場合にのみ設定されます。 |
| `Source Blocked Tags`       | `Source` がこれらの `GameplayTags` を**いずれか**持っている場合、この `GameplayAbility` をアクティブ化できません。`Source` の `GameplayTags` は、この `GameplayAbility` がイベントによってトリガーされた場合にのみ設定されます。   |
| `Target Required Tags`      | `Target` がこれらの `GameplayTags` を**すべて**持っている場合にのみ、この `GameplayAbility` をアクティブ化できます。`Target` の `GameplayTags` は、この `GameplayAbility` がイベントによってトリガーされた場合にのみ設定されます。 |
| `Target Blocked Tags`       | `Target` がこれらの `GameplayTags` を**いずれか**持っている場合、この `GameplayAbility` をアクティブ化できません。`Target` の `GameplayTags` は、この `GameplayAbility` がイベントによってトリガーされた場合にのみ設定されます。   |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec
`GameplayAbilitySpec`は、`GameplayAbility`が付与された後に`ASC`上に存在し、アクティベート可能な`GameplayAbility`（`GameplayAbility`クラス、レベル、入力バインディング、ランタイム状態）を定義します。これらは`GameplayAbility`クラスとは別に保持される必要があります。

サーバー上で`GameplayAbility`が付与されると、サーバーは`GameplayAbilitySpec`を所有するクライアントに複製し、クライアントがそれをアクティベートできるようにします。

`GameplayAbilitySpec`をアクティベートすると、その`Instancing Policy`に応じて`GameplayAbility`のインスタンスが作成されます（`Non-Instanced`の`GameplayAbilities`の場合は作成されません）。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 アビリティへのデータの渡し方
`GameplayAbilities`の一般的なパラダイムは「アクティベート→データ生成→適用→終了」です。しかし、既存のデータを操作する必要がある場合もあります。GASは、`GameplayAbilities`に外部データを渡すためのいくつかのオプションを提供しています。
| Method                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | イベントを使用してペイロードデータを含む`GameplayAbility`をアクティブ化します。イベントのペイロードは、ローカル予測された`GameplayAbilities`の場合、クライアントからサーバーに複製されます。既存の変数に適合しない任意のデータには、2つの`Optional Object`または[`TargetData`](#concepts-targeting-data)変数を使用します。この方法の欠点は、入力バインドでアビリティをアクティブ化できなくなることです。イベントで`GameplayAbility`をアクティブ化するには、`GameplayAbility`内でその`Triggers`を設定する必要があります。`GameplayTag`を割り当て、`GameplayEvent`のオプションを選択します。イベントを送信するには、関数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`を使用します。 |
| Use `WaitGameplayEvent` `AbilityTask`           | `WaitGameplayEvent` `AbilityTask`を使用して、アクティブ化後にペイロードデータを持つイベントをリッスンするよう`GameplayAbility`に指示します。イベントペイロードと送信プロセスは、イベントによる`GameplayAbilities`のアクティブ化と同じです。この方法の欠点は、イベントが`AbilityTask`によって複製されないため、`Local Only`および`Server Only`の`GameplayAbilities`にのみ使用するべきであることです。イベントペイロードを複製する独自の`AbilityTask`を作成することも可能です。                                                                                                                                                                                                                                                                                               |
| Use `TargetData`                                | カスタム`TargetData`構造体は、クライアントとサーバー間で任意のデータを渡すための良い方法です。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Store Data on the `OwnerActor` or `AvatarActor` | `OwnerActor`、`AvatarActor`、または参照を取得できる他のオブジェクトに保存された複製変数を使用します。この方法は最も柔軟で、入力バインドによってアクティブ化された`GameplayAbilities`でも機能します。ただし、使用時にデータが複製によって同期されることを保証するものではありません。事前にそれを確保する必要があります。つまり、複製変数を設定してすぐに`GameplayAbility`をアクティブ化する場合、パケットロスの可能性により、受信側での順序が保証されない可能性があります。                                                                                                                                                                                                                                   |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 アビリティのコストとクールダウン
`GameplayAbilities`には、オプションのコストとクールダウンの機能が備わっています。コストは、`ASC`が`GameplayAbility`をアクティブ化するために必要な`Attributes`の事前定義された量であり、`Instant`型の`GameplayEffect`（[`Cost GE`](#concepts-ge-cost)）で実装されます。クールダウンは、`GameplayAbility`を再アクティブ化するまでのタイマーであり、`Duration`型の`GameplayEffect`（[`Cooldown GE`](#concepts-ge-cooldown)）で実装されます。

`GameplayAbility`が`UGameplayAbility::Activate()`を呼び出す前に、`UGameplayAbility::CanActivateAbility()`を呼び出します。この関数は、所有する`ASC`がコストを負担できるかどうか（`UGameplayAbility::CheckCost()`）を確認し、`GameplayAbility`がクールダウン中でないことを保証します（`UGameplayAbility::CheckCooldown()`）。

`GameplayAbility`が`Activate()`を呼び出した後、任意のタイミングでコストとクールダウンをコミットすることができます。これは`UGameplayAbility::CommitAbility()`を使用して行われ、`UGameplayAbility::CommitCost()`と`UGameplayAbility::CommitCooldown()`を呼び出します。デザイナーは、コストとクールダウンを同時にコミットする必要がない場合、それぞれを個別に呼び出すこともできます。コストとクールダウンをコミットする際に、`CheckCost()`と`CheckCooldown()`がもう一度呼び出され、これがそれらに関連する最後の失敗のチャンスとなります。`GameplayAbility`がアクティブ化された後、所有する`ASC`の`Attributes`が変更され、コミット時にコストを満たせなくなる可能性があります。コストとクールダウンのコミットは、[予測](#concepts-p)が有効な場合、[予測キー](#concepts-p-key)が有効であればローカルで予測することができます。

実装の詳細については、[`CostGE`](#concepts-ge-cost)および[`CooldownGE`](#concepts-ge-cooldown)を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 アビリティのレベルアップ
アビリティをレベルアップするには、一般的に以下の2つの方法があります：
| Level Up Method                            | 説明                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ungrant and Regrant at the New Level       | サーバー上で`ASC`から`GameplayAbility`を削除し、新しいレベルで再付与します。この方法では、アビリティがアクティブな場合、そのアビリティは終了します。                                   |
| Increase the `GameplayAbilitySpec's` Level | サーバー上で`GameplayAbilitySpec`を見つけ、そのレベルを増加させ、所有するクライアントに複製されるようにマークします。この方法では、アビリティがアクティブな場合でも終了しません。 |

2つの方法の主な違いは、レベルアップ時にアクティブな`GameplayAbilities`をキャンセルするかどうかです。`GameplayAbilities`に応じて、両方の方法を使用する可能性が高いです。どちらの方法を使用するかを指定するために、`UGameplayAbility`サブクラスに`bool`型の変数を追加することをお勧めします。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 アビリティセット
`GameplayAbilitySets`は、入力バインディングやキャラクターの初期`GameplayAbilities`リストを保持するための便利な`UDataAsset`クラスであり、`GameplayAbilities`を付与するためのロジックを含んでいます。サブクラスは追加のロジックやプロパティを含むこともできます。Paragonでは、各ヒーローに対して`GameplayAbilitySet`があり、すべての付与された`GameplayAbilities`が含まれていました。

このクラスは、少なくともこれまで見た限りでは不要だと感じています。サンプルプロジェクトでは、`GDCharacterBase`およびそのサブクラス内で`GameplayAbilitySets`のすべての機能を処理しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 アビリティのバッチ処理
従来の`Gameplay Ability`のライフサイクルでは、クライアントからサーバーへの最低2回または3回のRPCが必要です。

1. `CallServerTryActivateAbility()`
2. `ServerSetReplicatedTargetData()`（オプション）
3. `ServerEndAbility()`

もし`GameplayAbility`がこれらのアクションを1フレーム内で1つの原子的なグループとして実行する場合、このワークフローを最適化して、2回または3回のRPCを1回のRPCにまとめることができます。`GAS`では、このRPCの最適化を`Ability Batching`と呼びます。`Ability Batching`を使用する一般的な例は、ヒットスキャン銃です。ヒットスキャン銃はアクティブ化され、ライントレースを実行し、[`TargetData`](#concepts-targeting-data)をサーバーに送信し、1フレーム内でアビリティを終了します。[GASShooter](https://github.com/tranek/GASShooter)サンプルプロジェクトは、この技術をヒットスキャン銃で示しています。

セミオート銃は最良のケースであり、`CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（弾丸のヒット結果）、および`ServerEndAbility()`を1回のRPCにまとめます。

フルオート/バースト銃では、最初の弾丸の`CallServerTryActivateAbility()`と`ServerSetReplicatedTargetData()`を1回のRPCにまとめます。それ以降の各弾丸は、それぞれ独自の`ServerSetReplicatedTargetData()` RPCを持ちます。最後に、銃の発射が停止したときに`ServerEndAbility()`が別のRPCとして送信されます。これは最悪のケースであり、最初の弾丸で2回ではなく1回のRPCを節約するだけです。このシナリオは、[`Gameplay Event`](#concepts-ga-data)を介してアビリティをアクティブ化し、弾丸の`TargetData`を`EventPayload`に含めてクライアントからサーバーに送信する方法でも実装できます。ただし、後者のアプローチの欠点は、`TargetData`をアビリティの外部で生成する必要があることです。一方、バッチ処理アプローチでは、アビリティ内で`TargetData`を生成します。

`Ability Batching`は、[`ASC`](#concepts-asc)でデフォルトでは無効になっています。`Ability Batching`を有効にするには、`ShouldDoServerAbilityRPCBatch()`をオーバーライドしてtrueを返すようにします。

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

`Ability Batching`が有効になった後、バッチ処理したいアビリティをアクティブ化する前に、`FScopedServerAbilityRPCBatcher`構造体を事前に作成する必要があります。この特別な構造体は、そのスコープ内で後続のアビリティをバッチ処理しようとします。`FScopedServerAbilityRPCBatcher`がスコープ外になると、それ以降にアクティブ化されるアビリティはバッチ処理を試みません。`FScopedServerAbilityRPCBatcher`は、バッチ処理可能な各関数に特別なコードを持ち、それがRPCの送信をインターセプトし、代わりにメッセージをバッチ構造体にパックすることで動作します。`FScopedServerAbilityRPCBatcher`がスコープ外になると、自動的にこのバッチ構造体を`UAbilitySystemComponent::EndServerAbilityRPCBatch()`でサーバーにRPCします。サーバーは`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`でバッチRPCを受信します。`BatchInfo`パラメータには、アビリティを終了するかどうか、アクティベーション時に入力が押されていたかどうかのフラグ、および`TargetData`が含まれている場合はそれが含まれます。バッチ処理が正しく動作していることを確認するために、この関数にブレークポイントを設定するのが良い方法です。あるいは、cvar `AbilitySystem.ServerRPCBatching.Log 1`を使用して、特別なアビリティバッチ処理のログを有効にすることもできます。

この仕組みはC++でのみ実行可能であり、アビリティを`FGameplayAbilitySpecHandle`でのみアクティブ化できます。

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

GASShooterは、セミオートマチックおよびフルオートマチックの銃に対して同じバッチ処理された`GameplayAbility`を再利用します。このアビリティは直接`EndAbility()`を呼び出すことはなく（これはプレイヤー入力を管理するローカル専用アビリティによって処理され、現在の発射モードに基づいてバッチ処理されたアビリティを呼び出します）、すべてのRPCは`FScopedServerAbilityRPCBatcher`のスコープ内で行われる必要があります。そのため、`EndAbilityImmediately`パラメータを提供し、制御/管理するローカル専用アビリティが、このアビリティが`EndAbility()`呼び出しをバッチ処理するべきか（セミオートマチック）、またはバッチ処理しないべきか（フルオートマチック）を指定できるようにしています。フルオートマチックの場合、`EndAbility()`呼び出しは後で独自のRPC内で行われます。

GASShooterは、アビリティのバッチ処理を可能にするBlueprintノードを公開しており、前述のローカル専用アビリティがこのノードを使用してバッチ処理されたアビリティをトリガーします。

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 ネットセキュリティポリシー
`GameplayAbility`の`NetSecurityPolicy`は、アビリティがネットワーク上でどこで実行されるべきかを決定します。これにより、クライアントが制限されたアビリティを実行しようとすることから保護されます。

| `NetSecurityPolicy`     | 説明                                                                                                                                               |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | セキュリティ要件はありません。クライアントまたはサーバーが自由にこのアビリティの実行および終了をトリガーできます。                                           |
| `ServerOnlyExecution`   | クライアントがこのアビリティの実行を要求しても、サーバーによって無視されます。ただし、クライアントはサーバーにこのアビリティのキャンセルまたは終了を要求できます。 |
| `ServerOnlyTermination` | クライアントがこのアビリティのキャンセルまたは終了を要求しても、サーバーによって無視されます。ただし、クライアントはこのアビリティの実行を要求できます。      |
| `ServerOnly`            | サーバーがこのアビリティの実行および終了の両方を制御します。クライアントが行ういかなる要求も無視されます。                                      |

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 アビリティタスク

<a name="concepts-at-definition"></a>
### 4.7.1 アビリティタスクの定義
`GameplayAbilities`は1フレーム内でのみ実行されます。これでは柔軟性があまりありません。時間をかけて行われるアクションや、後で発生するデリゲートに応答する必要がある場合には、`AbilityTasks`と呼ばれる遅延アクションを使用します。

GASには多くの`AbilityTasks`が標準で含まれています：
* `RootMotionSource`を使用してキャラクターを移動させるタスク
* アニメーションモンタージュを再生するタスク
* `Attribute`の変更に応答するタスク
* `GameplayEffect`の変更に応答するタスク
* プレイヤー入力に応答するタスク
* その他多数

`UAbilityTask`のコンストラクタは、ゲーム全体で同時に実行可能な`AbilityTasks`の最大数を1000に制限しています。RTSゲームのように同時に数百のキャラクターが存在するゲームの`GameplayAbilities`を設計する際には、これを考慮してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 カスタムアビリティタスク
しばしば独自のカスタム`AbilityTasks`を作成する必要があります（C++で）。サンプルプロジェクトには2つのカスタム`AbilityTasks`が含まれています：
1. `PlayMontageAndWaitForEvent`は、デフォルトの`PlayMontageAndWait`と`WaitGameplayEvent`の`AbilityTasks`を組み合わせたものです。これにより、アニメーションモンタージュが`AnimNotifies`を使用して、それを開始した`GameplayAbility`にゲームプレイイベントを送信できます。これを使用して、アニメーションモンタージュの特定のタイミングでアクションをトリガーします。
2. `WaitReceiveDamage`は、`OwnerActor`がダメージを受けるのをリッスンします。パッシブなアーマースタックの`GameplayAbility`は、ヒーローがダメージを受けた際にアーマーのスタックを1つ削除します。

`AbilityTasks`は以下で構成されます：
* 新しい`AbilityTask`インスタンスを作成する静的関数
* `AbilityTask`がその目的を達成したときにブロードキャストされるデリゲート
* 主なジョブを開始し、外部デリゲートにバインドするなどの`Activate()`関数
* クリーンアップのための`OnDestroy()`関数（バインドした外部デリゲートを含む）
* バインドした外部デリゲート用のコールバック関数
* メンバ変数および内部ヘルパー関数

**注意:** `AbilityTasks`は1種類の出力デリゲートしか宣言できません。すべての出力デリゲートはこの型でなければならず、パラメータを使用しない場合でも同じ型を使用する必要があります。未使用のデリゲートパラメータにはデフォルト値を渡してください。

`AbilityTasks`は、所有する`GameplayAbility`を実行しているクライアントまたはサーバーでのみ実行されます。ただし、`AbilityTasks`は`AbilityTask`コンストラクタ内で`bSimulatedTask = true;`を設定し、`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`をオーバーライドし、任意のメンバ変数を複製可能に設定することで、シミュレートされたクライアント上で実行するように設定できます。これは、すべての移動変更を複製するのではなく、移動全体の`AbilityTask`をシミュレートしたい場合など、まれな状況でのみ有用です。すべての`RootMotionSource`の`AbilityTasks`がこれを行います。例として`AbilityTask_MoveToLocation.h/.cpp`を参照してください。

`AbilityTasks`は、`AbilityTask`コンストラクタ内で`bTickingTask = true;`を設定し、`virtual void TickTask(float DeltaTime);`をオーバーライドすることで`Tick`することができます。これは、値をフレーム間でスムーズに補間する必要がある場合に便利です。例として`AbilityTask_MoveToLocation.h/.cpp`を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 アビリティタスクの使用
C++で`AbilityTask`を作成してアクティブ化するには（`GDGA_FireGun.cpp`から）：
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

Blueprintでは、作成した`AbilityTask`用のBlueprintノードを使用します。この場合、`ReadyForActivation()`を呼び出す必要はありません。これは`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`によって自動的に呼び出されます。`K2Node_LatentGameplayTaskCall`は、`AbilityTask`クラス内に`BeginSpawningActor()`および`FinishSpawningActor()`が存在する場合、それらも自動的に呼び出します（例: `AbilityTask_WaitTargetData`）。繰り返しますが、`K2Node_LatentGameplayTaskCall`はBlueprint専用の自動処理を行います。C++では、`ReadyForActivation()`、`BeginSpawningActor()`、および`FinishSpawningActor()`を手動で呼び出す必要があります。

<a name="concepts-at-rms"></a>
### 4.7.4 ルートモーションソースアビリティタスク
GASには、`CharacterMovementComponent`に接続された`Root Motion Sources`を使用して、ノックバック、複雑なジャンプ、引き寄せ、ダッシュなどの動きを`Characters`に与えるための`AbilityTasks`が含まれています。

**注意:** `RootMotionSource`の`AbilityTasks`の予測は、エンジンバージョン4.19および4.25以降で動作します。エンジンバージョン4.20から4.24では予測にバグがありますが、これらの`AbilityTasks`はマルチプレイヤー環境で軽微なネット修正を伴いながらも機能し、シングルプレイヤー環境では完全に動作します。4.25の[予測修正](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)を4.20から4.24のカスタムエンジンに取り込むことも可能です。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 ゲームプレイクエ

<a name="concepts-gc-definition"></a>
#### 4.8.1 ゲームプレイクエの定義
`GameplayCues`（`GC`）は、サウンドエフェクト、パーティクルエフェクト、カメラシェイクなどのゲームプレイに直接関係しない処理を実行します。`GameplayCues`は通常、複製され（明示的にローカルで`Executed`、`Added`、または`Removed`されない限り）、予測されます。

`GameplayCues`をトリガーするには、**必須の親名`GameplayCue.`**を持つ対応する`GameplayTag`とイベントタイプ（`Execute`、`Add`、または`Remove`）を`ASC`を介して`GameplayCueManager`に送信します。`GameplayCueNotify`オブジェクトや`IGameplayCueInterface`を実装する他の`Actors`は、`GameplayCue`の`GameplayTag`（`GameplayCueTag`）に基づいてこれらのイベントを購読できます。

**注意:** 繰り返しますが、`GameplayCue`の`GameplayTags`は`GameplayCue`という親`GameplayTag`で始まる必要があります。例えば、有効な`GameplayCue`の`GameplayTag`は`GameplayCue.A.B.C`のようになります。

`GameplayCueNotifies`には`Static`と`Actor`の2つのクラスがあります。それぞれ異なるイベントに応答し、異なるタイプの`GameplayEffects`がそれらをトリガーします。対応するイベントをオーバーライドしてロジックを実装します。

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | 静的な`GameplayCueNotifies`は`ClassDefaultObject`（インスタンスなし）で動作し、ヒットインパクトのような一度きりのエフェクトに最適です。                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | アクター`GameplayCueNotifies`は`Added`時に新しいインスタンスを生成します。これらはインスタンス化されているため、`Removed`されるまで時間をかけてアクションを実行できます。これらは、ループするサウンドやパーティクルエフェクトに適しており、基になる`Duration`または`Infinite`の`GameplayEffect`が削除されたとき、または手動で削除を呼び出すことで削除されます。また、同じエフェクトの複数の適用がサウンドやパーティクルを一度だけ開始するように、同時に許可される数を管理するオプションも備えています。 |

`GameplayCueNotifies` は技術的にはどのイベントにも応答できますが、通常は以下のように使用します。

**注意:** `GameplayCueNotify_Actor` を使用する場合、`Auto Destroy on Remove` をチェックしてください。そうしないと、後続の `Add` 呼び出しがその `GameplayCueTag` に対して機能しなくなります。

`ASC` の [Replication Mode](#concepts-asc-rm) を `Full` 以外に設定している場合、サーバープレイヤー（リッスンサーバー）では `Add` および `Remove` の `GC` イベントが2回発生します（`GE` の適用時とクライアントへの "Minimal" `NetMultiCast` の際）。ただし、`WhileActive` イベントは1回しか発生しません。すべてのイベントはクライアントでは1回のみ発生します。

サンプルプロジェクトには、スタンおよびスプリント効果用の `GameplayCueNotify_Actor` が含まれています。また、FireGun のプロジェクトタイルインパクト用の `GameplayCueNotify_Static` も含まれています。これらの `GC` は、`GE` を介して複製する代わりに [ローカルでトリガー](#concepts-gc-local) することでさらに最適化できます。サンプルプロジェクトでは、初心者向けの使用方法を示すためにこの方法を採用しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Gameplay Cue のトリガー

`GameplayEffect` 内で正常に適用された場合（タグや免疫によってブロックされない場合）、トリガーすべきすべての `GameplayCues` の `GameplayTags` を入力します。

![GameplayCue が GameplayEffect からトリガーされる](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` は、`GameplayCues` を `Execute`、`Add`、または `Remove` するための Blueprint ノードを提供します。

![GameplayCue が GameplayAbility からトリガーされる](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

C++ では、`ASC` 上で直接関数を呼び出すことができます（または、`ASC` サブクラスで Blueprint に公開することもできます）:

```c++
/** GameplayCues は単独でも発生可能です。これらはヒット結果などを渡すためのオプションのエフェクトコンテキストを取ります */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 永続的な GameplayCue を追加 */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 永続的な GameplayCue を削除 */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);

/** 単独で追加された GameplayCue をすべて削除します。つまり、GameplayEffect の一部としてではないものを削除します。 */
void RemoveAllGameplayCues();
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 ローカルゲームプレイキュー
`GameplayAbilities`や`ASC`から`GameplayCues`を発火させるために公開されている関数は、デフォルトで複製されます。各`GameplayCue`イベントはマルチキャストRPCです。これにより、多くのRPCが発生する可能性があります。GASは、1つのネット更新ごとに同じ`GameplayCue` RPCを最大2つまでに制限します。この問題を回避するために、可能な場合はローカル`GameplayCues`を使用します。ローカル`GameplayCues`は、個々のクライアント上でのみ`Execute`、`Add`、または`Remove`されます。

ローカル`GameplayCues`を使用できるシナリオ:
* プロジェクタイルの衝突
* 近接攻撃の衝突
* アニメーションモンタージュから発火される`GameplayCues`

ローカル`GameplayCue`関数を`ASC`サブクラスに追加する必要があります:

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
`GameplayCue`がローカルで`Added`された場合、それはローカルで`Removed`されるべきです。もしそれが複製を介して`Added`された場合、それは複製を介して`Removed`されるべきです。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue パラメータ
`GameplayCues`は、追加情報を含む`FGameplayCueParameters`構造体をパラメータとして受け取ります。`GameplayAbility`または`ASC`の関数から手動で`GameplayCue`をトリガーする場合、この`GameplayCue`に渡される`GameplayCueParameters`構造体を手動で埋める必要があります。`GameplayEffect`によって`GameplayCue`がトリガーされる場合、以下の変数が自動的に`GameplayCueParameters`構造体に埋め込まれます：

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude（`GameplayEffect`が上記の`GameplayCue`タグコンテナのドロップダウンでマグニチュード用の`Attribute`を持ち、その`Attribute`に影響を与える対応する`Modifier`がある場合）

`GameplayCueParameters`構造体内の`SourceObject`変数は、`GameplayCue`を手動でトリガーする際に任意のデータを渡すための適切な場所となる可能性があります。

**注意:** パラメータ構造体内の`Instigator`のような一部の変数は、すでに`EffectContext`内に存在する可能性があります。`EffectContext`は、`GameplayCue`をワールド内のどこにスポーンさせるかの位置情報を含む`FHitResult`を持つこともできます。特に`GameplayEffect`によってトリガーされる`GameplayCues`に対して、より多くのデータを渡すために`EffectContext`をサブクラス化するのは良い方法です。

`GameplayCueParameters`構造体を埋めるための詳細については、[`UAbilitySystemGlobals`](#concepts-asg)内の3つの関数を参照してください。これらは仮想関数であるため、より多くの情報を自動的に埋めるようにオーバーライドすることができます。

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue マネージャー
デフォルトでは、`GameplayCueManager` はゲームディレクトリ全体をスキャンし、`GameplayCueNotifies` を見つけてプレイ時にメモリにロードします。このスキャンするパスは、`DefaultGame.ini` で設定を変更できます。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

`GameplayCueManager` にすべての `GameplayCueNotifies` をスキャンして見つけてもらいたいですが、プレイ時にすべてを非同期でロードするのは避けたいです。これを行うと、すべての `GameplayCueNotify` とそれらが参照するサウンドやパーティクルがメモリにロードされ、レベルで使用されない場合でもメモリを消費します。大規模なゲーム（例: Paragon）では、これが数百メガバイトの不要なアセットをメモリにロードし、スタートアップ時に引っかかりやゲームのフリーズを引き起こす可能性があります。

すべての `GameplayCue` をスタートアップ時に非同期でロードする代わりに、ゲーム内でトリガーされたときにのみ非同期でロードする方法があります。これにより、不要なメモリ使用量と、すべての `GameplayCue` を非同期でロードする際のゲームのハードフリーズを軽減できます。その代わりに、特定の `GameplayCue` がプレイ中に初めてトリガーされたときにエフェクトが遅れる可能性があります。この遅延は SSD では存在しません。HDD でのテストは行っていません。このオプションを UE エディタで使用する場合、エディタがパーティクルシステムをコンパイルする必要がある場合、最初のロード時にわずかな引っかかりやフリーズが発生する可能性があります。ただし、ビルドではパーティクルシステムがすでにコンパイルされているため、この問題は発生しません。

まず、`UGameplayCueManager` をサブクラス化し、`AbilitySystemGlobals` クラスに `DefaultGame.ini` でそのサブクラスを使用するよう指示します。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

次に、`UGameplayCueManager` サブクラスで `ShouldAsyncLoadRuntimeObjectLibraries()` をオーバーライドします。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```
**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 Gameplay Cuesを発火させない方法
時には`GameplayCues`を発火させたくない場合があります。例えば、攻撃をブロックした場合、ダメージ`GameplayEffect`に付随するヒットインパクトを再生したくない、または代わりにカスタムのものを再生したい場合があります。このような場合、[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)内で`OutExecutionOutput.MarkGameplayCuesHandledManually()`を呼び出し、その後、手動で`GameplayCue`イベントを`Target`または`Source`の`ASC`に送信することができます。

特定の`ASC`で`GameplayCues`を一切発火させたくない場合、`AbilitySystemComponent->bSuppressGameplayCues = true;`を設定することができます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cueのバッチ処理
各`GameplayCue`が発火されるたびに、信頼性のないNetMulticast RPCが発生します。同時に複数の`GC`を発火させる場合、これらを1つのRPCにまとめたり、送信するデータ量を減らすためのいくつかの最適化方法があります。

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手動RPC
例えば、ショットガンが8つのペレットを発射する場合、それぞれのトレースとインパクト`GameplayCues`が発生します。[GASShooter](https://github.com/tranek/GASShooter)では、[`EffectContext`](#concepts-ge-ec)に[`TargetData`](#concepts-targeting-data)としてトレース情報をすべて格納することで、これらを1つのRPCにまとめるという簡易的なアプローチを取っています。これにより、RPCの数は8から1に減少しますが、その1つのRPCで大量のデータ（約500バイト）が送信されます。より最適化されたアプローチとして、カスタム構造体を使用してヒット位置を効率的にエンコードしたり、ランダムシード番号を送信して受信側でインパクト位置を再現/近似する方法があります。クライアントはこのカスタム構造体をアンパックし、[ローカルで実行される`GameplayCues`](#concepts-gc-local)に変換します。

この仕組み:
1. `FScopedGameplayCueSendContext`を宣言します。これにより、`FScopedGameplayCueSendContext`がスコープ外になるまで`UGameplayCueManager::FlushPendingCues()`が抑制され、すべての`GameplayCues`がキューに追加されます。
2. `UGameplayCueManager::FlushPendingCues()`をオーバーライドし、カスタム`GameplayTag`に基づいてバッチ処理可能な`GameplayCues`をカスタム構造体にまとめ、クライアントにRPCで送信します。
3. クライアントはカスタム構造体を受信し、それをアンパックしてローカルで実行される`GameplayCues`に変換します。

この方法は、`GameplayCueParameters`が提供するものに適合しない特定のパラメータ（ダメージ数値、クリティカルインジケーター、シールド破壊インジケーター、致命的ヒットインジケーターなど）が必要な場合にも使用できます。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 1つのGEに複数のGC
1つの`GameplayEffect`に含まれるすべての`GameplayCues`は、すでに1つのRPCで送信されます。デフォルトでは、`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`は、`ASC`の`Replication Mode`に関係なく、信頼性のないNetMulticastで`GameplayEffectSpec`全体（ただし`FGameplayEffectSpecForRPC`に変換されたもの）を送信します。これにより、`GameplayEffectSpec`に含まれる内容によっては大量の帯域幅が必要になる可能性があります。これを最適化するために、cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`を設定することができます。これにより、`GameplayEffectSpecs`が`FGameplayCueParameter`構造体に変換され、それがRPCで送信されます。これにより帯域幅を節約できますが、`GESpec`が`GameplayCueParameters`に変換される方法や`GC`が必要とする情報によっては、情報量が減少する可能性があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 Gameplay Cue イベント
`GameplayCues` は特定の `EGameplayCueEvents` に応答します:

| `EGameplayCueEvent` | 説明                                                                                                                                                                                                                                                                                                                                 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | `GameplayCue` がアクティブ化（追加）されたときに呼び出されます。                                                                                                                                                                                                                                                                       |
| `WhileActive`       | `GameplayCue` がアクティブな間に呼び出されます。実際に適用されたばかりでなくても（進行中の参加など）、呼び出されます。これは `Tick` ではありません！ `GameplayCueNotify_Actor` が追加されたり関連性を持つようになったときに、`OnActive` と同様に一度だけ呼び出されます。`Tick()` が必要な場合は、`GameplayCueNotify_Actor` の `Tick()` を使用してください。結局のところ、それは `AActor` です。 |
| `Removed`           | `GameplayCue` が削除されたときに呼び出されます。このイベントに応答する Blueprint の `GameplayCue` 関数は `OnRemove` です。                                                                                                                                                                                                             |
| `Executed`          | `GameplayCue` が実行されたときに呼び出されます：即時効果や定期的な `Tick()` の場合。このイベントに応答する Blueprint の `GameplayCue` 関数は `OnExecute` です。                                                                                                                                                                     |

`OnActive` は、`GameplayCue` の開始時に発生するが、後から参加したプレイヤーが見逃しても問題ないものに使用します。`WhileActive` は、`GameplayCue` の進行中の効果で、後から参加したプレイヤーにも見せたいものに使用します。例えば、MOBAのタワー構造物が爆発する `GameplayCue` がある場合、初期の爆発パーティクルシステムと爆発音を `OnActive` に配置し、残留する進行中の火のパーティクルや音を `WhileActive` に配置します。このシナリオでは、後から参加したプレイヤーが `OnActive` の初期爆発を再生するのは意味がありませんが、爆発後の地面に残る持続的でループする火のエフェクトは見せたいでしょう。`OnRemove` は、`OnActive` と `WhileActive` で追加されたものをすべてクリーンアップするべきです。`WhileActive` は、アクターが `GameplayCueNotify_Actor` の関連性範囲に入るたびに呼び出されます。`OnRemove` は、アクターが `GameplayCueNotify_Actor` の関連性範囲を離れるたびに呼び出されます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 Gameplay Cue の信頼性

`GameplayCues` は一般的に信頼性が低いと見なされるべきであり、ゲームプレイに直接影響を与えるものには適していません。

**実行された `GameplayCues`:** これらの `GameplayCues` は信頼性のないマルチキャストを介して適用され、常に信頼性が低いです。

**`GameplayEffects` から適用された `GameplayCues`:**
* 自律プロキシは `OnActive`、`WhileActive`、および `OnRemove` を確実に受信します。  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` は `UAbilitySystemComponent::HandleDeferredGameplayCues()` を呼び出して `OnActive` と `WhileActive` を呼び出します。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` は `OnRemoved` を呼び出します。
* シミュレートされたプロキシは `WhileActive` と `OnRemove` を確実に受信します。  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出します。`OnActive` イベントは信頼性のないマルチキャストによって呼び出されます。

**`GameplayEffect` を使用せずに適用された `GameplayCues`:**
* 自律プロキシは `OnRemove` を確実に受信します。  
`OnActive` と `WhileActive` イベントは信頼性のないマルチキャストによって呼び出されます。
* シミュレートされたプロキシは `WhileActive` と `OnRemove` を確実に受信します。  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出します。`OnActive` イベントは信頼性のないマルチキャストによって呼び出されます。

もし `GameplayCue` 内で何かを「信頼性のある」ものにしたい場合は、それを `GameplayEffect` から適用し、`WhileActive` を使用してエフェクトを追加し、`OnRemove` を使用してエフェクトを削除してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 アビリティシステムグローバル
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) クラスは、GASに関するグローバル情報を保持します。ほとんどの変数は `DefaultGame.ini` から設定できます。通常、このクラスと直接やり取りする必要はありませんが、その存在を知っておくべきです。例えば、[`GameplayCueManager`](#concepts-gc-manager) や [`GameplayEffectContext`](#concepts-ge-context) をサブクラス化する必要がある場合は、`AbilitySystemGlobals` を通じて行う必要があります。

`AbilitySystemGlobals` をサブクラス化するには、`DefaultGame.ini` にクラス名を設定します：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
UE 4.24 から 5.2 の間では、[`TargetData`](#concepts-targeting-data) を使用するために `UAbilitySystemGlobals::Get().InitGlobalData()` を呼び出す必要があります。これを呼び出さないと、`ScriptStructCache` に関連するエラーが発生し、クライアントがサーバーから切断されます。この関数はプロジェクト内で一度だけ呼び出せば十分です。Fortnite では `UAssetManager::StartInitialLoading()` から呼び出され、Paragon では `UEngine::Init()` から呼び出されていました。サンプルプロジェクトでは、`UAssetManager::StartInitialLoading()` に配置するのが適切だと考えています。これは、`TargetData` に関する問題を回避するためのテンプレートコードとしてプロジェクトにコピーすることをお勧めします。5.3 以降では自動的に呼び出されます。

`AbilitySystemGlobals` の `GlobalAttributeSetDefaultsTableNames` を使用中にクラッシュが発生した場合、Fortnite のように `AssetManager` または `GameInstance` 内で `UAbilitySystemGlobals::Get().InitGlobalData()` を後で呼び出す必要があるかもしれません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 予測
GAS はクライアントサイドの予測をサポートしていますが、すべてを予測するわけではありません。GAS におけるクライアントサイドの予測とは、クライアントがサーバーの許可を待たずに `GameplayAbility` をアクティブ化し、`GameplayEffects` を適用できることを意味します。クライアントはサーバーが許可を与えると予測し、`GameplayEffects` を適用するターゲットを予測します。その後、サーバーはクライアントがアクティブ化してからネットワーク遅延時間後に `GameplayAbility` を実行し、クライアントが予測に正しかったかどうかを伝えます。クライアントが予測に失敗した場合、変更を「ロールバック」してサーバーに一致させます。

GAS に関連する予測の決定的な情報源は、プラグインのソースコード内の `GameplayPrediction.h` です。

Epic の考え方は、「可能な限り最小限の予測を行う」ことです。例えば、Paragon や Fortnite はダメージを予測しません。おそらく、ダメージには [`ExecutionCalculations`](#concepts-ge-ec) を使用しており、これらは予測できません。ただし、ダメージのような特定のものを予測しようとすることを妨げるものではありません。もしそれがうまく機能するなら、それは素晴らしいことです。

> ... 私たちは「すべてを予測する：シームレスかつ自動的に」という解決策に完全に依存しているわけではありません。プレイヤーの予測は可能な限り最小限に抑えるべきだと考えています（つまり、可能な限り最小限のものを予測する）。

*Epic の Dave Ratti による新しい [Network Prediction Plugin](#concepts-p-npp) に関するコメント*

**予測されるもの:**
> * アビリティのアクティベーション
> * トリガーされたイベント
> * `GameplayEffect` の適用:
>    * Attributeの変更（例外: 実行は現在予測されず、Attribute修飾子のみ）
>    * `GameplayTag` の変更
> * `GameplayCue` イベント（予測可能な `GameplayEffect` 内および単独で）
> * モンタージュ
> * 移動（UE の `UCharacterMovement` に組み込まれている）

**予測されないもの:**
> * `GameplayEffect` の削除
> * `GameplayEffect` の定期的な効果（ドットのティック）


*`GameplayPrediction.h`から*

`GameplayEffect`の適用を予測することはできますが、`GameplayEffect`の削除を予測することはできません。この制限を回避する1つの方法は、`GameplayEffect`を削除したい場合に逆の効果を予測することです。例えば、移動速度を40%低下させる効果を予測した場合、それを削除するために移動速度を40%増加させる効果を予測的に適用します。そして、両方の`GameplayEffect`を同時に削除します。ただし、これはすべてのシナリオに適しているわけではなく、`GameplayEffect`削除の予測に対するサポートは依然として必要です。EpicのDave Rattiは、[GASの将来のバージョン](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)でこれを追加する意向を示しています。

`GameplayEffect`の削除を予測できないため、`GameplayAbility`のクールダウンを完全に予測することはできません。また、これに対する逆の`GameplayEffect`の回避策もありません。サーバーが複製する`Cooldown GE`はクライアント上に存在し、これを回避しようとする試み（例えば、`Minimal`複製モードを使用するなど）はサーバーによって拒否されます。これにより、レイテンシが高いクライアントは、サーバーにクールダウンを開始するよう通知し、サーバーの`Cooldown GE`の削除を受信するまでに時間がかかります。つまり、レイテンシが高いプレイヤーは、レイテンシが低いプレイヤーに比べて発射速度が低くなり、不利になります。Fortniteでは、`Cooldown GEs`の代わりにカスタムの記録方法を使用することでこの問題を回避しています。

ダメージを予測することについては、個人的にはお勧めしません。これはGASを始めたばかりの人が最初に試みることの1つですが、特に死亡を予測しようとすることはお勧めしません。ダメージを予測することは可能ですが、それは難しいです。ダメージの適用を誤って予測した場合、プレイヤーは敵の体力が元に戻るのを目にすることになります。これは特に死亡を予測しようとした場合に不自然であり、敵がラグドール化した後に再び立ち上がり、攻撃を続けるような状況は非常に困惑させられます。

**注意:** `Instant`型の`GameplayEffects`（例えば`Cost GEs`）が`Attributes`を変更する場合、自分自身に対してはシームレスに予測できますが、他のキャラクターに対して`Instant`型の`Attribute`変更を予測すると、短時間の異常や「ブリップ」が表示されます。予測された`Instant`型の`GameplayEffects`は、誤った予測があった場合にロールバックできるように、実際には`Infinite`型の`GameplayEffects`として扱われます。サーバーの`GameplayEffect`が適用されると、一時的に同じ`GameplayEffect`が2つ存在し、修飾子が一時的に2回適用されたり、全く適用されなかったりする可能性があります。最終的には修正されますが、プレイヤーにとってはブリップが目立つ場合があります。

GASの予測実装が解決しようとしている問題:
> 1. 「これを実行できますか？」予測の基本プロトコル。
> 2. 「元に戻す」予測が失敗した場合の副作用を元に戻す方法。
> 3. 「再実行」ローカルで予測した副作用がサーバーから複製される際に再実行を回避する方法。
> 4. 「完全性」すべての副作用を確実に予測する方法。
> 5. 「依存関係」予測に依存するイベントチェーンを管理する方法。
> 6. 「上書き」サーバーによって複製/所有される状態を予測的に上書きする方法。

*`GameplayPrediction.h`から*

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 予測キー
GASの予測は、クライアントが`GameplayAbility`をアクティブ化した際に生成される整数識別子である「予測キー」の概念に基づいています。

* クライアントは`GameplayAbility`をアクティブ化するときに予測キーを生成します。これが「アクティベーション予測キー」です。
* クライアントはこの予測キーを`CallServerTryActivateAbility()`を使用してサーバーに送信します。
* クライアントは、この予測キーを有効な間に適用するすべての`GameplayEffects`に追加します。
* クライアントの予測キーがスコープ外になります。同じ`GameplayAbility`内でさらに予測された効果を適用するには、新しい[スコープ予測ウィンドウ](#concepts-p-windows)が必要です。

* サーバーはクライアントから予測キーを受信します。
* サーバーは、この予測キーを適用するすべての`GameplayEffects`に追加します。
* サーバーは予測キーをクライアントに複製します。

* クライアントは、サーバーから複製された予測キーを持つ`GameplayEffects`を受信します。複製された`GameplayEffects`がクライアントが同じ予測キーで適用した`GameplayEffects`と一致する場合、それらは正しく予測されました。ターゲットには一時的に2つの`GameplayEffect`が存在し、クライアントは予測されたものを削除します。
* クライアントはサーバーから予測キーを受信します。これが「複製予測キー」です。この予測キーは古いものとしてマークされます。
* クライアントは、古い複製予測キーで作成したすべての`GameplayEffects`を削除します。サーバーによって複製された`GameplayEffects`は持続します。クライアントが追加し、サーバーから一致する複製バージョンを受信しなかった`GameplayEffects`は誤った予測でした。

予測キーは、`GameplayAbilities`内の命令の原子的なグループ化「ウィンドウ」中に有効であることが保証されています。これは1フレーム中のみ有効であると考えることができます。遅延アクション`AbilityTasks`からのコールバックは、ビルトインの同期ポイントを持つ`AbilityTask`が新しい[スコープ予測ウィンドウ](#concepts-p-windows)を生成しない限り、有効な予測キーを持ちません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 アビリティ内で新しい予測ウィンドウを作成する
`AbilityTasks`のコールバックでさらにアクションを予測するには、新しいスコープ予測ウィンドウを作成し、新しいスコープ予測キーを生成する必要があります。これは、クライアントとサーバー間の同期ポイントと呼ばれることもあります。一部の`AbilityTasks`（すべての入力関連タスクなど）は、コールバック内の原子的なコードが使用できる有効なスコープ予測キーを持つ新しいスコープ予測ウィンドウを作成するビルトイン機能を備えています。他のタスク（例えば`WaitDelay`タスク）は、コールバック用のスコープ予測ウィンドウを作成するビルトインコードを持っていません。`WaitDelay`のようなスコープ予測ウィンドウを作成しない`AbilityTask`の後にアクションを予測する必要がある場合、`OnlyServerWait`オプションを使用して`WaitNetSync` `AbilityTask`を手動で使用する必要があります。

クライアントが`OnlyServerWait`を使用して`WaitNetSync`に到達すると、`GameplayAbility`のアクティベーション予測キーに基づいて新しいスコープ予測キーを生成し、それをサーバーにRPCで送信し、適用する新しい`GameplayEffects`に追加します。サーバーが`OnlyServerWait`を使用して`WaitNetSync`に到達すると、クライアントから新しいスコープ予測キーを受信するまで待機します。このスコープ予測キーは、アクティベーション予測キーと同じダンスを行い、`GameplayEffects`に適用され、クライアントに複製されて古いものとしてマークされます。スコープ予測キーはスコープ外になるまで有効であり、つまりスコープ予測ウィンドウが閉じたことを意味します。したがって、再び、遅延のない操作のみが新しいスコープ予測キーを使用できます。

必要に応じて、スコープ予測ウィンドウをいくつでも作成できます。

独自のカスタム`AbilityTasks`に同期ポイント機能を追加したい場合、入力関連のものがどのように`WaitNetSync` `AbilityTask`コードを注入しているかを確認してください。

**注意:** `WaitNetSync`を使用する場合、サーバーの`GameplayAbility`がクライアントからの応答を待つまで実行を続行しません。これにより、ゲームをハッキングして新しいスコープ予測キーの送信を意図的に遅延させる悪意のあるユーザーによって悪用される可能性があります。Epicは`WaitNetSync`を慎重に使用していますが、これが懸念される場合は、クライアントなしで自動的に続行する遅延を持つ新しいバージョンの`AbilityTask`を構築することを推奨しています。

サンプルプロジェクトでは、スタミナコストを予測できるようにするために、スプリント`GameplayAbility`でスタミナコストを適用するたびに新しいスコープ予測ウィンドウを作成するために`WaitNetSync`を使用しています。理想的には、コストとクールダウンを適用する際に有効な予測キーを持ちたいと考えています。

予測された`GameplayEffect`が所有クライアントで2回再生されている場合、予測キーが古くなっており、「再実行」問題が発生しています。これを解決するには、`GameplayEffect`を適用する直前に`OnlyServerWait`を使用して`WaitNetSync` `AbilityTask`を配置し、新しいスコープ予測キーを作成することで通常解決できます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 アクターの予測的スポーン
クライアント上でアクターを予測的にスポーンすることは高度なトピックです。GASはこれを標準機能として提供していません（`SpawnActor` `AbilityTask`はアクターをサーバー上でのみスポーンします）。重要な概念は、複製されたアクターをクライアントとサーバーの両方でスポーンすることです。

アクターが単なる装飾的なもので、ゲームプレイに影響を与えない場合、簡単な解決策として、アクターの`IsNetRelevantFor()`関数をオーバーライドして、サーバーが所有クライアントに複製しないように制限することができます。所有クライアントはローカルでスポーンされたバージョンを持ち、サーバーと他のクライアントはサーバーの複製バージョンを持つことになります。
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

スポーンされたアクターがゲームプレイに影響を与える場合（例えば、ダメージを予測する必要があるプロジェクタイル）、このドキュメントの範囲外となる高度なロジックが必要です。Epic GamesのGitHubにあるUnrealTournamentのプロジェクトでは、所有クライアント上でのみスポーンされるダミープロジェクタイルがサーバーの複製プロジェクタイルと同期する方法を確認できます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 GASにおける予測の将来
`GameplayPrediction.h`には、将来的に`GameplayEffect`の削除や定期的な`GameplayEffect`の予測機能が追加される可能性があると記載されています。

EpicのDave Rattiは、クールダウンの予測における「レイテンシ調整」問題を修正し、高レイテンシのプレイヤーが低レイテンシのプレイヤーに比べて不利になる状況を改善することに興味を示しています。

Epicの新しい[`Network Prediction`プラグイン](#concepts-p-npp)は、以前の`CharacterMovementComponent`のようにGASと完全に相互運用可能になることが期待されています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 ネットワーク予測プラグイン
Epicは最近、`CharacterMovementComponent`を新しい`Network Prediction`プラグインに置き換える取り組みを開始しました。このプラグインはまだ非常に初期の段階ですが、Unreal EngineのGitHubで非常に早期アクセスが可能です。どの将来のエンジンバージョンで実験的なベータ版としてデビューするかはまだ不明です。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 ターゲティング

<a name="concepts-targeting-data"></a>
#### 4.11.1 ターゲットデータ
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) は、ネットワークを介してターゲティングデータを渡すための汎用構造体です。`TargetData` は通常、`AActor`/`UObject` の参照、`FHitResults`、およびその他の一般的な位置/方向/起点情報を保持します。ただし、サブクラス化することで、必要に応じて任意のデータを格納することが可能で、[`GameplayAbilities`内でクライアントとサーバー間でデータを渡す](#concepts-ga-data)ための簡単な手段となります。基本構造体である `FGameplayAbilityTargetData` は直接使用することを意図しておらず、サブクラス化して使用します。`GAS` には、`GameplayAbilityTargetTypes.h` にいくつかのサブクラス化された `FGameplayAbilityTargetData` 構造体が標準で含まれています。

`TargetData` は通常、[`Target Actors`](#concepts-targeting-actors) によって生成されるか、**手動で作成**され、[`AbilityTasks`](#concepts-at) や [`GameplayEffects`](#concepts-ge) を通じて [`EffectContext`](#concepts-ge-context) によって消費されます。`EffectContext` に含まれる結果として、[`Executions`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc)、および [`AttributeSet`](#concepts-as) のバックエンドにある関数が `TargetData` にアクセスできます。

通常、`FGameplayAbilityTargetData` を直接渡すことはせず、代わりに内部に `FGameplayAbilityTargetData` へのポインタを持つ TArray を含む [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html) を使用します。この中間構造体は、`TargetData` のポリモーフィズムをサポートします。

`FGameplayAbilityTargetData` を継承する例:
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

	// これはすべての FGameplayAbilityTargetData の子構造体に必要です
	virtual UScriptStruct* GetScriptStruct() const override
	{
		return FGameplayAbilityTargetData_CustomData::StaticStruct();
	}

	// これはすべての FGameplayAbilityTargetData の子構造体に必要です
	bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
	{
		// エンジンはすでに FName と FPredictionKey の NetSerialize を定義しています。Epic に感謝！
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
		WithNetSerializer = true // これは FGameplayAbilityTargetDataHandle のネットワークシリアライズが機能するために必要です
	};
};
```
ターゲットデータをハンドルに追加するためのコード:
```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
	// 独自のターゲットデータ型を作成します。
	// ハンドルは自動的にこのデータをクリーンアップし、ハンドルが破棄されるときに削除します。
	// このデータをハンドルに追加しない場合、メモリ管理やメモリリークの問題が発生する可能性があるため、フレーム内で必ずハンドルに追加するのが安全です。
	FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
	// 入力された名前を使用して構造体の情報を設定し、必要に応じて他の変更を行います。
	MyCustomData->CoolName = CustomName;
	
	// Blueprint 用のハンドルラッパーを作成します。
	FGameplayAbilityTargetDataHandle Handle;
	// ターゲットデータをハンドルに追加します。
	Handle.Add(MyCustomData);
	// ハンドルを Blueprint に出力します。
	return Handle;
}
```

値を取得するには型安全性のチェックが必要です。なぜなら、ハンドルのターゲットデータから値を取得する唯一の方法は、汎用的なC/C++キャストを使用することであり、これは型安全ではなく、オブジェクトのスライスやクラッシュを引き起こす可能性があるからです。型チェックを行う方法はいくつかあります（基本的には自由に選べます）が、一般的な方法として以下の2つがあります：
- Gameplay Tag(s): サブクラスの階層を使用し、特定のコードアーキテクチャの機能が発生するたびに、基底クラスの型にキャストしてその"Gameplay Tag(s)"を取得し、それを比較して継承クラスのキャストを行う方法です。
- Script Struct & Static Structs: 直接クラス比較を行う方法です（多くのIF文を使用するか、テンプレート関数を作成する必要がある場合があります）。以下はその例ですが、基本的には任意の`FGameplayAbilityTargetData`からスクリプト構造体を取得し（これは`USTRUCT`であることの利点であり、継承クラスが`GetScriptStruct`で構造体タイプを指定する必要があるため）、探している型と比較します。以下は、型チェックにこれらの関数を使用する例です：
```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{   
	// 注意: この '::Get(int32 Index)' 関数には2つのバージョンがあります。
	// 1) const バージョンは 'const FGameplayAbilityTargetData*' を返し、ターゲットデータの値を読み取るのに適しています。
	// 2) 非 const バージョンは 'FGameplayAbilityTargetData*' を返し、ターゲットデータの値を変更するのに適しています。
	FGameplayAbilityTargetData* Data = Handle.Get(Index); // インデックスの有効性はこの関数がチェックしてくれます。
	
	// 使用可能なデータがあるかを確認します。null データの場合、キャストするものがありません。
	if(Data == nullptr)
	{
		return NAME_None;
	}
	// これは基本的に型チェックの処理です。static_cast は型安全ではないため、このチェックを行います。
	// このチェックを行わないと、構造体がオブジェクトスライスされ、型を保証する方法がなくなります。
	if(Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
	{
		// ここでキャストを行います。この時点で正しい型であることが確認されています。
		FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);    
		return CustomData->CoolName;
	}
	return NAME_None;
}
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 ターゲットアクター
`GameplayAbilities`は、`WaitTargetData` `AbilityTask`を使用して[`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)を生成し、ワールドからターゲット情報を視覚化および取得します。`TargetActors`は、オプションで[`GameplayAbilityWorldReticles`](#concepts-targeting-reticles)を使用して現在のターゲットを表示することができます。確認後、ターゲット情報は[`TargetData`](#concepts-targeting-data)として返され、その後`GameplayEffects`に渡すことができます。

`TargetActors`は`AActor`を基にしているため、スタティックメッシュやデカールなど、どのような種類の可視コンポーネントでも使用して「どこで」「どのように」ターゲットしているかを表現できます。スタティックメッシュは、キャラクターが構築するオブジェクトの配置を視覚化するために使用できます。デカールは、地面上の効果範囲を表示するために使用できます。サンプルプロジェクトでは、[`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html)を使用し、地面にデカールを表示してメテオアビリティのダメージ範囲を表現しています。また、何も表示しないことも可能です。例えば、ヒットスキャン銃のように、ターゲットに向けて即座にラインをトレースする場合、何かを表示するのは意味がありません。この例は[GASShooter](https://github.com/tranek/GASShooter)で使用されています。

これらは基本的なトレースやコリジョンオーバーラップを使用してターゲット情報を取得し、その結果を`FHitResults`または`AActor`配列として`TargetData`に変換します。`WaitTargetData` `AbilityTask`は、`TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType`パラメータを通じてターゲットが確認されるタイミングを決定します。`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`を使用しない場合、`TargetActor`は通常、`Tick()`でトレース/オーバーラップを実行し、その実装に応じて`FHitResult`に基づいてその位置を更新します。この間、`Tick()`でトレース/オーバーラップを実行しますが、これは一般的に問題ではありません。なぜなら、複製されず、通常は1つ（場合によっては複数）の`TargetActor`しか実行されないためです。ただし、`Tick()`を使用していることを認識しておく必要があります。複雑な`TargetActors`は、例えばGASShooterのロケットランチャーのセカンダリアビリティのように、`Tick()`で多くの処理を行う可能性があります。`Tick()`でのトレースはクライアントにとって非常に応答性が高いですが、パフォーマンスの影響が大きい場合は、`TargetActor`のティックレートを下げることを検討してください。`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`の場合、`TargetActor`は即座に生成され、`TargetData`を生成し、破棄されます。この場合、`Tick()`は一切呼び出されません。 

| `EGameplayTargetingConfirmation::Type` | ターゲットが確定されるタイミング                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | ターゲティングは即座に行われ、特別なロジックやユーザー入力による「発射」の決定を必要としません。                                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | ターゲティングは、[アビリティが`Confirm`入力にバインドされている](#concepts-ga-input)場合、または`UAbilitySystemComponent::TargetConfirm()`を呼び出すことで、ユーザーがターゲティングを確定したときに行われます。また、`TargetActor`はバインドされた`Cancel`入力や`UAbilitySystemComponent::TargetCancel()`の呼び出しにも応答してターゲティングをキャンセルします。                              |
| `Custom`                               | GameplayTargetingアビリティが、`UGameplayAbility::ConfirmTaskByInstanceName()`を呼び出すことでターゲティングデータが準備完了したタイミングを決定します。また、`TargetActor`は`UGameplayAbility::CancelTaskByInstanceName()`の呼び出しにも応答してターゲティングをキャンセルします。                                                                                              |
| `CustomMulti`                          | GameplayTargetingアビリティが、`UGameplayAbility::ConfirmTaskByInstanceName()`を呼び出すことでターゲティングデータが準備完了したタイミングを決定します。また、`TargetActor`は`UGameplayAbility::CancelTaskByInstanceName()`の呼び出しにも応答してターゲティングをキャンセルします。データ生成時に`AbilityTask`を終了すべきではありません。                                       |

すべての`EGameplayTargetingConfirmation::Type`がすべての`TargetActor`でサポートされているわけではありません。例えば、`AGameplayAbilityTargetActor_GroundTrace`は`Instant`確認をサポートしていません。

`WaitTargetData` `AbilityTask`は、パラメータとして`AGameplayAbilityTargetActor`クラスを受け取り、この`AbilityTask`がアクティブ化されるたびにインスタンスを生成し、`AbilityTask`が終了すると`TargetActor`を破棄します。一方、`WaitTargetDataUsingActor` `AbilityTask`は、すでに生成された`TargetActor`を受け取りますが、それでも`AbilityTask`が終了すると破棄されます。これらの`AbilityTask`は、使用ごとに新しい`TargetActor`を生成または必要とするため、効率的ではありません。プロトタイピングには適していますが、自動ライフルのように`TargetData`を頻繁に生成するケースでは、最適化を検討する必要があります。GASShooterでは、[`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h)のカスタムサブクラスと、新しい[`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask`をゼロから作成し、`TargetActor`を破棄せずに再利用できるようにしています。

`TargetActors`はデフォルトでは複製されません。ただし、ローカルプレイヤーがどこをターゲットしているかを他のプレイヤーに表示する必要がある場合、複製可能にすることができます。また、`WaitTargetData` `AbilityTask`上でRPCを介してサーバーと通信するためのデフォルト機能も含まれています。`TargetActor`の`ShouldProduceTargetDataOnServer`プロパティが`false`に設定されている場合、クライアントは`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`内で`CallServerSetReplicatedTargetData()`を使用して`TargetData`をサーバーに送信します。一方、`ShouldProduceTargetDataOnServer`が`true`の場合、クライアントは`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`内で汎用確認イベント`EAbilityGenericReplicatedEvent::GenericConfirm`をサーバーにRPCで送信し、サーバーはRPCを受信した際にトレースまたはオーバーラップチェックを実行してサーバー上でデータを生成します。クライアントがターゲティングをキャンセルした場合、`UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`内で汎用キャンセルイベント`EAbilityGenericReplicatedEvent::GenericCancel`をサーバーにRPCで送信します。このように、`TargetActor`と`WaitTargetData` `AbilityTask`の両方に多くのデリゲートがあります。`TargetActor`は入力に応答して`TargetData`準備完了、確認、またはキャンセルのデリゲートを生成およびブロードキャストします。`WaitTargetData`は`TargetActor`の`TargetData`準備完了、確認、およびキャンセルのデリゲートをリッスンし、その情報を`GameplayAbility`およびサーバーに中継します。サーバーに`TargetData`を送信する場合、不正行為を防ぐためにサーバー側で`TargetData`が妥当であるかを検証することを検討してください。サーバー上で直接`TargetData`を生成することでこの問題を完全に回避できますが、所有クライアントにとっては予測ミスが発生する可能性があります。

使用する`AGameplayAbilityTargetActor`の特定のサブクラスによって、`WaitTargetData` `AbilityTask`ノードで公開される`ExposeOnSpawn`パラメータが異なります。一般的なパラメータには以下が含まれます：

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | `true`の場合、非出荷ビルドで`TargetActor`がトレースを実行するたびにデバッグトレース/オーバーラップ情報を描画します。非`Instant`の`TargetActor`は`Tick()`でトレースを実行するため、これらのデバッグ描画呼び出しも`Tick()`で発生します。                                                                                     |
| Filter                          | [オプション] トレース/オーバーラップが発生した際にターゲットから`Actor`を除外（フィルタリング）するための特別な構造体。典型的な使用例としては、プレイヤーの`Pawn`を除外する、ターゲットが特定のクラスであることを要求するなどがあります。詳細は[ターゲットデータフィルタ](#concepts-target-data-filters)を参照してください。 |
| Reticle Class                   | [オプション] `TargetActor`がスポーンする`AGameplayAbilityWorldReticle`のサブクラス。                                                                                                                                                                                                                                 |
| Reticle Parameters              | [オプション] レティクルを設定します。詳細は[レティクル](#concepts-targeting-reticles)を参照してください。                                                                                                                                                                                                                  |
| Start Location                  | トレースを開始する場所を指定する特別な構造体。通常、プレイヤーの視点、武器の銃口、または`Pawn`の位置が使用されます。                                                                                                                                                                                                   |

デフォルトの`TargetActor`クラスでは、`Actors`はトレース/オーバーラップ内に直接存在する場合のみ有効なターゲットと見なされます。トレース/オーバーラップから外れる（移動したり、視線を外したりする）と、それ以上有効ではなくなります。最後に有効だったターゲットを`TargetActor`が記憶するようにしたい場合、この機能をカスタム`TargetActor`クラスに追加する必要があります。これを「永続的なターゲット」と呼びます。永続的なターゲットは、`TargetActor`が確認またはキャンセルを受け取るか、`TargetActor`がトレース/オーバーラップ内で新しい有効なターゲットを見つけるか、ターゲットが無効（破壊されるなど）になるまで持続します。GASShooterでは、ロケットランチャーのセカンダリアビリティのホーミングロケットターゲティングに永続的なターゲットを使用しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 ターゲットデータフィルタ
`Make GameplayTargetDataFilter`ノードと`Make Filter Handle`ノードを使用して、プレイヤーの`Pawn`を除外したり、特定のクラスのみを選択したりすることができます。さらに高度なフィルタリングが必要な場合は、`FGameplayTargetDataFilter`をサブクラス化し、`FilterPassesForActor`関数をオーバーライドすることができます。 
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

However, this will not work directly into the `Wait Target Data` node as it requires a `FGameplayTargetDataFilterHandle`. A new custom `Make Filter Handle` must be made to accept the subclass:
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

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)（以下「レティクル」）は、非「Instant」確認の[`TargetActors`](#concepts-targeting-actors)を使用してターゲットを行う際に、**誰を**ターゲットしているかを視覚化します。`TargetActors`は、すべての`Reticles`の生成と破棄のライフタイムを管理します。`Reticles`は`AActor`を基にしているため、表現に任意の種類のビジュアルコンポーネントを使用できます。[GASShooter](https://github.com/tranek/GASShooter)で見られる一般的な実装として、`WidgetComponent`を使用してUMGウィジェットを画面空間に表示（常にプレイヤーのカメラに向ける）する方法があります。`Reticles`は、どの`AActor`に配置されているかを認識しませんが、カスタム`TargetActor`でその機能をサブクラス化することができます。`TargetActors`は通常、ターゲットの位置に基づいて`Reticle`の位置を毎フレーム更新します。

GASShooterでは、ロケットランチャーのセカンダリアビリティのホーミングロケットのターゲットを表示するために`Reticles`を使用しています。敵に表示される赤いインジケーターが`Reticle`です。同様の白い画像はロケットランチャーのクロスヘアです。
![GASShooterでのReticles](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` には、デザイナー向けにいくつかの `BlueprintImplementableEvents` が用意されています（Blueprint で開発されることを想定しています）:

```c++
/** bIsTargetValid の値が変更されたときに呼び出されます。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** bIsTargetAnActor の値が変更されたときに呼び出されます。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

/** パラメータが初期化されたときに呼び出されます。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

/** Reticle のマテリアルパラメータ（浮動小数点）を設定します。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

/** Reticle のマテリアルパラメータ（ベクトル）を設定します。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles`は、`TargetActor`によって提供される[`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html)を使用して設定することができます。デフォルトの構造体は1つの変数`FVector AOEScale`のみを提供します。この構造体をサブクラス化することは技術的には可能ですが、`TargetActor`は基本構造体のみを受け入れます。デフォルトの`TargetActors`でこれをサブクラス化できないのは少し短絡的に思えます。ただし、独自のカスタム`TargetActor`を作成すれば、独自のカスタムレティクルパラメータ構造体を提供し、それを`AGameplayAbilityWorldReticles`のサブクラスに手動で渡すことができます。

`Reticles`はデフォルトでは複製されませんが、ローカルプレイヤーがターゲットしている相手を他のプレイヤーに表示する必要がある場合、複製可能にすることができます。

デフォルトの`TargetActors`では、`Reticles`は現在有効なターゲットにのみ表示されます。例えば、`AGameplayAbilityTargetActor_SingleLineTrace`を使用してターゲットをトレースする場合、`Reticle`は敵がトレースパス内に直接いるときにのみ表示されます。視線を外すと、敵はもはや有効なターゲットではなくなり、`Reticle`は消えます。最後に有効だったターゲットに`Reticle`を保持したい場合、`TargetActor`をカスタマイズして最後の有効なターゲットを記憶し、`Reticle`をそのターゲットに保持する必要があります。これを「永続的なターゲット」と呼びます。永続的なターゲットは、`TargetActor`が確認またはキャンセルを受け取るか、`TargetActor`がトレース/オーバーラップ内で新しい有効なターゲットを見つけるか、ターゲットが無効（破壊されるなど）になるまで持続します。GASShooterでは、ロケットランチャーのセカンダリアビリティのホーミングロケットターゲティングに永続的なターゲットを使用しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers ターゲティング
[`GameplayEffectContainers`](#concepts-ge-containers) には、[`TargetData`](#concepts-targeting-data) を生成するためのオプションで効率的な手段が用意されています。このターゲティングは、クライアントとサーバーで `EffectContainer` が適用された際に即座に行われます。これは [`TargetActors`](#concepts-targeting-actors) よりも効率的で、ターゲティングオブジェクトのCDO上で実行されるため（`Actors` の生成や破棄が不要）、プレイヤー入力を必要とせず、即座に実行され、確認を必要とせず、キャンセルもできず、クライアントからサーバーにデータを送信することもできません（両方でデータを生成します）。即時トレースやコリジョンオーバーラップには適しています。Epicの[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) には、コンテナでのターゲティングの例として、アビリティの所有者をターゲットにするものや、イベントから `TargetData` を取得するものが含まれています。また、プレイヤーからのオフセット位置で即時の球体トレースを行うBlueprintの実装も含まれています（子Blueprintクラスによって設定されます）。独自のターゲティングタイプを作成するには、C++またはBlueprintで `URPGTargetType` をサブクラス化します。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae"></a>
## 5. よく実装されるアビリティとエフェクト

<a name="cae-stun"></a>
### 5.1 スタン
スタンでは通常、`Character` のアクティブな `GameplayAbilities` をすべてキャンセルし、スタンの期間中は新しい `GameplayAbility` のアクティベーションを防ぎ、移動を禁止します。サンプルプロジェクトのメテオ `GameplayAbility` は、ヒットしたターゲットにスタンを適用します。

ターゲットのアクティブな `GameplayAbilities` をキャンセルするには、スタン [`GameplayTag` が追加された](#concepts-gt-change) ときに `AbilitySystemComponent->CancelAbilities()` を呼び出します。

スタン中に新しい `GameplayAbilities` がアクティベーションされるのを防ぐには、`GameplayAbilities` にスタン `GameplayTag` を [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) に設定します。

スタン中に移動を禁止するには、`CharacterMovementComponent` の `GetMaxSpeed()` 関数をオーバーライドし、所有者がスタン `GameplayTag` を持っている場合に 0 を返すようにします。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 スプリント
サンプルプロジェクトでは、`Left Shift` を押している間、より速く走るスプリントの例を提供しています。

より速い移動は、`CharacterMovementComponent` によって予測的に処理され、フラグをネットワーク経由でサーバーに送信します。詳細は `GDCharacterMovementComponent.h/cpp` を参照してください。

`GA` は `Left Shift` 入力に応答し、`CMC` にスプリントの開始と停止を指示し、`Left Shift` が押されている間に予測的にスタミナを消費します。詳細は `GA_Sprint_BP` を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 エイムダウンサイト
サンプルプロジェクトでは、スプリントと全く同じ方法で処理されますが、移動速度を増加させる代わりに減少させます。

移動速度を予測的に減少させる方法については、`GDCharacterMovementComponent.h/cpp` を参照してください。

入力を処理する方法については、`GA_AimDownSight_BP` を参照してください。エイムダウンサイトにはスタミナコストはありません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 ライフスティール
ライフスティールはダメージの [`ExecutionCalculation`](#concepts-ge-ec) 内で処理します。この `GameplayEffect` には `Effect.CanLifesteal` のような `GameplayTag` が付いています。`ExecutionCalculation` は `GameplayEffectSpec` にこの `Effect.CanLifesteal` `GameplayTag` があるかどうかを確認します。`GameplayTag` が存在する場合、`ExecutionCalculation` は[動的な `Instant` `GameplayEffect`](#concepts-ge-dynamic) を作成し、与えるヘルス量を修飾子として設定し、それを `Source` の `ASC` に適用します。

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

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 クライアントとサーバーで同じ乱数を生成する
時には、弾の反動や拡散のようなもののために、`GameplayAbility`内で「乱数」を生成する必要があります。クライアントとサーバーの両方で同じ乱数を生成する必要があります。そのためには、`GameplayAbility`のアクティベーション時に`random seed`を同じ値に設定する必要があります。クライアントがアクティベーションを誤予測し、乱数シーケンスがサーバーと同期しなくなる可能性があるため、`GameplayAbility`をアクティベートするたびに`random seed`を設定する必要があります。


| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | `GameplayAbility` のアクティベーション予測キーは、クライアントとサーバーの両方で `Activation()` 内で同期されて利用可能であることが保証された int16 です。これをクライアントとサーバーの両方で `random seed` として設定できます。この方法の欠点は、予測キーがゲーム開始時に常にゼロから始まり、キーを生成するたびに一貫して値をインクリメントすることです。つまり、各試合で完全に同じ乱数シーケンスが生成されます。これがあなたのニーズにとって十分にランダムであるかどうかは場合によります。 |
| Send a seed through an event payload when you activate the `GameplayAbility` | イベントを介して `GameplayAbility` をアクティベートし、クライアントからサーバーにレプリケートされたイベントペイロードを通じてランダムに生成されたシードを送信します。これにより、より多くのランダム性が得られますが、クライアントがゲームをハックして毎回同じシード値のみを送信する可能性があります。また、イベントによる `GameplayAbilities` のアクティベーションは、入力バインドからのアクティベーションを妨げます。                                                                                                                                                                     |

もしランダムな偏差が小さい場合、ほとんどのプレイヤーはシーケンスが毎回同じであることに気づかないため、アクティベーション予測キーを「ランダムシード」として使用することで十分でしょう。より複雑でハッカー対策が必要な場合は、サーバーが予測キーを作成したり、イベントペイロードを介して送信する「ランダムシード」を生成できる「サーバー開始型」の「GameplayAbility」を使用する方法が適しているかもしれません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 クリティカルヒット
クリティカルヒットはダメージの[`ExecutionCalculation`](#concepts-ge-ec)内で処理します。この`GameplayEffect`には`GameplayTag`として`Effect.CanCrit`が付いています。`ExecutionCalculation`は`GameplayEffectSpec`にこの`Effect.CanCrit`の`GameplayTag`があるかどうかを確認します。`GameplayTag`が存在する場合、`ExecutionCalculation`はクリティカルヒットの確率（`Source`からキャプチャされた`Attribute`）に対応する乱数を生成し、成功した場合はクリティカルヒットダメージ（同じく`Source`からキャプチャされた`Attribute`）を追加します。ダメージを予測しないため、クライアントとサーバーの乱数生成器を同期させる必要はありません。なぜなら、`ExecutionCalculation`はサーバー上でのみ実行されるからです。もしダメージ計算を予測的に行うために`MMC`を使用する場合、`GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`から「ランダムシード」への参照を取得する必要があります。

[GASShooter](https://github.com/tranek/GASShooter)がヘッドショットをどのように処理しているかを参照してください。同じコンセプトですが、ランダムな確率に依存せず、代わりに`FHitResult`のボーン名をチェックしています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 スタックしないGameplayEffectで、最も大きな効果のみがターゲットに影響を与える
Paragonではスロー効果はスタックしませんでした。それぞれのスロー効果は通常通り適用され、その持続時間を追跡しますが、最も大きな効果量のスロー効果のみが`Character`に影響を与えます。GASはこのシナリオを`AggregatorEvaluateMetaData`で標準機能として提供しています。詳細と実装については[`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 ゲームが一時停止中にターゲットデータを生成する
プレイヤーの`WaitTargetData` `AbilityTask`から[`TargetData`](#concepts-targeting-data)を生成する間にゲームを一時停止する必要がある場合、代わりに`slomo 0`を使用することをお勧めします。

**[⬆ トップに戻る](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 ワンボタンインタラクションシステム
[GASShooter](https://github.com/tranek/GASShooter)では、プレイヤーが「E」を押すか長押しすることで、プレイヤーの復活、武器チェストの開放、スライドドアの開閉などのインタラクション可能なオブジェクトとやり取りできるワンボタンインタラクションシステムを実装しています。

**[⬆ トップに戻る](#table-of-contents)**

<a name="debugging"></a>
## 6. GASのデバッグ
GAS関連の問題をデバッグする際には、以下のような情報を知りたいことがよくあります：
> * 「Attributeの値は何ですか？」
> * 「どのGameplayTagを持っていますか？」
> * 「現在どのGameplayEffectを持っていますか？」
> * 「どのアビリティが付与されていて、どれが実行中で、どれがアクティベーションをブロックされていますか？」

GASには、これらの質問にランタイムで答えるための2つの手法があります - [`showdebug abilitysystem`](#debugging-sd)と[`GameplayDebugger`](#debugging-gd)のフックです。

**ヒント:** Unreal EngineはC++コードを最適化するのが好きで、これにより一部の関数をデバッグするのが難しくなります。コードを深く追跡する際にこれに遭遇することは稀です。Visual Studioのソリューション構成を`DebugGame Editor`に設定してもコードの追跡や変数の検査ができない場合、最適化をすべて無効にするには、最適化された関数を`UE_DISABLE_OPTIMIZATION`と`UE_ENABLE_OPTIMIZATION`マクロで囲むか、CoreMiscDefines.hで定義されている出荷バリエーションを使用します。この方法は、プラグインコードには使用できません（プラグインをソースから再構築する必要があります）。インライン関数が何をしているか、どこにあるかによって、この方法が機能するかどうかが異なります。デバッグが終わったら必ずマクロを削除してください！

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ トップに戻る](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
ゲーム内コンソールで `showdebug abilitysystem` と入力してください。この機能は3つの「ページ」に分かれています。すべてのページで現在持っている `GameplayTags` を表示します。コンソールで `AbilitySystem.Debug.NextCategory` を入力すると、ページを切り替えることができます。

最初のページでは、すべての `Attributes` の `CurrentValue` を表示します：
![showdebug abilitysystem の最初のページ](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

2番目のページでは、現在の `Duration` と `Infinite` の `GameplayEffects`、そのスタック数、付与される `GameplayTags`、および付与される `Modifiers` を表示します。
![showdebug abilitysystem の2番目のページ](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

3番目のページでは、付与されたすべての `GameplayAbilities`、現在実行中かどうか、アクティベーションがブロックされているかどうか、現在実行中の `AbilityTasks` のステータスを表示します。
![showdebug abilitysystem の3番目のページ](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

ターゲットを切り替えるには（アクターの周りに緑色の長方形のプリズムで表示されます）、`PageUp` キーまたは `NextDebugTarget` コンソールコマンドで次のターゲットに移動し、`PageDown` キーまたは `PreviousDebugTarget` コンソールコマンドで前のターゲットに移動します。

**注意:** 現在選択されているデバッグアクターに基づいてアビリティシステム情報を更新するには、`AbilitySystemGlobals` の `bUseDebugTargetFromHud=true` を `DefaultGame.ini` に以下のように設定する必要があります：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**注意:** `showdebug abilitysystem` を使用するには、実際の HUD クラスが GameMode に選択されている必要があります。そうでない場合、このコマンドは見つからず、「Unknown Command」と表示されます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GASはGameplay Debuggerに機能を追加します。Gameplay Debuggerにはアポストロフィ（'）キーでアクセスできます。テンキーの3を押すことでAbilitiesカテゴリを有効にします。使用しているプラグインによってカテゴリが異なる場合があります。ノートパソコンのようにテンキーがない場合は、プロジェクト設定でキー設定を変更できます。

Gameplay Debuggerは、**他の**`Characters`の`GameplayTags`、`GameplayEffects`、および`GameplayAbilities`を確認したい場合に使用します。残念ながら、ターゲットの`Attributes`の`CurrentValue`は表示されません。画面の中央にある`Character`をターゲットにします。ターゲットを変更するには、エディタ内のWorld Outlinerで選択するか、別の`Character`を見て再度アポストロフィ（'）を押します。現在検査中の`Character`の上には最も大きな赤い円が表示されます。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS ログ
GASのソースコードには、さまざまな詳細レベルで生成される多くのログステートメントが含まれています。これらは主に`ABILITY_LOG()`ステートメントとして表示されます。デフォルトの詳細レベルは`Display`です。それ以上の詳細レベルは、デフォルトではコンソールに表示されません。

ログカテゴリの詳細レベルを変更するには、コンソールに以下を入力します：

```
log [category] [verbosity]
```

例えば、`ABILITY_LOG()`ステートメントを有効にするには、コンソールに以下を入力します：
```
log LogAbilitySystem VeryVerbose
```

デフォルトにリセットするには、以下を入力します：
```
log LogAbilitySystem Display
```

すべてのログカテゴリを表示するには、以下を入力します：
```
log list
```


GASに関連する注目すべきログカテゴリ:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://unrealcommunity.wiki/logging-lgpidy6i) for more information.

**[⬆ トップに戻る](#table-of-contents)**

<a name="optimizations"></a>
## 7. 最適化

<a name="optimizations-abilitybatching"></a>
### 7.1 アビリティのバッチ処理
1フレーム内でアクティベートされ、オプションで"TargetData"をサーバーに送信し、終了する"GameplayAbilities"は、[2～3つのRPCを1つのRPCにまとめるためにバッチ処理](#concepts-ga-batching)できます。このタイプのアビリティは、ヒットスキャン銃でよく使用されます。

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cueのバッチ処理
多くの"GameplayCues"を同時に送信する場合、[1つのRPCにまとめる](#concepts-gc-batching)ことを検討してください。目標は、RPCの数（"GameplayCues"は信頼性のないNetMulticast）を減らし、可能な限り少ないデータを送信することです。

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponentのレプリケーションモード
デフォルトでは、"ASC"は["フルレプリケーションモード"](#concepts-asc-rm)に設定されています。これにより、すべての"GameplayEffects"がすべてのクライアントにレプリケートされます（これはシングルプレイヤーゲームでは問題ありません）。マルチプレイヤーゲームでは、プレイヤーが所有する"ASCs"を"混合レプリケーションモード"に設定し、AIが制御するキャラクターを"最小レプリケーションモード"に設定します。これにより、プレイヤーキャラクターに適用された"GameplayEffects"はそのキャラクターの所有者にのみレプリケートされ、AIが制御するキャラクターに適用された"GameplayEffects"はクライアントにレプリケートされません。"GameplayTags"は引き続きレプリケートされ、"GameplayCues"は"レプリケーションモード"に関係なく、すべてのクライアントに信頼性のないNetMulticastとして送信されます。これにより、すべてのクライアントが必要としない"GameplayEffects"のレプリケーションによるネットワークデータが削減されます。

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 Attributeプロキシのレプリケーション
Fortnite Battle Royale（FNBR）のような大規模なゲームでは、多くのプレイヤーが存在し、常に関連する"PlayerStates"に多くの"ASCs"が存在し、多くの"Attributes"をレプリケートします。このボトルネックを最適化するために、Fortniteは"PlayerState::ReplicateSubobjects()"で**シミュレートされたプレイヤー制御プロキシ**の"ASC"とその"AttributeSets"のレプリケーションを完全に無効にします。自律プロキシとAI制御の"Pawn"は、引き続きその["レプリケーションモード"](#concepts-asc-rm)に従って完全にレプリケートされます。常に関連する"PlayerStates"で"ASC"の"Attributes"をレプリケートする代わりに、FNBRはプレイヤーの"Pawn"にレプリケートされたプロキシ構造を使用します。"Attributes"がサーバーの"ASC"で変更されると、それらはプロキシ構造にも変更されます。クライアントはプロキシ構造からレプリケートされた"Attributes"を受け取り、それらの変更をローカルの"ASC"にプッシュします。これにより、"Attributes"のレプリケーションが"Pawn"の関連性と"NetUpdateFrequency"を利用できるようになります。このプロキシ構造は、ビットマスクで少数のホワイトリスト化された"GameplayTags"もレプリケートします。この最適化により、ネットワーク上のデータ量が削減され、Pawnの関連性を活用できます。AI制御の"Pawn"は"Pawn"上に"ASC"を持ち、すでにその関連性を使用しているため、この最適化は必要ありません。

> 他のサーバー側の最適化（レプリケーショングラフなど）が行われた後でも、これがまだ必要かどうかはわかりません。また、これは最も保守性の高いパターンではありません。

*EpicのDave Rattiによる[コミュニティ質問#3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)への回答*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASCの遅延読み込み
Fortnite Battle Royale（FNBR）には、世界中に多くのダメージ可能な"AActors"（木、建物など）が存在し、それぞれに"ASC"があります。これにより、メモリコストが増加します。FNBRは、"ASC"を必要なとき（プレイヤーが最初にダメージを与えたとき）にのみ遅延読み込みすることでこれを最適化します。これにより、試合中に一部の"AActors"がダメージを受けない場合、全体的なメモリ使用量が削減されます。

**[⬆ トップに戻る](#table-of-contents)**

<a name="qol"></a>
## 8. 品質向上の提案

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) は、[`GameplayEffectSpecs`](#concepts-ge-spec)、[`TargetData`](#concepts-targeting-data)、[シンプルなターゲティング](#concepts-targeting-containers)、および関連機能を簡単に使用できる構造体にまとめたものです。これらは、アビリティから生成されたプロジェクタイルに`GameplayEffectSpecs`を転送し、その後の衝突時に適用するのに最適です。

<a name="qol-asynctasksascdelegates"></a>
### 8.2 ASCデリゲートにバインドするためのBlueprint AsyncTasks
特にUMGウィジェットのUI設計時に、デザイナーが使いやすい反復時間を向上させるために、`ASC`上の一般的な変更デリゲートに直接バインドするためのBlueprint AsyncTasks（C++で作成）を作成します。唯一の注意点は、これらは手動で破棄する必要があることです（例えば、ウィジェットが破棄されたとき）。そうしないと、メモリに永遠に残ります。サンプルプロジェクトには3つのBlueprint AsyncTasksが含まれています。

`Attribute`の変更をリッスンします：

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

クールダウンの変更をリッスンします：

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

`GE`スタックの変更をリッスンします：

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. トラブルシューティング

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
クライアント上で`ASC`を[初期化する](#concepts-asc-setup)必要があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` エラー
[`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) を呼び出す必要があります。

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 アニメーションモンタージュがクライアントにレプリケートされない
[GameplayAbilities](#concepts-ga) で `PlayMontage` ノードではなく `PlayMontageAndWait` Blueprint ノードを使用していることを確認してください。この [AbilityTask](#concepts-at) はモンタージュを `ASC` を通じて自動的にレプリケートしますが、`PlayMontage` ノードはそうしません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Blueprint アクターを複製すると AttributeSets が nullptr に設定される
既存の Blueprint アクター クラスから複製された Blueprint アクター クラスで `AttributeSet` ポインタが nullptr に設定される [Unreal Engine のバグ](https://issues.unrealengine.com/issue/UE-81109) があります。これにはいくつかの回避策があります。私が成功した方法は、クラスに固有の `AttributeSet` ポインタを作成しないことです（.h ファイルにポインタを記述せず、コンストラクタで `CreateDefaultSubobject` を呼び出さない）。代わりに、`PostInitializeComponents()` 内で直接 `ASC` に `AttributeSets` を追加します（サンプルプロジェクトには表示されていません）。レプリケートされた `AttributeSets` は引き続き `ASC` の `SpawnedAttributes` 配列内に存在します。この方法は以下のようになります：
```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// 他の AttributeSets がある場合はここに追加
	}
}
```

このシナリオでは、[マクロから作成された関数を呼び出す](#concepts-as-attributes)代わりに、`ASC` 上の関数を使用して `AttributeSet` の値を読み取り、設定します。

```c++
/** Attributeの現在の（最終的な）値を返します */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Attributeの基本値を設定します。既存のアクティブな修飾子はクリアされず、新しい基本値に基づいて動作します。 */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

したがって、`GetHealth()` は次のようになります：

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

`Attribute` を設定（初期化）する場合は次のようになります：

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

リマインダーとして、`ASC` は各 `AttributeSet` クラスごとに最大1つの `AttributeSet` オブジェクトしか期待しません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 未解決の外部シンボル UEPushModelPrivate::MarkPropertyDirty(int,int)

次のようなコンパイルエラーが発生した場合：

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

これは、`FFastArraySerializer` で `MarkItemDirty()` を呼び出そうとしたときに発生します。たとえば、クールダウンの持続時間を更新する際に `ActiveGameplayEffect` を更新するときに遭遇することがあります。

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

ここで起きているのは、`WITH_PUSH_MODEL` が複数の場所で定義されていることです。`PushModelMacros.h` では 0 として定義されている一方で、複数の場所で 1 として定義されています。`PushModel.h` は 1 として認識していますが、`PushModel.cpp` は 0 として認識しています。

解決策として、プロジェクトの `Build.cs` の `PublicDependencyModuleNames` に `NetCore` を追加してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>
### 9.6 Enum 名が現在パス名で表現されている

次のようなコンパイル警告が発生した場合：

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE 5.1 では、`BindAbilityActivationToInputComponent()` のコンストラクタで `FString` を使用することが非推奨となりました。代わりに、`FTopLevelAssetPath` を渡す必要があります。

古い非推奨の方法：
```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

新しい方法：
```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

詳細については、`Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h` を参照してください。

**[⬆ トップに戻る](#table-of-contents)**

<a name="acronyms"></a>
## 10. 一般的なGAS略語

| 名前                                                                                                   | 略語                |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ トップに戻る](#table-of-contents)**

<a name="resources"></a>
## 11. その他のリソース
* [公式ドキュメント](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* ソースコード！
	* 特に `GameplayPrediction.h`
* [EpicによるLyraサンプルプロジェクト](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [EpicによるAction RPGサンプルプロジェクト](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/)にはGAS専用のテキストチャンネル`#gameplay-ability-system`があります
	* ピン留めされたメッセージを確認してください
* [Dan 'Pan'によるリソースのGitHubリポジトリ](https://github.com/Pantong51/GASContent)
* [SabreDartStudiosによるYouTube動画](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Epic GamesのDave RattiとのQ&A

<a name="resources-daveratti-community1"></a>
#### 11.1.1 コミュニティ質問 1
[Unreal Slackers DiscordサーバーのコミュニティによるGASに関する質問に対するDave Rattiの回答](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. `GameplayAbilities`とは無関係に、またはそれを超えて、スコープ付き予測ウィンドウをオンデマンドで作成するにはどうすればよいですか？例えば、敵にヒットした際にダメージ`GameplayEffect`をローカルで予測するためのファイア・アンド・フォーゲット型プロジェクタイルをどのように作成できますか？

> PredictionKeyシステムはこれを行うことを意図していません。このシステムは基本的に、クライアントが予測的なアクションを開始し、そのキーをサーバーに伝え、クライアントとサーバーが同じことを実行し、予測的な副作用をその予測キーに関連付けるという仕組みで動作します。例えば、「私は予測的にアビリティをアクティベートしています」や「ターゲットデータを生成し、WaitTargetDataタスクの後にアビリティグラフを予測的に実行します」などです。
>
> このパターンでは、PredictionKeyはサーバーから「跳ね返り」、UAbilitySystemComponent::ReplicatedPredictionKeyMap（レプリケートされたプロパティ）を介してクライアントに戻ります。キーがサーバーからレプリケートされると、クライアントはローカルで予測された副作用（GameplayCues、GameplayEffects）を元に戻すことができます。レプリケートされたバージョンが存在する場合、それらが存在しない場合は誤予測です。予測的な副作用を元に戻す正確なタイミングを知ることが重要です。早すぎるとギャップが生じ、遅すぎると「二重」になります。（これは、持続時間ベースのGameplayEffectのループするGameplayCueのような状態予測に関連しています。「バースト」GameplayCuesやインスタントGameplayEffectsは決して「元に戻される」ことはなく、単にクライアントでスキップされます。）
>
> さらに強調すると、予測的なアクションは、サーバーが独自に行うものではなく、クライアントから指示された場合にのみ行うものであることが重要です。そのため、「オンデマンドでキーを作成し、サーバーに伝えて何かを実行する」という一般的な方法は、その「何か」がクライアントから指示されるまでサーバーが独自に行わないものでない限り機能しません。
>
> 元の質問に戻ると、ファイア・アンド・フォーゲット型プロジェクタイルのようなものです。ParagonとFortniteの両方にはGameplayCuesを使用するプロジェクタイルアクタークラスがあります。ただし、これらではPredictionKeyシステムを使用していません。代わりに、非レプリケートGameplayCuesの概念を持っています。これらは単にUGameplayCueManager::HandleGameplayCueへの直接呼び出しであり、UAbilitySystemComponentを経由しないため、予測キーのチェックや早期リターンは行われません。
>
> 非レプリケートGameplayCuesの欠点は、レプリケートされないことです。そのため、プロジェクタイルクラスやBlueprintがこれらの関数を呼び出すコードパスがすべての人で実行されることを確認する必要があります。これには、起動時（BeginPlayで呼び出される）、爆発、壁やキャラクターへのヒットなどが含まれます。
>
> これらのタイプのイベントはすでにクライアント側で生成されているため、非レプリケートGameplayCueを呼び出すことは問題ありません。複雑なBlueprintは難しい場合があり、どこで何が実行されているかを理解する必要があります。

2. ローカルで予測された`GameplayAbility`で`OnlyServerWait`を使用した`WaitNetSync` `AbilityTask`を使用してスコープ付き予測ウィンドウを作成する場合、サーバーが予測キーを含むRPCを待機している間に、プレイヤーがパケットを遅延させて`GameplayAbility`のタイミングを制御することで不正行為を行う可能性がありますか？ParagonやFortniteでこの問題が発生したことはありますか？その場合、Epicはどのように対処しましたか？

> はい、これは有効な懸念事項です。クライアントの「信号」を待機しているサーバー上のアビリティBlueprintは、ラグスイッチ型の不正行為に対して潜在的に脆弱です。
>
> Paragonには、UAbilityTask_WaitTargetDataに似たカスタムターゲティングタスクがありました。このタスクでは、インスタントターゲティングモードの場合、クライアントを待機する最大遅延時間やタイムアウトを設定していました。ターゲティングモードがユーザー確認（ボタン押下）を待機している場合、この制限は無視されます。ただし、ターゲティングが即座に確認されるアビリティの場合、一定時間のみ待機し、その後A）サーバー側でターゲットデータを生成するか、B）アビリティをキャンセルするかのいずれかを行いました。
>
> WaitNetSyncに対しては、このようなメカニズムはありませんでしたが、使用頻度は少なかったです。
>
> Fortniteではこのような仕組みは使用されていないと思います。Fortniteの武器アビリティは、特定のFortnite専用RPCにバッチ処理されています。1つのRPCでアビリティをアクティベートし、ターゲットデータを提供し、アビリティを終了します。そのため、Battle Royaleでは武器アビリティは本質的にこの問題に対して脆弱ではありません。
>
> 私の見解では、これはシステム全体で解決できる問題だと思いますが、私たちがすぐにこの変更を行うことはないでしょう。WaitNetSyncをスポット修正して、あなたが言及したケースに対して最大遅延を含めるのは合理的なタスクだと思いますが、これもまた、私たちがすぐに行うことはないでしょう。


3. ParagonとFortniteはどの`EGameplayEffectReplicationMode`を使用しており、各モードを使用する際のEpicの推奨事項は何ですか？

> 両ゲームとも、プレイヤーが操作するキャラクターには基本的にMixedモードを使用し、AIが操作するキャラクター（AIミニオン、ジャングルクリープ、AIハスクなど）にはMinimalモードを使用しています。これは、マルチプレイヤーゲームでこのシステムを使用するほとんどの人に推奨される設定です。プロジェクトの早い段階でこれらを設定するほど良いです。
>
> Fortniteはさらに最適化を進めています。実際には、シミュレートされたプロキシに対して`UAbilitySystemComponent`を全くレプリケートしません。このコンポーネントとAttributeのサブオブジェクトは、所有するFortniteプレイヤーステートクラスの`::ReplicateSubobjects()`内でスキップされます。代わりに、アビリティシステムコンポーネントから最小限のレプリケートデータをポーン上の構造体にプッシュします（基本的にはAttribute値のサブセットと、ビットマスクでレプリケートされるタグのホワイトリストサブセット）。これを「プロキシ」と呼びます。受信側では、ポーン上でレプリケートされたプロキシデータを取得し、それをプレイヤーステート上のアビリティシステムコンポーネントにプッシュバックします。そのため、FNBRでは各プレイヤーにASCがありますが、それは直接レプリケートされず、代わりにポーン上の最小プロキシ構造を介してデータをレプリケートし、受信側でASCにルーティングされます。この利点は、A）より最小限のデータセットであること、B）ポーンの関連性を活用できることです。
>
> 他のサーバー側の最適化（レプリケーショングラフなど）が行われた後でも、これがまだ必要かどうかはわかりません。また、これは最も保守性の高いパターンではありません。

4. `GameplayPrediction.h`に記載されているように、`GameplayEffects`の削除を予測することができない場合、`GameplayEffects`の削除に伴う遅延の影響を軽減するための戦略はありますか？例えば、移動速度の低下を削除する際、サーバーが`GameplayEffect`の削除をレプリケートするのを待つ必要があるため、プレイヤーキャラクターの位置がスナップする現象が発生します。

> これは難しい問題で、良い解決策はありません。私たちは一般的に許容範囲やスムージングでこれらの問題を回避してきました。アビリティシステムとキャラクター移動システムの正確な同期が良好な状態ではないことは完全に同意しますし、これは修正したいと考えています。
>
> `GameplayEffects`の削除を予測することを可能にするアイデアを検討したことがありますが、すべてのエッジケースを解決することができず、他の作業に移らざるを得ませんでした。ただし、これはすべてを解決するわけではありません。キャラクター移動には、アビリティシステムや移動速度修飾子などを知らない内部の保存された移動バッファがあるためです。このため、`GameplayEffects`の削除を予測できない場合でも、修正フィードバックループに陥る可能性があります。
>
> 本当に切迫したケースがある場合、移動速度を抑制する`GameplayEffects`を予測的に追加することができます。私はこれを実際に行ったことはありませんが、以前に理論的に検討したことがあります。これにより、特定の問題クラスに対処できる可能性があります。

5. ParagonとFortniteでは`AbilitySystemComponent`が`PlayerState`に存在し、Action RPG Sampleでは`Character`に存在しています。Epicの内部ルール、ガイドライン、または`AbilitySystemComponent`をどこに配置すべきか、またその`Owner`を何にすべきかについての推奨事項は何ですか？

> 一般的に、リスポーンする必要がないものは、`Owner`と`Avatar`アクターを同じものにするべきです。AIの敵、建物、ワールドプロップなどがこれに該当します。
>
> リスポーンする必要があるものは、`Owner`と`Avatar`を異なるものにするべきです。これにより、リスポーン後にアビリティシステムコンポーネントを保存、再作成、または復元する必要がなくなります。`PlayerState`は論理的な選択肢です。これはすべてのクライアントにレプリケートされます（`PlayerController`はそうではありません）。欠点は、`PlayerState`が常に関連性を持つため、100人のプレイヤーがいるゲームでは問題が発生する可能性があることです（質問3のFortniteの対応を参照してください）。

6. 同じ`Owner`を持つが異なる`Avatar`を持つ複数の`AbilitySystemComponent`を持つことは可能ですか（例：ポーンと武器/アイテム/プロジェクタイルに`Owner`を`PlayerState`として設定）？

> 最初の問題は、所有するアクターに`IGameplayTagAssetInterface`と`IAbilitySystemInterface`を実装することです。前者は可能かもしれません：すべてのASCからタグを集約するだけです（ただし注意が必要です。`HasAllMatchingGameplayTags`は、ASC間の集約によってのみ満たされる場合があります。単に各ASCに呼び出しを転送し、結果をORするだけでは不十分です）。しかし、後者はさらに難しいです：どのASCが権威あるものですか？誰かがGEを適用したい場合、どのASCがそれを受け取るべきですか？これらを解決することは可能かもしれませんが、この側面が最も難しい部分です：複数のASCを持つ所有者。
>
> ポーンと武器に別々のASCを持つことは、それ自体で意味をなす場合があります。例えば、武器を記述するタグと所有するポーンを記述するタグを区別する場合です。武器に付与されたタグが所有者にも「適用」され、他には適用されない（例えば、AttributeとGEは独立しているが、所有者は所有するタグを集約する）というのは理にかなっているかもしれません。これが機能する可能性は十分にあります。ただし、同じ所有者を持つ複数のASCを持つことは、やや複雑になる可能性があります。

7. 所有するクライアント上でローカルに予測されたアビリティのクールダウン期間をサーバーが上書きするのを防ぐ方法はありますか？高遅延のシナリオでは、所有するクライアントがローカルのクールダウンが終了したときにアビリティを再度「試みる」ことができるようになりますが、サーバーではまだクールダウン中です。所有するクライアントのアクティベーション要求がネットワークを介してサーバーに到達する頃には、サーバーがクールダウンを終了している可能性があります。または、サーバーが残りのミリ秒の間アクティベーション要求をキューに入れることができるかもしれません。現在の状態では、高遅延のクライアントは低遅延のクライアントに比べてアビリティを再アクティベートできるまでの遅延が長くなります。これは、1秒未満のクールダウンを持つ基本攻撃のような非常に短いクールダウンアビリティで最も顕著です。サーバーがローカルに予測されたアビリティのクールダウン期間を上書きするのを防ぐ方法がない場合、Epicは高遅延の影響を軽減するためにどのような戦略を採用していますか？別の例を挙げると、EpicはParagonの基本攻撃や他のアビリティをどのように設計して、高遅延のプレイヤーが低遅延のプレイヤーと同じ速度で攻撃やアクティベートできるようにしましたか？

> 短い答えとしては、その方法はなく、Paragonでは確かにこの問題がありました。高遅延の接続では基本攻撃の発射速度が低下していました。
>
> これを修正するために、GEの期間を計算する際に遅延を考慮する「GE調整」を追加しようとしました。基本的には、サーバーがGEの合計時間の一部を消費し、クライアント側でのGEの有効時間が遅延に関係なく100％一貫するようにするというものです（ただし、変動によって問題が発生する可能性はあります）。しかし、これを出荷可能な状態で動作させることができず、プロジェクトが進むにつれて完全に対処することはありませんでした。
>
> Fortniteでは武器の発射速度を管理するために独自の帳簿を使用しており、武器のクールダウンにはGEを使用していません。この問題がゲームにとって重要な場合は、これを推奨します。


8. EpicのGameplayAbilitySystemプラグインのロードマップは何ですか？2019年以降にEpicが追加する予定の機能はどれですか？

> 現時点では、システム全体がかなり安定していると感じており、大きな新機能に取り組んでいる人はいません。FortniteやUDN/プルリクエストからのバグ修正や小さな改善が時折行われていますが、それだけです。
>
> 長期的には、「V2」または大きな変更を行う可能性があります。このシステムを作成する中で多くのことを学び、多くのことが正しかったと感じる一方で、多くの間違いもありました。それらの間違いを修正し、上記で指摘された致命的な欠陥を改善する機会があればと思います。
>
> もし「V2」が実現する場合、アップグレードパスを提供することが最も重要です。「V2」を作成してFortniteを「V1」のままにすることは決してありません。可能な限り自動的に移行するための手順やプロセスが提供されるでしょうが、それでも手動で再作成が必要な部分がある可能性は高いです。
>
> 高優先度の修正は以下の通りです：
> * キャラクター移動システムとの相互運用性の向上。クライアント予測の統一。
> * GE削除の予測（質問#4）
> * GE遅延の調整（質問#7）
> * Fortniteで行ったような、RPCのバッチ処理やプロキシ構造などの一般的なネットワーク最適化。これらをより一般化された形で分解し、少なくともゲームが独自のゲーム固有の最適化をより簡単に記述できるようにする方法を見つける。
>
> より一般的なリファクタリングの変更として考えられるもの：
> * GEがスプレッドシートの値を直接参照するのではなく、パラメータを発行し、それらのパラメータをスプレッドシートの値にバインドされた上位オブジェクトが埋めることができるようにする方向に根本的に移行したいと考えています。現在のモデルの問題は、GEがカーブテーブル行と密接に結びついているため、共有できなくなることです。パラメータ化のための一般化されたシステムを記述し、それをV2システムの基盤とすることができると思います。
> * UGameplayAbilityにある「ポリシー」の数を減らしたいです。ReplicationPolicyとInstancingPolicyを削除します。Replicationはほとんど実際には必要なく、混乱を引き起こすと考えています。InstancingPolicyは、FGameplayAbilitySpecをサブクラス化可能なUObjectに置き換えるべきです。これが「非インスタンス化されたアビリティオブジェクト」であり、イベントを持ち、Blueprint化可能であるべきです。UGameplayAbilityは「実行ごとにインスタンス化される」オブジェクトであるべきです。インスタンス化が必要な場合にのみオプションで使用できます。「非インスタンス化」アビリティは、新しいUGameplayAbilitySpecオブジェクトを介して実装されます。
> * システムは「フィルタリングされたGE適用コンテナ」（どのGEをどのアクターに適用するかをデータ駆動で決定する高レベルのゲームプレイロジック）、「重複するボリュームサポート」（コリジョンプライミティブの重複イベントに基づいて「フィルタリングされたGE適用コンテナ」を適用する）などの「中間レベル」の構造をより多く提供するべきです。これらはすべてのプロジェクトが独自の方法で実装することになるビルディングブロックです。それらを正しく実装するのは簡単ではないため、基本的な実装を提供することでより良い仕事をするべきだと思います。
> * プロジェクトを立ち上げるために必要なボイラープレートを減らす。おそらく「Exライブラリ」などの別モジュールを作成し、パッシブアビリティや基本的なヒットスキャン武器などをすぐに利用できるようにする。このモジュールはオプションですが、迅速に立ち上げることができます。
> * GameplayCuesをアビリティシステムと結びつかない別モジュールに移動したいと考えています。ここで多くの改善が可能だと思います。

> これはあくまで私個人の意見であり、誰かのコミットメントではありません。最も現実的な進行方向は、新しいエンジン技術のイニシアチブが進むにつれて、アビリティシステムが更新される必要があり、その際にこの種の変更を行うことだと思います。これらのイニシアチブはスクリプティング、ネットワーキング、または物理/キャラクター移動に関連している可能性があります。ただし、これは非常に先の話であり、タイムラインについてのコミットメントや見積もりを提供することはできません。

**[⬆ トップに戻る](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 コミュニティ質問 2
コミュニティメンバー[iniside](https://github.com/iniside)によるDave RattiとのQ&A：

1. 固定ティックを分離するサポートは計画されていますか？ゲームスレッドを固定（30/60fpsなど）にし、レンダリングスレッドを自由に動作させたいです。将来これが期待できるかどうかを尋ねるのは、ゲームプレイがどのように機能するべきかについていくつかの仮定を立てるためです。主に、物理の非同期固定ティックが現在存在しているため、システムの残りの部分が将来どのように機能するかという疑問が生じます。ゲームスレッドの固定ティックを持ちながら、エンジンの残りのティックレートを固定しない能力があれば非常に素晴らしいと思います。

> レンダリングフレームレートとゲームスレッドティックフレームレートを分離する計画はありません。これらのシステムの複雑さと、以前のエンジンバージョンとの後方互換性を維持する必要性から、この変更が行われる可能性は低いと思います。
>
> 代わりに、私たちが進んでいる方向性は、ゲームスレッドとは独立して固定ティックレートで動作する非同期「物理スレッド」を持つことです。固定レートで動作する必要があるものはここで動作し、ゲームスレッド/レンダリングはこれまで通り動作します。
>
> 明確にする価値があるのは、ネットワーク予測が「独立ティック」と「固定ティック」モードをサポートしているということです。私の長期的な計画は、ネットワーク予測において独立ティックを現在のように維持し、ゲームスレッドで可変フレームレートで動作し、「グループ/ワールド」予測がなく、クライアントが自分のポーンや所有するアクターを予測する従来のモデルを維持することです。そして固定ティックは非同期物理を使用し、物理オブジェクトや他のクライアント/ポーン/車両などのクライアントが制御していない/所有していないアクターを予測することを可能にします。


2. ネットワーク予測とアビリティシステムの統合がどのように行われるかについての計画はありますか？例えば、固定フレームアビリティのアクティベーション（サーバーがアビリティがアクティベートされたフレームやタスクが実行されたフレームを受け取る代わりに予測キーを使用する）などですか？

> はい、アビリティシステムの予測キーを再設計または削除し、ネットワーク予測の構造に置き換える計画があります。NetworkPredictionExtrasのMockAbilityの例はこれがどのように機能するかを示していますが、GASが必要とするものよりも「ハードコーディング」されています。
>
> 主なアイデアは、ASCのRPCにおける明示的なクライアント->サーバーの予測キー交換を削除することです。これにより、予測ウィンドウやスコープ付き予測キーはなくなります。その代わり、すべてがネットワーク予測フレームを基準に行われます。重要なのは、クライアントとサーバーがいつ何が起こるかについて合意することです。例としては以下が挙げられます：
>
> * アビリティがアクティベート/終了/キャンセルされたタイミング
> * GameplayEffectが適用/削除されたタイミング
> * Attribute値（フレームXでのAttribute値が何であったか）
>
> これはアビリティシステムレベルで一般的に実行できると思います。ただし、UGameplayAbility内のユーザー定義ロジックを完全にロールバック可能にするには、さらに作業が必要です。UGameplayAbilityのサブクラスを作成し、それを完全にロールバック可能にし、より限定された機能セットまたはロールバック対応としてマークされたアビリティタスクのみを使用できるようにする可能性があります。そのようなものです。また、アニメーションイベントやルートモーションの処理方法にも多くの影響があります。
>
> より明確な答えを出したいところですが、GASに再び手を加える前に基盤をしっかりと整えることが非常に重要です。移動や物理が確固たるものでなければ、高レベルのシステムを変更することはできません。

3. ネットワーク予測の開発をメインブランチに移行する計画はありますか？正直なところ、最新のコードを確認したいです。状態に関係なく。

> その方向に向かっています。このシステムの作業はまだすべてNetworkPredictionで行われています（NetworkPhysics.hを参照）。基盤となる非同期物理関連のもの（RewindData.hなど）はすべて利用可能なはずです。ただし、Fortniteでのユースケースに焦点を当てており、それらは公開できません。現在、バグ修正やパフォーマンス最適化などに取り組んでいます。
>
> さらなる背景として、このシステムの初期バージョンに取り組んでいたとき、私たちは「フロントエンド」の部分、つまり状態やシミュレーションがどのように定義され、記述されるかに非常に焦点を当てていました。そこで多くのことを学びました。しかし、非同期物理がオンラインになったことで、初期の抽象化の一部を放棄してでも、このシステムで実際に機能するものを得ることに集中してきました。目標は、実際のものが機能するようになったら、再び統一することです。例えば、「フロントエンド」に戻り、その最終バージョンを現在取り組んでいるコア技術の上に構築することです。

4. 一時的にメインブランチに存在していたGameplay Messages（イベント/メッセージバスのように見える）プラグインが削除されました。これを復元する計画はありますか？Game Features/Modular Gameplayプラグインと組み合わせて、汎用的なイベントバスディスパッチャーが非常に役立つと思います。

> おそらくGameplayMessagesプラグインのことを指していると思います。これはいずれ復活する可能性がありますが、APIはまだ完全に確定しておらず、作成者はそれを公開するつもりではありませんでした。モジュラーゲームプレイ設計には役立つはずですが、私の専門分野ではないため、これ以上の情報は持っていません。 


5. 最近、非同期固定物理を試してみたところ、結果は有望でした。ただし、将来的にネットワーク予測（NP）の更新がある場合は、それを試して待つことになるかもしれません。現在のところ、エンジン全体を固定ティックにする必要があり、一方で物理を33msに保とうとしています。このため、すべてが30fpsで動作する場合、良い体験にはなりません。

非同期の"CharacterMovementComponent"に関する作業があることに気づきましたが、これがネットワーク予測を使用するのか、それとも別の取り組みなのかはわかりません。

これに気づいたので、固定ティックレートで動作するカスタム非同期移動を実装してみました。結果はまずまずでしたが、その上に補間のための別の更新を追加する必要がありました。セットアップとしては、シミュレーションティックを別のワーカースレッドで固定33msの更新で実行し、計算を行い、結果を保存し、ゲームスレッドで補間して現在のフレームレートに一致させるというものでした。完璧ではありませんが、目的は達成できました。

私の質問は、将来的にこれが設定しやすくなる可能性があるかどうかです。補間部分を書くためのボイラープレートコードがかなり多く、特に効率的ではないからです。各移動オブジェクトを個別に補間するのは非効率的です。

非同期の仕組みは非常に興味深いです。これにより、ゲームシミュレーションを固定更新レートで実行できるようになり（これにより固定スレッドが不要になります）、より予測可能な結果が得られます。これは今後進められる予定のものですか、それとも特定のシステムに対する利点に過ぎないのでしょうか？私の記憶では、アクタートランスフォームは非同期で更新されず、ブループリントは完全にスレッドセーフではありません。言い換えれば、これはフレームワークレベルでサポートされる予定のものですか、それとも各ゲームが独自に解決する必要があるものですか？

> 非同期"CharacterMovementComponent"
>
> これは基本的に、現在の"CMC"を物理スレッドに移植するための初期プロトタイプ/実験です。これが"CMC"の将来の形になるとはまだ考えていませんが、進化する可能性はあります。現時点ではネットワークサポートがないため、あまり注目するべきではありません。このシステムが追加する入力遅延を測定し、それをどのように軽減できるかに関心を持つ人々が主に取り組んでいます。
>
> エンジン全体を固定ティックにする必要があり、一方で物理を33msに保とうとしています。このため、すべてが30fpsで動作する場合、良い体験にはなりません。
>
> 非同期の仕組みは非常に興味深いです。これにより、ゲームシミュレーションを固定更新レートで実行できるようになり（これにより固定スレッドが不要になります）。
>
> はい。ここでの目標は、非同期物理を有効にすることで、エンジンを可変ティックレートで実行しながら、物理や「コア」ゲームプレイシミュレーションを固定レート（キャラクター移動、車両、GASなど）で実行できるようにすることです。
>
> 現在これを有効にするために設定する必要がある"cvars"は次のとおりです（おそらくすでに理解されていると思います）：  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> "Chaos"は物理状態の補間を提供します（例：ゲームコードで表示される"UPrimitiveComponent"にプッシュバックされるトランスフォーム）。現在、これを制御する"cvar"として`p.AsyncInterpolationMultiplier`があります。これを確認したい場合は見てみてください。物理ボディのスムーズで連続的な動きを追加コードなしで確認できるはずです。
>
> 非物理状態を補間したい場合は、現時点ではそれを手動で行う必要があります。例としては、非同期物理スレッドで更新（ティック）したいクールダウンがあり、ゲームスレッドでスムーズで連続的な補間を行い、すべてのレンダーフレームでクールダウンのビジュアライゼーションが更新されるようにする場合です。これについては最終的に対応する予定ですが、まだ例はありません。
>
> ボイラープレートコードがかなり多い、
>
> はい、これまでのところシステム全体の大きな一般的な問題です。経験豊富なプログラマーがパフォーマンスと安全性を最大化するために使用できるインターフェースを提供したいと考えています（予測的に「ただ動作する」ゲームプレイコードを記述する能力を提供し、多くの危険や「できるけどやらないほうがいい」ことを排除する）。そのため、"CharacterMovement"のようなものは、パフォーマンスを最大化するために多くのカスタム処理を行うかもしれません。例えば、テンプレート化されたコードを書き、バッチ更新を行い、広範囲にわたり、更新ループを明確なフェーズに分割するなどです。このユースケースに対して、非同期スレッドとロールバックシステムへの良い「低レベル」インターフェースを提供したいと考えています。そして、この場合も、キャラクター移動システム自体が独自の方法で拡張可能であることが合理的です。例えば、カスタム移動モードをブループリント化し、スレッドセーフなブループリントAPIを提供する方法を提供するなどです。
>
> しかし、独自の「システム」を必要としないシンプルなゲームプレイオブジェクトに対しては、これは受け入れられません。Unrealにより近いものが必要です。例えば、リフレクションシステムを使用し、一般的なブループリントサポートを持つなどです。他のスレッドでブループリントが使用されている例があります（"BlueprintThreadSafe"キーワードとアニメーションシステムが目指しているものを参照してください）。そのため、これがいつか何らかの形で実現すると思います。しかし、まだそこには到達していません。
>
> 補間についてだけ尋ねていることは理解していますが、これが一般的な回答です：現時点では、すべてを手動で行う必要があります（"NetSerialize"、"ShouldReconcile"、"Interpolate"など）。しかし、最終的には「リフレクションシステムを使用したい場合は、これらのことを手動で書く必要はありません」という方法が提供されるでしょう。リフレクションシステムを使用することをすべての人に強制したくないだけです。これは、システムの最も低いレベルで取り入れたくない他の制限を課すことになるからです。
>
> そして、これを先ほど述べたことに結びつけると、現在は非常に具体的な例を機能させ、パフォーマンスを向上させることに集中しています。その後、フロントエンドに再び注意を向け、使いやすく反復可能にし、ボイラープレートを削減するなど、他のすべての人が使用できるようにする予定です。

**[⬆ トップに戻る](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS Changelog

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

**[⬆ Back to Top](#table-of-contents)**
