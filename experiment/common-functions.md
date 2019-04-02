# 常見函數

## 常見函數 <a id="functions"></a>

| 選項 | 描述 |
| :---: | :--- |
| Osinit\(\) | 系統初始化參數 |
| PC\_DOSSaveReturn\(\) | return |
| OS\_EXIT\_CRITICAL\(\) | 退出臨界區，系統可以恢復外部中斷。 |
| OS\_ENTER\_CRITICAL\(\) | 進入臨界區，保護下文執行時不會受外部中斷干。 |
| OSTimeTick \(void\) | 在系統發生計時中斷後會執行一次。 |
|  OS\_Sched \(void\) | 目前任務完成，主動讓出CPU給其他任務 |
| OSIntExit \(void\) | 系統發生中斷服務後，退回中斷服務時會使用 |
| OSTimeDly\(tick\) | 任務延遲tick時間，此時會讓出CPU |
| OSTaskCreateExt | 創新一個任務 |
| OSTaskCreate | 創新一個任務 |





