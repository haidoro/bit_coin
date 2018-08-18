# BitCoin価格表示

## API
APIを使うには公式サイトに行き  
[CoinDesk Bitcoin Price Index API](https://www.coindesk.com/api/)  
ページに記述してあるURLを使用します。`https://api.coindesk.com/v1/bpi/currentprice.json`

URLをコピーしてブラウザで確認するとjsonデータが取れていることが確認できます。

Chromeの拡張JSONViewを使うと見やすく表示されます。

## データプロパティを作成
HTMLファイルとJavaScriptファイルの基本構文を記述してデータプロパティの準備をします。

HTMLファイル
```
<h1>Bit Coin 価格</h1>
	<div id="app">

	</div>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.js"></script>
	<script src="js/app.js"></script>
```

dataプロパティの中身はとりあえずnullの状態にしておきます。
jsファイル
```
var app =new Vue({
	el: '#app',
	data: {
		bpi:null;
	}
})
```

## axiosの活用
axiosを使ってAPIを取得します。
データ取得のタイミングはmountedで指定します。こうすることでappの内容がHTMLにマウントされた時点で関数が実行されます。

[axiosのリンク先](https://www.jsdelivr.com/package/npm/axios)
こちらから必要なバージョンを見つけてクリックすると詳細が出ます。axios.min.jsファイルを選択してCopy HTMLでコピーし、HTMLファイルにペーストします。

HTML
```
<h1>Bit Coin 価格</h1>
	<div id="app">

	</div>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.js"></script>
	<script src="https://cdn.jsdelivr.net/npm/axios@0.18.0/dist/axios.min.js"></script>
	<script src="js/app.js"></script>
```

axiosの使い方は`axios.get().then()`を使います。axios は Promise オブジェクトを返すので .done()、.catch()、.then() などで結果を受け取ります。get()の引数にはAPIのURLを入れます。

jsファイル データの取得をcosoleで確認
```
var app =new Vue({
	el: '#app',
	data: {
		bpi:null
	},
	mounted: function(){
		axios.get('https://api.coindesk.com/v1/bpi/currentprice.json')
		.then(function(response){
			console.log(response)
			console.log(response.data.bpi.USD.rate_float)
		})
	}
})
```

## エラー処理

エラー処理はcatch()を使います。

```
.catch(function(error){
			console.log(error)
})
```

## 表示
表示はv-forディレクティブとマスタッシュ構文`{{}}`を使います。

HTML
```
<h1>Bit Coin 価格</h1>
	<div id="app">
		<ul>
			<li v-for="(rate,currency) in bpi">{{currency}}:{{rate.rate_float}}</li>
		</ul>
	</div>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.js"></script>
	<script src="https://cdn.jsdelivr.net/npm/axios@0.18.0/dist/axios.min.js"></script>
	<script src="js/app.js"></script>
```

jsファイル`.bind(this)`が表示のポイントです。
```
var app =new Vue({
	el: '#app',
	data: {
		bpi:null
	},
	mounted: function(){
		axios.get('https://api.coindesk.com/v1/bpi/currentprice.json')
		.then(function(response){
			this.bpi = response.data.bpi
		}.bind(this))
		.catch(function(error){
			console.log(error)
		})
	}
})
```

## チラつき防止
チラつき防止は`v-cloak`ディレクティブを使います。これで囲まれた部分でチラつきを防止することができます。CSSで`display:none`にしておきます。

## ローディング表示
`loading:true`はtrueにしておいてfinally関数を使うことでローディング終了のタイミングを感知します。その際に`loading:true`はfalseに切り替える仕組みです。bind()でその値をHTML側に渡します。

```
finally(function(){
	this.loading = false
}.bind(this))
```
`v-if="loading"`ディレクティブを使い、ローディング中は「Loading」と表示し、ローディング終了後はv-elseディレクティブに移る仕掛けです。













