---
title: Flutter Gestureの学習
date: 2025-03-18
categories: [flutter]
math: true
---

## 1. 概要

Flutterは、単一のコードベースで複数のプラットフォーム（iOS、Android、Web、デスクトップなど）で動作するアプリケーションを開発できるUIフレームワークです。Flutterアプリでのユーザー入力は通常、以下のステップで処理されます。

1. ユーザーが画面をタッチ（またはマウスクリックなど）します。
2. プラットフォームのオペレーティングシステムがそのイベントを受信し、Flutterエンジンに渡します。
3. Flutterエンジンはイベントをポインター（Pointer）イベントとして抽象化します。
4. UIフレームワークの層でヒットテスト（Hit Test）を行い、イベントがどのウィジェットに届いたか確認します。
5. 該当ウィジェットまたはそのウィジェットが内部で使用するジェスチャー認識器（GestureRecognizer）がイベントを受け取ります。
6. 複数のジェスチャーが同時に候補となる場合、**Gesture Arena**が最終的な勝者を決定します。
7. 勝利したジェスチャー認識器のコールバック（onTap, onPan, onLongPressなど）が実行され、開発者が定義したロジックが実行されます。

---

## 2. ジェスチャー認識パイプライン

### 2.1 ポインターイベントとヒットテスト

Flutterでジェスチャーが認識される前の段階では、ユーザーの入力が「ポインターイベント」として伝達されます。ポインターイベントには、指を新たに画面に触れたときに発生する`PointerDown`、指を離したときの`PointerUp`、移動時に発生する`PointerMove`などがあります。これらのポインターイベントはFlutterフレームワークのヒットテストを通じて特定のウィジェット（またはそのウィジェットに登録されたジェスチャー認識器）に渡されます。

ヒットテストは「タッチが実際にどのウィジェットエリアと接触したか」を判定する過程で、複数のウィジェットが重なっている場合は「最上位（レンダー順序上）から」探索します。各ウィジェットの`HitTestBehavior`（例: `opaque`、`translucent`、`deferToChild`）設定により、イベントがどのレベルまで伝達されるかが異なります。

### 2.2 GestureDetectorとGestureRecognizer

最もよく使用されるジェスチャーウィジェットは`GestureDetector`と呼ばれます。内部的にタップ（Tap）、ドラッグ（Pan）、ロングプレス（LongPress）などを感知するために様々な`GestureRecognizer`オブジェクトを生成します。実際のロジックは次のように動作します。

1. `GestureDetector`がヒットテストを通じてイベントを受信します。
2. `TapGestureRecognizer`、`PanGestureRecognizer`など必要な認識器がイベントを分析します。
3. 各認識器が自分のジェスチャーとして認識できると判断すれば**Gesture Arena**に参加して「勝利意思（claim）」を示し、適切でない場合は「放棄（reject）」します。
4. Arenaが最終勝者を決定すると、その認識器のコールバック（onTap、onPanStartなど）が実行されます。

---

## 3. 内部動作原理: Gesture Arena

Flutterのジェスチャー認識が強力で柔軟な理由は、**Gesture Arena**という衝突処理メカニズムにあります。複数のジェスチャーが同一イベントを同時に取得しようとする際、Arenaが中裁し最終勝者を決定します。動作原理は大きく次のステップで要約されます。

1. **PointerDownイベント発生**
    
    - ユーザーが画面を押した瞬間、その領域に反応できるすべてのジェスチャー認識器がArenaに登録されます。
    
2. **ポインター移動・アップイベント収集**
    
    - その後発生する`PointerMove`、`PointerUp`イベントを見て、各認識器は自分が検知するジェスチャー（タップ、ドラッグ、ロングプレスなど）に合致するかどうかを判別します。

3. **ClaimまたはReject**
    
    - ジェスチャー認識器が「このイベントは自分のジェスチャーだ」と確信すればArenaにclaimを宣言します。
    - 反対に、条件が合わず放棄する場合はrejectを宣言します。

4. **勝者決定**
    
    - 複数の認識器が同時にclaimした場合、Flutterは1つの認識器が決定的に「勝利」条件を満たす時点でその認識器を最終勝者として確定します。
    - 他の認識器はすべて敗北処理され、コールバックは呼び出されません。

5. **コールバック実行**
    
    - 勝利した認識器のコールバックが呼び出され、アプリのロジックを実行します。

同一ポインター（指1本）で発生するイベントは1つのArenaでのみ処理されます。もし2本の指（2つのPointer ID）を同時に使用する場合、それぞれのPointer IDごとに別のArenaが動作し、異なるジェスチャーが並行して進行することができます。

---

## 4. 複数ジェスチャー同時使用時の衝突事例と優先順位制御

### 4.1 衝突事例

1. **スクロール vs タップ**
    
    - スクロール可能なリスト（ListView）領域内にタップ可能な要素（Buttonなど）があると、ユーザーが少しだけドラッグしてもスクロールと認識されるのか、タップと認識されるのか曖昧になることがあります。
    - この時、一般的にリストスクロールが優先権を持つように実装します。
2. **ドラッグ vs ロングプレス**
    
    - 長押ししながら少し動かす動作をドラッグと見るか、ロングプレスと見るか判断が曖昧になることがあります。
    - ロングプレス認識器の設定時間（通常500ms程度）とドラッグの最小移動距離（閾値）によって決まります。
3. **親ウィジェット vs 子ウィジェット**
    
    - 親に`GestureDetector`があり、子にも別の`GestureDetector`がある場合、親がイベントを先に消費して子がイベントを受け取れないことがあります。
    - この場合、`behavior: HitTestBehavior.translucent`または`deferToChild`などを適切に設定してイベントフローを調整する必要があります。

### 4.2 優先順位制御技法

1. **HitTestBehavior調整**
    
    - `GestureDetector(behavior: HitTestBehavior.xxx)`形態でイベントがどの経路で伝達されるかを明示することができます。
    - 例: `HitTestBehavior.translucent`を使用すると、ウィジェット領域をタッチしてもイベントが子ウィジェットまで伝達される可能性があります。
2. **閾値（Threshold）調整**
    
    - `PanGestureRecognizer`などは一定距離以上動いたときにclaimを宣言します。この距離を大きくとると「少し動かしただけ」ではタップと認識される可能性が高くなります。
    - 反対に、小さくすると少しドラッグしただけで直ちにonPanイベントが発生します。
3. **RawGestureDetectorとカスタムRecognizer**
    
    - Flutterが基本提供する認識ロジックと異なり、`RawGestureDetector`を通じて直接`GestureRecognizer`を作成したり、詳細な優先順位ロジックを設定することができます。
    - 例: 特定の状況ではタップ認識器が非常に早くclaimするようにして、他の状況では逆にドラッグ認識器が優先してclaimするように設定することができます。
4. **ウィジェットツリー構造の単純化**
    
    - 多段階でネストされたウィジェットでジェスチャーが重なると衝突が頻発します。必要な場所にのみ`GestureDetector`を配置したり、イベントハンドリングロジックを分離して管理することで衝突を減らすことができます。

---

## 5. 例: 基本ジェスチャー処理

次の例は単一の`GestureDetector`を使用してタップ、ロングプレス、ドラッグイベントを簡単に処理する方法を示しています。

```dart
import 'package:flutter/material.dart';

class BasicGestureDemo extends StatefulWidget {
  @override
  _BasicGestureDemoState createState() => _BasicGestureDemoState();
}

class _BasicGestureDemoState extends State<BasicGestureDemo> {
  Color _color = Colors.blue;
  Offset _offset = Offset(100, 100);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Basic Gesture Demo')),
      body: GestureDetector(
        onTap: () {
          setState(() {
            // タップ時に色を変更
            _color = _color == Colors.blue ? Colors.red : Colors.blue;
          });
        },
        onLongPress: () {
          setState(() {
            // 長押しすると緑色に変更
            _color = Colors.green;
          });
        },
        onPanUpdate: (details) {
          setState(() {
            // ドラッグ（パン）移動
            _offset += details.delta;
          });
        },
        child: Stack(
          children: [
            Positioned(
              left: _offset.dx,
              top: _offset.dy,
              child: Container(
                width: 100,
                height: 100,
                color: _color,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```
- `onTap`: タップジェスチャーに反応してボックスの色を赤/青にトグルします。
- `onLongPress`: 長押しするとボックスの色を緑色に変更します。
- `onPanUpdate`: ドラッグするたびに現在の位置に`details.delta`を加えてボックスを移動させます。

この例は非常に簡単であるため、衝突処理の問題は大きく発生しません。ただし、ユーザーが押している間に少しだけ動かすと`onPanUpdate`が先にclaimしてタップジェスチャーが無視されることがあります。このような部分はFlutter内部の閾値ロジックによって決定されます。

---

## 6. 例: 複数ジェスチャー間の衝突処理

今回は親（全体画面）がドラッグを感知し、子（ボタンウィジェット）がタップを感知する状況を仮定します。ユーザーがボタンを押そうとしたのに少しだけ指が動いても親のドラッグとして認識される可能性があるため、希望通りに動作するように`behavior`などを設定することができます。

```dart 
import 'package:flutter/material.dart';

class ParentChildGestureDemo extends StatefulWidget {
  @override
  _ParentChildGestureDemoState createState() => _ParentChildGestureDemoState();
}

class _ParentChildGestureDemoState extends State<ParentChildGestureDemo> {
  double _dragPosition = 0.0;
  String _message = 'ボタンが押されていません';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Parent-Child Gesture Demo')),
      body: GestureDetector(
        // 親のドラッグ感知
        onPanUpdate: (details) {
          setState(() {
            _dragPosition += details.delta.dx;
          });
        },
        // hitTestBehaviorをtranslucentに設定すると、
        // 子ウィジェットもイベントを受け取れるようになります。
        behavior: HitTestBehavior.translucent,
        child: Stack(
          children: [
            Positioned(
              left: _dragPosition,
              top: 200,
              child: GestureDetector(
                onTap: () {
                  setState(() {
                    _message = '子ウィジェット（ボタン）が押されました';
                  });
                },
                child: Container(
                  width: 150,
                  height: 60,
                  color: Colors.blue,
                  alignment: Alignment.center,
                  child: Text('Tap Me', style: TextStyle(color: Colors.white)),
                ),
              ),
            ),
            Positioned(
              top: 400,
              left: 20,
              child: Text(_message, style: TextStyle(fontSize: 20)),
            ),
          ],
        ),
      ),
    );
  }
}
```

- 親ウィジェット（`GestureDetector`）で`onPanUpdate`を登録し、`behavior`を`HitTestBehavior.translucent`に設定しました。
- 子ウィジェット（青色ボタン）にも別途`GestureDetector`があり、onTapを感知します。
- この設定のおかげで親ウィジェットがドラッグを処理する一方で子ウィジェットでタップイベントが正常に処理されます。
- `behavior`を設定しないか`HitTestBehavior.opaque`に設定した場合、親がイベントを横取りし子のタップイベントが動作しない可能性があります。

---

## 7. RawGestureDetectorを利用した拡張例

`RawGestureDetector`を使用すると、Flutterが基本提供するジェスチャー認識器ではないカスタム認識器を登録したり、`GestureRecognizer`の優先順位制御を直接定義することができます。以下は単純化された例で、タップ（TapGestureRecognizer）と水平ドラッグ（HorizontalDragGestureRecognizer）を同時に登録し、各認識器がclaimするロジックを確認するコードです。

```dart
import 'package:flutter/material.dart';
import 'package:flutter/gestures.dart';

class RawGestureDemo extends StatefulWidget {
  @override
  _RawGestureDemoState createState() => _RawGestureDemoState();
}

class _RawGestureDemoState extends State<RawGestureDemo> {
  String _gestureText = 'まだジェスチャーなし';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('RawGestureDetector Demo')),
      body: RawGestureDetector(
        gestures: {
          // TapGestureRecognizer登録
          TapGestureRecognizer: GestureRecognizerFactoryWithHandlers<
              TapGestureRecognizer>(
            () => TapGestureRecognizer(),
            (TapGestureRecognizer instance) {
              instance.onTap = () {
                setState(() {
                  _gestureText = 'タップジェスチャー発生';
                });
              };
            },
          ),
          // HorizontalDragGestureRecognizer登録
          HorizontalDragGestureRecognizer:
              GestureRecognizerFactoryWithHandlers<
                  HorizontalDragGestureRecognizer>(
            () => HorizontalDragGestureRecognizer(),
            (HorizontalDragGestureRecognizer instance) {
              instance
                ..onStart = (details) {
                  setState(() {
                    _gestureText = '水平ドラッグ開始';
                  });
                }
                ..onUpdate = (details) {
                  setState(() {
                    _gestureText =
                        'ドラッグ中 (distance: ${details.primaryDelta?.toStringAsFixed(2)})';
                  });
                }
                ..onEnd = (details) {
                  setState(() {
                    _gestureText = '水平ドラッグ終了';
                  });
                };
            },
          ),
        },
        child: Center(
          child: Text(
            _gestureText,
            style: TextStyle(fontSize: 24),
          ),
        ),
      ),
    );
  }
}
```

- `RawGestureDetector`の`gestures`パラメータに、複数の`GestureRecognizerFactoryWithHandlers`を通じて認識器を登録します。
- ここでは`TapGestureRecognizer`と`HorizontalDragGestureRecognizer`を同時に登録し、タップと水平ドラッグを区別します。
- 実際の使用時、Flutterの内部ロジックに従い先にclaimするジェスチャーが最終的な勝者となります（スクロールが一定距離以上ならドラッグが優先、移動がほとんどなければタップとして認識）。
- 必要に応じて`GestureRecognizer`を継承し`acceptGesture`や`rejectGesture`メソッドをオーバーライドして直接優先順位を決めることもできます。

---

## 8. 実装時の考慮事項
1. **ユーザー期待行動に合致**
    
    - スクロール領域内のボタン、長押しでメニューを開く vs ドラッグなど、ユーザー体験をまず考慮すべきです。
    - 必要であれば閾値（ドラッグ距離、ロングプレス時間）を調整して意図したシナリオに合わせます。
2. **ウィジェットツリー構造簡素化**
    
    - 親と子、または兄弟ウィジェットで重複してジェスチャーを感知すると衝突が多発するため、不必要な`GestureDetector`の重複を避けます。
3. **HitTestBehavior設定確認**
    
    - イベントが上位、下位ウィジェットのどちらで処理されるよう誘導するか明確に設定します。
    - デフォルト値（`deferToChild`）で十分でない場合、`translucent`や`opaque`に変更して望む流れを誘導します。
4. **性能**
    
    - 一般的なアプリ規模では大きな問題にはなりませんが、過度に多くの`GestureDetector`や複雑なカスタム認識器を同時に登録すると性能が低下する可能性があります。
5. **テスト環境多様性**
    
    - シミュレータ/エミュレータだけでなく、実機、様々な画面サイズとプラットフォームでテストしてユーザーが実際にどのようなジェスチャーを主に使用するか把握します。

---

## 9. 結論

Flutterのジェスチャーシステムはポインターイベントを基盤とし、内部的に**Gesture Arena**メカニズムを使用して複数のジェスチャー認識器が同一イベントをclaimしようとする際に衝突を解決します。これによりタップ、ドラッグ、ロングプレス、スクロール、スワイプなど様々なインタラクションを柔軟に実装することが可能です。

- 基本的には`GestureDetector`を使用して`onTap`、`onPan`、`onLongPress`など各種コールバックを登録すれば良いです。
- イベント伝達優先順位が問題となる場合、**`HitTestBehavior`**設定、ドラッグ閾値調整、**`RawGestureDetector`**などを活用して希望する動作を細かく制御できます。
- **親子ウィジェット間のイベント衝突**も`behavior`オプションやウィジェットツリー設計を通じて解決可能です。