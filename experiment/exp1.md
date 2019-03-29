# 實驗1

### 範例1

```cpp
/*
*********************************************************************************************************
*                                                uC/OS-II
*                                          The Real-Time Kernel
*
*                           (c) Copyright 1992-2002, Jean J. Labrosse, Weston, FL
*                                           All Rights Reserved
*
*                                               EXAMPLE #1
*********************************************************************************************************
*/
#define  TASK_STK_SIZE                 512    //定義任務的推疊大小
#define  N_TASKS                        10    //定義n個task


OS_STK        TaskStk[N_TASKS][TASK_STK_SIZE];        /* Tasks stacks                                  */
OS_STK        TaskStartStk[TASK_STK_SIZE];
char          TaskData[N_TASKS];                      /* Parameters to pass to each task               */
OS_EVENT     *RandomSem;                        //宣告一個訊號量 semaphore

void  Task(void *data);                      
void  TaskStart(void *data);                   
static  void  TaskStartCreateTasks(void);     //創建之後執行任務的任務
static  void  TaskStartDispInit(void);        //顯示初始化
static  void  TaskStartDisp(void);            //用於顯示的任務

void  main (void)
{
    PC_DispClrScr(DISP_FGND_WHITE + DISP_BGND_BLACK);      /* Clear the screen                         */

    OSInit();                                              /* Initialize uC/OS-II                      */

    PC_DOSSaveReturn();                                    /* Save environment to return to DOS        */
    PC_VectSet(uCOS, OSCtxSw);                             /* Install uC/OS-II's context switch vector */

    RandomSem   = OSSemCreate(1);                          /*這要幹麻 Random number semaphore                  */

    OSTaskCreate(TaskStart, (void *)0, &TaskStartStk[TASK_STK_SIZE - 1], 0);  //建立任務
    OSStart();                                             /* Start multitasking                       */
}
```

> 1. 在main\(\)中執行到`OSStart`後CPU就不會在跳回main，此時由任務調度器，安排CPU執行的任務。
> 2. `OSCtxSw`對應中斷向量表中的0x80,功能是執行context switch。
>
>    \`\`\` void TaskStart \(void \*pdata\) {
>
>    **if OS\_CRITICAL\_METHOD == 3      /** _**Allocate storage for CPU status register**_ **/**
>
>    OS\_CPU\_SR cpu\_sr;
>
>    **endif**
>
>    char s\[100\]; INT16S key;

```text
pdata = pdata;                                  /* Prevent compiler warning                 */

TaskStartDispInit();                            //初始化顯示器

OS_ENTER_CRITICAL();                            //進入臨界區，此時關閉外部中斷，用於保護下文
PC_VectSet(0x08, OSTickISR);                    //設定定時器中斷
PC_SetTickRate(OS_TICKS_PER_SEC);               //1秒之內倒數 OS_TICKS_PER_SEC 個 tick
OS_EXIT_CRITICAL();                             //退出臨界區，恢復中斷

OSStatInit();                                          /* Initialize uC/OS-II's statistics         */

TaskStartCreateTasks();                                /* Create all the application tasks         */

for (;;) {
    TaskStartDisp();                                  /* Update the display                       */

                                                       /*若按esc則退出
    if (PC_GetKey(&key) == TRUE) {                     /* See if key has been pressed              */
        if (key == 0x1B) {                             /* Yes, see if it's the ESCAPE key          */
            PC_DOSReturn();                            /* Return to DOS                            */
        }
    }

    OSCtxSwCtr = 0;                                    /* Clear context switch counter             */
    OSTimeDlyHMSM(0, 0, 1, 0);                         /* Wait one second                          */
}
```

}

```text
> * 使用pdata = pdata來防止編譯出警告
> * 在ucos中的任務會用以下形式表達:
>
```

> void task\(\){  
> //初始化
>
> ```text
> for(;;){
> ```
>
> //任務內容
>
> ```text
> }
> ```
>
> } \`\`\`

```text
static  void  TaskStartDispInit (void)
{
/*                                1111111111222222222233333333334444444444555555555566666666667777777777 */
/*                      01234567890123456789012345678901234567890123456789012345678901234567890123456789 */
    PC_DispStr( 0,  0, "                         uC/OS-II, The Real-Time Kernel                         ", DISP_FGND_WHITE + DISP_BGND_RED + DISP_BLINK);
    PC_DispStr( 0,  1, "                                Jean J. Labrosse                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  2, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  3, "                                    EXAMPLE #1                                  ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  4, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  5, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  6, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  7, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  8, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0,  9, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 10, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 11, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 12, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 13, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 14, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 15, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 16, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 17, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 18, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 19, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 20, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 21, "                                                                                ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 22, "#Tasks          :        CPU Usage:     %                                       ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 23, "#Task switch/sec:                                                               ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
    PC_DispStr( 0, 24, "                            <-PRESS 'ESC' TO QUIT->                             ", DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY + DISP_BLINK);
/*                                1111111111222222222233333333334444444444555555555566666666667777777777 */
/*                      01234567890123456789012345678901234567890123456789012345678901234567890123456789 */
}

/*$PAGE*/
/*
```

```text
*********************************************************************************************************
*                                           UPDATE THE DISPLAY
*********************************************************************************************************
*/

static  void  TaskStartDisp (void)
{
    char   s[80];
    sprintf(s, "%5d", OSTaskCtr);                                  /* Display #tasks running               */
    PC_DispStr(18, 22, s, DISP_FGND_YELLOW + DISP_BGND_BLUE);

#if OS_TASK_STAT_EN > 0
    sprintf(s, "%3d", OSCPUUsage);                                 /* Display CPU usage in %               */
    PC_DispStr(36, 22, s, DISP_FGND_YELLOW + DISP_BGND_BLUE);
#endif

    sprintf(s, "%5d", OSCtxSwCtr);                                 /* Display #context switches per second */
    PC_DispStr(18, 23, s, DISP_FGND_YELLOW + DISP_BGND_BLUE);

    sprintf(s, "V%1d.%02d", OSVersion() / 100, OSVersion() % 100); /* Display uC/OS-II's version number    */
    PC_DispStr(75, 24, s, DISP_FGND_YELLOW + DISP_BGND_BLUE);

    switch (_8087) {                                               /* Display whether FPU present          */
        case 0:
             PC_DispStr(71, 22, " NO  FPU ", DISP_FGND_YELLOW + DISP_BGND_BLUE);
             break;

        case 1:
             PC_DispStr(71, 22, " 8087 FPU", DISP_FGND_YELLOW + DISP_BGND_BLUE);
             break;

        case 2:
             PC_DispStr(71, 22, "80287 FPU", DISP_FGND_YELLOW + DISP_BGND_BLUE);
             break;

        case 3:
             PC_DispStr(71, 22, "80387 FPU", DISP_FGND_YELLOW + DISP_BGND_BLUE);
             break;
    }
}

/*$PAGE*/
/*
```

> 顯示用的函數

```text
*********************************************************************************************************
*                                             CREATE TASKS
*********************************************************************************************************
*/

static  void  TaskStartCreateTasks (void)
{
    INT8U  i;


    for (i = 0; i < N_TASKS; i++) {                        /* Create N_TASKS identical tasks           */
        TaskData[i] = '0' + i;                             /* Each task will display its own letter    */
        OSTaskCreate(Task, (void *)&TaskData[i], &TaskStk[i][TASK_STK_SIZE - 1], i + 1);
    }
}
```

> 專門用來建立新任務的任務，在這個任務中使用for迴圈，產生`N_TASKS`個任務，當任務完成後，這個任務將不會再被執行。

```text
void  Task (void *pdata)
{
    INT8U  x;
    INT8U  y;
    INT8U  err;


    for (;;) {
        OSSemPend(RandomSem, 0, &err);           /* Acquire semaphore to perform random numbers        */
        x = random(80);                          /* Find X position where task number will appear      */
        y = random(16);                          /* Find Y position where task number will appear      */
        OSSemPost(RandomSem);                    /* Release semaphore                                  */
                                                 /* Display the task number on the screen              */
        PC_DispChar(x, y + 5, *(char *)pdata, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
        OSTimeDly(1);                            /* Delay 1 clock tick                                 */
    }
}
```

> 使用信號機去產生亂數後再顯示到螢幕上，任務執行完畢則延遲1秒後繼續。

```text
digraph hierarchy {

                nodesep=1.0 

                node [
                Color=Red,fontname=Courier,shape=box] 
                edge [color=Blue, style=dashed] 

                main->{TaskStart  var_TaskStartStk var_RandomSem}
                TaskStart->{TaskStartCreateTasks TaskStartDispInit TaskStartDisp }
                TaskStartCreateTasks->{Task }
                Task->{var_TaskData var_TaskStk var_RandomSem}

}
```

OSTaskCtr OSCPUUsage OSCtxSwCtr OSVersion\(\) OSSemCreate\(\) //Random number semaphore OSTaskCreate\(\) OSStart\(\) OSStatInit\(\)// Initialize uC/OS-II's statistics OSSemPend\(\) OSSemPost\(\)

PC\_DispStr\(column,row,"string",color\) PC\_DispClrScr\(\) // Clear the screen PC\_DOSSaveReturn\(\) //Save environment to return to DOS PC\_VectSet\(\)//Install uC/OS-II's context switch vector PC\_SetTickRate\(OS\_TICKS\_PER\_SEC\)// Reprogram tick rate PC\_DOSReturn\(\)// Return to DOS OSTimeDlyHMSM\(\)

sprintf\(\)

unknown variable 1. \_8087 2. pdata

