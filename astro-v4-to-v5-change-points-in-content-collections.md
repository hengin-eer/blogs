---
cover: ./no-cover.png
title: Astro v4からv5へのContent Collectionsアップデートのメモ
author: timdaik
updatedAt: 2026-04-01
tags:
  - Tech
  - 駄文
---
[Git Submodulesを利用してAstroサイトの各コンテンツ管理をObsidianで行う](./cm-with-astro-obsidian-git-submodules)ではこのブログにおける記事コンテンツの管理をObsidianとGit Submodulesを利用する方法にアップデートした内容をまとめた。

ところでGit Submodulesで管理するために、元々`src/content/blog`, `src/content/work`といったディレクトリ構造だったのを、`src/blogs`, `src/works`というように`src`直下に配置したくなった。
> というより、それぞれをリポジトリとして管理するにあたって、GitHub上で良い感じのリポジトリ名にしたいな～と考えていたところ、「今のディレクトリ構造だとページ表示のパスが`/content`以下のディレクトリ名にそのまま使われてしまい不格好になってしまう！！」という勘違いを起こした。
> 
> 例えば`src/content/portfolio-blogs`とすると、SSGで`/blog`ではなく`portfolio-blogs`というページ名になってしまうではないか！と錯乱したわけだ。
> （実際はコンテンツの置いているディレクトリ名は無関係でこれらを呼び出して表示するページ側の問題）
> 
> そこでContent Collectionsについて調べ出したところアップデートの旨を知ったのでついでに更新しようと決めたのだった。

このような勘違いから始まったアップデートによって、運よく仕様とニーズがハマったわけだ。

あとはただただバージョンアップのメモ
- 定義ファイルは`/src/content/config.ts`から`/src/content.config.ts`に移動
- コンテンツファイルの位置を指定するには`glob()`を利用する

```typescript
import { defineCollection, z } from "astro:content";
import { glob } from "astro/loaders";

const blogs = defineCollection({
	loader: glob({ pattern: "**/*.md", base: "./src/blogs" }),
	schema: ({ image }) =>
		z.object({
			cover: image(),
			title: z.string(),
			author: z.string(),
			updatedAt: z.string(),
			tag: z.array(
				z.enum([
					"ニュース",
					"日常",
					"ポエム",
					"読書",
					"振り返り",
					"年の総括",
					"イベント",
					"Tech",
					"教養",
					"高専",
					"NUT",
				]),
			),
			draft: z.boolean().default(false),
		}),
});
```

- コンテンツのユニークIDが`slug`から`id`に変更された
	- Markdownのフロントマターに`slug`を加えるなどしてカスタムIDを生成することも可能

## 参考
- [Astro v5以降のContent collections | 前編 Content collectionsの変更点とローダーの導入](https://www.codegrid.net/articles/2025-astro-content-collections-1/#toc-1)
- [Content collections | Docs](https://docs.astro.build/ja/guides/content-collections/#defining-custom-ids)
