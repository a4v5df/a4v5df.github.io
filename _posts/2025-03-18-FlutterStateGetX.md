---
title: Flutter 状態管理の概念と GetX 学習レポート
date: 2025-03-18
categories: [flutter]
math: true
excerpt: Flutterの状態管理の基本概念とGetXを使ったリアクティブな状態管理手法を学習
---

## 1. 状態管理 (State Management)

### 1.1 概念

状態 (State) とは、アプリケーション内で変化するデータを指します。状態管理は、UI とデータ間の一貫性を効率的に維持する手法です。Flutter では、状態が変更されるとウィジェットを再ビルドして画面を更新することで動作します。

### 1.2 状態管理の必要性

Flutter は宣言的 UI フレームワークであるため、状態の変化に応じて UI が適切に更新される必要があります。これを効率的に管理しないと、次のような問題が発生する可能性があります：

- **不要な再ビルド:** 全体のウィジェットが不必要に再描画される。
- **非効率なデータフロー:** 親子関係を経由するデータ伝達過程での複雑性の増加。
- **予測困難な状態変化:** 状態が複数の場所で変更される場合、メンテナンスが困難になる。

### 1.3 状態管理の種類

Flutter の状態管理は、大きく分けて **ローカル状態管理** と **グローバル状態管理** に分類されます。

- **ローカル状態管理 (Local State Management):** ウィジェット内部でのみ使用する状態を管理。
  - `setState()`
  - `InheritedWidget`
  - `Provider`

- **グローバル状態管理 (Global State Management):** アプリ全体で状態を共有し管理。
  - `Provider`
  - `Riverpod`
  - `GetX`
  - `Bloc`

## 2. 状態管理とライフサイクル

### 2.1 ウィジェットのライフサイクル (Widget Lifecycle)

Flutter の `StatefulWidget` にはライフサイクルがあります。ウィジェットが生成されて削除される過程で様々な段階があり、状態を管理する際にこのライフサイクルを理解することが重要です。

#### StatefulWidget のライフサイクルの段階

1. `createState()` - State オブジェクトを生成します。
2. `initState()` - State が初めて生成されるときに一度呼び出されます。
3. `didChangeDependencies()` - 依存関係が変更されたときに呼び出されます。
4. `build()` - UI を描画する関数で、状態変更時に再び呼び出されます。
5. `didUpdateWidget()` - 親ウィジェットが変更されたときに呼び出されます。
6. `deactivate()` - ウィジェットがツリーから削除されるときに呼び出されます。
7. `dispose()` - State が完全に削除されるときに呼び出され、リソースの整理作業を行います。

```dart
class MyWidget extends StatefulWidget {
  @override
  _MyWidgetState createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  @override
  void initState() {
    super.initState();
    print('initState: ウィジェットが初めて生成されました');
  }

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    print('didChangeDependencies: 依存関係が変更されました');
  }

  @override
  Widget build(BuildContext context) {
    print('build: UI が再ビルドされました');
    return Scaffold(
      appBar: AppBar(title: Text('ライフサイクルの例')),
      body: Center(child: Text('Flutter ライフサイクルテスト')),
    );
  }

  @override
  void dispose() {
    print('dispose: ウィジェットが完全に削除されました');
    super.dispose();
  }
}
```

## 3. `setState()` を活用した状態更新

### 3.1 `setState()`

`setState()` は `StatefulWidget` で状態が変更されたとき、UI を再ビルドするための関数です。この関数は **状態が変更されるたびに必ず呼び出す必要があり、そうしないと UI は更新されません。**

```dart
class CounterPage extends StatefulWidget {
  @override
  _CounterPageState createState() => _CounterPageState();
}

class _CounterPageState extends State<CounterPage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('setState の例')),
      body: Center(
        child: Text(
          'Counter: $_counter',
          style: TextStyle(fontSize: 24),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

## 4. GetX 状態管理

### 4.0 GetX 概要

GetX は Flutter で最も軽量でありながら強力な状態管理ライブラリの一つで、簡単な API と高性能を提供します。また、状態管理だけでなく依存性注入 (Dependency Injection) とルーティング機能も含まれています。

### 4.1 GetX コントローラ (GetxController)

#### コントローラとは？

GetX コントローラ (`GetxController`) は **状態を管理** する役割を持ち、アプリのビジネスロジックと UI 状態を分離するために使用されます。

#### 主な機能

1. **状態管理** - UI 変更が必要なデータを管理
2. **依存性管理** - `Get.put()` を使用してグローバルにアクセス可能
3. **メモリ管理** - `onInit()`, `onClose()` などを活用してライフサイクル管理が可能

#### 基本コントローラの例

```dart
import 'package:get/get.dart';

// GetxController を継承してコントローラを作成
class CounterController extends GetxController {
  var count = 0.obs; // リアクティブ変数

  void increment() {
    count++; // 状態更新時に UI 自動変更
  }

  @override
  void onInit() {
    super.onInit();
    print("CounterController が生成されました");
  }

  @override
  void onClose() {
    print("CounterController が削除されました");
    super.onClose();
  }
}
```

#### UI でのコントローラ使用

```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
import 'counter_controller.dart';

class CounterPage extends StatelessWidget {
  final CounterController controller = Get.put(CounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("GetX コントローラの例")),
      body: Center(
        child: Obx(() => Text(
          "Counter: ${controller.count}",
          style: TextStyle(fontSize: 24),
        )),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

- `Get.put(CounterController());` → コントローラをメモリに登録
- `Obx(() => Text(...))` → リアクティブ状態変更時に UI 自動更新

### 4.2 Rx と .obs

GetX では **リアクティブ状態管理** のために `Rx<Type>` または `.obs` キーワードを使用します。

- `Rx<Type>`: 変数をリアクティブにして、UI を自動的に更新できるようにします。
- `.obs`: `Rx<Type>` を簡単に表現する短縮記法です。

#### 例:

```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';

// コントローラクラスの定義
class CounterController extends GetxController {
  // リアクティブ状態変数の宣言
  var count = 0.obs;

  // count 値を増加させる関数
  void increment() {
    count++; // count.value++ と同じ
  }
}

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      home: CounterPage(),
    );
  }
}

class CounterPage extends StatelessWidget {
  // Get.put を使用してコントローラをメモリに登録
  final CounterController controller = Get.put(CounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX カウンターの例')),
      body: Center(
        // Obx を使用して状態変化を自動的に検知し UI 更新
        child: Obx(() => Text(
              'Counter: ${controller.count}',
              style: TextStyle(fontSize: 24),
            )),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

- `count = 0.obs;` を使用すると、`count` 値が変更されたときに自動で UI が更新されます。
- `count++` を実行すると、`count` の値が増加し UI が自動で更新されます。
- `Obx` を使用して UI を自動的に更新できます。
- `Get.put(CounterController())` を使用してコントローラをグローバルに登録し管理できます。

### 4.3 GetX 状態管理方式

#### 1. **単純状態 (Simple State) (`GetBuilder`)**

- 状態が変更されたときに特定のウィジェットのみリビルドする。
- UI の更新が頻繁に起こらない場合に使用。
- `Obx` より軽量で、状態変更時に特定のウィジェットのみ再描画されます。

#### 例:

```dart
class SimpleCounterController extends GetxController {
  int count = 0;

  void increment() {
    count++;
    update(); // GetBuilder を使用する UI で変更を反映
  }
}

class SimpleCounterPage extends StatelessWidget {
  final SimpleCounterController controller = Get.put(SimpleCounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetBuilder の例')),
      body: Center(
        child: GetBuilder<SimpleCounterController>(
          builder: (_) => Text(
            'Counter: ${controller.count}',
            style: TextStyle(fontSize: 24),
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

- `GetBuilder` は `update()` メソッドを呼び出す必要があり、UI が更新されます。
- `Obx` より少ないリソースを使用しますが、リアクティブ状態管理より明示的に UI 更新を呼び出す必要があります。

#### 2. **リアクティブ状態 (Reactive State) (`Rx`)**

- 状態変更を自動で検知して UI を更新。
- `Rx<Type>` または `.obs` キーワードを使ってリアクティブ変数を生成。

#### 3. **Mixin ベース状態 (Mixin State) (`GetX`, `Obx`)**

- UI にリアクティブ変数を直接バインドして更新。
- `Obx` ウィジェットを使用して状態変化を検知し自動更新。

#### 例:

```dart
class MixinController extends GetxController {
  var count = 0.obs;

  void increment() {
    count++;
  }
}

class MixinCounterPage extends StatelessWidget {
  final MixinController controller = Get.put(MixinController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX Mixin の例')),
      body: Center(
        child: GetX<MixinController>(
          builder: (_) => Text(
            'Counter: ${controller.count}',
            style: TextStyle(fontSize: 24),
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

- `GetX<T>` を使用して UI がコントローラの状態変化を検知するようにします。
- `Obx` と似ていますが、コントローラを直接バインドしてより明確な構造を提供します。

### 4.4 Get.put() vs Get.lazyPut() vs Get.find()

| メソッド | 説明 |
|---|---|
| `Get.put()` | インスタンスを即座にメモリに生成して使用できるように登録。 |
| `Get.lazyPut(() => Controller())` | 必要な瞬間にのみインスタンスを生成してメモリを節約。 |
| `Get.find<T>()` | 既にメモリに登録済みのコントローラを探して使用。 |

#### 例:

```dart
class MyController extends GetxController {
  var count = 0.obs;
}

void main() {
  Get.lazyPut(() => MyController()); // コントローラを必要なときに生成
  final MyController controller = Get.find<MyController>(); // 登録されたコントローラを取得
  runApp(MyApp());
}
```

このように `Get.put()`, `Get.lazyPut()`, `Get.find()` を適切に使用してメモリ管理と性能を最適化できます。