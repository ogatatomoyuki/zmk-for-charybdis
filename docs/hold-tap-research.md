# Hold-Tap + Trackball 設定リサーチ (2025-02-14)

## 問題

PMW3610トラックボールのイベントがZMKのhold-tap判定に干渉し、タップが効かない。
ZMK Issue #3030: https://github.com/zmkfirmware/zmk/issues/3030

## 参考設定

### 280Zo (Charybdis Wireless Mini)

https://github.com/280Zo/charybdis-wireless-mini-zmk-firmware

```dts
ht_left: left_side_homerow_mod {
    compatible = "zmk,behavior-hold-tap";
    #binding-cells = <2>;
    flavor = "balanced";
    tapping-term-ms = <280>;
    quick-tap-ms = <160>;
    require-prior-idle-ms = <65>;   // 左手
    retro-tap;
    bindings = <&kp>, <&kp>;
    hold-trigger-key-positions = <KEYS_R THUMBS>;
};

ht_right: right_side_homerow_mod {
    compatible = "zmk,behavior-hold-tap";
    #binding-cells = <2>;
    flavor = "balanced";
    tapping-term-ms = <280>;
    quick-tap-ms = <160>;
    require-prior-idle-ms = <55>;   // 右手（トラックボール側、より短い）
    retro-tap;
    bindings = <&kp>, <&kp>;
    hold-trigger-key-positions = <KEYS_L THUMBS>;
};
```

特徴:
- balanced + 長いtapping-term (280ms)
- require-prior-idle-ms が非常に短い (55-65ms) → 通常タイピング中はほぼ即tap
- retro-tap あり
- hold-trigger-on-release なし
- トラックボール操作用に別途 hold-while-undecided を使った behavior あり:
  - mo_clk: hold-preferred, tapping-term=40, hold-while-undecided (トラックボール+クリック用)
  - mo_kp: hold-preferred, tapping-term=40, hold-while-undecided

### nophramel (Charybdis)

https://github.com/nophramel/charybdis_zmk

```dts
hml: hml {
    compatible = "zmk,behavior-hold-tap";
    #binding-cells = <2>;
    tapping-term-ms = <190>;
    quick-tap-ms = <175>;
    flavor = "balanced";
    hold-trigger-key-positions = <右手 + 親指>;
    hold-trigger-on-release;
    bindings = <&kp>, <&kp>;
};

hmr: hmr {
    compatible = "zmk,behavior-hold-tap";
    #binding-cells = <2>;
    tapping-term-ms = <190>;
    quick-tap-ms = <175>;
    flavor = "balanced";
    hold-trigger-key-positions = <左手 + 親指>;
    hold-trigger-on-release;
    bindings = <&kp>, <&kp>;
};
```

特徴:
- balanced + 短めのtapping-term (190ms)
- require-prior-idle-ms なし
- retro-tap なし
- hold-trigger-on-release あり

### urob (ZMK HRM のリファレンス)

https://github.com/urob/zmk-config

```dts
flavor = "balanced";
tapping-term-ms = <280>;
quick-tap-ms = <175>;
require-prior-idle-ms = <150>;
hold-trigger-key-positions = <対側の手 + 親指>;
hold-trigger-on-release;
```

## 設定比較表

| 項目 | 280Zo | nophramel | urob |
|------|-------|-----------|------|
| flavor | balanced | balanced | balanced |
| tapping-term-ms | 280 | 190 | 280 |
| quick-tap-ms | 160 | 175 | 175 |
| require-prior-idle-ms | 55-65 | なし | 150 |
| retro-tap | あり | なし | なし |
| hold-trigger-key-positions | クロスハンド+親指 | クロスハンド+親指 | クロスハンド+親指 |
| hold-trigger-on-release | なし | あり | あり |

## 共通点

1. 全員 `balanced` flavor を使用
2. 全員 `hold-trigger-key-positions` でクロスハンド設定
3. tapping-term は 190-280ms（150ms以下の設定者はいない）

## hold-while-undecided について

280Zoはトラックボール操作（modifier + trackball/click）用に `hold-while-undecided` を使用。
これは「キーを押した瞬間にmodifierを有効化し、tapと判定されたら取り消す」動作。
タップ問題の解決ではなく、modifier+マウス操作の問題向け。

## balanced flavor の動作

- 対側キーが押されて**離された**時にholdが発動
- tapping-term内に対側キー押下がなければ、tapping-term後にholdになる
  （retro-tapがあれば、他キーなしならtapになる）
- require-prior-idle-ms以内に他キーがあった場合は即tap
- hold-trigger-key-positionsにないキー（トラックボール含む）はhold判定に影響しない
