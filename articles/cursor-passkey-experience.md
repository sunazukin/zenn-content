---
title: "Cursorでパスキーを動かしてみた"
emoji: "🔑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Cursor", "Passkey", "認証", "セキュリティ", "WebAuthn"]
published: false
---

# Cursorでパスキーを動かしてみた

こんにちは！この記事では、Cursorエディタでパスキー（Passkey）認証を実装して動かしてみた経験を共有します。

## パスキー（Passkey）とは

パスキーは、パスワードに代わる新しい認証方法です。FIDO2とWebAuthnに基づいており、フィッシング耐性があり、より安全で使いやすい認証を提供します。

主な特徴：
- 生体認証（指紋、顔認証など）を利用
- デバイスに保存される秘密鍵を使用
- サーバーにはパスワードが保存されない
- フィッシング攻撃に強い

## Cursorエディタでの実装

Cursorは、AIを活用したコーディング支援機能を持つ次世代のコードエディタです。このエディタを使って、パスキー認証の実装を試みました。

### 環境構築

まず、必要なライブラリをインストールします：

```bash
npm install @simplewebauthn/server @simplewebauthn/browser
```

### バックエンド実装

Node.jsとExpressを使用したバックエンドの基本的な実装例：

```javascript
const express = require('express');
const { generateRegistrationOptions, verifyRegistrationResponse } = require('@simplewebauthn/server');

const app = express();
app.use(express.json());

// パスキー登録のためのチャレンジを生成
app.post('/generate-registration-options', async (req, res) => {
  const options = await generateRegistrationOptions({
    rpName: 'My Application',
    rpID: 'example.com',
    userID: 'user123',
    userName: 'testuser@example.com',
    attestationType: 'none',
    authenticatorSelection: {
      userVerification: 'preferred',
      residentKey: 'required',
    },
  });
  
  // チャレンジをセッションに保存
  req.session.currentChallenge = options.challenge;
  
  res.json(options);
});

// その他の実装...

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### フロントエンド実装

フロントエンドでのパスキー登録処理：

```javascript
import { startRegistration } from '@simplewebauthn/browser';

async function registerPasskey() {
  try {
    // サーバーからオプションを取得
    const optionsResponse = await fetch('/generate-registration-options', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
    });
    const options = await optionsResponse.json();
    
    // ブラウザのWebAuthn APIを使用して登録を開始
    const registrationResponse = await startRegistration(options);
    
    // 結果をサーバーに送信して検証
    const verificationResponse = await fetch('/verify-registration', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(registrationResponse),
    });
    
    const verification = await verificationResponse.json();
    
    if (verification.verified) {
      alert('パスキーの登録に成功しました！');
    } else {
      alert('パスキーの登録に失敗しました');
    }
  } catch (error) {
    console.error('エラー:', error);
    alert('パスキー登録中にエラーが発生しました');
  }
}
```

## 実装中に遭遇した課題

パスキー実装中にいくつかの課題に直面しました：

1. **ブラウザの互換性**: 一部の古いブラウザではWebAuthn APIがサポートされていない
2. **ローカル開発環境での制約**: HTTPSが必要なため、開発環境の設定に工夫が必要
3. **デバッグの難しさ**: 認証プロセスのデバッグが複雑

## 解決策と学んだこと

これらの課題に対する解決策：

1. **ブラウザ互換性チェック**: 実装前にブラウザのサポート状況を確認
2. **ローカル開発用の証明書**: mkcertなどを使用してローカル開発用の有効な証明書を作成
3. **段階的なテスト**: 各ステップを個別にテストして問題を特定

## まとめ

Cursorエディタを使ってパスキー認証を実装することで、最新の認証技術について多くのことを学ぶことができました。パスキーは従来のパスワードに比べて安全性と使いやすさの両面で優れており、今後のウェブアプリケーション開発において重要な役割を果たすでしょう。

次回は、パスキーを実際のプロダクションアプリケーションに統合する方法について詳しく解説する予定です。

## 参考リソース

- [WebAuthn API - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API)
- [SimpleWebAuthn ライブラリ](https://simplewebauthn.dev/)
- [FIDO Alliance - Passkeys](https://fidoalliance.org/passkeys/)
