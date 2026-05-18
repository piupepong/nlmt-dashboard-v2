# NLMT Dashboard

Dashboard tinh cho he thong nang luong mat troi.

## Kien truc moi

- Render chay `render-worker.js`.
- Worker nghe ESPHome SSE tai `ESPHOME_EVENT_URL`.
- Worker ghi mau moi len Supabase moi phut vao bang `energy_samples`.
- Web tinh chi doc Supabase cho lich su/san luong, lay dong moi nhat khi nguoi dung mo trang, sau do cap nhat bang Supabase Realtime va poll du phong moi 60 giay.
- Neu chay trong mang noi bo qua HTTP, web nghe them ESPHome SSE truc tiep tai `http://192.168.1.150/events` de topology/so lieu realtime nhay nhanh hon Supabase.

## Render env vars

Bat buoc:

```text
ESPHOME_EVENT_URL=https://piupepong.ddnsfree.com/events
SUPABASE_URL=https://iopqamrtcuxntcojtqeu.supabase.co
SUPABASE_SERVICE_ROLE_KEY=<service-role-key>
SUPABASE_TABLE=energy_samples
DEVICE_ID=nlmt-main
SAMPLE_INTERVAL_MS=60000
```

Nen dung `SUPABASE_SERVICE_ROLE_KEY` tren Render, khong dua key nay vao frontend.

## Bao mat key

- `SUPABASE_SERVICE_ROLE_KEY` chi dat trong Render Environment, khong commit vao repo.
- `.env` va `.env.*` da duoc ignore. Neu can ghi mau local, copy `.env.example` thanh `.env`.
- Frontend tinh chi co the dung Supabase anon key. Anon key khong phai bi mat, nhung bat buoc cau hinh RLS/policy dung.
- Khong dua Home Assistant token, service role key, hoac token ca nhan vao `app.js`, `supabase-config.js`, `README.md`.
- Neu service role key da tung bi lo trong chat/log, hay rotate key trong Supabase va cap nhat lai Render env.

## Supabase

Bang `energy_samples` can co unique constraint:

```sql
unique (device_id, ts)
```

De web cap nhat realtime, bat Realtime cho bang `energy_samples` trong Supabase.

## Realtime truc tiep tu ESPHome

Trong `supabase-config.js`, `esphomeEventUrl` dang duoc cau hinh cho mang noi bo:

```js
esphomeEventUrl: window.location.protocol === "https:" ? "" : "http://192.168.1.150/events"
```

Trinh duyet chan HTTP noi bo neu trang dang mo bang HTTPS, nen GitHub Pages se dung Supabase fallback. Khi chay tren Armbian/LAN bang HTTP, topology se lay truc tiep tu ESPHome va khong ghi database.
