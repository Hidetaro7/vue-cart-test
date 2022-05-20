# ショッピングカートのUIを実装してください

## 考え方

### emitされたら親側のデータで受ける処理をする

ShoesCardコンポーネントでemitされた商品データを、App.vue内の `addCart`メソッドで `cart` 配列にemitされたアイテムをpushして配列に追加します。

### ShoppingCartコンポーネントをつくる

App.vueの持つ`cart` 配列は常にリアクティブなので、さらに他の商品がemitされれば自動的にデータも増え続けます。

新たにShoppingCart.vueをコンポーネントとしてつくり、App.vueに取り込みます。

ShoppingCartに対してApp.vueから `cart` 配列を渡してあげる必要があります。propsを使って以下のような例で渡します。

```
<ShoppingCart v-if="showCart" :_items="cart" />

```

`:_items="cart"` の部分がそうですね。
（アンダースコアをつけているのは、ShoppingCart内部の `items` と区別するだけの理由です）

### ShoppingCart内の処理

#### 要件

主にこのような機能を持つUIだとします。

- カートに入れられた商品が一覧表示される
- 削除したい商品を削除できる（削除ボタンの設置）

ひとまずこの要件だけ満たすことを目的としてつくります。

まず、propsで受け取ったデータを内部で `items` として管理します。
```
const items = ref(props._items);
```

#### 表示

これだけでカートの一覧表示をするデータは準備できました。あとは表示するだけです。
template側でv-forしているように、`items`のデータをループ処理で表示しましょう。

```
<div class="shopping-item" v-for="(item, index) in items">
```

`v-for="(item, index)` としているのは、第一引数で商品1個ごと取得できますが、第2引数の `index` によって0から始まる個別の番号が取れます。この場合、シューズが12点あるので、0〜11までがそれぞれ取れるわけです。
なぜこれをやるかというと、「カートから削除」をするとき、予め「何番目のシューズ商品を削除する」処理の「何番目」が必要だからです。

ここからはその「何番目を削除する」という処理について考えましょう。

#### カートから削除

```
<button @click="removeItem(index)">カートから削除</button>
```

この `removeItem(index)` にその番号である `index` を指定してます。
対応している `removeItem` メソッドを見てみましょう。

```
const removeItem = (itemId) => {
  items.value.splice(itemId, 1);
};
```

これだけです、 `items` 内の配列の「itemId番目の商品」に対してのみ、配列から削除をしています。
`splice`はJavaScriptネイティブメソッドです、くわしくは
https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
を読みましょう。

`items` はリアクティブなので、配列の数が減るだけで表示側はVueが勝手に減らしてくれます。

#### 合計表示

`subTotal`, `tax`, `totalPrice` それぞれにcomputedで金額が計算されていますね。
この講義でもお伝えしたとおり、methodで処理するよりもcomputedのほうがパフォーマンスとして良い期待が持てるため、今回はcomputedにしました。

### ヘッダ

#### 要件

headerにショッピングアイコンを付け、ShoppingCartコンポーネントの表示、非表示ができるボタンを追加。
ボタンには現在のカート内のアイテム数を表示する。

#### ボタンの処理

ヘッダの右上にカートアイコンがあり、そこの右に数字がありますね。
App.vueを見ると、このように書いています。

```
<button @click="showCart = !showCart"><CartIcon />{{ cart.length }}</button>
```

アイコン画像は今回SVGデータにして、別のコンポーネント　`<CartIcon />`　にしました、これには何の処理も書かれていません。ただの画像表示用です。
着目すべきはボタンに書かれている `@click="showCart = !showCart"` の部分です。

何をしているのかというと、script内にrefで定義された `showCart` データのtrue か false のどちらかにひっくり返す処理をしています。
つまり、 `showCart` がfalseの時にクリックされたらtrueになり、またクリックされたらfalseになる、値がひっくり返るわけです。

#### ボタンクリックでShoppingCartの表示・非表示

その `showCart` のtrue/falseは先程見た、このShoppingCartコンポーネントの表示・非表示をこのように処理しています。

```
<ShoppingCart v-if="showCart" :_items="cart" />
```

`v-if`がtrueなら表示、falseなら非表示、となります。
これで開閉ができるようになりました。
