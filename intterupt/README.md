# 中斷

#### 中斷\(Interrupt\) <a id="&#x4E2D;&#x65B7;Interrupt"></a>

* 外部中斷\(External Interrupt\):使用CPU外部設備引發的中斷，例如 GPIO觸發的中斷、計時器\(Timer\)中斷、計數器\(Counter\)中斷。
* 內部中斷\(Internal Interrupt\):使用程式碼直接進行軟體中斷。

#### 中斷向量\(Interrupt Vector\) <a id="&#x4E2D;&#x65B7;&#x5411;&#x91CF;Interrupt-Vector"></a>

系統事先定義了中斷向量表，上面記錄各種中斷發生時對應到的中斷服務入口位址，當中斷執行時，CPU就會在表中找到對應中斷服務的地址。

#### 中斷服務\(Interrupt Service Routine\) <a id="&#x4E2D;&#x65B7;&#x670D;&#x52D9;Interrupt-Service-Routine"></a>

縮寫為ISR，當中段發生時，CPU會到中斷向量表中查詢目前中斷來源的號碼，並進入相對應的入口地址，執行該中斷服務內容。中斷服務是可以依照使用者需求進行更改。

