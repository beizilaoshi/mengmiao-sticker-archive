# 邮件存证 / E-mail Evidence

本目录保存 2026-11-25 那封「自寄邮件」的四份材料：

1. `2026-11-25-qqmail-headers.eml` —— **收件副本的邮件头**（3,079 B，含腾讯网关写入的 `Received` / `Authentication-Results` / `DKIM` 签名头）。**注意：字节精确只表示"我们没改过"，不表示"别人改不了" —— 见第八节**；
2. `2026-11-25-qqmail-sent-headers.eml` —— **发件副本的邮件头**（853 B，来自 163「已发送」，含 `X-Originating-IP`、Coremail 的 `Received`，**无 DKIM 签名**，是纯文本，可被任意编辑）；
3. `2026-11-25-qqmail-59M.pdf` —— 手机端 QQ 邮箱导出的页面快照（13 页，人可读）；
4. 两份原始 `.eml`（各约 60.6 MB，**不入库**，只登记哈希）。

---

## 一、这封邮件是什么

| 项目 | 内容 |
|------|------|
| 邮件主题 | （无主题） |
| 发件人 | 共筑社 `<zhuo0355943@163.com>`（网易 163 邮箱，本人所有） |
| 收件人 | beizi `<2023351165@qq.com>`（腾讯 QQ 邮箱，本人所有） |
| 发送时间（`Date` 头） | **Wed, 25 Nov 2026 23:57:44 +0800** |
| 收件时间（`Received` 头） | **Wed, 25 Nov 2026 23:57:53 +0800**（qq.com 邮件网关 NewMX 记录，id `E758E47B`） |
| `Message-ID` | `<718bccc7.ee3d.1a2136d606f.Coremail.zhuo0355943@163.com>`（两端副本完全一致） |
| 腾讯侧流水号 | `X-QQ-mid: xmmxzb51-1t1795622273tr43ujcn3` |
| 发信出口 IP | `223.74.189.22`（`X-Originating-IP`，手机端 163 客户端） |
| 发信服务器 | `zhuo0355943$163.com` → `m16.mail.163.com`（117.135.210.5） |
| 邮件大小 | 收件副本 63,535,666 B；发件副本 63,533,438 B |

---

## 二、为什么邮件头比 PDF 强

页面截图只能证明「有人把截图给我看」，`.eml` 能证明「邮件真的从 163 发到了腾讯，而且没被改过」：

| 校验项 | 结果 | 说明 |
|--------|------|------|
| `SPF` | **pass**（117.135.210.5，`smtp.mailfrom=<zhuo0355943@163.com>`） | 确认发信 IP 属于 163，非伪造发件人 |
| `DKIM` | **pass**（`d=163.com`，`s=s110527`） | 邮件正文经 163 私钥签名，改动任意一个字节签名即失效 |
| `DMARC` | **pass**（`p=NONE`） | 与 SPF/DKIM 的域名对齐一致 |
| `Received` 链 | `m16.mail.163.com` → `newxmmxszb51-1.qq.com` | 两家服务商各自留下时间戳，本机时钟无法影响 |

这三项校验值来自腾讯邮件网关的 `Authentication-Results` 头，是**收件方亲自验证后写下的**，不依赖发件方自述。

---

## 三、两端副本互证（本目录最值钱的一点）

普通自寄邮件只有收件箱那一份，容易被质疑「你可以自己伪造一封再存进邮箱」。**同时保存发件箱副本后，这个质疑就失效了**：

| 对照项 | 发件副本（163「已发送」） | 收件副本（QQ「收件箱」） |
|--------|--------------------------|--------------------------|
| `Message-ID` | `<718bccc7.ee3d.1a2136d606f.Coremail.zhuo0355943@163.com>` | **完全相同** |
| `Date` | Wed, 25 Nov 2026 23:57:44 +0800 | Wed, 25 Nov 2026 23:57:44 +0800 |
| `Received` | `from zhuo0355943$163.com ([223.74.189.22]) by ajax-webmail-wzpm-k8s-gz (Coremail)` 23:57:44 | `from m16.mail.163.com (117.135.210.5) by newxmmxszb51-1.qq.com (NewMX) id E758E47B` **23:57:53** |
| `X-Originating-IP` | `[223.74.189.22]` | 无（该头不跨服务器保留） |
| `Authentication-Results` | 无（尚未投递） | **有，SPF/DKIM/DMARC 全 pass** |
| 邮件头长度 | 851 B | 3,077 B |
| 文件大小 | 63,533,438 B | 63,535,666 B |

**逻辑闭环**：同一 `Message-ID` 出现在两个不同服务商的邮箱里，且一个记录了「23:57:44 从 223.74.189.22 发出」，另一个记录了「23:57:53 由 qq.com 网关验签后收下」。要伪造这条链，得同时攻破网易与腾讯两家的存档——这不是个人能做的事。

---

## 四、原件为什么不放进仓库

两份原始 `.eml` 各约 60.6 MB，其中 99.9% 是 22 张图片的 base64 附件——这些图片在 `assets/` 里已有完整、带哈希的副本，重复入库只会让仓库变肿（GitHub 超过 50 MB 警告、超过 100 MB 拒收）。

因此本仓库只保存**邮件头**，并登记原件的哈希：

```
收件副本（QQ 收件箱导出）
  路径     D:\下载\(无主题).eml
  字节数   63535666
  SHA-256  48103abb79c4df7f9af09cda0b46dc9cb5a2355d99742899722cf09b7a046991

发件副本（163 已发送导出）
  路径     D:\下载\(无主题) (1).eml
  字节数   63533438
  SHA-256  1606f2e43ed2230b0481a5f5bb8940bdc1ea883a0e35a18c0995d30576aec966
```

任何人拿到原件，用上面的 SHA-256 一比对即可确认「就是这一封」，无需把 120 MB 塞进 Git。

---

## 五、目录内文件的 SHA-256

```
8b3954ac676e7e83ca514fd9c86336d7fecd2cddc3b87b1b217f10b516053e08  2026-11-25-qqmail-headers.eml        （收件副本邮件头）
6baebbac7a78e2fb949170bbfe49f2720d553cceba57c0b3b76efed41408d364  2026-11-25-qqmail-sent-headers.eml   （发件副本邮件头）
ec04cee714c759a777eed83f710b6598b7c7dd11d4d48a87b25cd773a8593902  2026-11-25-qqmail-59M.pdf            （页面快照）
8253739041413e1add614b548a9063deaf70e638ee7e59b959e6ebb0fdaecbe2  2026-11-26-qqmail-headers.eml        （补件·收件副本邮件头）
73d00925951438bd045a7b65f32b6d9ffdfaab1d0999dd0ebcb0edd7cf1a81f2  2026-11-26-qqmail-sent-headers.eml   （补件·发件副本邮件头）
```

---

## 六、校验方式

```powershell
# 在仓库根目录执行
Get-FileHash evidence\email\2026-11-25-qqmail-headers.eml      -Algorithm SHA256
# 期望 8b3954ac676e7e83ca514fd9c86336d7fecd2cddc3b87b1b217f10b516053e08

Get-FileHash evidence\email\2026-11-25-qqmail-sent-headers.eml -Algorithm SHA256
# 期望 6baebbac7a78e2fb949170bbfe49f2720d553cceba57c0b3b76efed41408d364

Get-FileHash evidence\email\2026-11-25-qqmail-59M.pdf          -Algorithm SHA256
# 期望 ec04cee714c759a777eed83f710b6598b7c7dd11d4d48a87b25cd773a8593902

Get-FileHash evidence\email\2026-11-26-qqmail-headers.eml      -Algorithm SHA256
# 期望 8253739041413e1add614b548a9063deaf70e638ee7e59b959e6ebb0fdaecbe2

Get-FileHash evidence\email\2026-11-26-qqmail-sent-headers.eml -Algorithm SHA256
# 期望 73d00925951438bd045a7b65f32b6d9ffdfaab1d0999dd0ebcb0edd7cf1a81f2

# 补件原件（各约 13.7 MB，不入库）：
Get-FileHash 'D:\下载\36bdf56d316c1bd980be2578fb591526 (2).eml' -Algorithm SHA256   # e3f539fb…（收件）
Get-FileHash 'D:\下载\36bdf56d316c1bd980be2578fb591526 (1).eml' -Algorithm SHA256   # 8e47effc…（发件）

# 若手上有两份原始 .eml：
Get-FileHash 'D:\下载\(无主题).eml'      -Algorithm SHA256   # 48103abb…
Get-FileHash 'D:\下载\(无主题) (1).eml'  -Algorithm SHA256   # 1606f2e4…
```

或者逐字比对两端 `Message-ID`：

```powershell
Select-String -Path evidence\email\*.eml -Pattern '^Message-ID:'
```

---
---

## 七、还能再补的一层（可选）

- 把两份邮件头 + `MANIFEST.sha256` 一起做**可信时间戳**（联合信任 TSA，约 10 元/次，见 [`../timestamp/README.md`](../timestamp/README.md)），把"文件在此刻已存在"钉死——这是唯一能防止"事后改文件"的手段；
- 网易 163 的「已发送」与腾讯 QQ 邮箱的「收件箱」**都不要删**，两家各自保留的投递日志是最强后盾；
- 如需更正式，可申请**邮件头公证**（公证处现场登录邮箱导出），一般投诉用不到这一步。

---
## 八、这条证据能证明什么、不能证明什么（必读）

`.eml` 就是纯文本，用记事本就能改。所以必须把它的效力说准，不能吹。

| 说法 | 是否成立 |
|------|----------|
| `.eml` 文件可以被修改 | ✅ **完全正确**。发件副本尤其如此——它连 DKIM 签名都没有 |
| 改了之后一定看不出来 | ❌ 部分能看出，但**不能只依赖文件本身** |
| 邮件头能证明"这封邮件曾经存在" | ✅ 成立，但需要**第三方锚**，不能只靠手里这份文件 |

### 三个层次，效力递减

**第 1 层：服务器端存档（最强）**
网易 163 的「已发送」与腾讯 QQ 邮箱的「收件箱」里都还留着原件。任何人——包括法院持调查令——都可以要求两家服务商调取原始邮件与投递日志。**你改得了手里的文件，改不了他们服务器上的记录。**凭 `Message-ID: <718bccc7.ee3d.1a2136d606f.Coremail.zhuo0355943@163.com>` 就能精确定位那一封。

**第 2 层：DKIM 签名（"当时"的密码学校验）**
收件副本带 `DKIM-Signature`，覆盖 `Date:From:To:Subject:Content-Type:MIME-Version:Message-ID` 七个头，私钥只有 163 有——理论上改一个字就验不过。

**但实测结果（2026-11-26）是验不过：**

```
自写 RFC 6376 规范化 + openssl 验签      → Verification failure
dkimpy 独立实现（跳过正文，仅验头部）     → False
对照原始 63 MB .eml 前 3,077 字节         → 逐字节完全一致（排除我们改动）
DNS 取 s110527._domainkey.163.com        → 拿到 1024-bit 公钥，但与签名不匹配
```

最可能的两个原因：① 163 已轮换 `s110527` 选择器的密钥，旧签名的公钥已查不到；② 投递过程中某个被签头被网关改写。两种情况下签名都会失效，且**都与"邮件是否真实"无关**。

> **结论：DKIM 的效力是"当时"，不是"永久"。**
> 投递当时腾讯网关记录了 `dkim=pass`，那是它在 2026-11-25 23:57:53 的结论；
> 今天你无法再独立复验它。

**第 3 层：文件本身（最弱）**
纯文本，可改。它唯一的价值是"与服务器存档对照的线索"。

### 所以正确的用法是

1. **投诉时**用它说明情况，不要指望平台去验 DKIM（平台不会验）；
2. **起诉时**向法院申请**调查令**，凭 `Message-ID` 调取腾讯/网易服务器端存档——那才是改不掉的；
3. **想给手里这份文件加时间锚**：做可信时间戳或公证（见 [`../timestamp/README.md`](../timestamp/README.md)），把"文件在某时刻是这个字节"钉死。

### 顺带一个技术细节

`Authentication-Results`（写着 SPF/DKIM/DMARC pass 的那行头）**不在 DKIM 签名覆盖的七个头之内**，也就是说它本身是可被文本编辑伪造的。所以它只能当"线索"，不能单独当"证据"。真正压得住的是腾讯服务器上的投递日志。

---

© 2026 龚震宇. 保留所有权利。任何使用均须事先书面授权，见 [LICENSE](../../LICENSE)。

---

## 九、2026-11-26 补件邮件（23、24 号）

2026-11-26 00:43:58，同一账号（`zhuo0355943@163.com` → `2023351165@qq.com`）再发一封 13.7 MB 邮件，用同样方式为新增的两幅作品定下时间锚。

| 项目 | 值 |
|------|-----|
| `Message-ID` | `<515a00c9.9fe9.1a21397b45e.Coremail.zhuo0355943@163.com>` |
| `Date` | `Thu, 26 Nov 2026 00:43:58 +0800 (CST)` |
| `Subject` | `36bdf56d316c1bd980be2578fb591526`（发送端自动生成的附件包标识） |
| 发件副本 | `36bdf56d316c1bd980be2578fb591526 (1).eml`，13,747,518 B，sha256 `8e47effc34b30c29ea1255195259311404e7b05966a481b6161c0ef11cdf9434` |
| 收件副本 | `36bdf56d316c1bd980be2578fb591526 (2).eml`，13,749,668 B，sha256 `e3f539fb64589bcb18dcbd3408a6083d19a9b60ede22e1ca3b734e6cb9746207` |
| 收件端校验 | `spf=pass(220.197.31.2)`、`dkim=pass(signature was verified) header.d=163.com`、`dmarc=pass` |
| 附件（＝`assets/` 中的文件，逐字节一致） | `36bdf56d…png` 2,463,911 B sha256 `6871de7d…` → `23-无所事事.png`；`451653ec…png` 2,558,081 B sha256 `fee3fa0f…` → `24-跑路.png` |

**这封邮件解决的是"23、24 号在 2026-11-26 00:43:58 之前就已存在"这件事**——两幅图的原始 PNG 就躺在邮件附件里，与仓库中的文件哈希完全相同（说明入库时没有重新编码）。它与第八节的三层效力结论完全一致：文件本身可改，但两家服务商服务器上的这封邮件改不了。

> 注意：这两幅**不在** QQ 时间轴（2026-11-06 ~ 11-10）里，它们的时间证据只有这一封邮件 + Git 提交，弱于前 22 幅。若要拉平，可以对两张 PNG 单独做一次 10 元时间戳。
