# 任務管理

## 任務狀態

![&#x4EFB;&#x52D9;&#x72C0;&#x614B;](https://i.imgur.com/KT7PAQy.png)

| 狀態 | 說明 |
| :--- | :--- |
| 準備就緒\(Rready\) | 任務已經創建好\(TCB已被建立\)，並在任務就緒表掛起\(設定為1\)，並等待CPU來執行。同一時間可以有很多個任務準備就緒 |
| 運行 \(Running\) | 經由系統判斷優先權，獲得使用權的任務將會從準備狀態跳到運行狀態，此任務將會被運行，且一次只有一個任務可以運行，當最高優先權的任務結束後則放棄CPU，換次優先權的任務繼續使用CPU。 |
| 等待\(Waiting\) | 當較高優先級的任務完成後，會讓出CPU給其他任務，而此任務進入等帶狀態，當等待時間結束或是某個事件發生時，才會繼續占用CPU |
| 中斷服務處理\(Interrupt Service Routine\) | 當外部或內部中斷被觸發時，CPU會放下運行狀態的任務，優先處理中斷服務內的任務，等到處理結束，在跳轉回來 |
| 休眠\(Dormant\) | 當任務被刪除時會跳到此狀態。 |

{% hint style="info" %}
在單核心CPU內一次只能處理一個任務，當有更高優先權的任務要被執行時，CPU將會放棄原本任務，並優先處裡優先級較高的任務，完成後再繼續執行原本任務,兩次任務之間都會有中斷發生。
{% endhint %}



