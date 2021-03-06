# 任務控制塊

## 任務控制塊\(Task Control Block\)

ucos的任務可以由三個部份組成，任務控制塊，任務推疊，任務函數組成，其中任務控制塊縮寫TCB,為一個資料結構所組成，裡面紀錄任務的詳細資訊。

## 任務控制塊的結構

| 變數名稱 | 型態 | 功能 |
| :--- | :--- | :--- |
| OSTCBStkPtr | OS\_STK\* | 指向該任務推疊的最上層 |
| OSTCBStkSize | INT32U | 紀錄任務推疊的大小 |
| OSTCBNext | os\_tcb\* | 指向下一個任務控制塊 |
| OSTCBPrev | os\_tcb\* | 指向上一個任務控制塊指標 |
| OSTCBDly | INT16U | 紀錄此任務需要再延遲的tick次數 |
| OSTCBStat | INT8U | 紀錄任務的狀態 |
| OSTCBPrio | INT8U | 紀錄任務的優先級別 |

{% code-tabs %}
{% code-tabs-item title="os\_tcb" %}
```c
typedef struct os_tcb {
    OS_STK        *OSTCBStkPtr;        /* Pointer to current top of stack                              */

#if OS_TASK_CREATE_EXT_EN > 0
    void          *OSTCBExtPtr;        /* Pointer to user definable data for TCB extension             */
    OS_STK        *OSTCBStkBottom;     /* Pointer to bottom of stack                                   */
    INT32U         OSTCBStkSize;       /* Size of task stack (in number of stack elements)             */
    INT16U         OSTCBOpt;           /* Task options as passed by OSTaskCreateExt()                  */
    INT16U         OSTCBId;            /* Task ID (0..65535)                                           */
#endif

    struct os_tcb *OSTCBNext;          /* Pointer to next     TCB in the TCB list                      */
    struct os_tcb *OSTCBPrev;          /* Pointer to previous TCB in the TCB list                      */

#if ((OS_Q_EN > 0) && (OS_MAX_QS > 0)) || (OS_MBOX_EN > 0) || (OS_SEM_EN > 0) || (OS_MUTEX_EN > 0)
    OS_EVENT      *OSTCBEventPtr;      /* Pointer to event control block                               */
#endif

#if ((OS_Q_EN > 0) && (OS_MAX_QS > 0)) || (OS_MBOX_EN > 0)
    void          *OSTCBMsg;           /* Message received from OSMboxPost() or OSQPost()              */
#endif

#if (OS_VERSION >= 251) && (OS_FLAG_EN > 0) && (OS_MAX_FLAGS > 0)
#if OS_TASK_DEL_EN > 0
    OS_FLAG_NODE  *OSTCBFlagNode;      /* Pointer to event flag node                                   */
#endif
    OS_FLAGS       OSTCBFlagsRdy;      /* Event flags that made task ready to run                      */
#endif

    INT16U         OSTCBDly;           /* Nbr ticks to delay task or, timeout waiting for event        */
    INT8U          OSTCBStat;          /* Task status                                                  */
    INT8U          OSTCBPrio;          /* Task priority (0 == highest, 63 == lowest)                   */

    INT8U          OSTCBX;             /* Bit position in group  corresponding to task priority (0..7) */
    INT8U          OSTCBY;             /* Index into ready table corresponding to task priority        */
    INT8U          OSTCBBitX;          /* Bit mask to access bit position in ready table               */
    INT8U          OSTCBBitY;          /* Bit mask to access bit position in ready group               */

#if OS_TASK_DEL_EN > 0
    BOOLEAN        OSTCBDelReq;        /* Indicates whether a task needs to delete itself              */
#endif
} OS_TCB;
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
OSTCBStkPtr為tcb的第一個指標變數，地址指向任務推疊的最上層。方便組合語言進入記憶體位址操作。
{% endhint %}

## 任務列表

任務列表\(OSTCBList\)是由很多任務控制塊所組成的list。

![OSTCBList](../.gitbook/assets/ch03_html_m2e671ed1.png)

## 操作任務列表的方法

{% code-tabs %}
{% code-tabs-item title="Accessing to os\_tcb" %}
```c
  if (OSRunning == TRUE) {          //判斷系統是否正在執行        
  
        ptcb = OSTCBList;            //ptcb 為一個os_tcb的指標型態 ，並將它指向任務列表的第一個任務
        
   while (ptcb->OSTCBPrio != OS_IDLE_PRIO) {  //如果這次所指向的任務控制塊的             
                                             //優先級別不是最低的(idle task)         
            
              OS_ENTER_CRITICAL();     //進入臨界區域，保護下面程式執行不被中斷                                                                              
       
                   //執行程式
       
       
            ptcb = ptcb->OSTCBNext;          //將指標指向下一個任務                  
            OS_EXIT_CRITICAL();              //恢復系統的中斷

        }
    }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
迴圈將從任務列表的第一個訪問到最後一個任務就行，因為idle task 是第最後一個任務，所以輪到idle task就結束。
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="list" %}
```text
#include <iostream>

using namespace std;

struct task_type{
    int id;
    int prio;
     task_type* next_task;
     task_type* prev_task;
};

int main()
{

    task_type task[10];
    task_type* first_task = task; //定義first指標指向list的第一個元素
    task_type* last_task = &task[9]; //定義last指標指向list的最後一個元素

                                //初始化 list
    for(int i =0 ;i<10 ;i++){
        if(i<9){
               task[i].next_task  = &task[i+1]; //將next_task只向下一個任務的位址
               task[i].id = i;
        }else{
            task[i].next_task = NULL;     //如果到最後一個任務則next_task指向 null
            task[i].id = i;

        }
        if(i<1){
            task[i].prev_task = NULL;      //如果到第一個任務則prev_task指向 null
            task[i].prio    =  8-i;

        }else{
            task[i].prio    =  8-i;
            task[i].prev_task = &task[i-1];     //將next_task只向下一個任務的位址
        }
    }


    //the main part
    //用for迴圈進行操作
    for(task_type* g= first_task;g!= last_task;g++){
        printf("id = %d, prio = %d\n",g->id , g->prio);
    }


    task_type  *p;          //宣告一個指標變數
    p=first_task ;          //將它指向list第一個的地址
    printf("=============================\n");
    while(p!= last_task){   //如果p和last_task不相同 則繼續print
        printf("id = %d, prio = %d\n",p->id , p->prio);
        p = p->next_task;                //只向下一個任務
    };

    return 0;
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

