---
title: "Mozilla Hubs: カメラを使ってみる。"
emoji: "🦝"
type: "tech"
topics: [MozillaHubs, Blender, VR]
published: true
created: "2022-03-04"
---

Mozilla Hubsで、カメラや鏡の様なことができるらしいので試してみる。

仕組みとしては、カメラオブジェクトで切り取った画像を、別のオブジェクトに投影させる感じ。

## blenderでの作成方法

- blenderの基本操作は理解しているものとする。
- Y軸マイナス方向を手前にすると楽。
    + ギズモのYマイナスを押すとさらに楽。
- カメラを追加して選択する。
    + Object Properties → Hubs → Add Component → Video Texture Source
    + パラメータは2つ
        * Resolution: デフォルト値は 1280 x 720 (16:9)
        * 後で追加するスクリーンのサイズに Resolutionをあわせる。 
        * Fps: デフォルト値は 15
    + Output Properties → Resolution
        * 後で追加するスクリーンのサイズに Resolutionをあわせる。 
    + カメラを示すために適当なメッシュを追加する。
        * なくてもいいけど、空間のどこにカメラがあるかわからなくなる。
- スクリーンになるオブジェクト(A)を追加して面を選択する。
    + Materialがなければ追加する。
    + Material Properties → Hubs → Add Component → Video Texture Target
    + Target Base Color Map
        * チェックすると、AのBase Color Mapをカメラのテクスチャが上書きする。
    + Target Emissive Map
        * チェックすると、AのEmissionをカメラのテクスチャが上書きする。
    + Sourceとして使いたいカメラを選択する。
    + UV Editモードで Imageが Aの意図した範囲に収まるように合わせる。
        * Plainだと最初から収まっている。
        * UVからProject from View (Bounds)を選択すると簡単にフィットする。
- Aを選択してShadingモードにする。
    + Image Textureを追加して BSDFにつなぐ。
        * Image Textureを追加しないと部屋を作れなくなる。
        * Image自体はカメラからのテクスチャで上書きされるため何でもよい。
        * サイズは小さくてよい。e.g. 32x32
        * Color Gridを使うと投影具合が分かるので便利。縦縞で左に赤がくる。
        * Color Gridの場合、サイズが16x16だと縞模様が十分表示できない。
    + Emissionを追加してMaterial OutputのSurfaceにつなぐ。
        * Image Textureを追加して Emissionにつなぐ。
        * Emissionはなくてもよさそう。
        * ただし、無いと暗くなるのであった方がよい。
        + Add Shaderノードを使って両者をつなぐ。
- glTF形式でエクスポートする。
    + File → Export → glTF
    + glTF Binary Format
    + Includeの Camerasにチェックを入れる。
    + Extensionsの Hubs Componentsにチェックを入れる。

## ShadingとTarget Overrideの関係

ターゲットマテリアルのShadingと Overrideの組み合わせで効果を調べてみた。

![](/images/mozillahubs-camera-object-001.png)

結局、Emissionを使わないとスクリーンが暗くなるので使ったほうがいい。
そしてEmissionの上書きにチェックをしないとEmissionから入ってきたBase Mapが上書きされないのでチェックするのがいい。

まとめると、
- ShadingにTexture ImageとEmissionを追加する。
- Video Texture TargetのTarget Base Color MapとTarget Emissive Mapにチェックを入れる。

## Trouble Shooting: カメラ入りの部屋を作ろうとするとエラーになる。その１

カメラとスクリーンが入った部屋を作ると、Loading Objectのままグルグルが止まらなくなる。

Firefoxのコンソールを見ると以下のエラーが出ていた。

```
TypeError: e.renderTarget is undefined
video-texture-target.js:144
```

Chromeでは以下のエラーが出る。

```
Uncaught (in promise) TypeError: Cannot read properties of undefined (reading 'texture')
```

問題の箇所

```js
      if (this.data.srcEl) {
        const videoTextureSource = this.data.srcEl.components["video-texture-source"];
        const texture = videoTextureSource.renderTarget.texture;   // <-- ここ
        this.applyTexture(texture);
```

デバッガで追うと、 `components["video-texture-source"]`の renderTargetが定義されていない。

というわけで、Blenderで作る時に何か足りていないと予想。

結局、Blenderから glTF形式にエクスポートする時に、Cameraにチェックが入っていないのが原因と分かる。

## Trouble Shooting: カメラ入りの部屋を作ろうとするとエラーになる。その２

カメラとスクリーンが入った部屋を作ると、Loading Objectのままグルグルが止まらなくなる。

Firefoxのコンソール

```
Uncaught (in promise) TypeError: t.map is null
update video-texture-target.js:148
```

video-texture-target.js:148
```
      if (this.data.srcEl) {
        const videoTextureSource = this.data.srcEl.components["video-texture-source"];
        const texture = videoTextureSource.renderTarget.texture;
        this.applyTexture(texture);

        // Bit of a hack here to only update the renderTarget when the screens are in view
        material.map.isVideoTexture = true; // <-- ここ
```

Chromeのコンソール

```
Uncaught (in promise) TypeError: Cannot set properties of null (setting 'isVideoTexture')
    at n.update (hub-39c57ee146255dd40379.js:1:261240)
```

キャッシュが悪さをしているかも？
部屋を削除して作り直すとエラーがでなくなった。
