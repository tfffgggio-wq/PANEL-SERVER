# Setup Panel Admin via Telegram Bot (Vercel)

## 1. Bikin bot Telegram
1. Chat `@BotFather` di Telegram → `/newbot` → ikutin instruksinya.
2. Simpan **token** yang dikasih (formatnya kayak `123456:ABC-DEF...`).
3. Chat bot `@userinfobot` buat tau **chat ID** akun Telegram kamu sendiri.

## 2. Siapkan project
1. Taruh folder ini (`telegram-panel/`) sebagai project Vercel baru, atau gabungin ke repo yang sudah ada — yang penting file `api/telegram.js` ada di path `api/`.
2. **Jangan** upload `serviceAccountKey.json` ke repo/git. Isinya nanti dipindah ke Environment Variable.

## 3. Set Environment Variables di Vercel
Buka Project → Settings → Environment Variables, tambahin:

| Key | Value |
|---|---|
| `TELEGRAM_BOT_TOKEN` | token dari BotFather |
| `TELEGRAM_ADMIN_CHAT_ID` | chat ID kamu (bisa lebih dari satu, pisah koma, contoh: `111,222`) |
| `FIREBASE_SERVICE_ACCOUNT` | isi seluruh file `serviceAccountKey.json`, di-paste sebagai satu baris JSON |
| `FIREBASE_DATABASE_URL` | `https://daftar-id-default-rtdb.asia-southeast1.firebasedatabase.app` |

Buat `FIREBASE_SERVICE_ACCOUNT` jadi satu baris, gampangnya jalanin ini di komputer kamu (bukan di sini) lalu paste hasilnya:

```bash
node -e "console.log(JSON.stringify(require('./serviceAccountKey.json')))"
```

## 4. Deploy
```bash
vercel deploy --prod
```
Catat URL project-nya, contoh: `https://blue-games-panel.vercel.app`

## 5. Daftarin webhook ke Telegram
Jalanin sekali aja (ganti `<TOKEN>` dan URL-nya):

```bash
curl "https://api.telegram.org/bot<TOKEN>/setWebhook?url=https://blue-games-panel.vercel.app/api/telegram"
```

Kalau sukses, balasannya `{"ok":true,"result":true,...}`.

## 6. Coba
Chat bot kamu di Telegram: `/menu`

## Command yang tersedia
- `/status` — lihat status dialog update & jumlah akun
- `/on` — aktifkan dialog update paksa
- `/off` — matikan dialog update
- `/setlink <url>` — ganti link update
- `/accounts` — lihat daftar akun terdaftar (max 100 ditampilin)

## Catatan keamanan
- Bot **nolak semua chat** kecuali chat ID-nya ada di `TELEGRAM_ADMIN_CHAT_ID`. Pastikan ini diisi sebelum deploy, karena command `/on`/`/off`/`/setlink` bisa ngubah tampilan ke semua user aplikasi.
- Kalau mau nambah admin lain, tinggal tambahin chat ID-nya ke env var itu (pisah koma), lalu redeploy.
