# DNS公開記録

最終更新：2026-10-06

出典：Cloudflare DNS over HTTPS（`https://cloudflare-dns.com/dns-query`）で取得した公開レコードのみ。ConoHa管理画面の値との照合は未実施。

秘密情報（認証用TXTの全文を増やす等）は書かない。

## sauna-cospa.com

| 種別 | 値 | TTL |
|------|------|-----|
| A | 157.120.209.148 | 3600 |
| AAAA | なし | — |
| CNAME | なし | — |
| MX | 10 mail1004.conoha.ne.jp. | 3600 |
| NS | ns-a1.conoha.io. / ns-a2.conoha.io. / ns-a3.conoha.io. | 3600 |
| TXT | `v=spf1 include:_spf.conoha.ne.jp ~all` | 3600 |
| SOA | ns-a1.conoha.io. postmaster.sauna-cospa.com. … | 3600 |

## www.sauna-cospa.com

| 種別 | 値 |
|------|------|
| A | 157.120.209.148 |
| CNAME | なし（A直接） |

## 読み取り

- Webもメールも現状ConoHa配下。
- メール用MXがConoHaのため、独自ドメイン切替時はAレコードだけ変え、MX/SPFは残す必要がある。
- メールアドレス・転送設定・認証用TXTの全店舗は、ConoHa再ログイン後に `dns-mail/` へ本番記録する。

## 2026-10-03 切替前の再確認と戻し方（Claude）

- 公開DNSを同じ方法で再取得し、上の表から**変化なし**を確認した（apex・wwwとも A 157.120.209.148、AAAAなし、CAAなし、MX・SPF・NSは同じ、TTL 3600）。
- 旧サイト側に `ads.txt`・`robots.txt`・`favicon.ico`・検索エンジンの所有確認タグは無い（切替で失うものなし）。GitHub側の `sauna-cospa.com` 宛て絶対URL 133件はすべてリポジトリ内に実在。
- 予定する変更（Web用だけ）：apex の A を GitHub Pages の4件（185.199.108.153 / 185.199.109.153 / 185.199.110.153 / 185.199.111.153）へ。www は A 157.120.209.148 を消して CNAME `kenji0618y.github.io.` に。**MX・TXT・NSは触らない**（メールはConoHaのまま動く）。
- 戻し方：ConoHaのDNSで apex の A を 157.120.209.148 の1件に戻し、www の CNAME を消して A 157.120.209.148 を作り直す。GitHub の Settings → Pages の Custom domain を空にして保存する。TTLが3600のため、戻しても全員に反映されるまで最大1時間ほどかかる。

## 2026-10-06 切替後の値（Claude・ConoHa管理画面と公開DNSで確認）

| 名前 | 種別 | 値 | TTL | 備考 |
|------|------|------|-----|------|
| `@` | A | 185.199.108.153 / 185.199.109.153 / 185.199.110.153 / 185.199.111.153 | 3600 | GitHub Pages（変更） |
| `www` | CNAME | kenji0618y.github.io. | 3600 | GitHub Pages（A 157.120.209.148 から変更） |
| `mail` | A | 160.251.148.14 | 3600 | ConoHaメール用・変更なし |
| `ml-cp` | A | 160.251.148.14 | 3600 | ConoHaメール用・変更なし |
| `@` | MX | 10 mail1004.conoha.ne.jp. | 3600 | 変更なし |
| `@` | TXT | `v=spf1 include:_spf.conoha.ne.jp ~all` | 3600 | 変更なし |
| `default._domainkey` | TXT | DKIM（ConoHa発行） | 3600 | 変更なし |
| `@` | NS | ns-a1 / ns-a2 / ns-a3.conoha.io. | 3600 | 変更なし（DNSは引き続きConoHa） |

- `mail`・`ml-cp` の A と DKIM の TXT は、公開DNSの調査（2026-09-05）では見えていなかったが、ConoHa管理画面には存在した。
- GitHub側：リポジトリ直下の `CNAME`（`sauna-cospa.com`）、Settings → Pages で DNS check successful、Enforce HTTPS オン。
- 戻し方は上の「切替前の再確認と戻し方」のとおり（GitHub側は `CNAME` ファイルの削除でも外れる）。
