---
title: Cursor AIをしっかり活用しよう
tags:
  - AI
  - cursor
private: false
updated_at: '2025-01-24T13:29:15+09:00'
id: f40851bbd5969deb763b
organization_url_name: null
slide: false
ignorePublish: false
---

CursorAIを最大限に活用できていますか？Microsoftのエンジニア、Zohaib Raufさんは、CursorAIとLLMを活用することで、長年先送りにしていた多くのサイドプロジェクトを完成させることができたそうです。彼の**CursorAIを使ってサイドプロジェクトを素早く完成させるコツ**を3つのステップにまとめてみました。

## 目次

- [目次](#目次)
- [仕様を具体化する](#仕様を具体化する)
- [初期コードを生成する](#初期コードを生成する)
- [小さな単位に分割する](#小さな単位に分割する)
- [🍯 その他の便利なヒント](#-その他の便利なヒント)

## 仕様を具体化する

まず、GPTを活用して作りたいアプリケーションの仕様を具体化します。十分な詳細が集まったと判断したら、**「他の開発者やAIがこのアプリを作れるほど詳細な技術仕様書を作成してください」**とリクエストしてみましょう。最終仕様をまとめたら、それを常に参照できるように**SPEC.mdファイルを作成して保管（重要！）**しましょう。

<center><b>👇 プロンプト例</b></center>

「他の人やAIがこの文書を見てウェブサイトを作れるように、markdown形式の仕様書を作成してください。プロジェクトの詳細、UX/UI、各機能に必要な要素を必ず含めてください。技術はTypeScript、React、TailwindCSSを中心に使用します。」

![spec.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3539939/ab480b87-6d84-1d32-a209-caab8097e462.jpeg)

<center>🔼 SPEC.mdファイルを先に作成しましょう</center>

## 初期コードを生成する

Viteなどでプロジェクトをセットアップしたら、先ほど作成したSPEC.mdファイルをプロジェクトフォルダに配置します。その後、Cursor IDEのComposer > Select Agentに移動し、**SPEC.mdをコンテキストとして追加してコードの実装を依頼**します。これにより、わずか数分で基本的な機能を備えた土台を作ることができます。

## 小さな単位に分割する

次は詳細な機能の実装に移ります。ここでよくある間違いは、一度に多くの実装を依頼することです。複数のタスクを同時に依頼すると、エラーが発生する確率が高くなり、修正にも時間がかかってしまいます。そのため、**機能をできるだけ小さな単位に分割し、一つずつ着実に実装することをお勧めします**。また、既存のコードやドキュメントへのリンクをコンテキストとして提供できるので、これを積極的に活用してAIの幻覚（hallucination）を防ぐことが重要です。

![cursorrules.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3539939/bb9b8f4b-8595-2268-e69b-c51c6849bb6a.jpeg)

<center>🔼 .cursorrules を積極的に活用しましょう</center>

## 🍯 その他の便利なヒント

- 必要に応じて複数のLLMを使い分けてみましょう。Zohaibさんは、機能の初期ドラフト作成にはo1を、細かい反復作業にはClaude-3.5-sonnetを活用しているそうです。
- [vo.dev](https://v0.dev/?utm_source=Nomad+Academy&utm_campaign=a8b6833d84-EMAIL_CAMPAIGN_2025_01_24&utm_medium=email&utm_term=0_4313d957c9-7ac123137e-160968050)のようなツールを使用すると、UXの実装がより簡単になります。
- SPEC.mdやウェブサイトのドキュメントなど、プロジェクトに関連するマークダウンファイルは、コンテキストとして追加できるように必ず保存しておきましょう。
- プロジェクトディレクトリに.cursorrulesファイルを作成し、プロジェクトを望ましい方向へ導きましょう。特定の技術を中心とした開発を依頼したり、不要な技術を除外したりすることができます。
- コード全体を理解した状態で作業を進めましょう。AIが生成したコードの内容を把握していないと、デバッグが困難な状況に陥る可能性があります。

<br>

👉 原文は[こちら](https://zohaib.me/using-llms-and-cursor-for-finishing-projects-productivity/?utm_source=Nomad+Academy&utm_campaign=a8b6833d84-EMAIL_CAMPAIGN_2025_01_24&utm_medium=email&utm_term=0_4313d957c9-7ac123137e-160968050)でご確認いただけます。
