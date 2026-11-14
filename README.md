# HEYGO Flutter 起步專案

這是 HEYGO MVP 的 Flutter 專案。附近頁使用 OpenStreetMap 底圖；地圖只顯示自願開啟附近模式會員的約略位置點。Supabase 用戶端依賴已加入，但要建立 Supabase 專案並設定網址和 Publishable Key 後才會連線。

## 開啟方式

Windows 可雙擊專案資料夾內的 `HEYGO-Preview.bat`，在 Chrome 預覽；如尚未建立 Supabase，Project URL 和 Publishable Key 都留空即可進入示範模式。連接真實後端時，只輸入 Supabase Project URL 和公開 Publishable Key。專案已包含 Android、iOS 和 Web 平台檔案。iPhone 編譯之後由雲端 Mac 處理。

## Android 先測試、iPhone 雲端建置

專案內的 `codemagic.yaml` 有兩個雲端工作流程：`heygo-android` 會輸出 Android 測試 APK；`heygo-ios` 會建置 iPhone 模擬器 `.app` 和未簽署的 iPhone App。Android 工作流程在 Codemagic 雲端執行，不需要在這部 Windows 電腦安裝 Java 或 Android SDK。操作步驟見 [CODEMAGIC_SETUP.md](CODEMAGIC_SETUP.md)。

Codemagic 需要連接 Git 倉庫。雙擊 `HEYGO-Publish-Private-Repo.bat`；如尚未建立倉庫，程式會開啟 GitHub 新倉庫頁面。建立空白的 GitHub **Private** 倉庫（不要先加入 README、License 或 `.gitignore`）後，貼上倉庫 HTTPS 網址。程式會檢查本機沒有未提交修改且 Supabase CLI 本機資料夾已被忽略，並要求確認 `PRIVATE` 和 `PUSH` 才會上傳。GitHub 登入若出現，請在自己的瀏覽器完成。

Codemagic 的方案和用量可能改變；開始建置前請查看[官方價格](https://codemagic.io/pricing/)。安裝到真實 iPhone 或上架 App Store 前，仍需 Apple Developer 帳戶、簽署憑證和對應的應用程式識別碼；真實會員功能亦需在 Codemagic 設定 `SUPABASE_URL` 和 `SUPABASE_PUBLISHABLE_KEY`。

目前 iOS Bundle ID 仍是範本值 `com.example.heygo`，只適合開發預覽。安裝到真實 iPhone 或提交 App Store 前，需在 Apple Developer 帳戶註冊並改用 HEYGO 專屬的 Bundle ID，再設定相符的簽署資料。

## 目前包含

- HEYGO 香港繁體中文首頁
- HEYGO 品牌桌面圖示和手機顯示名稱
- iPhone 和 Android 啟動畫面使用 HEYGO 品牌紫色及圖示
- iPhone 定位權限說明提供繁體中文版本
- 電郵註冊驗證回跳、忘記密碼電郵及密碼重設流程
- 附近模式開關、開啟前的位置分享說明和只在使用期間取得的約略位置流程；位置先四捨五入到約 1 公里級
- 附近模式開啟時，App 進入背景、關閉或登出會停用地圖點並清除座標；回到前景後才重新取得約略位置，網絡中斷時伺服器會在 15 分鐘後停止回傳舊位置
- 可拖曳、縮放的 OpenStreetMap 附近地圖，並顯示自願開啟模式會員的約略位置
- 附近活動清單、顯示活動介紹與容量、建立活動和申請加入流程
- 附近、訊息、活動、我的四個可切換分頁
- 接受或略過邀請、配對後進入即時聊天
- 編輯顯示名稱、自我介紹、表情頭像和大概地區
- 主持人完成活動後，參加者可互相評分並查看收到的評分
- 活動和附近會員檢舉、聊天檢舉和封鎖、個人封鎖名單、安全指引及帳戶刪除入口
- 「我的」頁可查看本人提交的檢舉類別、日期和處理狀態
- 「我的」頁提供私隱與資料摘要，說明電郵、活動內容、約略位置、地圖服務與刪除帳戶資料的用途

## 版本範圍

首發 MVP 以香港 iPhone 用戶為主，先完成註冊登入、附近模式、活動邀請與配對、即時聊天、活動評分，以及安全和帳戶刪除功能。頭像目前使用表情符號。

以下項目留待核心 MVP 上線後再開發：

- 可自訂造型、服飾的全身 3D Q 版人物頭像
- Coins 錢包及商店

活動清單和會員互動要在 Supabase 專案連線後才會使用真實資料。OpenStreetMap 公開圖磚服務採盡力供應，沒有服務保證；正式公開前應確認服務條款和容量是否適合預期流量，並可替換圖磚網址。

## 後端起步

- Windows 部署步驟見 `supabase/WINDOWS_SETUP.md`；`scripts/deploy-supabase.ps1` 會先預覽資料庫遷移，只有輸入 `DEPLOY` 才會套用並部署帳戶刪除功能。

- `supabase/config.toml` 設定本機 Supabase 專案識別及要求刪除帳戶函式驗證登入 JWT。
- Windows 可雙擊 `HEYGO-Deploy-Supabase.bat`，輸入 Project Ref 後按畫面指示部署資料庫；要套用遷移時必須再輸入 `DEPLOY`。
- `scripts/publish-private-repo.ps1` 只推送已提交的 `main` 分支，且要求先確認倉庫為 Private，再輸入 `PUSH`。
- `supabase/migrations/202611100001_heygo_mvp.sql` 建立會員、附近模式、活動、邀請、配對、訊息、評分、封鎖和檢舉資料表及基本存取規則。
- `supabase/migrations/202611110001_nearby_map_points.sql` 加入僅限已開附近模式會員使用的約略地圖點 RPC。
- `supabase/migrations/202611110002_enforce_approx_location.sql` 在資料庫強制四捨五入位置，關閉附近模式時清除座標。
- `supabase/migrations/202611120001_activity_completion_ratings.sql` 加入活動主持人完成活動及參加者互相評分流程。
- `supabase/migrations/202611130001_block_list.sql` 提供只列出自己封鎖會員的安全 RPC。
- `supabase/migrations/202611140001_visibility_aware_blocks.sql` 讓封鎖雙方在資料庫規則中都不能查看配對、訊息或個人資料，也不能評分或傳訊息。
- `supabase/migrations/202611150001_restrict_client_writes.sql` 限制 App 可寫入的欄位，並由資料庫產生邀請回覆時間及保護系統狀態。
- `supabase/migrations/202611160001_coarse_activity_areas.sql` 將活動及個人地區限定為香港大概地區，並拒絕已過去的活動開始時間。
- `supabase/migrations/202611170001_expire_stale_nearby_presence.sql` 附近模式超過 15 分鐘沒有更新時，不再回傳會員地圖點。
- `supabase/migrations/202611180001_expand_hong_kong_districts.sql` 支援香港十八區及四個常用地區選項。
- `supabase/migrations/202611190001_prevent_duplicate_activity_requests.sql` 防止已加入活動的會員重複申請或被邀請加入同一活動。
- `supabase/migrations/202611200001_hide_profiles_after_declined_invites.sql` 邀請被拒絕或取消後，不再因舊邀請而公開雙方個人資料。
- `supabase/migrations/202611210001_hide_invitations_between_blocked_members.sql` 封鎖任一方後，雙方收件匣不再顯示舊邀請。
- `supabase/migrations/202611220001_server_manage_profile_timestamp.sql` 改由資料庫更新個人資料時間，避免用戶端自行偽造。
- `supabase/migrations/202611230001_enable_invitation_realtime.sql` 為邀請啟用即時事件；發起人和接收人可同步收到狀態變更。
- `supabase/migrations/202611240001_index_outgoing_invitations.sql` 為已送出邀請列表加入查詢索引。
- `supabase/migrations/202611250001_allow_invitation_cancellation.sql` 僅允許發起人撤回仍待回覆的邀請。
- `supabase/migrations/202611260001_index_member_matches.sql` 為配對列表查詢加入索引。
- `supabase/migrations/202611270001_allow_host_to_cancel_activity.sql` 讓主持人取消仍有效的活動並關閉待處理邀請。
- `supabase/migrations/202611280001_limit_nearby_search_frequency.sql` 將附近地圖搜尋限制為每位會員每分鐘一次，減慢反覆掃描不同位置的速度。
- `supabase/migrations/202611290001_preserve_reports_when_subjects_deleted.sql` 讓會員刪除帳戶或活動被移除時，檢舉紀錄保留匿名化對象而不阻擋刪除。
- `supabase/migrations/202611300001_allow_reinviting_after_cancellation.sql` 讓已撤回或被拒絕的邀請可重新發送，同時阻止同一對會員重複建立待回覆邀請。
- `supabase/migrations/202612010001_hide_activities_after_block.sql` 封鎖任一方後，雙方不再看到對方主持的公開活動。
- `supabase/migrations/202612020001_limit_profile_emoji_and_activity_categories.sql` 在資料庫限制頭像和活動類型為 App 提供的選項。
- `supabase/migrations/202612030001_fix_participant_rating_policy.sql` 保持參加者名單私隱，同時讓已完成活動的成員可以互相評分。
- `supabase/migrations/202612040001_keep_security_helpers_private.sql` 將封鎖狀態和評分資格檢查留在資料庫內部，不開放成可直接呼叫的 API。
- `supabase/migrations/202612050001_index_chat_message_cursor.sql` 為配對內的訊息時間和 ID 分頁加入複合索引。
- `supabase/migrations/202612060001_restore_invitation_cancellation_guard.sql` 保持邀請發起人可撤回待回覆邀請，並限制只有收件人可接受或拒絕邀請。
- `supabase/migrations/202612070001_reject_stale_activity_invites.sql` 防止已加入活動的會員接受舊邀請後建立重複配對，並撤回已失效的反方向待處理邀請。
- `supabase/migrations/202612080001_expire_pending_invites_when_full.sql` 及 `202612100001_close_invites_for_terminal_activities.sql` 活動額滿、取消或完成時自動關閉待回覆邀請。
- `supabase/migrations/202612090001_require_location_coordinate_pairs.sql` 在資料庫要求分享位置的緯度和經度成對存在，缺少任一項時會關閉分享並清空座標。
- MVP 檢舉由專案擁有人在 Supabase Dashboard 的 `reports` 資料表人工檢視和更新；一般會員不能查看其他人的檢舉或更改狀態，詳見 `supabase/WINDOWS_SETUP.md`。
- `supabase/functions/delete-account/index.ts` 提供登入會員刪除自己帳戶的伺服器端流程；上線前需要部署 Edge Function。
- 附近模式預設關閉；搜尋函式只回傳已開啟附近模式、5 公里內且 15 分鐘內有更新的會員約略座標和距離範圍。App 先把自己的位置四捨五入到約 1 公里級再儲存；關閉附近模式會清除位置。
- 建立 Supabase 專案後，以 `SUPABASE_URL` 和 `SUPABASE_PUBLISHABLE_KEY` 傳入專案網址和公開用戶端 key；不要把 service-role key 放進 App。
- 按順序套用資料庫 migration 並部署刪除帳戶 Edge Function；目前尚未連接真實帳戶或資料。

