# 第１２章 メソッド(関数)
## -良きクラスには良きメソッドあり-


# 12.1 必ず自身のクラスのインスタンス変数を使うこと
- インスタンス変数を使う
  - immutable(不変)を心がけよう
  - 副作用(immutableでない=外部変数をmutableに再代入する → 様々な箇所に伝播する)のない設計に
  - ガード節を有効に使おう

#### So Bad...
```typescript
const damage = () => {
  // memberメンバーさんは一体誰？
  // 外部の関数でローカル変数の値が変化し
  // 更に、このdamage関数を利用した他の値も無法地帯に
  let basicAttackPower = member.power() + member.weaponAttack();
  let finalAttackPower = (int) => {
    basicAttackPower * (1f+member.speed() / 100f)
  };
  let reduction = (int)(enemy.defence/2);
  let damage = Math.max(0,finalAttackPowerreduction);
  return damage;
}
```

#### Good!
```java
int damage(attack) {
  // ガード節
  if (attack => 0) {
    return false;
  }
  final int basicAttackPower = member.power()+member.weaponAttack();
  final int finalAttackPower = (int)(basicAttackPower*(1f+member.speed()/100f));
  final int reduction = (int)(enemy.defence/2);
  final int damage = Math.max(0,finalAttackPowerreduction);
  return damage;
}
```

### コンストラクタでインスタンス変数を不変に

# 12.2. 不変をベースに予期せぬ動作を防ぐ関数にすること

## 「関数に引数Aを入れたら値Bが不変である事」
- 想定外の出力の無いような工夫
    - ガード節
    - 型の利用

# 12.3. 尋ねるな、命じろ

## 「よそのクラスを気にしたりいじったりするメソッド構造」は低凝集構造です」

## getter/setterはこの「よそのクラスを気にしたりいじったりするメソッド構造」に陥りやすく、
   => 結果、開発生産性が良くないソフトウェアのソースコードでは、頻繁に見受けられます

- 例えばAPI -> CLIENT間のやり取りでもそう、相談しすぎてロジックを把握しすぎていたら QAテストが不要に
- 関数型プログラミングでは、HOC(Higher-Order-Component: 高階コンポーネント(高階関数))という考え方で、
  引数に関数を突っ込むと、必要な機能の関数を出力してくれる機能(Reactでよく使ってましたが、今はFWがよしなにしてくれるHooksが一般的)などは、こういう問題への対応のひとつの解答

# 12.4. コマンド・クエリ分離

## コマンド・クエリ分離（CQS）
メソッドはCQのどちらか一方だけを行うよう設計する考え方
- コマンド（＝変更）
- クエリ（＝問い合わせ）

```typescript
import { useVideo } from "hooks/video"; // useVideo関数をimport

const { setVideoUiNone } = useVideo(); // useVideo関数内のsetVideoUiNoneを取得
useEffect(setVideoUiNone, [setVideoUiNone]);　// setVideoUiNoneを実行
```

# 12.5. 引数 

## 12.5.1 引数は不変にすること

- final修飾子を付与
- JS/TSではCONST

## 12.5.2. フラグ引数は使わない

- boolean判定をしている別の関数を見にいく面倒が => 可読性/生産性の低下
- boolean判定は、関数内で解決する

## 12.5.3. nullを渡さない

- 言語によって `null` の扱いに違いがある
- 未装備状態を表現するには
  - `Equipment.EMPTY`
  - JS/TS の場合では`undefined`

## 12.5.4. 出力引数は使わない

- 引数は入力値として用いるのが基本
- 出力値として用いると => 可読性/生産性の低下

## 12.5.5. 引数は可能な限り少なく

- 大きすぎるパラメーターは、外部オブジェクトを参照するなど

# 12.6. 戻り値
## 12.6.1. 「型」を使って戻り値の意図を表明すること

```java
class Price {
  //省略
  Price add (final Price other) {
    final int added = amount+other.amount;
    return new Price(added); // Price型で返しているのが明瞭
  }
}
```

## 12.6.2. nullを返さない

- 引数にnullを渡さないのと同様に、nullを返さないようにしましょう

## 12.6.3. エラーは戻り値で返さない、例外をスローすること

```typescript
try {
  await new AnyApi(await AuthManager.getInstance().getConfigration())
    .catch(handleApiError(dispatch)); // APIエラーの制御機構がちゃんとしているケース
} catch (error) {
  throw new Error(error);　// errorがthrowされることによって、エラー検知が容易に
  // console.error(error);
  // これだけで済ませがち、あくまでデバッグ用途で
}
```
