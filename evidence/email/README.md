# 邮件存证 / E-mail Evidence

本目录保存 2026-11-25 那封「自寄邮件」的四份材料：

1. `2026-11-25-qqmail-headers.eml` —— **收件副本的邮件头**（字节精确，3,079 B，含腾讯网关写入的 `Received` / `Authentication-Results` / `DKIM` 验证结果）；
2. `2026-11-25-qqmail-sent-headers.eml` —— **发件副本的邮件头**（字节精确，853 B，来自 163「已发送」，含 `X-Originating-IP`、Coremail 的 `Received`）；
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

# 若手上有两份原始 .eml：
Get-FileHash 'D:\下载\(无主题).eml'      -Algorithm SHA256   # 48103abb…
Get-FileHash 'D:\下载\(无主题) (1).eml'  -Algorithm SHA256   # 1606f2e4…
```

或者逐字比对两端 `Message-ID`：

```powershell
Select-String -Path evidence\email\*.eml -Pattern '^Message-ID:'
```

---

## 七、还能再补的一层（可选）

- 把两份邮件头 + `MANIFEST.sha256` 一起做**可信时间戳**（联合信任 TSA，约 10 元/次，见 [TIMESTAMP.md](../../TIMESTAMP.md)），把「文件在此刻已存在」钉死；
- 网易 163 的「已发送」与腾讯 QQ 邮箱的「收件箱」**都不要删**，两家各自保留的投递日志是最强后盾；
- 如需更正式，可申请**邮件头公证**（公证处现场登录邮箱导出），一般投诉用不到这一步。

---

© 2026 龚震宇. 保留所有权利。任何使用均须事先书面授权，见 [LICENSE](../../LICENSE)。
