---
layout: layouts/doc-post.njk
title: アトリビューションレポート：要約レポート
subhead: |2

  明らかにすることなく、ユーザー全体で集計された広告コンバージョンを測定する
  個々のデータ。以前は集計レポートと呼ばれていました。
description: |2

  明らかにすることなく、ユーザー全体で集計された広告コンバージョンを測定する
  個々のデータ。以前は集計レポートと呼ばれていました。
date: '2022-02-16'
updated: '2022-06-06'
authors:
  - alexandrawhite
---

## 実施状況

- 最初の提案（ [クライアント側](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATE.md)、 [サーバー側](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATION_SERVICE_TEE.md)）とディスカッションの段階
- アトリビューションレポート[API仕様](https://wicg.github.io/conversion-measurement-api/)
- [Chromeプラットフォームのステータス](https://chromestatus.com/feature/5762222527610880)

## アトリビューションレポートの概要レポートとは何ですか？

Attribution Reporting APIを使用すると、広告のクリックまたは表示が、販売やサインアップなどの広告主サイトでのコンバージョンにつながる時期を測定できます。 APIは、サイト全体で個々のユーザーを識別するために使用できるサードパーティのCookieやメカニズムに依存していません。

このAPIは、2種類のレポートを提供します。[イベントレベルのレポート](/docs/privacy-sandbox/attribution-reporting/#event-level-reports)はChromeでのテストにすでに利用可能であり、特定の広告のクリックまたはビューをより詳細でないコンバージョンデータに関連付けます。ブラウザは、サイト間のID接続を防ぐために、アドテック企業へのレポートの送信を数日間遅らせます。

*要約レポート*（以前は集計レポートと呼ばれていました）は、ユーザーのグループ用にコンパイルされているため、個人に関連付けることはできません。サマリーレポートは、購入額やカートの内容などの詳細なコンバージョンデータを提供し、クリックしてデータを表示する柔軟性を備えています。これらのレポートは、イベントレベルのレポートと同じ程度に遅延することはありません。

まだ読んでいない場合は、この記事の残りの部分を読む前に[、アトリビューションレポートの一般的な概要](/docs/privacy-sandbox/attribution-reporting-introduction/)を読むことをお勧めします。

## なぜ要約レポートが必要なのですか？

{% Img class="float-right", src="image/VbsHyyQopiec0718rMq2kTE1hke2/mvk4I92OFInhVvwZEbiu.jpg", alt="A collection of users that take the same action in their browser (such as buying a pair of shoes), can have their conversions aggregated.", width="305", height="220" %}

現在、広告コンバージョンの測定は、多くの場合、 [サードパーティのCookie](https://developer.mozilla.org/docs/Web/HTTP/Cookies#Third-party_cookies)に依存しています。ブラウザはサードパーティのCookieへのアクセスを制限しており、サイト間でユーザーを追跡することをより困難にし、ユーザーのプライバシーを向上させています。 Attribution Reporting APIを使用すると、アドテクはサードパーティのCookieを使用せずに、プライバシーを保護する方法で会話を測定できます。

単一のイベント（クリックやビューなど）を[粗いデータ](/docs/privacy-sandbox/glossary/#coarse-data)に関連付けるAttribution Reporting APIのイベントレベルのレポートとは対照的に、サマリーレポートは、詳細なコンバージョンデータ（ユーザーが購入した特定の製品）。サマリーレポートは、詳細なコンバージョンデータ（ユーザーが購入した特定の製品など）に添付された集計データ（コンバージョンしたユーザーの数など）を提供します。

{% Aside 'key-term' %} Adtechsは、ブラウザイベントを処理[する集約サービスを実行し](#aggregation-service)ます。イベントで報告されるほとんどのデータポイントに[ノイズが追加さ](https://en.wikipedia.org/wiki/Additive_noise_mechanisms)れるため、要約レポートで単一の個人のデータを検出することはできません。

*集計データ*は、コンバージョンを達成したユーザー数など、コンバージョンの測定に関連するノイズ値です。 {% endAside %}

サードパーティのCookieとは異なり、Attribution Reporting APIのレポートタイプでは、エンティティ（アドテック、バイヤー、パブリッシャーなど）が複数のサイトでのユーザーの閲覧行動を「確認」することはできませんが、広告の測定は可能です。コンバージョン。

## ユーザーデータはどのようにキャプチャおよび集約されますか？

{% Aside %}このAPIは進行中の作業であり、エコシステムのフィードバックと入力に応じて、時間の経過とともに進化します。

AttributionReportingAPIがサポートするすべての機能は提案です。これらの提案はそれぞれ、初期のブラウザ実装の準備ができているものを含め、議論とフィードバックに開かれています。

このAPIは、オープンでインキュベートおよび開発されています。ディスカッションへの[参加を検討し](/docs/privacy-sandbox/attribution-reporting-introduction/#participate)てください。 {% endAside %}

Attribution Reporting APIを使用すると、サイト間での個々のユーザーの詳細なアクティビティ、および場合によってはサイト間でのユーザーのIDが、デバイス上のユーザーのブラウザーに対して非公開になります。このデータは集約可能なレポートに収集でき、各レポートは暗号化されて、さまざまな関係者が基になるデータにアクセスできないようにします。

{% Aside 'key-term' %}*集計可能なレポート*は、個々のユーザーのブラウザから収集されたレポートです。これらは、アドテックプロバイダーによって定義されたクロスサイトユーザーの行動と[コンバージョン](/docs/privacy-sandbox/glossary/#conversion)について詳しく説明しています。 {% endAside %}

要約レポートを作成するために提案されたプロセスは次のとおりです。

1. 集約可能なレポートは、アドテックプロバイダーによって運営されている*レポートオリジン*に送信されます。
    - たとえば、これらのレポートには、場所の詳細、クリック数、コンバージョンの価値（購入価格など）、またはアドテックプロバイダーによって定義されたその他の指標が含まれる場合があります。レポートは暗号化されているため、アドテックプロバイダーは個々のレポートのコンテンツを表示したりアクセスしたりすることはできません。
2. アドテックのレポートオリジンが集約可能なレポートを受信すると、アドテックはレポートを[*集約サービス*](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATION_SERVICE_TEE.md)に送信します。
    - 最初の実装では、[アグリゲーションサービス](#aggregation-service)は、クラウドでホストされているTrusted Execution Environment（TEE）を備えたアドテックプロバイダーによって運用されています。*コーディネーター*は、検証されたエンティティのみが復号化キーにアクセスできるようにし、他の仲介者（アドテック、クラウドプロバイダー、またはその他の関係者）が集約プロセスの外部で機密データにアクセスして復号化できないようにします。
3. 集約サービスは、復号化されたデータを結合し、*要約レポート*をアドテックプロバイダーに出力します。
    - 要約レポートには、結合されたデータの要約が含まれます。アドテックプロバイダーは、要約レポートを読んで使用できます。

<figure>{% Img    class="screehshot",    src="image/VbsHyyQopiec0718rMq2kTE1hke2/hoRtQVV2b2MCXIKi1okK.jpg",     alt="The proposed process to create a summary report is represented by encrypted reports sent to a collector server. The collector server sends the data to a secure aggregation service, which has a key to decrypt the data and create the summary report. The report is then sent back to the adtech provider.",    width="800", height="168" %}<figcaption>完全なシーケンス図については、<a href="/docs/privacy-sandbox/attribution-reporting-introduction/#aggregate-reports">アトリビューションレポートの概要を</a>参照してください。</figcaption></figure>

個々のレポートにはサイト間のユーザー行動情報が含まれている可能性があるため、集計サービスはこの情報を非公開として扱う必要があります。このサービスは、他のエンティティが暗号化されていない個々のアトリビューションレポートにアクセスできないようにします。さらに、サービス自体がプライバシーを侵害するアクションを実行してはなりません。

アグリゲーションサービスが実際に安全であることを保証するには、サービスには、消費者監査によって検証可能な技術的および組織的な保護手段が必要です。これらのセーフガードは、次の点で意味があります。

- 個々のデータを知ることができる個々のユーザーは、単一のエンティティではなく、集合的にのみアクセスできます
- 集計プロセスが有効なデータを使用し、適切に監視できることを確認できるAdtechs

### 集約サービスの提案{：＃aggregation-service}

{% Aside 'key-term' %}*信頼できる実行環境*は、コンピューターのハードウェアとソフトウェアの特別な構成であり、外部の関係者がコンピューターで実行されているソフトウェアの正確なバージョンを確認できるようにします。 TEEを使用すると、外部の関係者は、ソフトウェアがソフトウェアの製造元が主張していることを正確に実行していることを確認できます。多かれ少なかれ何もしません。 {% endAside %}

[最初の提案](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATION_SERVICE_TEE.md)では、各アドテックプロバイダーに、必要なセキュリティ機能をサポートするクラウドサービスにデプロイされたTrusted Execution Environment（TEE）で、アグリゲーションサービスの独自のインスタンスを運用するように依頼しています。

{% Aside %}

アグリゲーションサービスを使用して、アトリビューションレポート[のオリジントライアルに参加](/docs/privacy-sandbox/attribution-reporting-experiment/)できます。アグリゲーションサービスは、ローカルテストまたはAmazonWebServicesが提供するTEEでのテストを最初にサポートします。 [アグリゲーションサービスの詳細なセットアップ手順を](https://github.com/google/trusted-execution-aggregation-service/#set-up-aggregation-service-for-aggregatable-reports)お読みください。

将来的には、アグリゲーションサービスのセキュリティ要件を満たす他のクラウドプロバイダーのサポートを追加する予定です。 {% endAside %}

TEEのコードは、生のレポートにアクセスできる集約サービス内の唯一の場所です。このコードは、セキュリティ研究者、プライバシー擁護者、およびアドテックによって監査可能になります。 TEEが正確に承認されたソフトウェアを実行しており、データが保護されたままであることを確認するために、コーディネーターは認証を実行します。

コーディネーターにはいくつかの責任があります。

- 許可されたバイナリイメージのリストを維持します。これらのイメージは、Googleが定期的にリリースするアグリゲーションサービスソフトウェアビルドの[暗号化ハッシュ](https://en.wikipedia.org/wiki/Cryptographic_hash_function)です。これは再現可能であるため、どのパーティもイメージがアグリゲーションサービスビルドと同一であることを確認できます。
- 鍵管理システムを運用します。ユーザーのデバイス上のChromeで集計可能なレポートを暗号化するには、暗号化キーが必要です。復号化キーは、集約サービスコードがバイナリイメージと一致することを証明するために必要です。
- 再利用により個人識別情報（PII）が明らかになる可能性があるため、集計可能なレポートを追跡して、要約レポートの集計で再利用されないようにします。

オリジントライアルでアグリゲーションサービスのテストを利用できるようにするために、Googleがコーディネーターの役割を果たします。長期的には、この役割を共有できる1つ以上の独立したエンティティを特定するために取り組んでいます。

### どのような情報が取得されますか？

サマリーレポートは、詳細な広告側およびコンバージョンデータとともに集計データの組み合わせを提供します。

たとえば、アドテックプロバイダーは、 `news.example`で広告キャンペーンを実行します。この場合、コンバージョンは、ユーザーが靴の広告をクリックして、 `shoes.example`で靴の購入を完了したことを表します。アドテックは、ID `1234567`でこの広告キャンペーンの概要レポートを受け取ります。これは**、2022年1月12日**にshoes.exampleで**518**回のコンバージョンがあり、合計費用が**38,174ドル**であると述べています。コンバージョンの**60％**は、製品SKU `9872`で青い靴を購入したユーザーからのものであり、 **40％**は、製品`2643`で黄色のサンダルを購入したユーザーでした。キャンペーンIDは詳細な広告側データであり、商品SKUは詳細なコンバージョンデータです。コンバージョン数と総費用は集計データです。

コンバージョンは広告主またはアドテック企業によって定義され、広告キャンペーンごとに異なる場合があります。 1つのキャンペーンでは、広告されたアイテムを購入したユーザーが続いた広告クリックの数を測定できます。別のキャンペーンでは、広告主のサイトへのアクセスにつながった広告ビューの数を測定できます。

### 集約前にブラウザデータはどのようにキャプチャされますか？

要約レポートは個人のグループからのデータで構成されているため、1人の個人のブラウザアクションから始めましょう。

1. ユーザーがサイト運営者のサイトにアクセスし、広告を表示またはクリックします。これは、アトリビューションソースイベントとも呼ばれます。
2. 数分または数日後、ユーザーはコンバージョンを達成します。これは、アトリビューショントリガーイベントとも呼ばれます。たとえば、コンバージョンは商品の購入として定義できます。 {% Img class="screehshot", src="image/VbsHyyQopiec0718rMq2kTE1hke2/oHfdWrzpCl2uAYcXQfU8.jpg", alt="", width="700", height="209" %}
3. ブラウザソフトウェアは、広告のクリックまたは表示をコンバージョンイベントと照合します。この一致に基づいて、ブラウザーは、アドテックプロバイダーによって作成された特定のロジックを使用して集約可能なレポートを作成します。
4. ブラウザはこのデータを暗号化し、少し遅れて、収集のためにアドテックサーバーに送信します。 adtechサーバーは、これらの集約可能なレポートから集約された洞察にアクセスするために、集約サービスに依存する必要があります。 {% Img class="screehshot", src="image/VbsHyyQopiec0718rMq2kTE1hke2/gGKktJZoaKXTX4YG9udv.jpg", alt="", width="564", height="209" %}

## アドテックプロバイダーはどのように要約レポートを作成しますか？

アドテックプロバイダーが要約レポートを取得するには、次の手順を実行する必要があります。

1. アドテックプロバイダーは、個々のユーザーのブラウザーから集計可能なレポートを収集します。 {% Aside %}アドテックプロバイダーは、アグリゲーションサービスでこれらのレポートのみを復号化できます。 {% endAside %}
2. アドテックプロバイダーは、集約可能なレポートをバッチ処理し、そのバッチを集約サービスに送信します。
3. 集約サービスは、データを集約するようにワーカーをスケジュールします。 {% Aside %}ワーカーが集約する前に、コーディネーターからの証明が必要です。ワーカーが認証に合格すると、復号化キーが提供されます。 {% endAside %}
4. 集約ワーカーは、ノイズのあるデータ（データのプライバシーメカニズム）とともに、集約可能なレポートからデータを復号化して集約します。
5. 集約サービスは、要約レポートをアドテックプロバイダーに返します。

アドテックは、要約レポートを使用して入札を通知し、自社の顧客にレポートを提供できます。 [JSONでエンコードされたスキーム](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATE.md#aggregate-attribution-reports)は、要約レポート用に提案された形式です。

## 参加してフィードバックを共有する

- GitHub： [クライアント側の提案](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATE.md)と[集約サービスの提案](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATION_SERVICE_TEE.md)を読み、質問をし、フィードバックを提案します。
- 開発者サポート：[プライバシーサンドボックス開発者サポートリポジトリ](https://github.com/GoogleChromeLabs/privacy-sandbox-dev-support)で質問をしたり、ディスカッションに参加したりします。

## 詳細をご覧ください

- [アトリビューションレポートの概要（コンバージョン測定）](/docs/privacy-sandbox/attribution-reporting-introduction/)を読む
- [アグリゲーションサービスの説明](https://github.com/WICG/conversion-measurement-api/blob/main/AGGREGATION_SERVICE_TEE.md)と[詳細なセットアップ手順](https://github.com/google/trusted-execution-aggregation-service/)をお読みください。
- [プライバシーサンドボックスを掘り下げる](https://web.dev/digging-into-the-privacy-sandbox)
