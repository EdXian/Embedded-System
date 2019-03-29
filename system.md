# 系統

#### 上下文交換\(Context Switch\)

當中斷發生時，CPU會進行上下文交換，系統將保存目前任務的進度，並讀取要交換任務目前的進度後執行。

#### 程式計數器\(Program Counter\)

#### 優先權\(Priority\)

優先權決定任務被執行的先後順序，在ucos-II中，優先級別可以透過任務控制塊中的參數`OSCTCBPrio`決定，值越小則優先級別越高。

#### 先行、搶占\(Preemption\)

優先權高的任務可以搶佔CPU，當任務完成後釋放CPU。ucos-ii是依照搶占式的機制調配任務。

#### 信號機（semaphore）

